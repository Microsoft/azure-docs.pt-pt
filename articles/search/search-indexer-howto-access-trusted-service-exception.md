---
title: Permitir o acesso ao armazenamento através de uma exceção de serviço fidedigno
titleSuffix: Azure Cognitive Search
description: Como orientar isso descreve como configurar uma exceção de serviço fidedigna para aceder aos dados a partir de contas de armazenamento de forma segura.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320477"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Aceder aos dados nas contas de armazenamento de forma segura através de uma exceção de serviço fidedigno

Os indexantes que acedem aos dados nas contas de armazenamento podem utilizar a capacidade de exceção do [serviço fidedigno](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para aceder de forma segura aos dados. Este mecanismo oferece aos clientes que não conseguem conceder acesso a [indexante através de regras de firewall IP](search-indexer-howto-access-ip-restricted.md) uma alternativa simples, segura e gratuita para aceder a dados em contas de armazenamento.

> [!NOTE]
> O suporte para aceder a dados em contas de armazenamento através de uma exceção de serviço fidedigno está limitado ao armazenamento de Azure Blob e ao armazenamento do Azure Data Lake Gen2. O armazenamento da mesa Azure não é suportado.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Passo 1: Configurar a ligação à conta de armazenamento por identidade

Siga os detalhes descritos no [guia de acesso](search-howto-managed-identities-storage.md) à identidade gerido para configurar indexantes para aceder às contas de armazenamento através da identidade gerida do serviço de pesquisa.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Passo 2: Permitir que serviços fidedignos da Microsoft acedam à conta de armazenamento

No portal Azure, navegue para o separador "Firewalls and Virtual Networks" da conta de armazenamento. Certifique-se de que a opção "Permitir que serviços fidedignos da Microsoft acedam a esta conta de armazenamento" é verificada. Esta opção apenas permitirá que a instância específica do serviço de pesquisa com acesso adequado à conta de armazenamento (autenticação forte) aceda aos dados na conta de armazenamento, mesmo que seja protegida pelas regras de firewall IP.

![Exceção de serviço fidedigno](media\search-indexer-howto-secure-access\exception.png "Exceção de serviço fidedigno")

Os indexantes poderão agora aceder aos dados na conta de armazenamento, mesmo que a conta seja protegida através das regras de firewall IP.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os indexantes de armazenamento Azure:

- [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Indexador Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexador de tabela azure](search-howto-indexing-azure-tables.md)
