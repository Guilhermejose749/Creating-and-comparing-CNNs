# Relatório Técnico: Implementação e Análise Comparativa de Redes Neurais Convolucionais

## 1. Introdução e Preparação dos Dados
Este relatório detalha a construção, otimização e avaliação de uma **Rede Neural Convolucional (CNN) modular** desenvolvida do zero, comparando o seu desempenho preditivo com a arquitetura pré-treinada **ResNet18**. A avaliação foi conduzida sobre dois conjuntos de dados distintos: **MNIST** (dígitos manuscritos) e **CIFAR-10** (objetos e animais).

### Divisão do Conjunto de Dados
* **Treino/Validação:** 80% / 20%.
* **Teste:** Conjunto de teste original isolado para avaliação final.
* **Batch Size:** 64 amostras.

### Diferenças de Pré-processamento
* **CNN Customizada:** As imagens mantiveram dimensões originais (28x28 para MNIST; 32x32 para CIFAR-10). Foi aplicada apenas conversão para tensor e normalização simples (escala 0.5).
* **ResNet18:** Exigiu redimensionamento para **224x224 pixels**. No MNIST, houve conversão de tons de cinza para **RGB (3 canais)** e aplicação da normalização específica da **ImageNet**. Esse redimensionamento de imagens pequenas para resoluções altas inseriu um efeito de "borrão" (blur) e alterou a pureza dos dados originais.

---

## 2. Metodologia de Otimização (Random Search)
A arquitetura da CNN customizada foi definida através de um processo de **Random Search**, que explorou 10 combinações de hiperparâmetros com treinamento de até 20 épocas por tentativa e **Early Stopping** (paciência de 4 épocas) baseado na *Loss* de validação.

### Espaço de Busca (Hyperparameter Grid)
| Parâmetro | Valores Explorados |
| :--- | :--- |
| **Nº de Blocos** | [1, 2, 3] |
| **Convoluções por Bloco** | [1, 2, 3] |
| **Profundidade (Filtros)** | [16, 32, 64, 128] (Ordenação crescente) |
| **Tamanho do Kernel** | [3x3, 5x5] |
| **Pooling** | [Sim, Não] (Obrigatório no último bloco) |
| **Neurônios FC** | [128, 256, 512] |
| **Dropout** | [0.0, 0.2, 0.3, 0.5] |
| **Otimizadores** | [Adam, SGD] |
| **Learning Rate** | [1e-2, 1e-3, 5e-4] |
| **Momentum (SGD)** | [0.9, 0.95, 0.99] |
| **Weight Decay (L2)** | [0.0, 1e-4, 1e-5] |

---

## 3. Análise Visual de Ativações (Feature Maps)
A análise visual foi realizada para os números **0, 4 e 8** no MNIST e para as classes **airplane, cat e ship** no CIFAR-10.

* **Camadas Iniciais:** A rede demonstrou aprender principalmente os **contornos e bordas** dos objetos.
* **Camadas Profundas:** A segunda camada apresenta um nível maior de **abstração**, onde os mapas de características tornam-se menos legíveis para humanos, mas capturam padrões complexos necessários para a classificação.
* **Comparativo Visual:** No CIFAR-10, embora a ResNet18 utilize imagens com resize (blur), os feature maps resultantes foram consideravelmente mais **nítidos e claros** que os do modelo customizado, demonstrando que a arquitetura pré-treinada isola melhor o objeto do fundo.

---

## 4. Resultados e Comparação de Desempenho

### 4.1 Desempenho Geral (Acurácia no Teste)
| Dataset | CNN Customizada | ResNet18 (Transfer Learning) |
| :--- | :--- | :--- |
| **MNIST** | **99.39%** | 96.53% |
| **CIFAR-10** | 76.72% | **80.73%** |

### 4.2 Análise por Classe
* **MNIST:** A CNN Customizada superou a ResNet18. Isso se justifica pelo fato de a ResNet ter sofrido com a distorção do resize e a mudança de canais, enquanto a CNN customizada operou nos dados originais mais nítidos. A ResNet teve dificuldade nas classes '5' (93.0%) e '2' (95.8%).
* **CIFAR-10:** A ResNet18 obteve vantagem devido à sua profundidade e blocos residuais, que lidam melhor com texturas animais e cores. A classe mais difícil para ambas foi **'cat'** (Gato), enquanto as classes **'frog'** (Sapo) e **'truck'** (Caminhão) foram as mais fáceis.

---

## 5. Conclusões e Melhorias
Os resultados indicam que, para problemas de baixa complexidade visual como o MNIST, modelos menores e específicos para a resolução nativa são superiores. Para cenários complexos como o CIFAR-10, o *Transfer Learning* de redes profundas apresenta maior robustez.

**Sugestões de melhoria:**
1.  **Aumento de Trials:** Expandir o Random Search para 50 ou 100 tentativas.
2.  **Aumento de Épocas:** Permitir que o modelo treine por mais tempo (50+ épocas).
3.  **Regularização:** Implementar *Batch Normalization* entre as camadas convolucionais.
4.  **Data Augmentation:** Utilizar rotações e espelhamentos para aumentar a generalização no CIFAR-10.