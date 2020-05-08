---
title: Microsoft Azure Data Box Gateway utiliza casos de utilização de casos Microsoft Docs
description: Descreve os casos de utilização do Azure Data Box Gateway, uma solução de armazenamento de aparelhos virtuais que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: dde84f0973cc7e21e57574bbabe398b38581358f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562416"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Utilize casos para gateway de caixa de dados Azure

O Azure Data Box Gateway é um dispositivo de porta de entrada de armazenamento em nuvem que reside nas suas instalações e envia a sua imagem, meios de comunicação e outros dados para o Azure. Este portal de armazenamento em nuvem é uma máquina virtual aprovisionada no seu hipervisor. Escreve dados para este dispositivo virtual utilizando os protocolos NFS e SMB, que depois envia para o Azure. Este artigo fornece-lhe uma descrição detalhada dos cenários onde pode implementar este dispositivo.

Utilize o Portal da Caixa de Dados para os seguintes cenários:

- Para ingerir continuamente quantidades massivas de dados.
- Para o arquivo de dados em nuvem de forma segura e eficiente.
- Para transferência incremental de dados através da rede após a transferência inicial a granel é feita usando data Box.

Cada um destes cenários é descrito em detalhe nas secções seguintes.


## <a name="continuous-data-ingestion"></a>Ingestão contínua de dados

Uma das principais vantagens do Data Box Gateway é a capacidade de ingerir continuamente dados no dispositivo para copiar para a nuvem, independentemente do tamanho dos dados.

