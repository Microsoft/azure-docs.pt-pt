---
title: Aplicação e objetos de principal de serviço no Azure Active Directory
description: Saiba mais sobre a relação entre a aplicação e objetos de principal de serviço no Azure Active Directory.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b7ab4007a94efbc48bfea67c6b954c02de0b2ba
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887442"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Aplicação e objetos de principal de serviço no Azure Active Directory

Às vezes, o significado de "aplicação" pode ser mal compreendido quando utilizado no contexto do Azure Active Directory (Azure AD). Este artigo esclarece os aspetos conceituais e concretos de integração de aplicações do Azure AD, com uma ilustração de registo e o consentimento para um [aplicação multi-inquilino](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Descrição geral

Uma aplicação que foi integrada com o Azure AD tem implicações que vão além do aspecto de software. "Aplicação" é frequentemente utilizada como um termo conceitual, que faça referência a não apenas o software de aplicação, mas também o registo do Azure AD e função na autenticação/autorização "conversas" em tempo de execução.

Por definição, um aplicativo possa funcionar nessas funções:

- [Cliente](developer-glossary.md#client-application) função (consumindo um recurso)
- [Servidor de recurso](developer-glossary.md#resource-server) função (expor as APIs para os clientes)
- Tanto a função de cliente, como a função de servidor de recurso

Uma [fluxo de concessão de autorização do OAuth 2.0](developer-glossary.md#authorization-grant) define o protocolo de conversação, o que permite que o cliente/recurso para acesso/proteger os dados de um recurso, respectivamente.

Nas seções a seguir, verá como o modelo de aplicação do Azure AD representa uma aplicação em tempo de design e tempo de execução.

## <a name="application-registration"></a>Registo da aplicação

Quando registar uma aplicação do Azure AD no [portal do Azure][AZURE-Portal], dois objetos são criados no seu inquilino do Azure AD:

- Um objeto de aplicativo, e
- Um objeto principal de serviço

### <a name="application-object"></a>objeto de aplicativo

Uma aplicação do Azure AD é definida por seu um e apenas a objeto application, que reside no inquilino do Azure AD em que a aplicação foi registrada, conhecido como inquilino "principal" da aplicação. O Azure AD Graph [entidade de aplicativo] [ AAD-Graph-App-Entity] define o esquema para as propriedades de um objeto de aplicação.

### <a name="service-principal-object"></a>objeto do principal de serviço

Para acessar recursos protegidos pelo inquilino do Azure AD, a entidade que precisa de acesso tem de ser representada por uma entidade de segurança. Isso é verdadeiro para aplicativos (principal de serviço) e os utilizadores (principal de utilizador).

A entidade de segurança define a política de acesso e permissões para a aplicação/utilizador no inquilino do Azure AD. Isto permite que os principais recursos como a autenticação de utilizador/aplicação durante o início de sessão e autorização durante o acesso a recursos.

Quando um aplicativo é concedido permissão para aceder aos recursos num inquilino (após o registo ou [consentimento](developer-glossary.md#consent)), é criado um objeto principal de serviço. O Azure AD Graph [entidade ServicePrincipal] [ AAD-Graph-Sp-Entity] define o esquema para as propriedades de um serviço principal do objeto.

### <a name="application-and-service-principal-relationship"></a>Aplicação e a relação de principal de serviço

Considere o objeto de aplicativo como o *global* representação da sua aplicação para utilização em todos os inquilinos e o principal de serviço como o *local* representação para utilização num inquilino específico.

O serve de objeto de aplicativo como o modelo a partir do qual comuns e as propriedades predefinidas é *derivado* para utilização na criação de objetos de principal de serviço correspondentes. Um objeto de aplicativo, portanto, tem uma relação de 1:1 com o aplicativo de software e uma relação de 1:many com seus objetos de principal de serviço correspondentes.

Um principal de serviço tem de ser criado em cada inquilino onde o aplicativo é usado, permitindo que ele estabeleça uma identidade para início de sessão e/ou acesso a recursos a serem protegidos pelo inquilino. Uma aplicação de inquilino único tem apenas um principal de serviço (no inquilino principal), criado e dar o seu consentimento para utilização durante o registo de aplicação. Uma multi-inquilino aplicação/API Web também tem um principal de serviço criado em cada inquilino em que um utilizador de inquilino deu consentimento à sua utilização. 

> [!NOTE]
> As alterações que fizer para seu objeto de aplicativo, também são refletidas no seu objeto principal de serviço no inquilino principal da aplicação apenas (o inquilino onde foi registada). Para aplicações multi-inquilino, alterações para o objeto de aplicativo não são refletidas em objetos de principal de serviço dos inquilinos qualquer consumidor, até que o acesso é removido por meio do [painel de acesso de aplicação](https://myapps.microsoft.com) e concederam novamente.
>
> Observe também que os aplicativos nativos são registados como multi-inquilino por predefinição.

## <a name="example"></a>Exemplo

O diagrama seguinte ilustra a relação entre um aplicativo objeto application e objetos de principal, no contexto de uma aplicação multi-inquilino de exemplo chamados de serviço correspondente **aplicação de RH**. Existem três inquilinos do Azure AD neste cenário de exemplo:

- **Adatum** -o inquilino utilizado pela empresa que desenvolveu o **aplicação de RH**
- **Contoso** -o inquilino utilizado pela organização Contoso, que é um consumidor do **aplicação de RH**
- **A Fabrikam** -o inquilino utilizado pela organização Fabrikam, que também consome o **aplicação de RH**

![Relação entre um objeto de aplicativo e um objeto principal de serviço](./media/app-objects-and-service-principals/application-objects-relationship.png)

Neste cenário de exemplo:

| Passo | Descrição |
|------|-------------|
| 1    | É o processo de criação do aplicativo e objetos de principal de serviço no inquilino principal da aplicação. |
| 2    | Quando os administradores de Contoso e Fabrikam concluir consentimento, um objeto principal de serviço é criado no inquilino do Azure AD da empresa e atribuído as permissões que o administrador concedido. Observe também que a aplicação de RH pode ser configurado/projetado para permitir que o consentimento por utilizadores para utilização individual. |
| 3    | Os inquilinos de consumidor do RH aplicativo (Contoso e Fabrikam) cada tem seu próprio objeto principal do serviço. Cada representa seu uso de uma instância do aplicativo em tempo de execução, regido pelas permissões consentidas pelo administrador do respectivo. |

## <a name="next-steps"></a>Passos Seguintes

- Pode utilizar o [do Azure AD Graph](https://graphexplorer.azurewebsites.net/) para consultar o aplicativo e os objetos principais de serviço.
- Pode acessar o objeto de aplicativo de uma aplicação com o Azure AD Graph API, o [do portal do Azure] [ AZURE-Portal] editor de manifesto de aplicativo, ou [cmdlets do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), como ilustrado pelos seu OData [entidade de aplicativo][AAD-Graph-App-Entity].
- Pode acessar o objeto principal do serviço de um aplicativo por meio da API do Azure AD Graph ou [cmdlets do PowerShell do Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado pelo seu OData [ServicePrincipal entidade] [ AAD-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[AAD-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
