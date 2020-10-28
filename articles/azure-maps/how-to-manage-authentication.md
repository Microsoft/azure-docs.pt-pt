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
ms.openlocfilehash: 57e847116febcea66e1e3ac4ba131617463b6c94
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895771"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerir a autenticação em Azure Maps

Depois de criar uma conta Azure Maps, é criada uma identificação de cliente e chaves para suportar a autenticação do Azure Ative Directory (Azure AD) e a autenticação da Chave Partilhada.

## <a name="view-authentication-details"></a>Ver detalhes da autenticação

Depois de criar uma conta Azure Maps, as teclas primárias e secundárias são geradas. Recomendamos que utilize uma chave primária como chave de subscrição quando [utilizar a autenticação da Chave Partilhada para ligar para OZure Maps](./azure-maps-authentication.md#shared-key-authentication). Pode utilizar uma chave secundária em cenários como alterações de teclas rolantes. Para mais informações, consulte [autenticação no Azure Maps.](./azure-maps-authentication.md)

Pode ver os seus dados de autenticação no portal Azure. Ali, na sua conta, no menu **Definições,** selecione **Autenticação** .

> [!div class="mx-imgBorder"]
> ![Detalhes da autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Descubra a categoria e o cenário

Dependendo das necessidades de aplicação, existem caminhos específicos para assegurar a aplicação. A Azure AD define categorias para suportar uma vasta gama de fluxos de autenticação. Consulte [as categorias de candidaturas](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) para perceber qual a categoria que a aplicação se enquadra.

> [!NOTE]
> Mesmo que utilize a autenticação de chaves partilhadas, a compreensão de categorias e cenários ajuda-o a garantir a aplicação.

## <a name="determine-authentication-and-authorization"></a>Determinar a autenticação e a autorização

A tabela que se segue descreve cenários comuns de autenticação e autorização no Azure Maps. A tabela fornece uma comparação dos tipos de proteção que cada cenário oferece.

> [!IMPORTANT]
> A Microsoft recomenda a implementação do Azure Ative Directory (Azure AD) com o controlo de acesso baseado em funções Azure (Azure RBAC) para aplicações de produção.

| Cenário                                                                                    | Autenticação | Autorização | Esforço de desenvolvimento | Esforço operacional |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Aplicação de cliente fidedigna de daemon /não-interativa](./how-to-secure-daemon-app.md)        | Chave Partilhada     | N/D           | Médio             | Alto               |
| [Aplicação de cliente fidedigna de daemon /não-interativa](./how-to-secure-daemon-app.md)        | Azure AD       | Alto          | Baixo                | Médio             |
| [Aplicação web de página única com único sign-on interativo](./how-to-secure-spa-users.md) | Azure AD       | Alto          | Médio             | Médio             |
| [Aplicação web de página única com sinal não interativo](./how-to-secure-spa-app.md)      | Azure AD       | Alto          | Médio             | Médio             |
| [Aplicação web com único sign-on interativo](./how-to-secure-webapp-users.md)          | Azure AD       | Alto          | Alto               | Médio             |
| [Dispositivo IoT / dispositivo constrangido de entrada](./how-to-secure-device-code.md)                     | Azure AD       | Alto          | Médio             | Médio             |

Os links na tabela levam-no a informações detalhadas de configuração para cada cenário.

## <a name="view-role-definitions"></a>Ver definições de funções

Para ver as funções Azure que estão disponíveis para O Azure Maps, vá ao **Controlo de Acesso (IAM)** . Selecione **Roles** , e, em seguida, procure por papéis que comecem com *Azure Maps* . Estas funções do Azure Maps são as funções a que podes conceder acesso.

> [!div class="mx-imgBorder"]
> ![Ver funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Ver atribuições de funções

Para ver utilizadores e apps a quem foi concedido acesso ao Azure Maps, aceda ao **Access Control (IAM)** . Lá, selecione **atribuições de funções** e, em seguida, filtrar por **Azure Maps** .

> [!div class="mx-imgBorder"]
> ![Ver utilizadores e apps a quem foi concedido acesso](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Fichas de pedido para Azure Maps

Solicite um sinal do ponto final da Azure AD. No seu pedido AZure AD, utilize os seguintes detalhes:

| Ambiente azul      | Ponto final simbólico Azure AD             | ID de recurso Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Nuvem pública azul     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nuvem do governo de Azure | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Para obter mais informações sobre a solicitação de tokens de acesso da Azure AD para utilizadores e diretores de serviço, consulte [cenários de autenticação para Azure AD](../active-directory/develop/authentication-vs-authorization.md) e veja cenários específicos na tabela de [Cenários.](./how-to-manage-authentication.md#determine-authentication-and-authorization)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Azure AD e Azure Maps Web SDK](./how-to-use-map-control.md).

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore amostras que mostram como integrar a Azure AD com Azure Maps:

> [!div class="nextstepaction"]
> [Amostras de autenticação AD AZure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)