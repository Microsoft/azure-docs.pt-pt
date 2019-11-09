---
title: Importar/exportar dados nos serviços Web
titleSuffix: ML Studio (classic) - Azure
description: Saiba como usar os módulos importar dados e exportar dados para enviar e receber dados de um serviço Web.
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
ms.openlocfilehash: 0ae545fd3ecafda74b90a6a4694dd6f506fb44b1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838807"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Implantar serviços Web Azure Machine Learning Studio (clássico) que usam módulos de importação de dados e exportação de dados

Ao criar um experimento de previsão, você normalmente adiciona uma saída e entrada de serviço Web. Quando você implanta o experimento, os consumidores podem enviar e receber dados do serviço Web por meio de entradas e saídas. Para alguns aplicativos, os dados de um consumidor podem estar disponíveis em um feed de dados ou já residir em uma fonte de dados externa, como o armazenamento de BLOBs do Azure. Nesses casos, eles não precisam de leitura e gravação de dados usando entradas e saídas do serviço Web. Em vez disso, eles podem usar o BES (serviço de execução em lote) para ler dados da fonte de dados usando um módulo importar dados e gravar os resultados da pontuação em um local de dados diferente usando um módulo exportar dados.

Os módulos importar dados e exportar dados podem ler e gravar em vários locais de dados, como uma URL da Web por meio de HTTP, uma consulta de Hive, um Azure SQL Database, um armazenamento de tabelas do Azure, um armazenamento de BLOBs do Azure, um feed de dados fornecido ou um SQL Database local.

Este tópico usa o exemplo "exemplo 5: treinar, testar, avaliar para classificação binária: conjunto de conteúdo adulto" e pressupõe que o conjunto de um já tenha sido carregado em uma tabela SQL do Azure denominada CensusData.

## <a name="create-the-training-experiment"></a>Criar o teste de treinamento
Quando você abre o exemplo de "exemplo 5: treinar, testar, avaliar para classificação binária: conjunto de conteúdo adulto", ele usa o conjunto de censo de classificação binária de renda de exemplo de adulto. E o experimento na tela será semelhante à imagem a seguir:

