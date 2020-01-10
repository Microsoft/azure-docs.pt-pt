---
title: Armazenamento em nuvem para criar aplicativos altamente seguros, duráveis e escalonáveis com o armazenamento do Azure
description: Saiba mais sobre os serviços para armazenar grandes dados estruturados e não estruturados de aplicativos móveis na nuvem.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 53754976a6e1f6d893cf7d6ab73d57c95dae193f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453255"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Armazenamento em nuvem para aplicativos altamente seguros, duráveis e escalonáveis com o armazenamento do Azure
O [armazenamento do Azure](https://azure.microsoft.com/services/storage/) é a solução de armazenamento em nuvem da Microsoft para aplicativos modernos que oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, um repositório de mensagens para mensagens confiáveis e um repositório NoSQL. O Armazenamento do Azure é:
- **Durável e altamente disponível:** A redundância garante que seus dados estejam seguros no caso de falhas transitórias de hardware. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.
- **Seguro:** Todos os dados gravados no armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalonável:** Os serviços são projetados para serem altamente escalonáveis para atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.
- **Gerenciados:** O Azure lida com a manutenção de hardware, atualizações e problemas críticos para você.
- **Acessível:** Os dados podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. A Microsoft fornece bibliotecas de cliente em uma variedade de linguagens, como .NET, Java, Node. js, Python, PHP, Ruby e go, e uma API REST madura. Há suporte para scripts no Azure PowerShell ou no CLI do Azure. O portal do Azure e o Gerenciador de Armazenamento do Azure oferecem soluções visuais fáceis para trabalhar com seus dados.

Use os seguintes serviços para habilitar o armazenamento em nuvem em seus aplicativos móveis.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/) oferece uma solução de armazenamento de objeto para a nuvem. O armazenamento de BLOBs é otimizado para armazenar grandes quantidades de dados não estruturados que não aderem a um modelo ou definição de dados específico, como texto ou binário. Ele dá suporte à variedade de idiomas que as bibliotecas de cliente usam. O armazenamento de BLOBs é projetado para:
- Fornecer imagens ou documentos diretamente a um navegador.
- Armazene arquivos para acesso distribuído.
- Transmita vídeo e áudio.
- Gravar em arquivos de log.
- Armazene dados para backup e restauração, recuperação de desastres e arquivamento.
- Armazene dados para análise por um serviço local ou hospedado no Azure.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação do Armazenamento de Blobs do Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Inícios rápidos](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure
O [armazenamento de tabelas do Azure](https://azure.microsoft.com/services/storage/tables/) é um serviço que armazena dados NoSQL estruturados na nuvem e fornece uma chave ou um repositório de atributos com um design sem esquema. O armazenamento de Tabelas do Azure armazena grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL, que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados e não relacionais. O armazenamento de tabela normalmente é usado para:
- Armazene terabytes de dados estruturados capazes de servir aplicativos de escala da Web.
- Armazene conjuntos de os que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e pode ser desnormalizado para acesso rápido.
- Consultar dados rapidamente usando um índice clusterizado.
- Acesse dados usando o protocolo OData e consultas LINQ com as bibliotecas do .NET dos serviços de dados Windows Communication Foundation (WCF).

Você pode usar o armazenamento de tabelas para armazenar e consultar grandes conjuntos de dados estruturados e não relacionais. As tabelas são dimensionadas conforme a demanda aumenta.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação do armazenamento de tabelas do Azure](/azure/storage/tables/table-storage-overview)
- [Amostras](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Inícios rápidos](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Ficheiros do Azure
Com os [arquivos do Azure](https://azure.microsoft.com/services/storage/files/), você pode configurar compartilhamentos de arquivos de rede altamente disponíveis que podem ser acessados usando o protocolo SMB (Server Message Block) padrão. Várias VMs podem compartilhar os mesmos arquivos com acesso de leitura e gravação. Você também pode ler os arquivos usando a interface REST ou as bibliotecas de cliente de armazenamento. Você pode acessar os arquivos de qualquer lugar do mundo usando uma URL que aponta para o arquivo e inclui um token de SAS (assinatura de acesso compartilhado). Você pode gerar tokens SAS. Eles permitem acesso específico a um ativo privado por um período específico.

As partilhas de ficheiros do Azure podem ser utilizadas para:
- **Substituir ou complementar servidores de arquivos locais:** Sistemas operacionais populares, como Windows, macOS e Linux, podem montar diretamente compartilhamentos de arquivos do Azure onde quer que estejam no mundo. As partilhas de ficheiros do Azure também podem ser replicadas com o Azure File Sync em Servidores Windows, no local ou na cloud, para desempenho e colocação em cache distribuída dos dados no local onde estão a ser utilizados.
- **Aplicativos de comparação de precisão e deslocamento:** Migre aplicativos para a nuvem que esperam que um compartilhamento de arquivos armazene dados do usuário ou do aplicativo de arquivo.
- **Simplifique o desenvolvimento em nuvem:** Os arquivos do Azure também podem ser usados de várias maneiras para simplificar novos projetos de desenvolvimento em nuvem. Por exemplo:
    - **Configurações de aplicativo compartilhado:** Um padrão comum para aplicativos distribuídos é ter arquivos de configuração em um local centralizado, onde eles podem ser acessados de várias instâncias do aplicativo. As instâncias do aplicativo podem carregar sua configuração por meio da API REST do arquivo. Os usuários podem acessá-los conforme necessário, montando o compartilhamento SMB localmente.
    - **Compartilhamento de diagnóstico:** Um compartilhamento de arquivos do Azure é um local conveniente para aplicativos de nuvem gravarem seus logs, métricas e despejos de memória. Os logs podem ser gravados pelas instâncias do aplicativo por meio da API REST do arquivo. Os desenvolvedores podem acessá-los montando o compartilhamento de arquivos em seu computador local. Essa funcionalidade permite uma grande flexibilidade. Os desenvolvedores podem adotar o desenvolvimento em nuvem sem precisar abandonar as ferramentas existentes que eles conhecem.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação dos arquivos do Azure](/azure/storage/files/storage-files-introduction)
- [Inícios rápidos](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure
O [armazenamento de filas do Azure](https://azure.microsoft.com/services/storage/queues/) é um serviço para armazenar grandes números de mensagens. Você acessa mensagens de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação do armazenamento de filas do Azure](/azure/storage/queues/)
- [Inícios rápidos](/azure/storage/queues/storage-quickstart-queues-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
