# Previsão do valor de veículos com base na Tabela FIPE

Projeto final da disciplina de **Aprendizado de Máquina** (UFS).

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Danillitos/UFS-Trabalho-IA/blob/main/projeto_final_fipe.ipynb)

## Objetivo

Construir um modelo de **regressão** capaz de estimar o valor de mercado de um veículo na Tabela FIPE a partir de suas características (marca, cilindrada, câmbio, idade) e da data de referência, permitindo estimar quanto um carro valia — ou valeria — em um mês/ano dentro do período coberto pelos dados (2001–2022) e analisar como os preços evoluem com a idade do veículo e com o tempo.

## Integrantes

- Danillo Matos Garcez
- Luciano Davi Martins de Santana Silva
- Nicolas de Carvalho Lessa

## Fonte dos dados

[Tabela Fipe — Kaggle (`franckepeixoto/tabela-fipe`)](https://www.kaggle.com/datasets/franckepeixoto/tabela-fipe): 466.020 registros de preços médios publicados pela FIPE entre janeiro/2001 e agosto/2022. O download é feito **dentro do notebook** via `kagglehub` (fonte pública, sem credenciais) — nenhum arquivo local é necessário.

## Tipo da tarefa

**Regressão** — o atributo-alvo (`valor`, em R$) é numérico contínuo.

## Organização dos arquivos

| Arquivo | Conteúdo |
|---|---|
| `projeto_final_fipe.ipynb` | Notebook completo: identificação do problema, compreensão dos dados, análise exploratória, pré-processamento, separação dos dados, modelagem, avaliação e discussão |
| `README.md` | Este arquivo |
| `requirements.txt` | Dependências para execução local (o Colab não precisa) |
| `.gitignore` | Exclui ambiente virtual e caches do versionamento |

## Como executar no Google Colab

1. Clique no badge **"Open in Colab"** no topo deste README (ou acesse o Colab, `Arquivo → Abrir notebook → GitHub` e cole a URL deste repositório);
2. Execute as células em ordem (`Ambiente de execução → Executar tudo`). O download do dataset é automático.

⏱️ **Atenção:** as células de modelagem (seções 7 e 8) treinam Random Forest com validação cruzada sobre 373 mil registros. Em uma máquina com muitos núcleos levam ~15 minutos; no Colab gratuito (2 vCPUs) podem levar bem mais — é o comportamento esperado, não um travamento.

### Execução local (opcional)

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Depois, abra o notebook no VS Code/Jupyter e **selecione o kernel do `.venv`** antes de executar.

## Modelos utilizados

- **Baseline:** `DummyRegressor` (mediana do treino);
- **Regressão linear** (`LinearRegression`);
- **Árvore de decisão** (`DecisionTreeRegressor`);
- **Random Forest** (`RandomForestRegressor`, 100 árvores) — **modelo final escolhido**.

Todos treinados sobre `log10(valor)` via `TransformedTargetRegressor` (com previsões convertidas de volta a reais), dentro de um `Pipeline` com imputação, escalonamento e one-hot encoding ajustados apenas no treino (sem vazamento).

## Principais resultados

Comparação por validação cruzada (3 folds, no treino) e avaliação final no teste reservado (20%, estratificado por faixas do alvo):

| Modelo | MAE (CV) | R² (CV) |
|---|---|---|
| Baseline (mediana) | R$ 95.332 | −0,06 |
| Regressão linear | R$ 36.023 | 0,78 |
| Árvore de decisão | R$ 18.971 | 0,91 |
| **Random Forest** | **R$ 17.738** | **0,93** |

**Random Forest no conjunto de teste:** MAE R$ 17.118 · RMSE R$ 84.545 · erro mediano R$ 2.373 · MAPE 12,9% · R² 0,933.

No teste de robustez temporal (treinar até 2020 e prever 2021–22), o desempenho degrada para MAE R$ 54.970 com viés de −20,4%: o modelo interpola muito bem dentro do período coberto, mas não antecipa o choque de preços da pandemia — florestas não extrapolam tendências. A discussão completa (erros, limitações e melhorias) está na seção 8 do notebook.

## Vídeo

📹 **Link do vídeo:** [Vídeo](https://drive.google.com/file/d/1SbjSYsGPvcag_OzlWRDlLDN3Juji9dq4/view)
