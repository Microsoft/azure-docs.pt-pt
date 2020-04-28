---
title: Escolha uma solução Azure para transferência de dados. Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados com base nos tamanhos de dados e largura de banda disponível da rede no seu ambiente
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: df261dbf8af8ffdb4ccb10626594626150245748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176401"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Escolha uma solução Azure para transferência de dados

Este artigo fornece uma visão geral de algumas das soluções comuns de transferência de dados do Azure. O artigo também se liga a opções recomendadas dependendo da largura de banda da rede no seu ambiente e do tamanho dos dados que pretende transferir.

## <a name="types-of-data-movement"></a>Tipos de movimento de dados

A transferência de dados pode estar offline ou sobre a ligação de rede. Escolha a sua solução dependendo da sua:

- **Tamanho** dos dados - Dimensão dos dados destinados à transferência,
- **Frequência de transferência** - Ingestão única ou periódica de dados, e
- **Rede** – Largura de banda disponível para transferência de dados no seu ambiente.

O movimento de dados pode ser dos seguintes tipos:

- **Transferência offline utilizando dispositivos shippable** - Utilize dispositivos físicos de naves móveis quando pretender fazer transferência de dados a granel offline. A Microsoft envia-lhe um disco ou um dispositivo especializado seguro. Em alternativa, pode comprar e enviar os seus próprios discos. Copia dados para o dispositivo e depois envia-os para o Azure onde os dados são carregados.  As opções disponíveis para este caso são Data Box Disk, Data Box, Data Box Heavy e Import/Export (use os seus próprios discos).

- **Transferência de Rede** - Transfere os seus dados para o Azure sobre a sua ligação de rede. Isto pode ser feito de muitas maneiras.

    - **Interface gráfica** - Se ocasionalmente transferir apenas alguns ficheiros e não precisar automatizar a transferência de dados, pode escolher uma ferramenta de interface gráfica, como o Azure Storage Explorer ou uma ferramenta de exploração baseada na Web no portal Azure.
    - **Transferência scriptada ou programática** - Pode utilizar ferramentas de software otimizadas que fornecemos ou chamamos diretamente aos nossos APIs/SDKs REST. As ferramentas scriptable disponíveis são AzCopy, Azure PowerShell e Azure CLI. Para interface programática, utilize um dos SDKs para .NET, Java, Python, Node/JS, C++, Go, PHP ou Ruby.
    - **Dispositivos no local** - Fornecemos-lhe um dispositivo físico ou virtual que reside no seu datacenter e otimiza a transferência de dados pela rede. Estes dispositivos também fornecem uma cache local de ficheiros frequentemente usados. O dispositivo físico é o Data Box Edge e o dispositivo virtual é o Portal da Caixa de Dados. Ambos correm permanentemente nas suas instalações e ligam-se ao Azure sobre a rede.
    - **Gasoduto** de dados gerido - Pode configurar um gasoduto em nuvem para transferir regularmente ficheiros entre vários serviços Azure, no local ou uma combinação de dois. Utilize a Azure Data Factory para configurar e gerir os gasodutos de dados e mover e transformar dados para análise.

O seguinte visual ilustra as diretrizes para escolher as várias ferramentas de transferência de dados Azure, dependendo da largura de banda da rede disponível para transferência, tamanho de dados destinado à transferência e frequência da transferência.

![Ferramentas de transferência de dados azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Os limites superiores dos dispositivos de transferência offline - Data Box Disk, Data Box e Data Box Heavy podem ser prolongados colocando várias encomendas de um tipo de dispositivo.*

## <a name="selecting-a-data-transfer-solution"></a>Selecionar uma solução de transferência de dados

Responda às seguintes questões para ajudar a selecionar uma solução de transferência de dados:

- A largura de banda da rede disponível é limitada ou inexistente e pretende transferir grandes conjuntos de dados?
  
    Se sim, ver: [Cenário 1: Transferir grandes conjuntos](storage-solution-large-dataset-low-network.md)de dados sem largura de banda de rede ou baixa .
- Deseja transferir grandes conjuntos de dados por rede e tem uma largura de banda de rede moderada a alta?

    Se sim, ver: [Cenário 2: Transferir grandes conjuntos](storage-solution-large-dataset-moderate-high-network.md)de dados com largura de banda de rede moderada a alta .
- Deseja transferir ocasionalmente apenas alguns ficheiros pela rede?

    Se sim, consulte [cenário 3: Transfira pequenos conjuntos](storage-solution-small-dataset-low-moderate-network.md)de dados com largura de banda de rede limitada a moderada .
- Está à procura de transferência de dados ponto-a-tempo em intervalos regulares?

    Se sim, utilize as opções script/programáticas descritas no [Cenário 4: Transferências periódicas](storage-solution-periodic-data-transfer.md)de dados .
- Está à procura de transferência contínua de dados em curso?

    Se sim, utilize as opções no [Cenário 4: Transferências periódicas](storage-solution-periodic-data-transfer.md)de dados .

## <a name="data-transfer-feature-in-azure-portal"></a>Funcionalidade de transferência de dados no portal Azure

Também pode ir à sua conta de Armazenamento Azure no portal Azure e selecionar a funcionalidade de transferência de **Dados.** Forneça a largura de banda da rede no seu ambiente, o tamanho dos dados que pretende transferir e a frequência da transferência de dados. Verá as soluções ideais de transferência de dados correspondentes à informação que forneceu. 

## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma introdução ao Azure Storage Explorer.](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/)
- [Leia uma visão geral da AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Quickstart: Upload, download e lista de blobs com PowerShell](../blobs/storage-quickstart-blobs-powershell.md)
- [Quickstart: Criar, descarregar e listar bolhas com o Azure CLI](../blobs/storage-quickstart-blobs-cli.md)
- Saiba mais sobre:

    - [Caixa de dados Azure, Disco de Caixa de Dados Azure e Caixa de Dados Azure Pesada para transferências offline](https://docs.microsoft.com/azure/databox/).
    - Portal de Caixas de Dados Azure e Borda de Caixa de [Dados Azure para transferências online.](https://docs.microsoft.com/azure/databox-online/)
- [Saiba o que é a Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)
- Use as APIs rest para transferir dados

    - [Em .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Em Java](https://docs.microsoft.com/java/api/overview/azure/storage)
