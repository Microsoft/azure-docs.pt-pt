---
title: Fornecimento automatizado de utilizadores de aplicações SaaS em Azure AD Microsoft Docs
description: Uma introdução à forma como pode utilizar o Azure AD para fornecer, desprovisionar e atualizar continuamente as contas de utilizadores em várias aplicações SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a24a557cb436f18252abd88a4c82f15004f4390
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522055"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizar o fornecimento e o desprovisionamento de utilizadores a aplicações com o Diretório Ativo da Azure

No Azure Ative Directory (Azure AD), o fornecimento de **aplicações** de termo refere-se à criação automática de identidades e funções de utilizador nas aplicações cloud[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)a que os utilizadores precisam de acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou funções. Os cenários comuns incluem o fornecimento de um utilizador de AD Azure em aplicações como [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)e muito mais.

![Diagrama de visão geral de provisionamento](./media/user-provisioning/provisioning-overview.png)

Esta funcionalidade permite-lhe:

- **Fornecimento**automático : Crie automaticamente novas contas nos sistemas certos para novas pessoas quando se juntarem à sua equipa ou organização.
- **Automatizar a desprovisionamento:** Desativar automaticamente as contas nos sistemas certos quando as pessoas saem da equipa ou da organização.
- **Sincronizar dados entre sistemas:** Certifique-se de que as identidades nas suas apps e sistemas são mantidas atualizadas com base em alterações no diretório ou no seu sistema de recursos humanos.
- **Grupos de disposição:** Grupos de provisão para aplicações que os apoiem.
- **Governar o acesso:** Monitore e audite quem foi aprovisionado nas suas candidaturas.
- **Implantação perfeita em cenários de campo castanho:** Combine as identidades existentes entre sistemas e permita uma fácil integração, mesmo quando os utilizadores já existem no sistema alvo.
- **Use personalização rica:** Aproveite os mapeamentos de atributos personalizáveis que definem quais os dados do utilizador que devem fluir do sistema de origem para o sistema alvo.
- **Receba alertas para eventos críticos:** O serviço de provisionamento fornece alertas para eventos críticos, e permite a integração do Log Analytics onde pode definir alertas personalizados para atender às suas necessidades de negócio.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

À medida que o número de aplicações utilizadas nas organizações modernas continua a crescer, os administradores de TI são incumbidos de gestão de acesso em escala. Padrões como a Linguagem de Marcação de Afirmações de Segurança (SAML) ou Open ID Connect (OIDC) permitem que os administradores criem rapidamente um único sinal (SSO), mas o acesso também requer que os utilizadores sejam aprovisionados na aplicação. Para muitos administradores, o provisionamento significa criar manualmente todas as contas de utilizador ou carregar ficheiros CSV todas as semanas, mas estes processos são demorados, dispendiosos e propensos a erros. Soluções como a SAML just-in-time (JIT) foram adotadas para automatizar o fornecimento, mas as empresas também precisam de uma solução para desabastecer os utilizadores quando saem da organização ou já não exigem o acesso a determinadas aplicações com base na mudança de papéis.

Algumas motivações comuns para a utilização do provisionamento automático incluem:

- Maximizar a eficiência e a precisão dos processos de provisionamento.
- Poupar nos custos associados ao hospedagem e manter soluções e scripts de provisionamento personalizados.
- Garantindo a sua organização removendo instantaneamente as identidades dos utilizadores das principais aplicações SaaS quando deixam a organização.
- Importando facilmente um grande número de utilizadores para uma determinada aplicação ou sistema SaaS.
- Ter um único conjunto de políticas para determinar quem é provisionado e quem pode iniciar sessão numa aplicação.

