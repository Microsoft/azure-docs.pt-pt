---
title: Dados de Importação/Exportação em serviços web
titleSuffix: ML Studio (classic) - Azure
description: Saiba como utilizar os módulos importar dados e exportar dados para enviar e receber dados de um serviço web.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 144a3bc0d9e0499a238e4033d37d5e4d3fa61e05
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204068"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Implementar serviços web Azure Machine Learning Studio (clássicos) que utilizam módulos de importação de dados e exportação de dados

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Quando cria uma experimentação preditiva, normalmente, adicione uma entrada do serviço web e a saída. Quando implementa a experimentação, os consumidores podem enviar e receber dados do serviço web através de entradas e saídas. Para algumas aplicações, dados de um consumidor poderão estar disponível a partir de um feed de dados ou já estejam na origem de dados externa, como o armazenamento de Blobs do Azure. Nestes casos, eles não precisam de leitura e escrita de dados com o web service entradas e saídas. Podem, em vez disso, utilize o serviço de execução de lote (BES) para ler dados de origem de dados com um módulo de importar dados e escrever os resultados de classificação para uma localização de dados diferentes usando um módulo de exportar dados.

Os dados de importação e exportação módulos de dados, pode ler a partir do e gravar dados de várias localizações, como um URL de Web via HTTP, uma consulta do Hive, uma base de dados SQL do Azure, armazenamento de tabelas do Azure, armazenamento de Blobs do Azure, um Feed de dados fornecem ou uma base de dados do SQL no local.

Este tópico utiliza o "exemplo 5: Train, teste, avaliação de classificação binária: conjunto de dados para adultos" de exemplo e pressupõe que o conjunto de dados já foi carregado para uma tabela de SQL do Azure com o nome censusdata.

## <a name="create-the-training-experiment"></a>Criar a experimentação de preparação
Ao abrir o "exemplo 5: Train, teste, avaliação de classificação binária: conjunto de dados para adultos" exemplo utiliza o conjunto de dados de classificação de binário para adultos recenseamento receitas de exemplo. E a experimentação na tela será semelhante à seguinte imagem:

