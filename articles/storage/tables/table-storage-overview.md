---
title: Introdução ao Armazenamento de tabelas – Armazenamento de objetos no Azure | Microsoft Docs
description: Armazene dados estruturados na nuvem através do Table Storage do Azure, um arquivo de dados NoSQL.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027745"
---
# <a name="what-is-azure-table-storage-"></a>O que é o armazenamento da Mesa Azure? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

O armazenamento de mesas Azure é um serviço que armazena dados estruturados não relacionais (também conhecidos como dados NoSQL estruturados) na nuvem, fornecendo uma loja chave/atributo com um design sem esquema. Uma vez que o Table Storage não tem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso a dados do Armazenamento de tabelas é rápido e rentável para muitos tipos de aplicações, e é normalmente mais económico do que o SQL tradicional para semelhantes volumes de dados.

Pode utilizar o Armazenamento de Tabelas para armazenar conjuntos de dados flexíveis, como os dados do utilizador para aplicações Web, livros de endereços, informações do dispositivo, ou outros tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Passos seguintes

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

* [Introdução ao Armazenamento de Tabelas do Azure no .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)

* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:

    * [Referência da Biblioteca de Clientes do Storage para o .NET](/dotnet/api/overview/azure/storage)

    * [Referência da API REST](/rest/api/storageservices/)