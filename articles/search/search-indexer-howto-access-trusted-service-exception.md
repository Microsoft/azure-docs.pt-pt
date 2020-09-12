---
title: Permitir o acesso ao armazenamento através de uma exceção de serviço fidedigno
titleSuffix: Azure Cognitive Search
description: Como orientar isso descreve como configurar uma exceção de serviço fidedigna para aceder aos dados a partir de contas de armazenamento de forma segura.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: f901833caf0623de643e0372c53658fa7da8c8be
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463721"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Aceder aos dados nas contas de armazenamento de forma segura através de uma exceção de serviço fidedigno

Os indexantes que acedem aos dados nas contas de armazenamento podem utilizar a capacidade de exceção do [serviço fidedigno](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para aceder de forma segura aos dados. Este mecanismo oferece aos clientes que não conseguem conceder acesso a [indexante através de regras de firewall IP](search-indexer-howto-access-ip-restricted.md) uma alternativa simples, segura e gratuita para aceder a dados em contas de armazenamento.

> [!NOTE]
> O acesso à conta de armazenamento através do sistema atribuído à identidade de um serviço de pesquisa ainda se encontra em pré-visualização. Esta funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Passo 1: Configurar a ligação à conta de armazenamento por identidade

Siga os detalhes descritos no [guia de acesso](search-howto-managed-identities-storage.md) à identidade gerido para configurar indexantes para aceder às contas de armazenamento através da identidade gerida do serviço de pesquisa.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Passo 2: Permitir que serviços fidedignos da Microsoft acedam à conta de armazenamento

No portal Azure, navegue para o separador "Firewalls and Virtual Networks" da conta de armazenamento. Certifique-se de que a opção "Permitir que serviços fidedignos da Microsoft acedam a esta conta de armazenamento" é verificada. Esta opção apenas permitirá que a instância específica do serviço de pesquisa com acesso adequado à conta de armazenamento (autenticação forte) aceda aos dados na conta de armazenamento, mesmo que seja protegida pelas regras de firewall IP.

![Exceção de serviço fidedigno](media\search-indexer-howto-secure-access\exception.png "Exceção de serviço fidedigno")

Os indexantes poderão agora aceder aos dados na conta de armazenamento, mesmo que a conta seja protegida através das regras de firewall IP.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os indexantes de armazenamento Azure:

- [Indexador Azure Blob](search-howto-indexing-azure-blob-storage.md)
- [Indexador Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexador de tabela azure](search-howto-indexing-azure-tables.md)
