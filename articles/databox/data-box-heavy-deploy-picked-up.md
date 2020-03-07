---
title: Tutorial para enviar o Azure Data Box Heavy de volta | Microsoft Docs
description: Saiba como enviar o Azure Data Box Heavy para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: cfb9f54f5ba219a4db87144ab1e7ebff2b72b69e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380247"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Tutorial: Devolver caixa de dados Azure Heavy e verificar o upload de dados para o Azure

::: zone-end

::: zone target = "chromeless"

# <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Devolver o Azure Data Box Heavy e verificar os dados carregados para o Azure

::: zone-end

::: zone target = "docs"

Este tutorial descreve como devolver o Azure Data Box Heavy e como verificar se os dados foram carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box Heavy para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Heavy

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

- Completou o [Tutorial: copiar dados para a Caixa](data-box-heavy-deploy-copy-data.md)de Dados Do Azure e verificar .
- As tarefas de cópia foram concluídas. A preparação para envio não poderá ser executada se as tarefas de cópia estiverem em curso.


## <a name="prepare-to-ship"></a>Preparar para enviar

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Preparar para enviar

Antes da preparação para envio, verifique se as tarefas de cópia foram concluídas.

1. Aceda à página Preparação para envio na IU da Web local e inicie a preparação do envio.
2. Desative o dispositivo a partir da IU da Web local. Remova os cabos do dispositivo.

Agora já pode enviar o dispositivo de volta.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Enviar o Data Box Heavy de volta

1. Verifique se o dispositivo está desligado e se todos os cabos foram removidos. Enrole e posicione de forma segura os quatro cabos de alimentação na bandeja que se encontra na parte traseira do dispositivo.
2. O dispositivo é enviado como carga LTL via FedEx nos EUA e DHL na UE

    1. Contacte as [Operações do Data Box](mailto:DataBoxOps@microsoft.com) para fornecer as informações sobre a recolha e para obter a guia de remessa de devolução.
    2. Ligue para o número local da sua transportadora para agendar a recolha.
    3. Verifique se a guia de remessa é apresentada de forma proeminente no exterior da remessa.
    4. Verifique se as antigas guias de remessa de envios anteriores foram removidas do dispositivo.
3. Após a transportadora recolher e proceder à leitura do Data Box Heavy, o estado da encomenda no portal será atualizado para **Recolhida**. É também mostrado um ID de controlo.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois de a verificação estar concluída, o Data Box Heavy é ligado à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

Confirme se os dados foram carregados para o Azure antes de os eliminar da origem. Os dados podem estar:

- Na(s) conta(s) do Armazenamento do Microsoft Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- No(s) grupo(s) de recursos dos discos geridos. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação da encomenda. 

    - Se a cópia para os discos geridos no Azure tiver sido realizada com êxito, poderá aceder aos **Detalhes da encomenda** no portal do Azure e anotar os grupos de recursos especificados para os discos geridos.

        ![Identificar os grupos de recursos dos discos geridos](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Aceda ao grupo de recursos anotado e localize os discos geridos.

        ![Disco gerido anexado aos grupos de recursos](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se copiou um VHDX ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de teste como um blob de páginas, mas a conversão do VHD em disco gerido falhará. Aceda à **Conta de armazenamento > Blobs** de teste e, em seguida, selecione o contentor apropriado – SSD Standard, HDD Standard ou SSD Premium. Os VHDs são carregados como blobs de páginas na conta de armazenamento de teste.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando o dispositivo Data Box Heavy está ligado à rede do datacenter do Azure, o carregamento de dados para o Azure é iniciado automaticamente. O serviço Data Box notifica-o de que a cópia de dados está concluída através do portal do Azure.

- Consulte os registos de erros para verificar a existência de quaisquer falhas e executar as ações apropriadas.
- Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Eliminação de dados do Data Box Heavy
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Uma vez a eliminação concluída, pode [Transferir o histórico de encomendas](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box Heavy para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Heavy

Avance para o artigo seguinte para saber como gerir o Data Box Heavy através da IU da Web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


