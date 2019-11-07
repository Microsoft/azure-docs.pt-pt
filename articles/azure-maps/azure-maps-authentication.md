---
title: Autenticação com o Azure Maps | Microsoft Docs
description: Autenticação para usar os serviços do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 84af496a92bd3c7b30062e965335782f7661aa4a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575661"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com o Azure Maps

O mapas do Azure dá suporte a duas maneiras de autenticar solicitações: chave compartilhada e Azure Active Directory (AD do Azure). Este artigo explica esses métodos de autenticação para ajudar a guiar sua implementação.

## <a name="shared-key-authentication"></a>Autenticação de chave compartilhada

A autenticação de chave compartilhada passa as chaves geradas por uma conta do Azure Maps com cada solicitação para mapas do Azure.  Duas chaves são geradas quando sua conta do Azure Maps é criada. Para cada solicitação para os serviços do Azure Maps, a chave de assinatura precisa ser adicionada como um parâmetro à URL.

> [!Tip]
> É recomendável regenerar suas chaves regularmente. Você recebe duas chaves para que possa manter conexões com uma chave ao regenerar a outra. Ao regenerar as chaves, você precisa atualizar todos os aplicativos que acessam a conta para usar as novas chaves.

Para obter informações sobre como exibir suas chaves, consulte [Exibir detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com Azure Active Directory (versão prévia)

O Azure Maps agora oferece integração de [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) para a autenticação de solicitações para os serviços do Azure Maps. O Azure AD fornece autenticação baseada em identidade, incluindo [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview), para conceder acesso em nível de usuário, nível de grupo e de aplicativo aos recursos do Azure Maps. As seções a seguir podem ajudá-lo a entender os conceitos e os componentes da integração do Azure Maps com o Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com tokens de acesso OAuth

O Azure Maps aceita tokens de acesso **OAuth 2,0** para locatários do Azure ad associados a uma assinatura do Azure que contém uma conta do Azure Maps. O Azure Maps aceita tokens para:

* Usuários do Azure AD. 
* Aplicativos de parceiros que usam permissões delegadas por usuários.
* Identidades gerenciadas para recursos do Azure.

O mapas do Azure gera um *identificador exclusivo (ID do cliente)* para cada conta do Azure Maps. Ao combinar essa ID de cliente com parâmetros adicionais, você pode solicitar tokens do Azure AD especificando os valores na tabela a seguir, dependendo do seu ambiente do Azure.

| Ambiente do Azure   | Ponto de extremidade de token do Azure AD |
| --------------------|-------------------------|
| Público do Azure        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Para obter mais informações sobre como configurar o Azure AD e solicitar tokens para mapas do Azure, consulte [gerenciar a autenticação no Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Para obter informações gerais sobre como solicitar tokens do AD do Azure, consulte [o que é autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Solicitar recursos do Azure MAP com tokens OAuth

Depois que um token é recebido do Azure AD, uma solicitação pode ser enviada ao Azure Maps com os dois seguintes cabeçalhos de solicitação necessários definidos:

| Cabeçalho do pedido    |    Valor    |
|:------------------|:------------|
| x-MS-Client-ID    | 30d7cc....9f55|
| Autorização     | Portador eyJ0e... HNIVN |

> [!Note]
> `x-ms-client-id` é o GUID baseado na conta do Azure Maps que aparece na página de autenticação do Azure Maps.

Aqui está um exemplo de uma solicitação de rota do Azure Maps que usa um token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Para obter informações sobre como exibir sua ID do cliente, consulte [Exibir detalhes da autenticação](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Controlar o acesso com o RBAC

O Azure AD permite que você controle o acesso a recursos protegidos usando o RBAC. Depois de criar sua conta do Azure Maps e registrar seu aplicativo Azure AD do Azure Maps no seu locatário do Azure AD, você pode configurar o RBAC para um usuário, grupo, aplicativo ou recurso do Azure na página do portal da conta do Azure Maps.

O Azure Maps dá suporte ao controle de acesso de leitura para usuários individuais do Azure AD, grupos, aplicativos e serviços do Azure por meio de identidades gerenciadas para recursos do Azure.

![Leitor de dados do Azure Maps (versão prévia)](./media/azure-maps-authentication/concept.png)

Para obter informações sobre como exibir suas configurações de RBAC, consulte [como configurar o RBAC para mapas do Azure](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades gerenciadas para recursos do Azure e mapas do Azure

[Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecem serviços do azure (Azure app serviço, Azure functions, máquinas virtuais do Azure e assim por diante) com uma identidade gerenciada automaticamente que pode ser autorizada para acesso aos serviços do Azure Maps.  

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como autenticar um aplicativo com o Azure AD e o Azure Maps, consulte [gerenciar a autenticação no Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre como autenticar o Controle de Mapeamento do Azure Maps e o Azure AD, confira [usar o controle de mapeamento do Azure Maps](https://aka.ms/amaadmc).
