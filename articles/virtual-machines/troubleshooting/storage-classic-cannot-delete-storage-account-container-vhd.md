---
title: Resolver erros ao eliminar contas de armazenamento clássicas do Azure, contentores ou VHDs | Documentos da Microsoft
description: Como resolver problemas ao eliminar os recursos de armazenamento com VHDs ligadas.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078164"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Resolver problemas de erros de eliminação de recursos de armazenamento clássicas
Este artigo fornece orientações de resolução de problemas quando um dos seguintes erros ocorre ao tentar eliminar a conta de armazenamento clássico do Azure, contentor ou VHD; ficheiro de BLOBs de página. 


Este artigo apenas aborda problemas com os recursos de armazenamento clássicas. Se um utilizador eliminar uma máquina virtual clássica no portal do Azure, PowerShell ou CLI, em seguida, os discos não são eliminados automaticamente. O utilizador obtém a opção de eliminar o recurso de "Disco". No caso da opção não estiver selecionada, o recurso de "Disco" irá impedir a eliminação de conta de armazenamento, contentor e o arquivo de blob de página real de *. vhd.

Podem encontrar mais informações acerca dos discos do Azure [aqui](../../virtual-machines/windows/managed-disks-overview.md). Azure impede a eliminação de um disco que está ligado a uma VM para evitar danos. Ele também impede a eliminação de contentores e contas de armazenamento, que têm um blob de página que está ligado a uma VM. 

## <a name="what-is-a-disk"></a>O que é um "disco"?
Um recurso de "Disco" é usado para montar um ficheiro de BLOBs de página *. vhd para uma máquina virtual, como um disco de SO ou disco de dados. Um disco de SO ou o recurso de disco de dados, até eliminadas, irá continuar a manter uma concessão no arquivo *. vhd. Não é possível eliminar qualquer recurso de armazenamento no caminho abaixo na imagem, se um recurso de "Disco" aponta para ela.

