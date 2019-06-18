---
title: Criar uma VM do Azure gerido a partir de um VHD generalizado no local | Documentos da Microsoft
description: Carregar um VHD generalizado para o Azure e utilizá-lo para criar novas VMs, no modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: ee2fe91d915faf7e09dee004891edfc6bef38d6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685407"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e utilizá-lo para criar novas VMs no Azure

Este artigo orienta-o através da utilização do PowerShell para carregar um VHD de uma VM generalizada no Azure, criar uma imagem de VHD e criar uma nova VM a partir dessa imagem. Pode carregar um VHD exportado a partir de uma ferramenta de virtualização no local ou de outra cloud. Usando [Managed Disks](managed-disks-overview.md) para a nova VM simplifica o gerenciamento de VM e fornece a melhor disponibilidade quando a VM é colocada no conjunto de disponibilidade. 

Para um script de exemplo, consulte [exemplo de script para carregar um VHD para o Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD para o Azure, deve seguir [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Revisão [planear a migração para os Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para o [Managed Disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizar a VM de origem utilizando o Sysprep

O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para obter detalhes sobre o Sysprep, consulte a [visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Certifique-se de que as funções de servidor em execução na máquina são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se pretender executar o Sysprep antes de carregar o VHD para o Azure pela primeira vez, certifique-se de que tem [preparou a sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Inicie sessão para a máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Altere o diretório para % windir%\system32\sysprep e, em seguida, execute `sysprep.exe`.
3. Na **ferramenta System Preparation** caixa de diálogo, selecione **Enter System Out-of-Box Experience (OOBE)** e certifique-se de que o **Generalize** caixa de verificação está ativada.
4. Para **opções de encerramento**, selecione **encerramento**.
5. Selecione **OK**.
   
    ![Inicie Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando Sysprep estiver concluída, encerra a máquina virtual. Não reinicie a VM.


## <a name="get-a-storage-account"></a>Obtenha uma conta de armazenamento

Precisará de uma conta de armazenamento no Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Se usará o VHD para criar um disco gerido para uma VM, a localização da conta de armazenamento tem de ser a mesma localização onde irá criar a VM.

Para mostrar as contas de armazenamento disponível, introduza:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD para a conta de armazenamento

Utilize o [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) cmdlet para carregar o VHD para um contentor na conta de armazenamento. Este exemplo carrega o ficheiro *myVHD.vhd* partir *discos rígidos de C:\Users\Public\Documents\Virtual\\*  para uma conta de armazenamento com o nome *mystorageaccount* no o *myResourceGroup* grupo de recursos. O ficheiro será colocado no contentor com o nome *mycontainer* e o novo nome de ficheiro serão *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se tiver êxito, receberá uma resposta que é semelhante a este:

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

Dependendo da sua ligação de rede e o tamanho do ficheiro VHD, este comando pode demorar algum tempo a concluir.

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
 
Também pode carregar um VHD para a sua conta de armazenamento através de um dos seguintes procedimentos:

- [AZCopy](https://aka.ms/downloadazcopy)
- [Blob de cópia de armazenamento do Azure API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Blobs de carregar o Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
- [Referência da API do REST do armazenamento de importação/exportação serviço](https://msdn.microsoft.com/library/dn529096.aspx)
-   Recomendamos que utilize o serviço importar/exportar se estimado tempo a carregar tem mais de sete dias. Pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) estimar o tempo da unidade de tamanho e a transferência de dados. 
    Importação/exportação pode ser utilizada para copiar para uma conta de armazenamento standard. Terá de copiar do armazenamento standard para a conta de armazenamento premium com uma ferramenta como o AzCopy.

> [!IMPORTANT]
> Se estiver a utilizar o AzCopy para carregar o VHD para o Azure, certifique-se de que definiu [ **/BlobType:page** ](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) antes de executar o script de carregamento. Se o destino é um blob e esta opção não for especificada, por predefinição, o AzCopy cria um blob de blocos.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerida a partir do VHD carregado 

Crie uma imagem gerida a partir de seu VHD do SO generalizado. Substitua os seguintes valores com as suas próprias informações.


Em primeiro lugar, defina alguns parâmetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Crie a imagem com o VHD do SO generalizado.

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

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem. Este exemplo cria uma VM com o nome *myVM* partir *myImage*, na *myResourceGroup*.


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

Inicie sessão na sua nova máquina virtual. Para obter mais informações, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure a executar o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

