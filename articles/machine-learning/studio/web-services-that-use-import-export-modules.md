---
title: Importar/exportar dados nos serviços da web - Azure Machine Learning Studio | Documentos da Microsoft
description: Saiba como utilizar os módulos importar dados e exportar dados para enviar e receber dados de um serviço web.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 28d16bce6dbb5063c085e8c4393777ee9d152768
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092043"
---
# <a name="deploy-azure-machine-learning-studio-web-services-that-use-data-import-and-data-export-modules"></a>Implementar serviços web Azure Machine Learning Studio que utilizam módulos importar dados e exportação de dados

Quando cria uma experimentação preditiva, normalmente, adicione uma entrada do serviço web e a saída. Quando implementa a experimentação, os consumidores podem enviar e receber dados do serviço web através de entradas e saídas. Para algumas aplicações, dados de um consumidor poderão estar disponível a partir de um feed de dados ou já estejam na origem de dados externa, como o armazenamento de Blobs do Azure. Nestes casos, eles não precisam de leitura e escrita de dados com o web service entradas e saídas. Podem, em vez disso, utilize o serviço de execução de lote (BES) para ler dados de origem de dados com um módulo de importar dados e escrever os resultados de classificação para uma localização de dados diferentes usando um módulo de exportar dados.

Os dados de importação e exportação módulos de dados, pode ler a partir do e gravar dados de várias localizações, como um URL de Web via HTTP, uma consulta do Hive, uma base de dados SQL do Azure, armazenamento de tabelas do Azure, armazenamento de Blobs do Azure, um Feed de dados fornecem ou uma base de dados do SQL no local.

Este tópico utiliza o "exemplo 5: Preparar, testar, avalie para classificação binária: Exemplo de um conjunto de dados para adultos"e pressupõe que o conjunto de dados já foi carregado para uma tabela de SQL do Azure com o nome censusdata.

## <a name="create-the-training-experiment"></a>Criar a experimentação de preparação
Ao abrir o "exemplo 5: Preparar, testar, avalie para classificação binária: Exemplo de um conjunto de dados para adultos"utiliza o conjunto de dados de classificação de binário para adultos recenseamento receitas de exemplo. E a experimentação na tela será semelhante à seguinte imagem:

