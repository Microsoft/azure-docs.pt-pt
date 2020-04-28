---
title: Erros de eliminação de recursos de armazenamento de problemas em VMs Linux em Azure Microsoft Docs
description: Como resolver problemas ao apagar recursos de armazenamento que contenham VHDs anexados.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058157"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Erros de eliminação de recursos de armazenamento de resolução de problemas

Em certos cenários, poderá encontrar um dos seguintes erros enquanto tenta eliminar uma conta de armazenamento Azure, contentor ou blob numa implantação do Gestor de Recursos Azure:

> **Falhou em eliminar a conta de armazenamento 'StorageAccountName'. Erro: A conta de armazenamento não pode ser eliminada devido à utilização dos seus artefactos.**
> 
> **Não conseguiu excluir # fora<br>de # contentores): vhds: Existe atualmente um arrendamento no contentor e não foi especificado no pedido qualquer id de locação.**
> 
> **Falha em excluir # de #<br>blobs: BlobName.vhd: Existe atualmente um contrato de arrendamento na bolha e nenhum ID de locação foi especificado no pedido.**

Os VHDs utilizados em VMs Azure são ficheiros .vhd armazenados como bolhas de página numa conta de armazenamento padrão ou premium em Azure. Para mais informações sobre discos Azure, consulte a nossa [Introdução em discos geridos](../linux/managed-disks-overview.md).

Azure impede a eliminação de um disco que está ligado a um VM para prevenir a corrupção. Também impede a eliminação de contentores e contas de armazenamento que tenham uma bolha de página que esteja anexada a um VM. 

O processo de eliminação de uma conta de armazenamento, contentor ou bolha ao receber um destes erros é: 
1. Identificar bolhas anexas a um VM
2. [Eliminar VMs com **disco OS** anexado](#step-2-delete-vm-to-detach-os-disk)
3. [Separar todos os **discos de dados** dos Restantes VM(s)](#step-3-detach-data-disk-from-the-vm)

Tente novamente apagar a conta de armazenamento, o recipiente ou a bolha depois de concluídos estes passos.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Passo 1: Identificar a bolha anexada a um VM

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: Apagar uma bolha – identificar VM anexado
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos.** Vá à conta de armazenamento, em **blob Service** selecione **Recipientes,** e navegue até à bolha para apagar.
3. Se o **Estado** de Arrendamento blob for **alugado,** clique à direita e selecione **Editar Metadados** para abrir o painel de metadados Blob. 

    ![Screenshot do portal, com as bolhas da conta de Armazenamento e clique à direita > "Editar Metadados" em destaque](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. No painel de metadados Blob, verifique e registe o valor para **MicrosoftAzureCompute_VMName**. Este valor é o nome do VM a que o VHD está ligado. (Ver **importante** se este campo não existe)
5. No painel de metadados Blob, verifique e registe o valor da **MicrosoftAzureCompute_DiskType**. Este valor identifica se o disco anexado é OS ou disco de dados (ver **importante** se este campo não existe). 

     ![Screenshot do portal, com o painel de armazenamento "Blob Metadata" aberto](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Se o tipo de disco blob for **OSDisk** siga o [Passo 2: Elimine o VM para desprender o disco OS](#step-2-delete-vm-to-detach-os-disk). Caso contrário, se o tipo de disco blob for **DataDisk** siga os passos no [passo 3: Desmontar o disco de dados do VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecerem nos metadados blob, indica que a bolha é explicitamente alugada e não está ligada a um VM. As bolhas alugadas não podem ser eliminadas sem quebrar primeiro o contrato de arrendamento. Para quebrar o aluguer, clique à direita na bolha e selecione **Break lease**. As bolhas alugadas que não estejam ligadas a um VM impedem a eliminação da bolha, mas não impedem a eliminação do contentor ou da conta de armazenamento.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: Apagar um recipiente - identifique todas as bolhas dentro do recipiente que estejam anexadas a VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos.** Vá à conta de armazenamento, em **blob Service** selecione **Recipientes,** e encontre o recipiente a ser eliminado.
3. Clique para abrir o recipiente e aparecerá a lista de bolhas no seu interior. Identifique todas as bolhas com Blob Type = **Page blob** e Lease State = **Arrendadas** desta lista. Siga o Cenário 1 para identificar o VM associado a cada uma destas bolhas.

    ![Screenshot do portal, com as bolhas da conta de armazenamento e o "Estado de Arrendamento" com "Arrendatário" em destaque](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Siga o [passo 2](#step-2-delete-vm-to-detach-os-disk) e o [passo 3](#step-3-detach-data-disk-from-the-vm) para eliminar vM(s) com **OSDisk** e desprender **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: Conta de armazenamento de detetada - identifique todas as bolhas dentro da conta de armazenamento anexadas a VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos.** Vá à conta de armazenamento, sob o **Serviço Blob** selecione **Blobs**.
3. No painel **de contentores,** identifique todos os recipientes onde o Estado de **Arrendamento** é **Arrendado** e siga o [Cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada contentor **alugado.**
4. Siga o [passo 2](#step-2-delete-vm-to-detach-os-disk) e o [passo 3](#step-3-detach-data-disk-from-the-vm) para eliminar vM(s) com **OSDisk** e desprender **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Passo 2: Eliminar vM para desaperte o disco OS
Se o VHD for um disco OS, deve eliminar o VM antes de o VHD anexado poder ser eliminado. Não serão necessárias medidas adicionais para os discos de dados ligados ao mesmo VM uma vez concluídos estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Máquinas Virtuais**.
3. Selecione o VM a que o VHD está ligado.
4. Certifique-se de que nada está a utilizar ativamente a máquina virtual e que já não precisa da máquina virtual.
5. Na parte superior do painel de detalhes da **Máquina Virtual,** selecione **Eliminar**, e, em seguida, clique **em Sim** para confirmar.
6. O VM deve ser eliminado, mas o VHD pode ser mantido. No entanto, o VHD não deve mais ser ligado a um VM ou ter um contrato de arrendamento. Pode levar alguns minutos para o arrendamento ser liberado. Para verificar se o arrendamento é liberado, navegue para a localização blob e no painel de **propriedades Blob,** o Estado de **Arrendamento** deve estar **disponível**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Passo 3: Desprender o disco de dados do VM
Se o VHD for um disco de dados, retire o VHD do VM para remover o aluguer:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Máquinas Virtuais**.
3. Selecione o VM a que o VHD está ligado.
4. Selecione **Discos** no painel de detalhes da **Máquina Virtual.**
5. Selecione o disco de dados a eliminar a que o VHD está ligado. Pode determinar qual a bolha que está presa no disco verificando o URL do VHD.
6. Pode verificar a localização da bolha clicando no disco para verificar o caminho no campo **VHD URI.**
7. **Selecione Editar** na parte superior do painel de **discos.**
8. Clique no **ícone de desapor** do disco de dados a eliminar.

     ![Screenshot do portal, com o painel de armazenamento "Blob Metadata" aberto](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selecione **Guardar**. O disco está agora separado do VM, e o VHD já não está alugado. Pode levar alguns minutos para o arrendamento ser liberado. Para verificar se o arrendamento foi lançado, navegue para a localização blob e no painel de **propriedades Blob,** o valor **do Estado** de Arrendamento deve ser **desbloqueado** ou **disponível**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

