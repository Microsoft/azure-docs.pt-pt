---
title: Crie seu primeiro experimento de Machine Learning automatizado
titleSuffix: Azure Machine Learning service
description: Saiba como treinar e implantar um modelo de classificação com o aprendizado de máquina automatizado no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: e53cd92a9dfd8f823918fb38e14c2b73c2ce071f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534414"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Tutorial: Crie seu primeiro modelo de classificação com o Machine Learning automatizado

Neste tutorial, você aprenderá a criar seu primeiro experimento de aprendizado de máquina automatizado no portal do Azure (versão prévia) sem escrever uma única linha de código. Este exemplo cria um modelo de classificação para prever se um cliente assinará um depósito de termo fixo com uma instituição financeira.

Usando os recursos de aprendizado de máquina automatizados do serviço de Azure Machine Learning e o portal do Azure, você começa o processo de aprendizado de máquina automatizado. A seleção de algoritmos e o ajuste de hiperparâmetro são feitos para você. A técnica de Machine Learning automatizada itera em várias combinações de algoritmos e hiperparâmetros até encontrar o melhor modelo com base no seu critério.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Configurar um espaço de trabalho de serviço do Azure Machine Learning.
> * Crie um experimento.
> * Treine automaticamente um modelo de classificação.
> * Exibir detalhes da execução de treinamento.
> * Implemente o modelo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