![Abra a captura de ecrã do portal, com o painel de "Property" do disco (clássico)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Passos ao eliminar uma máquina virtual clássica 
1. Elimine a máquina virtual clássica.
2. Se a caixa de verificação "Discos" estiver selecionada, o **concessão de disco** (mostrado na imagem acima) associados com o blob de página *. vhd foi interrompida. O ficheiro de VHD de blob de página real continuarão a existir na conta de armazenamento.
![Abra a captura de ecrã do portal, com o painel de erro "Delete" da máquina virtual (clássico)](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Assim que a concessão de disco (s) for interrompida, os BLOBs de página em si podem ser eliminados. Uma conta de armazenamento ou contentor pode ser eliminado depois de todos os recursos de "Disco" presentes nos mesmos são eliminados.

>[!NOTE] 
>Se o utilizador elimina a VM, mas não o VHD, os custos de armazenamento irão continuar a acumular no ficheiro de VHD de blob de página. Os encargos serão em conformidade com o tipo de conta de armazenamento, verifique os [página de preços](https://azure.microsoft.com/en-us/pricing/details/storage/) para obter mais detalhes. Se o utilizador já não tenciona utilizar o VHD, elimine it /-los para evitar futuras Cobranças. 

## <a name="unable-to-delete-storage-account"></a>Não é possível eliminar a conta de armazenamento 

Quando o utilizador tentar eliminar uma conta de armazenamento clássico que não é mais necessária, o utilizador poderá ver o seguinte comportamento.

#### <a name="azure-portal"></a>Portal do Azure 
Utilizador navega para a conta de armazenamento clássico no [portal do Azure](https://portal.azure.com) e clica **eliminar**, utilizador verá a seguinte mensagem: 

Com disco (s) "anexado" a uma máquina virtual

![Abra a captura de ecrã do portal, com o painel de erro "Delete" da máquina virtual (clássico)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Com disco (s) "ligados" a uma máquina virtual

![Abra a captura de ecrã do portal, com o painel de erro não "Delete" da máquina virtual (clássico)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
O utilizador tenta eliminar uma conta de armazenamento, que já não está a ser utilizada, utilizando cmdlets do PowerShell clássico. Utilizador verá a seguinte mensagem:

> <span style="color:cyan">**Remove-AzureStorageAccount - StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Myclassicaccount de conta de armazenamento tem algumas imagens ativas e/ou discos, por exemplo  
> myclassicaccount. Certifique-se de que estas imagens e/ou discos são removidos antes de eliminar esta conta de armazenamento.</span>

## <a name="unable-to-delete-storage-container"></a>Não é possível eliminar o contentor de armazenamento

Quando o utilizador tentar eliminar um contentor de BLOBs de armazenamento clássico que não é mais necessária, o utilizador poderá ver o seguinte comportamento.

#### <a name="azure-portal"></a>Portal do Azure 
Portal do Azure não permite ao utilizador eliminar um contentor, se uma concessão de "Disco (s)", existe a apontar para um ficheiro de BLOBs de página *. vhd no contentor. É por design para impedir a eliminação acidental de um ficheiro VHD com a concessão de disco ou discos nos mesmos. 

![Abra a captura de ecrã do portal, com o painel de "list" do contentor de armazenamento](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Se o utilizador optar por eliminar com o PowerShell, irá resultar no seguinte erro. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : O servidor remoto devolveu um erro: (412) existe atualmente uma concessão no contentor e nenhum ID de concessão foi especificado no pedido.... Código de estado HTTP: 412 - mensagem de erro HTTP: Atualmente, existe uma concessão no contentor e nenhum ID de concessão foi especificado no pedido.</span>

## <a name="unable-to-delete-a-vhd"></a>Não é possível eliminar um vhd 

Depois de eliminar a máquina virtual do Azure, o usuário tentar excluir o arquivo de vhd (blob de página) e receber a mensagem abaixo:

#### <a name="azure-portal"></a>Portal do Azure 
No portal, pode haver mais de duas experiências consoante a lista de blobs selecionados para eliminação.

1. Se apenas blobs de "Concedido" estiverem selecionados, em seguida, no botão Eliminar não é exibida.
![Abra a captura de ecrã do portal, com o painel de "list" de Blobs do contentor](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Se forem selecionada uma mistura de blobs "Concedido" e "Disponível", no botão "Eliminar" aparece. Mas a operação "Delete" irá deixar para trás os blobs de página, que tem uma concessão de disco nos mesmos. 
![Captura de ecrã do portal, com o painel de contentor. o blob "lista" open](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![captura de ecrã do portal, com o blob selecionado "delete" painel aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Se o utilizador optar por eliminar com o PowerShell, irá resultar no seguinte erro. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : O servidor remoto devolveu um erro: (412) existe atualmente uma concessão no blob e nenhum ID de concessão foi especificado no pedido.... Código de estado HTTP: 412 - mensagem de erro HTTP: Atualmente, existe uma concessão no blob e nenhum ID de concessão foi especificado no pedido.</span>


## <a name="resolution-steps"></a>Passos de resolução

### <a name="to-remove-classic-disks"></a>Para remover discos clássicos
Siga estes passos no portal do Azure:
1.  Navegue para o [portal do Azure](https://portal.azure.com).
2.  Navegue para o Disks(classic). 
3.  Clique no separador Discos. ![Abra a captura de ecrã do portal, com o painel de "list" de Blobs do contentor](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selecione o disco de dados e, em seguida, clique em Eliminar Disco.
 ![Abra a captura de ecrã do portal, com o painel de "list" de Blobs do contentor](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Repita a operação de eliminação que falhou anteriormente.
6.  Não é possível eliminar uma conta de armazenamento ou contentor, desde que ele tem um único disco.

### <a name="to-remove-classic-images"></a>Para remover as imagens clássicas   
Siga estes passos no portal do Azure:
1.  Navegue para o [portal do Azure](https://portal.azure.com).
2.  Navegue para imagens do SO (clássico).
3.  Elimine a imagem.
4.  Repita a operação de eliminação que falhou anteriormente.
5.  Não é possível eliminar uma conta de armazenamento ou contentor, desde que ele tem uma única imagem.
