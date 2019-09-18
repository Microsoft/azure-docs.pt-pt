---
title: Solucionar erros de exclusão de recursos de armazenamento em VMs do Linux no Azure | Microsoft Docs
description: Como solucionar problemas ao excluir recursos de armazenamento que contêm VHDs anexados.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058157"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Solucionar erros de exclusão de recursos de armazenamento

Em determinados cenários, você pode encontrar um dos seguintes erros durante a tentativa de excluir uma conta de armazenamento do Azure, um contêiner ou um blob em uma implantação de Azure Resource Manager:

> **Falha ao excluir a conta de armazenamento ' StorageAccountName '. Erro: A conta de armazenamento não pode ser excluída porque seus artefatos estão sendo usados.**
> 
> **Falha ao excluir # de # contêiner (es):<br>VHDs: Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação.**
> 
> **Falha ao excluir # de # BLOBs:<br>blobname. vhd: Atualmente, há uma concessão no BLOB e nenhuma ID de concessão foi especificada na solicitação.**

Os VHDs usados nas VMs do Azure são arquivos. vhd armazenados como BLOBs de páginas em uma conta de armazenamento Standard ou Premium no Azure. Para obter mais informações sobre discos do Azure, consulte nossa [introdução aos Managed disks](../linux/managed-disks-overview.md).

O Azure impede a exclusão de um disco que está anexado a uma VM para evitar danos. Ele também impede a exclusão de contêineres e contas de armazenamento que têm um blob de páginas anexado a uma VM. 

O processo para excluir uma conta de armazenamento, um contêiner ou um blob ao receber um desses erros é: 
1. Identificar BLOBs anexados a uma VM
2. [Excluir VMs com **disco do sistema operacional** anexado](#step-2-delete-vm-to-detach-os-disk)
3. [Desanexar todos os **discos de dados** das VM (s) restantes](#step-3-detach-data-disk-from-the-vm)

Tente excluir novamente a conta de armazenamento, o contêiner ou o blob depois que essas etapas forem concluídas.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Passo 1: Identificar o blob anexado a uma VM

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: Excluindo um blob – identificar VM anexada
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **todos os recursos**. Vá para a conta de armazenamento, em **serviço blob** , selecione **contêineres**e navegue até o blob a ser excluído.
3. Se o **estado de concessão** de blob for **concedido**, clique com o botão direito do mouse e selecione **Editar metadados** para abrir o painel de metadados de BLOB. 

    ![Captura de tela do portal, com os BLOBs da conta de armazenamento e clique com o botão direito do mouse > "Editar metadados" realçado](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. No painel metadados do blob, verifique e registre o valor de **MicrosoftAzureCompute_VMName**. Esse valor é o nome da VM à qual o VHD está anexado. (Consulte **importante** se esse campo não existir)
5. No painel metadados do blob, verifique e registre o valor de **MicrosoftAzureCompute_DiskType**. Esse valor identifica se o disco anexado é um sistema operacional ou disco de dados (consulte **importante** se esse campo não existir). 

     ![Captura de tela do portal, com o painel de armazenamento "metadados de blob" aberto](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Se o tipo de disco de blob for [OSDisk, siga a etapa 2: Exclua a VM para desanexar o disco](#step-2-delete-vm-to-detach-os-disk)do sistema operacional. Caso contrário, se o tipo de disco de blob for **datadisk** , [siga as etapas na etapa 3: Desanexar o disco de dados](#step-3-detach-data-disk-from-the-vm)da VM. 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecerem nos metadados do blob, isso indica que o blob é concedido explicitamente e não está anexado a uma VM. Os BLOBs concedidos não podem ser excluídos sem interromper a concessão primeiro. Para interromper a concessão, clique com o botão direito do mouse no BLOB e selecione **interromper concessão**. Os BLOBs concedidos que não estão anexados a uma VM impedem a exclusão do blob, mas não impedem a exclusão do contêiner ou da conta de armazenamento.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: Excluindo um contêiner-identificar todos os BLOBs no contêiner que estão anexados às VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **todos os recursos**. Vá para a conta de armazenamento, em **serviço blob** , selecione **contêineres**e localize o contêiner a ser excluído.
3. Clique para abrir o contêiner e a lista de BLOBs dentro dele será exibida. Identifique todos os BLOBs com tipo de blob = **blob de páginas** e estado de concessão = **concedidos** desta lista. Siga o cenário 1 para identificar a VM associada a cada um desses BLOBs.

    ![Captura de tela do portal, com os BLOBs da conta de armazenamento e o "estado da concessão" com "concedido" realçado](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Siga as [etapas 2](#step-2-delete-vm-to-detach-os-disk) e [3](#step-3-detach-data-disk-from-the-vm) para excluir VM (s) com **OSDisk** e desanexar **datadisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: Excluindo conta de armazenamento-identificar todos os BLOBs na conta de armazenamento que estão anexados às VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **todos os recursos**. Vá para a conta de armazenamento, em **serviço blob** , selecione **BLOBs**.
3. No painel **contêineres** , identifique todos os contêineres onde o **estado de concessão** é **concedido** e siga o [cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada contêiner **concedido** .
4. Siga as [etapas 2](#step-2-delete-vm-to-detach-os-disk) e [3](#step-3-detach-data-disk-from-the-vm) para excluir VM (s) com **OSDisk** e desanexar **datadisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Passo 2: Excluir a VM para desanexar o disco do sistema operacional
Se o VHD for um disco do sistema operacional, você deverá excluir a VM antes que o VHD anexado possa ser excluído. Nenhuma ação adicional será necessária para discos de dados anexados à mesma VM depois que estas etapas forem concluídas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **máquinas virtuais**.
3. Selecione a VM à qual o VHD está anexado.
4. Certifique-se de que nada esteja usando ativamente a máquina virtual e que você não precise mais da máquina virtual.
5. Na parte superior do painel de **detalhes da máquina virtual** , selecione **excluir**e, em seguida, clique em **Sim** para confirmar.
6. A VM deve ser excluída, mas o VHD pode ser retido. No entanto, o VHD não deve mais ser anexado a uma VM ou ter uma concessão nele. Pode levar alguns minutos para que a concessão seja liberada. Para verificar se a concessão foi liberada, navegue até o local do blob e, no painel **Propriedades do blob** , o **status da concessão** deve estar **disponível**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Passo 3: Desanexar o disco de dados da VM
Se o VHD for um disco de dados, desanexe o VHD da VM para remover a concessão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **máquinas virtuais**.
3. Selecione a VM à qual o VHD está anexado.
4. Selecione **discos** no painel **detalhes da máquina virtual** .
5. Selecione o disco de dados a ser excluído ao qual o VHD está anexado. Você pode determinar qual blob está anexado ao disco verificando a URL do VHD.
6. Você pode verificar o local do blob clicando no disco para verificar o caminho no campo **URI do VHD** .
7. Selecione **Editar** na parte superior do painel **discos** .
8. Clique no **ícone desanexar** do disco de dados a ser excluído.

     ![Captura de tela do portal, com o painel de armazenamento "metadados de blob" aberto](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selecione **Guardar**. O disco agora é desanexado da VM e o VHD não é mais concedido. Pode levar alguns minutos para que a concessão seja liberada. Para verificar se a concessão foi liberada, navegue até o local do blob e, no painel **Propriedades do blob** , o valor do **status de concessão** deve ser **desbloqueado** ou **disponível**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

