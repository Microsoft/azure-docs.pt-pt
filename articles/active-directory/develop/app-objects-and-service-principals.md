---
title: Objetos do principal de serviço e aplicação no Azure Active Directory
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a relação entre objetos de aplicativo e entidade de serviço no Azure Active Directory.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03054f328513c7356b02d296076c211cc1c3865e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844588"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos do principal de serviço e aplicação no Azure Active Directory

Às vezes, o significado do termo "aplicativo" pode ser mal compreendido quando usado no contexto de Azure Active Directory (AD do Azure). Este artigo esclarece os aspectos conceituais e concretos da integração de aplicativos do Azure AD, com uma ilustração de registro e consentimento para um [aplicativo multilocatário](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Visão geral

Um aplicativo que foi integrado ao Azure AD tem implicações que vão além do aspecto do software. "Aplicativo" é frequentemente usado como um termo conceitual, referindo-se não apenas ao software do aplicativo, mas também ao seu registro e função do Azure AD em "conversas" de autenticação/autorização em tempo de execução.

Por definição, um aplicativo pode funcionar nessas funções:

- Função de [cliente](developer-glossary.md#client-application) (consumindo um recurso)
- Função de [servidor de recursos](developer-glossary.md#resource-server) (expondo APIs para clientes)
- Função de cliente e função de servidor de recursos

Um [fluxo de concessão de autorização do OAuth 2,0](developer-glossary.md#authorization-grant) define o protocolo de conversa, que permite que o cliente/recurso acesse/proteja os dados de um recurso, respectivamente.

Nas seções a seguir, você verá como o modelo de aplicativo do Azure AD representa um aplicativo em tempo de design e em tempo de execução.

## <a name="application-registration"></a>Registo da aplicação

Quando você registra um aplicativo do Azure AD na [portal do Azure][AZURE-Portal], dois objetos são criados em seu locatário do Azure AD:

- Um objeto de aplicativo e
- Um objeto de entidade de serviço

### <a name="application-object"></a>Objeto Application

Um aplicativo do Azure AD é definido por seu único objeto de aplicativo, que reside no locatário do Azure AD em que o aplicativo foi registrado, conhecido como o locatário "inicial" do aplicativo. A [entidade de aplicativo][MS-Graph-App-Entity] Microsoft Graph define o esquema para as propriedades de um objeto de aplicativo.

### <a name="service-principal-object"></a>objeto de entidade de serviço

Para acessar recursos que são protegidos por um locatário do Azure AD, a entidade que requer acesso deve ser representada por uma entidade de segurança. Isso é verdadeiro para usuários (entidade de usuário) e aplicativos (entidade de serviço).

A entidade de segurança define a política de acesso e as permissões para o usuário/aplicativo no locatário do Azure AD. Isso habilita os principais recursos, como a autenticação do usuário/aplicativo durante a entrada, e a autorização durante o acesso aos recursos.

Quando um aplicativo recebe permissão para acessar recursos em um locatário (após o registro ou [consentimento](developer-glossary.md#consent)), um objeto de entidade de serviço é criado. A [entidade Microsoft Graph servicePrincipalName][MS-Graph-Sp-Entity] define o esquema para as propriedades de um objeto de entidade de serviço.

### <a name="application-and-service-principal-relationship"></a>Relação do principal de serviço e aplicação

Considere o objeto Application como a representação *global* do seu aplicativo para uso em todos os locatários e a entidade de serviço como a representação *local* para uso em um locatário específico.

O objeto da aplicação serve como o modelo a partir do qual as propriedades comuns e predefinidas são *derivadas* para utilização na criação de objetos correspondentes do principal de serviço. Um objeto de aplicativo, portanto, tem uma relação 1:1 com o aplicativo de software e um número de relações 1: muitos com seus objetos de entidade de serviço correspondentes.

Uma entidade de serviço deve ser criada em cada locatário em que o aplicativo é usado, permitindo que ele estabeleça uma identidade para entrada e/ou acesso aos recursos que estão sendo protegidos pelo locatário. Uma aplicação de inquilino único tem apenas um principal de serviço (no inquilino principal), criado e com permissão para utilização durante o registo da aplicação. Um aplicativo Web multilocatário/API também tem uma entidade de serviço criada em cada locatário em que um usuário desse locatário consentiu em seu uso.

> [!NOTE]
> As alterações feitas em seu objeto de aplicativo também são refletidas em seu objeto de entidade de serviço somente no locatário inicial do aplicativo (o locatário onde ele foi registrado). Para aplicativos multilocatários, as alterações no objeto de aplicativo não são refletidas em objetos de entidade de serviço de locatários de consumidor, até que o acesso seja removido por meio do [painel de acesso do aplicativo](https://myapps.microsoft.com) e concedido novamente.
>
> Observe também que os aplicativos nativos são registrados como multilocatário por padrão.

## <a name="example"></a>Exemplo

O diagrama a seguir ilustra a relação entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes, no contexto de um aplicativo de multilocatário de exemplo chamado **aplicativo de RH**. Há três locatários do Azure AD neste cenário de exemplo:

- **Adatum** -o locatário usado pela empresa que desenvolveu o **aplicativo de RH**
- **Contoso** -o locatário usado pela organização Contoso, que é um consumidor do aplicativo de **RH**
- **Fabrikam** -o locatário usado pela organização Fabrikam, que também consome o aplicativo de **RH**

![Relação entre objeto de aplicativo e objeto de entidade de serviço](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Neste cenário de exemplo:

| Passo | Descrição |
|------|-------------|
| 1    | É o processo de criar os objetos de aplicativo e entidade de serviço no locatário inicial do aplicativo. |
| 2    | Quando os administradores da Contoso e da Fabrikam concluírem o consentimento, um objeto de entidade de serviço será criado no locatário do Azure AD da empresa e receberá as permissões concedidas pelo administrador. Observe também que o aplicativo de RH pode ser configurado/projetado para permitir o consentimento pelos usuários para uso individual. |
| 3    | Os locatários do consumidor do aplicativo de RH (contoso e Fabrikam) têm seu próprio objeto de entidade de serviço. Cada um representa o uso de uma instância do aplicativo em tempo de execução, governado pelas permissões consentidas pelo respectivo administrador. |

## <a name="next-steps"></a>Passos seguintes

- Você pode usar o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) para consultar os objetos de aplicativo e entidade de serviço.
- Você pode acessar o objeto de aplicativo de um aplicativo usando a API Microsoft Graph, o editor de manifesto [do aplicativo portal do Azure][AZURE-Portal] ou os [cmdlets do PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado por sua [entidade de aplicativo][MS-Graph-App-Entity]OData.
- Você pode acessar o objeto de entidade de serviço de um aplicativo por meio da API do Microsoft Graph ou dos [cmdlets do PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), conforme representado por sua [entidade de UserEntity][MS-Graph-Sp-Entity]do OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
