---
title: Monitor Azure AD B2C com Monitor Azure
titleSuffix: Azure AD B2C
description: Saiba como registar eventos Azure AD B2C com o Azure Monitor utilizando a gestão de recursos delegada.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 6f7f0252a6377397ccaccdc44c9c8561da7c9d29
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121376"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitor Azure AD B2C com Monitor Azure

Utilize o Monitor Azure para encaminhar o Diretório Ativo Azure B2C (Azure AD B2C) e os registos de [auditoria](view-audit-logs.md) a diferentes soluções de monitorização. Pode reter os registos para uso a longo prazo ou integrar-se com ferramentas de informação de segurança de terceiros e gestão de eventos (SIEM) para obter informações sobre o seu ambiente.

Pode encaminhar eventos de registo para:

* Uma [conta de armazenamento](../storage/blobs/storage-blobs-introduction.md)Azure.
* Um hub de [eventos](../event-hubs/event-hubs-about.md) Azure (e integrar-se com as suas instâncias Splunk e Sumo Logic).
* Um espaço de [trabalho de Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (para analisar dados, criar dashboards e alertar sobre eventos específicos).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, implementa um modelo de Gestor de Recursos Azure utilizando o módulo PowerShell Azure.

* Versão do [módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 6.13.1 ou superior

Também pode utilizar o [Azure Cloud Shell,](https://shell.azure.com)que inclui a versão mais recente do módulo PowerShell Azure.

## <a name="delegated-resource-management"></a>Gestão de recursos delegados

Azure AD B2C alavanca [a monitorização do Diretório Ativo Azure](../active-directory/reports-monitoring/overview-monitoring.md). Para ativar *as definições* de Diagnóstico no Diretório Ativo Azure dentro do seu inquilino Azure AD B2C, utiliza a [gestão de recursos delegada.](../lighthouse/concepts/azure-delegated-resource-management.md)

Autoriza um utilizador ou grupo no seu diretório Azure AD B2C (o Prestador de **Serviços)** a configurar a instância Do Monitor Azure dentro do inquilino que contém a sua assinatura Azure (o **Cliente).** Para criar a autorização, você implementa um modelo de Gestor de [Recursos Azure](../azure-resource-manager/index.yml) para o seu inquilino Azure AD contendo a subscrição. As seguintes secções percorrem-no durante o processo.

## <a name="create-or-choose-resource-group"></a>Criar ou escolher grupo de recursos

Este é o grupo de recursos que contém a conta de armazenamento do destino Azure, o centro de eventos ou o espaço de trabalho log Analytics para receber dados do Azure Monitor. Especifica o nome do grupo de recursos quando implementa o modelo do Gestor de Recursos Azure.

[Crie um grupo](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) de recursos ou escolha um existente no inquilino Azure Ative Directory (Azure AD) que contenha a sua assinatura Azure, *e não* o diretório que contém o seu inquilino Azure AD B2C.

Este exemplo utiliza um grupo de recursos chamado *azure-ad-b2c-monitor* na região *centro dos EUA.*

## <a name="delegate-resource-management"></a>Gestão de recursos delegados

Em seguida, recolher as seguintes informações:

ID do **diretório** do seu diretório Azure AD B2C (também conhecido como ID do inquilino).

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como utilizador com a função de administrador do *Utilizador* (ou superior).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. Selecione **Diretório Ativo Azure,** selecione **Propriedades**.
1. Grave o ID do **Diretório.**

**Id** de objeto do grupo Ou utilizador Azure AD AD que pretende dar permissão ao *Colaborador* para o grupo de recursos que criou anteriormente no diretório que contém a sua subscrição.

Para facilitar a gestão, recomendamos a utilização de *grupos* de utilizadores Da Azure AD para cada função, permitindo-lhe adicionar ou remover utilizadores individuais ao grupo em vez de atribuir permissões diretamente a esse utilizador. Nesta passagem, adicione um utilizador.

1. Com o **Diretório Ativo Azure** ainda selecionado no portal Azure, selecione **Utilizadores**e, em seguida, selecione um utilizador.
1. Grave o ID do **objeto**do utilizador .

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager

Para embarcar no seu inquilino Azure AD (o **Cliente),** crie um modelo de Gestor de [Recursos Azure](../lighthouse/how-to/onboard-customer.md) para a sua oferta com as seguintes informações. Os valores `mspOfferName` e `mspOfferDescription` são visíveis quando vê a oferta de detalhes na página de fornecedores de [serviços](../lighthouse/how-to/view-manage-service-providers.md) do portal Azure.

| Campo   | Definição |
|---------|------------|
| `mspOfferName`                     | Um nome que descreve esta definição. Por exemplo, *Serviços Geridos Azure AD B2C*. Este valor é apresentado ao cliente como título da oferta. |
| `mspOfferDescription`              | Uma breve descrição da sua oferta. Por exemplo, ativa o *Monitor Azure em Azure AD B2C*.|
| `rgName`                           | O nome do grupo de recursos que cria mais cedo no seu inquilino Azure AD. Por exemplo, *monitor azure-ad-b2c*. |
| `managedByTenantId`                | A ID do **Diretório** do seu inquilino Azure AD B2C (também conhecido como id do inquilino). |
| `authorizations.value.principalId` | O Id do **Objeto** do grupo B2C ou utilizador que terá acesso aos recursos nesta subscrição do Azure. Para esta passagem, especifique o ID do objeto do utilizador que gravou anteriormente. |

Descarregue o modelo de gestor de recursos azure e os ficheiros de parâmetros:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Em seguida, atualize o ficheiro de parâmetros com os valores que registou anteriormente. O seguinte snippet JSON mostra um exemplo de um ficheiro de parâmetros de modelo do Gestor de Recursos Azure. Para `authorizations.value.roleDefinitionId`, utilize o valor de [papel incorporado](../role-based-access-control/built-in-roles.md) para o papel *de Contribuinte*, `b24988ac-6180-42a0-ab88-20f7382dd24c`.

```JSON
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

Assim que atualizar o ficheiro de parâmetros, implemente o modelo do Gestor de Recursos Azure no inquilino Azure como uma implementação de nível de subscrição. Como se trata de uma implantação ao nível da subscrição, não pode ser iniciada no portal Azure. Pode ser implantado utilizando o módulo Azure PowerShell ou o Azure CLI. O método Azure PowerShell é mostrado abaixo.

Inscreva-se no diretório que contém a sua subscrição utilizando o [Connect-AzAccount](/powershell/azure/authenticate-azureps). Utilize a bandeira `-tenant` para forçar a autenticação ao diretório correto.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Utilize o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) para listar as subscrições a que a conta corrente pode aceder ao abrigo do inquilino Azure AD. Grave a identificação da subscrição que pretende projetar no seu inquilino Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Em seguida, mude para a subscrição que pretende projetar para o inquilino Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Por fim, implemente o modelo de gestor de recursos azure e os ficheiros de parâmetros que descarregou e atualizou anteriormente. Substitua os valores `Location`, `TemplateFile`e `TemplateParameterFile` em conformidade.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

A implantação bem sucedida do modelo produz uma saída semelhante à seguinte (saída truncada para brevidade):

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

Depois de implementar o modelo, pode levar alguns minutos para que a projeção do recurso esteja concluída. Pode ter de esperar alguns minutos (normalmente não mais do que cinco) antes de passar para a secção seguinte para selecionar a subscrição.

## <a name="select-your-subscription"></a>Selecione a sua subscrição

Uma vez implementado o modelo e tenha esperado alguns minutos para que a projeção do recurso esteja concluída, associe a sua subscrição ao seu diretório Azure AD B2C com os seguintes passos.

1. **Assine pelo** portal Azure se estiver inscrito. Este e o passo seguinte são feitos para refrescar as suas credenciais na sessão do portal.
1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta administrativa Azure AD AD B2C.
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal.
1. Selecione o diretório que contém a sua subscrição.

    ![Diretório de comutação](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Verifique se selecionou o diretório e a subscrição corretos. Neste exemplo, todos os diretórios e subscrições são selecionados.

    ![Todos os diretórios selecionados no filtro de Diretório e Subscrição](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Configurar as definições de diagnóstico

As definições de diagnóstico definem onde devem ser enviados registos e métricas para um recurso. Os destinos possíveis são:

- [Conta de armazenamento do Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- Soluções de hubs de [eventos.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Área de trabalho do Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Se ainda não o fez, crie uma instância do seu tipo de destino escolhido no grupo de recursos especificado no modelo do Gestor de [Recursos Azure](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Criar definições de diagnóstico

Está pronto para [criar configurações](../active-directory/reports-monitoring/overview-monitoring.md) de diagnóstico no portal Azure.

Para configurar as definições de monitorização dos registos de atividade do Azure AD B2C:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. Selecione **Diretório Ativo Azure**
1. Sob **monitorização,** selecione **definições de diagnóstico**.
1. Se existirem definições existentes no recurso, verá uma lista de definições já configuradas. **Selecione Adicionar definição de diagnóstico** para adicionar uma nova definição, ou **editar** a definição para editar uma existente. Cada definição não pode ter mais do que um dos tipos de destino..

    ![Painel de definições de diagnóstico no portal Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dê um nome ao seu cenário se já não tiver um.
1. Verifique a caixa para cada destino para enviar os registos. **Selecione Configurar** para especificar as suas definições conforme descrito na tabela seguinte.

    | Definição | Descrição |
    |:---|:---|
    | Arquivar para uma conta de armazenamento | Nome da conta de armazenamento. |
    | Stream para um centro de eventos | O espaço de nome onde o hub do evento é criado (se esta for a sua primeira vez em diários de streaming) ou transmitido para (se já existem recursos que estão a transmitir essa categoria de log para este espaço de nome).
    | Enviar para o Log Analytics | Nome do espaço de trabalho. |

1. Selecione **Registos de Auditoria** e **SignInLogs**.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a adição e configuração das definições de diagnóstico no Monitor Azure, consulte [Tutorial: Colete e analise registos de recursos a partir de um recurso Azure](../azure-monitor/insights/monitor-azure-resource.md).

Para obter informações sobre o streaming de registos de Anúncios Azure para um centro de [eventos, consulte Tutorial: Stream Azure Ative Directory logy para um hub de eventos Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
