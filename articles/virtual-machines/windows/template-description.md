---
title: Máquinas virtuais em um modelo de Azure Resource Manager | Microsoft Azure
description: Saiba mais sobre como o recurso de máquina virtual é definido em um modelo de Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 23519edb61df23c97dfd2162d6cabea6b7fa5d38
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101778"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Máquinas virtuais em um modelo de Azure Resource Manager

Este artigo descreve os aspectos de um modelo de Azure Resource Manager que se aplicam a máquinas virtuais. Este artigo não descreve um modelo completo para criar uma máquina virtual; para que você precise de definições de recursos para contas de armazenamento, interfaces de rede, endereços IP públicos e redes virtuais. Para obter mais informações sobre como esses recursos podem ser definidos em conjunto, confira o [tutorial do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Há muitos [modelos na Galeria](https://azure.microsoft.com/documentation/templates/?term=VM) que incluem o recurso de VM. Nem todos os elementos que podem ser incluídos em um modelo são descritos aqui.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Este exemplo mostra uma seção de recursos típico de um modelo para criar um número especificado de VMs:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Este exemplo se baseia em uma conta de armazenamento que foi criada anteriormente. Você pode criar a conta de armazenamento implantando-a do modelo. O exemplo também se baseia em uma interface de rede e seus recursos dependentes que seriam definidos no modelo. Esses recursos não são mostrados no exemplo.
>
>

## <a name="api-version"></a>Versão da API

Ao implantar recursos usando um modelo, você precisa especificar uma versão da API a ser usada. O exemplo mostra o recurso de máquina virtual usando este elemento apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

A versão da API que você especifica em seu modelo afeta as propriedades que podem ser definidas no modelo. Em geral, você deve selecionar a versão mais recente da API ao criar modelos. Para modelos existentes, você pode decidir se deseja continuar usando uma versão de API anterior ou atualizar seu modelo para a versão mais recente para aproveitar os novos recursos.

Use estas oportunidades para obter as versões mais recentes da API:

- API REST – [listar todos os provedores de recursos](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell- [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Exibição do [provedor](https://docs.microsoft.com/cli/azure/provider) CLI do Azure-AZ


## <a name="parameters-and-variables"></a>Parâmetros e variáveis

Os [parâmetros](../../resource-group-authoring-templates.md) facilitam a especificação de valores para o modelo quando você o executa. Essa seção de parâmetros é usada no exemplo:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Ao implantar o modelo de exemplo, você insere valores para o nome e a senha da conta de administrador em cada VM e o número de VMs a serem criadas. Você tem a opção de especificar valores de parâmetro em um arquivo separado que é gerenciado com o modelo ou fornecendo valores quando solicitado.

As [variáveis](../../resource-group-authoring-templates.md) facilitam a configuração de valores no modelo que são usados repetidamente em todo o processo ou que podem mudar ao longo do tempo. Essa seção de variáveis é usada no exemplo:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Quando você implanta o modelo de exemplo, os valores de variáveis são usados para o nome e o identificador da conta de armazenamento criada anteriormente. As variáveis também são usadas para fornecer as configurações para a extensão de diagnóstico. Use as [práticas recomendadas para criar modelos de Azure Resource Manager](../../resource-manager-template-best-practices.md) para ajudá-lo a decidir como deseja estruturar os parâmetros e variáveis em seu modelo.

## <a name="resource-loops"></a>Loops de recursos

Quando você precisar de mais de uma máquina virtual para seu aplicativo, poderá usar um elemento de cópia em um modelo. Esse elemento opcional percorre a criação do número de VMs que você especificou como um parâmetro:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Além disso, observe que, no exemplo, o índice de loop é usado ao especificar alguns dos valores para o recurso. Por exemplo, se você inseriu uma contagem de instâncias de três, os nomes dos discos do sistema operacional serão myOSDisk1, myOSDisk2 e myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Este exemplo usa Managed disks para as máquinas virtuais.
>
>

Tenha em mente que a criação de um loop para um recurso no modelo pode exigir que você use o loop ao criar ou acessar outros recursos. Por exemplo, várias VMs não podem usar a mesma interface de rede, portanto, se o modelo faz um loop pela criação de três VMs, ele também deve fazer um loop pela criação de três interfaces de rede. Ao atribuir uma interface de rede a uma VM, o índice de loop é usado para identificá-lo:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Dependências

A maioria dos recursos depende de outros recursos para funcionar corretamente. As máquinas virtuais devem ser associadas a uma rede virtual e, para isso, precisam de uma interface de rede. O [](../../resource-group-define-dependencies.md) elemento depending é usado para garantir que a interface de rede esteja pronta para ser usada antes que as VMs sejam criadas:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

O Resource Manager implanta em paralelo todos os recursos que não dependem de outro recurso que está sendo implantado. Tenha cuidado ao definir dependências, pois você pode retardar inadvertidamente a implantação especificando dependências desnecessárias. As dependências podem ser encadeadas por meio de vários recursos. Por exemplo, a interface de rede depende do endereço IP público e dos recursos de rede virtual.

Como saber se uma dependência é necessária? Examine os valores definidos no modelo. Se um elemento na definição de recurso de máquina virtual apontar para outro recurso implantado no mesmo modelo, você precisará de uma dependência. Por exemplo, sua máquina virtual de exemplo define um perfil de rede:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Para definir essa propriedade, a interface de rede deve existir. Portanto, você precisa de uma dependência. Você também precisa definir uma dependência quando um recurso (um filho) é definido dentro de outro recurso (um pai). Por exemplo, as configurações de diagnóstico e as extensões de script personalizado são definidas como recursos filho da máquina virtual. Eles não podem ser criados até que a máquina virtual exista. Portanto, ambos os recursos são marcados como dependentes na máquina virtual.

## <a name="profiles"></a>Perfis

Vários elementos de perfil são usados ao definir um recurso de máquina virtual. Alguns são necessários e alguns são opcionais. Por exemplo, os elementos hardwareProfile, osProfile, storageProfile e networkProfile são necessários, mas o diagnosticsProfile é opcional. Esses perfis definem configurações como:
   
- [size](sizes.md)
- [nome](/azure/architecture/best-practices/naming-conventions) e credenciais
- configurações do [sistema operacional](cli-ps-findimage.md) e do disco
- [interface de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- diagnóstico de inicialização

## <a name="disks-and-images"></a>Discos e imagens
   
No Azure, os arquivos VHD podem representar [discos ou imagens](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando o sistema operacional em um arquivo VHD é especializado para ser uma VM específica, ele é chamado de disco. Quando o sistema operacional em um arquivo VHD é generalizado para ser usado para criar várias VMs, ele é chamado de imagem.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Criar novas máquinas virtuais e novos discos de uma imagem de plataforma

Ao criar uma VM, você deve decidir qual sistema operacional será usado. O elemento imageReference é usado para definir o sistema operacional de uma nova VM. O exemplo mostra uma definição para um sistema operacional Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Se você quiser criar um sistema operacional Linux, poderá usar essa definição:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

As definições de configuração para o disco do sistema operacional são atribuídas com o elemento osDisk. O exemplo define um novo disco gerenciado com o modo de cache definido como **ReadWrite** e que o disco está sendo criado a partir de uma [imagem de plataforma](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Criar novas máquinas virtuais a partir de discos gerenciados existentes

Se você quiser criar máquinas virtuais a partir de discos existentes, remova os elementos imageReference e osProfile e defina essas configurações de disco:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Criar novas máquinas virtuais a partir de uma imagem gerenciada

Se você quiser criar uma máquina virtual de uma imagem gerenciada, altere o elemento imageReference e defina essas configurações de disco:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Anexar discos de dados

Opcionalmente, você pode adicionar discos de dados às VMs. O [número de discos](sizes.md) depende do tamanho do disco do sistema operacional que você usa. Com o tamanho das VMs definidas como Standard_DS1_v2, o número máximo de discos de dados que podem ser adicionados a eles é dois. No exemplo, um disco de dados gerenciado está sendo adicionado a cada VM:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Extensões

Embora [as extensões](extensions-features.md) sejam um recurso separado, elas estão fortemente ligadas às VMs. As extensões podem ser adicionadas como um recurso filho da VM ou como um recurso separado. O exemplo mostra a [extensão de diagnóstico](extensions-diagnostics-template.md) que está sendo adicionada às VMs:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Esse recurso de extensão usa a variável storagename e as variáveis de diagnóstico para fornecer valores. Se você quiser alterar os dados coletados por essa extensão, poderá adicionar mais contadores de desempenho à variável wadperfcounters. Você também pode optar por colocar os dados de diagnóstico em uma conta de armazenamento diferente daquela em que os discos de VM estão armazenados.

Há muitas extensões que você pode instalar em uma VM, mas a mais útil é provavelmente a [extensão de script personalizado](extensions-customscript.md). No exemplo, um script do PowerShell chamado start. ps1 é executado em cada VM quando ele é iniciado pela primeira vez:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

O script Start. ps1 pode realizar muitas tarefas de configuração. Por exemplo, os discos de dados que são adicionados às VMs no exemplo não são inicializados; Você pode usar um script personalizado para inicializá-los. Se você tiver várias tarefas de inicialização para fazer, poderá usar o arquivo start. ps1 para chamar outros scripts do PowerShell no armazenamento do Azure. O exemplo usa o PowerShell, mas você pode usar qualquer método de script disponível no sistema operacional que você está usando.

Você pode ver o status das extensões instaladas nas configurações de extensões no Portal:

![Obter status da extensão](./media/template-description/virtual-machines-show-extensions.png)

Você também pode obter informações de extensão usando o comando do PowerShell **Get-AzVMExtension** , a **extensão de VM Get** CLI do Azure comando ou a API REST **obter informações de extensão** .

## <a name="deployments"></a>Implementações

Quando você implanta um modelo, o Azure rastreia os recursos implantados como um grupo e atribui automaticamente um nome a esse grupo implantado. O nome da implantação é o mesmo que o nome do modelo.

Se você estiver curioso sobre o status dos recursos na implantação, exiba o grupo de recursos no portal do Azure:

![Obter informações de implantação](./media/template-description/virtual-machines-deployment-info.png)
    
Não é um problema usar o mesmo modelo para criar recursos ou para atualizar os recursos existentes. Ao usar comandos para implantar modelos, você tem a oportunidade de dizer qual [modo](../../resource-group-template-deploy.md) você deseja usar. O modo pode ser definido como **completo** ou **incremental**. O padrão é fazer atualizações incrementais. Tenha cuidado ao usar o modo **completo** , pois você pode excluir recursos acidentalmente. Quando você define o modo como **concluído**, o Resource Manager exclui todos os recursos no grupo de recursos que não estão no modelo.

## <a name="next-steps"></a>Próximos Passos

- Crie seu próprio modelo usando a [criação de modelos de Azure Resource Manager](../../resource-group-authoring-templates.md).
- Implante o modelo que você criou usando [criar uma máquina virtual do Windows com um modelo do Resource Manager](ps-template.md).
- Saiba como gerenciar as VMs que você criou examinando [criar e gerenciar VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Para obter a sintaxe JSON e as propriedades dos tipos de recursos em modelos, consulte [Azure Resource Manager referência de modelo](/azure/templates/).
