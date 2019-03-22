---
title: Enviar o Microsoft Azure Data Box Disk de volta | Microsoft Docs
description: Utilize este tutorial para aprender enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 0dd0474ad1ad360fd82cfdf746d2e9837f74833a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108380"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o disco do Azure Data Box e verifique se o carregamento de dados para o Azure

Este é o último tutorial da série: Implemente o disco do Azure Data Box. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que concluiu o [Tutorial: Copiar dados para o disco do Azure Data Box e certifique-se](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Enviar o Data Box Disk de volta

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio.
3. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se a etiqueta estiver danificada ou perder-se, transfira uma nova etiqueta de envio do portal do Azure e coloque-a no dispositivo. Aceda a **Descrição geral > Transferir etiqueta de envio**.

    ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

    ![Examinar a etiqueta de envio](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
5. Se estiver a devolver o dispositivo nos EUA, contacte a UPS para agendar uma recolha. Se estiver a devolver o dispositivo na Europa com a DHL, solicite uma recolha à DHL acedendo ao respetivo site e especificando o número de carta de porte aéreo. Aceda ao site da DHL Express do seu país e selecione **Book a Courier Collection (Agendar uma Recolha por Estafeta) > eReturn Shipment (Envio eReturn)**.

    ![Devolução de DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    Especifique o número de carta de porte aéreo e clique em **Schedule Pickup (Agendar Recolha)** para marcar uma recolha.

      ![Agendar recolha](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. Após a sua transportadora recolher os discos, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)**. É também mostrado um ID de controlo.

    ![Discos recolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o disco, o estado da tarefa será alterado para **Received (Recebido)**. 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são automaticamente copiados após os discos serem ligados a um servidor no datacenter do Azure. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)**.

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem. Os dados podem estar:

- Sua conta de armazenamento do Azure (s). Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Os grupos de recursos de disco gerido. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e, em seguida, convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação de ordem.

  - Se a sua cópia para os managed disks no Azure foi concluída com êxito, pode ir para o **detalhes de pedidos** no portal do Azure e fazer uma observação do grupo de recursos especificada para discos geridos.

      ![Ver detalhes dos pedidos](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos observado e localize os discos geridos.

      ![Grupo de recursos para discos geridos](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se tiver copiado um VHDX ou um VHD de diferenciação/dinâmico, o VHD/VHDX é carregado para a conta de armazenamento de teste como um blob de blocos. Aceda a sua transição **conta de armazenamento > Blobs** e, em seguida, selecione o contentor - StandardSSD, StandardHDD ou PremiumSSD adequado. O VHD/VHDX deve aparecer como blobs de blocos na conta de armazenamento temporário.

Para garantir que os dados foram carregados para o Azure, execute os seguintes passos:

1. Aceda à conta de armazenamento associada à sua encomenda de disco.
2. Aceda a **Serviço Blob > Procurar blobs**. É apresentada a lista de contentores. De forma correspondente à subpasta que criou nas pastas *BlockBlob* e *PageBlob*, os contentores com o mesmo nome são criados na sua conta de armazenamento.
    Se os nomes de pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure irá falhar.

4. Para se certificar de que todo o conjunto de dados foi carregado, utilize o Explorador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente à encomenda de aluguer de discos e, em seguida, observe a lista de contentores de blobs. Selecione um contentor, clique em **…Mais** e, em seguida, clique em **Estatísticas de pasta**. No painel **Atividades**, a estatística dessa pasta, incluindo o número de blobs e o tamanho total de blobs, é apresentado. O tamanho total de blobs em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas de pastas no Explorador de Armazenamento do Azure](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminação de dados do Data Box Disk

Após a cópia ser concluída e se certificar de que os dados estão na conta de armazenamento do Azure, os discos são apagados de forma segura, segundo a norma NIST.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk


Avance para as próximas instruções para saber como gerir o Data Box Disk através do portal do Azure.

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)


