# SELENE — Classificação de fungos em cogumelos

Este repositório contém um notebook experimental do projeto **SELENE**, voltado à identificação de fungos e anomalias em imagens de cogumelos. O modelo usa a MobileNetV2 pré-treinada no ImageNet como extrator de características e foi preparado para treinamento no Google Colab.

> **Estado do projeto:** protótipo em desenvolvimento. A configuração de treinamento do notebook é binária; os trechos de inferência e algumas métricas ainda precisam ser alinhados com essa configuração antes de serem usados como resultado confiável.

## Conteúdo

- [`Selene_MobileNet2.ipynb`](./Selene_MobileNet2.ipynb) — notebook com preparação dos dados, treinamento, avaliação e funções experimentais de inferência.

## Como o modelo está configurado

- Entrada de imagens redimensionadas para `224 × 224` pixels.
- MobileNetV2 com pesos do ImageNet e camada de classificação original removida.
- Camadas convolucionais da MobileNetV2 congeladas durante o treinamento.
- Cabeça de classificação com Global Average Pooling, camada densa de 64 unidades, Dropout de 50% e saída sigmoide binária.
- Aumento de dados no conjunto de treino, incluindo rotação, deslocamento, zoom, alteração de brilho, desfoque e ruído.
- Treinamento com Adam, entropia cruzada binária, pesos para compensar o desbalanceamento das classes e parada antecipada.

## Preparação dos dados

O notebook espera o conjunto de dados no Google Drive, neste caminho:

```text
/content/drive/MyDrive/dataset_shimeji/
├── treino/
│   ├── anomalia/
│   └── saudavel/
└── validacao/
    ├── anomalia/
    └── saudavel/
```

Cada subpasta deve conter imagens da classe correspondente. Com `flow_from_directory`, as classes são associadas aos rótulos em ordem alfabética; no exemplo do notebook, `anomalia` corresponde a `0` e `saudavel` a `1`.

## Executar no Google Colab

1. Abra `Selene_MobileNet2.ipynb` no Google Colab.
2. Disponibilize o conjunto de dados no Google Drive seguindo a estrutura acima.
3. Execute as células em ordem. A primeira montagem do Drive pode solicitar autorização.
4. Ao final, o notebook pretende salvar o modelo em `dataset_shimeji/modelo_mobilenet_shimeji.keras` no Drive. O melhor checkpoint de treinamento é configurado como `melhor_modelo_binario.keras` no ambiente do notebook.

O notebook importa TensorFlow/Keras, OpenCV, NumPy, scikit-learn e Matplotlib, além da integração `google.colab`. O ambiente Colab é o caminho previsto; a configuração de um ambiente local não está documentada neste repositório.

## Limitações conhecidas

- O treinamento e o gerador de dados estão configurados para **duas classes**, mas a função de inferência do notebook lista três classes (`com_fungo`, `defeituoso` e `saudavel`) e trata a saída binária como se fosse multiclasse.
- O bloco de download procura `melhor_modelo_mobilenet.keras`, nome diferente do checkpoint configurado no treinamento (`melhor_modelo_binario.keras`).
- A interpretação das métricas da matriz de confusão precisa ser revisada: os rótulos são `anomalia = 0` e `saudavel = 1`, mas parte do relatório identifica as taxas como se a classe positiva fosse anomalia.
- Portanto, os resultados de inferência e as métricas exibidos pelo notebook devem ser validados e corrigidos antes de qualquer uso operacional. O protótipo não substitui avaliação especializada.

## Próximos passos

- Aumentar o dataset e retreinar o modelo para reduzir os falsos-negativos.
- Documentar os resultados experimentais após validação em um conjunto de teste independente (in loco).
