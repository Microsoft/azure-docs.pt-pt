---
title: Obtenha informações chave de encriptação
titleSuffix: Azure Cognitive Search
description: Recupere o nome e versão da chave de encriptação utilizados num mapa de índice ou sinónimo para que possa gerir a chave no Cofre da Chave Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932904"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Obtenha informações-chave geridas pelo cliente a partir de índices e mapas de sinónimos

Na Pesquisa Cognitiva Azure, as chaves de encriptação geridas pelo cliente são criadas, armazenadas e geridas no Cofre da Chave Azure. Se precisar de determinar se um objeto está encriptado ou qual o nome ou versão chave que foi utilizado, utilize a API REST ou um SDK para recuperar a propriedade **encriptaçãoKey** a partir de uma definição de mapa de índice ou sinónimo. 

Recomendamos que [ative o registo](../key-vault/general/logging.md) no Key Vault para que possa monitorizar a utilização da chave.

## <a name="get-the-admin-api-key"></a>Obtenha a chave API de administrador

Para obter definições de objetos de um serviço de pesquisa, você precisará autenticar com direitos de administração. A maneira mais fácil de obter a chave API administrador é através do portal.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e abra a página geral do serviço de pesquisa.

1. No lado esquerdo, clique em **Chaves** e copie uma API administradora. É necessária uma chave de administração para a recuperação do mapa de índices e sinónimos.

Para os passos restantes, mude para PowerShell e para a API REST. O portal não mostra mapas de sinónimo, nem as propriedades chave de encriptação dos índices.

## <a name="use-powershell-and-rest"></a>Use PowerShell e REST

Executar os seguintes comandos para configurar as variáveis e obter definições de objetos.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe qual a chave e versão de encriptação que é utilizada, pode gerir a chave no Cofre de Chaves Azure ou verificar outras definições de configuração.

+ [Início Rápido: Definir e obter um segredo do Azure Key Vault com o PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Configure as chaves geridas pelo cliente para encriptação de dados na Pesquisa Cognitiva Azure](search-security-manage-encryption-keys.md)