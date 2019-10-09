---
title: Criar seu primeiro experimento de classificação de ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar um modelo de classificação com o Machine Learning automatizado na página de aterrissagem do espaço de trabalho Azure Machine Learning (versão prévia).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: dcd6f2ea6f5c79664af0c2431da07549e71c26bc
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035674"
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

Você conclui as seguintes etapas de configuração e execução de experimento na página de aterrissagem do espaço de trabalho, uma interface consolidada que inclui ferramentas de aprendizado de máquina para executar cenários de ciência de dados para profissionais de ciência de dados de todos os níveis de habilidade. Não há suporte para a página de aterrissagem do espaço de trabalho em navegadores do Internet Explorer.

1. Entre na página de [aterrissagem do espaço de trabalho](https://ml.azure.com/workspaceportal/).

1. Selecione sua assinatura e o espaço de trabalho que você criou.

1. Selecione **introdução**.

1. No painel esquerdo, selecione **ml automatizado** na seção **autor** .

   Como esse é seu primeiro experimento de ML automatizado, você verá a tela introdução.

   ![Azure Machine Learning studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selecione **criar experimento**. 

1. Insira este nome de experimento: `my-1st-automl-experiment`

1. Selecione **criar uma nova computação** e configure seu destino de computação. Um destino de computação é um ambiente de recursos local ou baseado em nuvem usado para executar o script de treinamento ou hospedar sua implantação de serviço. Para este experimento, usamos uma computação baseada em nuvem. 

   Campo | Descrição | Valor do tutorial
   ----|---|---
   Nome da computação |Um nome exclusivo que identifica o contexto de computação.|automl-computação
   Virtual @ no__t-0machine @ no__t-1size| Selecione o tamanho da máquina virtual para sua computação.|Standard_DS12_V2
   Nós mín./máx. (em configurações avançadas)| Para criar o perfil de dados, você deve especificar um ou mais nós.|Nós mínimos: 1<br>Máximo de nós: 6

   >[!NOTE]
   >Para este tutorial, você usará a conta de armazenamento padrão e o contêiner criado com sua nova computação. Eles são preenchidos automaticamente no formulário.
    
1. Selecione **criar** para obter o destino de computação. 

   **Isso leva alguns minutos para ser concluído.** 

1. Após a criação, selecione o novo destino de computação na lista suspensa e selecione **Avançar**.

1. Selecione **carregar do arquivo local** para começar a criar um novo conjunto de um. 

    1. Selecione **Procurar**.
    
    1. Escolha o arquivo **bankmarketing_train. csv** no computador local. Esse é o arquivo que você baixou como um [pré-requisito](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Avançar** na parte inferior esquerda para carregá-lo no contêiner padrão que foi configurado automaticamente durante a criação do espaço de trabalho. A visualização pública dá suporte apenas a carregamentos de arquivos locais. 
    
       Quando o upload for concluído, as configurações e o formulário de visualização serão preenchidos previamente com base no tipo de arquivo. 
       
    1. Verifique se as **configurações e** o formulário de visualização estão preenchidos da seguinte maneira e selecione **Avançar**.
        
        Campo|Descrição| Valor do tutorial
        ---|---|---
        Formato de arquivo|Define o layout e o tipo de dados armazenados em um arquivo.| Delimitados
        Delimitador|Um ou mais caracteres para especificar o limite entre @ no__t-0 regiões separadas e independentes em texto sem formatação ou outros fluxos de dados. |Pontos
        Codificação|Identifica o bit para a tabela de esquema de caractere a ser usada para ler seu conjunto de seus.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de uma, se houver, serão tratados.| Todos os ficheiros têm os mesmos cabeçalhos
        Ignorar linhas | Indica quantas linhas, se houver, são ignoradas no conjunto de registros.| Nenhum
    
        ![Configuração da guia de visualização](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Selecione **classificação** como a tarefa de previsão.

1. Selecione **y** como a coluna de destino, o que você deseja prever. Esta coluna indica se o cliente assinou um termo de depósito ou não.

1. Expanda **Configurações avançadas** e preencha os campos da seguinte maneira. Essas configurações são para controlar melhor o trabalho de treinamento. Caso contrário, os padrões serão aplicados com base na seleção de experimento e nos dados.

   >[!NOTE]
   > Neste tutorial, você não definirá uma pontuação de métrica ou um limite máximo de núcleos por iterações. Nem você bloqueará o teste de algoritmos.
   
   Avançado @ no__t-0settings|Descrição|Valor @ no__t-0for @ no__t-1tutorial
   ------|---------|---
   Métrica primária| Métrica de avaliação para a qual o algoritmo de aprendizado de máquina será medido.|AUC_weighted
   Critérios de saída| Se um critério for atendido, o trabalho de treinamento será interrompido. |Treinamento @ no__t-0job @ no__t-1time: 5 <br> <br> Max @ no__t-0 @ no__t-1 @ no__t-2of @ no__t-3iterations&#58;10
   Pré-processamento| Habilita o pré-processamento feito pelo aprendizado de máquina automatizado. Isso inclui a limpeza automática de dados, a preparação e a transformação para gerar recursos sintéticos.| Ativar
   Tipo de validação | Escolha um tipo de validação cruzada.|Validação cruzada de K-fold
   Número de validações | Número de testes. | 2 validações cruzadas 
   Simultaneidade| O número máximo de iterações simultâneas.|5
   
1. Selecione **Iniciar** para executar o experimento. Uma tela é exibida com uma mensagem de status à medida que a preparação do experimento é iniciada.

>[!IMPORTANT]
> A preparação leva de **10-15 minutos** para preparar a execução do experimento. Uma vez em execução, leva **2-3 minutos mais para cada iteração**.  
>
> Em produção, você provavelmente se desapareceria por um pouco. Mas para este tutorial, sugerimos que você comece a explorar os resultados da iteração conforme eles são concluídos enquanto os outros ainda estão em execução. 

##  <a name="explore-iteration-results"></a>Explorar resultados de iteração

À medida que o experimento progride, a tela atualiza o **gráfico de iteração** e a **lista de iteração** com as diferentes iterações (modelos) criadas conforme elas são concluídas. Por padrão, as iterações são ordenadas por Pontuação de métrica. Para este tutorial, o modelo que classifica o mais alto com base na métrica de **AUC_weighted** escolhida está no topo da lista.

Enquanto você aguarda até que todas as iterações de experimento sejam concluídas, selecione o **nome** de uma iteração concluída para explorar seus detalhes de desempenho. 

O exemplo a seguir mostra os gráficos e as métricas de execução geradas para cada iteração, como uma curva de recall de precisão, matriz de confusão, pontuações de precisão ponderada, etc. 

![Detalhes da execução de iteração](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Implementar o modelo

O Machine Learning automatizado na página de aterrissagem do espaço de trabalho permite que você implante o melhor modelo como um serviço Web em algumas etapas. A implantação é a integração do modelo para que ele possa prever novos dados e identificar possíveis áreas de oportunidade. Para este experimento, a implantação em um serviço Web significa que a instituição financeira agora tem uma solução Web iterativa e escalonável para identificar clientes de depósito de prazo fixo. 

Quando a execução for concluída, navegue de volta para o **gráfico de iteração** e a página de detalhes da **lista de iterações** . 

Nesse contexto de experimento, **VotingEnsemble** é considerado o melhor modelo, com base na métrica **AUC_weighted** .  Implantamos esse modelo, mas é recomendável que a implantação demore cerca de 20 minutos para ser concluída. O processo de implantação envolve várias etapas, incluindo o registro do modelo, a geração de recursos e a configuração deles para o serviço Web.

1. Selecione o botão **implantar melhor modelo** no canto superior direito.

1. Preencha o painel **implantar melhor modelo** da seguinte maneira:

    Campo| Value
    ----|----
    Nome da implantação| meu-automl-implantar
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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de aprendizado de máquina automatizado, você usou a página de aterrissagem do espaço de trabalho para criar e implantar um modelo de classificação. Consulte estes artigos para obter mais informações e as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](how-to-consume-web-service.md)

+ Saiba mais sobre o [pré-processamento](how-to-create-portal-experiments.md#preprocess).
+ Saiba mais sobre a [criação de perfil de dados](how-to-create-portal-experiments.md#profile).
+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).
+ Para obter mais informações sobre métricas de classificação e gráficos, consulte o artigo [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md#classification) .

>[!NOTE]
> Esse conjunto @no__t de 0Creative de marketing bancário é disponibilizado sob o (CCO: Domínio público) licença @ no__t-0. Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [licença de conteúdo do banco de dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados estava originalmente disponível no [banco de dados de Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. moro, P. Cortez e P. Rita. Uma abordagem controlada por dados para prever o sucesso do telemarketing bancário. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho 2014.
