---
title: Visão geral do armazenamento da mesa Azure
description: Saiba como utilizar o armazenamento do Azure Table para armazenar conjuntos de dados flexíveis, como dados do utilizador para aplicações web, livros de endereços, informações de dispositivos ou outros tipos de metadados.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 7727dd2ae69fab75219c5d6d733db3f150061df3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477237"
---
# <a name="azure-table-storage-overview"></a>Descrição geral do armazenamento de tabelas do Azure

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

O Armazenamento de tabelas do Azure é um serviço que armazena dados NoSQL estruturados na cloud, fornecendo uma loja de atributos/chaves com um design sem esquemas. Uma vez que o Table Storage não tem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso a dados do Armazenamento de tabelas é rápido e rentável para muitos tipos de aplicações, e é normalmente mais económico do que o SQL tradicional para semelhantes volumes de dados.

Pode utilizar o Armazenamento de Tabelas para armazenar conjuntos de dados flexíveis, como os dados do utilizador para aplicações Web, livros de endereços, informações do dispositivo, ou outros tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Passos seguintes

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

* [Introdução ao armazenamento de API de Tabela do Azure Cosmos DB e Tabelas do Azure com o SDK .NET](./tutorial-develop-table-dotnet.md)

* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:

    * [Referência da Biblioteca de Clientes do Storage para o .NET](/dotnet/api/overview/azure/storage)

    * [Referência da API REST](/rest/api/storageservices/)