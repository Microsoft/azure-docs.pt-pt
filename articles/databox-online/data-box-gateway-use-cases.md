---
title: Microsoft Azure Data Box Gateway usa casos Microsoft Docs
description: Descreve os casos de utilização do Azure Data Box Gateway, uma solução virtual de armazenamento de aparelhos que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: dde84f0973cc7e21e57574bbabe398b38581358f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82562416"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Use caixas para Azure Data Box Gateway

Azure Data Box Gateway é um dispositivo de gateway de armazenamento em nuvem que reside nas suas instalações e envia a sua imagem, meios de comunicação e outros dados para o Azure. Este gateway de armazenamento em nuvem é uma máquina virtual a provisionada no seu hipervisor. Escreve dados para este dispositivo virtual utilizando os protocolos NFS e SMB, que depois envia para a Azure. Este artigo fornece-lhe uma descrição detalhada dos cenários onde pode implantar este dispositivo.

Utilize o Portal da Caixa de Dados para os seguintes cenários:

- Para ingerir continuamente quantidades massivas de dados.
- Para o arquivo em nuvem de dados de forma segura e eficiente.
- Para transferência incremental de dados sobre a rede após a transferência inicial a granel é feita usando a Caixa de Dados.

Cada um destes cenários é descrito em detalhe nas secções seguintes.


## <a name="continuous-data-ingestion"></a>Ingestão contínua de dados

Uma das principais vantagens do Data Box Gateway é a capacidade de ingerir continuamente dados no dispositivo para copiar para a nuvem, independentemente do tamanho dos dados.

