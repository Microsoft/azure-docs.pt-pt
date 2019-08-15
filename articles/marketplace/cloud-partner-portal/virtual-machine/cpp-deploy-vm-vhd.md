---
title: Implantar uma VM de seus VHDs para o Azure Marketplace
description: Explica como registrar uma VM por meio de um VHD implantado pelo Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 08/08/2019
ms.author: evansma
ms.openlocfilehash: 1aa946c813de41423d4fb2ba5b3aa5274db90f39
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934972"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Implantar uma VM de seus VHDs

Esta seção explica como implantar uma VM (máquina virtual) por meio de um VHD (disco rígido virtual) implantado pelo Azure.  Ele lista as ferramentas necessárias e como usá-las para criar uma imagem de VM de usuário e, em seguida, implantá-la no Azure usando scripts do PowerShell.

Depois de ter carregado seus VHDs (discos rígidos virtuais) — o VHD do sistema operacional generalizado e zero ou mais VHDs de disco de dados — para sua conta de armazenamento do Azure, você pode registrá-los como uma imagem de VM do usuário. Em seguida, você pode testar essa imagem. Como o VHD do sistema operacional é generalizado, não é possível implantar diretamente a VM fornecendo a URL do VHD.

Para saber mais sobre imagens de VM, Confira as postagens de blog a seguir:

- [Imagem da VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Instruções "como" do PowerShell de imagem de VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Pré-requisito: instalar as ferramentas necessárias

Se você ainda não tiver feito isso, instale Azure PowerShell e o CLI do Azure, usando as seguintes instruções:

- [Instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Passos da implementação

Você usará as seguintes etapas para criar e implantar uma imagem de VM do usuário:

1. Crie a imagem de VM do usuário, que envolve a captura e a generalização da imagem. 
2. Crie certificados e armazene-os em um novo Azure Key Vault. Um certificado é necessário para estabelecer uma conexão segura do WinRM com a VM.  Um modelo de Azure Resource Manager e um script de Azure PowerShell são fornecidos. 
3. Implante a VM de uma imagem de VM de usuário, usando o modelo e o script fornecidos.

Depois que a VM for implantada, você estará pronto para [certificar sua imagem de VM](./cpp-certify-vm.md).

1. Clique em **novo** e procure **implantação de modelo**e, em seguida, selecione **criar seu próprio modelo no editor**.  <br/>
   ![Criar modelo de implantação VHD no portal do Azure](./media/publishvm_021.png)

1. Copie e cole esse [modelo JSON](./cpp-deploy-json-template.md) no editor e clique em **salvar**. <br/>
   ![Salvar modelo de implantação VHD no portal do Azure](./media/publishvm_022.png)

1. Forneça os valores de parâmetro para as páginas de propriedades de **implantação personalizadas** exibidas.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parâmetro**              |   **Descrição**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome da conta de armazenamento do usuário   | Nome da conta de armazenamento em que o VHD generalizado está localizado                    |
   | Nome do contêiner de armazenamento do usuário | Nome do contêiner em que o VHD generalizado está localizado                          |
   | Nome DNS para IP público      | Nome DNS do IP público. O nome DNS é da VM, você irá defini-lo no portal do Azure, depois que a oferta for implantada.  |
   | Nome de utilizador administrador             | Nome de usuário da conta de administrador para Nova VM                                  |
   | Palavra-passe de Administrador              | Senha da conta de administrador para Nova VM                                  |
   | Tipo de SO                     | Sistema operacional da VM `Windows` : \|`Linux`                                    |
   | ID da subscrição             | Identificador da assinatura selecionada                                      |
   | Location                    | Localização geográfica da implantação                                        |
   | Tamanho da VM                     | [Tamanho da VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), por exemplo`Standard_A2` |
   | Nome do endereço IP público      | Nome do seu endereço IP público                                               |
   | Nome da VM                     | Nome da nova VM                                                           |
   | Nome da rede virtual        | Nome da rede virtual usada pela VM                                   |
   | Nome da NIC                    | Nome da placa de interface de rede que executa a rede virtual               |
   | URL DO VHD                     | URL do VHD do disco do so completo                                                     |
   |  |  |
            
1. Depois de fornecer esses valores, clique em **comprar**. 

O Azure iniciará a implantação: ele cria uma nova VM com o VHD não gerenciado especificado, no caminho da conta de armazenamento especificado.  Você pode acompanhar o progresso na portal do Azure clicando em **máquinas virtuais** no lado esquerdo do Portal.  Quando a VM tiver sido criada, o status será alterado de `Starting` para `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Implantar uma VM do PowerShell

Para implantar uma VM grande a partir da imagem de VM generalizada recém-criada, use os cmdlets a seguir.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Passos seguintes

Em seguida, você [criará uma imagem de VM do usuário](cpp-create-user-image.md) para sua solução.

