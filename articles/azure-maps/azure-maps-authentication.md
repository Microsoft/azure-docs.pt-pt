---
title: Métodos de autenticação | Mapas do Microsoft Azure
description: Neste artigo, você vai aprender sobre o Diretório Ativo Azure (Azure AD) e a autenticação de Chave Partilhada. Ambos são utilizados para serviços microsoft Azure Maps. Saiba como obter a chave de assinatura do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 2bcc2d4c92e903b723bffa8461a8a1a10534d3e4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025627"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com o Azure Maps

O Azure Maps suporta duas formas de autenticar pedidos: Autenticação de Chave Partilhada e Autenticação de Diretório Ativo Azure. Este artigo explica esses métodos de autenticação para ajudar a guiar sua implementação.

## <a name="shared-key-authentication"></a>Autenticação de chave compartilhada

A autenticação de chave compartilhada passa as chaves geradas por uma conta do Azure Maps com cada solicitação para mapas do Azure. Para cada solicitação para os serviços do Azure Maps, a *chave de assinatura* precisa ser adicionada como um parâmetro à URL. As chaves primária e secundária são geradas depois que a conta do Azure Maps é criada. Recomendamos que utilize a chave principal como chave de subscrição quando ligar para o Azure Maps utilizando a autenticação de chaves partilhadas. A chave secundária pode ser usada em cenários como alterações de chave sem interrupção.  

Para obter informações sobre como exibir suas chaves no portal do Azure, consulte [gerenciar a autenticação](https://aka.ms/amauthdetails).

> [!Tip]
> É recomendável regenerar suas chaves regularmente. Tem duas chaves, para que possa manter ligações com uma chave enquanto regenera a outra. Quando regenerar as chaves, precisa de atualizar quaisquer aplicações que acedam à sua conta com as novas teclas.



## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com Azure Active Directory (versão prévia)

O Azure Maps oferece agora pedidos de autenticação para serviços Azure Maps utilizando [o Azure Ative Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) A Azure AD fornece autenticação baseada na identidade, incluindo [o controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). O RBAC é utilizado para conceder acesso ao nível do utilizador, ao nível do grupo ou ao nível da aplicação aos recursos do Azure Maps. As próximas secções podem ajudá-lo a entender conceitos e componentes da integração do Azure Maps com a Azure AD.
## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com tokens de acesso OAuth

O Azure Maps aceita tokens de acesso **OAuth 2,0** para locatários do Azure ad associados a uma assinatura do Azure que contém uma conta do Azure Maps. O Azure Maps aceita tokens para:

* Utilizadores de Anúncios Azure
* Aplicações de parceiros que utilizam permissões delegadas pelos utilizadores
* Identidades geridas para os recursos do Azure

O mapas do Azure gera um *identificador exclusivo (ID do cliente)* para cada conta do Azure Maps. Pode solicitar fichas da Azure AD quando combinar este ID do cliente com parâmetros adicionais. Para solicitar um símbolo, é necessário especificar os valores na tabela seguinte com base no seu Ambiente Azure.

| Ambiente do Azure   | Ponto de extremidade de token do Azure AD |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Para obter mais informações sobre como configurar o Azure AD e solicitar tokens para mapas do Azure, consulte [gerenciar a autenticação no Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Para obter informações gerais sobre como solicitar tokens do AD do Azure, consulte [o que é autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Solicitar recursos do Azure MAP com tokens OAuth

Depois de receber uma ficha da Azure AD, um pedido é enviado para o Azure Maps com o seguinte conjunto de cabeçalhos de pedido exigidos:

| Cabeçalho do pedido    |    Valor    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc....9f55|
| Autorização     | Bearer eyJ0e….HNIVN |

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

Em Azure AD, utilize o RBAC para controlar o acesso a recursos seguros. Configura risa a sua conta Azure Maps e registe o seu Azure Maps Azure AD TENANT. O Azure Maps suporta a leitura do controlo de acesso para utilizadores individuais de Anúncios Azure, grupos, aplicações, recursos Azure e serviços Azure através de identidades geridas para recursos Azure. Na página do portal Azure Maps, pode configurar o RBAC para as suas funções desejadas.

![Leitor de dados do Azure Maps (versão prévia)](./media/azure-maps-authentication/concept.png)

Para obter informações sobre como exibir suas configurações de RBAC, consulte [como configurar o RBAC para mapas do Azure](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades gerenciadas para recursos do Azure e mapas do Azure

[As identidades geridas para os recursos azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecem aos serviços Azure uma identidade gerida automaticamente, que pode ser autorizada a aceder aos serviços do Azure Maps. Alguns exemplos de identidades geridas incluem: Serviço de Aplicações Azure, Funções Azure e Máquinas Virtuais Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como autenticar um aplicativo com o Azure AD e o Azure Maps, consulte [gerenciar a autenticação no Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre como autenticar o Controle de Mapeamento do Azure Maps e o Azure AD, confira [usar o controle de mapeamento do Azure Maps](https://aka.ms/amaadmc).
