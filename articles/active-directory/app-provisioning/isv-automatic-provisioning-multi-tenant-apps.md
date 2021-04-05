---
title: Permitir o provisionamento automático do utilizador para aplicações multi-inquilinos - Azure AD
description: Um guia para fornecedores de software independentes para permitir o provisionamento automatizado
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255700"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Ativar o fornecimento automático de utilizadores para a sua aplicação multi-inquilino

O fornecimento automático de utilizadores é o processo de automatização da criação, manutenção e remoção de identidades dos utilizadores em sistemas-alvo como as suas aplicações de software-as-a-service.

## <a name="why-enable-automatic-user-provisioning"></a>Porquê permitir o fornecimento automático do utilizador?

As aplicações que exigem que um registo de utilizador esteja presente na aplicação antes do primeiro sinal de um utilizador exigir o provisionamento do utilizador. Existem benefícios para si como prestador de serviços e benefícios para os seus clientes.

### <a name="benefits-to-you-as-the-service-provider"></a>Benefícios para si como prestador de serviços

* Aumente a segurança da sua aplicação utilizando a plataforma de identidade da Microsoft.

* Reduza o esforço real e percebido do cliente para adotar a sua aplicação.

* Reduza os seus custos na integração com vários fornecedores de identidade (IdPs) para o fornecimento automático de utilizadores utilizando o fornecimento baseado no Sistema de Gestão de Identidade de Domínio Cruzado (SCIM).

* Reduza os custos de suporte fornecendo registos ricos para ajudar os clientes a resolver problemas de atenção dos utilizadores.

* Aumente a visibilidade da sua aplicação na galeria de [aplicações AZure AD.](https://azuremarketplace.microsoft.com/marketplace/apps)

* Obtenha uma listagem prioritária na página de Tutoriais da Aplicação.

### <a name="benefits-to-your-customers"></a>Benefícios para os seus clientes

* Aumentar a segurança removendo automaticamente o acesso à sua aplicação para utilizadores que alterem funções ou deixem a organização para a sua aplicação.

* Simplifique a gestão do utilizador para a sua aplicação evitando erros humanos e trabalhos repetitivos associados ao fornecimento manual.

* Reduzir os custos de hospedagem e manutenção de soluções de avisão personalizadas.

## <a name="choose-a-provisioning-method"></a>Escolher um método de aprovisionamento

A Azure AD fornece vários caminhos de integração para permitir o fornecimento automático do utilizador para a sua aplicação.

* O [Serviço de Provisionamento AZure AD](../app-provisioning/user-provisioning.md) gere o fornecimento e desprovisionamento de utilizadores da Azure AD para a sua aplicação (provisionamento de saída) e da sua aplicação para Azure AD (provisionamento de entrada). O serviço conecta-se ao Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) pontos finais de gestão de IPC fornecidos pela sua aplicação.

* Ao utilizar o [Microsoft Graph,](/graph/)a sua aplicação gere o fornecimento de entrada e saída de utilizadores e grupos desde a Azure AD até à sua aplicação consultando a API do Microsoft Graph.

* O fornecimento de identificação do utilizador de marcação de afirmação de segurança justo no tempo (SAML JIT) pode ser ativado se a sua aplicação estiver a utilizar o SAML para federação. Utiliza informações de sinistros enviadas no token SAML aos utilizadores de provisão.

Para ajudar a determinar qual a opção de integração a utilizar para a sua aplicação, consulte a tabela de comparação de alto nível e, em seguida, consulte as informações mais detalhadas sobre cada opção.

