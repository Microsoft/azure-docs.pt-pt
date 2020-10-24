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
ms.openlocfilehash: a95361dda74c145e435a7e122339fb5945fed0f6
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491228"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Data transfer for small datasets with low to moderate network bandwidth (Transferência de dados para conjuntos de dados pequenos com pouca largura de banda de rede ou com largura de banda moderada)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando você tem largura de banda de rede baixa a moderada no seu ambiente e você está planejando transferir pequenos conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher [uma solução de transferência de dados Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Pequenos conjuntos de dados referem-se a tamanhos de dados na ordem dos GBs a algumas TBs. A largura de banda de rede baixa a moderada implica 45 Mbps (ligação T3 no datacenter) a 1 Gbps.

- Se estiver a transferir apenas um punhado de ficheiros e não precisar de automatizar a transferência de dados, considere as ferramentas com uma interface gráfica.
- Se estiver confortável com a administração do sistema, considere a linha de comando ou ferramentas programáticas/scripting.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário são:

- **Ferramentas de interface gráficas** como O Explorador de Armazenamento Azure e Azure Storage no portal Azure. Estes fornecem uma maneira fácil de visualizar os seus dados e transferir rapidamente alguns ficheiros.

    - **Azure Storage Explorer** - Esta ferramenta de plataforma cruzada permite-lhe gerir o conteúdo das suas contas de armazenamento Azure. Permite-lhe fazer upload, download e gerir blobs, ficheiros, filas, mesas e entidades DB do Azure Cosmos. Utilize-o com armazenamento Blob para gerir bolhas e pastas, bem como carregar e descarregar bolhas entre o seu sistema de ficheiros local e o armazenamento Blob, ou entre contas de armazenamento.
    - **Portal Azure** - O Azure Storage no portal Azure fornece uma interface baseada na Web para explorar ficheiros e carregar novos ficheiros um de cada vez. Esta é uma boa opção se não quiser instalar ferramentas ou emitir comandos para explorar rapidamente os seus ficheiros, ou simplesmente carregar um punhado de novos.

- **Ferramentas de script/programática** como AzCopy/PowerShell/Azure CLI e Azure Storage REST APIs.

    - **AzCopy** - Utilize esta ferramenta de linha de comando para copiar facilmente dados de e para Azure Blobs, Ficheiros e Armazenamento de Mesa com o melhor desempenho. A AzCopy suporta a concordância e o paralelismo, e a capacidade de retomar as operações de cópia quando interrompidas.
    - **Azure PowerShell** - Para utilizadores confortáveis com a administração do sistema, utilize o módulo de armazenamento Azure em Azure PowerShell para transferir dados.
    - **Azure CLI** - Utilize esta ferramenta de plataforma cruzada para gerir os serviços Azure e fazer upload de dados para o Azure Storage.
    - **Azure Storage REST APIs/SDKs** – Ao construir uma aplicação, pode desenvolver a aplicação contra APIs/SDKs de armazenamento Azure e utilizar as bibliotecas de clientes Azure oferecidas em várias línguas.


## <a name="comparison-of-key-capabilities"></a>Comparação das capacidades-chave

A tabela seguinte resume as diferenças nas capacidades-chave.

| Funcionalidade | Explorador de Armazenamento do Azure | Portal do Azure | AzCopy<br>Azure PowerShell<br>CLI do Azure | Azure Storage REST APIs ou SDKs |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Disponibilidade | Transferir e instalar <br>Ferramenta autónoma | Ferramentas de exploração baseadas na Web no portal Azure | Ferramenta de linha de comando |Interfaces programáveis em .NET, Java, Python, JavaScript, C++, Go, Ruby e PHP |
| Interface gráfica | Yes | Yes | No | No |
| Plataformas apoiadas | Windows, Mac, Linux | Baseado na Web |Windows, Mac, Linux |Todas as plataformas |
| Operações de armazenamento permitidas blob<br>para bolhas e pastas | Carregar<br>Download<br>Gerir | Carregar<br>Download<br>Gerir |Carregar<br>Download<br>Gerir | Sim, personalizável |
| Armazenamento permitido do Data Lake Gen1<br>operações para ficheiros e pastas | Carregar<br>Download<br>Gerir | No |Carregar<br>Download<br>Gerir                   | No |
| Operações permitidas de armazenamento de ficheiros<br>para ficheiros e diretórios | Carregar<br>Download<br>Gerir | Carregar<br>Download<br>Gerir   |Carregar<br>Download<br>Gerir | Sim, personalizável |
| Operações permitidas de armazenamento de mesa<br>para tabelas |Gerir | No |Suporte de mesa em AzCopy v7 |Sim, personalizável|
| Armazenamento permitido da fila | Gerir | No  |No | Sim, é personalizável|


## <a name="next-steps"></a>Passos seguintes

- Saiba como [transferir dados com o Azure Storage Explorer.](/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer)
- [Transferir dados com o AzCopy](/azure/storage/common/storage-use-azcopy-v10)

