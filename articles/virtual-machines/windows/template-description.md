---
title: Máquinas virtuais em um modelo de Gestor de Recursos Azure  Microsoft Azure
description: Saiba mais sobre como o recurso virtual da máquina é definido num modelo de Gestor de Recursos Azure.
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
ms.openlocfilehash: e1b513344b6ea16c25d829939e64cd5ca1063c87
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243240"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Máquinas virtuais em um modelo de Gestor de Recursos Azure

Este artigo descreve aspetos de um modelo de Gestor de Recursos Azure que se aplicam a máquinas virtuais. Este artigo não descreve um modelo completo para a criação de uma máquina virtual; para isso é necessário definições de recursos para contas de armazenamento, interfaces de rede, endereços IP públicos e redes virtuais. Para obter mais informações sobre como estes recursos podem ser definidos em conjunto, consulte o modelo do Gestor de [Recursos](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Há muitos [modelos na galeria](https://azure.microsoft.com/documentation/templates/?term=VM) que incluem o recurso VM. Nem todos os elementos que podem ser incluídos num modelo são descritos aqui.

 

Este exemplo mostra uma secção típica de recursos de um modelo para criar um número especificado de VMs:

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
>Este exemplo baseia-se numa conta de armazenamento que foi previamente criada. Pode criar a conta de armazenamento implantando-a a partir do modelo. O exemplo também se baseia numa interface de rede e nos seus recursos dependentes que seriam definidos no modelo. Estes recursos não são mostrados no exemplo.
>
>

## <a name="api-version"></a>Versão API

Quando implementa recursos usando um modelo, tem de especificar uma versão da API para utilizar. O exemplo mostra o recurso virtual da máquina utilizando este elemento de apiVersão:

```
"apiVersion": "2016-04-30-preview",
```

A versão da API que especifica no seu modelo afeta quais as propriedades que pode definir no modelo. Em geral, deve selecionar a versão API mais recente ao criar modelos. Para os modelos existentes, pode decidir se pretende continuar a utilizar uma versão API anterior ou atualizar o seu modelo para a versão mais recente para tirar partido de novas funcionalidades.

Use estas oportunidades para obter as versões API mais recentes:

- REST API - [Listar todos os fornecedores de recursos](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az provider show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parâmetros e variáveis

[Os parâmetros](../../resource-group-authoring-templates.md) facilitam-lhe a especificação dos valores para o modelo quando o executa. Esta secção de parâmetros é utilizada no exemplo:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Ao implementar o modelo de exemplo, introduz valores para o nome e senha da conta de administrador em cada VM e o número de VMs para criar. Tem a opção de especificar valores de parâmetros num ficheiro separado que é gerido com o modelo, ou fornecer valores quando solicitado.

[As variáveis](../../resource-group-authoring-templates.md) facilitam a configuração de valores no modelo que são usados repetidamente ao longo dele ou que podem mudar ao longo do tempo. Esta secção de variáveis é utilizada no exemplo:

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

Quando se implementa o modelo de exemplo, são utilizados valores variáveis para o nome e identificador da conta de armazenamento previamente criada. As variáveis também são usadas para fornecer as definições para a extensão de diagnóstico. Use as [melhores práticas para criar modelos](../../resource-manager-template-best-practices.md) de Gestor de Recursos Azure para ajudá-lo a decidir como quer estruturar os parâmetros e variáveis no seu modelo.

## <a name="resource-loops"></a>Loops de recursos

Quando precisa de mais do que uma máquina virtual para a sua aplicação, pode utilizar um elemento de cópia num modelo. Este elemento opcional passa através da criação do número de VMs que especificou como parâmetro:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Além disso, note no exemplo que o índice de loop é usado ao especificar alguns dos valores para o recurso. Por exemplo, se introduziu uma contagem de três exemplos, os nomes dos discos do sistema operativo são myOSDisk1, myOSDisk2 e myOSDisk3:

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

Tenha em mente que criar um loop para um recurso no modelo pode exigir que você use o loop ao criar ou aceder a outros recursos. Por exemplo, vários VMs não podem usar a mesma interface de rede, por isso, se o seu modelo circular através da criação de três VMs, também deve circular através da criação de três interfaces de rede. Ao atribuir uma interface de rede a um VM, o índice de loop é utilizado para identificá-lo:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Dependências

A maioria dos recursos depende de outros recursos para funcionar corretamente. As máquinas virtuais devem estar associadas a uma rede virtual e para isso precisam de uma interface de rede. O elemento [dependsOn](../../resource-group-define-dependencies.md) é utilizado para se certificar de que a interface de rede está pronta a ser utilizada antes da criação dos VMs:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

O Gestor de Recursos implanta paralelamente quaisquer recursos que não dependam de outro recurso que está a ser implantado. Tenha cuidado ao definir dependências porque pode, inadvertidamente, abrandar a sua implantação especificando dependências desnecessárias. As dependências podem acorrentar-se através de múltiplos recursos. Por exemplo, a interface de rede depende do endereço IP público e dos recursos de rede virtuais.

Como sabe se é necessária uma dependência? Olhe para os valores que definiu no modelo. Se um elemento na definição de recurso de máquina virtual apontar para outro recurso que é implantado no mesmo modelo, precisa de uma dependência. Por exemplo, o seu exemplo de máquina virtual define um perfil de rede:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Para definir esta propriedade, a interface de rede deve existir. Portanto, precisas de uma dependência. Também é necessário definir uma dependência quando um recurso (uma criança) é definido dentro de outro recurso (um progenitor). Por exemplo, as definições de diagnóstico e as extensões de script personalizadas são definidas como recursos infantis da máquina virtual. Não podem ser criados até que a máquina virtual exista. Portanto, ambos os recursos são marcados como dependentes da máquina virtual.

## <a name="profiles"></a>Perfis

Vários elementos de perfil são usados na definição de um recurso virtual da máquina. Alguns são necessários e outros são opcionais. Por exemplo, são necessários os elementos hardwareProfile, osProfile, storageProfile e networkProfile, mas o perfil de diagnóstico é opcional. Estes perfis definem definições como:
   
- [tamanho](sizes.md)
- [nome](/azure/architecture/best-practices/resource-naming) e credenciais
- [definições](cli-ps-findimage.md) de disco e sistema operativo
- [interface de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- diagnósticos de arranque

## <a name="disks-and-images"></a>Discos e imagens
   
Em Azure, os ficheiros VHD podem representar [discos ou imagens](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando o sistema operativo num ficheiro VHD é especializado para ser um VM específico, é referido como um disco. Quando o sistema operativo num ficheiro VHD é generalizado para ser usado para criar muitos VMs, é referido como uma imagem.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Criar novas máquinas virtuais e novos discos a partir de uma imagem da plataforma

Quando cria um VM, tem de decidir qual o sistema operativo a utilizar. O elemento ImageReference é utilizado para definir o sistema operativo de um novo VM. O exemplo mostra uma definição para um sistema operativo Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Se quiser criar um sistema operativo Linux, poderá utilizar esta definição:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

As definições de configuração do disco do sistema operativo são atribuídas com o elemento osDisk. O exemplo define um novo disco gerido com o modo de cache definido para **ReadWrite** e que o disco está a ser criado a partir de uma imagem da [plataforma:](cli-ps-findimage.md)

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Criar novas máquinas virtuais a partir de discos geridos existentes

Se pretender criar máquinas virtuais a partir de discos existentes, remova as imagensReferência e os elementos osPerfil e defina estas definições de disco:

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

Se pretender criar uma máquina virtual a partir de uma imagem gerida, altere o elemento ImageReference e defina estas definições de disco:

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

Pode adicionar opcionalmente discos de dados aos VMs. O [número de discos](sizes.md) depende do tamanho do disco do sistema operativo que utiliza. Com o tamanho dos VMs definidos para Standard_DS1_v2, o número máximo de discos de dados que poderiam ser adicionados aos mesmos é de dois. No exemplo, um disco de dados gerido está a ser adicionado a cada VM:

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

Embora [as extensões](extensions-features.md) sejam um recurso separado, estão intimamente ligadas a VMs. As extensões podem ser adicionadas como um recurso infantil do VM ou como um recurso separado. O exemplo mostra que a [extensão](extensions-diagnostics-template.md) de diagnóstico é adicionada aos VMs:

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

Este recurso de extensão utiliza a variável storageName e as variáveis de diagnóstico para fornecer valores. Se quiser alterar os dados recolhidos por esta extensão, pode adicionar mais contadores de desempenho à variável wadperfcounters. Também pode optar por colocar os dados de diagnóstico numa conta de armazenamento diferente da onde os discos VM são armazenados.

Existem muitas extensões que pode instalar num VM, mas o mais útil é provavelmente a [extensão do script personalizado](extensions-customscript.md). No exemplo, um script PowerShell chamado start.ps1 corre em cada VM quando começa:

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

O script start.ps1 pode realizar muitas tarefas de configuração. Por exemplo, os discos de dados adicionados aos VMs no exemplo não são inicializados; pode usar um script personalizado para inicializá-los. Se tiver várias tarefas de arranque para fazer, pode utilizar o ficheiro start.ps1 para ligar para outros scripts PowerShell no armazenamento Azure. O exemplo utiliza o PowerShell, mas pode utilizar qualquer método de script disponível no sistema operativo que está a utilizar.

Pode ver o estado das extensões instaladas a partir das definições de Extensões no portal:

![Obter estado de extensão](./media/template-description/virtual-machines-show-extensions.png)

Também pode obter informações de extensão utilizando o comando **Get-AzVMExtension** PowerShell, a **extensão vm obtém** comando Azure CLI ou a informação de **extensão Get** REST API.

## <a name="deployments"></a>Implementações

Ao implementar um modelo, o Azure rastreia os recursos que implementou em grupo e atribui automaticamente um nome a este grupo implantado. O nome da implantação é o mesmo que o nome do modelo.

Se está curioso sobre o estado dos recursos na implantação, consulte o grupo de recursos no portal Azure:

![Obtenha informações de implementação](./media/template-description/virtual-machines-deployment-info.png)
    
Não é um problema usar o mesmo modelo para criar recursos ou atualizar os recursos existentes. Quando utiliza comandos para implantar modelos, tem a oportunidade de dizer que [modo](../../resource-group-template-deploy.md) pretende utilizar. O modo pode ser definido para **completar** ou **incremental**. O padrão é fazer atualizações incrementais. Tenha cuidado ao utilizar o modo **Completo** porque pode acidentalmente apagar recursos. Quando define o modo de **completar,** o Gestor de Recursos elimina quaisquer recursos no grupo de recursos que não estejam no modelo.

## <a name="next-steps"></a>Passos Seguintes

- Crie o seu próprio modelo usando modelos de [Gestor de Recursos Do Azure da Autoria](../../resource-group-authoring-templates.md).
- Implemente o modelo que criou usando [Criar uma máquina virtual do Windows com um modelo de Gestor de Recursos](ps-template.md).
- Saiba como gerir os VMs que criou através da revisão [do Create e gerenciando os VMs do Windows com o módulo PowerShell Azure](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Para a sintaxe JSON e propriedades de tipos de recursos em modelos, consulte a referência do modelo do Gestor de [Recursos Azure](/azure/templates/).
