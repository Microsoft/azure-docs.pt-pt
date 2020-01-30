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
ms.openlocfilehash: 9848e686188288b507a0a74d0f9fa16f8f0e4253
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841204"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos com Azure Active Directory

No Azure Active Directory (AD do Azure), o termo **provisionamento de aplicativo** refere-se à criação automática de identidades de usuário e funções nos aplicativos de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) aos quais os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Os cenários comuns incluem o provisionamento de um usuário do Azure AD em aplicativos como [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)e muito mais.

![Diagrama de visão geral de provisionamento](media/user-provisioning/provisioning-overview.png)

Esse recurso permite que você:

- **Automatizar o provisionamento**: crie automaticamente novas contas nos sistemas certos para novas pessoas ao ingressarem em sua equipe ou organização.
- **Automatizar o desprovisionamento:** Desative automaticamente as contas nos sistemas certos quando as pessoas saírem da equipe ou da organização.
- **Sincronizar dados entre sistemas:** Certifique-se de que as identidades em seus aplicativos e sistemas sejam mantidas atualizadas com base nas alterações no diretório ou no sistema de recursos humanos.
- **Provisionar grupos:** Provisione grupos para aplicativos que dão suporte a eles.
- **Controle de acesso:** Monitore e audite quem foi provisionado em seus aplicativos.
- **Implantar diretamente em cenários de campo marrom:** Combine identidades existentes entre sistemas e permita uma fácil integração, mesmo quando os usuários já existem no sistema de destino.
- **Usar personalização avançada:** Aproveite os mapeamentos de atributo personalizáveis que definem quais dados de usuário devem fluir do sistema de origem para o sistema de destino.
- **Obter alertas para eventos críticos:** O serviço de provisionamento fornece alertas para eventos críticos e permite a integração de Log Analytics em que você pode definir alertas personalizados para atender às suas necessidades de negócios.

## <a name="benefits-of-automatic-provisioning"></a>Benefícios do provisionamento automático

Como o número de aplicativos usados em organizações modernas continua crescendo, os administradores de ti são tarefa com gerenciamento de acesso em escala. Padrões como SAML (Security Asserties Markup Language) ou OIDC (Open ID Connect) permitem que os administradores configurem rapidamente o SSO (logon único), mas o acesso também exige que os usuários sejam provisionados no aplicativo. Para muitos administradores, o provisionamento significa criar manualmente cada conta de usuário ou carregar arquivos CSV a cada semana, mas esses processos são demorados, caros e sujeitos a erros. Soluções como JIT (just-in-time) do SAML foram adotadas para automatizar o provisionamento, mas as empresas também precisam de uma solução para desprovisionar usuários quando deixam a organização ou não precisam mais de acesso a determinados aplicativos com base na alteração de função.

Algumas motivações comuns para usar o provisionamento automático incluem:

- Maximizar a eficiência e a precisão dos processos de provisionamento.
- Economizando os custos associados à hospedagem e manutenção de soluções e scripts de provisionamento desenvolvidos com personalização.
- Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles deixam a organização.
- Importar facilmente um grande número de usuários para um sistema ou aplicativo SaaS específico.
- Ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar em um aplicativo.

