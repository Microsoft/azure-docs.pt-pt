---
title: Falhas nos recursos de armazenamento de resolução de problemas nos VMs do Linux em Azure Microsoft Docs
description: Aprenda a resolver problemas em VMs Linux ao eliminar recursos de armazenamento que contenham VHDs anexados.
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
ms.openlocfilehash: 8d727bc8bdc8f015504baa57f9596b3bacac9712
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022875"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Resolver problemas de erros de eliminação de recursos de armazenamento

Em certos cenários, poderá encontrar um dos seguintes erros enquanto tenta eliminar uma conta de armazenamento, contentor ou bolha Azure numa implantação do Azure Resource Manager:

> **Não eliminou a conta de armazenamento 'StorageAccountName'. Erro: A conta de armazenamento não pode ser eliminada devido à utilização dos seus artefactos.**
> 
> **Não conseguiu eliminar # fora do (s) recipiente(s): <br> vhds: Existe atualmente um arrendamento no contentor e não foi especificado nenhum ID de locação no pedido.**
> 
> **Falha em apagar # de # blobs: <br> BlobName.vhd: Existe atualmente um arrendamento na bolha e nenhum ID de arrendamento foi especificado no pedido.**

Os VHDs utilizados nos VMs Azure são ficheiros .vhd armazenados como bolhas de página numa conta de armazenamento padrão ou premium em Azure. Para obter mais informações sobre discos Azure, consulte a nossa [Introdução aos discos geridos.](../managed-disks-overview.md)

Azure impede a supressão de um disco que está ligado a um VM para prevenir a corrupção. Também impede a eliminação de contentores e contas de armazenamento que tenham uma bolha de página que é anexada a um VM. 

O processo para eliminar uma conta de armazenamento, um contentor ou uma bolha ao receber um destes erros é: 
1. Identificar bolhas anexadas a um VM
2. [Eliminar VMs com **disco oss** anexado](#step-2-delete-vm-to-detach-os-disk)
3. [Separar todos os **discos de dados** dos restantes VM(s)](#step-3-detach-data-disk-from-the-vm)

Redaça a eliminação da conta de armazenamento, do recipiente ou da bolha após a conclusão destes passos.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Passo 1: Identificar bolha anexada a um VM

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: Apagar uma bolha – identificar VM anexado
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá à conta de armazenamento, em **Blob Service** select **Containers**, e navegue até à bolha para apagar.
3. Se o **Estado de Locação blob** estiver **alugado,** clique à direita e **selecione Editar Metadados** para abrir o painel de metadados Blob. 

    ![Screenshot do portal, com as bolhas da conta de Armazenamento e clique à direita > "Editar Metadados" em destaque](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. No painel de metadados Blob, verifique e registem o valor **de MicrosoftAzureCompute_VMName**. Este valor é o nome do VM a que o VHD está ligado. (Ver **importante** se este campo não existe)
5. No painel de metadados Blob, verifique e registem o valor de **MicrosoftAzureCompute_DiskType**. Este valor identifica se o disco anexado é SO ou disco de dados (Ver **se** este campo não existe). 

     ![Screenshot do portal, com o painel de armazenamento "Blob Metadata" aberto](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Se o tipo de disco blob for **OSDisk** siga [o passo 2: Eliminar VM para descodição do disco OS](#step-2-delete-vm-to-detach-os-disk). Caso contrário, se o tipo de disco blob for **DataDisk** siga os passos no [passo 3: Retire o disco de dados do VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecerem nos metadados blob, indica que a bolha está explicitamente alugada e não está ligada a um VM. As bolhas alugadas não podem ser apagadas sem quebrar primeiro o contrato de arrendamento. Para quebrar o arrendamento, clique com o botão direito na bolha e selecione **Break lease**. Bolhas alugadas que não estejam ligadas a um VM impeçam a supressão da bolha, mas não impeçam a supressão do contentor ou da conta de armazenamento.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: Eliminação de um contentor - identifique todas as bolhas dentro do contentor que estão ligados aos VM
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá à conta de armazenamento, em **Blob Service** select **Containers**, e encontre o recipiente a ser apagado.
3. Clique para abrir o recipiente e aparecerá a lista de bolhas no seu interior. Identifique todas as bolhas com Blob Type = **Page blob** and Lease State = **Alugado** a partir desta lista. Siga o Cenário 1 para identificar o VM associado a cada uma destas bolhas.

    ![Screenshot do portal, com as bolhas da conta de Armazenamento e o "Estado de Arrendamento" com "Locação" em destaque](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Siga [o passo 2](#step-2-delete-vm-to-detach-os-disk) e [o passo 3](#step-3-detach-data-disk-from-the-vm) para eliminar vM(s) com **OSDisk** e desvincular **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: Eliminação da conta de armazenamento - identifique todas as manchas na conta de armazenamento que estão anexadas aos VMs
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Aceda à conta de armazenamento, em **Blob Service** select **Blobs**.
3. No **painel de contentores,** identifique todos os contentores onde **o Estado de Arrendamento** é **Arrendado** e siga [o Cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada recipiente **alugado.**
4. Siga [o passo 2](#step-2-delete-vm-to-detach-os-disk) e [o passo 3](#step-3-detach-data-disk-from-the-vm) para eliminar vM(s) com **OSDisk** e desvincular **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Passo 2: Eliminar VM para desprender o disco OS
Se o VHD for um disco DE, deve eliminar o VM antes que o VHD anexado possa ser eliminado. Não serão necessárias medidas adicionais para os discos de dados ligados ao mesmo VM uma vez que estes passos estejam concluídos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Máquinas Virtuais.**
3. Selecione o VM ao qual o VHD está ligado.
4. Certifique-se de que nada está a utilizar ativamente a máquina virtual e que já não necessita da máquina virtual.
5. Na parte superior do painel de detalhes da **Máquina Virtual,** selecione **Delete**, e clique em **Sim** para confirmar.
6. O VM deve ser eliminado, mas o VHD pode ser mantido. No entanto, o VHD não deve mais ser ligado a um VM ou ter um contrato de arrendamento. Pode levar alguns minutos para o arrendamento ser liberado. Para verificar se o arrendamento é liberado, navegue para a localização do blob e no painel **de propriedades Blob,** o **Estado do Arrendamento** deve estar **disponível**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Passo 3: Retirar o disco de dados do VM
Se o VHD for um disco de dados, retire o VHD do VM para remover o arrendamento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Máquinas Virtuais.**
3. Selecione o VM ao qual o VHD está ligado.
4. Selecione **Discos** no painel de detalhes da **máquina virtual.**
5. Selecione o disco de dados a eliminar ao qual o VHD está ligado. Pode determinar qual a bolha que está fixada no disco verificando o URL do VHD.
6. Pode verificar a localização da bolha clicando no disco para verificar o caminho no campo **VHD URI.**
7. **Selecione Editar** na parte superior do **painel de discos.**
8. Clique **no ícone de desprendimento** do disco de dados a eliminar.

     ![Screenshot do portal, com o painel de armazenamento "Blob Metadata" aberto e realça o ícone de desprendimento do disco de dados a ser eliminado.](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selecione **Guardar**. O disco está agora desligado do VM, e o VHD já não está alugado. Pode levar alguns minutos para o arrendamento ser liberado. Para verificar se o arrendamento foi liberado, navegue para a localização do blob e no painel **de propriedades Blob,** o valor **do Estado do arrendamento** deve ser **desbloqueado** ou **disponível**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

