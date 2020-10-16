---
title: Use o Azure Storage Explorer para gerir discos geridos a Azure
description: Aprenda a carregar, descarregar e migrar um disco gerido pelo Azure através das regiões e criar uma imagem instantânea de um disco gerido, utilizando o Azure Storage Explorer.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88749372"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Use o Azure Storage Explorer para gerir discos geridos a Azure

O Storage Explorer 1.10.0 permite aos utilizadores carregar, descarregar e copiar discos geridos, bem como criar instantâneos. Devido a estas capacidades adicionais, pode utilizar o Storage Explorer para migrar dados do local para o Azure e migrar dados através das regiões de Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, você precisará do seguinte:
- Uma subscrição do Azure
- Um ou mais discos geridos pela Azure
- A mais recente versão do [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

Se o seu Explorador de Armazenamento não estiver ligado ao Azure, não poderá usá-lo para gerir recursos. Esta secção passa por ligar à sua conta Azure para que possa gerir recursos utilizando o Storage Explorer.

1. Lance o Azure Storage Explorer e clique no ícone **plug-in** à esquerda.

    ![Clique no ícone plug-in](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. **Selecione Adicionar uma Conta Azure**e, em seguida, clique em **Seguinte**.

    ![Adicionar uma Conta do Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Na caixa **de diálogo Azure,** insira as suas credenciais Azure.

    ![Sinal de Azure no diálogo](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selecione a sua subscrição na lista e clique em **Aplicar**.

    ![Selecione a sua subscrição](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Faça o upload de um disco gerido a partir de um VHD on-prem

1. No painel esquerdo, expanda **os Discos** e selecione o grupo de recursos para o quais pretende carregar o disco.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecione **Carregar**.

    ![Selecionar a operação carregar](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. No **Upload VHD** especificar a sua fonte VHD, o nome do disco, o tipo de SO, a região para a qual pretende carregar o disco, bem como o tipo de conta. Em algumas regiões as zonas de disponibilidade são suportadas, para essas regiões pode selecionar uma zona à sua escolha.
1. Selecione **Criar** para começar a carregar o seu disco.

    ![Upload vhd diálogo](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. O estado do upload será agora exibido nas **Atividades**.

    ![Estado do upload](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Se o upload tiver terminado e não vir o disco no painel direito, selecione **Refresh**.

## <a name="download-a-managed-disk"></a>Faça o download de um disco gerido

Os passos seguintes explicam como descarregar um disco gerido para um VHD on-prem. O estado de um disco tem de ser **desapegado** para ser descarregado, não é possível descarregar um disco **anexo.**

1. No painel esquerdo, se ainda não estiver expandido, expanda os **Discos** e selecione o grupo de recursos a partir do qual pretende descarregar o seu disco.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que pretende descarregar.
1. Selecione **Baixar** e, em seguida, escolha onde pretende guardar o disco.

    ![Faça o download de um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. **Selecione Save** e o seu disco começará a descarregar. O estado do download será exibido nas **Atividades.**

    ![Estado do download](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

Com o Storage Explorer, pode copiar um disco manged dentro ou em todas as regiões. Para copiar um disco:

1. A partir do **dropdown** dos Discos à esquerda, selecione o grupo de recursos que contém o disco que pretende copiar.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que pretende copiar e selecione **Copy**.

    ![Copiar um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. No painel esquerdo, selecione o grupo de recursos que pretende colar o disco.

    ![Selecione grupo de recursos 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. **Selecione Pasta** no painel direito.

    ![Cole um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. No diálogo **do disco pasta,** preencha os valores. Também pode especificar uma zona de disponibilidade em regiões apoiadas.

    ![Diálogo de disco de pasta](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. **Selecione Pasta** e o seu disco começará a copiar, o estado é apresentado em **Atividades**.

    ![Copiar estado da pasta](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Criar um instantâneo

1. A partir do **dropdown** dos Discos à esquerda, selecione o grupo de recursos que contém o disco que pretende ser instantâneo.

    ![Selecione grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. À direita, selecione o disco que pretende fazer e selecione **Create Snapshot**.

    ![Criar um instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. In **Create Snapshot**, especifique o nome do instantâneo, bem como o grupo de recursos em que pretende criá-lo. Em seguida, selecione **Criar**.

    ![Criar diálogo instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Uma vez criado o instantâneo, pode selecionar **Abrir no Portal** em **Atividades** para visualizar o instantâneo no portal Azure.

    ![Abrir instantâneo no portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Passos seguintes


Saiba como criar um VM a [partir de um VHD utilizando o portal Azure](windows/create-vm-specialized-portal.md).

Saiba [como anexar um disco de dados gerido a um VM do Windows utilizando o portal Azure](windows/attach-managed-disk-portal.md).