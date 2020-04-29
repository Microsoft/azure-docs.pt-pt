---
title: Erros de resolução de problemas ao eliminar contas de armazenamento clássicos do Azure, contentores ou VHDs [ Microsoft Docs
description: Como resolver problemas ao apagar recursos de armazenamento que contenham VHDs anexados.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77915042"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Problemas de eliminação de recursos de armazenamento clássicos
Este artigo fornece orientação de resolução de problemas quando ocorre um dos seguintes erros tentando eliminar a conta de armazenamento clássica do Azure, o recipiente ou o ficheiro blob de página *.vhd. 


Este artigo abrange apenas questões com recursos de armazenamento clássicos. Se um utilizador eliminar uma máquina virtual clássica utilizando o portal Azure, PowerShell ou CLI, os Discos não são automaticamente eliminados. O utilizador tem a opção de eliminar o recurso "Disk". Caso a opção não seja selecionada, o recurso "Disk" evitará a eliminação da conta de armazenamento, do contentor e do ficheiro blob da página real de *.vhd.

Mais informações sobre discos Azure podem ser encontradas [aqui.](../../virtual-machines/windows/managed-disks-overview.md) Azure impede a eliminação de um disco que está ligado a um VM para prevenir a corrupção. Também impede a eliminação de contentores e contas de armazenamento, que têm uma bolha de página que está anexada a um VM. 

## <a name="what-is-a-disk"></a>O que é um "Disco"?
Um recurso "Disk" é usado para montar um ficheiro blob de página *.vhd para uma máquina virtual, como um disco de OS ou disco de dados. Um recurso de disco osso ou de disco de dados, até ser eliminado, continuará a realizar um contrato de arrendamento no ficheiro *.vhd. Qualquer recurso de armazenamento no caminho mostrado na imagem abaixo não pode ser eliminado se um recurso "Disk" apontar para ele.

![Screenshot do portal, com o painel de disco (clássico) "Propriedade" aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Passos ao apagar uma máquina virtual clássica 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Apague a máquina virtual clássica.
2. Se a caixa de verificação "Discos" for selecionada, a locação de **disco** (mostrada na imagem acima) associada à página blob *.vhd está quebrada. O ficheiro blob de página real *.vhd ainda existirá na conta de armazenamento.
![Screenshot do portal, com a máquina virtual (clássica) painel de erro "Delete" aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Uma vez quebrado o ou o ou s do disco, a própria página blob(s) pode ser eliminada. Uma conta de armazenamento ou recipiente pode ser eliminada assim que todos os recursos "Disco" neles presentes forem eliminados.

>[!NOTE] 
>Se o utilizador eliminar o VM mas não o VHD, os custos de armazenamento continuarão a acumular-se no ficheiro blob *.vhd da página. Os encargos estarão em linha com o tipo de conta de armazenamento, verifique [a página](https://azure.microsoft.com/pricing/details/storage/) de preços para mais detalhes. Se o utilizador já não pretender utilizar os VHD(s), elimine-o/para evitar futuras cargas. 

## <a name="unable-to-delete-storage-account"></a>Incapaz de apagar conta de armazenamento 

Quando o utilizador tenta eliminar uma conta de armazenamento clássica que já não seja necessária, o utilizador pode ver o seguinte comportamento.

#### <a name="azure-portal"></a>Portal do Azure 
O utilizador navega para a conta de armazenamento clássica no [portal Azure](https://portal.azure.com) e clica **Eliminar,** o utilizador verá a seguinte mensagem: 

Com disco(s) "ligado" a uma máquina virtual

![Screenshot do portal, com a máquina virtual (clássica) painel de erro "Delete" aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Com disco(s) "desconectado" a uma máquina virtual

![Screenshot do portal, com a máquina virtual (clássica) "Eliminar" painel de não erro aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
O utilizador tenta eliminar uma conta de armazenamento, que já não está a ser utilizada, utilizando cmdlets clássicos da PowerShell. O utilizador verá a seguinte mensagem:

> <span style="color:cyan">**Remover-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: A conta de armazenamento myclassicaccount tem algumas imagens ativas e/ou discos, por exemplo.  
> minha conta clássica. Certifique-se de que estas imagens e/ou discos são removidos antes de eliminar esta conta de armazenamento.</span>

## <a name="unable-to-delete-storage-container"></a>Incapaz de apagar recipiente de armazenamento

Quando o utilizador tenta eliminar um recipiente de bolha de armazenamento clássico que já não seja necessário, o utilizador pode ver o seguinte comportamento.

#### <a name="azure-portal"></a>Portal do Azure 
O portal Azure não permitiria que o utilizador apagasse um contentor se existisse um contrato de locação "Disk(s)" que apontasse para um ficheiro blob de página *.vhd no recipiente. É por conceção para evitar a supressão acidental de um ficheiro vhd com o Disco(s) arrendamento neles. 

![Screenshot do portal, com o painel de "lista" do recipiente de armazenamento aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Se o utilizador optar por eliminar utilizando o PowerShell, resultará no seguinte erro. 

> <span style="color:cyan">**Remover-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : O servidor remoto devolveu um erro: (412) Existe atualmente uma locação no contentor e não foi especificado qualquer ID de locação no pedido.. Código de Estado HTTP: 412 - HTTP Error Message: Existe atualmente um contrato de arrendamento no contentor e não foi especificado qualquer ID de locação no pedido.</span>

## <a name="unable-to-delete-a-vhd"></a>Incapaz de apagar um vhd 

Depois de eliminar a máquina virtual Azure, o utilizador tenta eliminar o ficheiro VHD (blob de página) e receber a mensagem abaixo:

#### <a name="azure-portal"></a>Portal do Azure 
No portal, pode haver duas experiências dependendo da lista de bolhas selecionadas para eliminação.

1. Se forem selecionadas apenas bolhas "Alugadas", o botão Delete não aparece.
![Screenshot do portal, com o painel de "lista" de blob do recipiente aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Se for selecionada uma mistura de bolhas "Alugadas" e "disponíveis", aparece o botão "Eliminar". Mas a operação "Eliminar" deixará para trás as bolhas de página, que têm um contrato de arrendamento de disco. 
![Screenshot do portal, com o painel de "lista" de blob do recipiente aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Screenshot do portal, com o painel de "apagar" blob selecionado aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Se o utilizador optar por eliminar utilizando o PowerShell, resultará no seguinte erro. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : O servidor remoto devolveu um erro: (412) Existe atualmente um contrato de arrendamento na bolha e não foi especificado qualquer ID de locação no pedido.. Código de Estado HTTP: 412 - HTTP Error Message: Existe atualmente um contrato de arrendamento sobre a bolha e nenhum ID de locação foi especificado no pedido.</span>


## <a name="resolution-steps"></a>Passos de resolução

### <a name="to-remove-classic-disks"></a>Para remover discos clássicos
Siga estes passos no portal Azure:
1.  Navegue para o [portal Azure.](https://portal.azure.com)
2.  Navegue para os Discos (clássico). 
3.  Clique no separador ![Discos. Screenshot do portal, com o painel de "lista" de blob do recipiente aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selecione o disco de dados e, em seguida, clique em Eliminar Disco.
 ![Screenshot do portal, com o painel de "lista" de blob do recipiente aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Tente novamente a operação Delete que anteriormente falhou.
6.  Uma conta de armazenamento ou recipiente não pode ser eliminada desde que tenha um único Disco.

### <a name="to-remove-classic-images"></a>Para remover imagens clássicas   
Siga estes passos no portal Azure:
1.  Navegue para o [portal Azure.](https://portal.azure.com)
2.  Navegue para imagens de OS (clássica).
3.  Apague a imagem.
4.  Tente novamente a operação Delete que anteriormente falhou.
5.  Uma conta de armazenamento ou recipiente não pode ser eliminada desde que tenha uma única Imagem.
