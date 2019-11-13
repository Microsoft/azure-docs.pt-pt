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
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013780"
---
Gerenciador de Armazenamento 1.10.0 permite que os usuários carreguem, baixem e copiem discos gerenciados, bem como criem instantâneos. Por causa desses recursos adicionais, você pode usar Gerenciador de Armazenamento para migrar dados do local para o Azure e migrar dados entre regiões do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisará do seguinte:
- Uma subscrição do Azure.
- Um ou mais Azure Managed disks
- A versão mais recente do [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

Se o Gerenciador de Armazenamento não estiver conectado ao Azure, você não poderá usá-lo para gerenciar recursos. Esta seção vai se conectar à sua conta do Azure para que você possa gerenciar recursos usando Gerenciador de Armazenamento.

1. Inicie o Gerenciador de Armazenamento do Azure e clique no ícone de **plug-in** à esquerda.

    ![Clique no ícone de plug-in](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selecione **Adicionar uma conta do Azure**e clique em **Avançar**.

    ![Adicionar uma Conta do Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Na caixa de diálogo **entrar no Azure** , insira suas credenciais do Azure.

    ![Caixa de diálogo entrar no Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selecione a sua subscrição na lista e clique em **Aplicar**.

    ![Selecione a sua subscrição](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Carregar um disco gerenciado de um VHD local

1. No painel esquerdo, expanda **discos** e selecione o grupo de recursos no qual você deseja carregar o disco.

    ![Selecionar grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecione **Upload**.

    ![Selecionar a operação carregar](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. Em **carregar VHD** , ESPECIFIQUE o VHD de origem, o nome do disco, o tipo de sistema operacional, a região em que você deseja carregar o disco, bem como o tipo de conta. Em algumas regiões, as zonas de disponibilidade têm suporte, para essas regiões, você pode selecionar uma zona de sua escolha.
1. Selecione **criar** para começar a carregar o disco.

    ![Caixa de diálogo Carregar VHD](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. O status do carregamento agora será exibido em **atividades**.

    ![Status do carregamento](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Se o upload tiver sido concluído e você não vir o disco no painel direito, selecione **Atualizar**.

## <a name="download-a-managed-disk"></a>Baixar um disco gerenciado

As etapas a seguir explicam como baixar um disco gerenciado para um VHD local. O estado de um disco deve ser **desanexado** para ser baixado, você não pode baixar um disco **anexado** .

1. No painel esquerdo, se ainda não estiver expandido, expanda **discos** e selecione o grupo de recursos do qual você deseja baixar o disco.

    ![Selecionar grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que você deseja baixar.
1. Selecione **baixar** e, em seguida, escolha onde deseja salvar o disco.

    ![Baixar um disco gerenciado](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selecione **salvar** e seu disco começará a ser baixado. O status do download será exibido em **atividades**.

    ![Status do download](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

Com o Gerenciador de Armazenamento, você pode copiar um disco de gerenciada dentro ou entre regiões. Para copiar um disco:

1. No menu suspenso **discos** à esquerda, selecione o grupo de recursos que contém o disco que você deseja copiar.

    ![Selecionar grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que você deseja copiar e selecione **copiar**.

    ![Copiar um disco gerido](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. No painel esquerdo, selecione o grupo de recursos no qual você gostaria de colar o disco.

    ![Selecionar grupo de recursos 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selecione **colar** no painel à direita.

    ![Colar um disco gerenciado](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Na caixa de diálogo **colar disco** , preencha os valores. Você também pode especificar uma zona de disponibilidade em regiões com suporte.

    ![Caixa de diálogo colar disco](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selecione **colar** e o disco começará a ser copiado, o status será exibido em **atividades**.

    ![Copiar status de colagem](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Criar um instantâneo

1. No menu suspenso **discos** à esquerda, selecione o grupo de recursos que contém o disco que você deseja fazer o instantâneo.

    ![Selecionar grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. À direita, selecione o disco que você gostaria de fazer o instantâneo e selecione **criar instantâneo**.

    ![Criar um instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. Em **criar instantâneo**, especifique o nome do instantâneo, bem como o grupo de recursos no qual você deseja criá-lo. Em seguida, selecione **Criar**.

    ![Caixa de diálogo Criar instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Depois que o instantâneo tiver sido criado, você poderá selecionar **abrir no portal** em **atividades** para exibir o instantâneo no portal do Azure.

    ![Abrir instantâneo no portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Passos seguintes
