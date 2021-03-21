---
title: 'Estúdio ML (clássico): Migrar para Azure Machine Learning'
description: Migrar do Studio (clássico) para a Azure Machine Learning para uma plataforma de ciência de dados modernizada.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fda34a7ee06d35846bcec571e904297d0421c38f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565370"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrar para o Azure Machine Learning

Neste artigo, aprende-se a migrar os ativos do Studio (clássico) para a Azure Machine Learning. Neste momento, para migrar recursos, tem de reconstruir manualmente as suas experiências.

A Azure Machine Learning fornece uma plataforma de ciência de dados modernizada que combina abordagens sem código e código.. Para saber mais sobre as diferenças entre Studio (clássico) e Azure Machine Learning, consulte a secção [de Aprendizagem automática de Azure.](#step-1-assess-azure-machine-learning)


## <a name="recommended-approach"></a>Abordagem recomendada

Para migrar para Azure Machine Learning, recomendamos a seguinte abordagem:

> [!div class="checklist"]
> * Passo 1: Avaliar a aprendizagem da máquina de Azure
> * Passo 2: Criar um plano de migração
> * Passo 3: Reconstruir experiências e serviços web
> * Passo 4: Integrar aplicativos de clientes
> * Passo 5: Limpar ativos do Estúdio (clássico)


## <a name="step-1-assess-azure-machine-learning"></a>Passo 1: Avaliar a aprendizagem da máquina de Azure
1. Saiba mais sobre [a Azure Machine Learning;](https://azure.microsoft.com/services/machine-learning/) são benefícios, custos e arquitetura.

1. Compare as capacidades de Azure Machine Learning e Studio (clássico).

    >[!NOTE]
    > A funcionalidade **de designer** em Azure Machine Learning proporciona uma experiência semelhante ao Studio (clássico). No entanto, o Azure Machine Learning também fornece [fluxos de trabalho robustos de primeiro código](../concept-model-management-and-deployment.md) como alternativa. Esta série de migração centra-se no designer, uma vez que é mais semelhante à experiência studio (clássica).

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Verifique se os seus módulos críticos do Estúdio (clássico) são suportados no designer de Machine Learning Azure. Para mais informações, consulte a tabela de [mapeamento de módulos studio (clássico) e designer](#studio-classic-and-designer-module-mapping) abaixo.

4. Criar um espaço de trabalho para [aprendizagem automática Azure.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal)

## <a name="step-2-create-a-migration-plan"></a>Passo 2: Criar um plano de migração

1. Identifique os conjuntos de **dados** do Studio (clássicos), **modelos** e **serviços web** que pretende migrar.

1. Determine o impacto que uma migração terá no seu negócio.

1. Criar um plano de migração.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Passo 3: Reconstruir experiências e serviços web

1. [Migrar conjuntos de dados para Azure Machine Learning](migrate-register-dataset.md).
1. Use o designer para [reconstruir experiências.](migrate-rebuild-experiment.md)
1. Utilize o designer para [recolocar os serviços web](migrate-rebuild-web-service.md).

    >[!NOTE]
    > O Azure Machine Learning também suporta fluxos de trabalho de código para conjuntos de [dados,](../how-to-create-register-datasets.md) [formação](../how-to-set-up-training-targets.md)e [implantação.](../how-to-deploy-and-where.md)

## <a name="step-4-integrate-client-apps"></a>Passo 4: Integrar aplicativos de clientes

1. Modifique as aplicações do cliente que invocam os serviços web do Studio (clássico) para utilizar os seus novos [pontos finais de Aprendizagem automática Azure.](migrate-rebuild-integrate-with-client-app.md)

## <a name="step-5-cleanup-studio-classic-assets"></a>Passo 5: Ativos do Cleanup Studio (clássico)

1. [Limpe os ativos do Studio (clássico)](export-delete-personal-data-dsr.md) para evitar encargos adicionais. Pode querer reter ativos para recuo até ter validado cargas de trabalho de Aprendizagem automática do Azure.

## <a name="studio-classic-and-designer-module-mapping"></a>Estúdio (clássico) e designer de mapeamento de módulos

Consulte a seguinte tabela para vê-lo quais módulos usar enquanto reconstrói experiências studio (clássicas) no designer.


> [!IMPORTANT]
> O designer implementa módulos através de pacotes Python de código aberto em vez de pacotes C# como Studio (clássico). Devido a esta diferença, a saída dos módulos de design pode variar ligeiramente dos seus homólogos do Studio (clássico).


|Categoria|Módulo estúdio (clássico)|Módulo de design de substituição|
|--------------|----------------|--------------------------------------|
|Entrada e saída de dados|- Introduzir dados manualmente </br> - Dados de Exportação </br> - Dados de Importação </br> - Modelo treinado de carga </br> - Desembalar conjuntos de dados com fecho de malas|- Introduzir dados manualmente </br> - Dados de Exportação </br> - Dados de Importação|
|Conversões de formato de dados|- Converter em CSV </br> - Converter para conjunto de dados </br> - Converter em ARFF </br> - Converter para SVMLight </br> - Converter em TSV|- Converter em CSV </br> - Converter para conjunto de dados|
|Transformação de Dados - Manipulação|- Adicionar Colunas</br> - Adicionar linhas </br> - Aplicar a Transformação SQL </br> - Limpeza de dados em falta </br> - Converter para Valores Indicadores </br> - Editar Metadados </br> - Juntar dados </br> - Remover linhas duplicadas </br> - Selecione colunas no conjunto de dados </br> - Selecione Colunas Transformar </br> - SMOTE </br> - Valores Categóricos do Grupo|- Adicionar Colunas</br> - Adicionar linhas </br> - Aplicar a Transformação SQL </br> - Limpeza de dados em falta </br> - Converter para Valores Indicadores </br> - Editar Metadados </br> - Juntar dados </br> - Remover linhas duplicadas </br> - Selecione colunas no conjunto de dados </br> - Selecione Colunas Transformar </br> - SMOTE|
|Transformação de Dados – Escala e Redução |- Valores de clip </br> - Dados de grupo em caixotes </br> - Normalizar dados </br>- Análise principal de componentes |- Valores de clip </br> - Dados de grupo em caixotes </br> - Normalizar dados|
|Transformação de Dados – Amostra e Divisão|- Partição e Amostra </br> - Dados divididos|- Partição e Amostra </br> - Dados divididos|
|Transformação de Dados – Filtro |- Aplicar filtro </br> - Filtro FIR </br> - Filtro IIR </br> - Filtro Mediano </br> - Filtro médio móvel </br> - Filtro limiar </br> - Filtro definido pelo utilizador||
|Transformação de Dados – Aprendizagem com Condes |- Build Counting Transform </br> - Tabela de Contagem de Exportações </br> - Tabela de Contagem de Importações </br> - Fusão Contagem Transformar</br>  - Modificar os parâmetros da tabela da contagem||
|Seleção de Recursos |- Seleção de recursos baseados em filtros </br> - Análise Discriminante Linear de Fisher  </br> - Importância da Característica de Permutação |- Seleção de recursos baseados em filtros </br>  - Importância da Característica de Permutação|
| Modelo - Classificação| - Floresta de Decisão Multiclasse </br> - Multiclasse Decision Jungle  </br> - Regressão Logística Multiclass  </br>- Rede Neural Multiclasse  </br>- One-vs-All Multiclass </br>- Two-Class Perceptron Médio </br>- Two-Class Bayes Point Machine </br>- Two-Class Árvore de Decisão Reforçada  </br> - Two-Class Decisão Floresta  </br> - Two-Class Decisão Selva  </br> - Two-Class Locally-Deep SVM </br> - Two-Class Regressão Logística  </br> - Two-Class Rede Neural </br> - Two-Class máquina de vetor de apoio  | - Floresta de Decisão Multiclasse </br>  - Multiclass Boost Decision Tree  </br> - Regressão Logística Multiclass </br> - Rede Neural Multiclasse </br> - One-vs-All Multiclass  </br> - Two-Class Perceptron Médio  </br> - Two-Class Árvore de Decisão Reforçada  </br> - Two-Class Decisão Floresta </br>- Two-Class Regressão Logística </br> - Two-Class Rede Neural </br>- Two-Class máquina de vetor de apoio  |
| Modelo - Agrupamento| - K-significa agrupamento| - K-significa agrupamento|
| Modelo - Regressão| - Regressão Linear Bayesiana  </br> - Regressão da Árvore da Decisão Reforçada  </br>- Decisão De Regressão Florestal  </br> - Fast Forest Quantile Regression  </br> - Regressão Linear </br> - Regressão da Rede Neural </br> - Regressão Ordinal Poisson Regression| - Regressão da Árvore da Decisão Reforçada  </br>- Decisão De Regressão Florestal  </br> - Fast Forest Quantile Regression </br> - Regressão Linear  </br> - Regressão da Rede Neural </br> - Regressão de Poisson|
| Modelo – Deteção de Anomalias| - One-Class SVM  </br> - deteção PCA-Based de anomalias | - deteção PCA-Based de anomalias|
| Machine Learning – Avaliar  | - Modelo de validação cruzada  </br>- Avaliar Modelo  </br>- Avaliar o Recomendador | - Modelo de validação cruzada  </br>- Avaliar Modelo </br> - Avaliar o Recomendador|
| Machine Learning – Comboio| - Varrer clustering  </br> - Modelo de deteção de anomalias de comboio </br>- Modelo de Agrupamento de Comboios  </br> - Recomendador de caixa de fósforos de comboio -</br> Preparar Modelo  </br>- Sintonizar hiperparímetros modelo| - Modelo de deteção de anomalias de comboio  </br> - Modelo de Agrupamento de Comboios </br> - Modelo de Comboio -</br> - Modelo PyTorch de trem  </br>- Recomendador SVD do comboio  </br>- Train Wide and Deep Recommender </br>- Sintonizar hiperparímetros modelo|
| Machine Learning – Pontuação| - Aplicar a Transformação  </br>- Atribuir dados a clusters  </br>- Pontuar Matchbox Recommender </br> - Modelo de pontuação|- Aplicar a Transformação  </br> - Atribuir dados a clusters </br> - Modelo de imagem de pontuação  </br> - Modelo de pontuação </br>- Pontuação SVD Recommender </br> -Pontuar Recomendador largo e profundo|
| Módulos da Biblioteca OpenCV| - Importar Imagens </br>- Classificação de imagem em cascata pré-treinada | |
| Módulos de Língua Python| - Executar o Script Python| - Executar o Script Python  </br> - Criar modelo Python |
| Módulos de Linguagem R  | - Executar O Script R  </br> - Criar Modelo R| - Executar O Script R|
| Funções Estatísticas | - Aplicar operação matemática </br>- Estatísticas Elementares do Cálculo  </br>- Correlação Linear compute computacional  </br>- Avaliar a função de probabilidade  </br>- Substituir valores discretos  </br>- Resumir dados  </br>- Hipótese de teste utilizando t-Test| - Aplicar operação matemática  </br>- Resumir dados|
| Análise de Texto| - Detetar línguas  </br>- Extrair frases-chave do texto  </br>- Extrair funcionalidades N-Gram de texto  </br>- Hashing de Recurso </br>- Alocação latente de Dirichlet  </br>- Reconhecimento de Entidades Nomeadas </br>- Texto pré-processamento  </br>- Pontuar o Modelo 7-10 do Vowpal Wabbit  </br>- Pontuar Vowpal Wabbit Versão 8 Modelo </br>- Modelo 7-10 do Trem Vowpal Wabbit  </br>- Modelo Wabbit Wabbit 8 do comboio |- Converter palavra em vetor </br> - Extrair funcionalidades N-Gram de texto </br>- Hashing de Recurso  </br>- Alocação latente de Dirichlet </br>- Texto pré-processamento  </br>- Marcação Modelo Wabbit Vowpal </br> - Modelo de Wabbit Vowpal de trem|
| Séries Tempo a Tempo| - Deteção de Anomalias em Séries Tempotadas | |
| Serviço Web | - Entrada </br> - Saída | - Entrada </br>  - Saída|
| Imagem Digitalizada| | - Aplicar a Transformação de Imagem </br> - Converter para Diretório de Imagem </br> - Init Image Transformation </br> - Diretório de Imagem Dividido  </br> - Classificação de imagem DenseNet   </br>- Classificação de Imagem ResNet |

Para obter mais informações sobre como utilizar individualmente os módulos de design, consulte a referência do [módulo de design.](../algorithm-module-reference/module-reference.md)

### <a name="what-if-a-designer-module-is-missing"></a>E se faltar um módulo de designer?

O designer de Machine Learning Azure contém os módulos mais populares do Studio (clássico). Também inclui novos módulos que aproveitam as mais recentes técnicas de aprendizagem automática. 

Se a sua migração estiver bloqueada devido à falta de módulos no designer, contacte-nos [criando um bilhete de apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="example-migration"></a>Migração exemplo

A seguinte migração de experiências destaca algumas das diferenças entre Studio (clássico) e Azure Machine Learning.

### <a name="datasets"></a>Conjuntos de dados

Em Studio (clássico), **os conjuntos de dados** foram guardados no seu espaço de trabalho e só podiam ser usados pelo Studio (clássico).

![conjunto de dados clássicos de preço do automóvel](./media/migrate-overview/studio-classic-dataset.png)

No Azure Machine Learning, **os conjuntos de dados** estão registados no espaço de trabalho e podem ser utilizados em toda a Azure Machine Learning. Para obter mais informações sobre os benefícios dos conjuntos de dados Azure Machine Learning, consulte [o acesso de dados Secure](../concept-data.md#reference-data-in-storage-with-datasets).

![conjunto de dados de preço-aml de preço-automóvel](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Pipeline

Em Studio (clássico), **as experiências** continham a lógica de processamento para o seu trabalho. Criaste experiências com módulos de arrastar e largar.


![automóvel-preço-clássico-experiência](./media/migrate-overview/studio-classic-experiment.png)

No Azure Machine Learning, os **gasodutos** contêm a lógica de processamento do seu trabalho. Pode criar oleodutos com módulos de arrasto e queda ou escrevendo código.

![automóvel-preço-aml-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Ponto final do serviço web

Em Studio (clássico), a **API REQUEST/RESPOND** foi utilizada para previsão em tempo real. A **API DE EXECUÇÃO DE LOTE** foi utilizada para a previsão ou requalificação do lote.

![automóvel-preço-clássico-webservice](./media/migrate-overview/studio-classic-web-service.png)

Em Azure Machine Learning, **os pontos finais em tempo real** são usados para a previsão em tempo real. **Os pontos finais do gasoduto** são utilizados para a previsão ou requalificação do lote.

![ponto final de preço-automóvel](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu os requisitos de alto nível para migrar para Azure Machine Learning. Para etapas detalhadas, consulte os outros artigos da série de migração Studio (clássica):

1. **Visão geral da migração**.
1. [Conjunto de dados migrar](migrate-register-dataset.md).
1. [Reconstruir um pipeline de treino studio (clássico).](migrate-rebuild-experiment.md)
1. [Reconstruir um serviço web Studio (clássico).](migrate-rebuild-web-service.md)
1. [Integre um serviço web Azure Machine Learning com aplicações para clientes.](migrate-rebuild-integrate-with-client-app.md)
1. [Migrar Executar O Guião R](migrate-execute-r-script.md).






