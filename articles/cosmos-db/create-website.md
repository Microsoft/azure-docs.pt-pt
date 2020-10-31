---
title: Implementar uma aplicação web com um modelo - Azure Cosmos DB
description: Saiba como implementar uma conta Azure Cosmos, aplicações web do Azure App Service e uma aplicação web de amostras usando um modelo de Gestor de Recursos Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: c206c89bf8e9abae219ce863a8b08f4b0e7041c3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089921"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Implementar O Serviço de Aplicações Azure Cosmos DB e Azure app com uma aplicação web do GitHub usando um modelo de gestor de recursos Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial mostra-lhe como fazer uma implementação "sem toque" de uma aplicação web que se conecta ao Azure Cosmos DB na primeira execução sem ter que cortar e colar qualquer informação de conexão de Azure Cosmos DB para `appsettings.json` ou para as definições de aplicação Azure App Services no portal Azure. Todas estas ações são realizadas usando um modelo de Gestor de Recursos Azure numa única operação. No exemplo aqui, implementaremos a [amostra Azure Cosmos DB ToDo](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) a partir de um tutorial de [aplicações web.](sql-api-dotnet-application.md)

Os modelos de Gestor de Recursos são bastante flexíveis e permitem-lhe compor implementações complexas em qualquer serviço em Azure. Isto inclui tarefas avançadas como a implementação de aplicações do GitHub e a injeção de informações de ligação nas definições de aplicação do Azure App Service no portal Azure. Este tutorial irá mostrar-lhe como fazer as seguintes coisas usando um único modelo de Gestor de Recursos.

* Implemente uma conta Azure Cosmos.
* Implementar um plano de hospedagem de serviço de aplicações Azure.
* Implementar um Serviço de Aplicações Azure.
* Injetar o ponto final e as chaves da conta Azure Cosmos nas definições de aplicação do Serviço de Aplicações no portal Azure.
* Implemente uma aplicação web de um repositório GitHub para o Serviço de Aplicações.

A implementação resultante tem uma aplicação web totalmente funcional que pode ligar-se ao Azure Cosmos DB sem ter que cortar e colar o URL de ponta do Azure Cosmos DB ou chaves de autenticação do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

> [!TIP]
> Embora este tutorial não assuma experiência prévia com modelos de Gestor de Recursos Azure ou JSON, caso deseje modificar os modelos ou opções de implementação referenciados, então é necessário saber de cada uma dessas áreas.

## <a name="step-1-deploy-the-template"></a>Passo 1: Implementar o modelo