![Configuração inicial da experimentação.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela SQL do Azure:

1. Elimine o módulo de conjunto de dados.
2. Na caixa de pesquisa de componentes, tipo de importação.
3. A partir da lista de resultados, adicione uma *importar dados* módulo para a tela da experimentação.
4. Ligue a saída do *importar dados* a entrada do módulo da *Clean Missing Data* módulo.
5. No painel de propriedades, selecione **Azure SQL Database** no **origem de dados** lista pendente.
6. Na **nome do servidor de base de dados**, **nome da base de dados**, **nome de utilizador**, e **palavra-passe** campos, introduza as informações apropriadas para seu base de dados.
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
8. Na parte inferior da tela de experimentação, clique em **executar**.

## <a name="create-the-predictive-experiment"></a>Criar a experimentação preditiva
Em seguida, configure a experimentação preditiva a partir do qual implementar o serviço web.

1. Na parte inferior da tela de experimentação, clique em **segurança de serviço Web** e selecione **preditiva Web Service [Recomendado]**.
2. Remover os *entrada de serviço Web* e *módulos de saída de serviço da Web* da experimentação preditiva.
3. Na caixa de pesquisa de componentes, tipo de exportação.
4. A partir da lista de resultados, adicione uma *exportar dados* módulo para a tela da experimentação.
5. Ligue a saída do *modelo de pontuação* a entrada do módulo da *exportar dados* módulo.
6. No painel de propriedades, selecione **base de dados do Azure SQL** na lista pendente de destino de dados.
7. Na **nome do servidor de base de dados**, **nome da base de dados**, **nome de conta de utilizador do servidor**, e **palavra-passe de conta de utilizador servidor** campos, introduza o obter informações apropriadas para sua base de dados.
8. Na **lista de colunas a serem salvos separados por vírgulas** , digite etiquetas classificadas.
9. Na **campo de nome de tabela de dados**, escreva o dbo. ScoredLabels. Se a tabela não existir, é criado quando a experimentação é executada ou denomina-se o serviço web.
10. Na **lista de colunas da datatable separados por vírgulas** campo, escreva ScoredLabels.

Quando escreve uma aplicação que chama o serviço final web, poderá especificar uma consulta de entrada diferente ou de uma tabela de destino no tempo de execução. Para configurar essas entradas e saídas, utilize a funcionalidade de parâmetros do serviço Web para definir o *importar dados* módulo *origem de dados* propriedade e o *exportar dados* dados de modo propriedade de destino.  Para obter mais informações sobre parâmetros do serviço Web, consulte a [entrada de parâmetros do serviço Web do Azure Machine Learning studio](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) sobre o Cortana Intelligence e o blogue de Machine Learning.

Para configurar os parâmetros de serviço da Web para a consulta de importação e a tabela de destino:

1. No painel de propriedades para o *importar dados* módulo, clique no ícone na parte superior direita do **consulta de base de dados** campo e selecione **definido como parâmetro de serviço da web**.
2. No painel de propriedades para o *exportar dados* módulo, clique no ícone na parte superior direita do **nome da tabela de dados** campo e selecione **definido como parâmetro de serviço da web**.
3. Na parte inferior a *exportar dados* painel de propriedades do módulo, na **parâmetros do serviço Web** secção, clique em consulta de base de dados e renomeá-lo a consulta.
4. Clique em **nome da tabela de dados** e mude o nome **tabela**.

Quando tiver terminado, sua experimentação deve ter um aspeto semelhante à seguinte imagem:

![Aparência final da experimentação.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora pode implementar a experimentação como um serviço web.

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Pode implementar um clássico ou um novo serviço web.

### <a name="deploy-a-classic-web-service"></a>Implementar um serviço Web clássico
Para implementar como um serviço Web clássico e criar uma aplicação de consumi-las:

1. Na parte inferior da tela de experimentação, clique em executar.
2. Quando a execução for concluída, clique em **implementar serviço Web** e selecione **implementar o serviço Web [clássica]**.
3. No dashboard do serviço web, localize a chave de API. Copie e guarde-o para utilizar mais tarde.
4. Na **ponto final predefinido** da tabela, clique no **execução de lotes** link para abrir a página de ajuda da API.
5. No Visual Studio, crie um C# aplicação de consola: **Novos** > **projeto** > **Visual C#**   >  **ambiente de trabalho clássico do Windows**  >   **Aplicação da consola (.NET Framework)**.
6. A página Ajuda da API, encontrará a **código de exemplo** seção na parte inferior da página.
7. Copie e cole o C# código de exemplo para o ficheiro Program.cs e remover todas as referências para o armazenamento de Blobs.
8. Atualize o valor do *apiKey* variável com a chave de API que guardou anteriormente.
9. Localize a declaração de pedido e atualizar os valores dos parâmetros do serviço Web que são transmitidos para o *importar dados* e *exportar dados* módulos. Neste caso, usa a consulta original, mas define um novo nome de tabela.

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
> Para implementar um novo serviço web tem de ter permissões suficientes na subscrição para a qual estiver a implementar o serviço web. Para obter mais informações, consulte [gerir um serviço Web através do portal do Azure Machine Learning Web Services](manage-new-webservice.md).

Para implementar como um novo serviço Web e criar uma aplicação de consumi-las:

1. Na parte inferior da tela de experimentação, clique em **executar**.
2. Quando a execução for concluída, clique em **implementar serviço Web** e selecione **implementar o Web Service [novo]**.
3. Na página de experimentação de implementar, introduza um nome para o seu serviço web e selecione um plano de preços, em seguida, clique em **Deploy**.
4. Sobre o **início rápido** página, clique em **Consume**.
5. Na **código de exemplo** secção, clique em **Batch**.
6. No Visual Studio, crie um C# aplicação de consola: **Novos** > **projeto** > **Visual C#**   >  **ambiente de trabalho clássico do Windows**  >   **Aplicação da consola (.NET Framework)**.
7. Copie e cole o C# código de exemplo para o ficheiro Program.cs.
8. Atualize o valor do *apiKey* variável com o **chave primária** localizado no **informações de consumo básica** secção.
9. Localize a *scoreRequest* declaração e atualizar os valores dos parâmetros do serviço Web que são transmitidos para o *importar dados* e *exportar dados* módulos. Neste caso, usa a consulta original, mas define um novo nome de tabela.

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

