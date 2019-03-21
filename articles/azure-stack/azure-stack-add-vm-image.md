---
title: Adicionar e remover uma imagem de VM para o Azure Stack | Documentos da Microsoft
description: Adicionar ou Remover imagem personalizada do Windows ou Linux VM da sua organização para os inquilinos utilizarem.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: ccf3beaacd15ad7d3e9177614bb62b0050bd8d5c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109171"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Disponibilizar uma imagem de máquina virtual no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

No Azure Stack, é possível disponibilizar imagens de máquinas virtuais aos seus utilizadores. Estas imagens podem ser utilizadas por modelos Azure Resource Manager. Também pode adicioná-los na interface do usuário do Azure Marketplace como um item do mercado. Utilize um formulário com imagem global do Azure Marketplace ou sua própria imagem personalizada. A imagem pode ser adicionada com o portal ou o Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Adicionar uma imagem de VM através do portal

> [!NOTE]  
> Com esse método, tem de criar o item do Marketplace em separado.

Imagens tem de ser capazes de ser referenciado por um URI de armazenamento de Blobs. Preparar uma imagem de sistema operativo Windows ou Linux no formato VHD (não VHDX) e, em seguida, carregue a imagem para uma conta de armazenamento no Azure ou no Azure Stack. Se a sua imagem já foi carregada para o armazenamento de BLOBs no Azure ou no Azure Stack, pode ignorar o passo 1.

