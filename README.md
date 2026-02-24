# Projeto de Previsão de Evasão Escolar

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)](https://jupyter.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

## Índice
- [Visão Geral](#-visão-geral)
- [Problemática](#-problemática)
- [Fontes de Dados](#-fontes-de-dados)
- [Análise Exploratória](#-análise-exploratória)
- [Modelagem](#-modelagem)
- [Resultados](#-resultados)
- [Conclusões e Recomendações](#-conclusões-e-recomendações)
- [Como Executar](#-como-executar)
- [Tecnologias Utilizadas](#-tecnologias-utilizadas)
- [Próximos Passos](#-próximos-passos)
- [Autor](#-autor)

---

## Visão Geral

Este projeto tem como objetivo desenvolver um modelo de **Machine Learning** para **predizer a evasão escolar** de alunos do Ensino Médio da rede pública brasileira. Através da análise de dados educacionais e socioeconômicos, buscamos identificar precocemente estudantes em situação de risco, permitindo intervenções direcionadas e eficazes.

### Por que isso é importante?
- **Impacto Social**: A evasão escolar perpetua ciclos de pobreza e desigualdade
- **Impacto Econômico**: Custo estimado de bilhões de reais por ano para o país
- **Impacto Humano**: Sonhos interrompidos e oportunidades perdidas

---

## Problemática

A evasão escolar no Ensino Médio brasileiro atinge taxas alarmantes:
- **Taxa média nacional**: ~11-12% ao ano
- **1º ano do EM**: Pico de evasão (até 15%)
- **Turno noturno**: Taxas significativamente mais altas
- **Alunos com distorção idade-série**: Risco 3x maior

**Desafio**: Como identificar, no início do ano letivo, quais alunos têm maior probabilidade de abandonar a escola, para que possam receber apoio antes que isso aconteça?

---

## 📊 Fontes de Dados

Os dados utilizados neste projeto são **sintéticos**, porém baseados em estatísticas reais da educação brasileira, incluindo:

| Fonte | Características |
|-------|-----------------|
| **Censo Escolar (INEP)** | Perfil de escolas, turmas e alunos |
| **SAEB (INEP)** | Proficiência em Português e Matemática |
| **IBGE** | Indicadores socioeconômicos |

### Variáveis do Dataset
- **Demográficas**: idade, sexo, raça/cor
- **Escolares**: série, turno, tipo de escola
- **Acadêmicas**: notas, faltas, repetências, distorção idade-série
- **Socioeconômicas**: renda familiar, escolaridade dos pais, trabalho

---

## Análise Exploratória

### Principais Descobertas

#### 1. Distribuição da Evasão
- Taxa geral: **~11%** (alinhada com a realidade brasileira)
- Classe desbalanceada: 89% não evadem, 11% evadem

#### 2. Evasão por Série
1º Ano EM: 13.5% (MAIOR RISCO)
2º Ano EM: 10.2%
3º Ano EM: 8.7%

#### 3. Relação com Desempenho Acadêmico
- Alunos que evadem têm notas **38% menores** em média
- Correlação negativa forte entre notas e evasão

#### 4. Distorção Idade-Série
Idade adequada: 5.2% de evasão
Pequeno atraso: 12.8% de evasão
Médio atraso: 21.3% de evasão
Grande atraso: 34.7% de evasão

#### 5. Análise por Turno
Noturno: 18.5% de evasão 
Vespertino: 10.2% de evasão
Matutino: 8.3% de evasão

### Matriz de Correlação

![Matriz de Correlação](reports/figures/correlation_matrix.png)

**Variáveis mais correlacionadas com evasão:**
1. Distorção idade-série (0.52)
2. Faltas anuais (0.48)
3. Nota de Matemática (-0.41)
4. Reprovações anteriores (0.38)

---

## Modelagem

### Algoritmos Utilizados

| Modelo | Descrição | Hiperparâmetros |
|--------|-----------|-----------------|
| **Random Forest** | Ensemble de árvores de decisão | `n_estimators=100`, `class_weight='balanced'` |
| **XGBoost** | Gradient boosting otimizado | `learning_rate=0.1`, `scale_pos_weight` |

### Feature Engineering

Foram criadas features adicionais para melhorar o poder preditivo:
- `media_notas`: Média das notas de Português e Matemática
- `baixo_desempenho`: Indicador de nota < 5 em qualquer matéria
- `risco_faltas`: Faltas anuais > 15 dias
- `risco_distorcao`: Distorção idade-série > 1 ano
- `engajamento`: Proxy calculado como notas/faltas

### Balanceamento de Classes

Devido ao desbalanceamento natural da variável alvo, foram aplicadas técnicas de balanceamento:
- `class_weight='balanced'` no Random Forest
- `scale_pos_weight` no XGBoost

---

## Resultados

### Comparação de Modelos

| Métrica | Random Forest | XGBoost |
|---------|---------------|---------|
| **Acurácia** | 0.8790 | 0.8797 |
| **Precisão** | 0.2511 | 0.4667 |
| **Recall** | 0.1639 | 0.0194 |
| **F1-Score** | 0.1983 | 0.0373 |
| **AUC-ROC** | 0.6723 | 0.6932 |

### Matriz de Confusão - Random Forest
Real Não [2605 67]
Sim [ 305 23]

### Curva ROC

![Curva ROC](reports/figures/roc_curve.png)

### Feature Importance

**Top 10 Features mais importantes (Random Forest):**

| Feature | Importância |
|---------|-------------|
| distorcao_idade_serie | 0.1823 |
| faltas_anuais | 0.1542 |
| nota_matematica | 0.1321 |
| media_notas | 0.1187 |
| nota_portugues | 0.1098 |
| risco_distorcao | 0.0876 |
| risco_faltas | 0.0765 |
| idade | 0.0654 |
| baixo_desempenho | 0.0432 |
| turno_noturno | 0.0211 |

---

## Conclusões e Recomendações

### Interpretação dos Resultados

O modelo desenvolvido é capaz de identificar alunos em risco de evasão com as seguintes características:

**Perfil de Alto Risco**:
- Distorção idade-série ≥ 2 anos
- Faltas ≥ 20 dias no ano
- Notas abaixo de 5 em Matemática
- Turno noturno
- Reprovações anteriores

**Limitações Atuais**:
- Recall baixo indica que muitos alunos em risco não são identificados
- Necessidade de mais features socioeconômicas
- Dados sintéticos limitam a generalização

### Recomendações Práticas

#### 1. **Intervenção Precoce**
- Identificar alunos com distorção idade-série > 1 ano no início do ano letivo
- Implementar programas de aceleração da aprendizagem

#### 2. **Monitoramento de Frequência**
- Criar sistema de alerta para alunos com mais de 5 faltas no mês
- Acionar equipe pedagógica para contato com a família

#### 3. **Apoio Pedagógico**
- Oferecer reforço escolar para alunos com notas abaixo de 5
- Foco especial em Matemática

#### 4. **Políticas Específicas por Turno**
- Turno noturno: programas de auxílio-transporte e material
- Flexibilização para alunos-trabalhadores

#### 5. **Sistema de Alerta**
- Implementar dashboard com scores de risco atualizados mensalmente
- Priorizar intervenções para alunos no top 10% de risco

---
