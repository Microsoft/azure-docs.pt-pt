---
title: Criar & implantar modelos de ML automatizados
titleSuffix: Azure Machine Learning
description: Crie, gerencie e implante experimentos automatizados de aprendizado de máquina no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: c05b29dd5909d1371c71bffb9db555c15c5d23ed
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75764648"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Crie, explore e implante experimentos automatizados de aprendizado de máquina com o Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

 Neste artigo, você aprenderá a criar, explorar e implantar experimentos automatizados de aprendizado de máquina no Azure Machine Learning Studio sem uma única linha de código. O aprendizado de máquina automatizado automatiza o processo de seleção do melhor algoritmo a ser usado para seus dados específicos, para que você possa gerar um modelo de aprendizado de máquina rapidamente. [Saiba mais sobre o aprendizado de máquina automatizado](concept-automated-ml.md).

 Se você preferir uma experiência mais baseada em código, também poderá [configurar seus experimentos de aprendizado de máquina automatizados no Python](how-to-configure-auto-train.md) com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* Um espaço de trabalho Azure Machine Learning com um tipo de **edição Enterprise**. Consulte [criar um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).  Para atualizar um espaço de trabalho existente para o Enterprise Edition, consulte [atualizar para o Enterprise Edition](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Começar

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com). 

1. Selecione sua assinatura e seu espaço de trabalho. 

1. Navegue até o painel esquerdo. Selecione **ml automatizado** na seção **autor** .

