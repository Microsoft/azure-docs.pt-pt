---
title: Crie uma máquina virtual em Laboratórios DevTest com A PowerShell Azure
description: Aprenda a usar o Azure DevTest Labs para criar e gerir máquinas virtuais com o Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167115"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Crie uma máquina virtual com Laboratórios DevTest usando o Azure PowerShell
Este artigo mostra-lhe como criar uma máquina virtual em Azure DevTest Labs utilizando o Azure PowerShell. Pode usar scripts PowerShell para automatizar a criação de máquinas virtuais num laboratório em Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

- [Crie um laboratório](devtest-lab-create-lab.md) se não quiser usar um laboratório existente para testar o guião ou comandos neste artigo. 
- [Instale o Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) ou utilize a Azure Cloud Shell integrada no portal Azure. 

## <a name="powershell-script"></a>Script do PowerShell
O script da amostra nesta secção utiliza o cmdlet [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  Este cmdlet pega no ID de recursos do laboratório, nome da ação a executar e`createEnvironment`os parâmetros necessários para realizar essa ação. Os parâmetros estão numa tabela de hash que contém todas as propriedades de descrição da máquina virtual. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

As propriedades para a máquina virtual no script acima permitem-nos criar uma máquina virtual com o Windows Server 2016 DataCenter como O. Para cada tipo de máquina virtual, estas propriedades serão ligeiramente diferentes. A secção [de máquinas virtuais Define](#define-virtual-machine) mostra-lhe como determinar quais as propriedades a utilizar neste script.

O seguinte comando fornece um exemplo de execução do script guardado num nome de ficheiro: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definir máquina virtual
Esta secção mostra-lhe como obter as propriedades específicas de um tipo de máquina virtual que pretende criar. 

### <a name="use-azure-portal"></a>Utilizar o portal do Azure
Você pode gerar um modelo de Gestor de Recursos Azure ao criar um VM no portal Azure. Não precisa completar o processo de criação do VM. Só segue os passos até ver o modelo. Esta é a melhor maneira de obter a descrição json necessária se você ainda não tiver um VM de laboratório criado. 

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Selecione **Todos os Serviços** no menu de navegação à esquerda.
3. Procure e selecione **DevTest Labs** da lista de serviços. 
4. Na página de **DevTest Labs,** selecione o seu laboratório na lista de laboratórios.
5. Na página inicial do seu laboratório, selecione **+ Adicione** na barra de ferramentas. 
6. Selecione uma **imagem base** para o VM. 
7. Selecione opções de **automatização** na parte inferior da página acima do botão **Enviar.** 
8. Você vê o modelo de Gestor de **Recursos Azure** para criar a máquina virtual. 
9. O segmento JSON na secção **de recursos** tem a definição para o tipo de imagem que selecionou anteriormente. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Neste exemplo, você vê como obter uma definição de imagem Azure Market Place. Pode obter uma definição de imagem personalizada, uma fórmula ou um ambiente da mesma forma. Adicione quaisquer artefactos necessários para a máquina virtual e ajuste todas as definições avançadas necessárias. Depois de fornecer valores para os campos necessários, e quaisquer campos opcionais, antes de selecionar o botão de **opções de Automação.**

### <a name="use-azure-rest-api"></a>Use API de DESCANSO Azul
O seguinte procedimento dá-lhe passos para obter propriedades de uma imagem usando a API REST: Estes passos funcionam apenas para um VM existente em laboratório. 

1. Navegue para as [Máquinas Virtuais - página da lista,](/rest/api/dtl/virtualmachines/list) selecione **Experimente o** botão. 
2. Selecione a sua **subscrição Azure**.
3. Insira o **grupo de recursos para o laboratório.**
4. Insira o **nome do laboratório.** 
5. Selecione **Executar**.
6. Você vê as **propriedades para a imagem** baseada na qual o VM foi criado. 

## <a name="set-expiration-date"></a>Definir data de validade
Em cenários como treino, demonstrações e ensaios, pode querer criar máquinas virtuais e eliminá-las automaticamente após uma duração fixa para que não incorra em custos desnecessários. Pode definir uma data de validade para um VM enquanto o cria usando powerShell como mostrado na secção de [scripts PowerShell.](#powershell-script)

Aqui está um script de powerShell de amostra que define a data de validade para todos os VMs existentes em um laboratório:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Passos seguintes
Ver os seguintes conteúdos: [Documentação Azure PowerShell para A Azure DevTest Labs](/powershell/module/az.devtestlabs/)
