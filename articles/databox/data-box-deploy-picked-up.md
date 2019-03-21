---
title: Enviar o Microsoft Azure Data Box de volta | Microsoft Docs
description: Aprenda a enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: 72d6ce58a986ddd0d0976d99de5ca3426d78f0b9
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287167"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verifique se o carregamento de dados para o Azure

Este tutorial descreve como devolver o Azure Data Box e certificar-se de que os dados foram carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se:

- Já tiver concluído a [Tutorial: Copiar dados para o Azure Data Box e certifique-se](data-box-deploy-copy-data.md). 
- Tarefas de cópia estejam concluídas. Preparação para envio não é possível executar se tarefas de cópia estão em curso.

## <a name="prepare-to-ship"></a>Preparar para enviar

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Enviar o Data Box de volta

1. Certifique-se de que o dispositivo está desligado e os cabos são removidos. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
2. Certifique-se de que a etiqueta de envio é apresentada no ecrã de tinta eletrónica e agende uma recolha com a sua operadora. Se a etiqueta está danificada ou perdida ou não é apresentada no ecrã do E-tinta, contacte o Support da Microsoft. Se o suporte sugere, em seguida, pode aceder à **descrição geral > Download etiqueta de envio** no portal do Azure. Transfira a etiqueta de envio e affix no dispositivo. 
3. Agende uma recolha com UPS se retornando o dispositivo. Para agendar uma recolha:

    - Chame no-BREAK local (número gratuito de incidir específico do país).
    - Em sua chamada citar a remessa inversa número, como mostra a exibição de tinta E ou a etiqueta impressa de controle.
    - Se o número de controlo não é indicado, UPS exigirá que paga um custo adicional durante a recolha.

    Em vez da recolha de agendamento, também pode remover o desativar a caixa de dados no local de entrega mais próximo.
4. Após a sua transportadora recolher e analisar o Data Box, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)**. É também mostrado um ID de controlo.

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)**. O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois de a verificação estar concluída, o Data Box é ligado à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)**.

Certifique-se de que os seus dados são carregados para o Azure antes de eliminar a partir da origem. Os dados podem estar:

- Sua conta de armazenamento do Azure (s). Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Os grupos de recursos de disco gerido. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e, em seguida, convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação de ordem. 

    - Se a sua cópia para os managed disks no Azure foi concluída com êxito, pode ir para o **detalhes de pedidos** no portal do Azure e fazer uma observação dos grupos de recursos especificada para discos geridos.

        ![Identificar os grupos de recursos de disco gerido](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Vá para o grupo de recursos observado e localize os discos geridos.

        ![Disco ligado a grupos de recursos gerido](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se tiver copiado um VHDX ou um VHD de diferenciação/dinâmico, o VHD/VHDX é carregado para a conta de armazenamento de teste como um blob de página, mas a conversão de VHD para falha de disco gerido. Aceda a sua transição **conta de armazenamento > Blobs** e, em seguida, selecione o contentor adequado - Standard SSD, Standard HDD ou Premium SSD. Os VHDs são carregados como blobs de páginas na sua conta de armazenamento de teste.

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

Avance para o artigo seguinte para saber como gerir o Data Box através da IU da Web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](./data-box-local-web-ui-admin.md)


