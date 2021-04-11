---
title: Migrar para a Azure Cloud Services (suporte alargado) usando o PowerShell
description: Como migrar dos Serviços Azure Cloud (clássico) para Azure Cloud Services (suporte alargado) usando o PowerShell
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287008"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migrar para a Azure Cloud Services (suporte alargado) usando o PowerShell

Estes passos mostram-lhe como utilizar comandos Azure PowerShell para migrar dos [Serviços Cloud (clássicos)](../cloud-services/cloud-services-choose-me.md) para [serviços cloud (suporte alargado)](overview.md).

> [!IMPORTANT]
> A migração dos Serviços cloud (clássico) para os Serviços cloud (suporte alargado) utilizando a ferramenta de migração está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) Plano de migração
O planeamento é o passo mais importante para uma experiência de migração bem sucedida. Reveja a visão geral dos [Serviços de Nuvem (suporte alargado)](overview.md) e [planeamento para a migração de recursos iaas do clássico para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) antes de iniciar quaisquer etapas de migração. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) Instalar a versão mais recente do PowerShell
Existem duas opções principais para instalar a Azure PowerShell: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A PowerShell Gallery recebe atualizações continuamente. Este artigo baseia-se na versão 2.1.0 da Azure PowerShell.

Para obter instruções de instalação, consulte [como instalar e configurar a Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3) Garantir permissões de administração
Para realizar esta migração, tem de ser adicionado como coadministrator para a subscrição no [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu **Hub,** **selecione Subscrição**. Se não o vir, selecione **Todos os serviços**.
3. Encontre a entrada de subscrição adequada e, em seguida, olhe para o campo **MY ROLE.** Para um coadministrador, o valor deve ser *a administração da conta.*

Se não conseguir adicionar um coadministrador, contacte um administrador de serviço ou coadministrador para que a subscrição seja adicionada.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) Registar o fornecedor clássico e a funcionalidade CloudService
Primeiro, inicie uma solicitação powerShell. Para a migração, crie o seu ambiente tanto para o clássico como para o Gestor de Recursos.

Inscreva-se na sua conta para o modelo gestor de recursos.

```powershell
Connect-AzAccount
```

Obtenha as subscrições disponíveis utilizando o seguinte comando:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Desconfie da sua subscrição Azure para a sessão atual. Este exemplo define o nome de subscrição padrão da **Subscrição My Azure**. Substitua o nome de assinatura exemplo pelo seu próprio.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Registe-se junto do fornecedor de recursos migratório utilizando o seguinte comando:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> O registo é um passo único, mas deve fazê-lo uma vez antes de tentar a migração. Sem se registar, vê a seguinte mensagem de erro:
>
> *BadRequest : A subscrição não está registada para migração.*

Registe a funcionalidade CloudServices para a sua subscrição. As inscrições podem demorar vários minutos a ser concluídas. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Espere cinco minutos para a inscrição terminar. 

Verifique o estado da aprovação clássica do fornecedor utilizando o seguinte comando:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique o estado do registo utilizando o seguinte:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Certifique-se de que o Estado de Registo é `Registered` para ambos antes de prosseguir.

Antes de mudar para o modelo de implementação clássico, certifique-se de que tem quota vCPU vCPU do Azure Resource Manager na região Azure da sua atual implementação ou rede virtual. Pode utilizar o seguinte comando PowerShell para verificar o número atual de vCPUs que tem no Azure Resource Manager. Para saber mais sobre as quotas vCPU, consulte [Limits e o Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Este exemplo verifica a disponibilidade na região oeste **dos EUA.** Substitua o nome da região de exemplo pelo seu próprio.

```powershell
Get-AzVMUsage -Location "West US"
```

Agora, inscreva-se na sua conta para o modelo clássico de implementação.

```powershell
Add-AzureAccount
```

Obtenha as subscrições disponíveis utilizando o seguinte comando:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Desconfie da sua subscrição Azure para a sessão atual. Este exemplo define a subscrição padrão da **Subscrição My Azure**. Substitua o nome de assinatura exemplo pelo seu próprio.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) Migrar os seus Serviços cloud 
* [Migrar um Serviço cloud não numa rede virtual](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Migrar um Serviço cloud numa rede virtual](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Se tiver outro problema que não seja uma funcionalidade não suportada ou um erro de configuração, recomendamos que relemisse a preparação, abortar ou cometer o funcionamento. A plataforma tenta a ação novamente.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5.1) Opção 1 - Migrar um Serviço de Nuvem não numa rede virtual
Obtenha a lista de serviços em nuvem utilizando o seguinte comando. Em seguida, escolha o serviço de nuvem que quer migrar.

```powershell
Get-AzureService | ft Servicename
```

Obtenha o nome de implantação do Serviço cloud. Neste exemplo, o nome de serviço é **My Service**. Substitua o nome de serviço de exemplo pelo seu próprio nome de serviço.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Em primeiro lugar, valide que pode migrar o Serviço cloud utilizando os seguintes comandos:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

O seguinte comando apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode passar ao passo preparar.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5.1) Opção 2 - Migrar um Serviço de Nuvem numa rede virtual

Para migrar um Serviço Cloud numa rede virtual, migra a rede virtual. O Serviço cloud migra automaticamente com a rede virtual.

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo portal. O novo portal Azure apresenta o nome como `[vnet-name]` , mas o nome de rede virtual real é do tipo `Group [resource-group-name] [vnet-name]` . Antes de iniciar a migração, procure o nome da rede virtual, usando o comando `Get-AzureVnetSite | Select -Property Name` ou o veja no antigo portal Azure. 

Este exemplo define o nome da rede virtual para **myVnet**. Substitua o nome de rede virtual exemplo pelo seu próprio.

```powershell
$vnetName = "myVnet"
```

Em primeiro lugar, valide que pode migrar a rede virtual utilizando o seguinte comando:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O seguinte comando apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode proceder com o seguinte passo preparar:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração do Serviço cloud preparado utilizando o Azure PowerShell ou o portal Azure. Se não estiver pronto para a migração e quiser voltar ao estado antigo, use o seguinte comando:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada parecer boa, pode avançar e comprometer os recursos utilizando o seguinte comando:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Passos seguintes
Reveja a secção [de alterações de migração post](in-place-migration-overview.md#post-migration-changes) para ver alterações nos ficheiros de implementação, automação e outros atributos da sua nova implementação de Serviços cloud (suporte alargado). 
