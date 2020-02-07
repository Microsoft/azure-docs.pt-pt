---
title: Provisionamento de usuário de aplicativo SaaS automatizado no Azure AD | Microsoft Docs
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar continuamente as contas de usuário em vários aplicativos SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d061d830df9cef305982ff54e91a886d4406d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066086"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos com Azure Active Directory

No Azure Ative Directory (Azure AD), o fornecimento de **aplicações** de termo refere-se à criação automática de identidades e funções de utilizador nas aplicações cloud[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)a que os utilizadores precisam de acesso. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o fornecimento de um utilizador de AD Azure em aplicações como [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)e muito mais.

![Diagrama de visão geral de provisionamento](./media/user-provisioning/provisioning-overview.png)

Esse recurso permite que você:

- **Fornecimento**automático : Crie automaticamente novas contas nos sistemas certos para novas pessoas quando se juntarem à sua equipa ou organização.
- **Automatizar a desprovisionamento:** Desativar automaticamente as contas nos sistemas certos quando as pessoas saem da equipa ou da organização.
- **Sincronizar dados entre sistemas:** Certifique-se de que as identidades nas suas apps e sistemas são mantidas atualizadas com base em alterações no diretório ou no seu sistema de recursos humanos.
- **Grupos de disposição:** Grupos de provisão para aplicações que os apoiem.
- **Governar o acesso:** Monitore e audite quem foi aprovisionado nas suas candidaturas.
- **Implantação perfeita em cenários de campo castanho:** Combine as identidades existentes entre sistemas e permita uma fácil integração, mesmo quando os utilizadores já existem no sistema alvo.
- **Use personalização rica:** Aproveite os mapeamentos de atributos personalizáveis que definem quais os dados do utilizador que devem fluir do sistema de origem para o sistema alvo.
- **Receba alertas para eventos críticos:** O serviço de provisionamento fornece alertas para eventos críticos, e permite a integração do Log Analytics onde pode definir alertas personalizados para atender às suas necessidades de negócio.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

Como o número de aplicativos usados em organizações modernas continua crescendo, os administradores de ti são tarefa com gerenciamento de acesso em escala. Padrões como SAML (Security Asserties Markup Language) ou OIDC (Open ID Connect) permitem que os administradores configurem rapidamente o SSO (logon único), mas o acesso também exige que os usuários sejam provisionados no aplicativo. Para muitos administradores, o provisionamento significa criar manualmente cada conta de usuário ou carregar arquivos CSV a cada semana, mas esses processos são demorados, caros e sujeitos a erros. Soluções como JIT (just-in-time) do SAML foram adotadas para automatizar o provisionamento, mas as empresas também precisam de uma solução para desprovisionar usuários quando deixam a organização ou não precisam mais de acesso a determinados aplicativos com base na alteração de função.

Algumas motivações comuns para usar o provisionamento automático incluem:

- Maximizar a eficiência e a precisão dos processos de provisionamento.
- Economizando os custos associados à hospedagem e manutenção de soluções e scripts de provisionamento desenvolvidos com personalização.
- Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles deixam a organização.
- Importar facilmente um grande número de usuários para um sistema ou aplicativo SaaS específico.
- Ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar em um aplicativo.

