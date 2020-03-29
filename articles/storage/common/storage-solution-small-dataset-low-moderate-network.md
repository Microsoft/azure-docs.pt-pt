---
title: Opções de transferência de dados do Azure para pequenos conjuntos de dados com largura de banda de rede baixa a moderada Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando tiver uma largura de banda baixa a moderada no seu ambiente e planeia transferir pequenos conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60397282"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Data transfer for small datasets with low to moderate network bandwidth (Transferência de dados para conjuntos de dados pequenos com pouca largura de banda de rede ou com largura de banda moderada)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando você tem baixa a moderada largura de banda de rede no seu ambiente e você está planejando transferir pequenos conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher uma solução de transferência de [dados Azure.](storage-choose-data-transfer-solution.md)

## <a name="scenario-description"></a>Descrição do cenário

Pequenos conjuntos de dados referem-se aos tamanhos de dados na ordem dos GBs a alguns TBs. A largura de banda de rede baixa a moderada implica 45 Mbps (ligação T3 no datacenter) a 1 Gbps.

- Se estiver a transferir apenas um punhado de ficheiros e não precisar de automatizar a transferência de dados, considere as ferramentas com uma interface gráfica.
- Se estiver confortável com a administração do sistema, considere a linha de comando ou as ferramentas programáticas/scripts.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário são:

- **Ferramentas** de interface gráfica, como o Azure Storage Explorer e o Azure Storage no portal Azure. Estes fornecem uma maneira fácil de visualizar os seus dados e transferir rapidamente alguns ficheiros.

    - **Azure Storage Explorer** - Esta ferramenta de plataforma cruzada permite-lhe gerir o conteúdo das suas contas de armazenamento Azure. Permite-lhe fazer o upload, download e gestão de blobs, ficheiros, filas, mesas e entidades da Azure Cosmos DB. Use-o com armazenamento Blob para gerir bolhas e pastas, bem como carregar e descarregar bolhas entre o seu sistema de ficheiros local e o armazenamento blob, ou entre contas de armazenamento.
    - **Portal Azure** - O Portal Azure no portal Azure fornece uma interface baseada na web para explorar ficheiros e carregar novos ficheiros um de cada vez. Esta é uma boa opção se não quiser instalar ferramentas ou emitir comandos para explorar rapidamente os seus ficheiros, ou simplesmente carregar um punhado de novos.

- **Scripting/programmatic tools** tais como AzCopy/PowerShell/Azure CLI e Azure Storage REST APIs.

    - **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para o armazenamento de Blobs, Ficheiros e Mesa seletivas com um desempenho ótimo. A AzCopy suporta conmoeda e paralelismo, e a capacidade de retomar as operações de cópia quando interrompida.
    - **Azure PowerShell** - Para utilizadores confortáveis com a administração do sistema, utilize o módulo de armazenamento Azure no Azure PowerShell para transferir dados.
    - **Azure CLI** - Utilize esta ferramenta transversal para gerir os serviços do Azure e fazer upload de dados para o Armazenamento Azure.
    - **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra APIs/SDKs de REPOUSO de Armazenamento Azure e utilizar as bibliotecas de clientes Azure oferecidas em vários idiomas.


## <a name="comparison-of-key-capabilities"></a>Comparação de capacidades-chave

A tabela que se segue resume as diferenças nas capacidades-chave.

| Funcionalidade | Explorador do Storage do Azure | Portal do Azure | AzCopy<br>Azure PowerShell<br>CLI do Azure | Apis ou SDKs de armazenamento azure |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Disponibilidade | Transferir e instalar <br>Ferramenta autónoma | Ferramentas de exploração baseadas na Web no portal Azure | Ferramenta de linha de comando |Interfaces programáveis em .NET, Java, Python, JavaScript, C++, Go, Ruby e PHP |
| Interface gráfica | Sim | Sim | Não | Não |
| Plataformas suportadas | Windows, Mac, Linux | Baseado na Web |Windows, Mac, Linux |Todas as plataformas |
| Operações de armazenamento blob permitidas<br>para bolhas e pastas | Carregar<br>Transferência<br>Gerir | Carregar<br>Transferência<br>Gerir |Carregar<br>Transferência<br>Gerir | Sim, personalizável. |
| Armazenamento permitido data Lago Gen1<br>operações para ficheiros e pastas | Carregar<br>Transferência<br>Gerir | Não |Carregar<br>Transferência<br>Gerir                   | Não |
| Operações de armazenamento de ficheiros permitidas<br>para ficheiros e diretórios | Carregar<br>Transferência<br>Gerir | Carregar<br>Transferência<br>Gerir   |Carregar<br>Transferência<br>Gerir | Sim, personalizável. |
| Operações permitidas de armazenamento de mesas<br>para tabelas |Gerir | Não |Suporte de mesa em AzCopy v7 |Sim, personalizável.|
| Armazenamento permitido na fila | Gerir | Não  |Não | Sim, é personalizável.|


## <a name="next-steps"></a>Passos seguintes

- Saiba como [transferir dados com o Azure Storage Explorer.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer)
- [Transferir dados com o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

