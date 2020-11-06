---
title: Azure App Configuration REST API - Autorização de Diretório Ativo Azure
description: Utilize o Azure Ative Directory para autorização contra a Configuração de Aplicações Azure utilizando a API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424186"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Autorização do Diretório Ativo Azure - referência REST API

Quando a autenticação do Azure Ative Directory (Azure AD), a autorização é gerida pelo Azure Role Based Access Control (RBAC). O Azure RBAC exige que os utilizadores sejam designados para funções, a fim de garantir o acesso aos recursos. Cada função contém um conjunto de ações que os utilizadores atribuídos ao papel poderão desempenhar.

## <a name="roles"></a>Funções

As seguintes funções são funções incorporadas que estão disponíveis em subscrições Azure por padrão:

- **Proprietário de dados de configuração de aplicativos Azure** : Esta função proporciona acesso total a todas as operações.
- **Azure App Configuration Data Reader** : Esta função permite ler operações.

## <a name="actions"></a>Ações

As funções contêm uma lista de ações que os utilizadores designados para esse papel podem desempenhar. A Azure App Configuration suporta as seguintes ações:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Esta ação permite o acesso à leitura de recursos de valor-chave de configuração de aplicações, tais como /kv e /labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Esta ação permite escrever acesso a recursos de valor-chave de Configuração de Aplicação.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Esta ação permite eliminar os recursos de valor-chave da Configuração da Aplicação. Note que a eliminação de um recurso devolve o valor-chave que foi eliminado.

## <a name="errors"></a>Erros

```http
HTTP/1.1 403 Forbidden
```

**Razão:** O principal a fazer o pedido não tem as permissões necessárias para a realização da operação solicitada.
**Solução:** Atribuir a função necessária para a realização da operação solicitada ao diretor que efetua o pedido.

## <a name="managing-role-assignments"></a>Gestão de atribuições de funções

A gestão de atribuições de funções é feita usando procedimentos [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) que são padrão em todos os serviços Azure. É possível fazê-lo através do Azure CLI, PowerShell, o portal Azure, e muito mais. Documentação oficial sobre como fazer tarefas de função pode ser encontrada [aqui.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
