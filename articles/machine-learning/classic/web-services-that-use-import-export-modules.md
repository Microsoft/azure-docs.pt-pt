---
title: 'Estúdio ML (clássico): Dados de Importação/Exportação em serviços web - Azure'
description: Saiba como utilizar os módulos de Dados de Importação e Exportação para enviar e receber dados de um serviço web.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a963a9f10ee23c50f50e66191e92f0839c457d9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362853"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Implementar serviços web do Azure Machine Learning Studio (clássicos) que utilizam módulos de importação de dados e exportação de dados

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../compare-azure-ml-to-studio-classic.md)  


Quando cria uma experiência preditiva, normalmente adiciona-se uma entrada e saída de serviço web. Quando implementa a experiência, os consumidores podem enviar e receber dados do serviço web através das entradas e saídas. Para algumas aplicações, os dados de um consumidor podem estar disponíveis a partir de um feed de dados ou já residir numa fonte de dados externa, como o armazenamento Azure Blob. Nestes casos, não precisam de ler e escrever dados utilizando entradas e saídas de serviço web. Em vez disso, podem utilizar o Serviço de Execução de Lotes (BES) para ler dados da fonte de dados utilizando um módulo de Dados de Importação e escrever os resultados da pontuação para um local de dados diferente utilizando um módulo de Dados de Exportação.

Os módulos de dados de importação e exportação, podem ler e escrever para vários locais de dados, tais como um URL web via HTTP, uma Consulta de Colmeia, uma base de dados na Base de Dados Azure SQL, armazenamento de mesa Azure, armazenamento de Azure Blob, um feed de dados fornecer, ou uma base de dados do SQL Server.

Este tópico utiliza a amostra "Amostra 5: Comboio, Teste, Avaliação para Classificação Binária: Conjunto de Dados para Adultos" e assume que o conjunto de dados já foi carregado numa tabela Azure SQL chamada censusdata.

## <a name="create-the-training-experiment"></a>Criar a experiência de treino
Quando abre a amostra "Amostra 5: Comboio, Teste, Avaliação para Classificação Binária: Conjunto de Dados para Adultos" utiliza a amostra de conjunto de dados de classificação binária de rendimento de censos adulto. E a experiência na tela será semelhante à seguinte imagem:

