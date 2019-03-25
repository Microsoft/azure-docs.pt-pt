---
title: Casos de utilização do Gateway de caixa de dados do Microsoft Azure | Documentos da Microsoft
description: Descreve os casos de utilização para o Gateway de caixa de dados do Azure, uma solução de armazenamento de aplicação virtual que permite-lhe transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 03/2/2019
ms.author: alkohli
ms.openlocfilehash: 37ec1d05d07f33343b9ff21380a277d00b242b7c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403804"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Casos de utilização para o Gateway de caixa de dados do Azure

O Gateway de caixa de dados do Azure é um dispositivo de gateway de armazenamento na cloud que reside no local e envia a sua imagem, mídia e outros dados para o Azure. Este gateway de armazenamento na cloud é uma máquina virtual aprovisionada no seu hipervisor. Escrever dados para este dispositivo virtual, utilizando os protocolos SMB e NFS, em seguida, envia para o Azure. Este artigo fornece uma descrição detalhada dos cenários em que pode implementar este dispositivo.

Utilize o Gateway de caixa de dados para os seguintes cenários:

- Ingerir continuamente quantidades enormes de dados.
- Para a nuvem de arquivamento dos dados de forma segura e eficiente.
- Transferência de dados incrementais através da rede depois da maior parte inicial de transferência é feita com o Data Box.

Cada um destes cenários é descrita detalhadamente nas secções subsequentes.


## <a name="continuous-data-ingestion"></a>Ingestão de dados contínua

Uma das principais vantagens caixa do Gateway de dados é a capacidade de ingerir continuamente os dados no dispositivo para copiar para a nuvem, independentemente do tamanho de dados.