O provisionamento de usuários do Azure AD pode ajudar a resolver esses desafios. Para saber mais sobre como os clientes usam o provisionamento de usuário do Azure AD, você pode ler o [estudo de caso do Asos](https://aka.ms/asoscasestudy). O vídeo abaixo fornece uma visão geral do provisionamento de usuário no Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento automático de usuário do Azure AD?

O Azure AD apresenta suporte integrado para muitos sistemas de recursos humanos e aplicativos SaaS populares e suporte genérico para aplicativos que implementam partes específicas do [padrão SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Aplicativos previamente integrados (aplicativos SaaS de galeria)** . Você pode encontrar todos os aplicativos para os quais o Azure AD dá suporte a um conector de provisionamento previamente integrado na [lista de tutoriais de aplicativos para provisionamento de usuários](../saas-apps/tutorial-list.md). Os aplicativos previamente integrados listados na Galeria geralmente usam APIs de gerenciamento de usuário com base em SCIM 2,0 para provisionamento. 

   ![Logotipo do Salesforce](media/user-provisioning/gallery-app-logos.png)

   Se você quiser solicitar um novo aplicativo para provisionamento, poderá [solicitar que seu aplicativo seja integrado à nossa galeria de aplicativos](../develop/howto-app-gallery-listing.md). Para uma solicitação de provisionamento de usuário, exigimos que o aplicativo tenha um ponto de extremidade compatível com SCIM. Solicite que o fornecedor do aplicativo siga o padrão SCIM para que possamos integrar o aplicativo à nossa plataforma rapidamente.

* **Aplicativos que dão suporte ao SCIM 2,0**. Para obter informações sobre como se conectar genericamente a aplicativos que implementam APIs de gerenciamento de usuário com base em SCIM 2,0, consulte [criar um ponto de extremidade scim e configurar o provisionamento de usuários](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é o Sistema de Gestão de Identidade de Domínio Transversal (SCIM)?

Para ajudar a automatizar o provisionamento e o desprovisionamento, os aplicativos expõem APIs de usuário e de grupo proprietárias. No entanto, qualquer pessoa que tentasse gerenciar usuários em mais de um aplicativo informará que cada aplicativo tenta executar as mesmas ações simples, como criar ou atualizar usuários, adicionar usuários a grupos ou desprovisionar usuários. Ainda assim, todas essas ações simples são implementadas de forma um pouco diferente, usando diferentes caminhos de ponto de extremidade, métodos diferentes para especificar informações de usuário e um esquema diferente para representar cada elemento de informações.

Para resolver esses desafios, a especificação SCIM fornece um esquema de usuário comum para ajudar os usuários a migrar, de e para os aplicativos. O SCIM está se tornando o padrão de fato para o provisionamento e, quando usado em conjunto com os padrões de Federação como SAML ou OpenID Connect, fornece aos administradores uma solução baseada em padrões de ponta a ponta para o gerenciamento de acesso.

Para obter orientações detalhadas sobre como usar o SCIM para automatizar o provisionamento e desprovisionamento de usuários e grupos para um aplicativo, consulte [criar um ponto de extremidade scim e configurar o provisionamento de usuários](use-scim-to-provision-users-and-groups.md).

## <a name="manual-vs-automatic-provisioning"></a>Aprovisionamento manual vs. automático

Os aplicativos na galeria do Azure AD dão suporte a um dos dois modos de provisionamento:

* O provisionamento **manual** significa que ainda não há nenhum conector de provisionamento do Azure ad automático para o aplicativo. As contas de usuário devem ser criadas manualmente, por exemplo, adicionando usuários diretamente no portal administrativo do aplicativo ou carregando uma planilha com detalhes da conta de usuário. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

* **Automático** significa que um conector de provisionamento do Azure ad foi desenvolvido para este aplicativo. Você deve seguir o tutorial de instalação específico para configurar o provisionamento para o aplicativo. Os tutoriais do aplicativo podem ser encontrados na [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md).

Na galeria do Azure AD, os aplicativos que dão suporte ao provisionamento automático são designados por um ícone de **provisionamento** . Alterne para a nova experiência de visualização da galeria para ver esses ícones (na faixa na parte superior da **página Adicionar um aplicativo**, selecione o link que diz **clicar aqui para experimentar a Galeria de aplicativos nova e aprimorada**).

![Ícone de provisionamento na Galeria de aplicativos](media/user-provisioning/browse-gallery.png)

O modo de provisionamento com suporte de um aplicativo também será visível na guia **provisionamento** depois que você adicionar o aplicativo aos seus **aplicativos empresariais**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como fazer configurar o provisionamento automático para um aplicativo?

Para aplicativos previamente integrados listados na Galeria, a orientação passo a passo está disponível para configurar o provisionamento automático. Consulte a [lista de tutoriais para aplicativos da Galeria integrada](../saas-apps/tutorial-list.md). O vídeo a seguir demonstra como configurar o provisionamento automático de usuário para o SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Para outros aplicativos que dão suporte ao SCIM 2,0, siga as etapas no artigo [criar um ponto de extremidade scim e configurar o provisionamento de usuários](use-scim-to-provision-users-and-groups.md).


## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
- [Personalizando mapeamentos de atributo para provisionamento de usuário](customize-application-attributes.md)
- [Gravando expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
- [Filtros de escopo para provisionamento de usuário](define-conditional-rules-for-provisioning-user-accounts.md)
- [Criar um ponto de extremidade SCIM e configurar o provisionamento de usuário](use-scim-to-provision-users-and-groups.md)
- [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