O fornecimento de utilizadores da AD Azure pode ajudar a enfrentar estes desafios. Para saber mais sobre como os clientes têm usado o fornecimento de utilizadores da Azure AD, pode ler o estudo de [caso ASOS](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do fornecimento de utilizadores em Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Que aplicações e sistemas posso utilizar com o fornecimento automático de utilizadores da Azure AD?

A Azure AD dispõe de suporte pré-integrado para muitas aplicações populares do SaaS e sistemas de recursos humanos, e suporte genérico para apps que implementam partes específicas da [norma SCIM 2.0.](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)

* **Aplicações pré-integradas (apps galeria SaaS)** . Pode encontrar todas as aplicações para as quais a Azure AD suporta um conector de provisionamento pré-integrado na [lista de tutoriais de aplicações para o fornecimento](../saas-apps/tutorial-list.md)de utilizadores. As aplicações pré-integradas listadas na galeria utilizam geralmente APIs de gestão de utilizadores baseados no SCIM 2.0 para o fornecimento. 

   ![Logotipo da Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se quiser solicitar um novo pedido de provisionamento, pode solicitar que a sua aplicação seja integrada na nossa galeria de [aplicações.](../develop/howto-app-gallery-listing.md) Para um pedido de fornecimento de utilizadores, exigimos que a aplicação tenha um ponto final compatível com o SCIM. Por favor, solicite que o fornecedor de aplicações siga a norma SCIM para que possamos embarcar na aplicação para a nossa plataforma rapidamente.

* **Aplicações que suportam o SCIM 2.0**. Para obter informações sobre como ligar genericamente as aplicações que implementam APIs de gestão de utilizadores baseados no SCIM 2.0, consulte [Construir um ponto final sCIM e configurar o fornecimento de utilizadores](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é o Sistema de Gestão de Identidade de Domínio Transversal (SCIM)?

Para ajudar a automatizar o fornecimento e o deprovisionamento, as aplicações expõem o utilizador proprietário e as APIs do grupo. No entanto, qualquer pessoa que tenha tentado gerir os utilizadores em mais de uma aplicação dir-lhe-á que cada aplicação tenta realizar as mesmas ações simples, como criar ou atualizar utilizadores, adicionar utilizadores a grupos ou desprovisionar utilizadores. No entanto, todas estas ações simples são implementadas de forma um pouco diferente, utilizando diferentes caminhos finais, diferentes métodos para especificar a informação do utilizador, e um esquema diferente para representar cada elemento de informação.

Para responder a estes desafios, a especificação SCIM fornece um esquema comum de utilizador para ajudar os utilizadores a moverem-se para dentro, para fora e em torno de apps. O SCIM está a tornar-se a norma de facto para o provisionamento e, quando utilizado em conjunto com normas da federação como a SAML ou a OpenID Connect, fornece aos administradores uma solução baseada em normas de ponta a ponta para a gestão do acesso.

Para obter orientações pormenorizadas sobre a utilização do SCIM para automatizar o fornecimento e o deprovisionamento de utilizadores e grupos a uma aplicação, consulte [construir um ponto final sCIM e configurar o fornecimento](use-scim-to-provision-users-and-groups.md)de utilizadores.

## <a name="manual-vs-automatic-provisioning"></a>Aprovisionamento manual vs. automático

As candidaturas na galeria Azure AD suportam um dos dois modos de provisionamento:

* **O** fornecimento manual significa que ainda não existe um conector de fornecimento automático de AD Azure para a aplicação. As contas de utilizador devem ser criadas manualmente, por exemplo, adicionando os utilizadores diretamente ao portal administrativo da aplicação, ou fazendo o upload de uma folha de cálculo com detalhes da conta do utilizador. Consulte a documentação fornecida pela app ou contacte o programador da aplicação para determinar quais os mecanismos disponíveis.

* **Automática** significa que foi desenvolvido um conector de fornecimento de AD Azure para esta aplicação. Deve seguir o tutorial de configuração específico para a configuração do fornecimento para a aplicação. Os tutoriais de aplicações podem ser encontrados na [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](../saas-apps/tutorial-list.md).

Na galeria Azure AD, as aplicações que suportam o fornecimento automático são designadas por um ícone de **provisionamento.** Mude para a nova experiência de pré-visualização da galeria para ver estes ícones (no banner no topo da página Adicionar uma página de **aplicação**, selecione o link que diz Clique aqui para experimentar a nova e melhorada galeria de **aplicações).**

![Ícone de provisionamento na galeria de aplicações](./media/user-provisioning/browse-gallery.png)

O modo de provisionamento suportado por uma aplicação também é visível no separador **Provisioning** uma vez adicionado a aplicação às suas **aplicações Enterprise**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como posso configurar o fornecimento automático a uma aplicação?

Para aplicações pré-integradas listadas na galeria, está disponível orientação passo a passo para a criação de um provisionamento automático. Consulte a [lista de tutoriais para aplicações de galerias integradas.](../saas-apps/tutorial-list.md) O vídeo seguinte demonstra como configurar o fornecimento automático de utilizadores para o SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outras aplicações que suportem o SCIM 2.0, siga os passos do artigo [Construa um ponto final sCIM e configure o fornecimento](use-scim-to-provision-users-and-groups.md)de utilizadores.


## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicações do SaaS](../saas-apps/tutorial-list.md)
- [Personalização de mapeamentos de atributos para o fornecimento de utilizadores](customize-application-attributes.md)
- [Expressões de escrita para mapeamento de atributos](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtros de deteção para o fornecimento do utilizador](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Construir um ponto final SCIM e configurar o fornecimento de utilizadores](use-scim-to-provision-users-and-groups.md)
- [Visão geral da API de sincronização da AD Azure](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
