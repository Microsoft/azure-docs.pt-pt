---
title: Criar seu primeiro experimento de ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como treinar e implantar um modelo de classificação com o Machine Learning automatizado no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 93cbf8e9e60ef48e1ff3516dd4e9e123f70e0f42
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982426"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Tutorial: criar seu primeiro modelo de classificação com o Machine Learning automatizado
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste tutorial, você aprenderá a criar seu primeiro experimento de aprendizado de máquina automatizado por meio do Azure Machine Learning Studio sem escrever uma única linha de código. Este exemplo cria um modelo de classificação para prever se um cliente assinará um depósito de termo fixo com uma instituição financeira.

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

## <a name="create-a-workspace"></a>Criar áreas de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um espaço de trabalho por meio do Azure Machine Learning Studio, um console baseado na Web para gerenciar seus recursos do Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Anote seu espaço de **trabalho** e sua **assinatura**. Você precisará delas para garantir que crie seu experimento no lugar certo. 

## <a name="create-and-run-the-experiment"></a>Criar e executar o experimento

Você conclui as seguintes etapas de configuração e execução do experimento no Azure Machine Learning Studio, uma interface consolidada que inclui ferramentas de Machine Learning para executar cenários de ciência de dados para profissionais de ciência de dados de todos os níveis de habilidade. Não há suporte para o estúdio em navegadores do Internet Explorer.

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecione sua assinatura e o espaço de trabalho que você criou.

1. Selecione **introdução**.

