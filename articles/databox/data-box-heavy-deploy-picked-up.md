---
title: Tutorial para enviar novamente pesadas de caixa de dados do Azure | Documentos da Microsoft
description: Saiba como enviar seu Azure dados caixa pesada à Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 31185900ede0fae74a04f98eaecee7379fb1c4fe
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427803"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure-preview"></a>Tutorial: Devolver pesadas de caixa de dados do Azure e verifique se o carregamento de dados para o Azure (pré-visualização)


Este tutorial descreve como devolver pesadas de caixa de dados do Azure e certifique-se de que os dados carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar dados caixa pesado para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados a partir de dados de caixa pesadas

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se:

- Concluiu o [Tutorial: Copiar dados para o Azure Data Box e certifique-se](data-box-heavy-deploy-copy-data.md).
- Tarefas de cópia estejam concluídas. Preparação para envio não é possível executar se tarefas de cópia estão em curso.

## <a name="prepare-to-ship"></a>Preparar para enviar

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Enviar dados caixa pesada novamente

1. Certifique-se de que o dispositivo está desligado e todos os cabos são removidos. Spool e coloque em segurança os cabos de 4 power na Bandeja do que pode aceder a partir da parte de trás do dispositivo.
2. Se o dispositivo está a ser enviado nos EUA ou na Europa, a operadora de envio é FedEx.

    1. Chame o número de local para a sua operadora de envio agendar a recolha.
    2. Entrar em contacto com [operações de caixa de dados](mailto:DataBoxOps@microsoft.com) para informar sobre a recolha e obter a etiqueta de envio de devolução.
    3. Certifique-se de que a etiqueta é apresentada no ecrã claro à frente do dispositivo.
    4. Certifique-se de que as etiquetas de envio antigo da remessa anterior são removidas do dispositivo.
3. Depois dos dados de caixa pesada é selecionada e analisada pela operadora, o estado da encomenda no portal do que as atualizações **pegou**. É também mostrado um ID de controlo.

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois da verificação estiver concluída, a pesadas de caixa de dados está ligada à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

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

## <a name="erasure-of-data-from-data-box-heavy"></a>Eliminação de dados a partir de dados de caixa pesadas
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Depois da eliminação estiver concluída, poderá [transferir o histórico de pedidos](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar dados caixa pesado para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados a partir de dados de caixa pesadas

Avance para o seguinte artigo para saber como gerir dados de caixa pesada através da IU da web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](./data-box-local-web-ui-admin.md)


