---
title: Apps & os diretores de serviços em Azure AD ! Rio Azure
titleSuffix: Microsoft identity platform
description: Conheça a relação entre os objetos principais de aplicação e serviço no Azure Ative Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: e4325303f5a10fa1df670495dd6d8190167182e8
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861107"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos do principal de serviço e aplicação no Azure Active Directory

Este artigo descreve o registo de pedidos, objetos de aplicação e diretores de serviço no Azure Ative Directory: o que são, como são usados e como estão relacionados uns com os outros. É também apresentado um cenário de exemplo multi-inquilino para ilustrar a relação entre o objeto de aplicação de uma aplicação e os objetos principais de serviço correspondentes.

## <a name="application-registration"></a>Registo da aplicação
Para delegar funções de Gestão de Identidade e Acesso à Azure AD, deve ser registado um pedido junto de um [inquilino](developer-glossary.md#tenant)da AD Azure. Quando regista a sua candidatura com Azure AD, está a criar uma configuração de identidade para a sua aplicação que lhe permite integrar-se com a Azure AD. Quando regista uma aplicação no [portal Azure,][AZURE-Portal]você escolhe se é um único inquilino (apenas acessível no seu inquilino) ou multi-inquilino (acessível em outros inquilinos) e pode configurar opcionalmente um URI redirecionador (para onde o token de acesso é enviado).

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="Screenshot do portal Azure Registrar um painel de aplicação":::

Quando tiver concluído o registo da aplicação, tem uma instância globalmente única da app (o objeto da aplicação) que vive dentro do seu inquilino ou diretório.  Você também tem um ID globalmente único para a sua aplicação (a app ou iD do cliente).  No portal, pode então adicionar segredos ou certificados e âmbitos para fazer a sua app funcionar, personalizar a marca da sua app no diálogo de inscrição, e muito mais.

Se registar uma aplicação no portal, um objeto de aplicação e um objeto principal de serviço são automaticamente criados no seu inquilino de casa.  Se registar/criar uma aplicação utilizando as APIs do Gráfico microsoft, criar o objeto principal de serviço é um passo separado.

## <a name="application-object"></a>Objeto de aplicação
Um pedido AD Azure é definido pelo seu único objeto de candidatura, que reside no inquilino Azure AD onde o pedido foi registado (conhecido como o inquilino "casa" do pedido).  Um objeto de aplicação é usado como um modelo ou planta para criar um ou mais objetos principais de serviço.  Um diretor de serviço é criado em todos os inquilinos onde o pedido é usado. Semelhante a uma classe na programação orientada a objetos, o objeto de aplicação tem algumas propriedades estáticas que são aplicadas a todos os principais de serviço criados (ou instâncias de aplicação).

O objeto da aplicação descreve três aspetos de uma aplicação: como o serviço pode emitir fichas para aceder à aplicação, recursos a que a aplicação pode ter de aceder e as ações que a aplicação pode tomar.

A lâmina **de registos** da App no [portal Azure][AZURE-Portal] é usada para listar e gerir os objetos de aplicação no seu inquilino de casa.

![Lâmina de registos de aplicativos](./media/app-objects-and-service-principals/app-registrations-blade.png)

A entidade microsoft Graph [Application][MS-Graph-App-Entity] define o esquema para as propriedades de um objeto de aplicação.

## <a name="service-principal-object"></a>Objeto principal de serviço
Para aceder aos recursos que são assegurados por um inquilino da Azure AD, a entidade que necessita de acesso deve ser representada por um diretor de segurança. Este requisito é válido tanto para os utilizadores (principal utilizador) como para as aplicações (principal serviço). O diretor de segurança define a política de acesso e permissões para o utilizador/aplicação no inquilino AZure AD. Isto permite funcionalidades fundamentais, como a autenticação do utilizador/aplicação durante a entrada e autorização durante o acesso ao recurso.

Um diretor de serviço é a representação local, ou instância de aplicação, de um objeto de aplicação global em um único inquilino ou diretório. Um principal de serviço é um caso concreto criado a partir do objeto de aplicação e herda certas propriedades a partir desse objeto de aplicação.  Um diretor de serviço é criado em cada inquilino onde a aplicação é usada e faz referência ao objeto de aplicação globalmente único.  O objeto principal do serviço define o que a app pode realmente fazer no inquilino específico, que pode aceder à app, e quais os recursos a que a app pode aceder.

Quando um pedido é autorizado a aceder a recursos num inquilino (mediante registo ou [consentimento),](developer-glossary.md#consent)é criado um objeto principal de serviço. Também pode criar o principal objeto de serviço num inquilino utilizando [Azure PowerShell](howto-authenticate-service-principal-powershell.md), [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli) [Microsoft Graph,](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http)o [portal Azure][AZURE-Portal]e outras ferramentas.  Ao utilizar o portal, é criado automaticamente um resmounte de serviço quando regista uma aplicação.

A lâmina **de aplicações da Enterprise** no portal é usada para listar e gerir os principais serviços num inquilino. Pode ver as permissões do titular do serviço, permissões consentidas pelo utilizador, que os utilizadores fizeram esse consentimento, assinar informações e muito mais.

![Lâmina de aplicativos da empresa](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

A entidade Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] define o esquema para as propriedades de um objeto principal de serviço.

## <a name="relationship-between-application-objects-and-service-principals"></a>Relação entre objetos de aplicação e principais de serviço

O objeto de aplicação é a representação *global* do seu pedido de uso em todos os inquilinos, e o diretor de serviço é a representação *local* para uso em um inquilino específico.

O objeto da aplicação serve como o modelo a partir do qual as propriedades comuns e predefinidas são *derivadas* para utilização na criação de objetos correspondentes do principal de serviço. Um objeto de aplicação tem, portanto, uma relação 1:1 com a aplicação de software, e uma relação de 1:muitos com o seu(s) objeto principal de serviço correspondente.

Deve ser criado um resmedificado de serviço em cada arrendatário em que o pedido seja utilizado, permitindo-lhe estabelecer uma identidade para a inscrição e/ou acesso aos recursos que o arrendatário assegura. Uma aplicação de inquilino único tem apenas um principal de serviço (no inquilino principal), criado e com permissão para utilização durante o registo da aplicação. Uma aplicação Web/API multi-arrendatário também tem um principal serviço criado em cada inquilino onde um utilizador desse inquilino consentiu a sua utilização.

> [!NOTE]
> Quaisquer alterações que faça ao seu objeto de candidatura, também se refletem no seu objeto principal de serviço apenas no arrendatário do pedido (o inquilino onde foi registado). No caso de aplicações multi-arrendatários, as alterações ao objeto de aplicação não se refletem em objetos principais de serviço dos inquilinos do consumidor, até que o acesso seja removido através do Painel de Acesso à [Aplicação](https://myapps.microsoft.com) e concedido novamente.
>
> Note também que as aplicações nativas são registadas como multi-inquilino por padrão.

## <a name="example"></a>Exemplo

O diagrama que se segue ilustra a relação entre o objeto de aplicação de uma aplicação e os objetos principais de serviço correspondentes, no contexto de uma aplicação multi-inquilino de amostra chamada **app HR**. Há três inquilinos da AD AZure neste cenário de exemplo:

- **Adatum** - O inquilino usado pela empresa que desenvolveu a **app HR**
- **Contoso** - O inquilino usado pela organização Contoso, que é um consumidor da **app de RH**
- **Fabrikam** - O inquilino usado pela organização Fabrikam, que também consome a **app HR**

![Relação entre objeto de aplicação e objeto principal de serviço](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Neste cenário de exemplo:

| Passo | Descrição |
|------|-------------|
| 1    | É o processo de criação dos objetos principais de aplicação e serviço no inquilino da aplicação. |
| 2    | Quando os administradores da Contoso e da Fabrikam consentem completamente, um objeto principal de serviço é criado no inquilino AZure AD da sua empresa e atribuiu as permissões que o administrador concedeu. Note também que a aplicação HR poderia ser configurada/projetada para permitir o consentimento dos utilizadores para uso individual. |
| 3    | Os inquilinos de consumo da aplicação de RH (Contoso e Fabrikam) têm cada um o seu próprio objeto principal de serviço. Cada um representa a sua utilização de um caso do pedido em tempo de execução, regido pelas permissões consentidas pelo respetivo administrador. |

## <a name="next-steps"></a>Passos seguintes

- Pode utilizar o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) para consultar os objetos principais da aplicação e do serviço.
- Pode aceder ao objeto de aplicação de uma aplicação utilizando a Microsoft Graph API, o editor manifesto de aplicação [do portal Azure,][AZURE-Portal] ou [cmdlets Azure AD PowerShell](/powershell/azure/), representado pela sua entidade de [aplicação][MS-Graph-App-Entity]OData .
- Pode aceder ao objeto principal de serviço de uma aplicação através dos cmdlets API ou [Azure AD PowerShell](/powershell/azure/), representados pela sua entidade OData [ServicePrincipal][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