1. No painel esquerdo, selecione **ml automatizado** na seção **autor** .

   Como este é seu primeiro experimento de ML automatizado, você verá uma lista vazia e links para a documentação.

   ![Azure Machine Learning studio](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecione **nova execução automática de ml**. 

1. Crie um novo conjunto de uma seleção **de arquivos locais** na lista suspensa **+ criar conjunto de DataSet** . 

    1. Selecione **Procurar**.
    
    1. Escolha o arquivo **bankmarketing_train. csv** no computador local. Esse é o arquivo que você baixou como um [pré-requisito](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Selecione **tabular** como seu tipo de conjunto de texto. 

    1. Dê um nome exclusivo ao seu conjunto de dado e forneça uma descrição opcional. 

    1. Selecione **Avançar** na parte inferior esquerda para carregá-lo no contêiner padrão que foi configurado automaticamente durante a criação do espaço de trabalho.  
    
       Quando o upload for concluído, as configurações e o formulário de visualização serão preenchidos previamente com base no tipo de arquivo. 
       
    1. Verifique se as **configurações e** o formulário de visualização estão preenchidos da seguinte maneira e selecione **Avançar**.
        
        Campo|Descrição| Valor do tutorial
        ---|---|---
        Formato de arquivo|Define o layout e o tipo de dados armazenados em um arquivo.| Delimitados
        Delimitador|Um ou mais caracteres para especificar o limite entre&nbsp; regiões separadas e independentes em texto sem formatação ou outros fluxos de dados. |Vírgula
        Encoding|Identifica o bit para a tabela de esquema de caractere a ser usada para ler seu conjunto de seus.| UTF-8
        Cabeçalhos de coluna| Indica como os cabeçalhos do conjunto de uma, se houver, serão tratados.| Todos os arquivos têm os mesmos cabeçalhos
        Ignorar linhas | Indica quantas linhas, se houver, são ignoradas no conjunto de registros.| Nenhuma

    1. O formulário de **esquema** permite a configuração adicional de seus dados para esse experimento. Para este exemplo, selecione a opção Alternar para o recurso **day_of_week** , para que não o inclua neste experimento. Selecione **Seguinte**.

        ![Configuração da guia de visualização](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. No formulário **confirmar detalhes** , verifique se as informações correspondem ao que foi previamente populado nas **informações básicas** e **nas configurações e** na versão prévia dos formulários.
    1. Selecione **criar** para concluir a criação do conjunto de seus conjuntos de seus.
    1. Selecione o conjunto de seus conjuntos de seus quando ele aparecer na lista.
    1. Examine a **visualização de dados** para garantir que você não incluiu **day_of_week** , em seguida, selecione **OK**.

    1. Selecione **Seguinte**.

1. Preencha o formulário de **execução de configuração** da seguinte maneira:
    1. Insira este nome de experimento: `my-1st-automl-experiment`

    1. Selecione **y** como a coluna de destino, o que você deseja prever. Esta coluna indica se o cliente assinou um termo de depósito ou não.
    1. Selecione **criar uma nova computação** e configure seu destino de computação. Um destino de computação é um ambiente de recursos local ou baseado em nuvem usado para executar o script de treinamento ou hospedar sua implantação de serviço. Para este experimento, usamos uma computação baseada em nuvem. 

        Campo | Descrição | Valor do tutorial
        ----|---|---
        Nome da computação |Um nome exclusivo que identifica o contexto de computação.|automl-computação
        Tamanho do&nbsp;máquina&nbsp;virtual| Selecione o tamanho da máquina virtual para sua computação.|Standard_DS12_V2
        Nós mín./máx. (em configurações avançadas)| Para criar o perfil de dados, você deve especificar um ou mais nós.|Nós mínimos: 1<br>Máximo de nós: 6
  
        1. Selecione **criar** para obter o destino de computação. 

            **Isso leva alguns minutos para ser concluído.** 

        1. Após a criação, selecione o novo destino de computação na lista suspensa.

    1. Selecione **Seguinte**.

1. No formulário **tipo de tarefa e configurações** , selecione **classificação** como o tipo de tarefa de aprendizado de máquina.

    1. Selecione **Exibir definições de configuração adicionais** e preencha os campos da seguinte maneira. Essas configurações são para controlar melhor o trabalho de treinamento. Caso contrário, os padrões serão aplicados com base na seleção de experimento e nos dados.

        >[!NOTE]
        > Neste tutorial, você não definirá uma pontuação de métrica ou um limite máximo de núcleos por iterações. Nem você bloqueará o teste de algoritmos.
   
        Configurações de&nbsp;adicionais|Descrição|&nbsp;de valor para o tutorial de&nbsp;
        ------|---------|---
        Métrica primária| Métrica de avaliação para a qual o algoritmo de aprendizado de máquina será medido.|AUC_weighted
        Personalização automática| Habilita o pré-processamento. Isso inclui a limpeza automática de dados, a preparação e a transformação para gerar recursos sintéticos.| Ativar
        Algoritmos bloqueados | Algoritmos que você deseja excluir do trabalho de treinamento| Nenhuma
        Critério de saída| Se um critério for atendido, o trabalho de treinamento será interrompido. |Tempo de&nbsp;do trabalho de treinamento&nbsp;(horas): 1 <br> &nbsp;limite da classificação de&nbsp;métrica: nenhum
        Validação | Escolha um tipo de validação cruzada e um número de testes.|Tipo de validação:<br>&nbsp;de validação cruzada de &nbsp;k-fold <br> <br> Número de validações: 2
        Simultaneidade| O número máximo de iterações paralelas executadas e núcleos usados por iteração| Máximo&nbsp;iterações de&nbsp;simultâneas: 5<br> Máximo de núcleos de&nbsp;&nbsp;por iteração de&nbsp;: nenhum
        
        Selecione **Guardar**.

1. Selecione **concluir** para executar o experimento. A tela **executar detalhes** é aberta com o **status de execução** à medida que a preparação do experimento é iniciada.

>[!IMPORTANT]
> A preparação leva de **10-15 minutos** para preparar a execução do experimento.
> Uma vez em execução, leva **2-3 minutos mais para cada iteração**.  
> Selecione **Atualizar** periodicamente para ver o status da execução durante o andamento do experimento.
>
> Em produção, você provavelmente se desapareceria por um pouco. Mas para este tutorial, sugerimos que você comece a explorar os algoritmos testados na guia modelos à medida que eles forem concluídos enquanto os outros ainda estiverem em execução. 

##  <a name="explore-models"></a>Explorar modelos

Navegue até a guia **modelos** para ver os algoritmos (modelos) testados. Por padrão, os modelos são ordenados pela pontuação da métrica à medida que são concluídos. Para este tutorial, o modelo que classifica o mais alto com base na métrica de **AUC_weighted** escolhida está na parte superior da lista.

Enquanto você aguarda que todos os modelos de experimento sejam concluídos, selecione o **nome do algoritmo** de um modelo concluído para explorar seus detalhes de desempenho. 

O seguinte navega pelas guias **detalhes do modelo** e **visualizações** para exibir as propriedades, as métricas e os gráficos de desempenho do modelo selecionado. 

![Detalhes da execução de iteração](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Implementar o modelo

O Machine Learning automatizado no Azure Machine Learning Studio permite que você implante o melhor modelo como um serviço Web em algumas etapas. A implantação é a integração do modelo para que ele possa prever novos dados e identificar possíveis áreas de oportunidade. 

Para este experimento, a implantação em um serviço Web significa que a instituição financeira agora tem uma solução Web iterativa e escalonável para identificar clientes de depósito de prazo fixo. 

Quando a execução for concluída, navegue de volta para a página de **detalhes da execução** e selecione a guia **modelos** . Selecione **Atualizar**. 

Nesse contexto de experimento, **VotingEnsemble** é considerado o melhor modelo, com base na métrica de **AUC_weighted** .  Implantamos esse modelo, mas é recomendável que a implantação demore cerca de 20 minutos para ser concluída. O processo de implantação envolve várias etapas, incluindo o registro do modelo, a geração de recursos e a configuração deles para o serviço Web.

1. Selecione o botão **implantar melhor modelo** no canto inferior esquerdo.

1. Preencha o painel **implantar um modelo** da seguinte maneira:

    Campo| Valor
    ----|----
    Nome da implantação| meu-automl-implantar
    Descrição da implantação| Minha primeira implantação de experimento automatizada do Machine Learning
    Tipo de computação | Selecionar instância de computação do Azure (ACI)
    Ativar autenticação| Desativar. 
    Usar implantações personalizadas| Desativar. Permite que o arquivo de driver padrão (script de pontuação) e o arquivo de ambiente sejam gerados automaticamente. 
    
    Para este exemplo, usamos os padrões fornecidos no menu *avançado* . 

1. Selecione **Implementar**.  

    Uma mensagem de êxito verde aparece na parte superior da tela de **execução** e, no painel **modelo recomendado** , uma mensagem de status aparece em **implantar status**. Selecione **Atualizar** periodicamente para verificar o status da implantação.
    
Agora você tem um serviço Web operacional para gerar previsões. 

Prossiga para as [**próximas etapas**](#next-steps) para saber mais sobre como consumir seu novo serviço Web e testar suas previsões usando o suporte Azure Machine Learning interno do Power bi.

## <a name="clean-up-resources"></a>Limpar recursos

Os arquivos de implantação são maiores que os dados e os arquivos de teste, portanto, eles custam mais para serem armazenados. Exclua somente os arquivos de implantação para minimizar os custos em sua conta ou se você quiser manter o espaço de trabalho e os arquivos de teste. Caso contrário, exclua o grupo de recursos inteiro, se você não planeja usar nenhum dos arquivos.  

### <a name="delete-the-deployment-instance"></a>Excluir a instância de implantação

Exclua apenas a instância de implantação do Azure Machine Learning Studio, se você quiser manter o grupo de recursos e o espaço de trabalho para outros tutoriais e explorações. 

1. Vá para o [Azure Machine Learning Studio](https://ml.azure.com/). Navegue até seu espaço de trabalho e, à esquerda, no painel **ativos** , selecione **pontos de extremidade**. 

1. Selecione a implantação que você deseja excluir e selecione **excluir**. 

1. Selecione **continuar**.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de aprendizado de máquina automatizado, você usou o Azure Machine Learning Studio para criar e implantar um modelo de classificação. Consulte estes artigos para obter mais informações e as próximas etapas:

> [!div class="nextstepaction"]
> [Consumir um serviço Web](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Saiba mais sobre o [pré-processamento](how-to-create-portal-experiments.md#preprocess).
+ Saiba mais sobre a [criação de perfil de dados](how-to-create-portal-experiments.md#profile).
+ Saiba mais sobre o [aprendizado de máquina automatizado](concept-automated-ml.md).
+ Para obter mais informações sobre métricas de classificação e gráficos, consulte o artigo [entender os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md#classification) .

>[!NOTE]
> Esse conjunto de Cco de marketing bancário é disponibilizado na [licença Creative Commons (: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Todos os direitos no conteúdo individual do banco de dados são licenciados sob a [licença de conteúdo do banco de dados](https://creativecommons.org/publicdomain/zero/1.0/) e estão disponíveis em [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Esse conjunto de dados estava originalmente disponível no [banco de dados de Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. moro, P. Cortez e P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Sistemas de suporte a decisões, Elsevier, 62:22-31, junho 2014.
