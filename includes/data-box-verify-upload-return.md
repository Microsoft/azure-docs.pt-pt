---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631546"
---
Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois de a verificação estar concluída, o Data Box é ligado à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

Confirme se os dados foram carregados para o Azure antes de os eliminar da origem. Os dados podem estar:

- Na(s) conta(s) do Armazenamento do Microsoft Azure. Quando copia os dados para o Data Box, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure:

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- No(s) grupo(s) de recursos dos discos geridos. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação da encomenda. 

    - Se a cópia para os discos geridos no Azure tiver sido realizada com êxito, poderá aceder aos **Detalhes da encomenda** no portal do Azure e anotar os grupos de recursos especificados para os discos geridos.

        ![Identificar os grupos de recursos dos discos geridos](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Aceda ao grupo de recursos anotado e localize os discos geridos.

        ![Disco gerido anexado aos grupos de recursos](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Se copiou um VHDX ou um VHD dinâmico ou diferencial, o VHDX ou VHD será carregado para a conta de armazenamento de teste como um blob de páginas, mas a conversão do VHD num disco gerido falhará. Aceda à **Conta de armazenamento > Blobs** de teste e selecione o contentor apropriado – SSD Standard, HDD Standard ou SSD Premium. Os VHDs são carregados como blobs de páginas na conta de armazenamento de teste e acrescidos de custos.


## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

Avance para o artigo seguinte para saber como gerir o Data Box através da IU da Web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](../articles/databox/data-box-local-web-ui-admin.md)