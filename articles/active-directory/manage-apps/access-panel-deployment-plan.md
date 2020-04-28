---
title: Planeie uma implantação do Painel de Acesso ativo do Diretório Azure
description: Orientação sobre a implantação do Painel de Acesso ao Diretório Ativo do Azure
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76897070"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planeie uma implantação do Painel de Acesso ativo do Diretório Azure

O Painel de Acesso ao Diretório Ativo Azure (Azure AD) é um portal baseado na web que ajuda a reduzir os custos de suporte, aumentar a produtividade e segurança e reduzir a frustração dos utilizadores. O sistema inclui relatórios detalhados que rastreiam quando acedeao ao sistema e notifica administradores de uso indevido ou abuso.

Utilizando o Painel de Acesso AD Azure, pode:

* Descubra e aceda a todos os recursos ligados ao Azure Dad da sua empresa, tais como aplicações
* Solicitar acesso a novas apps e grupos
* Gerir o acesso a estes recursos para outros
* Gerir resets de palavra-passe de autosserviço e definições de autenticação de multi-factores Azure
* Gerir os seus dispositivos

Também permite que os administradores gerem:

* Termos de serviço
* Organizações
* Revisões de acesso


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Benefícios da integração do Painel de Acesso AD Azure

O Painel de Acesso Azure AD beneficia as empresas das seguintes formas:

**Proporciona uma experiência intuitiva ao utilizador**: O Painel de Acesso fornece-lhe uma única plataforma para todas as suas aplicações de inscrição única (SSO) ligadas ao Azure. Tem um portal unificado para encontrar as configurações e novas capacidades existentes, como gestão de grupo e reset de senha de autosserviço, à medida que são adicionadas. A experiência intuitiva permite que os utilizadores regressem ao trabalho mais rapidamente e sejam mais produtivos, reduzindo ao mesmo tempo a sua frustração.

**Aumenta a produtividade**: Todas as aplicações do utilizador no Painel de Acesso têm SSO ativada. Permitir o SSO através de aplicações empresariais e o Office 365 cria uma experiência de inscrição superior reduzindo ou eliminando solicitações adicionais de inscrição. O Painel de Acesso utiliza a adesão ao self-service e dinâmica e melhora a segurança geral do seu sistema de identidade. Fá-lo garantindo que as pessoas certas gerem o acesso às aplicações. O Painel de Acesso serve como uma página de aterragem coerente para que encontre rapidamente recursos e continue as tarefas de trabalho.

**Gerir o custo**: Permitir o Painel de Acesso com AD Azure pode ajudar na alienação de infraestruturas no local. Reduz os custos de suporte, fornecendo-lhe um portal consistente para encontrar todas as suas apps, solicitar acesso a recursos e gerir contas.

**Aumenta a flexibilidade e a segurança**: O Painel de Acesso dá-lhe acesso à segurança e flexibilidade que uma plataforma cloud proporciona. Os administradores podem facilmente alterar as definições para aplicações e recursos e podem acomodar novos requisitos de segurança sem afetar os utilizadores.

Permite uma auditoria robusta e rastreio de **utilização:** Auditoria e rastreio de utilização de todas as capacidades do utilizador informe-o quando os utilizadores estão a utilizar os seus recursos e garante que pode avaliar a segurança.

### <a name="licensing-considerations"></a>Considerações de licenciamento

O Painel de Acesso é gratuito e não necessita de licenças para usar a um nível básico. No entanto, o número de objetos no seu diretório e as funcionalidades adicionais que pretende implementar podem requerer licenças adicionais. Alguns cenários comuns da AD Azure que têm requisitos de licenciamento incluem as seguintes funcionalidades de segurança:

