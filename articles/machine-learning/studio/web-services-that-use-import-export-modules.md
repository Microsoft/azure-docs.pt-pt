---
title: Dados de Importação/Exportação em serviços web
titleSuffix: ML Studio (classic) - Azure
description: Saiba como utilizar os módulos de Dados de Importação e Dados de Exportação para enviar e receber dados de um serviço web.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 3275a372e496b79da2c9f31258f557389c5b1ee1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209371"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Implementar serviços web Azure Machine Learning Studio (clássicos) que utilizam módulos de importação de dados e exportação de dados

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Quando se cria uma experiência preditiva, normalmente adiciona-se uma entrada e saída de serviço web. Quando implementa a experiência, os consumidores podem enviar e receber dados do serviço web através das inputs e saídas. Para algumas aplicações, os dados de um consumidor podem estar disponíveis a partir de um feed de dados ou já residir numa fonte externa de dados, como o armazenamento de Azure Blob. Nestes casos, não precisam de ler e escrever dados utilizando inputs e saídas do serviço web. Podem, em vez disso, utilizar o Serviço de Execução de Lotes (BES) para ler dados da fonte de dados utilizando um módulo de Dados de Importação e escrever os resultados da pontuação para um local de dados diferente utilizando um módulo de Dados de Exportação.

Os módulos de dados de importação e exportação, podem ler e escrever para vários locais de dados, tais como um URL Web via HTTP, uma Hive Query, uma base de dados Azure SQL, armazenamento de mesa Azure, armazenamento de Blob Azure, um Feed de Dados fornecer, ou uma base de dados SQL no local.

Este tópico utiliza a amostra "Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset" e assume que o conjunto de dados já foi carregado numa tabela Azure SQL chamada censusdata.

## <a name="create-the-training-experiment"></a>Criar a experiência de treino
Ao abrir a amostra "Sample 5: Train, Test, Assess for Binary Classification: Adult Dataset" utiliza a amostra de classificação binária de rendimento do recenseamento adulto. E a experiência na tela será semelhante à seguinte imagem:

