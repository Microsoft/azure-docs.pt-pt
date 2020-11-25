---
title: Opções de transferência de dados Azure para pequenos conjuntos de dados com largura de banda de rede baixa a moderada Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando tiver largura de banda de rede baixa a moderada no seu ambiente e planeia transferir pequenos conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023232"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Data transfer for small datasets with low to moderate network bandwidth (Transferência de dados para conjuntos de dados pequenos com pouca largura de banda de rede ou com largura de banda moderada)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando você tem largura de banda de rede baixa a moderada no seu ambiente e você está planejando transferir pequenos conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher [uma solução de transferência de dados Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Os conjuntos de dados pequenos referem-se a tamanhos de dados que variam entre alguns GBs e alguns TBs. A largura de banda de rede baixa a moderada implica 45 Mbps (ligação T3 no datacenter) a 1 Gbps.

- Se estiver a transferir apenas um punhado de ficheiros e não precisar de automatizar a transferência de dados, considere as ferramentas com uma interface gráfica.
- Se estiver familiarizado com a administração do sistema, considere utilizar ferramentas de linha de comandos ou programáticas/de scripting.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário são:

- **Ferramentas de interface gráficas** como O Explorador de Armazenamento Azure e Azure Storage no portal Azure. Estes fornecem uma maneira fácil de visualizar os seus dados e transferir rapidamente alguns ficheiros.

    - **Azure Storage Explorer** - Esta ferramenta de plataforma cruzada permite-lhe gerir o conteúdo das suas contas de armazenamento Azure. Possibilita o carregamento, a transferência e a gestão de blobs, ficheiros, filas, tabelas e entidades do Azure Cosmos DB. Utilize-a com o Armazenamento de blobs para gerir blobs e pastas, assim como carregar e transferir blobs entre o sistema de ficheiros local e o Armazenamento de blobs ou entre contas de armazenamento.
    - **Portal Azure** - O Azure Storage no portal Azure fornece uma interface baseada na Web para explorar ficheiros e carregar novos ficheiros um de cada vez. É uma boa opção se não quiser instalar ferramentas nem emitir comandos para explorar rapidamente os seus ficheiros ou apenas carregar alguns novos.

- **Ferramentas de script/programática** como AzCopy/PowerShell/Azure CLI e Azure Storage REST APIs.

    - **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para Azure Blobs, Ficheiros e Armazenamento de Mesa com o melhor desempenho. O AzCopy suporta simultaneidade e paralelismo, além da capacidade de retomar operações de cópia interrompidas.
    - **Azure PowerShell** - Para utilizadores confortáveis com a administração do sistema, utilize o módulo de armazenamento Azure em Azure PowerShell para transferir dados.
    - **Azure CLI** - Utilize esta ferramenta de plataforma cruzada para gerir os serviços Azure e fazer upload de dados para o Azure Storage.
    - **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra APIs/SDKs de armazenamento Azure e utilizar as bibliotecas de clientes Azure oferecidas em várias línguas.


## <a name="comparison-of-key-capabilities"></a>Comparação das capacidades-chave

A tabela seguinte resume as diferenças entre as principais capacidades.

| Funcionalidade | Explorador do Storage do Azure | Portal do Azure | AzCopy<br>Azure PowerShell<br>CLI do Azure | Azure Storage REST APIs ou SDKs |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Disponibilidade | Transferir e instalar <br>Ferramenta autónoma | Ferramentas de exploração baseadas na Web no portal Azure | Ferramenta de linha de comando |Interfaces programáveis em .NET, Java, Python, JavaScript, C++, Go, Ruby e PHP |
| Interface gráfica | Sim | Sim | Não | Não |
| Plataformas apoiadas | Windows, Mac, Linux | Baseado na Web |Windows, Mac, Linux |Todas as plataformas |
| Operações de armazenamento permitidas blob<br>para bolhas e pastas | Carregar<br>Download<br>Gerir | Carregar<br>Download<br>Gerir |Carregar<br>Download<br>Gerir | Sim, personalizável |
| Armazenamento permitido do Data Lake Gen1<br>operações para ficheiros e pastas | Carregar<br>Download<br>Gerir | Não |Carregar<br>Download<br>Gerir                   | Não |
| Operações permitidas de armazenamento de ficheiros<br>para ficheiros e diretórios | Carregar<br>Download<br>Gerir | Carregar<br>Download<br>Gerir   |Carregar<br>Download<br>Gerir | Sim, personalizável |
| Operações permitidas de armazenamento de mesa<br>para tabelas |Gerir | Não |Suporte de mesa em AzCopy v7 |Sim, personalizável|
| Armazenamento permitido da fila | Gerir | Não  |Não | Sim, é personalizável|


## <a name="next-steps"></a>Passos seguintes

- Saiba como [transferir dados com o Azure Storage Explorer.](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md)
- [Transferir dados com o AzCopy](./storage-use-azcopy-v10.md)