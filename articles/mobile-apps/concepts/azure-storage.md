---
title: Armazenamento em nuvem para construir aplicações altamente seguras, duráveis e escaláveis com armazenamento Azure
description: Conheça os serviços para armazenar dados de aplicações móveis estruturados e não estruturados na nuvem.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 6f8ed02659f1720435df3ff98817233a5a46dbcb
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450991"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Armazenamento em nuvem para aplicações altamente seguras, duráveis e escaláveis com armazenamento Azure
[O Azure Storage](https://azure.microsoft.com/services/storage/) é a solução de armazenamento em nuvem da Microsoft para aplicações modernas que oferece uma loja de objetos massivamente escalável para objetos de dados, um serviço de sistema de ficheiros para a nuvem, uma loja de mensagens para mensagens confiáveis e uma loja NoSQL. O Armazenamento do Azure é:
- **Durável e altamente disponível:** A redundância garante que os seus dados são seguros em caso de falhas de hardware transitórios. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.
- **Seguro:** Todos os dados escritos no Azure Storage são encriptados pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalável:** Os serviços são projetados para serem massivamente escaláveis para atender às necessidades de armazenamento de dados e desempenho das aplicações de hoje.
- **Gerido:** A Azure lida com manutenção de hardware, atualizações e problemas críticos para si.
- **Acessível:** Os dados estão acessíveis em qualquer parte do mundo em HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes em uma variedade de idiomas, tais como .NET, Java, Node.js, Python, PHP, Ruby e Go, e uma API de REST madura. A scripting é suportada em Azure PowerShell ou no Azure CLI. O portal Azure e o Azure Storage Explorer oferecem soluções visuais fáceis para trabalhar com os seus dados.

Utilize os seguintes serviços para permitir o armazenamento em nuvem nas suas aplicações móveis.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
[O armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) oferece uma solução de armazenamento de objetos para a nuvem. O armazenamento de blob é otimizado para armazenar quantidades massivas de dados não estruturados que não aderem a um determinado modelo ou definição de dados, como texto ou binário. Suporta a variedade de idiomas que as bibliotecas de clientes usam. O armazenamento de blobs foi concebido para:
- Sirva imagens ou documentos diretamente para um navegador.
- Guarde ficheiros para acesso distribuído.
- Transmita vídeo e áudio.
- Escreva para registar ficheiros.
- Armazenar dados para cópias de segurança e restauro, recuperação de desastres e arquivamento.
- Armazenar dados para análise por um serviço no local ou a azure hospedado.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação do Armazenamento de Blobs do Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Guias de Início Rápido](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure
[O armazenamento de mesa azure](https://azure.microsoft.com/services/storage/tables/) é um serviço que armazena dados NoSQL estruturados na nuvem e fornece uma loja de chaves ou atributos com um design sem esquemas. O armazenamento de Tabelas do Azure armazena grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL, que aceita chamadas autenticadas de dentro e fora da nuvem Azure. As tabelas azul são ideais para armazenar dados estruturados e não-relacionais. O armazenamento de mesa é normalmente usado para:
- Armazenar terabytes de dados estruturados capazes de servir aplicações à escala web.
- Armazenar conjuntos de dados que não requerem junções complexas, chaves estrangeiras ou procedimentos armazenados e pode ser desnormalizado para acesso rápido.
- Consulta rápida dados utilizando um índice agrupado.
- Aceder aos dados utilizando o protocolo OData e consultas LINQ com bibliotecas da Fundação de Comunicação do Windows (WCF).

Você pode usar o armazenamento de mesa para armazenar e consultar enormes conjuntos de dados estruturados e não culturais. As suas tabelas escalam à medida que a procura aumenta.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação de armazenamento da mesa Azure](/azure/storage/tables/table-storage-overview)
- [Amostras](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Guias de Início Rápido](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Ficheiros do Azure
Com [os Ficheiros Azure,](https://azure.microsoft.com/services/storage/files/)pode configurar ações de ficheiros de rede altamente disponíveis que podem ser acedidas utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). Vários VMs podem partilhar os mesmos ficheiros com acesso lido e escrito. Também pode ler os ficheiros utilizando a interface REST ou as bibliotecas do cliente de armazenamento. Pode aceder aos ficheiros a partir de qualquer parte do mundo, utilizando um URL que aponta para o ficheiro e inclui um token de Assinatura de Acesso Partilhado (SAS). Pode gerar fichas SAS. Permitem o acesso específico a um ativo privado por um período específico de tempo.

As partilhas de ficheiros do Azure podem ser utilizadas para:
- **Substitua ou complemente os servidores de ficheiros no local:** Sistemas operativos populares como Windows, macOS e Linux podem montar diretamente ações de ficheiros Azure onde quer que estejam no mundo. As partilhas de ficheiros do Azure também podem ser replicadas com o Azure File Sync em Servidores Windows, no local ou na cloud, para desempenho e colocação em cache distribuída dos dados no local onde estão a ser utilizados.
- **Aplicações de elevação e mudança:** Migrar aplicações para a nuvem que esperam uma partilha de ficheiros para armazenar aplicações de ficheiros ou dados do utilizador.
- **Simplificar o desenvolvimento da nuvem:** Os Ficheiros Azure também podem ser usados de várias formas para simplificar novos projetos de desenvolvimento em nuvem. Por exemplo:
    - **Definições de aplicações partilhadas:** Um padrão comum para aplicações distribuídas é ter ficheiros de configuração num local centralizado onde podem ser acedidos a partir de muitas instâncias de aplicação. As instâncias de aplicação podem carregar a sua configuração através da API de Rest do Ficheiro. Os utilizadores podem aceder-lhes conforme necessário, montando a partilha SMB localmente.
    - **Partilha de diagnóstico:** Uma partilha de ficheiros Azure é um local conveniente para aplicações em nuvem para escrever seus registos, métricas e despejos de colisão. Os registos podem ser escritos pelas instâncias de aplicação através da API de Rest do Ficheiro. Os desenvolvedores podem aceder-lhes através da montagem da partilha de ficheiros na sua máquina local. Esta capacidade permite uma grande flexibilidade. Os desenvolvedores podem abraçar o desenvolvimento da nuvem sem terem de abandonar as ferramentas existentes que conhecem.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação dos Ficheiros do Azure](/azure/storage/files/storage-files-introduction)
- [Guias de Início Rápido](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure
[O armazenamento da fila Azure](https://azure.microsoft.com/services/storage/queues/) é um serviço para armazenar um grande número de mensagens. Acede a mensagens de qualquer parte do mundo através de chamadas autenticadas através de HTTP ou HTTPS. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação do armazenamento de Filas do Azure](/azure/storage/queues/)
- [Guias de Início Rápido](/azure/storage/queues/storage-quickstart-queues-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
