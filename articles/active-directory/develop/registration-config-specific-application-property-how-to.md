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
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01ff1e2d0c9bc926d54bd54716e0579ef395ec0
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655999"
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
| Nome             | O nome da aplicação. Ele deve ter um mínimo de quatro carateres.                |
| Tipos de conta suportados| Selecionar quais as contas que pretender seu aplicativo para oferecer suporte a: contas apenas neste diretório organizacional, contas de qualquer diretório organizacional ou contas em qualquer diretório organizacional e contas Microsoft pessoais.  |
| (Opcional) do URI de redirecionamento | Selecione o tipo de aplicação que quer criar, **Web** ou **cliente público (móvel e ambiente de trabalho)** e, em seguida, introduza o URI de redirecionamento (ou URL de resposta) para a sua aplicação. Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, http://localhost:31544 pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web. Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico para a sua aplicação, como myapp://auth. Para ver exemplos específicos de aplicações Web ou nativas, veja os nossos [inícios rápidos](https://docs.microsoft.com/azure/active-directory/develop).|

Quando tiver preenchido as campos acima, o aplicativo está registado no portal do Azure e será redirecionado para a página de descrição geral do aplicativo. As páginas de definições no painel esquerdo em **gerir** têm mais campos para personalizar seu aplicativo. As tabelas a seguir descrevem todos os campos. Apenas conseguiria obter um subconjunto desses campos, dependendo de ter criado uma aplicação web ou uma aplicação cliente público.

### <a name="overview"></a>Descrição geral
| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da aplicação  | Ao registar uma aplicação, o Azure AD atribui seu aplicativo uma ID da aplicação. O ID pode ser utilizado para identificar exclusivamente a sua aplicação em pedidos de autenticação para o Azure AD, bem como para aceder aos recursos de aplicativo, como a Graph API.                                                          |
| URI de ID de aplicação      | Isso deve ser um URI exclusivo, normalmente, de forma **https://&lt;inquilino\_nome&gt;/&lt;aplicação\_nome&gt;.** Isto é utilizado durante o fluxo de concessão de autorização, como um identificador exclusivo para especificar o recurso que o token deve ser emitido para. Ele também torna-se a afirmação "aud" no token de acesso emitido. |

### <a name="branding"></a>Imagem corporativa

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logótipo | Pode utilizar este para carregar um logótipo para a sua aplicação. O logótipo tem de estar no formato. bmp,. jpg ou. PNG e o tamanho do ficheiro deve ser inferior a 100 KB. As dimensões para a imagem devem ser pixels 215 x 215, com dimensões da imagem central de: 94 x 94 pixéis.|
| URL da home page   | Este é o URL de início de sessão especificado durante o registo de aplicação.|

### <a name="authentication"></a>Authentication

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL de fim de sessão      | Este é o URL de fim de sessão de fim de sessão único. Azure AD envia um pedido de fim de sessão para este URL quando o utilizador desmarca a sessão com o Azure AD com qualquer outra aplicação registada.|
| Tipos de conta suportados  | Este parâmetro especifica se o aplicativo pode ser utilizado por vários inquilinos. Normalmente, isso significa que as organizações externas podem usar seu aplicativo registrando-lo no seu inquilino e conceder acesso aos dados da sua organização.|
| URLs de redirecionamento      | O redirecionamento ou resposta, as URLs são os pontos finais de onde o Azure AD devolve quaisquer tokens que a aplicação solicite. Para aplicativos nativos, isso é onde o utilizador é enviado após a autorização bem-sucedida. O Azure AD verifica que o seu aplicativo fornece no pedido OAuth 2.0 de URI de redirecionamento corresponde a um dos valores registrados no portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos do cliente            | É possível criar cliente segredos ou chaves, de forma programática acesso APIs web protegidas pelo Azure AD sem qualquer interação do utilizador. Partir do **novo segredo do cliente** página, introduza uma descrição da chave e a data de expiração e guardar para gerar a chave. Certifique-se de guardar num local seguro, pois não será possível aceder à mesma mais tarde.             |

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](../manage-apps/what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
