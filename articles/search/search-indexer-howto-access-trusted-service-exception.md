---
title: Indexer acesso ao Azure Storage usando exceção de serviço fidedigno
titleSuffix: Azure Cognitive Search
description: Permitir o acesso de dados por um indexante na Pesquisa Cognitiva Azure aos dados armazenados de forma segura no Azure Storage.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101380"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Indexante acesso ao Azure Storage usando a exceção de serviço fidedigno (Azure Cognitive Search)

Indexadores num serviço de Pesquisa Cognitiva Azure que acedem a dados em contas de Armazenamento Azure podem utilizar a capacidade de exceção do [serviço fidedigno](../storage/common/storage-network-security.md#exceptions) para aceder de forma segura aos dados. Este mecanismo oferece aos clientes que não conseguem conceder acesso ao [indexante utilizando regras de firewall IP](search-indexer-howto-access-ip-restricted.md) uma alternativa simples, segura e gratuita para aceder a dados em contas de armazenamento.

> [!NOTE]
> O suporte para aceder a dados em contas de armazenamento através de uma exceção de serviço fidedigno está limitado ao armazenamento de Azure Blob e ao armazenamento do Azure Data Lake Gen2. O armazenamento da mesa Azure não é suportado.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Passo 1: Configurar uma ligação utilizando uma identidade gerida

Siga as instruções em [Configurar uma ligação a uma conta de Armazenamento Azure utilizando uma identidade gerida](search-howto-managed-identities-storage.md). Quando terminar, terá registado o seu serviço de pesquisa com o Azure Ative Directory como um serviço de confiança, e terá concedido permissões no Azure Storage que conferem à identidade de pesquisa direitos específicos de acesso a dados ou informações.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Passo 2: Permitir que serviços fidedignos da Microsoft acedam à conta de armazenamento

No portal Azure, navegue para o separador **Firewalls e Redes Virtuais** da conta de armazenamento. Certifique-se de que a opção **Permite que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento.** Esta opção apenas permitirá que a instância específica do serviço de pesquisa com acesso adequado à conta de armazenamento (autenticação forte) aceda aos dados na conta de armazenamento, mesmo que seja protegida pelas regras de firewall IP.

![Exceção de serviço fidedigno](media\search-indexer-howto-secure-access\exception.png "Exceção de serviço fidedigno")

Os indexantes poderão agora aceder aos dados na conta de armazenamento, mesmo que a conta seja protegida através das regras de firewall IP.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os indexantes de armazenamento Azure:

- [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Indexador Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexador de tabela azure](search-howto-indexing-azure-tables.md)