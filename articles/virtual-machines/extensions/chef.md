---
title: Extensão do chefe para VMs do Azure | Microsoft Docs
description: Implante o cliente chefe em uma máquina virtual usando a extensão de VM chefe.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 0ed042b3b004fd43bc03304c3042c2ac3e1a6482
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092559"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Extensão de VM chefe para Linux e Windows

O Chef Software fornece uma plataforma de automatização DevOps para Linux e Windows que permite gerir configurações de servidor tanto físicas como virtuais. A extensão de VM chefe é uma extensão que habilita o chefe nas máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de VM chefe tem suporte em todos os [sistemas operacionais com suporte de extensão](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) no Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de VM chefe requer que a máquina virtual de destino esteja conectada à Internet para recuperar a carga do cliente chefe da rede de distribuição de conteúdo (CDN).  

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão de VM chefe. A extensão requer no mínimo a URL do servidor chefe, o nome do cliente de validação e a chave de validação para o servidor chefe; esses valores podem ser encontrados no `knife.rb` arquivo no Starter-Kit. zip que é baixado quando você instala o chefe Automate ou um [servidor chefe](https://downloads.chef.io/chef-server)autônomo. [](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) Como a chave de validação deve ser tratada como dados confidenciais, ela deve ser configurada no elemento **protectedSettings** , o que significa que ela só será descriptografada na máquina virtual de destino.

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

### <a name="core-property-values"></a>Valores de propriedade de núcleo

| Name | Valor / exemplo | Tipo de Dados
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (data) |
| publisher | `Chef.Bootstrap.WindowsAzure` | Cadeia de caracteres |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | Cadeia de caracteres |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>Definições

| Name | Valor / exemplo | Tipo de Dados | Obrigatório?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | S |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | Cadeia de caracteres | S |
| configurações/runlist | `recipe[mycookbook::default]` | Cadeia de caracteres | S |

### <a name="protected-settings"></a>Configurações protegidas

| Name | Exemplo | Tipo de Dados | Obrigatório?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | Cadeia de caracteres | S |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos podem ser usados para implantar uma ou mais máquinas virtuais, instalar o cliente chefe, conectar-se ao servidor chefe e executar a configuração inicial no servidor, conforme definido pela [lista de execução](https://docs.chef.io/run_lists.html)

Um modelo do Resource Manager de exemplo que inclui a extensão de VM chefe pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

O CLI do Azure pode ser usado para implantar a extensão de VM chefe em uma VM existente. Substitua o **validation_key** pelo conteúdo da sua chave de validação (este arquivo como uma `.pem` extensão).  Substitua **validation_client_name**, **chef_server_url** e **run_list** `knife.rb` por esses valores do arquivo no kit do iniciante.

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
| 51 | Não há suporte para essa extensão no sistema operacional da VM | |

Informações adicionais de solução de problemas podem ser encontradas no Leiame da [extensão de VM chefe](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Passos Seguintes

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