* Baixe o arquivo de dados [ **bankmarketing_train. csv** ](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . A coluna **y** indica se um cliente assinou um depósito de termo fixo, que é posteriormente identificado como a coluna de destino para previsões neste tutorial. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Criar uma experimentação

Essas etapas o orientarão durante a configuração do experimento a partir da seleção de dados para escolher a métrica primária e o tipo de modelo. 

1. Vá para o painel esquerdo do espaço de trabalho. Selecione **Machine Learning automatizado** na seção **criação (visualização)** .
Você verá a tela **Bem-vindo ao Machine Learning automatizado** , pois este é seu primeiro experimento com Machine Learning automatizado.

    ![Painel de navegação portal do Azure](media/tutorial-1st-experiment-automated-ml/nav-pane.png)



1. Selecione **criar experimento**. Em seguida, insira **My-1º-automl-experimento** como o nome do experimento.

1. Selecione **criar uma nova computação** e configure seu contexto de computação para este experimento.

    Campo| Value
    ---|---
    Nome de computação| Insira um nome exclusivo que identifique o contexto de computação. Para este exemplo, usamos **automl-Compute**.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação. Usamos **Standard_DS12_V2**.
    Definições adicionais| *Nó mínimo*: 1. Para habilitar a criação de perfil de dados, você deve ter um ou mais nós. <br> *Nó máximo*: 6. 

    Para criar sua nova computação, selecione **criar**. Isso leva alguns minutos. 

    Quando a criação for concluída, selecione a nova computação na lista suspensa e, em seguida, selecione **Avançar**.

    >[!NOTE]
    >Para este tutorial, usamos a conta de armazenamento padrão e o contêiner criado com sua nova computação. Eles são preenchidos automaticamente no formulário.

1. Selecione **carregar** e escolha o arquivo **bankmarketing_train. csv** do computador local para carregá-lo no contêiner padrão. A visualização pública dá suporte apenas a carregamentos de arquivos locais e contas de armazenamento de BLOBs do Azure. Quando o upload for concluído, selecione o arquivo na lista. 

    [![Selecionar arquivo de dados](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. A guia **Visualização** nos permite configurar ainda mais nossos dados para esse experimento.

    Na guia **Visualização** , indique que os dados incluem cabeçalhos. O padrão do serviço inclui todos os recursos (colunas) para treinamento. Para este exemplo, role para a direita e **ignore** o recurso **day_of_week** .

    ![Configuração da guia de visualização](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > A criação de perfil de dados não está disponível com computações que têm um mínimo de nós zero.

1. Selecione **classificação** como a tarefa de previsão.

1. Selecione **y** como a coluna de destino, onde queremos fazer previsões. Esta coluna indica se o cliente assinou um termo de depósito ou não.

1. Expanda **Configurações avançadas** e preencha os campos da seguinte maneira.

    Definições avançadas|Value
    ------|------
    Métrica primária| AUC_weighted 
    Critérios de saída| Quando qualquer um desses critérios é atendido, o trabalho de treinamento termina antes da conclusão completa: <br> *Tempo de trabalho de treinamento (minutos)* : 5  <br> *Número máximo de iterações*: 10 
    Pré-processamento| Habilita o pré-processamento feito pelo aprendizado de máquina automatizado. Isso inclui a limpeza automática de dados, a preparação e a transformação para gerar recursos sintéticos.
    Validação| Selecione a validação cruzada de K-e **2** para o número de validações cruzadas. 
    Simultaneidade| Selecione **5** para o número máximo de iterações simultâneas.

   >[!NOTE]
   > Para este experimento, não definimos uma métrica ou um limite máximo de núcleos por iterações. Também não bloqueamos que os algoritmos sejam testados.

1. Selecione **Iniciar** para executar o experimento.

   Quando o experimento for iniciado, você verá uma tela de **detalhes de execução** em branco com o status a seguir na parte superior. 

      ![Executar preparação](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
O processo de preparação do experimento leva alguns minutos. Quando o processo é concluído, a mensagem de status muda para **execução está em execução**.

##  <a name="view-experiment-details"></a>Exibir detalhes do experimento

À medida que o experimento progride, a tela de **detalhes da execução** atualiza o gráfico de iteração e a lista com as diferentes iterações (modelos) que são executadas. A lista de iterações está em ordem por pontuação da métrica. Por padrão, o modelo que pontua o mais alto com base em nossa métrica **AUC_weighted** está na parte superior da lista.

>[!TIP]
> Os trabalhos de treinamento levam vários minutos para que cada pipeline termine a execução.

[![Painel de detalhes de execução](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Implementar o modelo

Ao usar o Machine Learning automatizado no portal do Azure, podemos implantar o melhor modelo como um serviço Web para prever novos dados e identificar possíveis áreas de oportunidade. Para este experimento, a implantação significa que a instituição financeira agora tem uma solução iterativa e escalonável para identificar clientes potenciais de depósito fixo.

Nesse contexto de experimento, **VotingEnsemble** é considerado o melhor modelo, com base na métrica **AUC_weighted** .  Implantamos esse modelo, mas é recomendável que a implantação demore cerca de 20 minutos para ser concluída.

1. Na página **executar detalhe** , selecione o botão **implantar melhor modelo** .

1. Preencha o painel **implantar melhor modelo** da seguinte maneira:

    Campo| Value
    ----|----
    Nome da implementação| meu-automl-implantar
    Descrição da implantação| Minha primeira implantação de experimento automatizada do Machine Learning
    Script de Pontuação| AutoGenerate
    Script de ambiente| AutoGenerate
    
1. Selecione **Implementar**.

    A seguinte mensagem aparece quando a implantação é concluída com êxito:

    ![Implantação concluída](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    Agora você tem um serviço Web operacional para gerar previsões.

## <a name="clean-up-resources"></a>Limpar recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos em sua conta ou se você quiser manter o espaço de trabalho e os arquivos de teste. Caso contrário, exclua o grupo de recursos inteiro, se você não planeja usar nenhum dos arquivos.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do portal do Azure, se você quiser manter o grupo de recursos e o espaço de trabalho para outros tutoriais e explorações. 

1. Acesse o painel **ativos** à esquerda e selecione implantações. 

1. Selecione a implantação que você deseja excluir e selecione **excluir**. 

1. Selecione **continuar**.

### <a name="delete-the-resource-group"></a>Excluir o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial de aprendizado de máquina automatizado, você usou o portal do Azure para criar e implantar um modelo de classificação. Consulte estes artigos para obter mais informações e as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](how-to-consume-web-service.md)


+ Saiba mais sobre o [pré-processamento](how-to-create-portal-experiments.md#preprocess).
+ Saiba mais sobre a [criação de perfil de dados](how-to-create-portal-experiments.md#profile).
+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).

>[!NOTE]
> Esse conjunto de Cco de [marketing bancário é disponibilizado no Creative Commons Domínio público)](https://creativecommons.org/publicdomain/zero/1.0/). Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [licença de conteúdo do banco de dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados estava originalmente disponível no [banco de dados de Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> Cite o seguinte trabalho: <br> [Moro et al., 2014] S. moro, P. Cortez e P. Rita. Uma abordagem controlada por dados para prever o sucesso do telemarketing bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho 2014.
