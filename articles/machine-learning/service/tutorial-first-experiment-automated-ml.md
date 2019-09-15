---
title: Crie seu primeiro experimento de Machine Learning automatizado
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar um modelo de classificação com o Machine Learning automatizado na página de aterrissagem do espaço de trabalho Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: f8b9876680899a5d8e87f8a49a0b4ed6c113fb45
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001858"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Tutorial: Crie seu primeiro modelo de classificação com o Machine Learning automatizado

Neste tutorial, você aprenderá a criar seu primeiro experimento de aprendizado de máquina automatizado por meio da página de aterrissagem do espaço de trabalho (versão prévia) sem escrever uma única linha de código. Este exemplo cria um modelo de classificação para prever se um cliente assinará um depósito de termo fixo com uma instituição financeira.

Com o Machine Learning automatizado, você pode automatizar tarefas demoradas. O aprendizado de máquina automatizado itera rapidamente em várias combinações de algoritmos e hiperparâmetros para ajudá-lo a encontrar o melhor modelo com base em uma métrica de sucesso de sua escolha.

Neste tutorial, você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Crie uma área de trabalho do Azure Machine Learning.
> * Execute um experimento de aprendizado de máquina automatizado.
> * Exibir detalhes do experimento.
> * Implemente o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Baixe o arquivo de dados [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . A coluna **y** indica se um cliente assinou um depósito de termo fixo, que é posteriormente identificado como a coluna de destino para previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um espaço de trabalho por meio do portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu espaço de **trabalho** e sua **assinatura**. Você precisará delas para garantir que crie seu experimento no lugar certo. 

## <a name="create-and-run-the-experiment"></a>Criar e executar o experimento

Você conclui as seguintes etapas de configuração e execução de experimento na página de aterrissagem do espaço de trabalho, uma interface consolidada que inclui ferramentas de aprendizado de máquina para executar cenários de ciência de dados para profissionais de ciência de dados de todos os níveis de habilidade.

1. Entre na página de [aterrissagem do espaço de trabalho](https://ml.azure.com/workspaceportal/).

1. Selecione sua assinatura e o espaço de trabalho que você criou.

1. Selecione **introdução**.

1.  Selecione **ml automatizado** na seção **criação** , no painel do lado esquerdo.
Você verá a tela de **introdução** , já que este é seu primeiro experimento com o Machine Learning automatizado.

    ![Azure Machine Learning studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selecione **criar experimento**. 

1. Insira **My-1º-automl-experimento** como o nome do experimento.

1. Selecione **criar uma nova computação**. 

    1. Configure seu contexto de computação para este experimento.
        
        Campo | Value
        ----|---
        Nome de computação |  Insira um nome exclusivo que identifique o contexto de computação. Para este exemplo, use **automl-Compute**.
        Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação. Use o padrão **Standard_DS12_V2**.
        Definições adicionais| *Nó mínimo*: 1. Para habilitar a criação de perfil de dados, você deve ter um ou mais nós. <br> *Nó máximo*: 6.
 
    1. Para criar sua nova computação, selecione **criar**. Isso leva alguns minutos para ser concluído. 

    1. Quando a criação for concluída, selecione a nova computação na lista suspensa e, em seguida, selecione **Avançar**.

    >[!NOTE]
    >Para este tutorial, você usará a conta de armazenamento padrão e o contêiner criado com sua nova computação. Eles são preenchidos automaticamente no formulário.

1. Selecione **carregar do arquivo local**. A partir daqui, você cria um novo conjunto de uma com o arquivo **bankmarketing_train. csv** que você baixou anteriormente para este tutorial. 

    1. Selecione **procurar** e, em seguida, selecione o arquivo **bankmarketing_train. csv** no computador local. 

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Avançar** para carregá-lo no contêiner padrão que foi configurado automaticamente durante a criação do seu espaço de trabalho. A visualização pública dá suporte apenas a carregamentos de arquivos locais. 

    1. Quando o upload for concluído, as **configurações e** o formulário de visualização serão preenchidos de forma inteligente com base no tipo de arquivo. Verifique se o formulário está preenchido da seguinte maneira.
        
        Campo|Value
        ---|---
        Formato de arquivo| Delimitados
        Delimitador| Vírgula
        Codificação| UTF-8
        Cabeçalhos de coluna| Todos os arquivos têm os mesmos cabeçalhos
        Ignorar linhas | Nenhum

        >[!NOTE]
        > Se qualquer uma das configurações neste formulário for atualizada, a versão prévia atualizará adequadamente.

        Selecione **Seguinte**.
    

    1. O formulário de **esquema** permite a configuração adicional de seus dados para esse experimento. Para este exemplo, selecione a opção Alternar para o recurso **day_of_week** para não incluí-lo para este experimento. Selecione **concluído**, para concluir o carregamento do arquivo e a criação do conjunto de um para o experimento.

        ![Configuração da guia de visualização](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Selecione **classificação** como a tarefa de previsão.

1. Selecione **y** como a coluna de destino, o que você deseja prever. Esta coluna indica se o cliente assinou um termo de depósito ou não.

1. Expanda **Configurações avançadas** e preencha os campos da seguinte maneira.

    Definições avançadas|Value
    ------|------
    Métrica primária| AUC_weighted 
    Critérios de saída| Quando qualquer um desses critérios é atendido, o trabalho de treinamento termina antes da conclusão completa: <br> *Tempo de trabalho de treinamento (minutos)* : 5  <br> *Número máximo de iterações*: 10 
    Pré-processamento| Habilita o pré-processamento feito pelo aprendizado de máquina automatizado. Isso inclui a limpeza automática de dados, a preparação e a transformação para gerar recursos sintéticos.
    Validação| Selecione a validação cruzada de K-e **2** para o número de validações cruzadas. 
    Simultaneidade| Selecione **5** para o número máximo de iterações simultâneas.

   >[!NOTE]
   > Para este experimento, você não define uma métrica ou um limite máximo de núcleos por iterações. Você também não impede que os algoritmos sejam testados.

1. Selecione **Iniciar** para executar o experimento.

   Quando o experimento for iniciado, você verá uma tela em branco com uma mensagem de status na parte superior.

O processo de preparação do experimento leva vários minutos. Quando esse processo é concluído, a mensagem de status muda para **execução está em execução**.

##  <a name="view-experiment-details"></a>Exibir detalhes do experimento

À medida que o experimento progride, a tela atualiza o **gráfico de iteração** e a **lista de iteração** com as diferentes iterações (modelos) que são executadas. A lista de iterações está em ordem por pontuação da métrica. Por padrão, o modelo que pontua o mais alto com base em nossa métrica **AUC_weighted** está na parte superior da lista.

>[!WARNING]
> Os trabalhos de treinamento levam vários minutos para que cada pipeline termine a execução.

[![Painel de detalhes de execução](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Implementar o modelo

Usando o Machine Learning automatizado na página de aterrissagem do espaço de trabalho, você pode implantar o melhor modelo como um serviço Web para prever novos dados e identificar possíveis áreas de oportunidade. Para este experimento, a implantação significa que a instituição financeira agora tem uma solução iterativa e escalonável para identificar clientes potenciais de depósito fixo.

Nesse contexto de experimento, **VotingEnsemble** é considerado o melhor modelo, com base na métrica **AUC_weighted** .  Implantamos esse modelo, mas é recomendável que a implantação demore cerca de 20 minutos para ser concluída.

1. Na página **executar detalhe** , selecione o botão **implantar melhor modelo** no canto superior direito.

1. Preencha o painel **implantar melhor modelo** da seguinte maneira:

    Campo| Value
    ----|----
    Nome da implementação| meu-automl-implantar
    Descrição da implantação| Minha primeira implantação de experimento automatizada do Machine Learning
    Script de Pontuação| AutoGenerate
    Script de ambiente| AutoGenerate
    
1. Selecione **Implementar**.

    Uma mensagem de implantação concluída é exibida quando A implantação é concluída com êxito.
    
Agora você tem um serviço Web operacional para gerar previsões.

## <a name="clean-up-resources"></a>Limpar recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos em sua conta ou se você quiser manter o espaço de trabalho e os arquivos de teste. Caso contrário, exclua o grupo de recursos inteiro, se você não planeja usar nenhum dos arquivos.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do portal do Azure, se você quiser manter o grupo de recursos e o espaço de trabalho para outros tutoriais e explorações. 

1. Aceda ao [Portal do Azure](https://portal.azure.com//). Navegue até seu espaço de trabalho e, à esquerda, no painel **ativos** , selecione **implantações**. 

1. Selecione a implantação que você deseja excluir e selecione **excluir**. 

1. Selecione **continuar**.

### <a name="delete-the-resource-group"></a>Excluir o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial de aprendizado de máquina automatizado, você usou a página de aterrissagem do espaço de trabalho para criar e implantar um modelo de classificação. Consulte estes artigos para obter mais informações e as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](how-to-consume-web-service.md)

+ Saiba mais sobre o [pré-processamento](how-to-create-portal-experiments.md#preprocess).
+ Saiba mais sobre a [criação de perfil de dados](how-to-create-portal-experiments.md#profile).
+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).

>[!NOTE]
> Esse conjunto de Cco de [marketing bancário é disponibilizado no Creative Commons Domínio público)](https://creativecommons.org/publicdomain/zero/1.0/). Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [licença de conteúdo do banco de dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados estava originalmente disponível no [banco de dados de Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. moro, P. Cortez e P. Rita. Uma abordagem controlada por dados para prever o sucesso do telemarketing bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho 2014.
