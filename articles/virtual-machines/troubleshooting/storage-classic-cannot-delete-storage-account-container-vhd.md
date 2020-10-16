---
title: Erros de resolução de problemas ao eliminar contas de armazenamento clássicos do Azure, contentores ou VHDs Microsoft Docs
description: Como resolver problemas ao eliminar os recursos de armazenamento que contêm VHDs anexados.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361862"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Resolução de problemas erros clássicos de eliminação de recursos de armazenamento
Este artigo fornece orientação de resolução de problemas quando ocorre um dos seguintes erros tentando eliminar a conta de armazenamento clássico Azure, o contentor ou o ficheiro blob de página *.vhd. 


Este artigo abrange apenas questões com recursos de armazenamento clássicos. Se um utilizador eliminar uma máquina virtual clássica utilizando o portal Azure, PowerShell ou CLI, então os Discos não são automaticamente eliminados. O utilizador tem a opção de eliminar o recurso "Disco". Caso a opção não seja selecionada, o recurso "Disco" impedirá a eliminação da conta de armazenamento, do contentor e do ficheiro blob de página real *.vhd.

Mais informações sobre discos Azure podem ser encontradas [aqui.](../../virtual-machines/managed-disks-overview.md) Azure impede a supressão de um disco que está ligado a um VM para prevenir a corrupção. Também impede a eliminação de contentores e contas de armazenamento, que têm uma bolha de página que é anexada a um VM. 

## <a name="what-is-a-disk"></a>O que é um "Disco"?
Um recurso "Disco" é utilizado para montar um ficheiro blob de página *.vhd para uma máquina virtual, como um disco DE OU ou disco de dados. Um recurso de disco DE ou de Dados, até ser eliminado, continuará a realizar uma locação no ficheiro *.vhd. Qualquer recurso de armazenamento no caminho indicado na imagem abaixo não pode ser eliminado se um recurso "Disco" opontar.

