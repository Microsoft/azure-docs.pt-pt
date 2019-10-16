---
title: Habilitar o provisionamento automático de usuário para Azure Active Directory aplicativos multilocatários
description: Um guia para fornecedores independentes de software para habilitar o provisionamento automatizado
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: c44006b20f4c0ef186f406e554ff555cda0c1dd8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373393"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Habilitar o provisionamento automático de usuário para seu aplicativo multilocatário

O provisionamento automático de usuário é o processo de automatizar a criação, a manutenção e a remoção de identidades de usuário em sistemas de destino, como seus aplicativos de software como serviço.

## <a name="why-enable-automatic-user-provisioning"></a>Por que habilitar o provisionamento automático de usuário?

Aplicativos que exigem que um registro de usuário esteja presente no aplicativo antes que a primeira conexão de um usuário exija o provisionamento do usuário. Há benefícios para você como um provedor de serviços e os benefícios para seus clientes.

### <a name="benefits-to-you-as-the-service-provider"></a>Benefícios para você como o provedor de serviços

* Aumente a segurança do seu aplicativo usando a plataforma de identidade da Microsoft.

* Reduza o esforço real e percebido do cliente para adotar seu aplicativo.

* Reduza os custos na integração com vários provedores de identidade (IdPs) para o provisionamento automático de usuário usando o sistema para o provisionamento baseado em SCIM (gerenciamento de identidade entre domínios).

* Reduza os custos de suporte fornecendo logs avançados para ajudar os clientes a solucionar problemas de provisionamento do usuário.

* Aumente a visibilidade de seu aplicativo na [Galeria de aplicativos do Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps).

* Obtenha uma listagem priorizada na página de tutoriais do aplicativo.

### <a name="benefits-to-your-customers"></a>Benefícios para seus clientes

* Aumente a segurança removendo automaticamente o acesso ao seu aplicativo para usuários que alteram funções ou deixam a organização em seu aplicativo.

* Simplifique o gerenciamento de usuários para seu aplicativo, evitando o erro humano e o trabalho repetitivo associado ao provisionamento manual.

* Reduza os custos de hospedagem e manutenção de soluções de provisionamento personalizadas.

## <a name="choose-a-provisioning-method"></a>Escolher um método de provisionamento

O Azure AD fornece vários caminhos de integração para habilitar o provisionamento automático de usuário para seu aplicativo.

* O [serviço de provisionamento do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) gerencia o provisionamento e o desprovisionamento de usuários do Azure ad para seu aplicativo (provisionamento de saída) e de seu aplicativo para o Azure AD (provisionamento de entrada). O serviço se conecta ao sistema para pontos de extremidade de API de gerenciamento de usuário SCIM (gerenciamento de identidade de domínio cruzado) fornecidos pelo seu aplicativo.

