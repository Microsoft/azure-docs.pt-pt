---
title: Opções para conjuntos de dados pequeno com pouco espaço de transferência de dados do Azure para a largura de banda de rede moderada | Documentos da Microsoft
description: Saiba como escolher uma solução do Azure para a transferência de dados, se tiver baixa largura de banda de rede moderada no seu ambiente e estiver a planear transferir pequenos conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124308"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transferência de dados para conjuntos de dados pequeno com baixa largura de banda de rede moderada
 
Este artigo fornece uma descrição geral da transferência de dados soluções quando tiver baixa largura de banda de rede moderada no seu ambiente e estiver a planear transferir pequenos conjuntos de dados. O artigo também descreve as opções de transferência de dados recomendada e a matriz de respetivas capacidades-chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, aceda a [escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrição do cenário

Pequenos conjuntos de dados referem-se a tamanhos de dados por ordem de GBs para alguns TB. Baixo a moderado de largura de banda implica 45 Mbps (T3 ligação no Centro de dados) a 1 Gbps.

- Se estiver a transferir apenas alguns dos ficheiros e não precisa de automatizar a transferência de dados, considere as ferramentas com uma interface gráfica.
- Se estiver familiarizado com a administração do sistema, considere a linha de comandos ou ferramentas de programação/scripts.

## <a name="recommended-options"></a>Opções recomendadas

As opções recomendadas neste cenário são:

- **Ferramentas de interface gráfica** como o Explorador de armazenamento do Azure e o armazenamento do Azure no portal do Azure. Eles fornecem uma forma fácil de ver os seus dados e transferir rapidamente alguns arquivos.

    - **Explorador de armazenamento do Azure** -essa ferramenta para várias plataformas permite-lhe gerir o conteúdo das suas contas de armazenamento do Azure. Permite-lhe carregar, transferir e gerir blobs, ficheiros, filas, tabelas e entidades do Azure Cosmos DB. Utilize-o com o armazenamento de BLOBs para gerir blobs e pastas, bem como carregar e transferir blobs entre o sistema de arquivos local e o armazenamento de BLOBs, ou entre contas de armazenamento.
    - **Portal do Azure** - armazenamento do Azure no portal do Azure fornece uma interface baseada na web para explorar os arquivos e carregar novos ficheiros um de cada vez. Esta é uma boa opção se não pretender instalar quaisquer ferramentas ou emitir comandos de explorar rapidamente os ficheiros ou para carregar apenas um punhado de novos itens.

- **Ferramentas de criação de scripts/programática** como AzCopy/PowerShell/Azure CLI e APIs de REST de armazenamento do Azure.

    - **AzCopy** - Utilize esta ferramenta da linha de comandos para copiar facilmente os dados de e para os Blobs do Azure, ficheiros e o armazenamento com um desempenho ideal de tabelas. AzCopy oferece suporte a simultaneidade e paralelismo e a capacidade para retomar as operações de cópia quando interrompido.
    - **O Azure PowerShell** – para os utilizadores à vontade com administração de sistemas, utilizar o módulo de armazenamento do Azure no Azure PowerShell para transferir dados.
    - **CLI do Azure** -Utilize esta ferramenta de várias plataformas para gerir serviços do Azure e carregar dados para o armazenamento do Azure.
    - **Azure Storage REST APIs/SDKs** – ao criar uma aplicação, pode desenvolver a aplicação contra REST APIs/SDKs de armazenamento Azure e utilizar as bibliotecas de cliente do Azure disponibilizadas em vários idiomas.


## <a name="comparison-of-key-capabilities"></a>Comparação dos principais recursos

A tabela seguinte resume as diferenças nas capacidades principais.

| Funcionalidade | Explorador do Storage do Azure | Portal do Azure | AzCopy<br>Azure PowerShell<br>CLI do Azure | REST APIs do armazenamento do Azure ou SDKs |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Disponibilidade | Transferir e instalar <br>Ferramenta autónoma | Ferramentas de exploração baseada na Web no portal do Azure | Ferramenta de linha de comandos |Interfaces programáveis em .NET, Java, Python, JavaScript, C++, Go, Ruby e PHP |
| Interface gráfica | Sim | Sim | Não | Não |
| Plataformas suportadas | Windows, Mac, Linux | Baseado na Web |Windows, Mac, Linux |Todas as plataformas |
| Permitidas operações de armazenamento de BLOBs<br>para blobs e pastas | Carregar<br>Transferência<br>Gerir | Carregar<br>Transferência<br>Gerir |Carregar<br>Transferência<br>Gerir | Sim, personalizáveis |
| Armazenamento geração 1 do Data Lake<br>operações de ficheiros e pastas | Carregar<br>Transferência<br>Gerir | Não |Carregar<br>Transferência<br>Gerir                   | Não |
| Permitidas operações de armazenamento de ficheiros<br>para ficheiros e diretórios | Carregar<br>Transferência<br>Gerir | Carregar<br>Transferência<br>Gerir   |Carregar<br>Transferência<br>Gerir | Sim, personalizáveis |
| Permitidas operações de armazenamento de tabela<br>para tabelas |Gerir | Não |Suporte de tabela no v7 do AzCopy |Sim, personalizáveis|
| Armazenamento de filas permitido | Gerir | Não  |Não | Sim, é personalizável|


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [transferir dados com o Explorador de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Transferir dados com o AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

