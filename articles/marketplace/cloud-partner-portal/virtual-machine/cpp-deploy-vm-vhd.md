---
title: Implementar uma VM a partir dos VHDs para o Azure Marketplace | Documentos da Microsoft
description: Explica como registar uma VM a partir de um VHD implementadas no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: a5b4e0465e83dec5620954998db329e7902fb59c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106054"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Implementar uma VM a partir dos VHDs

Esta secção explica como implementar uma máquina virtual (VM) a partir de um Azure implementadas com o disco rígido virtual (VHD).  Esta tabela indica as ferramentas necessárias e como usá-los para criar uma imagem de VM do utilizador, em seguida, implementá-la para o Azure com scripts do PowerShell.

Depois de carregar os discos rígidos virtuais (VHDs) — o sistema de operativo VHD generalizado e zero ou mais VHDs de discos, à sua conta de armazenamento do Azure, pode registá-los como uma imagem de VM do utilizador. Em seguida, pode testar essa imagem. Uma vez que o sistema de operativo VHD é generalizado, é possível implementar diretamente a VM, fornecendo o URL de VHD.

Para mais informações sobre imagens VM, consulte as seguintes mensagens de blogue:

- [Imagem de VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [PowerShell de imagem VM "Como"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="prerequisite-install-the-necessary-tools"></a>Pré-requisito: instalar as ferramentas necessárias

Se ainda não o fez, instale o Azure PowerShell e CLI do Azure, com as instruções seguintes:

- [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Passos da implementação

Irá utilizar os seguintes passos para criar e implementar uma imagem de VM do utilizador:

1. Crie a imagem VM do utilizador, o que envolve a capturar e generalizar a imagem. 
2. Criar certificados e armazená-los num novo cofre de chaves do Azure. Um certificado é necessário para estabelecer uma ligação segura de WinRM para a VM.  Um modelo Azure Resource Manager e um script do PowerShell do Azure são fornecidos. 
3. Implemente a VM a partir de uma imagem de VM do utilizador, com o modelo fornecido e o script.

Depois da VM é implementada, está pronto para [certificar a sua imagem VM](./cpp-certify-vm.md).

1. Clique em **New** e procure **implementação do modelo**, em seguida, selecione **criar seu próprio modelo no Editor**.  <br/>
   ![Criar modelo de implementação do VHD no portal do Azure](./media/publishvm_021.png)

1. Copiar e colar isto [modelo JSON](./cpp-deploy-json-template.md) para o editor e clique em **guardar**. <br/>
   ![Guardar o modelo de implementação do VHD no portal do Azure](./media/publishvm_022.png)

1. Forneça os valores de parâmetro para apresentados **implementação personalizada** páginas de propriedades.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parâmetro**              |   **Descrição**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome de conta de armazenamento do utilizador   | Nome de conta de armazenamento onde está localizado um VHD generalizado                    |
   | Nome do contentor de armazenamento de usuário | Nome do contentor onde está localizado um VHD generalizado                          |
   | Nome DNS para o IP público      | Nome DNS de IP público                                                           |
   | Nome de utilizador administrador             | Nome de utilizador da conta de administrador para a nova VM                                  |
   | Palavra-passe de Administrador              | Palavra-passe de conta de administrador para a nova VM                                  |
   | Tipo de SO                     | Sistema de operativo da VM: `Windows` \| `Linux`                                    |
   | ID da subscrição             | Identificador da subscrição selecionada                                      |
   | Localização                    | Localização geográfica da implementação                                        |
   | Tamanho da VM                     | [Tamanho VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), por exemplo `Standard_A2` |
   | Nome do endereço IP público      | Nome do seu endereço IP público                                               |
   | Nome da VM                     | Nome da nova VM                                                           |
   | Nome da rede virtual        | Nome da rede virtual utilizada pela VM                                   |
   | Nome do NIC                    | Nome do cartão de interface de rede com a rede virtual               |
   | URL DE VHD                     | Concluir o URL de VHD de disco do SO                                                     |
   |  |  |
            
1. Depois de fornecer estes valores, clique em **Compra**. 

Azure começará a implementação: ele cria uma nova VM com o VHD não gerido especificado, no caminho de conta de armazenamento especificada.  Pode acompanhar o progresso no portal do Azure ao clicar em **máquinas virtuais** no lado esquerdo do portal.  Quando a VM tiver sido criada, o estado será alterado de `Starting` para `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Implementar uma VM a partir do PowerShell

Para implementar uma VM grande a partir da imagem de VM generalizada acabou de criar, utilize os seguintes cmdlets.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Passos Seguintes

Em seguida, irá [criar uma imagem de VM do utilizador](cpp-create-user-image.md) para a sua solução.

