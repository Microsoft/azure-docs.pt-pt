---
title: Métodos de autenticação Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre o Diretório Ativo Azure (Azure AD) e a autenticação de Chave Partilhada. Ambos são utilizados para serviços microsoft Azure Maps. Saiba como obter a chave de subscrição do Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335708"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com o Azure Maps

O Azure Maps suporta duas formas de autenticar pedidos: Autenticação de Chave Partilhada e Autenticação de Diretório Ativo Azure. Este artigo explica estes métodos de autenticação para ajudar a orientar a sua implementação dos serviços do Azure Maps.

> [!NOTE]
> Para melhorar a comunicação segura com o Azure Maps, apoiamos agora a Transport Layer Security (TLS) 1.2, e estamos a retirar o suporte para TLS 1.0 e 1.1. Para evitar interrupções de serviço, **atualize os seus servidores e aplicações para utilizar o TLS 1.2 antes de 2**de abril de 2020 .  Se utilizar atualmente o TLS 1.x, avalie a sua prontidão TLS 1.2 e desenvolva um plano de migração com os testes descritos na [resolução do Problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticação chave partilhada

 As chaves primárias e secundárias são geradas após a criação da conta Azure Maps. É encorajado a usar a chave principal como chave de subscrição ao ligar para o Azure Maps usando a autenticação de chaves partilhadas. A autenticação da Chave Partilhada passa uma chave gerada por uma conta Azure Maps para um serviço Azure Maps. Para cada pedido aos serviços do Azure Maps, adicione a *chave de subscrição* como parâmetro ao URL. A chave secundária pode ser usada em cenários como mudanças de chave de rolamento.  

Para obter informações sobre a visualização das suas chaves no portal Azure, consulte [Gerir a autenticação](https://aka.ms/amauthdetails).

> [!Tip]
> Recomendamos regenerar as chaves regularmente. Tem duas chaves, para que possa manter ligações com uma chave enquanto regenera a outra. Quando regenerar as chaves, precisa de atualizar quaisquer aplicações, que acedam à sua conta, com as novas teclas.

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com Diretório Ativo Azure (Pré-visualização)

O Azure Maps oferece agora pedidos de autenticação para serviços Azure Maps utilizando [o Azure Ative Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) A Azure AD fornece autenticação baseada na identidade, incluindo [o controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). O RBAC é utilizado para conceder acesso ao nível do utilizador, ao nível do grupo ou ao nível da aplicação aos recursos do Azure Maps. As secções seguintes discutem conceitos e componentes da integração do Azure Maps com a Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com fichas de acesso OAuth

O Azure Maps aceita fichas de acesso **OAuth 2.0** para inquilinos da Azure AD associadas a uma subscrição azure que contém uma conta Azure Maps. O Azure Maps também aceita fichas para:

* Utilizadores de Anúncios Azure
* Aplicações de parceiros que utilizam permissões delegadas pelos utilizadores
* Identidades geridas para os recursos do Azure

O Azure Maps gera um *identificador único (ID do cliente)* para cada conta do Azure Maps. Pode solicitar fichas da Azure AD quando combinar este ID do cliente com parâmetros adicionais. Para solicitar um símbolo, especifique os valores na tabela seguinte com base no seu Ambiente Azure.

| Ambiente Azure   | Ponta final simbólica azure AD |
| --------------------|-------------------------|
| Azure Público        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Para obter mais informações sobre como configurar o Azure AD e solicitar fichas para o Azure Maps, consulte Gerir a [autenticação em Mapas Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Para obter informações gerais sobre a solicitação de fichas da AD Azure, consulte [o que é a autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Solicitar recursos do Mapa Azure com fichas OAuth

Depois de a Azure AD receber um símbolo, o Azure Maps envia um pedido com o seguinte conjunto de cabeçalhos de pedido necessários:

| Cabeçalho do pedido    |    Valor    |
|:------------------|:------------|
| x-ms-cliente-id    | 30d7cc....9f55|
| Autorização     | Portador eyJ0e....HNIVN |

> [!Note]
> `x-ms-client-id`é o GUID baseado na conta Azure Maps que aparece na página de autenticação do Azure Maps.

Aqui está um exemplo de um pedido de rota Do Azure Maps que usa um símbolo OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Para obter informações sobre a visualização do ID do seu cliente, consulte [ver detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Controlar o acesso com RBAC

Em Azure AD, utilize o RBAC para controlar o acesso a recursos seguros. Instale a sua conta Azure Maps e registe o seu Azure Maps Azure AD Tenant. O Azure Maps suporta a leitura do controlo de acesso para utilizadores individuais de Anúncios Azure, grupos, aplicações, recursos Azure e serviços Azure através de identidades geridas para recursos Azure. Na página do portal Azure Maps, pode configurar o RBAC para as suas funções escolhidas.

![Leitor de dados do Azure Maps (Pré-visualização)](./media/azure-maps-authentication/concept.png)

Para obter informações sobre a visualização das definições do RBAC, consulte [como configurar o RBAC para o Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades geridas para recursos Azure e Mapas Azure

[As identidades geridas para os recursos azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecem aos serviços Azure uma identidade gerida automaticamente, que pode ser autorizada a aceder aos serviços do Azure Maps. Alguns exemplos de identidades geridas incluem: Serviço de Aplicações Azure, Funções Azure e Máquinas Virtuais Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre autenticação de uma aplicação com a Azure AD e azure Maps, consulte Gerir a [autenticação no Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre a autenticação do Azure Maps Map Control e Azure AD, consulte [Use the Azure Maps Map Control](https://aka.ms/amaadmc).