* Ao usar o [Microsoft Graph](https://docs.microsoft.com/graph/), seu aplicativo gerencia o provisionamento de entrada e saída de usuários e grupos do Azure ad para seu aplicativo consultando a API de Microsoft Graph.

* O provisionamento de usuário do Security Assertion Markup Language just in time (SAML JIT) poderá ser habilitado se seu aplicativo estiver usando o SAML para Federação. Ele usa informações de declarações enviadas no token SAML para provisionar usuários.

Para ajudar a determinar qual opção de integração usar para seu aplicativo, consulte a tabela de comparação de alto nível e, em seguida, veja as informações mais detalhadas sobre cada opção.

| Recursos habilitados ou aprimorados pelo provisionamento automático| Serviço de provisionamento do Azure AD (SCIM 2,0)| API de Microsoft Graph (OData v 4.0)| JIT DO SAML |
|---|---|---|---|
| Gerenciamento de usuários e grupos no Azure AD| √| √| Somente usuário |
| Gerenciar usuários e grupos sincronizados do Active Directory local| √| √| Somente usuário * |
| Acessar dados além de usuários e grupos durante o provisionamento de acesso a dados do O365 (equipes, SharePoint, email, calendário, documentos etc.)| X +| √| X |
| Criar, ler e atualizar usuários com base em regras de negócio| √| √| √ |
| Excluir usuários com base em regras de negócio| √| √| X |
| Gerenciar o provisionamento automático de usuário para todos os aplicativos do portal do Azure| √| X| √ |
| Suporte a vários provedores de identidade| √| X| √ |
| Suporte para contas de convidado (B2B)| √| √| √ |
| Suporte a contas não empresariais (B2C)| X| √| √ |

<sup>*</sup> – Azure ad Connect configuração é necessária para sincronizar usuários do AD para o Azure AD.  
<sup>+</sup >– usar o scim para provisionamento não impede que você faça a integração do seu aplicativo com o Microsoft Graph para outros fins.

## <a name="azure-ad-provisioning-service-scim"></a>Serviço de provisionamento do AD do Azure (SCIM)

Os serviços de provisionamento do Azure AD usam o [SCIM] (https://aka.ms/SCIMOverview, um protocolo com suporte de vários provedores de identidade (IdPs). Recomendamos que você use o serviço de provisionamento do Azure AD se quiser dar suporte ao IdPs além do Azure AD, já que qualquer IdP compatível com SCIM pode se conectar ao seu ponto de extremidade SCIM.

Para obter mais informações sobre como os usuários do serviço de provisionamento do Azure AD SCIMm, consulte: 

* [Saiba mais sobre o SCIM Standard](https://aka.ms/SCIMOverview)

* [Usando o sistema para o SCIM (gerenciamento de identidades entre domínios) para provisionar automaticamente usuários e grupos de Azure Active Directory para aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Entender a implementação de SCIM do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph para provisionamento

Ao usar Microsoft Graph para provisionamento, você tem acesso a todos os dados avançados do usuário disponíveis no Graph. Além dos detalhes de usuários e grupos, você também pode buscar informações adicionais, como funções do usuário, gerentes e relatórios diretos, dispositivos registrados e de propriedade e centenas de outras partes de dados disponíveis no [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Mais de 15 milhões organizações e 90% das empresas da Fortune 500 usam o Azure AD ao assinar serviços em nuvem da Microsoft, como o Office 365, Microsoft Azure, Enterprise Mobility Suite ou Microsoft 365. Você pode usar Microsoft Graph para integrar seu aplicativo com fluxos de trabalho administrativos, como a integração de funcionários (e encerramento), a manutenção do perfil e muito mais. 

Saiba mais sobre como usar Microsoft Graph para provisionamento:

* [Página inicial do Microsoft Graph](https://developer.microsoft.com/graph)

* [Descrição Geral do Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Visão geral da autenticação Microsoft Graph](https://docs.microsoft.com/graph/auth/)

* [Introdução ao Microsoft Graph](https://developer.microsoft.com/graph/get-started)

* [Usar a API de Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)

* [Microsoft Graph Explorer](https://aka.ms/ge) 

* [Referência de permissões de Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference)

* [Práticas recomendadas para trabalhar com Microsoft Graph](https://docs.microsoft.com/graph/best-practices-concept)

* [Cenários em destaque para Microsoft Graph](https://developer.microsoft.com/graph/examples)

## <a name="using-saml-jit-for-provisioning"></a>Usando o JIT do SAML para provisionamento

Se você quiser provisionar usuários somente na primeira entrada em seu aplicativo e não precisar desprovisionar automaticamente os usuários, o SAML JIT é uma opção. Seu aplicativo deve dar suporte ao SAML 2,0 como um protocolo de Federação para usar o SAML JIT.

O SAML JIT usa as informações de declarações no token SAML para criar e atualizar as informações do usuário no aplicativo. Os clientes podem configurar essas declarações necessárias no aplicativo do Azure AD, conforme necessário. Às vezes, o provisionamento JIT precisa ser habilitado no lado do aplicativo para que o cliente possa usar esse recurso. O JIT do SAML é útil para criar e atualizar usuários, mas não pode excluir ou desativar os usuários no aplicativo.

## <a name="next-steps"></a>Próximos Passos

* [Habilitar logon único para seu aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-sso-content)

* [Envie sua listagem de aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e parceiro com a Microsoft para criar a documentação no site da Microsoft.

* [Junte-se ao Microsoft Partner Network (gratuito) e crie seu plano ir para o mercado](https://partner.microsoft.com/en-us/explore/commercial).
