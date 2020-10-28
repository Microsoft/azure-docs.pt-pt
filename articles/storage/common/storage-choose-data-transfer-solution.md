---
title: Escolha uma solução Azure para transferência de dados Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados com base em tamanhos de dados e largura de banda de rede disponível no seu ambiente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 11ea9c759bdb4bb2b837028407ce6e83f6e25a8c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92784053"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Escolher uma solução do Azure para a transferência de dados

Este artigo fornece uma visão geral de algumas das soluções comuns de transferência de dados Azure. O artigo também se liga às opções recomendadas dependendo da largura de banda da rede no seu ambiente e do tamanho dos dados que pretende transferir.

## <a name="types-of-data-movement"></a>Tipos de movimento de dados

A transferência de dados pode estar offline ou sobre a ligação de rede. Escolha a sua solução dependendo da sua:

- Tamanho dos **dados** - Tamanho dos dados destinados à transferência,
- **Frequência de transferência** - Ingestão de dados pontuais ou periódicos, e
- **Rede** – Largura de banda disponível para transferência de dados no seu ambiente.

O movimento de dados pode ser dos seguintes tipos:

- **Transferência offline utilizando dispositivos de envio** - Utilize dispositivos físicos de envio quando pretender fazer transferência de dados a granel uma única vez. A Microsoft envia-lhe um disco ou um dispositivo especializado seguro. Em alternativa, pode comprar e enviar os seus próprios discos. Copia dados para o dispositivo e envia-os para Azure, onde os dados são enviados.  As opções disponíveis para este caso são Data Box Disk, Data Box, Data Box Heavy e Import/Export (use os seus próprios discos).

- **Transferência de Rede** - Transfira os seus dados para a Azure sobre a sua ligação à rede. Isto pode ser feito de muitas maneiras.

    - **Interface gráfica** - Se ocasionalmente transferir apenas alguns ficheiros e não precisar de automatizar a transferência de dados, pode escolher uma ferramenta de interface gráfica, como o Azure Storage Explorer ou uma ferramenta de exploração baseada na Web no portal Azure.
    - **Transferência escrita ou programática** - Pode utilizar ferramentas de software otimizadas que fornecemos ou chamamos diretamente às nossas APIs/SDKs REST. As ferramentas scriptable disponíveis são AzCopy, Azure PowerShell e Azure CLI. Para interface programática, utilize um dos SDKs para .NET, Java, Python, Node/JS, C++, Go, PHP ou Ruby.
    - **Dispositivos no local** - Fornecemos-lhe um dispositivo físico ou virtual que reside no seu datacenter e otimiza a transferência de dados pela rede. Estes dispositivos também fornecem uma cache local de ficheiros frequentemente usados. O dispositivo físico é o Azure Stack Edge e o dispositivo virtual é o Data Box Gateway. Ambos funcionam permanentemente nas suas instalações e conectam-se ao Azure pela rede.
    - **Pipeline de dados gerido** - Pode configurar um pipeline em nuvem para transferir regularmente ficheiros entre vários serviços Azure, no local ou uma combinação de dois. Utilize a Azure Data Factory para configurar e gerir os oleodutos de dados e mover e transformar dados para análise.

O seguinte visual ilustra as diretrizes para escolher as várias ferramentas de transferência de dados Azure, dependendo da largura de banda de rede disponível para transferência, tamanho dos dados destinados à transferência e frequência da transferência.

![Ferramentas de transferência de dados Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Os limites superiores dos dispositivos de transferência offline - Data Box Disk, Data Box e Data Box Heavy podem ser estendidos através da colocação de várias encomendas de um tipo de dispositivo.*

## <a name="selecting-a-data-transfer-solution"></a>Selecionando uma solução de transferência de dados

Responda às seguintes perguntas para ajudar a selecionar uma solução de transferência de dados:

- A largura de banda da rede disponível é limitada ou inexistente e pretende transferir grandes conjuntos de dados?
  
    Se sim, consulte: [Cenário 1: Transfira grandes conjuntos de dados sem](storage-solution-large-dataset-low-network.md)largura de banda de rede ou baixa .
- Deseja transferir grandes conjuntos de dados por rede e tem uma largura de banda de rede moderada a alta?

    Se sim, consulte: [Cenário 2: Transfira grandes conjuntos de dados com largura de banda de rede moderada a alta](storage-solution-large-dataset-moderate-high-network.md).
- Deseja ocasionalmente transferir apenas alguns ficheiros pela rede?

    Se sim, consulte [o cenário 3: Transfira pequenos conjuntos de dados com largura de banda limitada a moderada .](storage-solution-small-dataset-low-moderate-network.md)
- Está à procura de transferência de dados pontuais em intervalos regulares?

    Se sim, utilize as opções escritas/programáticas descritas no [Cenário 4: Transferências periódicas de dados](storage-solution-periodic-data-transfer.md).
- Está à procura de transferência contínua de dados?

    Se sim, utilize as opções no [Cenário 4: Transferências periódicas de dados](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Recurso de transferência de dados no portal Azure

Também pode ir à sua conta de Armazenamento Azure no portal Azure e selecionar a função **de transferência de Dados.** Forneça a largura de banda da rede no seu ambiente, o tamanho dos dados que pretende transferir e a frequência da transferência de dados. Verá as soluções ideais de transferência de dados correspondentes à informação que forneceu. 

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma introdução ao Azure Storage Explorer.](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/)
- [Leia uma visão geral da AzCopy](./storage-use-azcopy-v10.md).
- [Quickstart: Upload, download e list blobs com PowerShell](../blobs/storage-quickstart-blobs-powershell.md)
- [Quickstart: Criar, transferir e listar bolhas com Azure CLI](../blobs/storage-quickstart-blobs-cli.md)
- Saiba mais sobre:

    - [Caixa de dados Azure, Disco de Caixa de Dados Azure e Caixa de Dados Azure Pesado para transferências offline](../../databox/index.yml).
    - [Azure Data Box Gateway e Azure Stack Edge para transferências on-line](../../databox-online/index.yml).
- [Saiba o que é Azure Data Factory](../../data-factory/copy-activity-overview.md).
- Utilize as APIs REST para transferir dados

    - [Em .NET](/dotnet/api/overview/azure/storage)
    - [Em Java](/java/api/overview/azure/storage)