O provisionamento de usuários do Azure AD pode ajudar a resolver esses desafios. Para saber mais sobre como os clientes têm usado o fornecimento de utilizadores da Azure AD, pode ler o estudo de [caso ASOS](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do provisionamento de usuário no Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento automático de usuário do Azure AD?

A Azure AD dispõe de suporte pré-integrado para muitas aplicações populares do SaaS e sistemas de recursos humanos, e suporte genérico para apps que implementam partes específicas da [norma SCIM 2.0.](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)

* **Aplicações pré-integradas (apps galeria SaaS)** . Pode encontrar todas as aplicações para as quais a Azure AD suporta um conector de provisionamento pré-integrado na [lista de tutoriais de aplicações para o fornecimento](../saas-apps/tutorial-list.md)de utilizadores. Os aplicativos previamente integrados listados na Galeria geralmente usam APIs de gerenciamento de usuário com base em SCIM 2,0 para provisionamento. 

   ![Logotipo do Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se quiser solicitar um novo pedido de provisionamento, pode solicitar que a sua aplicação seja integrada na nossa galeria de [aplicações.](../develop/howto-app-gallery-listing.md) Para uma solicitação de provisionamento de usuário, exigimos que o aplicativo tenha um ponto de extremidade compatível com SCIM. Solicite que o fornecedor do aplicativo siga o padrão SCIM para que possamos integrar o aplicativo à nossa plataforma rapidamente.

* **Aplicações que suportam o SCIM 2.0**. Para obter informações sobre como ligar genericamente as aplicações que implementam APIs de gestão de utilizadores baseados no SCIM 2.0, consulte [Construir um ponto final sCIM e configurar o fornecimento de utilizadores](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é o Sistema de Gestão de Identidade de Domínio Transversal (SCIM)?

Para ajudar a automatizar o provisionamento e o desprovisionamento, os aplicativos expõem APIs de usuário e de grupo proprietárias. No entanto, qualquer pessoa que tentasse gerenciar usuários em mais de um aplicativo informará que cada aplicativo tenta executar as mesmas ações simples, como criar ou atualizar usuários, adicionar usuários a grupos ou desprovisionar usuários. Ainda assim, todas essas ações simples são implementadas de forma um pouco diferente, usando diferentes caminhos de ponto de extremidade, métodos diferentes para especificar informações de usuário e um esquema diferente para representar cada elemento de informações.

Para resolver esses desafios, a especificação SCIM fornece um esquema de usuário comum para ajudar os usuários a migrar, de e para os aplicativos. O SCIM está se tornando o padrão de fato para o provisionamento e, quando usado em conjunto com os padrões de Federação como SAML ou OpenID Connect, fornece aos administradores uma solução baseada em padrões de ponta a ponta para o gerenciamento de acesso.

Para obter orientações pormenorizadas sobre a utilização do SCIM para automatizar o fornecimento e o deprovisionamento de utilizadores e grupos a uma aplicação, consulte [construir um ponto final sCIM e configurar o fornecimento](use-scim-to-provision-users-and-groups.md)de utilizadores.

## <a name="manual-vs-automatic-provisioning"></a>Aprovisionamento manual vs. automático

Os aplicativos na galeria do Azure AD dão suporte a um dos dois modos de provisionamento:

* **O** fornecimento manual significa que ainda não existe um conector de fornecimento automático de AD Azure para a aplicação. As contas de usuário devem ser criadas manualmente, por exemplo, adicionando usuários diretamente no portal administrativo do aplicativo ou carregando uma planilha com detalhes da conta de usuário. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

* **Automática** significa que foi desenvolvido um conector de fornecimento de AD Azure para esta aplicação. Você deve seguir o tutorial de instalação específico para configurar o provisionamento para o aplicativo. Os tutoriais de aplicações podem ser encontrados na [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](../saas-apps/tutorial-list.md).

Na galeria Azure AD, as aplicações que suportam o fornecimento automático são designadas por um ícone de **provisionamento.** Mude para a nova experiência de pré-visualização da galeria para ver estes ícones (no banner no topo da página Adicionar uma página de **aplicação**, selecione o link que diz Clique aqui para experimentar a nova e melhorada galeria de **aplicações).**

![Ícone de provisionamento na Galeria de aplicativos](./media/user-provisioning/browse-gallery.png)

O modo de provisionamento suportado por uma aplicação também é visível no separador **Provisioning** uma vez adicionado a aplicação às suas **aplicações Enterprise**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como fazer configurar o provisionamento automático para um aplicativo?

Para aplicativos previamente integrados listados na Galeria, a orientação passo a passo está disponível para configurar o provisionamento automático. Consulte a [lista de tutoriais para aplicações de galerias integradas.](../saas-apps/tutorial-list.md) O vídeo a seguir demonstra como configurar o provisionamento automático de usuário para o SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outras aplicações que suportem o SCIM 2.0, siga os passos do artigo [Construa um ponto final sCIM e configure o fornecimento](use-scim-to-provision-users-and-groups.md)de utilizadores.


## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicações do SaaS](../saas-apps/tutorial-list.md)
- [Personalização de mapeamentos de atributos para o fornecimento de utilizadores](customize-application-attributes.md)
- [Expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtros de deteção para o fornecimento do utilizador](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Construir um ponto final SCIM e configurar o fornecimento de utilizadores](use-scim-to-provision-users-and-groups.md)
- [Visão geral da API de sincronização da AD Azure](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