![Configuração inicial da experiência.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela Azure SQL:

1. Elimine o módulo de conjunto de dados.
2. Na caixa de pesquisa de componentes, tipo importação.
3. Da lista de resultados, adicione um módulo *de Dados de Importação* à tela de experiência.
4. Ligue a saída do módulo *de dados de importação* a entrada do módulo de *dados em falta.*
5. No painel de propriedades, selecione **Azure SQL Database** na origem de **dados.**
6. No nome do **servidor base de dados**, **nome de base de dados,** **nome de utilizador** **e** password, introduza as informações adequadas para a sua base de dados.
7. No campo de consulta de base de dados, insira a seguinte consulta.

    ```tsql
     select [age],
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
     from dbo.censusdata;
    ```
8. Na parte inferior da tela de experiência, clique em **Executar**.

## <a name="create-the-predictive-experiment"></a>Criar a experiência preditiva
Em seguida, configura a experiência preditiva a partir da qual implementa o seu serviço web.

1. Na parte inferior da tela de experiência, clique em **Configurar o Serviço Web** e selecione **o Serviço Web Preditivo [Recomendado]**.
2. Remova os *módulos* de entrada de *serviço web* e de saída de serviço web da experiência preditiva.
3. Na caixa de pesquisa de componentes, escreva exportação.
4. Da lista de resultados, adicione um módulo *de Dados de Exportação* à tela de experiência.
5. Ligue a saída do módulo *'Modelo de Pontuação'* a entrada do módulo *de dados de exportação.*
6. No painel de propriedades, selecione **Azure SQL Database** no dropdown do destino de dados.
7. No nome do **servidor base de dados**, nome de base de **dados,** **nome da conta do utilizador do servidor**e na conta de utilizador do **Servidor,** introduza as informações apropriadas para a sua base de dados.
8. Na lista separada da **Vírgula de colunas a guardar,** escreva labels.
9. No campo de **nome da tabela de dados,** escreva dbo. ScoredLabels. Se a tabela não existir, é criada quando a experiência é executada ou o serviço web é chamado.
10. Na lista separada da **Vírgula do campo de colunas de dados,** escreva MarcouLabels.

Quando escrever uma aplicação que ligue para o serviço web final, pode querer especificar uma consulta de entrada ou tabela de destino diferente no tempo de execução. Para configurar estas entradas e saídas, utilize a funcionalidade Parâmetros de Serviço Web para definir a propriedade de fonte de *dados* do módulo *de dados de importação* e a propriedade de destino de dados do modo de *exportação.*  Para obter mais informações sobre os parâmetros do Serviço Web, consulte a entrada dos parâmetros do [Serviço Web Azure Machine Learning Studio](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) no Cortana Intelligence and Machine Learning Blog.

Para configurar os parâmetros do Serviço Web para a consulta de importação e para a tabela de destino:

1. No painel de propriedades para o módulo *de dados de importação,* clique no ícone no topo direito do campo de **consulta database** e selecione Definir como parâmetro de **serviço web**.
2. No painel de propriedades para o módulo *dados de exportação,* clique no ícone no topo direito do campo de **nome da tabela de dados** e selecione Definir como parâmetro de serviço **web**.
3. Na parte inferior do painel de propriedades do módulo *de dados de exportação,* na secção parâmetros do **Serviço Web,** clique na consulta de Base de Dados e rebatize-a em Consulta.
4. Clique no **nome da tabela de dados** e rebatize-o **Tabela**.

Quando terminar, a sua experiência deve ser semelhante à seguinte imagem:

![Último olhar de experiência.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora pode implementar a experiência como um serviço web.

## <a name="deploy-the-web-service"></a>Implementar o serviço Web
Pode implementar um serviço web Clássico ou Novo.

### <a name="deploy-a-classic-web-service"></a>Implementar um serviço web clássico
Para implementar como um Serviço Web Clássico e criar uma aplicação para consumi-lo:

1. Na parte inferior da tela de experiência, clique em Executar.
2. Quando a execução tiver terminado, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Clássico]**.
3. No painel de atendimento web, localize a sua chave API. Copie e guarde-o para usar mais tarde.
4. Na tabela **Ponto final predefinido,** clique no link **de execução** do lote para abrir a Página de Ajuda da API.
5. No Visual Studio, crie uma aplicação para consola C#: **Novo**  >  **Project**  >  **Visual C#** Windows Classic  >  **Desktop**Console  >  **App (.NET Framework)**.
6. Na página de ajuda da API, encontre a secção **código de amostra** na parte inferior da página.
7. Copie e cole o código de amostra C# no seu ficheiro Program.cs e remova todas as referências ao armazenamento do blob.
8. Atualize o valor da variável *apiKey* com a chave API guardada anteriormente.
9. Localize a declaração de pedido e atualize os valores dos Parâmetros do Serviço Web que são transmitidos aos módulos *de Dados de Importação* e *Exportação.* Neste caso, você usa a consulta original, mas define um novo nome de mesa.

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. Execute a aplicação.

Após a conclusão da execução, é adicionada uma nova tabela à base de dados que contém os resultados da pontuação.

### <a name="deploy-a-new-web-service"></a>Implementar um novo serviço web

> [!NOTE]
> Para implementar um Novo serviço web tem de ter permissões suficientes na subscrição à qual implementa o serviço web. Para obter mais informações, consulte [Gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md).

Para implementar como um Novo Serviço Web e criar uma aplicação para consumi-lo:

1. Na parte inferior da tela de experiência, clique em **Executar**.
2. Quando a execução tiver terminado, clique em **Implementar o Serviço Web** e selecione Implementar o Serviço Web **[Novo]**.
3. Na página 'Implementar experiência', insira um nome para o seu serviço web e selecione um plano de preços e, em seguida, clique em **Implementar**.
4. Na página **Quickstart,** clique em **Consumir**.
5. Na secção **Código amostra,** clique em **Lote**.
6. No Visual Studio, crie uma aplicação para consola C#: **Novo**  >  **Project**  >  **Visual C#** Windows Classic  >  **Desktop**Console  >  **App (.NET Framework)**.
7. Copie e cole o código de amostra C# no seu ficheiro Program.cs.
8. Atualize o valor da variável *apiKey* com a **Chave Primária** localizada na secção **informações de consumo básico.**
9. Localize a declaração *de pontuaçãoRequest* e atualize os valores dos Parâmetros do Serviço Web que são transmitidos aos módulos *de Dados de Importação* e *Exportação.* Neste caso, você usa a consulta original, mas define um novo nome de mesa.

    ```csharp
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
    ```
10. Execute a aplicação.