À medida que os dados são escritos para o dispositivo gateway, o dispositivo envia os dados para o Armazenamento Azure. O dispositivo gere automaticamente o armazenamento removendo os ficheiros localmente, mantendo os metadados quando atinge um determinado limiar. Manter uma cópia local dos metadados permite que o dispositivo gateway apenas carregue as alterações quando o ficheiro é atualizado. Os dados enviados para o seu dispositivo gateway devem ser de acordo com as diretrizes em [ressalvas](data-box-gateway-limits.md#data-upload-caveats)de upload de dados .

À medida que o dispositivo se enche de dados, começa a estrangular a taxa de entrada (conforme necessário) para corresponder à taxa a que os dados são carregados para a nuvem. Para monitorizar a ingestão contínua no dispositivo, utilize alertas. Estes alertas são levantados assim que a aceleração começa e são limpos uma vez que a aceleração tenha parado.

## <a name="cloud-archival-of-data"></a>Arquivo de dados em nuvem

Utilize o Portal da Caixa de Dados quando pretender reter os seus dados a longo prazo na nuvem. Pode utilizar o nível de armazenamento **archive** para retenção a longo prazo.

O nível de arquivo está otimizado para armazenar dados raramente acedidos durante pelo menos 180 dias. O nível **Archive** oferece os custos de armazenamento mais baixos, mas tem os custos de acesso mais elevados. Para mais informações, aceda ao [nível de acesso do Arquivo.](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)

### <a name="move-data-to-archive-tier"></a>Mover dados para o nível De Arquivo

Antes de começar, certifique-se de que tem um dispositivo Gateway de Caixa de Dados em execução. Siga os passos detalhados no [Tutorial: Prepare-se para implementar o Portal](data-box-gateway-deploy-prep.md) da Caixa de Dados Azure e continue a avançar para o próximo tutorial até ter um dispositivo operacional.

- Utilize o dispositivo Data Box Gateway para fazer o upload de dados para o Azure através do procedimento de transferência habitual, conforme descrito nos dados de [transferência através](data-box-gateway-deploy-add-shares.md)do Portal da Caixa de Dados .
- Depois de os dados serem carregados, terá de os deslocar para o nível de Arquivo. Pode definir o nível de blob de duas formas: script Azure PowerShell ou uma política de Gestão de Ciclo de Vida de Armazenamento Azure.  
    - Se utilizar o Azure PowerShell, siga estes [passos](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) para mover os dados para o nível De Arquivo.
    - Se utilizar a Azure Lifecycle Management, siga estes passos para mover os dados para o nível De Arquivo.
        - [Registe-se](/azure/storage/common/storage-lifecycle-management-concepts) na pré-visualização do serviço de gestão blob Lifecycle para utilizar o nível Archive.
        - Utilize a seguinte política para [arquivar dados sobre a ingestão](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Uma vez que as bolhas são marcadas como Arquivo, não podem mais ser modificadas pelo portal, a menos que sejam movidas para um nível quente ou frio. Se o ficheiro estiver no armazenamento local, quaisquer alterações feitas na cópia local (incluindo exclusões) não são enviadas para o nível de arquivo.
- Para ler os dados no armazenamento do Arquivo, deve ser rehidratado alterando o nível de bolha para quente ou fresco. [Refrescar a parte](data-box-gateway-manage-shares.md#refresh-shares) no portal não reidrata a bolha.

Para mais informações, saiba mais sobre como gerir o ciclo de vida de [armazenamento de blob Azure](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transferência inicial a granel seguida de transferência incremental

Utilize o Data Box e data Box Gateway juntos quando pretender fazer um upload a granel de uma grande quantidade de dados seguido de transferências incrementais. Utilize a Data Box para a transferência a granel num modo offline (semente inicial) e Data Box Gateway para transferências incrementais (feed em curso) sobre a rede.

### <a name="seed-the-data-with-data-box"></a>Semente os dados com Caixa de Dados

Siga estes passos para copiar os dados para Data Box e fazer upload para o Armazenamento Azure.

1. [Encomende a sua Caixa de Dados.](/azure/databox/data-box-deploy-ordered)
2. [Configurar a sua Caixa](/azure/databox/data-box-deploy-set-up)de Dados .
3. [Copiar dados para Data Box via SMB](/azure/databox/data-box-deploy-copy-data).
4. [Devolva a Caixa de Dados, verifique o upload de dados para o Azure](/azure/databox/data-box-deploy-picked-up).
5. Uma vez que o envio de dados para o Azure esteja completo, todos os dados devem estar em contentores de armazenamento Azure. Na conta de armazenamento da Data Box, vá ao recipiente Blob (e File) para se certificar de que todos os dados são copiados. Tome nota do nome do recipiente, pois utilizará este nome mais tarde. Por exemplo, na seguinte `databox` imagem, o recipiente será utilizado para a transferência incremental.

    ![Contentor com dados sobre Caixa de Dados](media/data-box-gateway-use-cases/data-container1.png)

Esta transferência a granel completa a fase inicial de semente.

### <a name="ongoing-feed-with-data-box-gateway"></a>Feed contínuo com Data Box Gateway

Siga estes passos para a ingestão contínua pelo Data Box Gateway. 

1. Crie uma partilha de nuvem no Data Box Gateway. Esta parte envia automaticamente quaisquer dados para a conta de Armazenamento Azure. Vá a **Partilhas** no seu recurso Data Box Gateway e clique **+ Adicionar partilha**.

    ![Clique +Adicionar partilha](media/data-box-gateway-use-cases/add-share1.png)

2. Certifique-se de que estes mapas de partilha para o recipiente que contém os dados semeados. Para **selecionar o recipiente blob,** escolha utilizar o **existente** e navegue até ao recipiente onde os dados da Data Box foram transferidos.

    ![Definições de partilha](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Depois da partilha ser criada, refresque a parte. Esta operação atualiza a partilha no local com o conteúdo do Azure.

    ![Partilhar o artigo Atualização](media/data-box-gateway-use-cases/refresh-share1.png)

    Quando a parte estiver sincronizada, o Data Box Gateway carregará as alterações incrementais se os ficheiros forem modificados no cliente.

## <a name="next-steps"></a>Passos seguintes

- Reveja os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Compreenda os [Limites do Data Box Gateway](data-box-gateway-limits.md).
- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.