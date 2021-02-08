---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820012"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)

## <a name="create-azure-communication-resource"></a>Criar recurso de comunicação Azure

Para criar um recurso Azure Communication Services, [inscreva-se no Azure CLI,](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)em seguida, executar o seguinte comando:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Pode configurar o seu recurso de Serviços de Comunicação com as seguintes opções:

* O grupo de recursos
* O nome do recurso serviços de comunicação
* A geografia com que o recurso será associado

No passo seguinte, pode atribuir etiquetas ao recurso. As etiquetas podem ser usadas para organizar os seus recursos Azure. Consulte a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre etiquetas.

## <a name="manage-your-communication-services-resource"></a>Gerir o seu recurso de Serviços de Comunicação

Para adicionar tags ao seu recurso serviços de comunicação, execute os seguintes comandos:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Para obter informações sobre comandos adicionais, consulte [a comunicação az](https://docs.microsoft.com/cli/azure/ext/communication/communication).