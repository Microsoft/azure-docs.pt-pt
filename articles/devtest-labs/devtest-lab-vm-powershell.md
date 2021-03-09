---
title: Criar uma máquina virtual em DevTest Labs com Azure PowerShell
description: Aprenda a usar a Azure DevTest Labs para criar e gerir máquinas virtuais com a Azure PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499259"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Crie uma máquina virtual com DevTest Labs usando Azure PowerShell
Este artigo mostra-lhe como criar uma máquina virtual em Azure DevTest Labs usando a Azure PowerShell. Pode utilizar scripts PowerShell para automatizar a criação de máquinas virtuais num laboratório em Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

- [Crie um laboratório](devtest-lab-create-lab.md) se não quiser usar um laboratório existente para testar o script ou comandos neste artigo. 
- [Instale a Azure PowerShell](/powershell/azure/install-az-ps) ou utilize a Azure Cloud Shell que está integrada no portal Azure. 

## <a name="powershell-script"></a>Script do PowerShell
O script da amostra nesta secção utiliza o [cmdlet Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction)  Este cmdlet requer o ID de recursos do laboratório, o nome da ação a executar ( `createEnvironment` ) e os parâmetros necessários para executar essa ação. Os parâmetros estão numa tabela de haxixe que contém todas as propriedades de descrição da máquina virtual. 

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

As propriedades da máquina virtual no script acima permitem-nos criar uma máquina virtual com o Windows Server 2016 DataCenter como o SISTEMA. Para cada tipo de máquina virtual, estas propriedades serão ligeiramente diferentes. A secção [de máquina virtual Define](#define-virtual-machine) mostra-lhe como determinar quais as propriedades a utilizar neste script.

O seguinte comando fornece um exemplo de execução do script guardado num nome de ficheiro: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definir máquina virtual
Esta secção mostra-lhe como obter as propriedades específicas de um tipo de máquina virtual que pretende criar. 

### <a name="use-azure-portal"></a>Utilizar o portal do Azure
Pode gerar um modelo de Gestor de Recursos Azure ao criar um VM no portal Azure. Não é preciso concluir o processo de criação do VM. Só segue os passos até ver o modelo. Esta é a melhor maneira de obter a descrição necessária do JSON se ainda não tiver um VM de laboratório criado. 

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Selecione **Todos os Serviços** no menu de navegação à esquerda.
3. Procure e selecione **DevTest Labs** da lista de serviços. 
4. Na página **de Laboratórios DevTest,** selecione o seu laboratório na lista de laboratórios.
5. Na página inicial do seu laboratório, selecione **+ Adicione** na barra de ferramentas. 
6. Selecione uma **imagem base** para o VM. 
7. Selecione opções de **automatização** na parte inferior da página acima do botão **Enviar.** 
8. Você vê o **modelo Azure Resource Manager** para criar a máquina virtual. 
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

Neste exemplo, você vê como obter uma definição de uma imagem de Azure Market Place. Você pode obter uma definição de uma imagem personalizada, uma fórmula, ou um ambiente da mesma forma. Adicione todos os artefactos necessários para a máquina virtual e defina as definições avançadas necessárias. Depois de fornecer valores para os campos necessários, e quaisquer campos opcionais, antes de selecionar o botão **de opções de Automação.**

### <a name="use-azure-rest-api"></a>Use Azure REST API
O procedimento a seguir dá-lhe passos para obter propriedades de uma imagem utilizando a API REST: Estes passos funcionam apenas para um VM existente em laboratório. 

1. Navegue para as [Máquinas Virtuais - listar](/rest/api/dtl/virtualmachines/list) página, selecione **Tente botão.** 
2. Selecione a sua **subscrição Azure**.
3. Introduza o **grupo de recursos para o laboratório.**
4. Insira o **nome do laboratório.** 
5. Selecione **Executar**.
6. Vê-se as **propriedades da imagem** com base na qual o VM foi criado. 

## <a name="set-expiration-date"></a>Definir data de validade
Em cenários como treino, demonstrações e testes, pode querer criar máquinas virtuais e eliminá-las automaticamente após uma duração fixa para que não incorram em custos desnecessários. Pode definir uma data de validade para um VM enquanto o cria utilizando o PowerShell, como mostrado na secção [de scripts PowerShell](#powershell-script) exemplo.

Aqui está uma amostra do script PowerShell que define a data de validade para todos os VMs existentes em um laboratório:

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
Ver o seguinte conteúdo: [Azure PowerShell documentação para Azure DevTest Labs](/powershell/module/az.devtestlabs/)
