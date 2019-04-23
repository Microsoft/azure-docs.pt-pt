---
title: Criar uma máquina virtual no DevTest Labs com o Azure PowerShell | Documentos da Microsoft
description: Saiba como utilizar o Azure DevTest Labs para criar e gerir máquinas virtuais com o Azure PowerShell.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 0e68958070e9c35e12dd9446b351f880dfea6f69
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793254"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Criar uma máquina virtual com o DevTest Labs com o Azure PowerShell
Este artigo mostra-lhe como criar uma máquina virtual no Azure DevTest Labs com o Azure PowerShell. Pode utilizar scripts do PowerShell para automatizar a criação de máquinas virtuais num laboratório no Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

- [Criar um laboratório](devtest-lab-create-lab.md) se não pretender utilizar um laboratório existente para testar o script ou os comandos neste artigo. 
- [Instalar o Azure PowerShell](/powershell/azure/azurerm/other-install) ou utilizar o Azure Cloud Shell, que está integrado no portal do Azure. 

## <a name="powershell-script"></a>Script do PowerShell
O script de exemplo nesta secção utiliza o [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction) cmdlet.  Este cmdlet utiliza o ID de recurso do laboratório, nome da ação a efetuar (`createEnvironment`), e os parâmetros necessários executam essa ação. Os parâmetros estão numa tabela de hash que contém todas as propriedades de descrição de máquina virtual. 

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
        $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzureRmResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

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

    Invoke-AzureRmResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

As propriedades da máquina virtual no script acima permitem-nos criar uma máquina virtual com o Windows Server 2016 DataCenter como o sistema operacional. Para cada tipo de máquina virtual, estas propriedades serão ligeiramente diferentes. O [definir a máquina virtual](#define-virtual-machine) secção mostra como determinar quais propriedades para utilizar este script.

O comando seguinte fornece um exemplo de execução do script guardado num nome de ficheiro: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definir a máquina virtual
Esta secção mostra como obter as propriedades que são específicas de um tipo de máquina virtual que pretende criar. 

### <a name="use-azure-portal"></a>Utilizar o portal do Azure
Pode gerar um modelo Azure Resource Manager ao criar uma VM no portal do Azure. Não precisa de concluir o processo de criação da VM. Apenas, siga os passos até ver o modelo. Esta é a melhor forma de obter a descrição do JSON necessária se ainda não tiver um laboratório de que VM criada. 

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu de navegação à esquerda.
3. Procure e selecione **DevTest Labs** na lista de serviços. 
4. Sobre o **DevTest Labs** , selecione o seu laboratório na lista de laboratórios.
5. Na home page para seu laboratório, selecione **+ adicionar** na barra de ferramentas. 
6. Selecione um **imagem base** para a VM. 
7. Selecione **opções de automatização** na parte inferior da página acima a **submeter** botão. 
8. Verá o **modelo Azure Resource Manager** para criar a máquina virtual. 
9. O segmento JSON na **recursos** secção tem a definição para o tipo de imagem que selecionou anteriormente. 

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

Neste exemplo, verá como obter uma definição de uma imagem do Azure. Pode obter uma definição de uma imagem personalizada, uma fórmula ou um ambiente da mesma forma. Adicione quaisquer artefactos necessários para a máquina virtual e defina as definições avançadas necessárias. Depois de fornecer valores para os campos obrigatórios e os campos opcionais, antes selecionando o **opções de automatização** botão.

### <a name="use-azure-rest-api"></a>Utilizar a API REST do Azure
O procedimento seguinte dá-lhe os passos para obter as propriedades de uma imagem com a API REST: Estes passos funcionam apenas para uma VM existente num laboratório. 

1. Navegue para o [lista de máquinas virtuais -](/rest/api/dtl/virtualmachines/list) página, selecione **experimente** botão. 
2. Selecione a sua **subscrição do Azure**.
3. Introduza o **grupo de recursos para o laboratório**.
4. Introduza o **nome do laboratório**. 
5. Selecione **Executar**.
6. Verá o **propriedades da imagem de** com base no qual a VM foi criada. 



## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte conteúdo: [Documentação do PowerShell do Azure para o Azure DevTest Labs](/powershell/module/az.devtestlabs/)
