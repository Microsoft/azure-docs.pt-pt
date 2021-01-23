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
ms.openlocfilehash: 45f2b75be9a0090b883c5cc62a0886366e81a302
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744670"
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
As implementações do Cloud Service (suporte alargado) devem estar numa rede virtual. A rede virtual pode ser criada através do [portal Azure,](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) [PowerShell,](https://docs.microsoft.com/azure/virtual-network/quick-create-powershell) [Azure CLI](https://docs.microsoft.com/azure/virtual-network/quick-create-cli) ou [ARM.](https://docs.microsoft.com/azure/virtual-network/quick-create-template) A rede virtual e as sub-redes também devem ser referenciadas na Configuração de Serviço (.cscfg) na `NetworkConfiguration` secção. 

Para uma rede virtual pertencente ao mesmo grupo de recursos que o serviço de nuvem, apenas é suficiente fazer referência ao nome da rede virtual no ficheiro Configuração de Serviço (.cscfg). Se a rede virtual e o serviço de nuvem estiverem em dois grupos de recursos diferentes, então o ID completo do Gestor de Recursos Azure da rede virtual tem de ser especificado no ficheiro de Configuração de Serviço (.cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Rede Virtual localizada no mesmo grupo de recursos
```json
<VirtualNetworkSite name="<vnet-name>"/> 
<AddressAssignments> 
<InstanceAddress roleName="<role-name>"> 
<Subnets> 
<Subnet name="<subnet-name>"/> 
</Subnets> 
</InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Rede virtual localizada em diferentes grupos de recursos
```json
“/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/> 
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

## <a name="required-service-definition-file-csdef-updates"></a>Atualizações necessárias do ficheiro de definição de serviço (.csdef)

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
> Para obter uma lista de tamanhos disponíveis consulte [Resource Skus - List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) e aplique os seguintes filtros: <br>
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

## <a name="key-vault-creation"></a>Criação do Cofre Chave 

O Key Vault é utilizado para armazenar certificados associados aos Serviços Cloud (suporte alargado). Adicione os certificados ao Key Vault e, em seguida, faça referência às impressões digitais do certificado no ficheiro de configuração de serviço. Também precisa de ativar o Key Vault para obter permissões apropriadas para que o recurso Cloud Services (suporte alargado) possa obter o certificado armazenado como segredos do Key Vault. O Key Vault pode ser criado através do [portal Azure](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)e  [da PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). O Cofre-Chave deve ser criado na mesma região e subscrição que o serviço de nuvem. Para mais informações consulte [os certificados utilizar com a Azure Cloud Services (suporte alargado)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Próximos passos 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