1. [Carregar uma imagem de VM do Windows para o Azure para implementações do Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, para uma imagem do Linux, siga as instruções descritas [máquinas virtuais Linux de implementar no Azure Stack](azure-stack-linux.md). Antes de carregar a imagem, é importante considerar os seguintes fatores:

   - O Azure Stack apenas suporta a geração formatar de VM um (1) no VHD de disco fixo. O formato fixo estrutura o disco lógico linearmente dentro do arquivo, para que esse deslocamento do disco X é armazenado no deslocamento do blob X. Um rodapé pequeno no final do blob descreve as propriedades do VHD. Para confirmar se o disco é fixa, utilize o [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando do PowerShell.  

     > [!IMPORTANT]  
     >  O Azure Stack não suporta os VHDs de discos dinâmicos. Redimensionar um disco dinâmico, que está ligado a uma VM deixará a VM no estado de falha. Para atenuar este problema, elimine a VM sem eliminar disco da VM, um blob VHD numa conta de armazenamento. O, converter o VHD a partir de um disco dinâmico para um disco fixo e, em seguida, voltar a criar a máquina virtual.

   - É mais eficiente para carregar uma imagem para o armazenamento de Blobs do Azure Stack que para o Azure blob storage porque demora menos tempo para enviar a imagem para o repositório de imagens do Azure Stack.

   - Quando carrega o [imagem de VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), certifique-se de substituir o **iniciar sessão no Azure** passo a passo com o [configurar o ambiente do PowerShell da operadora do Azure Stack](azure-stack-powershell-configure-admin.md) passo.  

   - Tome nota do armazenamento de Blobs do URI onde carregar a imagem. O URI de armazenamento de BLOBs tem o seguinte formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*. vhd.

   - Para tornar o blob acessíveis anonimamente, vá para o contentor de BLOBs da conta de armazenamento onde o VHD da imagem VM foi carregado. Selecione **Blob**e, em seguida, selecione **política de acesso**. Opcionalmente, pode gerar uma assinatura de acesso partilhado do contentor e incluí-lo como parte do URI de blob. Este passo torna-se de que o blob está disponível para ser usado para adicionar isso como uma imagem. Se o blob não está acessível anonimamente, será criada para a imagem de VM no estado de falha.

     ![Ir para os blobs de conta de armazenamento](./media/azure-stack-add-vm-image/image1.png)

     ![Acesso de blob de conjunto para o público](./media/azure-stack-add-vm-image/image2.png)

2. Inicie sessão no Azure Stack como operador. No menu, selecione **todos os serviços** > **imagens** sob **computação** > **adicionar**.

3. Sob **criar imagem**, introduza a localização de nome, subscrição, grupo de recursos, um disco do SO, o tipo de SO, o URI de blob de armazenamento, o tipo de conta e a colocação em cache do anfitrião. Em seguida, selecione **criar** para começar a criar a imagem de VM.

   ![Comece a criar a imagem](./media/azure-stack-add-vm-image/image4.png)

   Quando a imagem é criada com êxito, o estado de imagem VM é alterado para **bem-sucedido**.

4. Para fazer com que a imagem de máquina virtual mais prontamente disponíveis para consumo dos utilizadores na interface do Usuário, é uma boa idéia [criar um item do mercado](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Remover uma imagem de VM através do portal

1. Abra o portal de administração na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Selecione **gestão de Marketplace**e, em seguida, selecione a VM que pretende eliminar.

3. Clique em **Eliminar**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Adicionar uma imagem de VM no Marketplace com o PowerShell

> [!Note]  
> Quando adiciona uma imagem só estará disponível para o Azure Resource Manager com base em modelos e implementações do PowerShell. Para disponibilizar uma imagem para uma usuários como um item do mercado, publicar o item do marketplace através dos passos neste artigo, [criar e publicar um item do mercado](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-and-publish-marketplace-item)

1. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).  

2. Inicie sessão no Azure Stack como um operador. Para obter instruções, consulte [inicie sessão no Azure Stack como um operador](azure-stack-powershell-configure-admin.md).

3. Abra o PowerShell com uma linha de comandos elevada e execute:

   ```PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   O **Add-AzsPlatformimage** cmdlet especifica valores utilizados por modelos do Azure Resource Manager para fazer referência a imagem de VM. Os valores incluem:
   - **publisher**  
     Por exemplo: `Canonical`  
     O segmento de nome de publicador da imagem VM que os utilizadores utilizam quando implementam a imagem. Um exemplo é **Microsoft**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **offer**  
     Por exemplo: `UbuntuServer`  
     O segmento de nome de oferta da imagem VM que os utilizadores utilizam quando implementam a imagem de VM. Um exemplo é **WindowsServer**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **sku**  
     Por exemplo: `14.04.3-LTS`  
     O segmento de nome SKU da imagem de VM que os utilizadores utilizam quando implementam a imagem de VM. Um exemplo é **Datacenter2016**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **version**  
     Por exemplo: `1.0.0`  
     A versão da imagem de VM que os utilizadores utilizam quando implementam a imagem de VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **osType**  
     Por exemplo: `Linux`  
     O osType da imagem tem de ser **Windows** ou **Linux**.  
   - **OSUri**  
     Por exemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Pode especificar um URI de armazenamento de BLOBs para uma `osDisk`.  

     Para obter mais informações, consulte a referência do PowerShell para o [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet e os [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Adicionar uma imagem VM personalizada para o mercado com o PowerShell
 
1. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).

   ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. Se utilizar **serviços de Federação do Active Directory**, utilize o seguinte cmdlet:

   ```PowerShell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
   Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Inicie sessão no Azure Stack como um operador. Para obter instruções, consulte [inicie sessão no Azure Stack como um operador](azure-stack-powershell-configure-admin.md).

4. Crie uma conta de armazenamento no global Azure ou no Azure Stack para armazenar a imagem de VM personalizada. Para obter instruções, consulte [início rápido: Carregar, transferir e listar blobs através do portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Preparar uma imagem de sistema operativo Windows ou Linux no formato VHD (não VHDX), carregue a imagem à sua conta de armazenamento e obter o URI onde a imagem de VM pode ser obtida pelo PowerShell.  

   ```PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

6. (Opcionalmente) Pode carregar uma matriz de discos de dados como parte da imagem VM. Crie os seus discos de dados com o cmdlet New-DataDiskObject. Abra o PowerShell a partir de uma linha de comandos elevada e execute:

   ```PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Abra o PowerShell com uma linha de comandos elevada e execute:

   ```PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Para obter mais informações sobre o cmdlet Add-AzsPlatformimage e o cmdlet New-DataDiskObject, consulte o Microsoft PowerShell [documentação do módulo de operador do Azure Stack](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Remover uma imagem de VM com o PowerShell

Quando já não precisar da imagem de máquina virtual que carregou, pode eliminá-lo no Marketplace ao utilizar o seguinte cmdlet:

1. [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).

2. Inicie sessão no Azure Stack como um operador.

3. Abra o PowerShell com uma linha de comandos elevada e execute:

   ```PowerShell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   O **Remove-AzsPlatformImage** cmdlet especifica valores utilizados por modelos do Azure Resource Manager para fazer referência a imagem de VM. Os valores incluem:
   - **publisher**  
     Por exemplo: `Canonical`  
     O segmento de nome de publicador da imagem VM que os utilizadores utilizam quando implementam a imagem. Um exemplo é **Microsoft**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **offer**  
     Por exemplo: `UbuntuServer`  
     O segmento de nome de oferta da imagem VM que os utilizadores utilizam quando implementam a imagem de VM. Um exemplo é **WindowsServer**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **sku**  
     Por exemplo: `14.04.3-LTS`  
     O segmento de nome SKU da imagem de VM que os utilizadores utilizam quando implementam a imagem de VM. Um exemplo é **Datacenter2016**. Inclui um espaço nem outros caracteres especiais neste campo.  
   - **version**  
     Por exemplo: `1.0.0`  
     A versão da imagem de VM que os utilizadores utilizam quando implementam a imagem de VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Inclui um espaço nem outros caracteres especiais neste campo.  
    
     Para obter mais informações sobre o cmdlet Remove-AzsPlatformImage, consulte o Microsoft PowerShell [documentação do módulo de operador do Azure Stack](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Passos Seguintes

[Aprovisionar uma máquina virtual](azure-stack-provision-vm.md)
