---
title: Azure App Configuration REST API - Autorização de Diretório Ativo Azure
description: Utilize o Azure Ative Directory para autorização contra a Configuração da Aplicação Azure utilizando a API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 89c51e69a4274affcecb4d967deb96dcebcfd70f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253375"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Autorização do Diretório Ativo Azure - referência REST API

Quando utiliza a autenticação do Azure Ative Directory (Azure AD), a autorização é gerida por controlo de acesso baseado em funções (RBAC). O RBAC exige que os utilizadores sejam designados para funções, a fim de garantir o acesso aos recursos. Cada função contém um conjunto de ações que os utilizadores atribuídos ao papel são capazes de desempenhar.

## <a name="roles"></a>Funções

As seguintes funções estão disponíveis nas subscrições Azure por padrão:

- **Proprietário de dados de configuração de aplicativos Azure**: Esta função proporciona acesso total a todas as operações.
- **Azure App Configuration Data Reader**: Esta função permite ler operações.

## <a name="actions"></a>Ações

As funções contêm uma lista de ações que os utilizadores designados para esse papel podem desempenhar. A Azure App Configuration suporta as seguintes ações:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Esta ação permite o acesso à leitura dos recursos de valor-chave da Configuração de Aplicações, tais como /kv e /labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Esta ação permite escrever acesso a recursos de valor-chave de Configuração de Aplicação.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Esta ação permite eliminar os recursos de valor-chave da Configuração da Aplicação. Note que a eliminação de um recurso devolve o valor-chave que foi eliminado.

## <a name="error"></a>Erro

```http
HTTP/1.1 403 Forbidden
```

**Razão:** O principal a fazer o pedido não tem as permissões necessárias para realizar a operação solicitada.
**Solução:** Atribuir a função necessária para a realização da operação solicitada ao diretor que efetua o pedido.

## <a name="managing-role-assignments"></a>Gestão de atribuições de funções

Você pode gerir atribuições de funções usando [procedimentos RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) que são padrão em todos os serviços Azure. Pode fazê-lo através do Azure CLI, PowerShell e do portal Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de funções Azure utilizando o portal Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
