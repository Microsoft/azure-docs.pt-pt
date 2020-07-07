---
title: Obtenha acesso a token usando Azure CLI - Azure API para FHIR
description: Este artigo explica como obter um token de acesso para Azure API para FHIR usando o Azure CLI.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: 4eb4c10a6c98aa847c9fa6c239aacde891db5aae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871897"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Obtenha acesso a Azure API para FHIR usando Azure CLI

Neste artigo, você aprenderá a obter um token de acesso para a Azure API para FHIR usando o Azure CLI. Ao [providenciar a API Azure para FHIR,](fhir-paas-portal-quickstart.md)configura um conjunto de utilizadores ou diretores de serviço que têm acesso ao serviço. Se o seu ID do objeto de utilizador estiver na lista de IDs de objetos permitidos, pode aceder ao serviço utilizando um símbolo obtido através do Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Iniciar sessão com a CLI do Azure

Antes de obter um token, tem de iniciar sinsutada com o utilizador para obter um símbolo para:

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>Obter um símbolo

A Azure API para FHIR utiliza um `resource` ou `Audience` com URI igual ao URI do servidor FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Pode obter um token e armazená-lo numa variável `$token` (nomeada) com o seguinte comando:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com | jq -r .accessToken)
```

## <a name="use-with-azure-api-for-fhir"></a>Utilizar com Azure API para FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a obter um token de acesso para a Azure API para fHIR usando o Azure CLI. Para aprender a aceder à API FHIR usando o Carteiro, dirija-se ao tutorial do Carteiro.

>[!div class="nextstepaction"]
>[Acesso FHIR API usando Carteiro](access-fhir-postman-tutorial.md)