![Configuração inicial da experimentação.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela SQL do Azure:

1. Elimine o módulo de conjunto de dados.
2. Na caixa de pesquisa de componentes, tipo de importação.
3. Da lista de resultados, adicione um módulo *de Dados de Importação* à tela de experiência.
4. Ligue a saída do módulo *de dados* de importação a entrada do módulo clean *missing Data.*
5. No painel de propriedades, selecione Base de **Dados Azure SQL** no dropdown **da Fonte de Dados.**
6. No **nome**do servidor base de dados , Nome de base de **dados,** nome do **utilizador**e campos **de palavra-passe,** introduza as informações apropriadas para a sua base de dados.
7. No campo de consulta de base de dados, introduza a seguinte consulta.

     Selecione [idade],

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

## <a name="create-the-predictive-experiment"></a>Criar a experimentação preditiva
Em seguida, configure a experimentação preditiva a partir do qual implementar o serviço web.

1. Na parte inferior da tela de experiência, clique em **Configurar** o Web Service e selecione **Predictive Web Service [Recomendado]** .
2. Remova os *módulos* de saída de entrada de *serviço web* e de serviço web da experiência preditiva.
3. Na caixa de pesquisa de componentes, tipo de exportação.
4. Da lista de resultados, adicione um módulo de Dados de *Exportação* à tela de experimentação.
5. Ligue a saída do módulo *'Modelo de Pontuação'* a entrada do módulo Dados de *Exportação.*
6. No painel de propriedades, selecione Base de **Dados Azure SQL** no desfecho do destino de dados.
7. No **nome**do servidor base de dados , nome **da base**de dados , nome da conta do **servidor**e nos campos de **palavras-passe** da conta do servidor, introduza as informações apropriadas para a sua base de dados.
8. Na **lista separada da Vírsia de colunas a serem guardadas,** digite etiquetas pontuais.
9. No campo de nome da **tabela Data,** digite dbo. Rótulos pontuais. Se a tabela não existir, é criado quando a experimentação é executada ou denomina-se o serviço web.
10. Na **lista separada da Comma de colunas datatable,** digite Etiquetas pontuais.

Quando escreve uma aplicação que chama o serviço final web, poderá especificar uma consulta de entrada diferente ou de uma tabela de destino no tempo de execução. Para configurar estas inputs e saídas, utilize a funcionalidade Parâmetros do Serviço Web para definir a propriedade de origem de *dados* do módulo *de dados de importação* e a propriedade de destino de destino de dados do modo de *exportação Data.*  Para obter mais informações sobre os Parâmetros do Serviço Web, consulte a entrada dos parâmetros do Serviço Web do Estúdio de [Aprendizagem automática Azure](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) machine learning no Blog cortana Intelligence and Machine Learning.

Para configurar os parâmetros de serviço da Web para a consulta de importação e a tabela de destino:

1. No painel de propriedades para o módulo *Dados de Importação,* clique no ícone na parte superior direita do campo de **consulta base** de dados e selecione set como parâmetro de **serviço web**.
2. No painel de propriedades para o módulo Dados de *Exportação,* clique no ícone na parte superior direita do campo de nome da **tabela Data** e selecione set como parâmetro de **serviço web**.
3. Na parte inferior do painel de propriedades do módulo de dados de *exportação,* na secção Parâmetros do **Serviço Web,** clique na consulta de Base de Dados e mude o nome da consulta.
4. Clique no nome da **tabela Data** e mude o nome **tabela**.

Quando tiver terminado, sua experimentação deve ter um aspeto semelhante à seguinte imagem:

![Aparência final da experimentação.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora pode implementar a experimentação como um serviço web.

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode implementar um clássico ou um novo serviço web.

### <a name="deploy-a-classic-web-service"></a>Implementar um serviço Web clássico
Para implementar como um serviço Web clássico e criar uma aplicação de consumi-las:

1. Na parte inferior da tela de experimentação, clique em executar.
2. Quando a execução estiver concluída, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Classic]** .
3. No dashboard do serviço web, localize a chave de API. Copie e guarde-o para utilizar mais tarde.
4. Na tabela **'Ponto final' predefinido,** clique no link **de execução** do lote para abrir a página de ajuda da API.
5. No Estúdio Visual, C# crie uma aplicação de consola: **New** > **Project** > **Visual C#**  > **Windows Classic Desktop** > Console App **(.NET Framework)** .
6. Na página de ajuda da API, encontre a secção Código da **Amostra** na parte inferior da página.
7. Copie e cole o C# código de exemplo para o ficheiro Program.cs e remover todas as referências para o armazenamento de Blobs.
8. Atualize o valor da variável *apiKey* com a tecla API guardada anteriormente.
9. Localize a declaração de pedido e atualize os valores dos Parâmetros do Serviço Web que são passados para os módulos de Dados de *Importação* e *Dados de Exportação.* Neste caso, usa a consulta original, mas define um novo nome de tabela.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Execute a aplicação.

Após a conclusão da execução, uma nova tabela é adicionada à base de dados que contém os resultados de classificação.

### <a name="deploy-a-new-web-service"></a>Implementar um novo serviço Web

> [!NOTE]
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição para a qual estiver a implementar o serviço web. Para mais informações, consulte [Gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md).

Para implementar como um novo serviço Web e criar uma aplicação de consumi-las:

1. Na parte inferior da tela de experiência, clique em **Executar**.
2. Quando a execução estiver concluída, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Novo]** .
3. Na página Implementar Experiências, introduza um nome para o seu serviço web e selecione um plano de preços e, em seguida, clique em **Implementar**.
4. Na página **Quickstart,** clique **em Consumir**.
5. Na secção Código da **Amostra,** clique **em Batch**.
6. No Estúdio Visual, C# crie uma aplicação de consola: **New** > **Project** > **Visual C#**  > **Windows Classic Desktop** > Console App **(.NET Framework)** .
7. Copie e cole o C# código de exemplo para o ficheiro Program.cs.
8. Atualize o valor da variável *apiKey* com a **Chave Primária** localizada na secção de informação de **consumo Básico.**
9. Localize a *declaração de pedido de pontuação* e atualize os valores dos Parâmetros do Serviço Web que são passados para os módulos de Dados de *Importação* e Dados *de Exportação.* Neste caso, usa a consulta original, mas define um novo nome de tabela.

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

