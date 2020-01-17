---
title: Enviar eventos de armazenamento de BLOBs para o ponto de extremidade da Web-modelo
description: Use a grade de eventos do Azure e um modelo de Azure Resource Manager para criar uma conta de armazenamento de BLOBs e assinar seus eventos. Enviar os eventos para um webhook. '
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 01/15/2020
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 73f0e6cf5d7ebb3ae36d4048ce5f36f5c0547286
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122867"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Rotear eventos de armazenamento de BLOBs para o ponto de extremidade da Web usando Azure Resource Manager modelo
O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, você usa um **modelo de Azure Resource Manager** para criar uma conta de armazenamento de BLOBs, assinar eventos para esse armazenamento de BLOBs e disparar um evento para exibir o resultado. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

O [modelo do Resource Manager](../azure-resource-manager/templates/overview.md) é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite que você declare o que pretende implantar sem precisar escrever a sequência de comandos de programação para criá-lo. Se você quiser saber mais sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem
Antes de subscrever aos eventos do armazenamento de Blobs, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

1. Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

    [Implementar no Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

1. Vê o site, mas ainda não foram publicados eventos no mesmo.

   ![Ver novo site](./media/blob-event-quickstart-portal/view-site.png)


## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Criar uma conta de armazenamento com uma assinatura da grade de eventos
O modelo usado neste guia de início rápido é de [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage).

[!code-json[<Azure Resource Manager template create Blob strage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

Dois recursos do Azure são definidos no modelo:

* **Microsoft. Storage/storageAccounts**: Crie uma conta de armazenamento do Azure.
* **"Microsoft. Storage/storageAccounts/Providers/eventSubscriptions**: criar uma assinatura da grade de eventos do Azure para a conta de armazenamento. 

1. Selecione o link a seguir para entrar no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    [Implementar no Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)
2. Especifique o **ponto de extremidade**: forneça a URL do seu aplicativo Web e adicione `api/updates` à url de Home Page.
3. Selecione **comprar** para implantar o modelo. 

  O portal do Azure é usado aqui para implantar o modelo. Você também pode usar o Azure PowerShell, CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, consulte [implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> Você pode encontrar mais exemplos de modelo de grade de eventos do Azure [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).

## <a name="validate-the-deployment"></a>Validar a implementação
Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

![Ver evento da subscrição](./media/blob-event-quickstart-portal/view-subscription-event.png)

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final.

Aciona um evento para o armazenamento de Blobs ao carregar um ficheiro. O ficheiro não precisa de qualquer conteúdo específico. Os artigos partem do princípio de que tem um ficheiro denominado testfile.txt, mas pode utilizar qualquer ficheiro.

Quando você carrega o arquivo para o armazenamento de BLOBs do Azure, a grade de eventos envia uma mensagem para o ponto de extremidade que você configurou ao assinar. A mensagem está no formato JSON e contém uma matriz com um ou mais eventos. No exemplo a seguir, a mensagem JSON contém uma matriz com um evento. Veja a sua aplicação Web e repare que foi recebido um evento criado por um blob. 

![Ver resultados](./media/blob-event-quickstart-portal/view-results.png)



## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, [exclua o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
). 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre modelos de Azure Resource Manager, consulte os seguintes artigos:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager)
- [Definir recursos em modelos de Azure Resource Manager](/azure/templates/)
- [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)
- [Modelos de grade de eventos do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
