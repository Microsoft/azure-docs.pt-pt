---
title: Máquinas virtuais num modelo Azure Resource Manager | Microsoft Azure
description: Saiba mais sobre como o recurso de máquina virtual está definido num modelo do Azure Resource Manager.
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
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: fd4fad40ef4809c756321493854f38fd813569ca
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710290"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Máquinas virtuais num modelo Azure Resource Manager

Este artigo descreve aspectos de um modelo Azure Resource Manager que se aplicam a máquinas virtuais. Este artigo não descreve um modelo completo para a criação de uma máquina virtual; Para isso precisa de definições de recursos para contas de armazenamento, interfaces de rede, endereços IP públicos e redes virtuais. Para obter mais informações sobre como estes recursos podem ser definidos em conjunto, consulte a [instruções do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Existem muitas [modelos na galeria](https://azure.microsoft.com/documentation/templates/?term=VM) que incluam o recurso da VM. Nem todos os elementos que podem ser incluídos num modelo são descritos aqui.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Este exemplo mostra uma seção de recurso típico de um modelo para criar um número especificado de VMs:

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
>Este exemplo se baseia numa conta de armazenamento que foi criada anteriormente. Pode criar a conta de armazenamento, implantando-a partir do modelo. O exemplo também se baseia numa interface de rede e os respetivos recursos dependentes que seriam definidos no modelo. Estes recursos não são apresentados no exemplo.
>
>

## <a name="api-version"></a>Versão de API

Ao implementar recursos com um modelo, terá de especificar uma versão da API a utilizar. O exemplo mostra o recurso de máquina virtual com este elemento apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

A versão da API que especificar no seu modelo afeta as propriedades que pode definir no modelo. Em geral, deve selecionar a versão mais recente da API durante a criação de modelos. Para modelos existentes, pode decidir se pretende continuar a utilizar uma versão de API anterior ou atualizar o modelo para a versão mais recente tirar partido das novas funcionalidades.

Use essas oportunidades para obter as mais recentes versões de API:

- REST API - [listar todos os fornecedores de recursos](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell – [Get AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- CLI do Azure - [show de fornecedor de az](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parâmetros e variáveis

[Parâmetros](../../resource-group-authoring-templates.md) torna mais fácil para que especifique valores para o modelo quando ele é executado. Esta secção de parâmetros é usada no exemplo:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Ao implementar o modelo de exemplo, introduza valores para o nome e a palavra-passe da conta de administrador em cada VM e o número de VMs para criar. Tem a opção de especificar valores de parâmetro num arquivo separado que é gerido com o modelo ou fornecer valores quando lhe for pedido.

[Variáveis](../../resource-group-authoring-templates.md) torna mais fácil para configurar valores no modelo que são usadas repetidamente em toda ele ou que pode alterar ao longo do tempo. Esta secção de variáveis é usada no exemplo:

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

Ao implementar o modelo de exemplo, os valores das variáveis são utilizados para o nome e o identificador da conta de armazenamento criado anteriormente. Variáveis também são utilizadas para fornecer as definições para a extensão de diagnóstico. Utilize o [melhores práticas para a criação de modelos Azure Resource Manager](../../resource-manager-template-best-practices.md) para ajudar a decidir como pretende estruturar os parâmetros e variáveis no seu modelo.

## <a name="resource-loops"></a>Loops de recursos

Quando precisar de mais de uma máquina virtual para a sua aplicação, pode utilizar um elemento de cópia num modelo. Este elemento opcional faz um loop por meio de criar o número de VMs que especificou como um parâmetro:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Além disso, observe o exemplo que o índice de loop é utilizado durante a especificação de alguns dos valores para o recurso. Por exemplo, se tiver introduzido uma contagem de instâncias de três, os nomes dos discos de sistema operativo são myOSDisk1 myOSDisk2 e myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Este exemplo utiliza discos geridos para as máquinas virtuais.
>
>

Tenha em atenção que a criação de um loop para um recurso no modelo pode exigir que use o loop quando criar ou aceder a outros recursos. Por exemplo, várias VMs não é possível utilizar a mesma interface de rede, portanto, se seu modelo faz um loop através da criação de três VMs também tem loop através da criação de três interfaces de rede. Quando atribui uma interface de rede a uma VM, o índice de loop é usado para identificá-lo:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Dependências

A maioria dos recursos dependem de outros recursos funcione corretamente. Máquinas virtuais tem de estar associadas com uma rede virtual e fazer o que necessita de uma interface de rede. O [dependsOn](../../resource-group-define-dependencies.md) elemento é usado para certificar-se de que a interface de rede está pronta para ser utilizada antes das VMs são criadas:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager os implemente em paralelo todos os recursos que não dependentes de outro recurso que está sendo implantado. Tenha cuidado ao definir as dependências porque inadvertidamente pode abrandar a sua implementação ao especificar dependências desnecessárias. Dependências podem encadear através de vários recursos. Por exemplo, a interface de rede depende do endereço IP público e os recursos de rede virtual.

Como sabe se uma dependência é necessária? Ver os valores definidos no modelo. Se um elemento nos pontos de definição do recurso de máquina virtual para outro recurso que é implementado no mesmo modelo, terá uma dependência. Por exemplo, sua máquina de virtual de exemplo define um perfil de rede:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Para definir esta propriedade, a interface de rede tem de existir. Portanto, precisa de uma dependência. Também terá de definir uma dependência quando um recurso (filho) é definido dentro de outro recurso (principal). Por exemplo, as extensões de script personalizado e as definições de diagnóstico estão ambos definidas como recursos subordinados de máquina virtual. Não é possível criar até que a máquina virtual existe. Por conseguinte, ambos os recursos são marcados como dependentes na máquina virtual.

## <a name="profiles"></a>Perfis

Vários elementos de perfil são utilizados para definir um recurso de máquina virtual. Algumas são necessárias e alguns são opcionais. Por exemplo, os elementos hardwareProfile, osProfile, storageProfile e networkProfile são necessários, mas o diagnosticsProfile é opcional. Estes perfis definem as definições, tais como:
   
- [size](sizes.md)
- [nome](/azure/architecture/best-practices/naming-conventions) e credenciais
- disco e [definições do sistema operativo](cli-ps-findimage.md)
- [Interface de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Diagnóstico de arranque

## <a name="disks-and-images"></a>Discos e imagens
   
No Azure, os ficheiros vhd podem representar [discos ou imagens](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando o sistema operativo num ficheiro vhd é especializado para ser uma VM específica, é referido como um disco. Quando o sistema operativo num ficheiro vhd é generalizado para ser utilizado para criar o número de VMs, é referido como uma imagem.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Criar novas máquinas virtuais e novos discos a partir de uma imagem de plataforma

Quando cria uma VM, deve decidir qual o sistema operacional para utilizar. O elemento de imageReference é utilizado para definir o sistema operativo de uma nova VM. O exemplo mostra uma definição para um sistema operativo Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Se pretender criar um sistema operativo Linux, poderá utilizar esta definição:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Definições de configuração para o disco do sistema operativo são atribuídas com o elemento de osDisk. O exemplo define um novo disco gerido com o modo de colocação em cache definido como **ReadWrite** e que o disco está a ser criado a partir de um [imagem de plataforma](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Criar novas máquinas virtuais a partir de discos geridos existentes

Se quiser criar máquinas virtuais a partir de discos existentes, remover o imageReference e os elementos de osProfile e definir estas definições de disco:

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

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Criar novas máquinas virtuais a partir de uma imagem gerida

Se quiser criar uma máquina virtual a partir de uma imagem gerida, altere o elemento de imageReference e definir estas definições de disco:

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

Opcionalmente, pode adicionar discos de dados para as VMs. O [número de discos](sizes.md) depende do tamanho do disco do sistema operativo que utilizar. Com o tamanho das VMs definido como Standard_DS1_v2, o número máximo de discos de dados que poderiam ser adicionadas para o-los é dois. No exemplo, um disco de dados gerido está a ser adicionado a cada VM:

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

Embora [extensões](extensions-features.md) são um recurso separado, eles estivessem diretamente ligados às VMs. As extensões podem ser adicionadas como um recurso de subordinados da VM ou como um recurso separado. O exemplo mostra a [extensão de diagnóstico](extensions-diagnostics-template.md) a ser adicionado às VMs:

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

Este recurso de extensão utiliza a variável de storageName e as variáveis de diagnóstico para fornecer valores. Se pretender alterar os dados que são recolhidos por esta extensão, pode adicionar mais contadores de desempenho para a variável wadperfcounters. Também pode optar por colocar os dados de diagnóstico numa conta de armazenamento diferentes em que os discos VM são armazenados.

Há muitas extensões que pode instalar numa VM, mas o mais útil é provavelmente o [extensão de Script personalizado](extensions-customscript.md). No exemplo, um script do PowerShell com o nome start.ps1 é executado em cada VM de primeiro arranque:

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

O script de start.ps1 pode realizar muitas tarefas de configuração. Por exemplo, os discos de dados que são adicionados para as VMs no exemplo não são inicializados; Pode utilizar um script personalizado para inicializá-las. Se tiver várias tarefas de arranque para o fazer, pode utilizar o ficheiro de start.ps1 chamar outros scripts do PowerShell no armazenamento do Azure. O exemplo utiliza o PowerShell, mas pode utilizar qualquer método de criação de scripts que está disponível no sistema operativo que está a utilizar.

Pode ver o estado das extensões instaladas nas definições de extensões no portal do:

![Obter estado da extensão](./media/template-description/virtual-machines-show-extensions.png)

Também pode obter informações de extensão, utilizando o **Get-AzVMExtension** comando do PowerShell, o **get de extensão de vm** comando da CLI do Azure, ou o **obter informações de extensão**REST API.

## <a name="deployments"></a>Implementações

Quando implementa um modelo, o Azure controla os recursos que implementou como um grupo e atribui automaticamente um nome para este grupo implementado. O nome da implementação é igual ao nome do modelo.

Se estiver curioso sobre o estado dos recursos na implementação, ver o grupo de recursos no portal do Azure:

![Obtenha informações de implementação](./media/template-description/virtual-machines-deployment-info.png)
    
Não é um problema ao utilizar o mesmo modelo para criar recursos ou para atualizar recursos existentes. Quando utiliza comandos para implementar modelos, terá a oportunidade para dizer que [modo](../../resource-group-template-deploy.md) que pretende utilizar. O modo pode ser definido para o **concluída** ou **Incremental**. A predefinição é fazer atualizações incrementais. Tenha cuidado ao utilizar o **Complete** modo porque pode excluir acidentalmente os recursos. Se definir o modo como **Complete**, Gestor de recursos elimina todos os recursos no grupo de recursos que não estão no modelo.

## <a name="next-steps"></a>Próximos Passos

- Criar seu próprio modelo usando [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).
- Implementar o modelo que criou utilizando [criar uma máquina virtual do Windows com um modelo do Resource Manager](ps-template.md).
- Saiba como gerir as VMs que criar ao rever [criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Para a sintaxe JSON e propriedades de tipos de recursos em modelos, consulte [referência de modelo do Azure Resource Manager](/azure/templates/).
