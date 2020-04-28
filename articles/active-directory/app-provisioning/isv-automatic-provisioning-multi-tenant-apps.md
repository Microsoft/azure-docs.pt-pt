---
title: Ativar o fornecimento automático de utilizadores para aplicações multi-arrendatárias - Azure AD
description: Um guia para fornecedores de software independentes para permitir o provisionamento automatizado
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522398"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Ativar o fornecimento automático de utilizadores para a sua aplicação multi-inquilino

O fornecimento automático de utilizadores é o processo de automatização da criação, manutenção e remoção de identidades de utilizadores em sistemas-alvo como as suas aplicações de software como serviço.

## <a name="why-enable-automatic-user-provisioning"></a>Porquê ativar o fornecimento automático de utilizadores?

As aplicações que exigem que um registo de utilizador esteja presente na aplicação antes do primeiro sinal de um utilizador exigir o fornecimento do utilizador. Existem benefícios para si como prestador de serviços e benefícios para os seus clientes.

### <a name="benefits-to-you-as-the-service-provider"></a>Benefícios para si como prestador de serviços

* Aumente a segurança da sua aplicação utilizando a plataforma de identidade da Microsoft.

* Reduza o esforço real e percebido do cliente para adotar a sua aplicação.

* Reduza os seus custos na integração com vários fornecedores de identidade (IDPs) para o fornecimento automático de utilizadores utilizando o fornecimento baseado no Sistema de Gestão de Identidade de DomínioS Cruzados (SCIM).

* Reduza os custos de suporte fornecendo registos ricos para ajudar os clientes a resolver problemas nos problemas de fornecimento de utilizadores.

