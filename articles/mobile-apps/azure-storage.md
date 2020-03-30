---
title: Armazenamento em nuvem para construir aplicações altamente seguras, duráveis e escaláveis com armazenamento Azure
description: Conheça os serviços para armazenar dados de aplicações móveis estruturados e não estruturados na nuvem.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240980"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Armazenamento em nuvem para aplicações altamente seguras, duráveis e escaláveis com Armazenamento Azure
[O Azure Storage](https://azure.microsoft.com/services/storage/) é a solução de armazenamento em nuvem da Microsoft para aplicações modernas que oferece uma loja de objetos massivamente escalável para objetos de dados, um serviço de sistema de ficheiros para a nuvem, uma loja de mensagens para mensagens fiáveis e uma loja NoSQL. O Armazenamento do Azure é:
- **Durável e altamente disponível:** O Despedimento garante que os seus dados são seguros em caso de falhas de hardware transitórias. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.
- **Seguro:** Todos os dados escritos ao Azure Storage são encriptados pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalável:** Os serviços destinam-se a ser massivamente escaláveis para satisfazer as necessidades de armazenamento e desempenho das aplicações atuais.
- **Gerido:** O Azure lida com a manutenção de hardware, atualizações e questões críticas para si.
- **Acessível:** Os dados são acessíveis a partir de qualquer parte do mundo em HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes em várias línguas, tais como .NET, Java, Node.js, Python, PHP, Ruby e Go, e uma API REST madura. A escrita é suportada no Azure PowerShell ou no Azure CLI. O portal Azure e o Azure Storage Explorer oferecem soluções visuais fáceis para trabalhar com os seus dados.

Utilize os seguintes serviços para permitir o armazenamento em nuvem nas suas aplicações móveis.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
[O armazenamento Azure Blob](https://azure.microsoft.com/services/storage/blobs/) oferece uma solução de armazenamento de objetos para a nuvem. O armazenamento blob é otimizado para armazenar quantidades massivas de dados não estruturados que não aderem a um determinado modelo ou definição de dados, como texto ou binário. Suporta a variedade de idiomas que as bibliotecas de clientes usam. O armazenamento blob foi concebido para:
- Sirva imagens ou documentos diretamente para um navegador.
- Guarde os ficheiros para acesso distribuído.
- Transmita vídeo e áudio.
- Escreva para registar ficheiros.
- Armazenar dados para backup e restauro, recuperação de desastres e arquivamento.
- Armazenar dados para análise por um serviço no local ou azure hospedado.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação do Armazenamento de Blobs do Azure](/azure/storage/blobs/storage-blobs-introduction)
- [Arranques rápidos](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure
[O armazenamento da Mesa Azure](https://azure.microsoft.com/services/storage/tables/) é um serviço que armazena dados noSQL estruturados na nuvem e fornece uma loja chave ou atributo com um design sem esquemas. O armazenamento de Tabelas do Azure armazena grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL, que aceita chamadas autenticadas de dentro e de fora da nuvem Azure. As tabelas azure são ideais para armazenar dados estruturados e não-relacionais. O armazenamento de mesa é normalmente utilizado para:
- Armazenar terabytes de dados estruturados capazes de servir aplicações à escala web.
- Armazenar conjuntos de dados que não requerem juntas complexas, chaves estrangeiras ou procedimentos armazenados e podem ser desnormalizados para acesso rápido.
- Consulta rapidamente os dados utilizando um índice agrupado.
- Aceda aos dados utilizando o protocolo OData e as consultas LINQ com os Serviços de Dados da Fundação de Comunicação do Windows (WCF) nas bibliotecas .NET.

Pode utilizar o armazenamento de mesa para armazenar e consultar enormes conjuntos de dados estruturados e não-relacionais. As suas tabelas escalam à medida que a procura aumenta.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação do Armazenamento de tabelas do Azure](/azure/storage/tables/table-storage-overview)
- [Amostras](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Arranques rápidos](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Ficheiros do Azure
Com [ficheiros Azure,](https://azure.microsoft.com/services/storage/files/)pode configurar ações de ficheiros de rede altamente disponíveis que podem ser acedidas utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). Vários VMs podem partilhar os mesmos ficheiros com acesso a leitura e escrita. Também pode ler os ficheiros utilizando a interface REST ou as bibliotecas de clientes de armazenamento. Pode aceder aos ficheiros de qualquer parte do mundo utilizando um URL que aponta para o ficheiro e inclui um símbolo de Assinatura de Acesso Partilhado (SAS). Pode gerar fichas SAS. Permitem o acesso específico a um ativo privado durante um período de tempo específico.

As partilhas de ficheiros do Azure podem ser utilizadas para:
- **Substitua ou complemente os servidores de ficheiros no local:** Sistemas operativos populares como Windows, macOS e Linux podem montar diretamente ações de ficheiros Azure onde quer que estejam no mundo. As partilhas de ficheiros do Azure também podem ser replicadas com o Azure File Sync em Servidores Windows, no local ou na cloud, para desempenho e colocação em cache distribuída dos dados no local onde estão a ser utilizados.
- **Aplicações de elevação e turno:** Migrar aplicações para a nuvem que esperam uma partilha de ficheiros para armazenar a aplicação de ficheiros ou os dados dos utilizadores.
- Simplificar o **desenvolvimento da nuvem:** Os Ficheiros Azure também podem ser usados de inúmeras formas para simplificar novos projetos de desenvolvimento de nuvem. Por exemplo:
    - **Definições de aplicação partilhadas:** Um padrão comum para aplicações distribuídas é ter ficheiros de configuração num local centralizado onde podem ser acedidos a partir de muitas instâncias de aplicação. As instâncias de aplicação podem carregar a sua configuração através da API DO REST de Ficheiros. Os utilizadores podem aceder-lhes conforme necessário, montando a quota SMB localmente.
    - **Parte de diagnóstico:** Uma partilha de ficheiros Azure é um local conveniente para aplicações em nuvem para escrever os seus registos, métricas e dumps de colisão. Os registos podem ser escritos pelas instâncias de aplicação através da API DO ARQUIVO REST. Os desenvolvedores podem acessá-los montando a parte do ficheiro na sua máquina local. Esta capacidade permite uma grande flexibilidade. Os desenvolvedores podem abraçar o desenvolvimento da nuvem sem ter que abandonar a ferramenta existente que eles conhecem.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação dos Ficheiros do Azure](/azure/storage/files/storage-files-introduction)
- [Arranques rápidos](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Armazenamento de Filas do Azure
O armazenamento da [Fila Azure](https://azure.microsoft.com/services/storage/queues/) é um serviço para armazenar um grande número de mensagens. Acede a mensagens de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assincronicamente.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação do armazenamento de Filas do Azure](/azure/storage/queues/)
- [Arranques rápidos](/azure/storage/queues/storage-quickstart-queues-portal)
- [Amostras](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
