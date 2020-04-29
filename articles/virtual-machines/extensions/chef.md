---
title: Extensão de chef para VMs Azure
description: Desloque o Chef Client para uma máquina virtual usando a extensão Chef VM.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: a21b8f2fea7433e9f65fd790321a28ea47a38c79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76544723"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Extensão Chef VM para Linux e Windows

O Chef Software fornece uma plataforma de automatização DevOps para Linux e Windows que permite gerir configurações de servidor tanto físicas como virtuais. A Extensão Chef VM é uma extensão que permite chef em máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A Extensão Chef VM é suportada em todos os [Os's suportados](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) por extensão em Azure.

### <a name="internet-connectivity"></a>Conectividade Internet

A Extensão VM do Chef requer que a máquina virtual alvo esteja ligada à internet de forma a recuperar a carga útil do Chef Client da rede de entrega de conteúdos (CDN).  

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão chef VM. A extensão requer no mínimo o URL do Chef Server, o Nome do Cliente de Validação e a Chave de Validação para o Servidor do Chef; estes valores podem `knife.rb` ser encontrados no ficheiro no starter-kit.zip que é descarregado quando você instala [chef automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) ou um [Chef Server](https://downloads.chef.io/chef-server)autónomo. Uma vez que a chave de validação deve ser tratada como dados sensíveis, deve ser configurada sob o elemento **Definições protegidas,** o que significa que só será desencriptada na máquina virtual alvo.

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

### <a name="core-property-values"></a>Valores de propriedade central

| Nome | Valor / Exemplo | Tipo de Dados
| ---- | ---- | ----
| apiVersion | `2017-12-01` | corda (data) |
| publicador | `Chef.Bootstrap.WindowsAzure` | string |
| tipo | `LinuxChefClient`(Linux), `ChefClient` (Janelas) | string |
| typeHandlerVersion | `1210.13` | corda (duplo) |

### <a name="settings"></a>Definições

| Nome | Valor / Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ----
| definições/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | cadeia (url) | S |
| definições/bootstrap_options/validation_client_name | `myorg-validator` | string | S |
| definições/lista de execução | `recipe[mycookbook::default]` | string | S |

### <a name="protected-settings"></a>Definições protegidas

| Nome | Exemplo | Tipo de Dados | Necessário?
| ---- | ---- | ---- | ---- |
| definições/validation_key protegidos | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | S |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. Os modelos podem ser usados para implantar uma ou mais máquinas virtuais, instalar o Chef Client, ligar ao Chef Server e executar a configuração inicial no servidor como definido pela [Lista de Execução](https://docs.chef.io/run_lists.html)

Um modelo de Gestor de Recursos de amostra que inclui a extensão Do Chef VM pode ser encontrado na [galeria Azure quickstart.](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)

A configuração JSON para uma extensão virtual da máquina pode ser aninhada dentro do recurso virtual da máquina, ou colocada no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação da configuração JSON afeta o valor do nome e do tipo de recursos. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/resource-manager-template-child-resource.md)

## <a name="azure-cli-deployment"></a>Implantação Azure CLI

O Azure CLI pode ser usado para implantar a extensão Do Chef VM a um VM existente. Substitua a **validation_key** pelo conteúdo da sua `.pem` chave de validação (este ficheiro como uma extensão).  Substitua **validation_client_name,** **chef_server_url** e **run_list** com os valores do `knife.rb` ficheiro do seu Kit de Arranque.

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

Os dados sobre o estado das extensões podem ser recuperados do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI.

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

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código de Erro | Significado | Ação Possível |
| :---: | --- | --- |
| 51 | Esta extensão não é suportada no sistema operativo da VM | |

Informações adicionais de resolução de problemas podem ser encontradas no leume de [extensão chef VM.](https://github.com/chef-partners/azure-chef-extension)

> [!NOTE]
> Para qualquer outra coisa diretamente relacionada com chef, contacte [chef suporte.](https://www.chef.io/support/)

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
