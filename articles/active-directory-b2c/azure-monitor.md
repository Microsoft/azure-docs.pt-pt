---
title: Monitor Azure AD B2C com Monitor Azure
titleSuffix: Azure AD B2C
description: Saiba como registar eventos Azure AD B2C com o Azure Monitor utilizando a gestão de recursos delegada.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 3106e5a640ed66828558078e6986979ad7195450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386220"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitor Azure AD B2C com Monitor Azure

Utilize o Azure Monitor para encaminhar o Azure Ative Directory B2C (Azure AD B2C) para iniciar sessão de registos e [auditorias](view-audit-logs.md) para diferentes soluções de monitorização. Pode reter os registos para uso a longo prazo ou integrar-se com ferramentas de segurança de terceiros e gestão de eventos (SIEM) para obter informações sobre o seu ambiente.

Pode encaminhar eventos de registo para:

* Uma conta [de armazenamento](../storage/blobs/storage-blobs-introduction.md)Azure.
* Um [espaço de trabalho Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (para analisar dados, criar dashboards e alertar para eventos específicos).
* Um hub [de eventos](../event-hubs/event-hubs-about.md) Azure (e integrar-se com as suas instâncias Splunk e Sumo Logic).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, implementa um modelo de Gestor de Recursos Azure utilizando o módulo Azure PowerShell.

* [Azure PowerShell módulo](https://docs.microsoft.com/powershell/azure/install-az-ps) versão 6.13.1 ou superior

Também pode utilizar o [Azure Cloud Shell](https://shell.azure.com), que inclui a versão mais recente do módulo Azure PowerShell.

## <a name="delegated-resource-management"></a>Gestão delegada de recursos

Azure AD B2C aproveita [a monitorização do Azure Ative Directory](../active-directory/reports-monitoring/overview-monitoring.md). Para ativar *as definições de Diagnóstico* no Azure Ative Directory dentro do seu inquilino Azure AD B2C, utiliza a [gestão de recursos delegada.](../lighthouse/concepts/azure-delegated-resource-management.md)

Você autoriza um utilizador ou grupo no seu diretório Azure AD B2C (o **Fornecedor de Serviços)** para configurar a instância do Azure Monitor dentro do inquilino que contém a sua assinatura Azure (o **Cliente).** Para criar a autorização, você implementa um modelo [de Gestor de Recursos Azure](../azure-resource-manager/index.yml) para o seu inquilino AZure AD contendo a subscrição. As seguintes secções acompanham-no através do processo.

## <a name="create-or-choose-resource-group"></a>Criar ou escolher grupo de recursos

Este é o grupo de recursos que contém a conta de armazenamento de destino Azure, centro de eventos ou espaço de trabalho Log Analytics para receber dados do Azure Monitor. Especifica o nome do grupo de recursos quando implementar o modelo Azure Resource Manager.

[Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ou escolha um existente no inquilino Azure Ative Directory (Azure AD) que contenha a sua assinatura Azure, e *não* o diretório que contém o seu inquilino Azure AD B2C.

Este exemplo utiliza um grupo de recursos chamado *azure-ad-b2c-monitor* na região *central dos EUA.*

## <a name="delegate-resource-management"></a>Gestão de recursos delegados

Em seguida, reúna as seguintes informações:

**ID do diretório** do seu diretório Azure AD B2C (também conhecido como ID do inquilino).

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como utilizador com a função *de administrador do Utilizador* (ou superior).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Selecione **Azure Ative Directory**, selecione **Properties**.
1. Grave a identificação do **Diretório.**

**ID** de objeto do grupo Azure AD B2C ou utilizador que pretende dar permissão ao *Contribuinte* para o grupo de recursos que criou anteriormente no diretório que contém a sua subscrição.

Para facilitar a gestão, recomendamos a utilização de *grupos* de utilizadores AZure AD para cada função, permitindo-lhe adicionar ou remover utilizadores individuais ao grupo em vez de atribuir permissões diretamente a esse utilizador. Nesta passagem, adicione um utilizador.

1. Com **o Azure Ative Directory** ainda selecionado no portal Azure, selecione **Utilizadores**e, em seguida, selecione um utilizador.
1. Grave o **ID**do objeto do utilizador.

### <a name="create-an-azure-resource-manager-template"></a>Crie um modelo de gestor de recursos Azure

Para embarcar no seu inquilino Azure AD (o **Cliente),** crie um [modelo de Gestor de Recursos Azure](../lighthouse/how-to/onboard-customer.md) para a sua oferta com as seguintes informações. Os `mspOfferName` `mspOfferDescription` valores e valores são visíveis quando vê detalhes da oferta na página de [prestadores](../lighthouse/how-to/view-manage-service-providers.md) de serviços do portal Azure.

| Campo   | Definição |
|---------|------------|
| `mspOfferName`                     | Um nome descrevendo esta definição. Por exemplo, *Serviços Geridos Azure AD B2C*. Este valor é apresentado ao cliente como título da oferta. |
| `mspOfferDescription`              | Uma breve descrição da sua oferta. Por exemplo, *Ativa o Monitor Azure em Azure AD B2C*.|
| `rgName`                           | O nome do grupo de recursos que cria anteriormente no seu inquilino AZure AD. Por exemplo, *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | O **Diretório ID** do seu inquilino Azure AD B2C (também conhecido como iD do inquilino). |
| `authorizations.value.principalId` | O **ID** do Objeto do grupo B2C ou utilizador que terá acesso a recursos nesta subscrição do Azure. Para esta passagem, especifique o ID do objeto do utilizador que gravou anteriormente. |

Descarregue o modelo e os ficheiros de parâmetros do Azure Resource Manager:

- [rgDelegatedResourceManagement.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.js](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Em seguida, atualize o ficheiro de parâmetros com os valores que gravou anteriormente. O seguinte snippet JSON mostra um exemplo de um ficheiro de parâmetros do Azure Resource Manager. Para `authorizations.value.roleDefinitionId` , utilizar o valor de [função incorporado](../role-based-access-control/built-in-roles.md) para o papel de *Contribuinte,* `b24988ac-6180-42a0-ab88-20f7382dd24c` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Implementar os modelos do Gestor de Recursos Azure

Uma vez atualizado o seu ficheiro de parâmetros, coloque o modelo de Gestor de Recursos Azure no inquilino Azure como uma implementação de nível de subscrição. Por se trata de uma implementação de nível de subscrição, não pode ser iniciada no portal Azure. Pode ser implantado utilizando o módulo Azure PowerShell ou o Azure CLI. O método Azure PowerShell é mostrado abaixo.

Inscreva-se no diretório que contém a sua subscrição utilizando [o Connect-AzAccount](/powershell/azure/authenticate-azureps). Utilize a `-tenant` bandeira para forçar a autenticação ao diretório correto.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Utilize o [cmdlet Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) para listar as subscrições a que a conta corrente pode aceder sob o inquilino Azure AD. Grave o ID da subscrição que pretende projetar para o seu inquilino Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Em seguida, mude para a subscrição que pretende projetar para o inquilino Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Por fim, implemente o modelo do Gestor de Recursos Azure e os ficheiros de parâmetros que descarregou e atualizou anteriormente. Substitua o `Location` `TemplateFile` , e os `TemplateParameterFile` valores em conformidade.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

A implantação bem sucedida do modelo produz uma saída semelhante à seguinte (saída truncada para a brevidade):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Depois de implementar o modelo, pode levar alguns minutos para a projeção do recurso ser concluída. Pode ter de esperar alguns minutos (normalmente não mais do que cinco) antes de passar para a secção seguinte para selecionar a subscrição.

## <a name="select-your-subscription"></a>Selecione a sua subscrição

Depois de ter implementado o modelo e ter esperado alguns minutos para que a projeção do recurso esteja concluída, associe a sua subscrição ao seu diretório Azure AD B2C com os seguintes passos.

1. **Assine fora** do portal Azure se estiver a assinar. Este e o seguinte passo são feitos para refrescar as suas credenciais na sessão do portal.
1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta administrativa Azure AD B2C.
1. Selecione o ícone **de inscrição + Diretório** na barra de ferramentas do portal.
1. Selecione o diretório que contém a sua subscrição.

    ![Trocar diretório](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Verifique se selecionou o diretório e subscrição corretos. Neste exemplo, todos os diretórios e subscrições são selecionados.

    ![Todos os diretórios selecionados no filtro de subscrição de & do Diretório](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Configurar configurações de diagnóstico

As definições de diagnóstico definem onde devem ser enviados registos e métricas de um recurso. Os destinos possíveis são:

- [Conta de armazenamento Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Soluções de centros](../azure-monitor/platform/resource-logs-stream-event-hubs.md) de eventos.
- [Log Analytics espaço de trabalho](../azure-monitor/platform/resource-logs-collect-workspace.md)

Se ainda não o fez, crie uma instância do seu tipo de destino escolhido no grupo de recursos especificado no [modelo Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Criar definições de diagnóstico

Está pronto para [criar definições de diagnóstico](../active-directory/reports-monitoring/overview-monitoring.md) no portal Azure.

Para configurar as definições de monitorização dos registos de atividade Azure AD B2C:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Selecione **Azure Ative Directory**
1. Em **Monitorização**, selecione **Definições de diagnóstico**.
1. Se houver definições existentes no recurso, verá uma lista de definições já configuradas. **Selecione Adicionar a definição de diagnóstico** para adicionar uma nova definição ou **editar** a definição existente. Cada definição não pode ter mais do que um dos tipos de destino..

    ![Painel de definições de diagnóstico no portal Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dê um nome se já não tiver um.
1. Verifique a caixa para cada destino para enviar os registos. Selecione **Configurar** para especificar as suas definições conforme descrito no quadro seguinte.

    | Definições | Descrição |
    |:---|:---|
    | Arquivar numa conta de armazenamento | Nome da conta de armazenamento. |
    | Transmitir em fluxo para um hub de eventos | O espaço de nome onde o centro de eventos é criado (se esta for a sua primeira vez de registos de streaming) ou transmitido para (se já existem recursos que estão a transmitir essa categoria de registo para este espaço de nome).
    | Enviar para o Log Analytics | Nome do espaço de trabalho. |

1. Selecione **AuditLogs** e **SignInLogs**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a adição e configuração das definições de diagnóstico no Azure Monitor, consulte [Tutorial: Colete e analise os registos de recursos a partir de um recurso Azure](../azure-monitor/insights/monitor-azure-resource.md).

Para obter informações sobre o streaming de registos Azure AD para um centro de eventos, consulte [Tutorial: Stream Azure Ative Directory para um centro de eventos Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