À medida que os dados são escritos no dispositivo gateway, o dispositivo envia os dados para o Azure Storage. O dispositivo gere automaticamente o armazenamento removendo os ficheiros localmente, mantendo os metadados quando atinge um determinado limiar. Manter uma cópia local dos metadados permite que o dispositivo gateway apenas carre faça o upload das alterações quando o ficheiro é atualizado. Os dados enviados para o seu dispositivo gateway devem estar de acordo com as diretrizes em [data upload ressalvas](data-box-gateway-limits.md#data-upload-caveats).

À medida que o dispositivo se enche de dados, começa a estrangular a taxa de entrada (se necessário) para corresponder à taxa a que os dados são enviados para a nuvem. Para monitorizar a ingestão contínua no aparelho, utilize alertas. Estes alertas são levantados assim que o estrangulamento começa e são limpos uma vez que o estrangulamento tenha parado.

## <a name="cloud-archival-of-data"></a>Arquivo em nuvem de dados

Utilize o Data Box Gateway quando pretender reter os seus dados a longo prazo na nuvem. Pode utilizar o nível de armazenamento **Archive** para retenção a longo prazo.

O nível de arquivo é otimizado para armazenar dados raramente acedidos durante pelo menos 180 dias. O nível **Archive** oferece os custos de armazenamento mais baixos, mas tem os custos de acesso mais elevados. Para mais informações, aceda ao [nível de acesso ao Arquivo.](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)

### <a name="move-data-to-archive-tier"></a>Mover dados para o nível de arquivo

Antes de começar, certifique-se de que tem um dispositivo de Gateway data box em execução. Siga os passos detalhados no [Tutorial: Prepare-se para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md) e continue a avançar para o próximo tutorial até ter um dispositivo operacional.

- Utilize o dispositivo Data Box Gateway para enviar dados para a Azure através do procedimento de transferência habitual, tal como descrito nos [dados de transferência através do Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- Depois de os dados terem sido carregados, terá de os transferir para o nível de Arquivo. Pode definir o nível blob de duas maneiras: script Azure PowerShell ou uma política de Gestão de Ciclo de Vida de Armazenamento Azure.  
    - Se utilizar o Azure PowerShell, siga estes [passos](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) para mover os dados para o nível de Arquivo.
    - Se utilizar a Gestão do Ciclo de Vida Azure, siga estes passos para mover os dados para o nível Archive.
        - [Registe-se](/azure/storage/common/storage-lifecycle-management-concepts) para a pré-visualização do serviço de gestão blob Lifecycle para utilizar o nível Archive.
        - Utilize a seguinte política para [arquivar dados sobre ingerir](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Uma vez que as bolhas são marcadas como Archive, elas não podem mais ser modificadas pelo gateway a menos que sejam movidas para um nível quente ou frio. Se o ficheiro estiver no armazenamento local, quaisquer alterações efetuadas na cópia local (incluindo eliminações) não serão carregadas para o nível de arquivo.
- Para ler os dados no armazenamento do Arquivo, deve ser rehidratado alterando o nível do blob para quente ou fresco. [Refrescar a parte](data-box-gateway-manage-shares.md#refresh-shares) no gateway não hidrata a bolha.

Para mais informações, saiba mais sobre como gerir o ciclo de [vida de armazenamento de blob Azure Blob](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transferência inicial a granel seguida de transferência incremental

Utilize a Caixa de Dados e o Gateway da Caixa de Dados juntos quando pretender fazer um upload em massa de uma grande quantidade de dados seguido de transferências incrementais. Utilize a Caixa de Dados para a transferência a granel num modo offline (semente inicial) e Gateway de Caixa de Dados para transferências incrementais (feed em curso) sobre a rede.

### <a name="seed-the-data-with-data-box"></a>Semear os dados com a Caixa de Dados

Siga estes passos para copiar os dados para a Caixa de Dados e faça o upload para o Azure Storage.

1. [Encomende a sua Caixa de Dados.](/azure/databox/data-box-deploy-ordered)
2. [Configurar a sua Caixa de Dados.](/azure/databox/data-box-deploy-set-up)
3. [Copie os dados para a Caixa de Dados via SMB](/azure/databox/data-box-deploy-copy-data).
4. [Devolva a Caixa de Dados, verifique o upload dos dados para o Azure](/azure/databox/data-box-deploy-picked-up).
5. Uma vez que o upload de dados para Azure esteja completo, todos os dados devem estar em recipientes de armazenamento Azure. Na conta de armazenamento da Caixa de Dados, vá ao recipiente Blob (e Arquivo) para se certificar de que todos os dados são copiados. Tome nota do nome do recipiente, pois utilizará este nome mais tarde. Por exemplo, na imagem seguinte, o `databox` recipiente será utilizado para a transferência incremental.

    ![Contentor com dados na Caixa de Dados](media/data-box-gateway-use-cases/data-container1.png)

Esta transferência a granel completa a fase inicial de sementeira.

### <a name="ongoing-feed-with-data-box-gateway"></a>Feed em curso com data box gateway

Siga estes passos para a ingestão contínua por Data Box Gateway. 

1. Crie uma partilha na nuvem no Data Box Gateway. Esta partilha envia automaticamente quaisquer dados para a conta de Armazenamento Azure. Vá a **Partilhas** no seu recurso Data Box Gateway e clique **+ Adicionar a partilha**.

    ![Clique +Adicionar a partilha](media/data-box-gateway-use-cases/add-share1.png)

2. Certifique-se de que este mapa de partilha para o recipiente que contém os dados semeados. Para **Selecionar o recipiente blob**, escolha **Use e** navegue no recipiente onde os dados da Caixa de Dados foram transferidos.

    ![Configurações de partilha](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Após a criação da ação, refresque a parte. Esta operação atualiza a partilha no local com o conteúdo do Azure.

    ![Atualização de ações](media/data-box-gateway-use-cases/refresh-share1.png)

    Quando a partilha for sincronizada, o Data Box Gateway carregará as alterações incrementais se os ficheiros forem modificados no cliente.

## <a name="next-steps"></a>Passos seguintes

- Reveja os [requisitos de sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Compreenda os [Limites do Data Box Gateway](data-box-gateway-limits.md).
- Implemente o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.