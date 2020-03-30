---
title: Envie eventos de armazenamento blob para web endpoint - modelo
description: Utilize a Grelha de Eventos Azure e um modelo de Gestor de Recursos Azure para criar uma conta de armazenamento Blob e subscreva os seus eventos. Envie os eventos para um Webhook.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/27/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: subject-armqs
ms.openlocfilehash: 343ed57c87ea6df5db4cde0978132af31419f905
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78303346"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Route Blob eventos de armazenamento para ponto final web usando o modelo de Gestor de Recursos Azure

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, você usa um modelo de Gestor de **Recursos Azure** para criar uma conta de armazenamento Blob, subscrever eventos para esse armazenamento blob, e desencadear um evento para ver o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever aos eventos do armazenamento de Blobs, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

    [Desdobre para Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)

## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Criar uma conta de armazenamento com uma subscrição da Grelha de Eventos

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage).

[!code-json[<Azure Resource Manager template create Blob storage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

Dois recursos Azure são definidos no modelo:

* [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): crie uma conta de Armazenamento Azure.
* [**"Microsoft.Storage/storageAccounts/providers/eventSubscriptions**](/azure/templates/microsoft.eventgrid/eventsubscriptions): crie uma subscrição da Rede de Eventos Azure para a conta de armazenamento.

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o seguinte link para iniciar sessão no Azure e abra um modelo. O modelo cria um cofre chave e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json"><img src="./media/blob-event-quickstart-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Especifique o **ponto final**: forneça `api/updates` o URL da sua aplicação web e adicione ao URL da página inicial.
3. **Selecione Comprar** para implementar o modelo.

  O portal Azure é usado aqui para implementar o modelo. Também pode utilizar a API Azure PowerShell, Azure CLI e REST. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> Você pode encontrar mais amostras de modelo de grelha de evento sinuosa [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).

## <a name="validate-the-deployment"></a>Validar a implementação

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

![Ver evento da subscrição](./media/blob-event-quickstart-portal/view-subscription-event.png)

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final.

Aciona um evento para o armazenamento de Blobs ao carregar um ficheiro. O ficheiro não precisa de qualquer conteúdo específico. Os artigos partem do princípio de que tem um ficheiro denominado testfile.txt, mas pode utilizar qualquer ficheiro.

Ao fazer o upload do ficheiro para o armazenamento do Blob Azure, a Rede de Eventos envia uma mensagem para o ponto final configurado ao subscrever. A mensagem está no formato JSON e contém uma matriz com um ou mais eventos. No exemplo seguinte, a mensagem JSON contém uma matriz com um evento. Veja a sua aplicação Web e repare que foi recebido um evento criado por um blob.

![Ver resultados](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, [elimine o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os modelos do Gestor de Recursos Azure, consulte os seguintes artigos:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager)
* [Defina recursos nos modelos do Gestor de Recursos Azure](/azure/templates/)
* [Modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/)
* [Modelos de grelha de eventos azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
