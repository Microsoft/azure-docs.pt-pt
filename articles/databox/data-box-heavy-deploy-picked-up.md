---
title: Tutorial para enviar Azure Data Box Heavy de volta | Microsoft Docs
description: Saiba como enviar seu Azure Data Box Heavy para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: e438fb38afb649f6f4c7f595059ef64800977242
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240336"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Tutorial: Retornar Azure Data Box Heavy e verificar o carregamento de dados no Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Retornar Azure Data Box Heavy e verificar o carregamento de dados no Azure

::: zone-end

::: zone target = "docs"

Este tutorial descreve como retornar Azure Data Box Heavy e verificar os dados carregados no Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * preparação para envioPreparação para envio
> * Envie Data Box Heavy para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados de Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você concluiu o [tutorial: Copie dados para Azure Data Box e verifique](data-box-heavy-deploy-copy-data.md).
- Trabalhos de cópia concluídos. Preparação para o envio não poderá ser executado se os trabalhos de cópia estiverem em andamento.


## <a name="prepare-to-ship"></a>preparação para envioPreparação para envio

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>preparação para envioPreparação para envio

Antes de se preparar para enviar, verifique se os trabalhos de cópia estão concluídos.

1. Vá para a página Preparação para o envio na interface do usuário da Web local e inicie a preparação do envio.
2. Desative o dispositivo a partir da IU da Web local. Remova os cabos do dispositivo.

Agora você está pronto para enviar o dispositivo de volta.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Enviar Data Box Heavy de volta

1. Verifique se o dispositivo está desligado e se todos os cabos foram removidos. Coloque o spool e posicione com segurança os 4 cabos de alimentação na bandeja que você pode acessar na parte traseira do dispositivo.
2. O dispositivo envia o frete do LTL via FedEx nos EUA e na DHL na UE

    1. Entre em contato com [Data Box operações](mailto:DataBoxOps@microsoft.com) para informar sobre a retirada e para obter o rótulo de remessa de devolução.
    2. Chame o número local de sua operadora de entrega para agendar a retirada.
    3. Verifique se o rótulo de remessa é exibido de forma proeminente no exterior da remessa.
    4. Verifique se os antigos rótulos de envio da remessa anterior foram removidos do dispositivo.
3. Depois que o Data Box Heavy for coletado e verificado pela operadora, o status do pedido no portal será atualizado para **selecionado**. É também mostrado um ID de controlo.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois que a verificação for concluída, a Data Box Heavy será conectada à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

Verifique se os dados são carregados no Azure antes de excluí-los da origem. Seus dados podem estar em:

- Suas contas de armazenamento do Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como BLOBs de páginas e convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido. 

    - Se sua cópia para discos gerenciados no Azure tiver sido bem-sucedida, você poderá ir para os **detalhes do pedido** no portal do Azure e anotar os grupos de recursos especificados para discos gerenciados.

        ![Identificar grupos de recursos de disco gerenciado](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Vá para o grupo de recursos observado e localize os discos gerenciados.

        ![Disco gerenciado anexado a grupos de recursos](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se você copiou um VHDX ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de páginas, mas a conversão de VHD em disco gerenciado falhará. Acesse sua conta de **armazenamento** de preparo > BLOBs e, em seguida, selecione o SSD Standard de contêiner apropriado, HDD Standard ou SSD Premium. Os VHDs são carregados como BLOBs de páginas em sua conta de armazenamento de preparo.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando o dispositivo de Data Box Heavy está conectado à rede de datacenter do Azure, o carregamento de dados para o Azure é iniciado automaticamente. Data Box serviço notifica que a cópia de dados foi concluída por meio do portal do Azure.

- Consulte os registos de erros para verificar a existência de quaisquer falhas e executar as ações apropriadas.
- Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Eliminação de dados de Data Box Heavy
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Após a conclusão da eliminação, você pode [baixar o histórico do pedido](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * preparação para envioPreparação para envio
> * Envie Data Box Heavy para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados de Data Box Heavy

Avance para o artigo a seguir para saber como gerenciar Data Box Heavy por meio da interface do usuário da Web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


