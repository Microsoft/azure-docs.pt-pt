---
title: Escolher uma solução do Azure para transferência de dados | Microsoft Docs
description: Saiba como escolher uma solução do Azure para transferência de dados com base em tamanhos de dados e largura de banda de rede disponível em seu ambiente
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 838d65da90ec0daef69375e5a75bcb497a0c3512
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900391"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Escolher uma solução do Azure para transferência de dados

Este artigo fornece uma visão geral de algumas das soluções comuns de transferência de dados do Azure. O artigo também fornece links para as opções recomendadas dependendo da largura de banda de rede em seu ambiente e do tamanho dos dados que você pretende transferir.

## <a name="types-of-data-movement"></a>Tipos de movimentação de dados

A transferência de dados pode estar offline ou pela conexão de rede. Escolha sua solução dependendo do seu:

- **Tamanho dos dados** -tamanho dos dados destinados para transferência,
- **Frequência de transferência** -ingestão de dados única ou periódica e
- **Rede** – largura de banda disponível para transferência de dados em seu ambiente.

A movimentação de dados pode ser dos seguintes tipos:

- **Transferência offline usando dispositivos freqüentemente** – use dispositivos de freqüentemente físicos quando desejar fazer uma transferência de dados em massa de uso único offline. A Microsoft envia um disco ou um dispositivo especializado seguro. Como alternativa, você pode comprar e enviar seus próprios discos. Você copia dados para o dispositivo e, em seguida, envia-os para o Azure onde os dados são carregados.  As opções disponíveis para esse caso são Disco do Data Box, Data Box, Data Box Heavy e importação/exportação (Use seus próprios discos).

- **Transferência de rede** -você transfere seus dados para o Azure por meio de sua conexão de rede. Isso pode ser feito de várias maneiras.

    - **Interface gráfica** : se, ocasionalmente, você transferir apenas alguns arquivos e não precisar automatizar a transferência de dados, poderá escolher uma ferramenta de interface gráfica como Gerenciador de armazenamento do Azure ou uma ferramenta de exploração baseada na web no portal do Azure.
    - **Transferência programática ou com script** -você pode usar ferramentas de software otimizadas que fornecemos ou chamamos diretamente para nossos SDKs/APIs REST. As Ferramentas programáveis disponíveis são AzCopy, Azure PowerShell e CLI do Azure. Para a interface programática, use um dos SDKs para .NET, Java, Python, Node/JS, C++, go, php ou Ruby.
    - **Dispositivos locais** -fornecemos a você um dispositivo físico ou virtual que reside em seu datacenter e otimiza a transferência de dados pela rede. Esses dispositivos também fornecem um cache local de arquivos usados com frequência. O dispositivo físico é o Data Box Edge e o dispositivo virtual é o Gateway do Data Box. Ambos são executados permanentemente em seu local e se conectam ao Azure pela rede.
    - **Pipeline de dados gerenciados** – você pode configurar um pipeline de nuvem para transferir regularmente arquivos entre vários serviços do Azure, localmente ou uma combinação de dois. Use Azure Data Factory para configurar e gerenciar pipelines de dados e mover e transformar dados para análise.

O Visual a seguir ilustra as diretrizes para escolher as várias ferramentas de transferência de dados do Azure, dependendo da largura de banda da rede disponível para transferência, tamanho dos dados destinados para transferência e frequência da transferência.

![Ferramentas de transferência de dados do Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Os limites superiores dos dispositivos de transferência offline – Disco do Data Box, Data Box e Data Box Heavy podem ser estendidos colocando-se vários pedidos de um tipo de dispositivo.*

## <a name="selecting-a-data-transfer-solution"></a>Selecionando uma solução de transferência de dados

Responda às seguintes perguntas para ajudar a selecionar uma solução de transferência de dados:

- Sua largura de banda de rede disponível está limitada ou não existe e você deseja transferir grandes conjuntos de altos?
  
    Em caso afirmativo, consulte: [Cenário 1: Transfira conjuntos de grandes volumes com largura de banda](storage-solution-large-dataset-low-network.md)de rede insuficiente ou baixa.
- Você deseja transferir grandes conjuntos de volumes pela rede e tem uma largura de banda de rede moderada para alta?

    Em caso afirmativo, consulte: [Cenário 2: Transfira conjuntos de grandes volumes com largura de banda](storage-solution-large-dataset-moderate-high-network.md)de rede moderada para alta.
- Você deseja, ocasionalmente, transferir apenas alguns arquivos pela rede?

    Em caso afirmativo [, consulte Cenário 3: Transfira pequenos conjuntos de valores com largura de banda](storage-solution-small-dataset-low-moderate-network.md)de rede limitada à moderada.
- Você está procurando por transferência de dados pontual em intervalos regulares?

    Em caso afirmativo, use as opções de script/programática descritas [no cenário 4: Transferências](storage-solution-periodic-data-transfer.md)de dados periódicas.
- Você está procurando transferência de dados contínua em andamento?

    Em caso afirmativo, use as [opções no cenário 4: Transferências](storage-solution-periodic-data-transfer.md)de dados periódicas.
 

## <a name="data-transfer-feature-in-azure-portal"></a>Recurso de transferência de dados no portal do Azure

Você também pode acessar sua conta de armazenamento do Azure em portal do Azure e selecionar o recurso de **transferência de dados** . Forneça a largura de banda de rede em seu ambiente, o tamanho dos dados que você deseja transferir e a frequência de transferência de dados. Você verá as soluções de transferência de dados ideais correspondentes às informações fornecidas. 

## <a name="next-steps"></a>Passos Seguintes

- [Obtenha uma introdução ao Gerenciador de armazenamento do Azure](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Leia uma visão geral do AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Usar Azure PowerShell com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Usar CLI do Azure com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Saiba mais sobre:

    - [Azure data Box, disco do Azure data box e Azure data Box Heavy para transferências offline](https://docs.microsoft.com/azure/databox/).
    - [Gateway do Azure data box e Azure data Box Edge para transferências online](https://docs.microsoft.com/azure/databox-online/).
- [Saiba o que é Azure data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Usar as APIs REST para transferir dados

    - [No .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Em Java](https://docs.microsoft.com/java/api/overview/azure/storage)
