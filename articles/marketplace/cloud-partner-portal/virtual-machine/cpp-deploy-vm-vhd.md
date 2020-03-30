---
title: Implemente um VM dos seus VHDs para o Mercado Azure
description: Explica como registar um VM de um VHD implantado em Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: 5263d24c411ef8de4187c2fd750013374d779f04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277944"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Implante um VM dos seus VHDs

Esta secção explica como implantar uma máquina virtual (VM) a partir de um disco rígido virtual implantado em Azure (VHD).  Ele lista as ferramentas necessárias e como usá-las para criar uma imagem VM do utilizador e, em seguida, implantá-la para Azure usando scripts PowerShell.

Depois de ter carregado os seus discos rígidos virtuais (VHDs)— o vHD do sistema operativo generalizado e os VHDs de disco de dados zero ou mais - para a sua conta de armazenamento Azure, pode registá-los como uma imagem VM do utilizador. Então podes testar essa imagem. Uma vez que o vHD do seu sistema operativo é generalizado, não é possível implementar diretamente o VM fornecendo o URL VHD.

Para saber mais sobre as imagens vm, consulte as seguintes publicações de blog:

- [Imagem VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'How To'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Pré-requisito: instalar as ferramentas necessárias

Se ainda não o fez, instale o Azure PowerShell e o Azure CLI, utilizando as seguintes instruções:

- [Instalar a PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Passos da implementação

Utilizará os seguintes passos para criar e implementar uma imagem VM do utilizador:

1. Crie a imagem VM do utilizador, o que implica capturar e generalizar a imagem. 
2. Crie certificados e guarde-os num novo Cofre de Chaves Azure. É necessário um certificado para estabelecer uma ligação WinRM segura ao VM.  Um modelo de Gestor de Recursos Azure e um script Azure PowerShell são fornecidos. 
3. Implemente o VM a partir de uma imagem VM do utilizador, utilizando o modelo e script fornecidos.

Depois de implantado o VM, está pronto para certificar a [sua imagem VM](./cpp-certify-vm.md).

1. Clique em **Novo** e procure a **Implementação do Modelo**e, em seguida, selecione Construir o seu próprio modelo em **Editor**.  <br/>
   ![Construir modelo de implementação VHD no portal Azure](./media/publishvm_021.png)

1. Copie e cole este [modelo JSON](./cpp-deploy-json-template.md) no editor e clique em **Guardar**. <br/>
   ![Salvar o modelo de implementação vHD no portal Azure](./media/publishvm_022.png)

1. Forneça os valores do parâmetro para as páginas de propriedade de **implementação personalizada** saem.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parâmetro**              |   **Descrição**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome da conta de armazenamento do utilizador   | Nome da conta de armazenamento onde está localizado o VHD generalizado                    |
   | Nome do recipiente de armazenamento do utilizador | Nome do contentor onde se encontra o VHD generalizado                          |
   | Nome DNS para IP Público      | Nome público do DNS IP. O Nome DNS é do VM, irá defini-lo no Portal Azure, uma vez que a oferta é implementada.  |
   | Nome do utilizador do administrador             | Nome de utilizador da conta de administrador para novo VM                                  |
   | Palavra-passe de Administrador              | Senha da conta de administrador para novo VM                                  |
   | Tipo de SO                     | Sistema operativo VM: `Windows` \|`Linux`                                    |
   | ID da subscrição             | Identificador da subscrição selecionada                                      |
   | Localização                    | Localização geográfica da implantação                                        |
   | Tamanho da VM                     | [Tamanho VM Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)por exemplo`Standard_A2` |
   | Nome do endereço IP público      | Nome do seu endereço IP público                                               |
   | Nome da VM                     | Nome do novo VM                                                           |
   | Nome da rede virtual        | Nome da rede virtual utilizada pelo VM                                   |
   | Nome NIC                    | Nome do cartão de interface de rede que executa a rede virtual               |
   | VHD URL                     | URL VHD do disco completo                                                     |
   |  |  |
            
1. Depois de fornecer estes valores, clique em **Comprar**. 

O Azure começará a ser implantado: cria um novo VM com o VHD não gerido especificado, no caminho especificado da conta de armazenamento.  Pode acompanhar o progresso no portal Azure clicando em **Máquinas Virtuais** no lado esquerdo do portal.  Quando o VM for criado, o `Starting` `Running`estado mudará de . 


### <a name="deploy-a-vm-from-powershell"></a>Implementar um VM da PowerShell

Para implantar um VM grande a partir da imagem VM generalizada acabade ser criada, utilize os seguintes cmdlets.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Passos seguintes

Em seguida, irá [criar uma imagem VM do utilizador](cpp-create-user-image.md) para a sua solução.

