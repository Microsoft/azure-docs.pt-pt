---
title: Usar a interface do ML automatizada do Azure para treinar & implantar modelos
titleSuffix: Azure Machine Learning service
description: Crie, gerencie e implante experiências automatizadas de aprendizado de máquina na página de aterrissagem do espaço de trabalho Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 3ee15b5485f4fc0f81788107ce2378c65085e000
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910424"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learnings-workspace-landing-page-preview"></a>Criar, explorar e implantar experimentos automatizados de aprendizado de máquina com a página de aterrissagem do espaço de trabalho Azure Machine Learning (versão prévia)

 Neste artigo, você aprenderá a criar, explorar e implantar experimentos automatizados de aprendizado de máquina na página inicial do espaço de trabalho do Azure Machine Learning sem uma única linha de código. O aprendizado de máquina automatizado automatiza o processo de seleção do melhor algoritmo a ser usado para seus dados específicos, para que você possa gerar um modelo de aprendizado de máquina rapidamente. [Saiba mais sobre o aprendizado de máquina automatizado](concept-automated-ml.md).

 Se você preferir uma experiência mais baseada em código, também poderá [configurar seus experimentos de aprendizado de máquina automatizados no Python](how-to-configure-auto-train.md) com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Uma área de trabalho de serviço do Azure Machine Learning. Consulte [criar um Azure Machine Learning espaço de trabalho de serviço](how-to-manage-workspace.md).

## <a name="get-started"></a>Introdução


