---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 22a9cf3338f422341928a77f2bf14c497aa2ba31
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563786"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- Instalar [O Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-resource"></a>Criar recurso de comunicação Azure

Para criar um recurso Azure Communication Services, [inscreva-se no Azure CLI](/cli/azure/authenticate-azure-cli). Pode fazê-lo através do terminal usando o ```az login``` comando e fornecendo as suas credenciais. Executar o seguinte comando para criar o recurso:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Se quiser selecionar uma subscrição específica, também pode especificar a ```--subscription``` bandeira e fornecer o ID de subscrição.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

Pode configurar o seu recurso de Serviços de Comunicação com as seguintes opções:

* O grupo de recursos
* O nome do recurso serviços de comunicação
* A geografia com que o recurso será associado

No passo seguinte, pode atribuir etiquetas ao recurso. As etiquetas podem ser usadas para organizar os seus recursos Azure. Consulte a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre etiquetas.

## <a name="manage-your-communication-services-resource"></a>Gerir o seu recurso de Serviços de Comunicação

Para adicionar tags ao seu recurso serviços de comunicação, execute os seguintes comandos. Também pode direcionar uma subscrição específica.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Para obter informações sobre comandos adicionais, consulte [a comunicação az](/cli/azure/ext/communication/communication).
