---
author: sdwheeler
ms.service: azure-powershell
ms.topic: include
ms.date: 11/09/2018
ms.author: sewhee
ms.openlocfilehash: c5440555c11d98fb89f8594eec1d4b7e74ea8667
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57891380"
---
## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configurar o PowerShell para modelos do Resource Manager
Antes de poder utilizar o Azure PowerShell com o Resource Manager, terá de ter o direito de Windows PowerShell e versões do Azure PowerShell.

### <a name="verify-powershell-versions"></a>Verifique se as versões do PowerShell
Certifique-se de que tem o Windows PowerShell versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, escreva este comando num prompt de comando do Windows PowerShell.

    $PSVersionTable

Receberá os tipos de informações seguintes:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Certifique-se de que o valor de **PSVersion** é a 3.0 ou 4.0. Caso contrário, veja [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Definir a conta e a subscrição do Azure
Se ainda não tiver uma subscrição do Azure, pode ativar sua [benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se um [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra uma linha de comandos do Azure PowerShell e inicie sessão no Azure com este comando.

    Connect-AzureRmAccount

Se tiver várias subscrições do Azure, pode listar as suas subscrições do Azure com este comando.

    Get-AzureRmSubscription

Receberá os tipos de informações seguintes:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Pode definir a subscrição do Azure atual, executando estes comandos na linha de comandos do Azure PowerShell. Substituir tudo dentro das aspas, incluindo os < e > carateres, com o nome correto.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Para obter mais informações sobre contas e subscrições do Azure, consulte [como: Ligar à sua subscrição](/powershell/azureps-cmdlets-docs#step-3-connect).
