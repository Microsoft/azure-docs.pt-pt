---
title: Gerir autenticação
titleSuffix: Azure Maps
description: Familiarize-se com a autenticação do Azure Maps. Veja qual a abordagem que funciona melhor em que cenário. Saiba como utilizar o portal para visualizar as definições de autenticação.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864079"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerir a autenticação em Azure Maps

Depois de criar uma conta Azure Maps, é criada uma identificação de cliente e chaves para suportar a autenticação do Azure Ative Directory (Azure AD) e a autenticação da Chave Partilhada.

## <a name="view-authentication-details"></a>Ver detalhes da autenticação

Depois de criar uma conta Azure Maps, as teclas primárias e secundárias são geradas. Recomendamos que utilize uma chave primária como chave de subscrição quando [utilizar a autenticação da Chave Partilhada para ligar para OZure Maps](./azure-maps-authentication.md#shared-key-authentication). Pode utilizar uma chave secundária em cenários como alterações de teclas rolantes. Para mais informações, consulte [autenticação no Azure Maps.](./azure-maps-authentication.md)

Pode ver os seus dados de autenticação no portal Azure. Ali, na sua conta, no menu **Definições,** selecione **Autenticação**.

> [!div class="mx-imgBorder"]
> ![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Descubra a categoria e o cenário

Dependendo das necessidades de aplicação, existem caminhos específicos para assegurar a aplicação. A Azure AD define categorias para suportar uma vasta gama de fluxos de autenticação. Consulte [as categorias de candidaturas](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) para perceber qual a categoria que a aplicação se enquadra.

> [!NOTE]
> Mesmo que utilize a autenticação de chaves partilhadas, a compreensão de categorias e cenários ajuda-o a garantir a aplicação.

## <a name="determine-authentication-and-authorization"></a>Determinar a autenticação e a autorização

A tabela que se segue descreve cenários comuns de autenticação e autorização no Azure Maps. A tabela fornece uma comparação dos tipos de proteção que cada cenário oferece.

> [!IMPORTANT]
> A Microsoft recomenda a implementação do Azure Ative Directory (Azure AD) com o controlo de acesso baseado em funções Azure (Azure RBAC) para aplicações de produção.

| Scenario                                                                                    | Autenticação | Autorização | Esforço de desenvolvimento | Esforço operacional |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Aplicação de cliente fidedigna de daemon /não-interativa](./how-to-secure-daemon-app.md)        | Chave Partilhada     | N/D           | Médio             | Alto               |
| [Aplicação de cliente fidedigna de daemon /não-interativa](./how-to-secure-daemon-app.md)        | Azure AD       | Alto          | Baixo                | Médio             |
| [Aplicação web de página única com único sign-on interativo](./how-to-secure-spa-users.md) | Azure AD       | Alto          | Médio             | Médio             |
| [Aplicação web de página única com sinal não interativo](./how-to-secure-spa-app.md)      | Azure AD       | Alto          | Médio             | Médio             |
| [Aplicação web com único sign-on interativo](./how-to-secure-webapp-users.md)          | Azure AD       | Alto          | Alto               | Médio             |
| [Dispositivo IoT / dispositivo constrangido de entrada](./how-to-secure-device-code.md)                     | Azure AD       | Alto          | Médio             | Médio             |

Os links na tabela levam-no a informações detalhadas de configuração para cada cenário.

## <a name="view-role-definitions"></a>Ver definições de funções

Para ver as funções Azure que estão disponíveis para O Azure Maps, vá ao **Controlo de Acesso (IAM)**. Selecione **Roles**, e, em seguida, procure por papéis que comecem com *Azure Maps*. Estas funções do Azure Maps são as funções a que podes conceder acesso.

> [!div class="mx-imgBorder"]
> ![Ver funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Ver atribuições de funções

Para ver utilizadores e apps a quem foi concedido acesso ao Azure Maps, aceda ao **Access Control (IAM)**. Lá, selecione **atribuições de funções** e, em seguida, filtrar por **Azure Maps**.

> [!div class="mx-imgBorder"]
> ![Ver utilizadores e apps a quem foi concedido acesso](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Fichas de pedido para Azure Maps

Solicite um sinal do ponto final da Azure AD. No seu pedido AZure AD, utilize os seguintes detalhes:

| Ambiente azul      | Ponto final simbólico Azure AD             | ID de recurso Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Nuvem pública azul     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nuvem do governo de Azure | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Para obter mais informações sobre a solicitação de tokens de acesso da Azure AD para utilizadores e diretores de serviço, consulte [cenários de autenticação para Azure AD](../active-directory/develop/authentication-vs-authorization.md) e veja cenários específicos na tabela de [Cenários.](./how-to-manage-authentication.md#determine-authentication-and-authorization)

## <a name="manage-and-rotate-shared-keys"></a>Gerir e rodar as teclas partilhadas

As suas teclas de subscrição Azure Maps são semelhantes a uma palavra-passe de raiz para a sua conta Azure Maps. Tenha sempre cuidado para proteger as suas chaves de subscrição. Utilize o Cofre da Chave Azure para gerir e rodar as chaves de forma segura. Evite distribuir chaves de acesso a outros utilizadores, codificando-as duramente ou guardando-as em qualquer lugar em texto simples que seja acessível a outros. Rode as chaves se acredita que podem ter sido comprometidas.

> [!NOTE]
> A Microsoft recomenda a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos, se possível, em vez de "Shared Key". A Azure AD proporciona segurança superior e facilidade de utilização sobre a Chave Partilhada.

### <a name="manually-rotate-subscription-keys"></a>Rode manualmente as teclas de subscrição

A Microsoft recomenda que rode as suas teclas de subscrição periodicamente para ajudar a manter a sua conta Azure Maps segura. Se possível, utilize o Cofre da Chave Azure para gerir as suas chaves de acesso. Se não estiver a utilizar o Key Vault, terá de rodar as chaves manualmente.

São atribuídas duas teclas de subscrição para que possa rodar as suas chaves. Ter duas teclas garante que a sua aplicação mantém o acesso ao Azure Maps durante todo o processo.

Para rodar as teclas de subscrição do Azure Maps no portal Azure:

1. Atualize o seu código de aplicação para fazer referência à chave secundária da conta Azure Maps e implementar.
2. Navegue para a sua conta Azure Maps no [portal Azure](https://portal.azure.com/).
3. Em **Definições**, selecione **Autenticação**.
4. Para regenerar a chave primária para a sua conta Azure Maps, selecione o botão **Regenerar** ao lado da tecla primária.
5. Atualize o seu código de aplicação para fazer referência à nova chave primária e implementar.
6. Regenerar a chave secundária da mesma forma.

> [!WARNING]
> A Microsoft recomenda a utilização de apenas uma das chaves em todas as suas aplicações ao mesmo tempo. Se utilizar a Chave 1 em alguns lugares e a Chave 2 noutros, não poderá rodar as chaves sem que algumas aplicações percam o acesso.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Azure AD e Azure Maps Web SDK](./how-to-use-map-control.md).

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore amostras que mostram como integrar a Azure AD com Azure Maps:

> [!div class="nextstepaction"]
> [Amostras de autenticação AD AZure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)