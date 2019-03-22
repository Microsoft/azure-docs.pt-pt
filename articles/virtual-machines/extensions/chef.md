---
title: Extensão do chef para VMs do Azure | Documentos da Microsoft
description: Implemente o cliente do Chef para uma máquina virtual utilizando a extensão de VM da Chef.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 6bd3ea4e664523fe8014be40c51d573ed5158ecf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089170"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Extensão de VM do chef para Linux e Windows

O Chef Software fornece uma plataforma de automatização DevOps para Linux e Windows que permite gerir configurações de servidor tanto físicas como virtuais. A extensão de VM do Chef é uma extensão que habilita o Chef em máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de VM do Chef é suportada em todos os [do extensão suportada SO](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) no Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de VM do Chef requer que a máquina virtual de destino está ligada à internet para recuperar o payload de cliente do Chef a partir da rede de entrega de conteúdos (CDN).  

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de VM da Chef. A extensão requer no mínimo o URL do servidor Chef, o nome do cliente de validação e a chave de validação para o servidor do Chef; Estes valores podem ser encontrados no `knife.rb` arquivo em que é transferido ao instalar kit.zip-starter [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) ou autónomo [Chef Server](https://downloads.chef.io/chef-server). Uma vez que a chave de validação deve ser tratada como dados confidenciais, devem ser configurada sob o **protectedSettings** elemento, que significa que ele apenas será possível desencriptar na máquina de virtual de destino.

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
    "typeHandlerVersion": "1210.12",
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

### <a name="core-property-values"></a>Valores de propriedades de núcleo

| Name | Valor / exemplo | Tipo de Dados
| ---- | ---- | ---- 
| apiVersion | `2017-12-01` | a cadeia de caracteres (data) |
| publicador | `Chef.Bootstrap.WindowsAzure` | string |
| tipo | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.12` | a cadeia de caracteres (double) |

### <a name="settings"></a>Definições

| Name | Valor / exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | a cadeia de caracteres (url) | S |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | S |
| settings/runlist | `recipe[mycookbook::default]` | string | S |

### <a name="protected-settings"></a>Definições protegidas

| Name | Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | S |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Modelos podem ser utilizados para implementar uma ou mais máquinas virtuais, instalar o cliente do Chef, ligar ao servidor Chef e a executar a configuração inicial no servidor conforme definido pelo [Run-list](https://docs.chef.io/run_lists.html)

Um modelo do Resource Manager de exemplo que inclui a extensão de VM do Chef pode ser encontrado no [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

A CLI do Azure pode ser utilizada para implementar a extensão de VM do Chef para uma VM existente. Substitua a **validation_key** com o conteúdo da sua chave de validação (este ficheiro como um `.pem` extensão).  Substitua **validation_client_name**, **chef_server_url** e **run_list** com esses valores do `knife.rb` arquivo em seu Kit de iniciante.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e suporte

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 51 | Esta extensão não é suportada no sistema de operativo da VM | |

Informações de resolução de problemas adicionais podem ser encontradas no [Leiame de extensão de VM do Chef](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
