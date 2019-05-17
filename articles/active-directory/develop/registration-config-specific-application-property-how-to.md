---
title: Como preencher campos específicos para uma aplicação personalizada desenvolvida | Documentos da Microsoft
description: Documentação de orientação sobre como preencher campos específicos ao registar uma aplicação personalizada desenvolvida com o Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b93f26080229e980b680c157f59db4edf33e7a
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545487"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Como preencher campos específicos para uma aplicação personalizada desenvolvida

Este artigo fornece uma breve descrição de todos os campos disponíveis no formulário de registo de aplicação no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registar uma nova aplicação

-   Para registar uma nova aplicação, navegue para o [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **do Azure Active Directory.**

-   Escolher **registos de aplicações** e clique em **Add**.

-   Este abra o formulário de Registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos no formulário de Registro do aplicativo


| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name             | O nome da aplicação. Ele deve ter um mínimo de quatro carateres.                |
| Tipo de Aplicação | **Aplicação/Web API da Web**: Uma aplicação que represente uma aplicação web, uma API web ou ambos 
| |**Nativo**: Um aplicativo que pode ser instalado num computador ou dispositivo do utilizador           |
| URL de início de sessão      | O URL onde os utilizadores podem iniciar sessão para utilizar a sua aplicação                                  |

Quando tiver preenchido as campos acima, o aplicativo está registado no portal do Azure e será redirecionado para a página de aplicativo. O **definições** botão no painel aplicação abre-se a página de definições, que tem mais campos para personalizar seu aplicativo. A tabela abaixo descreve todos os campos na página de definições. tenha em atenção que apenas conseguiria obter um subconjunto desses campos, dependendo de ter criado uma aplicação web ou um aplicativo nativo.

| Campo           | Descrição                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da aplicação  | Ao registar uma aplicação, o Azure AD atribui seu aplicativo uma ID da aplicação. O ID pode ser utilizado para identificar exclusivamente a sua aplicação em pedidos de autenticação para o Azure AD, bem como para aceder aos recursos de aplicativo, como a Graph API.                                                          |
| URI do ID da Aplicação      | Isso deve ser um URI exclusivo, normalmente, de forma **https://&lt;inquilino\_nome&gt;/&lt;aplicação\_nome&gt;.** Isto é utilizado durante o fluxo de concessão de autorização, como um identificador exclusivo para especificar o recurso que o token deve ser emitido para. Ele também torna-se a afirmação "aud" no token de acesso emitido. |
| Carregar novo logótipo | Pode utilizar este para carregar um logótipo para a sua aplicação. O logótipo tem de estar no formato. bmp,. jpg ou. PNG e o tamanho do ficheiro deve ser inferior a 100KB. As dimensões para a imagem devem ser pixels 215 x 215, com dimensões da imagem central de: 94 x 94 pixéis.                                                       |
| URL da home page   | Este é o URL de início de sessão especificado durante o registo de aplicação.                                                                                                                                                                                                                                              |
| URL de fim de sessão      | Este URL de fim de sessão de fim de sessão único. Azure AD envia um pedido de fim de sessão para este URL quando o utilizador desmarca a sessão com o Azure AD com qualquer outra aplicação registada.                                                                                                                                       |
| Com Múltiplos Inquilinos  | Este parâmetro especifica se o aplicativo pode ser utilizado por vários inquilinos. Normalmente, isso significa que as organizações externas podem usar seu aplicativo registrando-lo no seu inquilino e conceder acesso aos dados da sua organização.                                                                   |
| URLs de Resposta      | A resposta URLs são os pontos finais de onde o Azure AD devolve quaisquer tokens que a aplicação solicite.                                                                                                                                                                                                          |
| URIs de Redirecionamento   | Para aplicativos nativos, isso é onde o utilizador é enviado após a autorização bem-sucedida. Verificação do Azure AD que o seu aplicativo de URI de redirecionamento fornece no pedido OAuth 2.0 corresponde a um dos valores registrados no portal.                                                            |
| Chaves            | Pode criar chaves para programaticamente acesso APIs web protegidas pelo Azure AD sem qualquer interação do utilizador. Partir do \* \*chaves\* \* página, introduza uma descrição da chave e a data de expiração e guardar para gerar a chave. Certifique-se de guardar num local seguro, pois não será possível aceder à mesma mais tarde.             |

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](../manage-apps/what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
