---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74013780"
---
O Storage Explorer 1.10.0 permite que os utilizadores carreguem, descarreguem e copiem discos geridos, bem como criem instantâneos. Devido a estas capacidades adicionais, você pode usar o Storage Explorer para migrar dados do local para O Azure, e migrar dados através das regiões de Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, vai precisar do seguinte:
- Uma subscrição do Azure.
- Um ou mais discos geridos pelo Azure
- A versão mais recente do [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

Se o seu Explorador de Armazenamento não estiver ligado ao Azure, não poderá utilizá-lo para gerir recursos. Esta secção passa por ligá-la à sua conta Azure para que possa gerir os recursos utilizando o Storage Explorer.

1. Lance o Azure Storage Explorer e clique no ícone **plug-in** à esquerda.

    ![Clique no ícone plug-in](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selecione **Adicionar uma Conta Azure**e, em seguida, clicar **em Seguinte**.

    ![Adicionar uma Conta do Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Na caixa de diálogo **Azure Sign in,** introduza as suas credenciais Azure.

    ![Sinal azul no diálogo](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selecione a sua subscrição na lista e clique em **Aplicar**.

    ![Selecione a sua subscrição](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Faça upload de um disco gerido a partir de um VHD on-prem

1. No painel esquerdo, expanda **discos** e selecione o grupo de recursos para o que pretende fazer upload do disco.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecione **Upload**.

    ![Selecionar a operação carregar](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. No **Upload VHD** especifique a sua fonte VHD, o nome do disco, o tipo OS, a região para a qual pretende fazer o upload do disco, bem como o tipo de conta. Em algumas regiões as zonas de disponibilidade são suportadas, para essas regiões você pode selecionar uma zona à sua escolha.
1. Selecione **Criar** para começar a carregar o disco.

    ![Carregar diálogo vhd](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. O estado do upload irá agora ser exibido em **Atividades**.

    ![Estado de upload](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Se o upload tiver terminado e não vir o disco no painel direito, selecione **Refresh**.

## <a name="download-a-managed-disk"></a>Descarregue um disco gerido

Os seguintes passos explicam como transferir um disco gerido para um VHD on-prem. O estado de um disco deve ser **desvinculado** para ser descarregado, não pode ndo descarregar um disco **Anexo.**

1. No painel esquerdo, se ainda não estiver expandido, expanda **discos** e selecione o grupo de recursos a que pretende descarregar o disco.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que pretende descarregar.
1. Selecione **Baixar** e, em seguida, escolher onde pretende guardar o disco.

    ![Descarregue um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selecione **Guardar** e o seu disco começará a descarregar. O estado do download será exibido em **Atividades.**

    ![Estado de descarregamento](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

Com o Storage Explorer, pode copiar um disco de manhoso dentro ou em todas as regiões. Para copiar um disco:

1. A partir do **dropdown dos Discos** à esquerda, selecione o grupo de recursos que contém o disco que pretende copiar.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que pretende copiar e selecionar **Copiar**.

    ![Copiar um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. No painel esquerdo, selecione o grupo de recursos em que gostaria de colar o disco.

    ![Selecione grupo de recursos 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selecione **Pasta** no painel direito.

    ![Colar um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. No diálogo do **Disco de Pasta,** preencha os valores. Também pode especificar uma zona de disponibilidade em regiões apoiadas.

    ![Diálogo do disco de pasta](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selecione **Pasta** e o seu disco começará a copiar, o estado é apresentado em **Atividades**.

    ![Copiar estado da pasta](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Criar um instantâneo

1. A partir do **dropdown** dos Discos à esquerda, selecione o grupo de recursos que contém o disco que pretende tirar o instantâneo.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. À direita, selecione o disco que deseja tirar o instantâneo e **selecione Create Snapshot**.

    ![Criar um instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. No **Create Snapshot,** especifique o nome do instantâneo, bem como o grupo de recursos em que pretende criá-lo. Em seguida, selecione **Criar**.

    ![Criar diálogo instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Uma vez criado o instantâneo, pode selecionar **Open in Portal** em **Atividades** para ver o instantâneo no portal Azure.

    ![Abrir instantâneo no portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Passos seguintes
