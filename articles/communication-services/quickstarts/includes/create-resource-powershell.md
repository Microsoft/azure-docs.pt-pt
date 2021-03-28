---
ms.openlocfilehash: cc628b1f1fcae5e837f7f61db584c8747100f353
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644751"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- Instale o [Módulo Azure Az PowerShell](https://docs.microsoft.com/powershell/azure/)

## <a name="create-azure-communication-resource"></a>Criar recurso de comunicação Azure

Para criar um recurso Azure Communication Services, [inscreva-se no Azure CLI](/cli/azure/authenticate-azure-cli). Pode fazê-lo através do terminal usando o ```Connect-AzAccount``` comando e fornecendo as suas credenciais. Executar o seguinte comando para criar o recurso:

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

Se quiser selecionar uma subscrição específica, também pode especificar a ```--subscription``` bandeira e fornecer o ID de subscrição.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

Pode configurar o seu recurso de Serviços de Comunicação com as seguintes opções:

* O grupo de recursos
* O nome do recurso serviços de comunicação
* A geografia com que o recurso será associado

No passo seguinte, pode atribuir etiquetas ao recurso. As etiquetas podem ser usadas para organizar os seus recursos Azure. Consulte a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre etiquetas.

## <a name="manage-your-communication-services-resource"></a>Gerir o seu recurso de Serviços de Comunicação

Para adicionar tags ao seu recurso serviços de comunicação, execute os seguintes comandos. Também pode direcionar uma subscrição específica.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

Para listar todos os seus Recursos de Serviços de Comunicação Azure numa determinada subscrição, utilize o seguinte comando:

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

Para listar todas as informações num dado recurso, utilize o seguinte comando:

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