Como os dados são escritos para o dispositivo de gateway, o dispositivo carrega os dados ao armazenamento do Azure. O dispositivo gere automaticamente o armazenamento ao remover os ficheiros localmente, mantendo os metadados quando ela atinge um certo limite. Manter uma cópia local dos metadados permite ao dispositivo de gateway carregar apenas as alterações quando o ficheiro é atualizado. Os dados carregados para o seu dispositivo de gateway devem estar de acordo com as diretrizes [limitações de carregamento de dados](data-box-gateway-limits.md#data-upload-caveats).

Como o dispositivo é preenchida com dados, este começa a limitação da taxa de entrada (conforme necessário) para corresponder a taxa a que os dados são carregados para a cloud. Para monitorizar a ingestão contínua no dispositivo, utilize alertas. Estes alertas são gerados quando a limitação é iniciado e são limpos quando a limitação parou.

## <a name="cloud-archival-of-data"></a>Arquivamento dos dados de cloud

Utilize o Gateway de caixa de dados quando pretende manter os seus dados de longo prazo na cloud. Pode utilizar o **arquivo** camada de armazenamento para retenção a longo prazo.

Camada de arquivo está otimizada para armazenar raramente são acedido dados durante, pelo menos, 180 dias. O **arquivo** camada oferece os custos de armazenamento mais baixos, mas tem os mais altos custos de acesso. Para obter mais informações, aceda a [camada de acesso de arquivo](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Mover dados para a camada de arquivo

Antes de começar, certifique-se de que tem um dispositivo de Gateway de caixa de dados em execução. Siga os passos detalhados [Tutorial: Preparar a implementação de Gateway de caixa de dados do Azure](data-box-gateway-deploy-prep.md) e manter a promover para o próximo tutorial depois de ter um dispositivo operacional.

- Utilizar o dispositivo de Gateway de caixa de dados para carregar dados para o Azure através do procedimento de transferência normal, conforme descrito em [transferir dados através do Gateway de dados de caixa](data-box-gateway-deploy-add-shares.md).
- Depois dos dados serem carregados, será necessário movê-lo para a camada de arquivo. Pode definir a camada de blob de duas formas: Script do Azure PowerShell ou uma política de gestão de ciclo de vida de armazenamento do Azure.  
    - Se utilizar o Azure PowerShell, siga estes [passos](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) para mover os dados para a camada de arquivo.
    - Se utilizar a gestão de ciclo de vida do Azure, siga estes passos para mover os dados para a camada de arquivo.
        - [Registar](/azure/storage/common/storage-lifecycle-management-concepts#register-for-preview) na pré-visualização do serviço de gestão do ciclo de vida do Blob para utilizar o arquivo do escalão.
        - Utilize a seguinte política para [ingerir dados de arquivo no](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest).
- Assim que os blobs são marcados como arquivo, eles já não podem ser modificados pelo gateway, a menos que são movidas para a camada frequente ou esporádica. Se o ficheiro estiver no armazenamento local, qualquer alteração feita a cópia local (incluindo eliminações) não é carregadas para o escalão de arquivo.
- Para ler dados no armazenamento de arquivo, tem de ser reativado ao alterar o escalão de blob para frequente ou esporádica. [Atualizar a partilha](data-box-gateway-manage-shares.md#refresh-shares) no gateway não reidratação de Blobs.

Para obter mais informações, saiba mais sobre como [gerir vida de armazenamento de Blobs do Azure](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transferência em massa inicial, seguida de transferência incremental

Utilize o Data Box e o Gateway de caixa de dados em conjunto quando deseja fazer um carregamento em massa de uma grande quantidade de dados, seguidos de transferências incrementais. Utilize o Data Box para a transferência em massa num modo offline (propagação inicial) e o Gateway de caixa de dados para transferências incrementais (feed em curso) através da rede.

### <a name="seed-the-data-with-data-box"></a>Propagar os dados com o Data Box

Siga estes passos para copiar os dados para Data Box e carregar para o armazenamento do Azure.

1. [Encomendar o Data Box](/azure/databox/data-box-deploy-ordered).
2. [Configurar o Data Box](/azure/databox/data-box-deploy-set-up).
3. [Copiar dados para o Data Box através do SMB](/azure/databox/data-box-deploy-copy-data).
4. [Devolver o Data Box, verifique se o carregamento de dados para o Azure](/azure/databox/data-box-deploy-picked-up).
5. Quando o carregamento de dados para o Azure estiver concluído, todos os dados devem ser em contentores de armazenamento do Azure. Na conta de armazenamento para o Data Box, vá para o contentor de BLOBs (e ficheiro) para se certificar de que todos os dados são copiados. Tome nota do nome do contentor, porque irá utilizar este nome mais tarde. Por exemplo, na seguinte captura de ecrã, `databox` contentor será utilizado para a transferência incremental.

    ![Contentor com os dados no Data Box](media/data-box-gateway-use-cases/data-container1.png)

Esta transferência em massa conclui a fase de propagação inicial.

### <a name="ongoing-feed-with-data-box-gateway"></a>Contínuas com o Gateway de caixa de dados do feed

Siga estes passos para ingestão em curso pelo Gateway de caixa de dados.

1. Crie uma partilha de cloud no Gateway de caixa de dados. Esta partilha carrega automaticamente todos os dados para a conta de armazenamento do Azure. Aceda a **partilhas** no seu recurso do Gateway de caixa de dados e clique em **+ Adicionar partilha**.

    ![Clique em + Adicionar partilha](media/data-box-gateway-use-cases/add-share1.png)

2. Certifique-se de que esta partilha é mapeado para o contentor que contém os dados propagados. Para **contentor de BLOBs Select**, escolha **utilizar existente** e navegue para o contentor onde os dados de caixa de dados foi transferidos.

    ![Definições de partilha](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Depois de criar a partilha, atualize a partilha. Esta operação atualiza a partilha no local com o conteúdo do Azure.

    ![Atualizar partilha](media/data-box-gateway-use-cases/refresh-share1.png)

    Quando a partilha está sincronizada, o Gateway de caixa de dados irá carregar as alterações incrementais se os arquivos foram modificados no cliente.

## <a name="next-steps"></a>Passos Seguintes

- Reveja os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Compreenda os [Limites do Data Box Gateway](data-box-gateway-limits.md).
- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.