Em primeiro lugar, selecione o botão **Implementar para Azure** abaixo para abrir o portal Azure para criar uma implementação personalizada. Também pode ver o modelo de Gestão de Recursos Azure da [Galeria de Modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Uma vez no portal Azure, selecione a subscrição para implementar e selecionar ou criar um novo grupo de recursos. Em seguida, preencha os seguintes valores.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Implementar no Azure":::

* **Região** - Isto é exigido pelo Gestor de Recursos. Insira a mesma região utilizada pelo parâmetro de localização onde os seus recursos estão localizados.
* **Nome da aplicação** - Este nome é utilizado por todos os recursos para esta implantação. Certifique-se de escolher um nome único para evitar conflitos com as contas existentes do Azure Cosmos DB e do App Service.
* **Localização** - A região onde os seus recursos são mobilizados.
* **Nível de preços** do Plano de Serviço de Aplicações - Nível de preços do App Service Plan.
* **Instâncias do Plano de Aplicação** - O número de trabalhadores para o plano de serviço de aplicações.
* **URL repositório** - O repositório da aplicação web no GitHub.
* **Filial** - O ramo do repositório GitHub.
* **Nome da base de dados** - O nome da base de dados Azure Cosmos.
* **Nome do recipiente** - O nome do recipiente Azure Cosmos.

Depois de preencher os valores, selecione o botão **Criar** para iniciar a implementação. Este passo deve demorar entre 5 a 10 minutos para ser concluído.

> [!TIP]
> O modelo não valida que o nome do Serviço de Aplicações Azure e o nome da conta Azure Cosmos introduzidos no modelo são válidos e disponíveis. É altamente recomendável verificar a disponibilidade dos nomes que pretende fornecer antes de submeter a implementação.


## <a name="step-2-explore-the-resources"></a>Passo 2: Explorar os recursos

### <a name="view-the-deployed-resources"></a>Ver os recursos implantados

Depois de o modelo ter implantado os recursos, agora pode ver cada um deles no seu grupo de recursos.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Implementar no Azure":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Ver ponto final e teclas Cosmos DB

Em seguida, abra a conta Azure Cosmos no portal. A imagem que se segue mostra o ponto final e as teclas de uma conta Azure Cosmos.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Implementar no Azure":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Veja as teclas DB do Azure Cosmos nas definições de aplicação

Em seguida, navegue para o Azure App Service no grupo de recursos. Clique no separador Configuração para ver as Definições de Aplicação para o Serviço de Aplicações. As Definições de Aplicação contêm a conta DB cosmos e os valores-chave primários necessários para ligar ao Cosmos DB, bem como os nomes da base de dados e dos contentores que foram transmitidos a partir da implementação do modelo.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Implementar no Azure":::

### <a name="view-web-app-in-deployment-center"></a>Ver aplicativo web no Centro de Implementação

Em seguida, vá ao Centro de Implementação do Serviço de Aplicações. Aqui você verá pontos de repositório para o repositório GitHub passado para o modelo. Também o Estado abaixo indica Sucesso (Ative), o que significa que a aplicação foi implementada e iniciada com sucesso.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Implementar no Azure":::

### <a name="run-the-web-application"></a>Execute a aplicação Web

Clique em **navegar** no topo do Centro de Implementação para abrir a aplicação web. A aplicação web abrir-se-á para o ecrã principal. Clique em **Criar Novo** e introduza alguns dados nos campos e clique em Guardar. O ecrã resultante mostra os dados guardados para a Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Implementar no Azure":::

## <a name="step-3-how-does-it-work"></a>Passo 3: Como funciona

Há três elementos necessários para que isto funcione.

### <a name="reading-app-settings-at-runtime"></a>Definições de aplicativo de leitura no tempo de execução

Em primeiro lugar, a aplicação precisa de solicitar o ponto final do Cosmos DB e a chave `Startup` na classe na aplicação web ASP.NET MVC. A [amostra cosmos DB To Do sample](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) pode ser executada localmente onde pode introduzir a informação de ligação em appsettings.js. No entanto, quando implementado, este ficheiro implementa-se com a app. Se estas linhas a vermelho não puderem aceder às definições a partir de appsettings.js, tentará a partir de Configurações de Aplicações no Serviço de Aplicações Azure.

:::image type="content" source="./media/create-website/startup.png" alt-text="Implementar no Azure":::

### <a name="using-special-azure-resource-management-functions"></a>Utilizando funções especiais de Gestão de Recursos Azure

Para que estes valores estejam disponíveis para a aplicação quando implementados, o modelo Azure Resource Manager pode solicitar esses valores da conta DB do Cosmos utilizando funções especiais de Gestão de Recursos Azure, incluindo [referência](../azure-resource-manager/templates/template-functions-resource.md#reference) e [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys) que agarram os valores da conta DB do Cosmos e os inserem nos valores de definições de aplicação com nomes-chave que correspondam ao que é usado na aplicação acima num formato '{seção:key}'. Por exemplo, `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Implementar no Azure":::

### <a name="deploying-web-apps-from-github"></a>Implementação de aplicativos web a partir do GitHub

Por último, precisamos de implementar a aplicação web do GitHub para o Serviço de Aplicações. Isto é feito usando o JSON abaixo. Duas coisas a ter cuidado são o tipo e o nome deste recurso. Tanto os valores como os `"type": "sourcecontrols"` `"name": "web"` valores de propriedade são codificados e não devem ser alterados.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Implementar no Azure":::

## <a name="next-steps"></a>Passos seguintes

Parabéns! Você implementou Azure Cosmos DB, Azure App Service, e uma aplicação web de amostra que tem automaticamente a informação de conexão necessária para ligar à Cosmos DB, tudo numa única operação e sem ter que cortar e colar informações sensíveis. Usando este modelo como ponto de partida, pode modificá-lo para implementar as suas próprias aplicações web da mesma forma.

* Para o modelo de gestor de recursos Azure para esta amostra vá para [Azure Quickstart Templates Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* Para o código-fonte da aplicação da amostra, aceda à [Aplicação Cosmos DB To Do no GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
