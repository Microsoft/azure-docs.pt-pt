---
title: Pré-requisitos para a implantação de Serviços cloud Azure (suporte alargado)
description: Pré-requisitos para a implantação de Serviços cloud Azure (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d85aad16049dee6496cb1eaf9def5451625ab876
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773475"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Pré-requisitos para a implantação de Serviços cloud Azure (suporte alargado)

> [!IMPORTANT]
> Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para garantir uma implementação bem sucedida dos Cloud Services (suporte alargado) reveja os passos abaixo e complete cada item antes de tentar quaisquer implementações. 

## <a name="register-the-cloudservices-feature"></a>Registar a funcionalidade CloudServices
Registe a funcionalidade da sua subscrição. A inscrição pode demorar vários minutos a ser concluída. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Verifique o estado do registo utilizando o seguinte:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Configurações de serviço necessárias (.cscfg) atualizações de ficheiros

### <a name="1-virtual-network"></a>1) Rede Virtual
As implementações do Cloud Service (suporte alargado) devem estar numa rede virtual. A rede virtual pode ser criada através do [portal Azure,](../virtual-network/quick-create-portal.md) [PowerShell,](../virtual-network/quick-create-powershell.md) [Azure CLI](../virtual-network/quick-create-cli.md) ou [ARM.](../virtual-network/quick-create-template.md) A rede virtual e as sub-redes também devem ser referenciadas na Configuração de Serviço (.cscfg) na secção [NetworkConfiguration.](schema-cscfg-networkconfiguration.md) 

Para uma rede virtual pertencente ao mesmo grupo de recursos que o serviço de nuvem, apenas é suficiente fazer referência ao nome da rede virtual no ficheiro Configuração de Serviço (.cscfg). Se a rede virtual e o serviço de nuvem estiverem em dois grupos de recursos diferentes, então o ID completo do Gestor de Recursos Azure da rede virtual tem de ser especificado no ficheiro de Configuração de Serviço (.cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Rede Virtual localizada no mesmo grupo de recursos
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Rede virtual localizada em diferentes grupos de recursos
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) Remova os plugins antigos

Remova as definições antigas de ambiente de trabalho remoto do ficheiro De configuração de serviço (.cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
Remova as definições de diagnóstico antigos para cada função no ficheiro de Configuração de Serviço (.cscfg).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Atualizações necessárias do ficheiro de definição de serviço (.csdef)

> [!NOTE]
> As alterações no ficheiro de definição de serviço (.csdef) exigem que o ficheiro de embalagem (.cspkg) seja novamente gerado. Por favor, construa e reembale o seu post .cspkg fazendo as seguintes alterações no ficheiro .csdef para obter as definições mais recentes para o seu serviço na nuvem

### <a name="1-virtual-machine-sizes"></a>1) Tamanhos de máquinas virtuais
Os seguintes tamanhos são depreciados no Azure Resource Manager. No entanto, se quiser continuar a usá-los, atualize o `vmsize` nome com a convenção de nomeação do Gestor de Recursos Azure associado.  

| Nome do tamanho anterior | Nome de tamanho atualizado | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Pequeno | Standard_A1 |
| Médio | Standard_A2 | 
| Grande | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Por exemplo, `<WorkerRole name="WorkerRole1" vmsize="Medium"` tornar-se-ia. `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`
 
> [!NOTE]
> Para obter uma lista de tamanhos disponíveis consulte [Resource Skus - List](/rest/api/compute/resourceskus/list) e aplique os seguintes filtros: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) Remover os antigos plugins remotos do ambiente de trabalho
As implementações que utilizaram os antigos plugins remotos de ambiente de trabalho precisam de remover os módulos do ficheiro De definição de serviço (.csdef) e de quaisquer certificados associados. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
As implementações que utilizaram os antigos plugins de diagnóstico precisam das definições removidas para cada função a partir do ficheiro Definição de Serviço (.csdef)

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

## <a name="key-vault-creation"></a>Criação do Cofre Chave 

O Key Vault é utilizado para armazenar certificados associados aos Serviços Cloud (suporte alargado). Adicione os certificados ao Key Vault e, em seguida, faça referência às impressões digitais do certificado no ficheiro de configuração de serviço. Também precisa de ativar as "políticas de acesso" do Key Vault (em portal) para acesso a 'Azure Virtual Machines for deployment' e 'Azure Resource Manager for template deployment' para que o recurso Cloud Services (suporte alargado) possa recuperar o certificado armazenado como segredos do Key Vault. Pode criar um cofre chave no [portal Azure](../key-vault/general/quick-create-portal.md) ou utilizando o [PowerShell](../key-vault/general/quick-create-powershell.md). O cofre-chave deve ser criado na mesma região e subscrição que o serviço de nuvem. Para obter mais informações, consulte [os certificados de utilização com a Azure Cloud Services (suporte alargado)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Visite o [repositório](https://github.com/Azure-Samples/cloud-services-extended-support) de amostras cloud services (suporte alargado)
