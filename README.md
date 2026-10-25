# 🚚 Análise de Eficiência Logística — Centro de Distribuição

![MySQL](https://img.shields.io/badge/MySQL-Banco%20Relacional-blue)
![Python](https://img.shields.io/badge/Python-3.12-yellow)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-orange)
![XGBoost](https://img.shields.io/badge/XGBoost-Gradient%20Boosting-green)
![SHAP](https://img.shields.io/badge/SHAP-Explicabilidade-red)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811)
![Status](https://img.shields.io/badge/Status-Concluído-brightgreen)

## 📋 Sobre o Projeto

Análise completa de eficiência operacional de um **centro de distribuição**, cobrindo todo o ciclo de trabalho de um profissional de dados: da modelagem do banco relacional à previsão de atrasos com Machine Learning, finalizando com um dashboard interativo no Power BI.

O projeto é inspirado na operação real de um CD (múltiplas docas, câmaras frias, frota de entrega) e simula o fluxo de pedidos, entregas e o desafio central da logística: **entregar no prazo**.

---

## 🎯 Objetivo

- Medir a **taxa de entrega no prazo**, o principal KPI operacional de um CD
- Identificar **gargalos** — quais motoristas, cargas e regiões concentram atrasos
- Construir um modelo de ML capaz de **prever o risco de atraso** de uma entrega antes dela sair
- Apresentar os resultados em um **dashboard** que apoie a tomada de decisão

---

## 🛠️ Tecnologias Utilizadas

| Ferramenta | Uso |
|---|---|
| **MySQL** | Banco de dados relacional (modelagem + consultas) |
| **MySQL Workbench** | Modelagem EER e Forward Engineer |
| **Python** | Análise, ML e integração |
| **Pandas / NumPy** | Manipulação de dados |
| **PyMySQL** | Conexão Python ↔ MySQL |
| **Scikit-learn** | Pipeline e modelos de ML |
| **XGBoost** | Gradient boosting |
| **SHAP** | Explicabilidade do modelo |
| **Power BI** | Dashboard interativo |

---

## 🗄️ Modelagem do Banco (MySQL)

Banco `logistica_cd` com **7 tabelas** relacionadas:

```
clientes ──< pedidos ──< entregas >── motoristas
                │
                └──< itens_pedido >── camaras_frias ──< docas
```

| Tabela | Descrição |
|---|---|
| `clientes` | Clientes por segmento, cidade e estado |
| `motoristas` | Frota e veículos |
| `docas` | Docas de carga e descarga |
| `camaras_frias` | Câmaras com faixas de temperatura (resfriado, congelado, exportação) |
| `pedidos` | Pedidos com valor, peso e status |
| `entregas` | Saída, entrega e cumprimento de prazo |
| `itens_pedido` | Itens de cada pedido |

---

## ⚙️ Metodologia

### 1. Banco de Dados (SQL)
- Modelagem EER com chaves primárias e estrangeiras (relações 1:N)
- Criação das tabelas via Forward Engineer
- População e consultas analíticas com `JOIN`, `GROUP BY`, `HAVING`

### 2. Análise Exploratória (Python + SQL)
- Conexão ao MySQL via PyMySQL
- KPIs extraídos direto do banco: taxa no prazo, desempenho por motorista, faturamento por segmento, impacto do peso

### 3. Feature Engineering
- Criação de variáveis: `carga_pesada`, `prazo_apertado`, `fim_semana`, `valor_por_kg`
- Codificação de variáveis categóricas (motorista, veículo, segmento, estado)

### 4. Machine Learning
- **Pipeline** com normalização + modelo
- Comparação de 3 algoritmos: Regressão Logística, Random Forest e XGBoost
- Avaliação com Precision, Recall e AUC-ROC

### 5. Explicabilidade (SHAP)
- Identificação dos fatores que mais pesam no atraso

### 6. Dashboard (Power BI)
- 3 páginas conectadas à base de dados

---

## 🤖 Resultados do Modelo

| Modelo | AUC-ROC (teste) |
|---|---|
| Regressão Logística | 0.68 |
| Random Forest | 0.67 |
| XGBoost | 0.65 |

> **Nota de honestidade técnica:** a avaliação foi feita sobre o conjunto de **teste** (dados que o modelo nunca viu), evitando *data leakage*. A acurácia no teste ficou em torno de 66% — a métrica real do modelo, diferente da acurácia na base completa, que é inflada por incluir dados de treino.

### 🔍 Fatores de atraso (SHAP)

Os principais fatores que elevam o risco de atraso:

1. **Quantidade de itens** — mais itens, mais tempo de separação
2. **Peso da carga** — cargas acima de 500kg atrasam mais
3. **Prazo acordado** — prazo de 1 dia deixa menos margem

Esses achados do modelo **confirmam** o que a análise descritiva mostrou no dashboard.

---

## 📊 Dashboard (Power BI)

### Página 1 — Visão Geral
- Cartões: total de entregas, taxa no prazo, faturamento, ticket médio
- Faturamento por segmento
- Evolução mensal da taxa no prazo

![Visão Geral](imagens_projeto/visao_geral.png)

### Página 2 — Desempenho Operacional
- Ranking de motoristas (com os abaixo da meta destacados em vermelho)
- Taxa no prazo por tipo de carga (leve vs pesada)
- Entregas por estado

![Desempenho Operacional](imagens_projeto/desempenho.png)

### Página 3 — Previsão de Risco
- Classificação de risco de atraso (Alto / Médio / Baixo)
- Entregas em risco alto (saída do modelo de ML)
- Tabela acionável com as entregas de maior risco

![Previsão de Risco](imagens_projeto/previsao_risco.png)

---

## 💡 Principais Insights

- A taxa de entrega no prazo oscila entre **61% e 75%** ao longo dos meses — abaixo do padrão de mercado (acima de 95%)
- A diferença entre o melhor e o pior motorista passa de **35 pontos percentuais**
- **Carga pesada** reduz a taxa no prazo, e **estados distantes** (CE, AM, MS) concentram os maiores riscos
- O modelo permite **antecipar** quais entregas vão atrasar, possibilitando ação preventiva (trocar motorista, redistribuir carga, avisar o cliente)

---

## 📁 Estrutura do Projeto

```
analise-logistica-centro-distribuicao/
│
├── 01_analise_logistica.ipynb   # Notebook completo (SQL + Python + ML)
├── projeto(logistica).pbix      # Dashboard Power BI
├── entregas_powerbi.csv         # Base exportada para o dashboard
└── .gitignore
```

---

## 🚀 Como Executar

1. Instale o MySQL e crie o banco `logistica_cd`
2. Instale as dependências:
```bash
pip install pymysql pandas numpy scikit-learn xgboost shap
```
3. No notebook, substitua `SUA_SENHA` pela senha do seu MySQL
4. Rode as células para popular o banco, treinar o modelo e gerar o CSV
5. Abra o `.pbix` no Power BI Desktop

---

## 👤 Autor

**João Casimiro**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-João%20Casimiro-blue)](https://www.linkedin.com/in/jo%C3%A3o-raphael-casimiro-de-almeida-63681a23b/)
[![GitHub](https://img.shields.io/badge/GitHub-casimiro10-black)](https://github.com/casimiro10)

> Projeto de portfólio desenvolvido durante a graduação em Ciências de Dados (Unicesumar), inspirado na operação real de um centro de distribuição.