[painel de navegação do ![Azure Machine Learning Studio](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Se esta for a primeira vez que você faz qualquer experimento, você verá uma lista vazia e links para a documentação. 

Caso contrário, você verá uma lista de experiências de aprendizado de máquina automatizadas recentes, incluindo aquelas criadas com o SDK. 

## <a name="create-and-run-experiment"></a>Criar e executar experimento

1. Selecione **+ criar experimento** e preencha o formulário.

1. Selecione um conjunto de uma do seu contêiner de armazenamento ou crie um novo conjunto de um. Os conjuntos de valores podem ser criados a partir de arquivos locais, URLs da Web, repositórios de armazenamento ou conjuntos de os Azure Open DataSets. 

    >[!Important]
    > Requisitos para dados de treinamento:
    >* Os dados devem estar no formato de tabela.
    >* O valor que você deseja prever (coluna de destino) deve estar presente nos dados.

    1. Para criar um novo conjunto de um de um arquivo em seu computador local, selecione **procurar** e, em seguida, selecione o arquivo. 

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Avançar**para carregá-lo no contêiner de armazenamento padrão criado automaticamente com seu espaço de trabalho ou escolha um contêiner de armazenamento que você deseja usar para o experimento. 

    1. Examine as **configurações e** o formulário de visualização para obter precisão. O formulário é populado de forma inteligente com base no tipo de arquivo. 

        Campo| Descrição
        ----|----
        Formato de arquivo| Define o layout e o tipo de dados armazenados em um arquivo.
        Delimitador| Um ou mais caracteres para especificar o limite entre regiões separadas e independentes em texto sem formatação ou outros fluxos de dados.
        Encoding| Identifica o bit para a tabela de esquema de caractere a ser usada para ler seu conjunto de seus.
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de uma, se houver, serão tratados.
        Ignorar linhas | Indica quantas linhas, se houver, são ignoradas no conjunto de registros.
    
        Selecione **Seguinte**.

    1. O formulário de **esquema** é preenchido de forma inteligente com base nas seleções no formulário **configurações e visualização** . Aqui configure o tipo de dados para cada coluna, examine os nomes de coluna e selecione quais colunas **não incluir** para o experimento. 
            
        Selecione **Avançar.**

    1. O formulário **confirmar detalhes** é um resumo das informações previamente populadas nas **informações básicas** e **nas configurações e na visualização** de formulários. Você também tem a opção de criar o perfil de seu conjunto de um usando uma computação de criação de perfil habilitada. Saiba mais sobre a [criação de perfil de dados](#profile).

        Selecione **Seguinte**.
1. Selecione o conjunto de seus conjuntos de seu recém-criado quando ele for exibido. Você também pode exibir uma visualização do conjunto de exemplos e estatísticas de exemplo. 

1. No formulário **configurar execução** , insira um nome de teste exclusivo.

1. Selecione uma coluna de destino; Essa é a coluna na qual você gostaria de fazer previsões.

1. Selecione uma computação para o trabalho de criação de perfil de dados e treinamento. Uma lista de suas computações existentes está disponível na lista suspensa. Para criar uma nova computação, siga as instruções na etapa 7.

1. Selecione **criar uma nova computação** para configurar o contexto de computação para este experimento.

    Campo|Descrição
    ---|---
    Nome da computação| Insira um nome exclusivo que identifique o contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação.
    Nós mín./máx. (em configurações avançadas)| Para criar o perfil de dados, você deve especificar um ou mais nós. Insira o número máximo de nós para sua computação. O padrão é 6 nós para uma computação AML.
    
    Selecione **Criar**. A criação de uma nova computação pode levar alguns minutos.

    >[!NOTE]
    > Seu nome de computação indicará se a computação que você selecionou/criar está com a *criação de perfil habilitada*. (Consulte a seção [criação de perfil de dados](#profile) para obter mais detalhes).

    Selecione **Seguinte**.

1. No formulário **tipo de tarefa e configurações** , selecione o tipo de tarefa: classificação, regressão ou previsão. Consulte [como definir tipos de tarefa](how-to-define-task-type.md) para obter mais informações.

    1. Para classificação, você também pode habilitar o aprendizado profundo, que é usado para featurizations de texto.

    1. Para previsão:
        1. Selecionar coluna de tempo: esta coluna contém os dados de hora a serem usados.

        1. Selecione previsão Horizonte: indica quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for necessário para prever no futuro, menor será a sua precisão. [Saiba mais sobre previsão e previsão horizonte](how-to-auto-train-forecast.md).

1. Adicional Configurações de adição: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento. Caso contrário, os padrões serão aplicados com base na seleção de experimento e nos dados. 

    Configurações adicionais|Descrição
    ------|------
    Métrica primária| Métrica principal usada para pontuar seu modelo. [Saiba mais sobre métricas de modelo](how-to-configure-auto-train.md#explore-model-metrics).
    Personalização automática| Selecione para habilitar ou desabilitar o pré-processamento feito pelo Machine Learning automatizado. O pré-processamento inclui a limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos. [Saiba mais sobre o pré-processamento](#preprocess).
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.
    Critério de saída| Quando qualquer um desses critérios for atendido, o trabalho de treinamento será interrompido. <br> *Tempo de trabalho de treinamento (horas)* : por quanto tempo permitir que o trabalho de treinamento seja executado. <br> *Limite de Pontuação de métrica*: Pontuação de métrica mínima para todos os pipelines. Isso garante que, se você tiver uma métrica de destino definida que deseja alcançar, não gaste mais tempo no trabalho de treinamento do que o necessário.
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre a validação cruzada](how-to-configure-auto-train.md).
    Simultaneidade| *Máximo de iterações simultâneas*: número máximo de pipelines (iterações) a serem testados no trabalho de treinamento. O trabalho não será executado mais do que o número especificado de iterações. <br> *Máximo de núcleos por iteração*: selecione os limites de vários núcleos que você gostaria de usar ao usar a computação de vários núcleos.

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
Tipo|Tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, Boolianos, datas e decimais.
Mín.| Valor mínimo da coluna. Entradas em branco aparecem para recursos cujo tipo não tem uma ordenação inerente (por exemplo, Boolianos).
Máx| Valor máximo da coluna. 
Contagem| Número total de entradas ausentes e não ausentes na coluna.
Contagem não ausente| Número de entradas na coluna que não estão ausentes. Cadeias de caracteres e erros vazios são tratados como valores, portanto, eles não contribuirão para a "contagem não encontrada".
Quantis| Valores aproximados em cada Quantil para fornecer uma noção da distribuição dos dados.
média| Média aritmética ou médio da coluna.
Desvio padrão| Medida da quantidade de dispersão ou variação dos dados desta coluna.
Variância| A medida de difundir os dados desta coluna é de seu valor médio. 
Assimetrias| Medida de quão diferentes os dados dessa coluna são de uma distribuição normal.
Kurtosis| A medida de quão cauda os dados desta coluna é comparada a uma distribuição normal.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Opções avançadas de pré-processamento

Ao configurar seus experimentos, você pode habilitar a configuração avançada `Preprocess`. Isso significa que as etapas de pré-processamento e personalização de dados a seguir são executadas automaticamente.

|Pré-processando&nbsp;etapas| Descrição |
| ------------- | ------------- |
|Remova cardinalidade elevada ou nenhum recurso de variância|Descarte-os dos conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou com cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Impute valores em falta|Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|Gerar recursos adicionais|Para as funcionalidades de DateTime: ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos de texto: a frequência de termos com base em unigrams, bi-grams e Tri-Character-grams.|
|Transformar e codificar |Recursos numéricos com poucos valores exclusivos são transformados em recursos categóricos.<br/><br/>A codificação One-Hot é executada para uma baixa cardinalidade categórica; para alta cardinalidade, codificação One-Hot-hash.|
|Incorporações de palavras|Texto featurizer que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso de documento.|
|Codificações de destino|Para recursos categóricos, o mapeia cada categoria com o valor de destino médio para problemas de regressão e a probabilidade de classe de cada classe para problemas de classificação. O peso baseado em frequência e a validação cruzada de k-fold são aplicados para reduzir o ajuste do mapeamento e do ruído causados por categorias de dados esparsas.|
|Codificação de destino de texto|Para entrada de texto, um modelo linear empilhado com conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (WoE)|Calcula WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de fora de classe vs in-Class. Esta etapa gera uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceção.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Gera novos recursos e um novo recurso numérico por cluster, contendo a distância de cada amostra para o centróide de cada cluster.|

## <a name="run-experiment-and-view-results"></a>Executar experimento e exibir resultados

Selecione **Iniciar** para executar seu experimento. O processo de preparação do experimento pode levar até 10 minutos. Os trabalhos de treinamento podem levar mais 2-3 minutos para que cada pipeline termine a execução.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

>[!NOTE]
> Selecione **Atualizar** periodicamente para exibir o status da execução. 

A tela de **detalhes da execução** é aberta na guia **detalhes** . Esta tela mostra um resumo da execução do experimento, incluindo o **status de execução**. 

A guia **modelos** contém uma lista dos modelos criados ordenados pela pontuação da métrica. Por padrão, o modelo que classifica o mais alto com base na métrica escolhida está no topo da lista. Como o trabalho de treinamento tenta mais modelos, eles são adicionados à lista. Use isso para obter uma comparação rápida das métricas para os modelos produzidos até agora.

[![painel de detalhes de execução](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Exibir detalhes da execução de treinamento

Faça uma busca detalhada em qualquer um dos modelos concluídos para ver detalhes de execução de treinamento, como executar métricas na guia **detalhes do modelo** ou gráficos de desempenho na guia **visualizações** . [saiba mais sobre gráficos](how-to-understand-automated-ml.md).

[detalhes da iteração de ![](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Implantar seu modelo

Quando você tem o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda você a implantar o modelo sem escrever código:

1. Você tem algumas opções de implantação. 

    + Opção 1: para implantar o melhor modelo (de acordo com os critérios de métrica que você definiu), selecione implantar melhor modelo na guia detalhes.

    + Opção 2: para implantar uma iteração de modelo específica deste experimento, faça uma busca detalhada no modelo para abrir a guia detalhes do modelo e selecione implantar modelo.

1. Preencha o painel **implantar modelo** .

    Campo| Valor
    ----|----
    Nome| Insira um nome exclusivo para sua implantação.
    Descrição| Insira uma descrição para identificar melhor a finalidade dessa implantação.
    Tipo de computação| Selecione o tipo de ponto de extremidade que você deseja implantar: *AKs (serviço kubernetes do Azure)* ou *instância de contêiner do Azure (ACI)* .
    Nome| *Aplica-se somente a AKs:* Selecione o nome do cluster AKS no qual você deseja implantar.
    Ativar autenticação | Selecione para permitir a autenticação baseada em token ou baseada em chave.
    Usar ativos de implantação personalizados| Habilite esse recurso se você quiser carregar seu próprio script de Pontuação e arquivo de ambiente. [Saiba mais sobre scripts de Pontuação](how-to-deploy-and-where.md#script).

    >[!Important]
    > Os nomes de arquivo devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Não são permitidos espaços.

    O menu *avançado* oferece recursos de implantação padrão, como a [coleta de dados](how-to-enable-app-insights.md) e as configurações de utilização de recursos. Se você quiser substituir esses padrões, faça isso neste menu.

1. Selecione **Implementar**. A implantação pode levar cerca de 20 minutos para ser concluída.

Agora você tem um serviço Web operacional para gerar previsões! Você pode testar as previsões consultando o serviço do [suporte Azure Machine Learning interno Power bi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Passos seguintes

* Experimente o tutorial de ponta a ponta [para criar seu primeiro experimento de ml automatizado com o Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [Saiba mais sobre o aprendizado de máquina](concept-automated-ml.md) e a Azure Machine Learning automatizados.
* [Entenda os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).
* [Saiba como consumir um serviço Web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
