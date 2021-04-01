---
title: O que é automatizado fornecimento de utilizadores de aplicativos SaaS em Azure AD
description: Uma introdução à forma como pode utilizar o Azure AD para provisões automáticas, desavisionamento e atualização contínua de contas de utilizadores através de várias aplicações SaaS de terceiros.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 02/08/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f899c4d67321482dab62729632fe031bb23f71ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99830673"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>O que é o fornecimento automatizado de utilizadores de aplicações SaaS em Azure AD?

No Azure Ative Directory (Azure AD), o termo **provisionamento de aplicações** refere-se à criação automática de identidades e funções de utilizador nas aplicações da cloud [(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)às que os utilizadores precisam de acesso. Além da criação de identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estado ou de funções. Os cenários comuns incluem a disponibilização de um utilizador AZure AD em aplicações como [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)e muito mais.

Apenas começando com a gestão de aplicativos e um único sign-on (SSO) em Azure AD? Confira a [Série Quickstart.](../manage-apps/view-applications-portal.md)

Para saber mais sobre o SCIM e junte-se à conversa da Tech Community, consulte [Provisioning with SCIM Tech Community](https://aka.ms/scimoverview).

![Diagrama de visão geral de provisionamento](./media/user-provisioning/provisioning-overview.png)

Esta funcionalidade permite-lhe:

- **Automatizar o fornecimento**: Crie automaticamente novas contas nos sistemas certos para novas pessoas quando se juntam à sua equipa ou organização.
- **Automatizar a desprovisionamento:** Desativar automaticamente as contas nos sistemas certos quando as pessoas saem da equipa ou da organização.
- **Sincronizar dados entre sistemas:** Certifique-se de que as identidades das suas apps e sistemas são mantidas atualizadas com base em alterações no diretório ou no sistema de recursos humanos.
- **Grupos de provisão:** Provisionar grupos para aplicações que os apoiam.
- **Reger o acesso:** Monitorize e audite quem foi a provisionado nas suas aplicações.
- **Implementar perfeitamente em cenários de campo marrom:** Combine as identidades existentes entre os sistemas e permita uma fácil integração, mesmo quando os utilizadores já existem no sistema alvo.
- **Use a personalização rica:** Aproveite os mapeamentos de atributos personalizáveis que definem quais os dados do utilizador que devem fluir do sistema de origem para o sistema alvo.
- **Receba alertas para eventos críticos:** O serviço de fornecimento fornece alertas para eventos críticos e permite a integração do Log Analytics onde pode definir alertas personalizados para atender às necessidades do seu negócio.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

À medida que o número de aplicações utilizadas nas organizações modernas continua a crescer, os administradores de TI são encarregados da gestão do acesso em escala. Padrões como Security Assertions Markup Language (SAML) ou Open ID Connect (OIDC) permitem que os administradores criem rapidamente um único pré-aviso (SSO), mas o acesso também requer que os utilizadores sejam aprovisionados na aplicação. Para muitos administradores, o provisionamento significa criar manualmente todas as contas de utilizador ou carregar ficheiros CSV todas as semanas, mas estes processos são demorados, dispendiosos e propensos a erros. Soluções como a SAML just-in-time (JIT) foram adotadas para automatizar o fornecimento, mas as empresas também precisam de uma solução para desprovisionar os utilizadores quando saem da organização ou já não precisam de acesso a certas aplicações com base na mudança de funções.

Algumas motivações comuns para o uso de provisões automáticas incluem:

- Maximizando a eficiência e a precisão dos processos de provisionamento.
- Economizando em custos associados ao alojamento e manutenção de soluções e scripts de fornecimento desenvolvidos sob medida.
- Garantir a sua organização removendo instantaneamente as identidades dos utilizadores das principais aplicações SaaS quando saírem da organização.
- Importando facilmente um grande número de utilizadores para uma aplicação ou sistema saaS particular.
- Ter um único conjunto de políticas para determinar quem é aprovisionado e quem pode entrar numa aplicação.

O fornecimento de utilizadores AZure AD pode ajudar a resolver estes desafios. Para saber mais sobre como os clientes têm usado o fornecimento de utilizadores AZure AD, você pode ler o [estudo de caso ASOS](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do fornecimento de utilizadores em Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Que aplicações e sistemas posso utilizar com o fornecimento automático de utilizadores Azure AD?

A Azure AD apresenta suporte pré-integrado para muitas aplicações populares do SaaS e sistemas de recursos humanos, e suporte genérico para aplicações que implementam partes específicas da [norma SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Aplicações pré-integradas (apps da galeria SaaS)**. Pode encontrar todas as aplicações para as quais a Azure AD suporta um conector de provisionamento pré-integrado na [lista de tutoriais de aplicações para o fornecimento de utilizadores.](../saas-apps/tutorial-list.md) As aplicações pré-integradas listadas na galeria geralmente utilizam APIs de gestão de utilizadores baseados no SCIM 2.0 para o provisionamento. 

   ![Logotipo da Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Se pretender solicitar um novo pedido de provisionamento, pode [solicitar que a sua aplicação seja integrada na nossa galeria de aplicações.](../develop/v2-howto-app-gallery-listing.md) Para um pedido de provisionamento do utilizador, exigimos que a aplicação tenha um ponto final compatível com SCIM. Por favor, solicite que o fornecedor de aplicações siga a norma SCIM para que possamos embarcar rapidamente na nossa plataforma.

* **Aplicações que suportam SCIM 2.0**. Para obter informações sobre como conectar genericamente aplicações que implementem APIs de gestão de utilizadores baseados em SCIM 2.0, consulte [Construir um ponto final SCIM e configurar o fornecimento do utilizador](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é Sistema de Gestão de Identidade de Domínio Cruzado (SCIM)?

Para ajudar a automatizar o fornecimento e desprovisionamento, as aplicações expõem as APIs do utilizador proprietário e do grupo. No entanto, quem tiver tentado gerir os utilizadores em mais do que uma aplicação irá dizer-lhe que cada aplicação tenta realizar as mesmas ações simples, como criar ou atualizar utilizadores, adicionar utilizadores a grupos ou desprovisionar utilizadores. No entanto, todas estas ações simples são implementadas de forma um pouco diferente, utilizando diferentes caminhos de ponto final, diferentes métodos para especificar a informação do utilizador, e um esquema diferente para representar cada elemento de informação.

Para responder a estes desafios, a especificação SCIM fornece um esquema comum de utilizador para ajudar os utilizadores a moverem-se para dentro, fora e em torno de apps. O SCIM está a tornar-se o padrão de facto para o provisionamento e, quando usado em conjunto com padrões de federação como o SAML ou o OpenID Connect, fornece aos administradores uma solução baseada em padrões de ponta a ponta para a gestão de acessos.

Para obter orientações detalhadas sobre o desenvolvimento de um ponto final SCIM para automatizar o fornecimento e desprovisionamento de utilizadores e grupos para uma aplicação, consulte [construir um ponto final SCIM e configurar o fornecimento do utilizador](use-scim-to-provision-users-and-groups.md). Para aplicações pré-integradas na galeria (Slack, Azure Databricks, Snowflake, etc.), pode ignorar a documentação do programador e utilizar os tutoriais [aqui](../saas-apps/tutorial-list.md)fornecidos.

## <a name="manual-vs-automatic-provisioning"></a>Aprovisionamento manual vs. automático

As candidaturas na galeria Azure AD suportam um dos dois modos de provisionamento:

* **O** fornecimento manual significa que ainda não existe um conector automático Azure AD para a aplicação. As contas dos utilizadores devem ser criadas manualmente, por exemplo, adicionando os utilizadores diretamente ao portal administrativo da aplicação ou carregando uma folha de cálculo com detalhes da conta do utilizador. Consulte a documentação fornecida pela app ou contacte o desenvolvedor de aplicações para determinar quais os mecanismos disponíveis.

* **O automático** significa que foi desenvolvido um conector AZure AD para esta aplicação. Deve seguir o tutorial de configuração específico para configurar o provisionamento para a aplicação. Os tutoriais de aplicações podem ser encontrados na [Lista de Tutoriais sobre Como Integrar as Aplicações SaaS com o Azure Ative Directory](../saas-apps/tutorial-list.md).

Na galeria Azure AD, as aplicações que suportam o provisionamento automático são designadas por um ícone **de Provisioning.** Mude para a nova experiência de pré-visualização da galeria para ver estes ícones (no banner no topo da página de **aplicação Adicionar**, selecione o link que diz **Clique aqui para experimentar a nova e melhorada galeria de aplicações).**

![Ícone de provisionamento na galeria de aplicações](./media/user-provisioning/browse-gallery.png)

O modo de provisionamento suportado por uma aplicação também é visível no **separador Provisioning** uma vez que tenha adicionado a aplicação às suas **aplicações Enterprise.**

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como posso configurar o provisionamento automático para um pedido?

Para aplicações pré-integradas listadas na galeria, está disponível orientação passo a passo para a criação de provisões automáticas. Consulte a [lista de tutoriais para aplicações de galeria integradas.](../saas-apps/tutorial-list.md) O vídeo que se segue demonstra como configurar o fornecimento automático de utilizadores para o SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outras aplicações que suportem o SCIM 2.0, siga os passos no artigo [Construa um ponto final SCIM e configuure o provisionamento do utilizador](use-scim-to-provision-users-and-groups.md).


## <a name="next-steps"></a>Passos seguintes

- [Lista de tutoriais sobre como integrar apps saaS](../saas-apps/tutorial-list.md)
- [Personalizar mapeamentos de atributos para fornecimento de utilizador](customize-application-attributes.md)
- [Filtros de deteção para o fornecimento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
