---
title: Extensão de configuração de hóspedes da política de Azure
description: Saiba mais sobre a extensão utilizada para auditar/configurar configurações dentro de máquinas virtuais
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368662"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Visão geral da extensão da configuração do hóspede da política de Azure

A extensão de Configuração do Hóspede é um componente Azure Policy que realiza operações de auditoria e configuração dentro de máquinas virtuais.
Políticas como definições de base de segurança para [Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) e [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) não podem verificar as definições dentro das máquinas até que a extensão seja instalada.

## <a name="prerequisites"></a>Pré-requisitos

Para que a máquina autentica o serviço de Configuração do Convidado, a máquina deve ter uma [identidade gerida atribuída ao sistema.](../../active-directory/managed-identities-azure-resources/overview.md)
O requisito de identidade numa máquina virtual é cumprido se a seguinte propriedade estiver definida.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Sistemas Operativos

O suporte para a extensão de Configuração do Hóspede é o mesmo que o suporte do sistema operativo [documentado para a solução final.](../../governance/policy/concepts/guest-configuration.md#supported-client-types)

### <a name="internet-connectivity"></a>Conectividade Internet

O agente instalado pela extensão de Configuração do Hóspede deve ser capaz de alcançar pacotes de conteúdo listados por atribuições de Configuração de Convidados e reportar o estado ao serviço de Configuração de Convidados.
A máquina pode ligar-se utilizando https de saída sobre a porta TCP 443, ou se for fornecida uma ligação através de uma rede privada.
Para saber mais sobre networking privado, consulte os seguintes artigos:

- [Configuração do hóspede, comunique sobre link privado em Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Use pontos finais privados para armazenamento Azure](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Como posso instalar a extensão?

Para implementar a versão mais recente da extensão em escala, incluindo requisitos de identidade, atribua a Política Azure, [implemente pré-requisitos para permitir políticas de configuração de hóspedes em máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).
Para máquinas individuais, a extensão pode ser implantada utilizando Azure CLI, PowerShell, modelos de Gestor de Recursos ou ferramentas de terceiros.

O nome da extensão deve ser definido como "AzurePolicyforWindows" ou "AzurePolicyforLinux", porque as políticas acima referidas requerem estas cordas específicas.

Por predefinição, todas as implementações atualizam-se para a versão mais recente. O valor da propriedade _autoUpgradeMinorVersion_ é "verdadeiro" a menos que seja especificado de outra forma. Não precisa de se preocupar em atualizar o seu código quando forem lançadas novas versões da extensão.

### <a name="azure-cli"></a>CLI do Azure

Para implementar a extensão para Linux:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Para implementar a extensão para o Windows:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Para implementar a extensão para Linux:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Para implementar a extensão para o Windows:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Para implementar a extensão para Linux:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Para implementar a extensão para o Windows:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Para implementar a extensão para Linux:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Para implementar a extensão para o Windows:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Definições

Não há necessidade de incluir quaisquer configurações ou propriedades de definições protegidas na extensão.
Todas essas informações são obtidas pelo agente a partir dos recursos de atribuição de [Configuração de Hóspedes.](/rest/api/guestconfiguration/guestconfigurationassignments) Por exemplo, as propriedades [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)e [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) são geridas por configuração e não na extensão VM.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a configuração do hóspede da política de Azure, consulte [a configuração de hóspedes da Política de Azure](../../governance/policy/concepts/guest-configuration.md)
* Para obter mais informações sobre como o Agente Linux e as extensões funcionam, consulte [extensões e funcionalidades do Azure VM para o Linux](features-linux.md).
* Para obter mais informações sobre o funcionamento do Windows Guest Agent e das extensões, consulte [as extensões e funcionalidades do Azure VM para windows](features-windows.md).  
* Para instalar o Windows Guest Agent, consulte [a visão geral do Agente de Máquinas Virtuais do Azure Windows](agent-windows.md).  
* Para instalar o Agente Linux, consulte [a visão geral do Agente de Máquinas Virtuais Azure Linux](agent-linux.md).  