![Configuração inicial da experiência.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela Azure SQL:

1. Elimine o módulo de conjunto de dados.
2. Na caixa de pesquisa de componentes, digite a importação.
3. Da lista de resultados, adicione um módulo *de Dados de Importação* à tela de experiência.
4. Ligue a saída do módulo *de dados* de importação a entrada do módulo clean *missing Data.*
5. No painel de propriedades, selecione Base de **Dados Azure SQL** no dropdown **da Fonte de Dados.**
6. No **nome**do servidor base de dados , Nome de base de **dados,** nome do **utilizador**e campos **de palavra-passe,** introduza as informações apropriadas para a sua base de dados.
7. No campo de consulta da Base de Dados, introduza a seguinte consulta.

     selecionar [idade],

        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     de dbo.censusdata;
8. Na parte inferior da tela de experiência, clique em **Executar**.

## <a name="create-the-predictive-experiment"></a>Criar a experiência preditiva
Em seguida, configura a experiência preditiva a partir da qual implementa o seu serviço web.

1. Na parte inferior da tela de experiência, clique em **Configurar** o Web Service e selecione **Predictive Web Service [Recomendado]**.
2. Remova os *módulos* de saída de entrada de *serviço web* e de serviço web da experiência preditiva.
3. Na caixa de pesquisa de componentes, digite a exportação.
4. Da lista de resultados, adicione um módulo de Dados de *Exportação* à tela de experimentação.
5. Ligue a saída do módulo *'Modelo de Pontuação'* a entrada do módulo Dados de *Exportação.*
6. No painel de propriedades, selecione Base de **Dados Azure SQL** no desfecho do destino de dados.
7. No **nome**do servidor base de dados , nome **da base**de dados , nome da conta do **servidor**e nos campos de **palavras-passe** da conta do servidor, introduza as informações apropriadas para a sua base de dados.
8. Na **lista separada da Vírsia de colunas a serem guardadas,** digite etiquetas pontuais.
9. No campo de nome da **tabela Data,** digite dbo. Rótulos pontuais. Se a tabela não existir, é criada quando a experiência é executada ou o serviço web é chamado.
10. Na **lista separada da Comma de colunas datatable,** digite Etiquetas pontuais.

Ao escrever uma aplicação que ligue para o serviço web final, poderá querer especificar uma consulta de entrada diferente ou tabela de destino no momento de execução. Para configurar estas inputs e saídas, utilize a funcionalidade Parâmetros do Serviço Web para definir a propriedade de origem de *dados* do módulo *de dados de importação* e a propriedade de destino de destino de dados do modo de *exportação Data.*  Para obter mais informações sobre os Parâmetros do Serviço Web, consulte a entrada dos parâmetros do Serviço Web do Estúdio de [Aprendizagem automática Azure](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) machine learning no Blog cortana Intelligence and Machine Learning.

Para configurar os parâmetros do Serviço Web para a consulta de importação e a tabela de destino:

1. No painel de propriedades para o módulo *Dados de Importação,* clique no ícone na parte superior direita do campo de **consulta base** de dados e selecione set como parâmetro de **serviço web**.
2. No painel de propriedades para o módulo Dados de *Exportação,* clique no ícone na parte superior direita do campo de nome da **tabela Data** e selecione set como parâmetro de **serviço web**.
3. Na parte inferior do painel de propriedades do módulo de dados de *exportação,* na secção Parâmetros do **Serviço Web,** clique na consulta de Base de Dados e mude o nome da consulta.
4. Clique no nome da **tabela Data** e mude o nome **tabela**.

Quando terminar, a sua experiência deve parecer semelhante à seguinte imagem:

![Olhar final da experiência.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora pode implementar a experiência como um serviço web.

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode ser implantado num serviço web Clássico ou Novo.

### <a name="deploy-a-classic-web-service"></a>Implementar um Serviço Web Clássico
Para implantar como um Serviço Web Clássico e criar uma aplicação para consumi-lo:

1. Na parte inferior da tela de experiência, clique em Run.
2. Quando a execução estiver concluída, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Classic]**.
3. No painel de instrumentos do serviço web, localize a sua chave API. Copie e guarde para usar mais tarde.
4. Na tabela **'Ponto final' predefinido,** clique no link **de execução** do lote para abrir a página de ajuda da API.
5. No Estúdio Visual, crie uma aplicação de consola C#: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)**.
6. Na página de ajuda da API, encontre a secção Código da **Amostra** na parte inferior da página.
7. Copie e cole o código de amostra C# no seu ficheiro Program.cs e remova todas as referências ao armazenamento de bolhas.
8. Atualize o valor da variável *apiKey* com a tecla API guardada anteriormente.
9. Localize a declaração de pedido e atualize os valores dos Parâmetros do Serviço Web que são passados para os módulos de Dados de *Importação* e *Dados de Exportação.* Neste caso, você usa a consulta original, mas define um novo nome de mesa.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Execute a aplicação.

Após a conclusão da execução, é adicionada uma nova tabela à base de dados que contém os resultados das pontuações.

### <a name="deploy-a-new-web-service"></a>Implementar um novo serviço web

> [!NOTE]
> Para implementar um novo serviço web deve ter permissões suficientes na subscrição à qual implementa o serviço web. Para mais informações, consulte [Gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md).

Para implementar como um Novo Serviço Web e criar uma aplicação para consumi-lo:

1. Na parte inferior da tela de experiência, clique em **Executar**.
2. Quando a execução estiver concluída, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Novo]**.
3. Na página Implementar Experiências, introduza um nome para o seu serviço web e selecione um plano de preços e, em seguida, clique em **Implementar**.
4. Na página **Quickstart,** clique **em Consumir**.
5. Na secção Código da **Amostra,** clique **em Batch**.
6. No Estúdio Visual, crie uma aplicação de consola C#: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework)**.
7. Copie e cole o código de amostra C# no seu ficheiro Program.cs.
8. Atualize o valor da variável *apiKey* com a **Chave Primária** localizada na secção de informação de **consumo Básico.**
9. Localize a *declaração de pedido de pontuação* e atualize os valores dos Parâmetros do Serviço Web que são passados para os módulos de Dados de *Importação* e Dados *de Exportação.* Neste caso, você usa a consulta original, mas define um novo nome de mesa.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Execute a aplicação.