* [Multi-Factor Authentication do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Associação baseada em grupo](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Reposição personalizada de palavra-passe](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Proteção de Identidade do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Consulte o [guia completo de licenciamento para Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Pré-requisitos para a implantação do Painel de Acesso AD Azure

Complete os seguintes pré-requisitos antes de iniciar este projeto:

* [Integrar aplicação SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Gerir a infraestrutura de utilizadores e grupos azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planeie a implantação do Painel de Acesso AD Azure

O quadro seguinte descreve os casos de utilização chave para uma implantação do Painel de Acesso:

| Área| Descrição |
| - | - |
| Acesso| O portal Access Panel é acessível a partir de dispositivos corporativos e pessoais dentro da rede corporativa. |
|Acesso | O portal Access Panel está acessível a partir de dispositivos corporativos fora da rede corporativa. |
| Auditoria| Os dados de utilização são descarregados em sistemas corporativos pelo menos a cada 29 dias. |
| Governação| O ciclo de vida das atribuições do utilizador a aplicações e grupos ligados à AD Azure é definido e monitorizado. |
| Segurança| O acesso aos recursos é controlado através de atribuições de utilizadores e grupos. Apenas os utilizadores autorizados podem gerir o acesso aos recursos. |
| Desempenho| Os prazos de propagação da atribuição de acesso são documentados e monitorizados. |
| Experiência do Utilizador| Os utilizadores estão cientes das capacidades do Painel de Acesso e de como usá-las.|
| Experiência do Utilizador| Os utilizadores podem gerir o seu acesso a aplicações e grupos.|
| Experiência do Utilizador| Os utilizadores podem gerir as suas contas. |
| Experiência do Utilizador| Os utilizadores estão cientes da compatibilidade do navegador. |
| Suporte| Os utilizadores podem encontrar suporte para problemas do Painel de Acesso. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Boas práticas para a implantação do Painel de Acesso AD Azure

A funcionalidade do Painel de Acesso pode ser ativada gradualmente. Recomendamos a seguinte ordem de implantação:

1. As Minhas Aplicações
   * Lançador de aplicativos
   * Gestão de aplicativos self-service
   * Integração do Microsoft Office 365

1. Descoberta de aplicativos self-service
   * Reposição personalizada de palavra-passe
   * Definições de autenticação de vários fatores
   * Gestão de dispositivos
   * Termos de utilização
   * Gerir organizações

1. Os meus grupos
   * Gestão de grupos self-service
1. Revisões de acesso
   * Gestão de revisão de acesso

Começando pelas Minhas Apps apresenta os utilizadores ao portal como um local comum para aceder a recursos. A adição de uma descoberta de aplicações self-service baseia-se na experiência My Apps. Os meus grupos e as minhas avaliações de acesso baseiam-se nas capacidades de autosserviço.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Configurações de planos para o Painel de Acesso AD Azure

A tabela seguinte lista várias configurações importantes do Painel de Acesso e os valores típicos que pode utilizar:

| Configuração| Valores típicos |
| - | - |
| Determinar os grupos piloto| Identifique o grupo de segurança Azure AD a ser utilizado e certifique-se de que todos os membros piloto fazem parte do grupo. |
| Determinar o grupo ou grupos a ativar para a produção.| Identifique os grupos de segurança Azure AD, ou os grupos de Diretório Ativo sincronizados com a AD Azure, a utilizar. Certifique-se de que todos os membros piloto fazem parte do grupo. |
| Permitir que os utilizadores utilizem SSO em determinados tipos de aplicações| SSO Federado, OAuth, Password SSO, App Proxy |
| Permitir que os utilizadores utilizem o reset de palavra-passe self-service | Sim |
| Permitir que os utilizadores utilizem a autenticação multi-factor| Sim |
| Permitir que os utilizadores utilizem a gestão do grupo self-service para determinados tipos de grupos| Grupos de segurança, Office 365 grupos |
| Permitir que os utilizadores utilizem a gestão de aplicações self-service| Sim |
| Permitir que os utilizadores utilizem comentários de acesso| Sim |

### <a name="plan-consent-strategy"></a>Estratégia de consentimento do plano

Os utilizadores ou administradores devem consentir os termos de utilização e privacidade de qualquer aplicação. Se possível, dadas as suas regras de negócio, utilize o consentimento do administrador, o que proporciona aos utilizadores uma melhor experiência.

Para utilizar o consentimento do administrador, deve ser um administrador global da organização, e as aplicações devem ser:

* Registado na sua organização

* Registado noutra organização da AD Azure e previamente consentido por pelo menos um utilizador

Para mais informações, consulte [Configurar a forma como os utilizadores do fim consentem com uma aplicação no Diretório Ativo do Azure](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](../fundamentals/active-directory-deployment-plans.md) de que está a envolver as partes interessadas certas e que os papéis das partes interessadas no projeto são bem compreendidos.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe proativamente os seus utilizadores como e quando a sua experiência mudará e como obter suporte se necessário.

Embora o Painel de Acesso não crie normalmente problemas de utilizador, é importante preparar. Crie guias e uma lista de todos os recursos para o seu pessoal de apoio antes do seu lançamento.

#### <a name="communications-templates"></a>Modelos de comunicação

A Microsoft fornece [modelos personalizáveis para e-mails e outras comunicações](https://aka.ms/APTemplates) para o Painel de Acesso. Pode adaptar estes ativos para utilização noutros canais de comunicações, conforme adequado para a sua cultura corporativa.

## <a name="plan-your-sso-configuration"></a>Planeie a sua configuração SSO

Quando um utilizador insere uma aplicação, passa por um processo de autenticação e é obrigado a provar quem são. Sem SSO, uma palavra-passe é armazenada na aplicação, e o utilizador é obrigado a saber esta palavra-passe. Com o SSO, as credenciais dos utilizadores são passadas para a aplicação, pelo que não precisam de reintroduzir senhas para cada aplicação.

Para lançar aplicações nas Minhas Apps, o SSO tem de ser ativado.

A Azure AD suporta três formas diferentes de permitir um [único início de inscrição nas aplicações:](what-is-single-sign-on.md)

* **Inscrição única federada** 
    * Permite que uma aplicação redirecione para a AD Azure para autenticação do utilizador, em vez de pedir uma senha. 
    * É suportado para aplicações que utilizam protocolos, tais como SAML 2.0, WS-Federation ou OpenID Connect, e é o modo mais rico de inscrição única.

* **Inscrição única baseada em palavra-passe** 
    * Permite o armazenamento e repetição de palavras-passe de aplicação segura utilizando uma extensão do navegador web ou uma aplicação móvel. 
    * Aproveita o processo de entrada existente fornecido pela aplicação, mas permite a um administrador gerir as palavras-passe. O utilizador não é obrigado a saber a senha.

* **Inscrição única existente** 
    * Permite ao Azure AD tirar partido de qualquer inscrição única existente que tenha sido configurada para a aplicação.
    * Permite que estas aplicações estejam ligadas aos portais do Office 365 ou do Painel de Acesso AD Azure. 
    * Permite relatórios adicionais em Azure AD quando as aplicações são lançadas lá. 
    * Inclui a utilização do Proxy de Aplicação Azure e o modo de inscrição único ligado.

Saiba como configurar o modo SSO de uma aplicação aqui: [inscrição única para aplicações no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Para a melhor experiência com a página My Apps, comece com a integração de aplicações em nuvem que estão disponíveis para SSO federado. O SSO federado permite que os utilizadores tenham uma experiência consistente de um clique nas suas superfícies de lançamento de aplicações e tende a ser mais robusto no controlo de configuração.

Utilize SSO federado com AD Azure (OpenID Connect/SAML) quando uma aplicação o suporta, em vez de SSO e ADFS baseados em palavra-passe.

Para obter mais informações sobre como implementar e configurar as suas aplicações SaaS, consulte o plano de [implementação SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plano para implementar a extensão do navegador My Apps

Quando os utilizadores acedem a aplicações SSO baseadas em palavras-passe, precisam de instalar e utilizar a extensão de entrada segura das Minhas Apps. A extensão executa um script que transmite a palavra-passe para o formulário de entrada da aplicação. Os utilizadores são solicitados a instalar a extensão quando lançarem pela primeira vez a aplicação SSO baseada em palavra-passe. Mais informações sobre a extensão podem encontrar nesta documentação sobre [a instalação da extensão do navegador do Painel](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)de Acesso.

Se tiver de integrar aplicações SSO baseadas em palavras-passe, deverá definir um mecanismo para implementar a extensão em escala com [navegadores suportados](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). As opções incluem:

* [Política de grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Gestor de configuração para Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Download e configuração orientados pelo utilizador para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Saiba mais: Como configurar o início de inscrição individual da [palavra-passe](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Os utilizadores que não utilizam aplicações SSO baseadas em palavras-passe também beneficiam da extensão. Estes benefícios incluem a capacidade de lançar qualquer aplicação a partir da sua barra de pesquisa, encontrar acesso a aplicações recentemente utilizadas e ter um link para a página My Apps.

Eis o que o utilizador verá ao lançar pela primeira vez uma aplicação SSO baseada em palavras-passe:

![Screenshot da extensão do navegador My Apps instala ção ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plano de acesso móvel

Um navegador protegido com a política Intune (Microsoft Edge ou Intune Managed Browser) é necessário para que os utilizadores móveis lancem aplicações SSO baseadas em palavras-passe. Um navegador protegido por políticas permite a transferência da palavra-passe guardada para a aplicação. O Microsoft Edge ou o navegador gerido fornece um conjunto de funcionalidades de proteção de dados web. Também pode utilizar o Microsoft Edge para cenários empresariais em dispositivos iOS e Android. O Microsoft Edge suporta os mesmos cenários de gestão que o Intune Managed Browser e melhora a experiência do utilizador. Saiba mais: [Gerir o acesso à Web utilizando um navegador protegido por políticas microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Planeie a sua implementação das minhas aplicações

A fundação do Painel de Acesso é o [https://myapps.microsoft.com](https://myapps.microsoft.com/)lançador de aplicações My Apps, a que os utilizadores acedem a . As páginas My Apps dão aos utilizadores um único lugar para iniciar em seu trabalho e chegar às suas aplicações necessárias. Aqui, os utilizadores encontram uma lista de todas as aplicações a que têm acesso único. 

![Uma imagem do painel de aplicações](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

As mesmas aplicações serão apresentadas no lançador de aplicações do Office 365 quando os utilizadores estiverem a utilizar o portal Office 365.

Planeie a encomenda na qual irá adicionar aplicações ao launcher my Apps e decida se irá lançá-las gradualmente ou tudo de uma vez. Para isso, crie um inventário de aplicações que enumere o tipo de autenticação e quaisquer integrações SSO existentes para cada aplicação.

#### <a name="add-applications-to-the-my-apps-panel"></a>Adicione aplicações ao painel My Apps

Qualquer aplicação ativada por SSO Azure AD pode ser adicionada ao launcher My Apps. Outras aplicações são adicionadas utilizando a opção Linked SSO. Pode configurar um azulejo de aplicação que se liga ao URL da sua aplicação web existente. O Linked SSO permite-lhe começar a direcionar os utilizadores para o portal My Apps sem migrar todas as aplicações para o Azure AD SSO. Pode gradualmente deslocar-se para aplicações configuradas em Azure AD SSO sem perturbar a experiência dos utilizadores.

#### <a name="use-my-apps-collections"></a>Use as coleções das minhas apps

Por padrão, todas as aplicações estão listadas numa única página. Mas pode usar coleções para agrupar aplicações relacionadas e apresentá-las num separador, tornando-as mais fáceis de encontrar. Por exemplo, pode utilizar coleções para criar agrupamentos lógicos de candidaturas para funções específicas de trabalho, tarefas, projetos, e assim por diante. Para obter informações, consulte [como utilizar as coleções das Minhas Apps para personalizar os painéis](access-panel-collections.md)de acesso ao utilizador. 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planeie se usar as Minhas Apps ou um portal existente

Os seus utilizadores podem já ter uma aplicação ou portal diferente das Minhas Apps. Em caso afirmativo, decida se suporta ambos os portais ou utilize apenas um.

Se um portal existente já estiver a ser utilizado como ponto de partida para os utilizadores, pode integrar a funcionalidade My Apps utilizando URLs de acesso ao utilizador. Os URLs de acesso ao utilizador funcionam como links diretos para as aplicações disponíveis no portal My Apps. Estes URLs podem ser incorporados em qualquer website existente. Quando um utilizador seleciona o link, abre a aplicação a partir do portal My Apps.

Pode encontrar a propriedade URL de acesso ao utilizador na área de **Propriedades** da aplicação no portal Azure.

![Uma imagem do painel de aplicações](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planear descoberta e acesso de aplicações de autosserviço

Uma vez que um conjunto de aplicações core é implementado para a página My Apps de um utilizador, você deve ativar funcionalidades de gestão de aplicações self-service. A descoberta de aplicações self-service permite aos utilizadores:

* Encontre novas aplicações para adicionar às suas Aplicações. 
* Adicione aplicações opcionais que podem não saber que precisam durante a configuração.

Os fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para aplicações de acesso. Os utilizadores que forem aprovadores receberão notificações dentro do portal My Apps quando houver um pedido pendente de acesso à aplicação.

## <a name="plan-self-service-group-membership"></a>Planear a adesão ao grupo de self-service 

Pode permitir que os utilizadores criem e gerem os seus próprios grupos de segurança ou grupos office 365 em Azure AD. O proprietário do grupo pode aprovar ou negar pedidos de adesão e delegar o controlo da adesão ao grupo. As funcionalidades de gestão de grupos self-service não estão disponíveis para grupos de segurança ou listas de distribuição ativadas por correio.

Para planear a adesão ao grupo self-service, determine se irá permitir que todos os utilizadores da sua organização criem e gerem grupos ou apenas um subconjunto de utilizadores. Se está a permitir um subconjunto de utilizadores, terá de criar um grupo ao qual essas pessoas são adicionadas. Consulte a [configuração da gestão do grupo self-service no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para obter detalhes sobre a ativação destes cenários.

## <a name="plan-reporting-and-auditing"></a>Relatórios e auditorias de planos

A Azure AD fornece [relatórios que oferecem insights técnicos e empresariais.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) Trabalhe com os proprietários de seus negócios e aplicações técnicas para assumir a propriedade destes relatórios e consumi-los regularmente. A tabela que se segue fornece alguns exemplos de cenários típicos de reporte.

|   | Faça a gestão do risco.| Aumentar a produtividade| Governação e conformidade |
|  - |- | - | - |
| Tipos de relatório|  Permissões de aplicação e utilização| Atividade de prestação de contas| Reveja quem está a aceder às aplicações |
| Ações potenciais| Acesso à auditoria; revogar permissões| Remediar eventuais erros de provisionamento| Revogar o acesso |

A Azure AD mantém a maioria dos dados de auditoria durante 30 dias. Os dados estão disponíveis via Portal de Administração Azure ou API para que possa descarregar nos seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os registos de auditoria para acesso à aplicação estão disponíveis por 30 dias. Se a auditoria de segurança dentro da sua empresa necessitar de uma retenção mais longa, os registos devem ser exportados para uma ferramenta de Evento e Gestão de Informações de Segurança (SIEM), como splunk ou ArcSight.

Para auditoria, relatório e backups de recuperação de desastres, documente a frequência necessária de descarregamento, qual é o sistema alvo e quem é responsável pela gestão de cada backup. Pode não precisar de auditoria seletiva separada e de reportar cópias de segurança. O seu apoio à recuperação de desastres deve ser uma entidade separada.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Implementar aplicações para o painel My Apps dos utilizadores

Depois de configurar uma aplicação para o SSO, os grupos têm acesso atribuído. Os utilizadores dos grupos designados terão acesso e verão a aplicação nas suas Aplicações My e no launcher de aplicações office 365.

Consulte [utilizadores e grupos de atribuição para uma aplicação no Diretório Ativo](methods-for-assigning-users-and-groups.md).

Se durante os testes ou implementação pretender adicionar os grupos mas ainda não permitir que as aplicações apareçam nas Minhas Apps, consulte [Hide uma aplicação da experiência do utilizador no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Implementar aplicações do Microsoft Office 365 para as minhas apps

Para aplicações do Office 365, os utilizadores recebem uma cópia do Office com base em licenças que lhes são atribuídas. Um pré-requisito para o acesso às aplicações do Office é que os utilizadores sejam atribuídos as licenças corretas ligadas às aplicações do Office. Ao atribuir uma licença a um utilizador, verão automaticamente as aplicações associadas à licença na sua página My Apps e no lançador de aplicações do Office 365.

Se quiser esconder um conjunto de aplicações do Office dos utilizadores, existe uma opção para ocultar aplicações do portal My Apps, permitindo ainda o acesso do portal Office 365. Encontre estas definições na parte das definições do Utilizador da aplicação. Saiba mais: [Ocultar uma aplicação da experiência do utilizador no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Uma imagem de configurar como esconder aplicações](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Implementar capacidades de autosserviço de aplicação

O acesso à aplicação self-service permite que os utilizadores se autodescubram e solicitem acesso a aplicações. Os utilizadores têm a liberdade de aceder às aplicações de que necessitam sem passar em cada vez por um grupo de TI para solicitar acesso. Quando um utilizador solicita acesso e é aprovado, automaticamente ou manualmente por um proprietário de aplicações, são adicionados a um grupo na parte de trás. O relatório está habilitado sobre quem solicitou, aprovou ou removeu o acesso, e dá-lhe controlo sobre a gestão das funções atribuídas.

Pode delegar a aprovação de pedidos de acesso a aplicações a aprovadores de empresas. O utilizador do negócio pode definir as palavras-passe de acesso à aplicação a partir da página My Apps do utilizador.

Saiba mais: [Como utilizar o acesso à aplicação self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Uma imagem de configurar a gestão de aplicações de self-service](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Validar a sua implementação

Certifique-se de que a implementação do Painel de Acesso está completamente testada e que está em vigor um plano de reversão.

Os seguintes testes devem ser realizados com dispositivos corporativos e dispositivos pessoais. Estes casos de teste também devem refletir os seus casos de uso do seu negócio. Seguem-se alguns casos baseados nos requisitos de negócio da amostra neste documento e em cenários técnicos típicos. Adicione outros específicos às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Aplicação SSO acaso de teste de acesso exemplos:


| Caso de negócios| Resultado esperado |
| - | -|
| User entra no portal My Apps| O utilizador pode fazer o sessão e ver as suas aplicações |
| Utilizador lança aplicação SSO federada| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança pela primeira vez uma aplicação SSO de senha| O utilizador precisa de instalar a extensão das Minhas Apps |
| Utilizador lança uma aplicação SSO de senha um momento subsequente| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança aplicação do Portal do Office 365| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança uma aplicação do Navegador Gerido| O utilizador é automaticamente inscrito na aplicação |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Aplicativo capacidades de autosserviço testar exemplos de casos


| Caso de negócios| Resultado esperado |
| - | - |
| O utilizador pode gerir a adesão à aplicação| O utilizador pode adicionar/remover membros que tenham acesso à aplicação |
| O utilizador pode editar a aplicação| O utilizador pode editar a descrição e credenciais da aplicação para aplicações SSO de senha |

### <a name="rollback-steps"></a>Passos de retrocesso

É importante planear o que fazer se a sua implantação não correr como planeado. Se a configuração SSO falhar durante a implementação, deve entender como resolver problemas de [Problemas sOO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e reduzir o impacto para os seus utilizadores. Em circunstâncias extremas, talvez precise [saciar o SSO.](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>Gerir a sua implementação

Deve utilizar o papel menos privilegiado para realizar uma tarefa necessária dentro do Diretório Ativo Azure. [Reveja as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolha a certa para resolver as suas necessidades para cada persona para esta aplicação. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Pessoas fictícias| Funções| Papel da AD Azure  |
| - | -| -|
| Administração de helpdesk| Suporte de nível 1| Nenhuma |
| Administrador de identidade| Configure e depuraquando problemas impactam Azure AD| Administrador global |
| Administrador de candidatura| Atestado de utilizador na aplicação, configuração em utilizadores com permissões| Nenhuma |
| Administradores de infraestruturas| Cert dono de capotamento| Administrador global |
| Empresário/stakeholder| Atestado de utilizador na aplicação, configuração em utilizadores com permissões| Nenhuma |

Pode utilizar a [Gestão de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) para gerir as suas funções para fornecer auditoria, controlo e revisão de acesso adicionais aos utilizadores com permissões de diretório.

### <a name="troubleshoot-access-panel-issues"></a>Problemas problemas do Painel de Acesso

Crie guias de resolução de problemas para a sua organização de suporte com cenários comuns, que apontam para a documentação da Microsoft nas suas resoluções. É melhor criar guias que quebrem o suporte nos níveis utilizados pela sua organização.

Consulte estes guias de resolução de problemas para referência:

[Aplicações que não aparecem](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Aplicações inesperadas que aparecem](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[O utilizador não pode inscrever-se no Painel de Acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemas de utilização do acesso a aplicações de autosserviço](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemas com a extensão do navegador](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Passos seguintes

[Planear uma implementação da autenticação de vários fatores do Azure](https://aka.ms/deploymentplans/mfa)
