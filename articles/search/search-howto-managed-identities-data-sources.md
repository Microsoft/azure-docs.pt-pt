---
title: Criar uma ligação a uma fonte de dados usando uma identidade gerida
titleSuffix: Azure Cognitive Search
description: Saiba como configurar uma ligação indexante a uma fonte de dados utilizando uma identidade gerida
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 95f1c4bf9b599da8285ac69e299549e5aa73c2f9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519593"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Configurar uma ligação indexante a uma fonte de dados utilizando uma identidade gerida

> [!IMPORTANT] 
> A criação de uma ligação a uma fonte de dados utilizando uma identidade gerida não é suportada com o nível de Pesquisa Cognitiva Free Azure.

Um [indexante](search-indexer-overview.md) em Azure Cognitive Search é um crawler que fornece uma maneira de extrair dados da sua fonte de dados para a Pesquisa Cognitiva Azure. Um indexante obtém uma ligação de fonte de dados a partir do objeto de origem de dados que cria. O objeto de origem de dados geralmente inclui credenciais para a fonte de dados-alvo. Por exemplo, o objeto de origem de dados pode incluir uma chave de conta de armazenamento Azure se quiser indexar dados a partir de um recipiente de armazenamento de bolhas.

Em muitos casos, fornecer credenciais diretamente no objeto de origem de dados não é um problema, mas há alguns desafios que podem surgir:
* Como mantenho as credenciais seguras no meu código que cria o objeto de origem de dados?
* Se a chave da minha conta ou palavra-passe estiver comprometida e precisar de alterá-la, agora preciso de atualizar os meus objetos de origem de dados com a nova chave de conta ou senha para que o meu indexante possa ligar-se novamente à fonte de dados.

Estas preocupações podem ser resolvidas através da configuração da sua ligação utilizando uma identidade gerida.

## <a name="using-managed-identities"></a>Utilização de identidades geridas

[Identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) é uma funcionalidade que fornece aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode utilizar esta funcionalidade na Azure Cognitive Search para criar um objeto de origem de dados com uma cadeia de ligação que não inclua quaisquer credenciais. Em vez disso, o seu serviço de pesquisa terá acesso à fonte de dados através do controlo de acesso baseado em funções Azure (Azure RBAC).

Ao configurar uma fonte de dados utilizando uma identidade gerida, pode alterar as suas credenciais de origem de dados e os seus indexantes ainda poderão ligar-se à fonte de dados. Também pode criar objetos de origem de dados no seu código sem ter de incluir uma chave de conta ou utilizar o Key Vault para recuperar uma chave de conta.

## <a name="limitations"></a>Limitações

As fontes de dados que se seguem suportam a criação de uma ligação indexante utilizando identidades geridas. 

* [Armazenamento Azure Blob, Azure Data Lake Storage Gen2 (pré-visualização), Armazenamento da Mesa Azure](search-howto-managed-identities-storage.md)
* [BD do Cosmos para o Azure](search-howto-managed-identities-cosmos-db.md)
* [Base de Dados SQL do Azure](search-howto-managed-identities-sql.md)

As seguintes funcionalidades não suportam atualmente a utilização de identidades geridas para configurar a ligação:
* Knowledge Store
* Competências personalizadas
 
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como configurar uma ligação indexante utilizando identidades geridas:

* [Armazenamento Azure Blob, Azure Data Lake Storage Gen2 (pré-visualização), Armazenamento da Mesa Azure](search-howto-managed-identities-storage.md)
* [BD do Cosmos para o Azure](search-howto-managed-identities-cosmos-db.md)
* [Base de Dados SQL do Azure](search-howto-managed-identities-sql.md)