| Capacidades ativadas ou reforçadas pelo Provisionamento Automático| Serviço de Prestação de Anúncios Azure (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Gestão de utilizadores e grupos em Azure AD| √| √| Apenas utilizador |
| Gerir utilizadores e grupos sincronizados a partir de ative directy no local| √*| √*| Apenas utilizador* |
| Aceder a dados para além dos utilizadores e grupos durante o fornecimento de dados de acesso ao Microsoft 365 (Equipas, SharePoint, Email, Calendário, Documentos, etc.)| X+| √| X |
| Criar, ler e atualizar utilizadores com base nas regras de negócio| √| √| √ |
| Excluir utilizadores com base em regras de negócio| √| √| X |
| Gerir o provisionamento automático do utilizador para todas as aplicações a partir do portal Azure| √| X| √ |
| Apoiar vários fornecedores de identidade| √| X| √ |
| Apoiar contas de hóspedes (B2B)| √| √| √ |
| Apoiar contas não empresariais (B2C)| X| √| √ |

<sup>*</sup> – A configuração AD Connect É necessária para sincronizar os utilizadores de AD a AZure AD.  
<sup>+</sup >– A utilização do SCIM para provisão não o impede de integrar a sua aplicação com o Microsoft Graph para outros fins.

## <a name="azure-ad-provisioning-service-scim"></a>Serviço de Prestação de Anúncios Azure (SCIM)

Os serviços de prestação de Azure AD utilizam [o SCIM,](https://aka.ms/SCIMOverview)uma norma da indústria para o provisionamento apoiado por muitos fornecedores de identidade (IdPs), bem como aplicações (por exemplo, Slack, G Suite, Dropbox). Recomendamos que utilize o serviço de fornecimento Azure AD se quiser apoiar os IDPs para além do Azure AD, uma vez que qualquer IdP compatível com SCIM pode ligar-se ao seu ponto final SCIM. Construindo um ponto final simples /Utilizador, pode ativar o provisionamento sem ter de manter o seu próprio motor de sincronização. 

Para obter mais informações sobre como os utilizadores do Serviço de Provisionamento AD Azure SCIM, consulte: 

* [Saiba mais sobre o padrão SCIM](https://aka.ms/SCIMOverview)

* [Utilizar o Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) para provisão automática de utilizadores e grupos do Azure Ative Directory para aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Compreender a implementação do Azure AD SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph for Provisioning

Quando utiliza o Microsoft Graph para provisões, tem acesso a todos os dados ricos do utilizador disponíveis no Gráfico. Além dos detalhes dos utilizadores e grupos, também pode obter informações adicionais como as funções do utilizador, gestor e relatórios diretos, dispositivos de propriedade e registados, e centenas de outras peças de dados disponíveis no [Microsoft Graph](/graph/api/overview). 

Mais de 15 milhões de organizações, e 90% da fortuna 500 empresas usam Azure AD enquanto subscrevem serviços na nuvem da Microsoft como Microsoft 365, Microsoft Azure ou Enterprise Mobility Suite. Pode utilizar o Microsoft Graph para integrar a sua aplicação com fluxos de trabalho administrativos, tais como embarque de funcionários (e rescisão), manutenção de perfis e muito mais. 

Saiba mais sobre a utilização do Microsoft Graph para provisões:

* [Página inicial do Microsoft Graph](https://developer.microsoft.com/graph)

* [Descrição Geral do Microsoft Graph](/graph/overview)

* [Visão geral do Microsoft Graph Auth](/graph/auth/)

* [Começar com o Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Utilização de SAML JIT para provisionamento

Se pretender que os utilizadores apenas após o primeiro sinal de entrada na sua aplicação, e não necessite de desprovisionar automaticamente os utilizadores, o SAML JIT é uma opção. A sua aplicação deve apoiar o SAML 2.0 como protocolo da federação para utilizar o SAML JIT.

A SAML JIT utiliza as informações de sinistros no token SAML para criar e atualizar informações dos utilizadores na aplicação. Os clientes podem configurar estas reclamações necessárias na aplicação AZure AD, se necessário. Por vezes, o provisionamento do JIT precisa de ser ativado a partir do lado da aplicação para que o cliente possa utilizar esta funcionalidade. O SAML JIT é útil para criar e atualizar utilizadores, mas não pode eliminar ou desativar os utilizadores na aplicação.

## <a name="next-steps"></a>Passos Seguintes

* [Ativar o Único S-On para a sua aplicação](../develop/v2-howto-app-gallery-listing.md)

* [Submeta a sua lista de aplicações](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e parceiro com a Microsoft para criar documentação no site da Microsoft.

* [Junte-se à Microsoft Partner Network (gratuita) e crie o seu plano de mercado.](https://partner.microsoft.com/explore/commercial)