1. Entre na [página de aterrissagem do espaço de trabalho](https://ml.azure.com/workspaceportal/). 

1. Selecione sua assinatura e seu espaço de trabalho. 

1. Navegue até o painel esquerdo. Selecione **ml automatizado** na seção **criação** .

[![Painel de navegação portal do Azure](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Se esta for a primeira vez que você faz qualquer experimento, você verá a tela **introdução** . 

Caso contrário, você verá o painel **automatizado do Machine Learning** com uma visão geral de todos os seus experimentos de aprendizado de máquina automatizados, incluindo aqueles criados com o SDK. Aqui você pode filtrar e explorar suas execuções por data, nome do experimento e status de execução.

## <a name="create-and-run-experiment"></a>Criar e executar experimento

1. Selecione **criar experimento** e popular o formulário.

1. Insira um nome de teste exclusivo.

1. Selecione uma computação para o trabalho de criação de perfil de dados e treinamento. Uma lista de suas computações existentes está disponível na lista suspensa. Para criar uma nova computação, siga as instruções na etapa 4.

1. Selecione **criar uma nova computação** para configurar o contexto de computação para este experimento.

    Campo|Descrição
    ---|---
    Nome de computação| Insira um nome exclusivo que identifique o contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação.
    Definições adicionais| *Nó mínimo*: Insira o número mínimo de nós para a computação. O número mínimo de nós para a computação AML é 0. Para habilitar a criação de perfil de dados, você deve ter um ou mais nós. <br> *Nó máximo*: Insira o número máximo de nós para sua computação. O padrão é 6 nós para uma computação AML.
    
    Selecione **Criar**. A criação de uma nova computação pode levar alguns minutos.

    >[!NOTE]
    > Seu nome de computação indicará se a computação que você selecionou/criar está com a *criação de perfil habilitada*. (Consulte a seção [criação de perfil de dados](#profile) para obter mais detalhes).

1. Selecione um conjunto de um DataSet do seu contêiner de armazenamento ou crie um carregando um arquivo do seu computador local para o contêiner. A visualização pública dá suporte apenas a carregamentos de arquivos locais e contas de armazenamento de BLOBs do Azure.

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar no formato de tabela.
    >* O valor que você deseja prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de um de um arquivo em sua computação local, selecione **procurar** e, em seguida, selecione o arquivo. 

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Avançar** para carregá-lo no contêiner de armazenamento padrão criado automaticamente com seu espaço de trabalho ou escolha um contêiner de armazenamento que você deseja usar para o experimento. 

    1. Examine as **configurações e** o formulário de visualização para obter precisão. O formulário é populado de forma inteligente com base no tipo de arquivo. 

        Campo| Descrição
        ----|----
        Formato de arquivo| Define o layout e o tipo de dados armazenados em um arquivo.
        Delimitador| Um ou mais caracteres para especificar o limite entre regiões separadas e independentes em texto sem formatação ou outros fluxos de dados.
        Codificação| Identifica o bit para a tabela de esquema de caractere a ser usada para ler seu conjunto de seus.
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de uma, se houver, serão tratados.
        Ignorar linhas | Indica quantas linhas, se houver, são ignoradas no conjunto de registros.
    
        Selecione **Seguinte**.

    1. O formulário de **esquema** é preenchido de forma inteligente com base nas seleções no formulário **configurações e visualização** . Aqui configure o tipo de dados para cada coluna, examine os nomes de coluna e selecione quais colunas **não incluir** para o experimento. 
            
        Selecione **Avançar.**

1. Selecione o tipo de trabalho de treinamento: classificação, regressão ou previsão.

1. Selecionar coluna de destino; Essa é a coluna na qual você gostaria de fazer previsões.

1. Para previsão:
    1. Selecione a coluna de tempo: Esta coluna contém os dados de tempo a serem usados.

    1. Selecione o horizonte de previsão: Indique quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for necessário para prever no futuro, menor será a sua precisão. [Saiba mais sobre previsão e previsão horizonte](how-to-auto-train-forecast.md).

1. Adicional Configurações avançadas: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento. Caso contrário, os padrões serão aplicados com base na seleção de experimento e nos dados. 

    Definições avançadas|Descrição
    ------|------
    Métrica primária| Métrica principal usada para pontuar seu modelo. [Saiba mais sobre métricas de modelo](how-to-configure-auto-train.md#explore-model-metrics).
    Critérios de saída| Quando qualquer um desses critérios é atendido, o trabalho de treinamento termina antes da conclusão completa. <br> *Tempo de trabalho de treinamento (minutos)* : Por quanto tempo permitir que o trabalho de treinamento seja executado.  <br> *Número máximo de iterações*: Número máximo de pipelines (iterações) a serem testados no trabalho de treinamento. O trabalho não será executado mais do que o número especificado de iterações. <br> *Limite de pontuação da métrica*:  Pontuação de métrica mínima para todos os pipelines. Isso garante que, se você tiver uma métrica de destino definida que deseja alcançar, não gaste mais tempo no trabalho de treinamento do que o necessário.
    Pré-processamento| Selecione para habilitar ou desabilitar o pré-processamento feito pelo Machine Learning automatizado. O pré-processamento inclui a limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos. [Saiba mais sobre o pré-processamento](#preprocess).
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre a validação cruzada](how-to-configure-auto-train.md).
    Simultaneidade| Selecione os limites de vários núcleos que você gostaria de usar ao usar a computação de vários núcleos.
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Criação de perfil de dados & estatísticas de resumo

Você pode obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se o conjunto de dados está pronto para ML. Para colunas não numéricas, elas incluem apenas estatísticas básicas, como mín., máx. e contagem de erros. Para colunas numéricas, você também pode revisar seus momentos estatísticos e quantis estimados. Especificamente, nosso perfil de dados inclui:

>[!NOTE]
> Entradas em branco aparecem para recursos com tipos irrelevantes.

Estatísticas|Descrição
------|------
Funcionalidade| Nome da coluna que está sendo resumida.
Perfil| Visualização embutida com base no tipo inferido. Por exemplo, cadeias de caracteres, Boolianos e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você tenha uma compreensão rápida da distribuição dos dados.
Distribuição de tipo| Valor na linha contagem de tipos dentro de uma coluna. Os nulos são de seu próprio tipo, portanto, essa visualização é útil para detectar valores ímpares ou ausentes.
Type|Tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, Boolianos, datas e decimais.
Mín.| Valor mínimo da coluna. Entradas em branco aparecem para recursos cujo tipo não tem uma ordenação inerente (por exemplo, Boolianos).
Máx.| Valor máximo da coluna. 
Count| Número total de entradas ausentes e não ausentes na coluna.
Contagem não ausente| Número de entradas na coluna que não estão ausentes. Cadeias de caracteres e erros vazios são tratados como valores, portanto, eles não contribuirão para a "contagem não encontrada".
Quantis| Valores aproximados em cada Quantil para fornecer uma noção da distribuição dos dados.
média| Média aritmética ou médio da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
Variância| A medida de difundir os dados desta coluna é de seu valor médio. 
Assimetrias| Medida de quão diferentes os dados dessa coluna são de uma distribuição normal.
Kurtosis| A medida de quão cauda os dados desta coluna é comparada a uma distribuição normal.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Opções avançadas de pré-processamento

Ao configurar seus experimentos, você pode habilitar a configuração `Preprocess`avançada. Isso significa que as etapas de pré-processamento e personalização de dados a seguir são executadas automaticamente.

|&nbsp;Etapas de pré-processamento| Descrição |
| ------------- | ------------- |
|Remova cardinalidade elevada ou nenhum recurso de variância|Descarte-os dos conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou com cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Impute valores em falta|Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|Gerar recursos adicionais|Para recursos de DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos de texto: A frequência do termo com base em unigrams, bi-grams e Tri-Character-grams.|
|Transformar e codificar |Recursos numéricos com poucos valores exclusivos são transformados em recursos categóricos.<br/><br/>A codificação One-Hot é executada para uma baixa cardinalidade categórica; para alta cardinalidade, codificação One-Hot-hash.|
|Incorporações de palavras|Texto featurizer que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso de documento.|
|Codificações de destino|Para recursos categóricos, o mapeia cada categoria com o valor de destino médio para problemas de regressão e a probabilidade de classe de cada classe para problemas de classificação. O peso baseado em frequência e a validação cruzada de k-fold são aplicados para reduzir o ajuste do mapeamento e do ruído causados por categorias de dados esparsas.|
|Codificação de destino de texto|Para entrada de texto, um modelo linear empilhado com conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (WoE)|Calcula WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de fora de classe vs in-Class. Esta etapa gera uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceção.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Gera novos recursos e um novo recurso numérico por cluster, contendo a distância de cada amostra para o centróide de cada cluster.|

## <a name="run-experiment-and-view-results"></a>Executar experimento e exibir resultados

Selecione **Iniciar** para executar seu experimento. O processo de preparação do experimento leva alguns minutos.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

Depois que a fase de preparação do experimento for concluída, você verá a tela de detalhes da execução começar a popular. Esta tela fornece uma lista completa dos modelos criados. Por padrão, o modelo que classifica o mais alto com base na métrica escolhida está no topo da lista. Como o trabalho de treinamento tenta mais modelos, eles são adicionados à lista de iterações e ao gráfico. Use o gráfico de iteração para obter uma comparação rápida das métricas para os modelos produzidos até agora.

Os trabalhos de treinamento podem levar algum tempo para que cada pipeline termine a execução.

[![Painel de detalhes de execução](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Exibir detalhes da execução de treinamento

Faça uma busca detalhada em qualquer um dos modelos de saída para ver detalhes de execução de treinamento, como métricas de desempenho e gráficos de distribuição. [Saiba mais sobre gráficos](how-to-understand-automated-ml.md).

[![Detalhes da iteração](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar seu modelo

Quando você tem o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda você a implantar o modelo sem escrever código:

1. Você tem algumas opções de implantação. 

    + Opção 1: Para implantar o melhor modelo (de acordo com os critérios de métrica que você definiu), selecione implantar melhor modelo na página executar detalhes.

    + Opção 2: Para implantar uma iteração de modelo específica deste experimento, faça uma busca detalhada no modelo para abrir sua página de detalhes de execução e selecione implantar modelo.

1. Preencha o painel **implantar modelo** .

    Campo| Value
    ----|----
    Nome da implementação| Insira um nome exclusivo para sua implantação.
    Descrição da implantação| Insira uma descrição para identificar melhor a finalidade dessa implantação.
    Script de Pontuação| Gerar automaticamente ou carregar seu próprio arquivo de pontuação. [Saiba mais sobre o script de Pontuação](how-to-deploy-and-where.md#script)
    Script de ambiente| Gerar automaticamente ou carregar seu próprio arquivo de ambiente.
    >[!Important]
    > Os nomes de arquivo devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Não são permitidos espaços.

1. Selecione **Implementar**. A implantação pode levar cerca de 20 minutos para ser concluída.

    A mensagem a seguir é exibida quando a implantação é concluída com êxito.

    ![Implantação concluída](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

Agora você tem um serviço Web operacional para gerar previsões!

## <a name="next-steps"></a>Passos Seguintes

* Experimente o tutorial de ponta a ponta [para criar seu primeiro experimento de ml automatizado com o Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Saiba mais sobre o aprendizado de máquina](concept-automated-ml.md) e a Azure Machine Learning automatizados.
* [Entenda os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).
* [Saiba como consumir um serviço Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