![Configuração inicial do experimento.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Para ler os dados da tabela SQL do Azure:

1. Exclua o módulo DataSet.
2. Na caixa de pesquisa componentes, digite importar.
3. Na lista de resultados, adicione um módulo *importar dados* à tela do experimento.
4. Conecte a saída do módulo *importar dados* a entrada do módulo *limpar dados ausentes* .
5. No painel Propriedades, selecione **banco** de dados SQL do Azure na lista suspensa **fonte de dado** .
6. Nos campos **nome do servidor de banco de dados**, **nome do banco de dados**, **nome de usuário**e **senha** , insira as informações apropriadas para seu banco de dados.
7. No campo consulta de banco de dados, insira a consulta a seguir.

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
     de dbo. CensusData;
8. Na parte inferior da tela do experimento, clique em **executar**.

## <a name="create-the-predictive-experiment"></a>Criar o experimento de previsão
Em seguida, configure o teste de previsão no qual você implanta seu serviço Web.

1. Na parte inferior da tela do experimento, clique em **configurar serviço Web** e selecione **serviço Web de previsão [recomendado]** .
2. Remova os módulos *entrada de serviço Web* e *saída do serviço Web* do teste de previsão.
3. Na caixa de pesquisa componentes, digite exportar.
4. Na lista de resultados, adicione um módulo *exportar dados* à tela do experimento.
5. Conecte a saída do módulo *modelo de Pontuação* à entrada do módulo *exportar dados* .
6. No painel Propriedades, selecione **banco** de dados SQL do Azure no menu suspenso de destino de dado.
7. Nos campos **nome do servidor de banco de dados**, **nome do banco de dados**, nome da **conta de usuário do**servidor e **senha da conta de usuário do servidor** , insira as informações apropriadas para seu banco de dados.
8. No campo **lista separada por vírgulas de colunas a serem salvas** , digite rótulos pontuados.
9. No **campo nome da tabela de dados**, digite dbo. ScoredLabels. Se a tabela não existir, ela será criada quando o experimento for executado ou o serviço Web for chamado.
10. No campo **lista separada por vírgulas de colunas DataTable** , digite ScoredLabels.

Quando você escreve um aplicativo que chama o serviço Web final, talvez queira especificar uma consulta de entrada ou tabela de destino diferente em tempo de execução. Para configurar essas entradas e saídas, use o recurso de parâmetros de serviço Web para definir a propriedade de *fonte de dados* importar módulo de *dados* e a propriedade destino de dados do modo *exportar dados* .  Para obter mais informações sobre parâmetros de serviço Web, consulte a [Azure Machine Learning Studio entrada de parâmetros de serviço Web](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) no Cortana Intelligence e Machine Learning blog.

Para configurar os parâmetros do serviço Web para a consulta de importação e a tabela de destino:

1. No painel Propriedades do módulo *importar dados* , clique no ícone na parte superior direita do campo de **consulta de banco** e selecione **definir como parâmetro de serviço Web**.
2. No painel Propriedades do módulo *exportar dados* , clique no ícone na parte superior direita do campo nome da **tabela de dados** e selecione **definir como parâmetro de serviço Web**.
3. Na parte inferior do painel *Exportar* Propriedades do módulo, na seção **parâmetros do serviço Web** , clique em consulta de banco de dados e renomeie a consulta.
4. Clique em **nome da tabela de dados** e renomeie a **tabela**.

Quando terminar, seu experimento deverá ser semelhante à imagem a seguir:

![A aparência final do experimento.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora você pode implantar o experimento como um serviço Web.

## <a name="deploy-the-web-service"></a>Implementar o serviço web
Você pode implantar o em um serviço Web clássico ou novo.

### <a name="deploy-a-classic-web-service"></a>Implantar um serviço Web clássico
Para implantar como um serviço Web clássico e criar um aplicativo para consumi-lo:

1. Na parte inferior da tela do experimento, clique em executar.
2. Quando a execução for concluída, clique em **implantar serviço Web** e selecione **implantar serviço Web [clássico]** .
3. No painel do serviço Web, localize sua chave de API. Copie e salve-o para usar mais tarde.
4. Na tabela **ponto de extremidade padrão** , clique no link **execução em lote** para abrir a página de ajuda da API.
5. No Visual Studio, crie um C# aplicativo de console: **novo** > **projeto** > **Visual C#**  > **Desktop clássico do Windows** > **aplicativo de console (.NET Framework)** .
6. Na página de ajuda da API, localize a seção de **código de exemplo** na parte inferior da página.
7. Copie e cole o C# código de exemplo em seu arquivo Program.cs e remova todas as referências ao armazenamento de BLOBs.
8. Atualize o valor da variável *apiKey* com a chave de API salva anteriormente.
9. Localize a declaração de solicitação e atualize os valores dos parâmetros de serviço Web que são passados para os módulos *importar dados* e *exportar dados* . Nesse caso, você usa a consulta original, mas define um novo nome de tabela.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Execute a aplicação.

Na conclusão da execução, uma nova tabela é adicionada ao banco de dados que contém os resultados da pontuação.

### <a name="deploy-a-new-web-service"></a>Implantar um novo serviço Web

> [!NOTE]
> Para implantar um novo serviço Web, você deve ter permissões suficientes na assinatura na qual você está implantando o serviço Web. Para obter mais informações, consulte [gerenciar um serviço Web usando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md).

Para implantar como um novo serviço Web e criar um aplicativo para consumi-lo:

1. Na parte inferior da tela do experimento, clique em **executar**.
2. Quando a execução for concluída, clique em **implantar serviço Web** e selecione **implantar serviço Web [novo]** .
3. Na página implantar experimento, insira um nome para o serviço Web e selecione um plano de preços e clique em **implantar**.
4. Na página **início rápido** , clique em **consumir**.
5. Na seção **código de exemplo** , clique em **lote**.
6. No Visual Studio, crie um C# aplicativo de console: **novo** > **projeto** > **Visual C#**  > **Desktop clássico do Windows** > **aplicativo de console (.NET Framework)** .
7. Copie e cole o C# código de exemplo em seu arquivo Program.cs.
8. Atualize o valor da variável *apiKey* com a **chave primária** localizada na seção **informações básicas de consumo** .
9. Localize a Declaração *scoreRequest* e atualize os valores dos parâmetros de serviço Web que são passados para os módulos *importar dados* e *exportar dados* . Nesse caso, você usa a consulta original, mas define um novo nome de tabela.

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

