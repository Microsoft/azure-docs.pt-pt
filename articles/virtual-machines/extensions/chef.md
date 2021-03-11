---
title: Extensão do chef para Azure VMs
description: Desdobre o Cliente Chef para uma máquina virtual usando a extensão do Chef VM.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 09/21/2018
ms.openlocfilehash: e316bf9763dd7c2cbbab21992086eac52d108912
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554790"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Extensão do Chef VM para Linux e Windows

O Chef Software fornece uma plataforma de automatização DevOps para Linux e Windows que permite gerir configurações de servidor tanto físicas como virtuais. A extensão do Chef VM é uma extensão que permite ao Chef em máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do Chef VM é suportada em todos os [OS suportados extensão](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) em Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Chef VM requer que a máquina virtual alvo esteja ligada à internet de forma a recuperar a carga útil do Chef Client a partir da rede de entrega de conteúdos (CDN).  

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do Chef VM. A extensão requer no mínimo o URL do Servidor do Chef, o Nome do Cliente de Validação e a Chave de Validação para o Servidor do Chef; estes valores podem ser encontrados `knife.rb` no ficheiro no starter-kit.zip que é descarregado quando instala o Chef [Automamate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) ou um [Chef Server](https://downloads.chef.io/chef-server)autónomo. Uma vez que a chave de validação deve ser tratada como dados sensíveis, deve ser configurada sob o elemento **protectedSettings,** o que significa que só será desencriptada na máquina virtual alvo.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Valores fundamentais da propriedade

| Name | Valor / Exemplo | Tipo de Dados
| ---- | ---- | ----
| apiVersion | `2017-12-01` | corda (data) |
| publicador | `Chef.Bootstrap.WindowsAzure` | string |
| tipo | `LinuxChefClient` (Linux), `ChefClient` (Janelas) | string |
| typeHandlerVersion | `1210.13` | corda (duplo) |

### <a name="settings"></a>Definições

| Name | Valor / Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ----
| definições/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | cadeia (url) | Y |
| definições/bootstrap_options/validation_client_name | `myorg-validator` | string | Y |
| definições/lista de corredores | `recipe[mycookbook::default]` | string | Y |

### <a name="protected-settings"></a>Configurações protegidas

| Name | Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. Os modelos podem ser usados para implantar uma ou mais máquinas virtuais, instalar o Cliente Chef, ligar ao Servidor do Chef e executar a configuração inicial no servidor tal como definido pela [lista de execução](https://docs.chef.io/run_lists.html)

Um modelo de gestor de recursos de amostra que inclui a extensão do Chef VM pode ser encontrado na [galeria de arranque rápido Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)

A configuração JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual, ou colocada no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação da configuração JSON afeta o valor do nome e do tipo do recurso. Para obter mais informações, consulte [o nome definido e o tipo para obter recursos para crianças.](../../azure-resource-manager/templates/child-resource-name-type.md)

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O Azure CLI pode ser usado para implantar a extensão do Chef VM para um VM existente. Substitua a **validation_key** pelo conteúdo da sua chave de validação (este ficheiro como `.pem` extensão).  Substitua **validation_client_name**, **chef_server_url** e **run_list** por esses valores do `knife.rb` ficheiro no seu Kit de Arranque.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e apoio

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e os seus significados

| Código de Erro | Significado | Possível Ação |
| :---: | --- | --- |
| 51 | Esta extensão não é suportada no sistema operativo do VM | |

Informações adicionais de resolução de problemas podem ser encontradas na leitura da [extensão do Chef VM.](https://github.com/chef-partners/azure-chef-extension)

> [!NOTE]
> Para qualquer outra coisa diretamente relacionada com chef, contacte [o Chef Support.](https://www.chef.io/support/)

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
