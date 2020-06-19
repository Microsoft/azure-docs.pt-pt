---
title: Tutorial para verificar o carregamento de dados do Azure Data Box Disk para a conta de armazenamento | Microsoft Docs
description: Utilize este tutorial para saber como verificar os dados carregados do Azure Data Box Disk para a conta de armazenamento do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/04/2019
ms.author: alkohli
ms.openlocfilehash: 3f89d713003f1f4265a7ab7c467454af750fab48
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707808"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Tutorial: Verificar o carregamento de dados do Azure Data Box Disk

Este é o último tutorial da série: Implementar o Azure Data Box Disk. Neste tutorial, vai aprender o seguinte:

> [!div class="checklist"]
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se concluiu o [Tutorial: Devolver o Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Após a sua transportadora recolher os discos, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

![Discos recolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando a Microsoft receber e analisar o disco, o estado da tarefa será alterado para **Received (Recebido)** . 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são automaticamente copiados após os discos serem ligados a um servidor no datacenter do Azure. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se a cópia for concluída com erros, veja [resolver problemas de erros de carregamento](data-box-disk-troubleshoot-upload.md).

Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem. Os dados podem estar:

- Na(s) conta(s) do Armazenamento do Microsoft Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- No(s) grupo(s) de recursos dos discos geridos. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação da encomenda.

  - Se a cópia para os discos geridos no Azure tiver sido realizada com êxito, poderá aceder aos **Detalhes da encomenda** no portal do Azure e anotar o grupo de recursos especificado para os discos geridos.

      ![Ver detalhes da encomenda](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Aceda ao grupo de recursos anotado e localize os discos geridos.

      ![Grupo de recursos para discos geridos](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se copiou um VHDX ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de teste como um blob de blocos. Aceda à **Conta de armazenamento > Blobs** de teste e, em seguida, selecione o contentor apropriado – StandardSSD, StandardHDD ou PremiumSSD. Os VHDX/VHDs devem aparecer como blobs de blocos na conta de armazenamento de teste.
  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Após os dados terem sido carregados para o Azure, confirme se estão na(s) conta(s) de armazenamento antes de os eliminar na origem. Os dados podem estar:

- Na(s) conta(s) do Armazenamento do Microsoft Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

    - **Para blobs de blocos e blobs de páginas**: https://<nome_conta_armazenamento>.blob.core.windows.net/<containername>/files/a.txt

    - **Para Ficheiros do Azure**: https://<nome_conta_armazenamento>.file.core.windows.net/<sharename>/files/a.txt

- No(s) grupo(s) de recursos dos discos geridos. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação da encomenda.

::: zone-end

Para garantir que os dados foram carregados para o Azure, execute os seguintes passos:

1. Aceda à conta de armazenamento associada à sua encomenda de disco.
2. Aceda a **Serviço Blob > Procurar blobs**. É apresentada a lista de contentores. De forma correspondente à subpasta que criou nas pastas *BlockBlob* e *PageBlob*, os contentores com o mesmo nome são criados na sua conta de armazenamento.
    Se os nomes de pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure irá falhar.

3. Para se certificar de que todo o conjunto de dados foi carregado, utilize o Explorador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente à encomenda do Data Box Disk e, em seguida, observe a lista de contentores de blobs. Selecione um contentor, clique em **…Mais** e, em seguida, clique em **Estatísticas de pasta**. No painel **Atividades**, a estatística dessa pasta, incluindo o número de blobs e o tamanho total de blobs, é apresentado. O tamanho total de blobs em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas de pastas no Explorador de Armazenamento do Azure](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminação de dados do Data Box Disk

Após o carregamento para o Azure, o Data Box Disk elimina os dados dos discos de acordo com a norma [SP 800-88 da Norma](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk


Avance para as próximas instruções para saber como gerir o Data Box Disk através do portal do Azure.

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end