![Screenshot do portal, com o painel de disco (clássico) "Propriedade" aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Passos ao apagar uma máquina virtual clássica 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Apague a máquina virtual clássica.
2. Se a caixa de verificação "Discos" for selecionada, a **locação** do disco (mostrada na imagem acima) associada à bolha da página *.vhd está partida. O ficheiro blob de página real *.vhd ainda existirá na conta de armazenamento.
![A screenshot mostra uma caixa de diálogo para confirmar a eliminação de uma máquina virtual.](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Uma vez que o ouso ouso arrendamento do disco é quebrado, a (s) blob(s) da página em si pode ser eliminada. Uma conta de armazenamento ou um recipiente pode ser eliminado uma vez que todos os recursos "Disco" presentes neles sejam eliminados.

>[!NOTE] 
>Se o utilizador eliminar o VM mas não o VHD, os custos de armazenamento continuarão a acumular-se no ficheiro blob da página *.vhd. Os encargos estarão em consonância com o tipo de conta de armazenamento, verifique [a página de preços](https://azure.microsoft.com/pricing/details/storage/) para obter mais detalhes. Se o utilizador já não pretender utilizar o VHD(s), elimine-o para evitar futuras taxas. 

## <a name="unable-to-delete-storage-account"></a>Não é possível apagar a conta de armazenamento 

Quando o utilizador tenta eliminar uma conta de armazenamento clássica que já não é necessária, o utilizador poderá ver o seguinte comportamento.

#### <a name="azure-portal"></a>Portal do Azure 
O utilizador navega para a conta de armazenamento clássica no [portal Azure](https://portal.azure.com) e clica **em Apagar**, o utilizador verá a seguinte mensagem: 

Com disco(s) "anexado" a uma máquina virtual

![A screenshot mostra uma mensagem explicando por que uma conta de armazenamento não pode ser eliminada.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Com disco(s) "desapegado" a uma máquina virtual

![Screenshot do portal, com a máquina virtual (clássica) "Delete" painel de não-erro aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
O utilizador tenta apagar uma conta de armazenamento que já não está a ser utilizada, utilizando cmdlets clássicos do PowerShell. O utilizador verá a seguinte mensagem:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Storage account myclassicaccount has some ative image(s) and/or disk(s), por exemplo.  
> myclassicaccount. Certifique-se de que estas imagens e/ou discos são removidos antes de eliminar esta conta de armazenamento.</span>

## <a name="unable-to-delete-storage-container"></a>Não é possível apagar o recipiente de armazenamento

Quando o utilizador tentar eliminar um recipiente de bolha de armazenamento clássico que já não é necessário, o utilizador poderá ver o seguinte comportamento.

#### <a name="azure-portal"></a>Portal do Azure 
O portal Azure não permitiria que o utilizador apagasse um recipiente se existisse uma locação "Disco(s)" que apontasse para um ficheiro blob de página *.vhd no recipiente. É por design para evitar a eliminação acidental de um ficheiro vhd(s) com o (s) lease (s) disco(s) neles. 

![Screenshot do portal, com o painel de "lista" do recipiente de armazenamento aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Se o utilizador optar por eliminar utilizando o PowerShell, resultará no seguinte erro. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Nome vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : O servidor remoto devolveu um erro: (412) Existe atualmente uma locação no recipiente e não foi especificado nenhum ID de locação no pedido.. Código de Estado HTTP: 412 - Mensagem de erro HTTP: Existe atualmente uma locação no contentor e não foi especificado nenhum ID de locação no pedido.</span>

## <a name="unable-to-delete-a-vhd"></a>Incapaz de apagar um vhd 

Depois de eliminar a máquina virtual Azure, o utilizador tenta eliminar o ficheiro vhd (bolha de página) e receber a mensagem abaixo:

#### <a name="azure-portal"></a>Portal do Azure 
No portal, pode haver duas experiências dependendo da lista de bolhas selecionadas para eliminação.

1. Se apenas forem selecionadas bolhas "Alugadas", o botão Eliminar não aparece.
![Screenshot do portal, com o painel de lista de bolhas do recipiente aberto e apenas bolhas alugadas selecionadas.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Se for selecionada uma mistura de bolhas "Alugadas" e "Disponíveis", o botão "Eliminar" aparece. Mas a operação "Eliminar" deixará para trás as bolhas de página, que têm um contrato de arrendamento em disco. 
![Screenshot do portal, com o painel de lista de bolhas do recipiente aberto e ambos os blobs alugados e disponíveis selecionados. ](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
 ![ Screenshot do portal, com o painel de bolhas "delete" selecionado aberto](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Se o utilizador optar por eliminar utilizando o PowerShell, resultará no seguinte erro. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : O servidor remoto devolveu um erro: (412) Existe atualmente uma locação-bomba na bolha e não foi especificado nenhum ID de locação no pedido.. Código de Estado HTTP: 412 - Mensagem de erro HTTP: Existe atualmente uma locação-venda na bolha e não foi especificado nenhum ID de locação no pedido.</span>


## <a name="resolution-steps"></a>Passos de resolução

### <a name="to-remove-classic-disks"></a>Para remover discos clássicos
Siga estes passos no portal Azure:
1.  Navegue até ao [portal Azure.](https://portal.azure.com)
2.  Navegue para os Discos (clássico). 
3.  Clique no separador Discos. ![ O Screenshot mostra o portal Azure com Discos (clássicos) selecionados e um nome clássico de disco e conta de armazenamento.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selecione o disco de dados e, em seguida, clique em Eliminar Disco.
 ![O Screenshot mostra o portal Azure com Discos (clássicos) selecionados, com um disco de dados selecionado e a opção de excluir.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Reda o teste da operação eliminar que falhou anteriormente.
6.  Uma conta de armazenamento ou um recipiente não podem ser eliminados desde que tenha um único Disco.

### <a name="to-remove-classic-images"></a>Para remover imagens clássicas   
Siga estes passos no portal Azure:
1.  Navegue até ao [portal Azure.](https://portal.azure.com)
2.  Navegue para imagens DES (clássico).
3.  Apague a imagem.
4.  Reda o teste da operação eliminar que falhou anteriormente.
5.  Uma conta de armazenamento ou um recipiente não podem ser eliminados desde que tenha uma única Imagem.
