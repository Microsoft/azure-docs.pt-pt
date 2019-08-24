---
title: Tutorial para verificar o carregamento de dados de Disco do Azure Data Box para a conta de armazenamento | Microsoft Docs
description: Use este tutorial para saber como verificar os dados carregados do seu Disco do Azure Data Box para a conta de armazenamento do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/22/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: e36f009422307b3b70091775d2288ee710839172
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014182"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Tutorial: Verificar o carregamento de dados do Disco do Azure Data Box

Este é o último tutorial da série: Implantar Disco do Azure Data Box. Neste tutorial, você aprenderá sobre:

> [!div class="checklist"]
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [tutorial: Retornar Disco do Azure Data Box](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Após a sua transportadora recolher os discos, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

![Discos recolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando a Microsoft receber e analisar o disco, o estado da tarefa será alterado para **Received (Recebido)** . 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são automaticamente copiados após os discos serem ligados a um servidor no datacenter do Azure. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se a cópia for concluída com erros, consulte [solucionar problemas de erros de upload](data-box-disk-troubleshoot-upload.md).

Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem. Seus dados podem estar em:

- Suas contas de armazenamento do Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como BLOBs de páginas e convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

  - Se sua cópia para discos gerenciados no Azure tiver sido bem-sucedida, você poderá ir para os **detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

      ![Exibir detalhes do pedido](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos observado e localize os discos gerenciados.

      ![Grupo de recursos para discos gerenciados](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se você copiou um VHDX, ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de blocos. Acesse sua conta de **armazenamento** de preparo > BLOBs e, em seguida, selecione o contêiner-StandardSSD, StandardHDD ou PremiumSSD apropriado. O VHDX/VHDs deve aparecer como BLOBs de blocos em sua conta de armazenamento de preparo.

Para garantir que os dados foram carregados para o Azure, execute os seguintes passos:

1. Aceda à conta de armazenamento associada à sua encomenda de disco.
2. Aceda a **Serviço Blob > Procurar blobs**. É apresentada a lista de contentores. De forma correspondente à subpasta que criou nas pastas *BlockBlob* e *PageBlob*, os contentores com o mesmo nome são criados na sua conta de armazenamento.
    Se os nomes de pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure irá falhar.

4. Para se certificar de que todo o conjunto de dados foi carregado, utilize o Explorador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente à encomenda de aluguer de discos e, em seguida, observe a lista de contentores de blobs. Selecione um contentor, clique em **…Mais** e, em seguida, clique em **Estatísticas de pasta**. No painel **Atividades**, a estatística dessa pasta, incluindo o número de blobs e o tamanho total de blobs, é apresentado. O tamanho total de blobs em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas de pastas no Explorador de Armazenamento do Azure](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminação de dados do Data Box Disk

Depois que a cópia for concluída e você tiver verificado que os dados estão na conta de armazenamento do Azure, os discos serão apagados com segurança de acordo com o padrão NIST.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk


Avance para as próximas instruções para saber como gerir o Data Box Disk através do portal do Azure.

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Depois que os dados são carregados no Azure, verifique se os dados estão nas contas de armazenamento antes de excluí-lo da origem. Seus dados podem estar em:

- Suas contas de armazenamento do Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

    - **Para BLOBs de bloco e blobs de página**: https://< storage_account_name >. blob. Core. Windows.<containername>net//files/a.txt

    - **Para arquivos do Azure**: https://< storage_account_name >. File. Core. Windows. NET<sharename>//files/a.txt

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como BLOBs de páginas e convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

::: zone-end