* Aumente a visibilidade da sua aplicação na galeria de [aplicações Azure AD.](https://azuremarketplace.microsoft.com/marketplace/apps)

* Obtenha uma listagem prioritária na página de Tutoriais de Aplicações.

### <a name="benefits-to-your-customers"></a>Benefícios para os seus clientes

* Aumente a segurança removendo automaticamente o acesso à sua aplicação para utilizadores que alterem funções ou deixem a organização para a sua aplicação.

* Simplificar a gestão do utilizador para a sua aplicação evitando erros humanos e trabalho repetitivo associado ao fornecimento manual.

* Reduzir os custos de hospedagem e manutenção de soluções de provisionamento personalizadas.

## <a name="choose-a-provisioning-method"></a>Escolher um método de aprovisionamento

A Azure AD fornece vários caminhos de integração para permitir o fornecimento automático de utilizadores para a sua aplicação.

* O Serviço de [Provisionamento de AD Azure](../app-provisioning/user-provisioning.md) gere o fornecimento e o desprovisionamento dos utilizadores da Azure AD à sua aplicação (prestação de saída) e da sua aplicação à Azure AD (fornecimento de entrada). O serviço liga-se ao Sistema de Gestão de Identidade de Domínio Transversal (SCIM) pontos finais de gestão de utilizadores fornecidos pela sua aplicação.

* Ao utilizar o [Microsoft Graph,](https://docs.microsoft.com/graph/)a sua aplicação gere o fornecimento de entrada e saída de utilizadores e grupos de AD Azure para a sua aplicação consultando a API do Microsoft Graph.

* O fornecimento de utilizador de marcação de afirmação de segurança apenas no tempo (SAML JIT) pode ser ativado se a sua aplicação estiver a utilizar o SAML para a federação. Utiliza informações de reclamações enviadas no token SAML para os utilizadores de fornecimento.

Para ajudar a determinar que opção de integração usar para a sua aplicação, consulte a tabela de comparação de alto nível e, em seguida, consulte as informações mais detalhadas sobre cada opção.

| Capacidades ativadas ou melhoradas por Fornecimento Automático| Serviço de Provisionamento AD Azure (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Gestão de utilizadores e grupos em Azure AD| √| √| Apenas para o utilizador |
| Gerir utilizadores e grupos sincronizados a partir do diretório ativo no local| √*| √*| Apenas utilizador* |
| Aceda a dados para além de utilizadores e grupos durante o fornecimento de acesso aos dados O365 (Equipas, SharePoint, Email, Calendário, Documentos, etc.)| X+| √| X |
| Criar, ler e atualizar utilizadores com base nas regras do negócio| √| √| √ |
| Eliminar utilizadores com base nas regras empresariais| √| √| X |
| Gerir o fornecimento automático de utilizadores para todas as aplicações do portal Azure| √| X| √ |
| Apoiar vários fornecedores de identidade| √| X| √ |
| Contas de hóspedes de suporte (B2B)| √| √| √ |
| Apoiar contas não empresariais (B2C)| X| √| √ |

<sup>*</sup>– A configuração Azure AD Connect é necessária para sincronizar os utilizadores de AD para Azure AD.  
<sup>+</sup >– A utilização do SCIM para o fornecimento não o impede de integrar a sua aplicação com o MIcrosoft Graph para outros fins.

## <a name="azure-ad-provisioning-service-scim"></a>Serviço de Provisionamento De AD Azure (SCIM)

Os serviços de provisionamento da Azure AD utilizam o [SCIM,](https://aka.ms/SCIMOverview)um padrão do setor para o fornecimento suportado por muitos fornecedores de identidade (IDPs), bem como aplicações (por exemplo, Slack, G Suite, Dropbox). Recomendamos que utilize o serviço de provisionamento Azure AD se pretender suportar IDPs para além do Azure AD, uma vez que qualquer IdP compatível com o SCIM pode ligar-se ao seu ponto final sCIM. Construindo um ponto final simples /Utilizador, pode ativar o fornecimento sem ter de manter o seu próprio motor de sincronização. 

Para obter mais informações sobre como os utilizadores do Serviço de Provisionamento De AD Azure SCIM, consulte: 

* [Saiba mais sobre o padrão SCIM](https://aka.ms/SCIMOverview)

* [Utilização do Sistema de Gestão de Identidade de Domínio Transversal (SCIM) para fornecer automaticamente utilizadores e grupos do Diretório Ativo do Azure às aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Compreender a implementação do Azure AD SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph for Provisioning

Quando utiliza o Microsoft Graph para o fornecimento, tem acesso a todos os dados de utilizadores ricos disponíveis no Graph. Além dos detalhes dos utilizadores e grupos, também pode obter informações adicionais, como as funções do utilizador, relatórios de gestor e diretos, dispositivos de propriedade e registo, e centenas de outras peças de dados disponíveis no [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Mais de 15 milhões de organizações, e 90% da fortuna 500 empresas usam AD Azure enquanto subscrevem serviços na nuvem da Microsoft como office 365, Microsoft Azure, Enterprise Mobility Suite ou Microsoft 365. Pode utilizar o Microsoft Graph para integrar a sua aplicação com fluxos de trabalho administrativos, tais como embarque (e rescisão de funcionários), manutenção de perfis e muito mais. 

Saiba mais sobre a utilização do Microsoft Graph para o fornecimento:

* [Página inicial do Microsoft Graph](https://developer.microsoft.com/graph)

* [Descrição Geral do Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Visão geral do Microsoft Graph Auth](https://docs.microsoft.com/graph/auth/)

* [Começar com o Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Utilização do SAML JIT para o fornecimento

Se pretender fornecer aos utilizadores apenas após o primeiro início iniciar sessão na sua aplicação, e não precisar de desprovisionar automaticamente os utilizadores, o SAML JIT é uma opção. A sua aplicação deve apoiar o SAML 2.0 como protocolo da federação para utilizar o SAML JIT.

A SAML JIT utiliza as informações de reclamações no token SAML para criar e atualizar informações dos utilizadores na aplicação. Os clientes podem configurar estes créditos necessários na aplicação Azure AD conforme necessário. Por vezes, o fornecimento de JIT tem de ser ativado a partir do lado da aplicação para que o cliente possa utilizar esta funcionalidade. O SAML JIT é útil para criar e atualizar os utilizadores, mas não pode excluir ou desativar os utilizadores na aplicação.

## <a name="next-steps"></a>Passos Seguintes

* [Ativar o Single Sign-on para a sua aplicação](../manage-apps/isv-sso-content.md)

* [Envie a sua listagem](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) de aplicações e parceiro com a Microsoft para criar documentação no site da Microsoft.

* [Junte-se à Microsoft Partner Network (grátis) e crie o seu plano de mercado.](https://partner.microsoft.com/en-us/explore/commercial)
