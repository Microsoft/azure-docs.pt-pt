---
title: Resolver erros de eliminação de recursos de armazenamento em VMs do Linux no Azure | Documentos da Microsoft
description: Como resolver problemas ao eliminar os recursos de armazenamento com VHDs ligadas.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: a1eb946d3f1b18aaa86735dedcfbaa1fd6a89621
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089986"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Resolver erros de eliminação de recursos de armazenamento

Em determinados cenários, pode encontrar um dos seguintes erros ocorrem enquanto está a tentar eliminar uma conta de armazenamento do Azure, contentor ou blob numa implementação do Azure Resource Manager:

> **Falha ao eliminar a conta de armazenamento 'StorageAccountName'. Erro: Não é possível eliminar a conta de armazenamento porque os artefactos estão a ser utilizados.**
> 
> **Falha ao eliminar o # fora do contentor (es) de #:<br>vhds: Atualmente, existe uma concessão no contentor e nenhum ID de concessão foi especificado no pedido.**
> 
> **Falha ao eliminar o # fora de blobs de #:<br>BlobName.vhd: Atualmente, existe uma concessão no blob e nenhum ID de concessão foi especificado no pedido.**

Os VHDs utilizados em VMs do Azure são ficheiros. vhd armazenados como blobs de páginas numa conta de armazenamento standard ou premium no Azure. Para obter mais informações acerca dos discos do Azure, consulte nosso [introdução aos discos geridos](../linux/managed-disks-overview.md).

Azure impede a eliminação de um disco que está ligado a uma VM para evitar danos. Ele também impede a eliminação de contentores e contas de armazenamento que têm um blob de página que está ligado a uma VM. 

O processo para eliminar uma conta de armazenamento, contentor ou blob ao receber um desses erros é: 
1. Identificar blobs anexados a uma VM
2. [VMs de eliminação com anexado **disco do SO**](#step-2-delete-vm-to-detach-os-disk)
3. [Desligar todas **discos de dados** das VMS restantes](#step-3-detach-data-disk-from-the-vm)

Repita a eliminar a conta de armazenamento, contentor ou blob depois de concluir estes passos.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Passo 1: Identificar blob anexado a uma VM

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: A eliminar um blob – identificar VM anexada
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **todos os recursos**. Vá para a conta de armazenamento, em **serviço Blob** selecionar **contentores**e navegue para o blob a eliminar.
3. Se o blob **estado de concessão** é **concedido**, em seguida, clique com botão direito e selecione **Editar metadados** para abrir o painel de metadados do Blob. 

    ![Captura de ecrã do portal, com o armazenamento de blobs da conta e clique com botão direito > "Metadados de editar" realçado](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. No painel de metadados de Blob, verifique e registe o valor para **MicrosoftAzureCompute_VMName**. Este valor é o nome da VM que o VHD está anexado a. (Consulte **importante** se este campo não existir)
5. No painel de metadados de Blob, verifique e registe o valor de **MicrosoftAzureCompute_DiskType**. Este valor identifica se o disco ligado é o disco de dados ou de sistema operacional (consulte **importante** se este campo não existir). 

     ![Abra a captura de ecrã do portal, com o painel de "Metadados do Blob" do armazenamento](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Se for o tipo de disco de blob **OSDisk** siga [passo 2: Eliminar VM ao desanexar o disco de SO](#step-2-delete-vm-to-detach-os-disk). Caso contrário, se é o tipo de disco de blob **DataDisk** siga os passos em [passo 3: Desanexar disco de dados da VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecem nos metadados do blob, ele indica que o blob é explicitamente concedido e não está ligado a uma VM. Não não possível eliminar os blobs de concessão sem interromper a concessão primeiro. Para interromper a concessão, com o botão direito no blob e selecione **Break lease**. Blobs de concessão que não estão ligados a uma VM a impedem a eliminação do blob, mas não impedir a eliminação de conta de armazenamento ou contentor.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: A eliminar o contentor, identificar todos os BLOBs no contentor que estão ligados às VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **todos os recursos**. Vá para a conta de armazenamento, em **serviço Blob** selecionar **contentores**e encontrar o contentor a eliminar.
3. Clique para abrir o contentor e será apresentada a lista de blobs no interior do mesmo. Identificar todos os blobs com o tipo de Blob = **BLOBs de páginas** e o estado da concessão = **concedido** desta lista. Siga o cenário 1 para identificar a VM associada a cada nestes blobs.

    ![Captura de ecrã do portal, com os blobs da conta de armazenamento e o "Estado da concessão" com "Concedido" realçado](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Siga [passo 2](#step-2-delete-vm-to-detach-os-disk) e [passo 3](#step-3-detach-data-disk-from-the-vm) ao eliminar a VM (s) com **OSDisk** e desanexar **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: A eliminar o armazenamento de contas – identificar todos os BLOBs na conta de armazenamento que estão ligados às VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **todos os recursos**. Vá para a conta de armazenamento, em **serviço Blob** selecionar **Blobs**.
3. Na **contentores** painel, identificar todos os contentores em que **estado da concessão** é **concedido** e siga [cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada  **Concedidos** contentor.
4. Siga [passo 2](#step-2-delete-vm-to-detach-os-disk) e [passo 3](#step-3-detach-data-disk-from-the-vm) ao eliminar a VM (s) com **OSDisk** e desanexar **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Passo 2: Eliminar VM ao desanexar o disco do SO
Se o VHD é um disco de SO, tem de eliminar a VM antes de pode eliminar o VHD anexado. Nenhuma ação adicional será necessária para discos de dados ligados para a mesma VM depois de concluir estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **máquinas virtuais**.
3. Selecione a VM que o VHD está anexado a.
4. Certifique-se de que nada é a utilizar ativamente a máquina virtual, e que já não precisar da máquina virtual.
5. Na parte superior a **detalhes da Máquina Virtual** painel, selecione **eliminar**e, em seguida, clique em **Sim** para confirmar.
6. A VM deve ser eliminada, mas o VHD que pode ser retido. No entanto, o VHD já não deve ser anexado a uma VM ou tem uma concessão sobre o mesmo. Pode demorar alguns minutos para que a concessão para lançamento. Para verificar que a concessão será libertada, navegue para a localização do blob e, no **propriedades do Blob** painel, o **estatuto da concessão** deve ser **disponível**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Passo 3: Desanexar disco de dados da VM
Se o VHD é um disco de dados, desanexe o VHD da VM para remover a concessão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **máquinas virtuais**.
3. Selecione a VM que o VHD está anexado a.
4. Selecione **discos** sobre o **detalhes da Máquina Virtual** painel.
5. Selecione o disco de dados a eliminar o VHD está anexado a. Pode determinar qual blob está anexado no disco ao verificar o URL do VHD.
6. Pode verificar a localização do blob ao clicar no disco para verificar o caminho **URI de VHD** campo.
7. Selecione **edite** na parte superior do **discos** painel.
8. Clique em **desanexar ícone** do disco de dados para serem eliminados.

     ![Abra a captura de ecrã do portal, com o painel de "Metadados do Blob" do armazenamento](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selecione **Guardar**. O disco agora está desligado da VM, e o VHD já não está a ser concedido. Pode demorar alguns minutos para que a concessão para lançamento. Para verificar que a concessão foi lançada, navegue para a localização do blob e, no **propriedades do Blob** painel, o **estatuto da concessão** valor deve ser **Unlocked** ou **Disponíveis**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

