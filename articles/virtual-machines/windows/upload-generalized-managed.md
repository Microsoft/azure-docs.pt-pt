---
title: Criar uma VM do Azure gerenciada a partir de um VHD local generalizado | Microsoft Docs
description: Carregue um VHD generalizado no Azure e use-o para criar novas VMs no modelo de implantação do Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: be3ccfd0c562763d0968398ddb042dc5f07dbdcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101566"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure

Este artigo explica como usar o PowerShell para carregar um VHD de uma VM generalizada para o Azure, criar uma imagem do VHD e criar uma nova VM a partir dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização local ou de outra nuvem. O uso de [Managed disks](managed-disks-overview.md) para a nova VM simplifica o gerenciamento de VM e fornece melhor disponibilidade quando a VM é colocada em um conjunto de disponibilidade. 

Para obter um script de exemplo, consulte [script de exemplo para carregar um VHD no Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD no Azure, você deve seguir [preparar um VHD do Windows ou VHDX para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Examine [o plano para a migração para Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [Managed disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizar a VM de origem usando o Sysprep

O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para obter detalhes sobre o Sysprep, consulte a [visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador têm suporte pelo Sysprep. Para obter mais informações, consulte [suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se você planeja executar o Sysprep antes de carregar o VHD no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Entre na máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Altere o diretório para%WINDIR%\system32\sysprep e execute `sysprep.exe`.
3. Na caixa de diálogo **ferramenta de preparação do sistema** , selecione entrar no **OOBE (experiência inicial do sistema)** e verifique se a caixa de seleção generalizar está habilitada.
4. Para **Opções**de desligamento, selecione **desligar**.
5. Selecione **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep é concluído, ele desliga a máquina virtual. Não reinicie a VM.


## <a name="get-a-storage-account"></a>Obter uma conta de armazenamento

Você precisará de uma conta de armazenamento no Azure para armazenar a imagem de VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Se você estiver usando o VHD para criar um disco gerenciado para uma VM, o local da conta de armazenamento deverá ser o mesmo local em que você criará a VM.

Para mostrar as contas de armazenamento disponíveis, digite:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD em sua conta de armazenamento

Use o cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para carregar o VHD em um contêiner em sua conta de armazenamento. Este exemplo carrega o arquivo *myVHD. vhd* de *\\ discos rígidos C:\Users\Public\Documents\Virtual* para uma conta de armazenamento chamada *mystorageaccount* no grupo de recursos MyResource Group. O arquivo será colocado no contêiner chamado MyContainer e o novo nome de arquivo será *myUploadedVHD. vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se for bem-sucedido, você receberá uma resposta semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da sua conexão de rede e do tamanho do arquivo VHD, esse comando pode demorar um pouco para ser concluído.

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
 
Você também pode carregar um VHD para sua conta de armazenamento usando um dos seguintes:

- [AZCopy](https://aka.ms/downloadazcopy)
- [API do blob de cópia do armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Gerenciador de Armazenamento do Azure carregar BLOBs](https://azurestorageexplorer.codeplex.com/)
- [Referência da API REST do serviço de importação/exportação do armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)
-   É recomendável usar o serviço de importação/exportação se o tempo de carregamento estimado for maior que sete dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo do tamanho dos dados e da unidade de transferência. 
    A importação/exportação pode ser usada para copiar para uma conta de armazenamento padrão. Você precisará copiar do armazenamento Standard para a conta de armazenamento Premium usando uma ferramenta como AzCopy.

> [!IMPORTANT]
> Se você estiver usando o AzCopy para carregar o VHD no Azure, certifique-se de ter definido [ **/BlobType: Page**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) antes de executar o script de carregamento. Se o destino for um blob e essa opção não for especificada, por padrão, o AzCopy criará um blob de blocos.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerenciada do VHD carregado 

Crie uma imagem gerenciada do seu VHD de so generalizado. Substitua os seguintes valores por suas próprias informações.


Primeiro, defina alguns parâmetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Crie a imagem usando o VHD do sistema operacional generalizado.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Crie a VM

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem. Este exemplo cria uma VM chamada *myVM* apartir de MyImage, no MyResource.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Passos Seguintes

Entre em sua nova máquina virtual. Para obter mais informações, consulte [como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

