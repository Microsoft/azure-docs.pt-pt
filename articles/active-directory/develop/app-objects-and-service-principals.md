---
title: Apps & diretores de serviço sinuosos em Azure AD Azure
titleSuffix: Microsoft identity platform
description: Conheça a relação entre os objetos principais de aplicação e serviço no Diretório Ativo Azure.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 19085346fb5797245c9f71911f8178df0a1b742a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263013"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos do principal de serviço e aplicação no Azure Active Directory

Por vezes, o significado do termo "aplicação" pode ser mal compreendido quando usado no contexto do Azure Ative Directory (Azure AD). Este artigo clarifica os aspetos conceptuais e concretos da integração de aplicações da AD Azure, com uma ilustração de registo e consentimento para uma [aplicação multi-arrendatária.](developer-glossary.md#multi-tenant-application)

## <a name="overview"></a>Descrição geral

Uma aplicação que foi integrada com a Azure AD tem implicações que vão além do aspeto do software. A "aplicação" é frequentemente utilizada como termo conceptual, referindo-se não só ao software de aplicação, mas também ao seu registo de AD Azure e ao seu papel na autenticação/autorização de "conversas" em tempo de execução.

Por definição, uma aplicação pode funcionar nestas funções:

- [Papel do cliente](developer-glossary.md#client-application) (consumir um recurso)
- Função do [servidor de recursos](developer-glossary.md#resource-server) (expondo APIs aos clientes)
- Tanto a função do cliente como o papel do servidor de recursos

Um fluxo de subvenção de [autorização OAuth 2.0](developer-glossary.md#authorization-grant) define o protocolo de conversação, que permite ao cliente/recurso aceder/proteger os dados de um recurso, respectivamente.

Nas seguintes secções, verá como o modelo de aplicação Azure AD representa uma aplicação no tempo de design e no tempo de execução.

## <a name="application-registration"></a>Registo da aplicação

Ao registar uma aplicação Azure AD no [portal Azure,][AZURE-Portal]são criados dois objetos no seu inquilino Azure AD:

- Um objeto de aplicação, e
- Um objeto do principal de serviço

### <a name="application-object"></a>Objeto de aplicação

Um pedido da AD Azure é definido pelo seu único objeto de candidatura, que reside no inquilino da AD Azure onde o pedido foi registado, conhecido como o inquilino "casa" do pedido. A entidade microsoft graph [application][MS-Graph-App-Entity] define o esquema para as propriedades de um objeto de aplicação.

### <a name="service-principal-object"></a>Objeto principal de serviço

Para aceder a recursos garantidos por um inquilino da AD Azure, a entidade que exige acesso deve ser representada por um diretor de segurança. Isto é verdade tanto para utilizadores (diretor de utilizador) como para aplicações (diretor de serviço).

O responsável pela segurança define a política de acesso e as permissões para o utilizador/aplicação no inquilino da AD Azure. Isto permite funcionalidades fundamentais como a autenticação do utilizador/aplicação durante o início de sessão e a autorização durante o acesso aos recursos.

Quando um pedido é autorizado a aceder a recursos num inquilino (mediante registo ou [consentimento),](developer-glossary.md#consent)é criado um objeto principal de serviço. A entidade Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] define o esquema para as propriedades de um objeto principal de serviço.

### <a name="application-and-service-principal-relationship"></a>Relação do principal de serviço e aplicação

Considere o objeto de candidatura como a representação *global* do seu pedido de utilização em todos os inquilinos, e o diretor de serviço como a representação *local* para uso em um inquilino específico.

O objeto da aplicação serve como o modelo a partir do qual as propriedades comuns e predefinidas são *derivadas* para utilização na criação de objetos correspondentes do principal de serviço. Um objeto de aplicação tem, portanto, uma relação 1:1 com a aplicação de software, e uma relação de 1:muitas relações com o seu principal objeto de serviço correspondente.

Deve ser criado um diretor de serviço em cada inquilino onde o pedido seja utilizado, permitindo-lhe estabelecer uma identidade para o acesso e/ou acesso aos recursos garantidos pelo arrendatário. Uma aplicação de inquilino único tem apenas um principal de serviço (no inquilino principal), criado e com permissão para utilização durante o registo da aplicação. Uma aplicação Web/API multi-inquilino também tem um diretor de serviço criado em cada inquilino onde um utilizador desse inquilino consentiu a sua utilização.

> [!NOTE]
> Quaisquer alterações que efaça ao seu objeto de candidatura, também se refletem no seu principal objeto de serviço apenas no inquilino da casa da aplicação (o inquilino onde foi registado). Para aplicações multi-arrendatárias, as alterações ao objeto de pedido não se refletem em quaisquer objetos principais de serviço dos inquilinos de consumo, até que o acesso seja removido através do Painel de Acesso à [Aplicação](https://myapps.microsoft.com) e concedido novamente.
>
> Note também que as aplicações nativas são registadas como multi-inquilinos por padrão.

## <a name="example"></a>Exemplo

O diagrama seguinte ilustra a relação entre o objeto de aplicação de uma aplicação e os objetos principais de serviço correspondentes, no contexto de uma aplicação de multi-inquilinos de amostra chamada **aplicação HR**. Há três inquilinos da AD Azure neste cenário de exemplo:

- **Adatum** - O inquilino usado pela empresa que desenvolveu a **app HR**
- **Contoso** - O inquilino utilizado pela organização Contoso, que é consumidor da **app HR**
- **Fabrikam** - O inquilino usado pela organização Fabrikam, que também consome a **app HR**

![Relação entre objeto de aplicação e objeto principal de serviço](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Neste cenário de exemplo:

| Passo | Descrição |
|------|-------------|
| 1    | É o processo de criação dos objetos principais de aplicação e serviço no inquilino da casa da aplicação. |
| 2    | Quando os administradores de Contoso e Fabrikam completam o consentimento, um objeto principal de serviço é criado no inquilino Azure AD da sua empresa e atribuído as permissões que o administrador concedeu. Note também que a aplicação HR pode ser configurada/concebida para permitir o consentimento dos utilizadores para uso individual. |
| 3    | Os inquilinos consumidores da aplicação HR (Contoso e Fabrikam) têm cada um o seu próprio objeto principal de serviço. Cada um representa a sua utilização de uma instância do pedido em tempo de execução, regida pelas permissões conferidas pelo respetivo administrador. |

## <a name="next-steps"></a>Passos seguintes

- Pode utilizar o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) para consultar tanto a aplicação como os principais objetos de serviço.
- Pode aceder ao objeto de aplicação de uma aplicação utilizando a Microsoft Graph API, o manifesto editor de aplicações [do portal Azure,][AZURE-Portal] ou [cmdlets Azure AD PowerShell, representados](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)pela sua entidade de [Aplicação][MS-Graph-App-Entity]OData.
- Pode aceder ao principal objeto de serviço de uma aplicação através dos cmdlets Microsoft Graph API ou [Azure AD PowerShell,](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)representados pela sua [entidade Principal][MS-Graph-Sp-Entity]de Serviço oData .

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
