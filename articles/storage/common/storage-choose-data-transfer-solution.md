---
title: Escolha uma solução do Azure para a transferência de dados | Documentos da Microsoft
description: Saiba como escolher uma solução do Azure para a transferência de dados com base nos tamanhos de dados e largura de banda de rede disponível no seu ambiente
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: 4e2a182493b1e9de3d2ba9d586a9560e42fe0ecb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773440"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Escolha uma solução do Azure para a transferência de dados

Este artigo fornece uma descrição geral de alguns da transferência de dados do Azure comuns soluções. Também o artigo contém ligações horizontalmente para recomendada opções consoante a largura de banda de rede no seu ambiente e o tamanho dos dados que pretende transferir.

## <a name="types-of-data-movement"></a>Tipos de movimento de dados

Transferência de dados pode estar offline ou através da ligação de rede. Escolha a sua solução consoante o:

- **Tamanho dos dados** -tamanho dos dados se destina a transferência,
- **Frequência de transferência** -ingestão de dados de uso individual ou periódico, e
- **Rede** – largura de banda disponível para os dados de transferência no seu ambiente.

O movimento de dados pode ser dos seguintes tipos:

- **Transferência offline usando dispositivos que pudessem ser enviados** -utilizar dispositivos físicos de que pudessem ser enviados quando deseja fazer a transferência de dados em massa única offline. A Microsoft envia um disco ou um dispositivo especializado seguro. Em alternativa, pode comprar e envie seus próprios discos. Copiar dados para o dispositivo e, em seguida, enviá-lo para o Azure em que os dados são carregados.  As opções disponíveis neste caso, são disco Data Box, Data Box, dados de caixa pesadas e importar/exportar (usar seus próprios discos).

- **Transferência de rede** -transfira os seus dados para o Azure através da ligação de rede. Isso pode ser feito de várias maneiras.

    - **Interface gráfica** -se apenas alguns arquivos de transferência, ocasionalmente e não é necessário automatizar a transferência de dados, pode escolher uma ferramenta de interface gráfica como o Explorador de armazenamento do Azure ou uma ferramenta de exploração baseada na web no portal do Azure.
    - **Transferência com script ou programação** -pode utilizar ferramentas de software otimizada, podemos fornecer ou chamar as nossas APIs/SDKs REST diretamente. As ferramentas de programável por scripts disponíveis são AzCopy, o Azure PowerShell e CLI do Azure. Interface programática, utilize um dos SDKs do .NET, Java, Python, nó/JS, C++, Go, PHP ou Ruby.
    - **Dispositivos no local** -nós fornecemos um dispositivo físico ou virtual que reside no seu datacenter e otimiza a transferência de dados através da rede. Esses dispositivos também fornecem um cache local de ficheiros utilizados frequentemente. O dispositivo físico é o limite da caixa de dados e o dispositivo virtual é o Gateway de caixa de dados. Ambos executar permanentemente no local e ligam ao Azure através da rede.
    - **Pipeline de dados geridos** -pode configurar um pipeline de cloud para regularmente transferir ficheiros entre vários serviços do Azure, no local ou uma combinação de dois. Utilize o Azure Data Factory para configurar e gerir pipelines de dados e mover e transformar dados para análise.

O elemento visual seguinte ilustra as diretrizes para escolher a transferência de dados do Azure de várias ferramentas, consoante a largura de banda disponível para transferência, o tamanho de dados se destina a transferência e a frequência da transferência.

![Ferramentas de transferência de dados do Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Os limites superiores a dispositivos de transferência offline - disco Data Box, Data Box e dados de caixa pesadas podem ser estendidos por colocar vários pedidos de um tipo de dispositivo.*

## <a name="selecting-a-data-transfer-solution"></a>Selecionar uma solução de transferência de dados

Responda às seguintes perguntas para ajudar a selecionar uma solução de transferência de dados:

- É a largura de banda de rede disponível limitada ou inexistente e desejar transferir grandes conjuntos de dados?
  
    Se Sim, consulte: [Cenário 1: Transferência de grandes conjuntos de dados sem nenhum ou pouca largura de banda](storage-solution-large-dataset-low-network.md).
- É que pretende transferir grandes conjuntos de dados pela rede e tiver uma moderada para elevada largura de banda?

    Se Sim, consulte: [Cenário 2: Transferir grandes conjuntos de dados com moderada para elevada largura de banda](storage-solution-large-dataset-moderate-high-network.md).
- Pretende transferir ocasionalmente alguns ficheiros através da rede?

    Se Sim, consulte o artigo [cenário 3: Transferir os conjuntos de dados pequeno com conexão limitada para largura de banda de rede Moderada](storage-solution-small-dataset-low-moderate-network.md).
- Está à procura de transferência de dados de ponto no tempo em intervalos regulares?

    Se Sim, utilizar as opções com script/programática descritas no [cenário 4: Transferências de dados periódica](storage-solution-periodic-data-transfer.md).
- Está à procura de transferência de dados em curso, contínua?

    Se Sim, utilizar as opções na [cenário 4: Transferências de dados periódica](storage-solution-periodic-data-transfer.md).

## <a name="next-steps"></a>Passos Seguintes

- [Obtenha uma introdução ao Explorador de armazenamento do Azure](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Leia uma visão geral do AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Utilizar o Azure PowerShell com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Utilizar a CLI do Azure com o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Saiba mais sobre:

    - [O Azure Data Box, o disco do Azure Data Box e Azure dados caixa pesadas para transferências offline](https://docs.microsoft.com/azure/databox/).
    - [Edge de caixa de dados do Azure para as transferências de online e Gateway de caixa de dados do Azure](https://docs.microsoft.com/azure/databox-online/).
- [Saiba o que é o Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Utilizar as APIs REST para transferir dados

    - [No .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
