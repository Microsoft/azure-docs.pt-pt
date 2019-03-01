---
title: Autenticação com o Azure Maps | Documentos da Microsoft
description: Autenticação para utilizar os serviços do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9dfe4024607e106565984d6d49de94d793bf7a8f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010417"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com o Azure Maps

Mapas do Azure suporta duas formas de autenticar pedidos: Chave partilhada e o Azure Active Directory (Azure AD). Este artigo explica estes métodos de autenticação para ajudar a orientar a sua implementação.

## <a name="shared-key-authentication"></a>Autenticação de chave partilhada

Autenticação de chave partilhada passa as chaves geradas por uma conta do Azure Maps com cada solicitação para o Azure Maps.  Duas chaves são geradas quando é criada a sua conta do Azure Maps. Para cada solicitação de serviços do Azure Maps, a chave de subscrição tem de ser adicionado como um parâmetro para o URL.

> [!Tip]
> Recomendamos a regeneração das chaves regularmente. É apresentada com duas chaves para que possa manter as ligações com uma chave enquanto Regenera a outra. Quando regenerar as chaves, tem de atualizar quaisquer aplicações que acedam a conta a utilizar as novas chaves.

Para obter informações sobre como ver as suas chaves, consulte [ver os detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com o Azure Active Directory (pré-visualização)

O Azure oferece agora de mapas [do Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) integração para a autenticação de pedidos para os serviços do Azure Maps. O Azure AD fornece autenticação baseada em identidades, incluindo [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), para conceder acesso de nível de usuário ou nível de aplicativo aos recursos do Azure Maps. As secções que se seguem podem ajudá-lo a compreender os conceitos e componentes de integração do Azure Maps com o Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com tokens de acesso de OAuth

Mapas do Azure aceita **OAuth 2.0** tokens de acesso para inquilinos do Azure AD associados a uma subscrição do Azure que contém uma conta do Azure Maps. Mapas do Azure aceita tokens para:

* Utilizadores do Azure AD. 
* Aplicações de parceiros que utilizam permissões delegadas pelos utilizadores.
* Identidades geridas para recursos do Azure.

O Azure Maps gera uma *Identificador exclusivo (ID de cliente)* para cada conta do Azure Maps. Ao combiná-este ID de cliente com parâmetros adicionais, pode pedir tokens do Azure AD, especificando o seguinte valor:

```
https://login.microsoftonline.com
```
Para obter mais informações sobre como configurar o Azure AD e solicitar tokens para o Azure Maps, consulte [autenticação de gerir no Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Para obter informações gerais sobre solicitar tokens do Azure AD, consulte [o que é a autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Solicitar recursos de mapa do Azure com tokens de OAuth

Depois de é recebido um token do Azure AD, pode ser enviado um pedido para o Azure Maps, com o seguinte conjunto de cabeçalhos de solicitação necessários dois:

| Cabeçalho do pedido    |    Value    |
|:------------------|:------------|
| x-MS-client-ID    | 30d7cc….9f55|
| Autorização     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` é o Azure Maps com base na conta GUID que é apresentado na página de autenticação do Azure Maps.

Eis um exemplo de um pedido de rota do Azure Maps que utiliza um token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Para obter informações sobre a visualização de seu ID de cliente, consulte [ver os detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Controlar o acesso com RBAC

O Azure AD permite que controlar o acesso para a recursos protegidos pelo utilizando o RBAC. Depois de criar a sua conta do Azure Maps e registar a sua aplicação do Azure Maps do Azure AD no seu inquilino do Azure AD, pode configurar o RBAC para um utilizador, aplicação ou recurso do Azure na página de portal de conta do Azure Maps.

Mapas do Azure suporta o controlo de acesso de leitura de pessoa utilizadores do Azure AD, aplicações e serviços do Azure através de identidades geridas para recursos do Azure.

![Leitor de Dados do Azure Maps (Pré-visualização)](./media/azure-maps-authentication/concept.png)

Para obter informações sobre como ver as definições de RBAC, veja [como configurar o RBAC para o Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades geridas para recursos do Azure e o Azure Maps

[Gerido identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecer serviços do Azure (serviço de aplicações do Azure, as funções do Azure, máquinas virtuais do Azure e assim por diante) com uma identidade gerida automaticamente, que pode ser autorizada para acesso aos serviços do Azure Maps.  

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como autenticar uma aplicação com o Azure AD e o Azure Maps, consulte [autenticação de gerir no Azure Maps](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre autenticação, o controle de mapa do Azure Maps e o Azure AD, veja [usar o controle de mapa do Azure Maps](https://aka.ms/amaadmc).