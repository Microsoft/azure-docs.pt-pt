---
title: Planeie uma implantação do Painel de Acesso ao Diretório Ativo Azure
description: Orientações sobre a implementação do Painel de Acesso ao Diretório Ativo Azure
services: active-directory
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeffcfc919a6aeeae61ed79286d613d72c6b183c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761175"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Planeie uma implantação do Painel de Acesso ao Diretório Ativo Azure

O Azure Ative Directory (Azure AD) Access Panel é um portal baseado na Web que ajuda a reduzir os custos de suporte, aumentar a produtividade e a segurança e reduzir a frustração dos utilizadores. O sistema inclui relatórios detalhados que rastreia quando acede ao sistema e notifica administradores de uso indevido ou abuso.

Ao utilizar o Painel de Acesso AD Azure, pode:

* Descubra e aceda a todos os recursos ligados à Azure AD da sua empresa, tais como aplicações
* Solicitar acesso a novas apps e grupos
* Gerir o acesso a estes recursos para outros
* Gerir resets de senha de autosserviço e definições de autenticação multi-factor Azure
* Gerir os seus dispositivos

Permite ainda que os administradores gerem:

* Termos de serviço
* Organizações
* Revisões de acesso


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Benefícios da integração do Painel de Acesso AD Azure

O Painel de Acesso AZure AD beneficia as empresas das seguintes formas:

**Proporciona uma experiência intuitiva**do utilizador : O Painel de Acesso fornece-lhe uma única plataforma para todas as suas aplicações ligadas a Azure single sign-on (SSO)." Tem um portal unificado para encontrar as definições existentes e novas capacidades, como a gestão de grupos e o reset da palavra-passe de autosserviço, tal como são adicionados. A experiência intuitiva permite que os utilizadores regressem ao trabalho mais rápido e sejam mais produtivos, reduzindo ao mesmo tempo a sua frustração.

**Aumenta a produtividade**: Todas as aplicações do utilizador no Painel de Acesso têm SSO ativado. Permitir o SSO através de aplicações empresariais e o Office 365 cria uma experiência de inscrição superior, reduzindo ou eliminando pedidos adicionais de inscrição. O Painel de Acesso utiliza o self-service e a adesão dinâmica e melhora a segurança geral do seu sistema de identidade. Fá-lo garantindo que as pessoas certas consigam o acesso às aplicações. O Painel de Acesso serve como uma página de aterragem coerente para que encontre rapidamente recursos e continue as tarefas de trabalho.

**Custo de gestão**: Permitir o Painel de Acesso com Azure AD pode ajudar na alienação de infraestruturas no local. Reduz os custos de suporte fornecendo-lhe um portal consistente para encontrar todas as suas apps, solicitar acesso a recursos e gerir contas.

**Aumenta a flexibilidade e a segurança**: O Painel de Acesso dá-lhe acesso à segurança e flexibilidade que uma plataforma em nuvem proporciona. Os administradores podem facilmente alterar as configurações para aplicações e recursos e podem acomodar novos requisitos de segurança sem afetar os utilizadores.

**Permite uma auditoria e rastreio robustos de auditoria e utilização:** Auditar e rastrear todas as capacidades do utilizador informe-o quando os utilizadores estão a utilizar os seus recursos e garante que pode avaliar a segurança.

### <a name="licensing-considerations"></a>Considerações de licenciamento

O Painel de Acesso é gratuito e não requer licenças para usar a um nível básico. No entanto, o número de objetos no seu diretório e as funcionalidades adicionais que pretende implementar podem requerer licenças adicionais. Alguns cenários comuns de AD Azure que têm requisitos de licenciamento incluem as seguintes funcionalidades de segurança:

* [Multi-Factor Authentication do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Adesão baseada em grupo](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Reset da palavra-passe de autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Proteção de Identidade do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Consulte o [guia de licenciamento completo para Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Pré-requisitos para a implantação do Painel de Acesso AD Azure

Complete os seguintes pré-requisitos antes de iniciar este projeto:

* [Integrar aplicação SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Gerir o utilizador Azure AD e a infraestrutura de grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Planear a implantação do Painel de Acesso AD Azure

O quadro que se segue descreve os principais casos de utilização para uma implantação do Painel de Acesso:

| Área| Description |
| - | - |
| Access| O portal Access Panel está acessível a partir de dispositivos corporativos e pessoais dentro da rede corporativa. |
|Access | O portal Access Panel está acessível a partir de dispositivos corporativos fora da rede corporativa. |
| Auditoria| Os dados de utilização são descarregados em sistemas corporativos pelo menos a cada 29 dias. |
| Governação| O ciclo de vida das atribuições do utilizador a aplicações e grupos ligados à Azure AD é definido e monitorizado. |
| Segurança| O acesso aos recursos é controlado através de atribuições de utilizador e grupo. Apenas os utilizadores autorizados podem gerir o acesso a recursos. |
| Desempenho| Os prazos de propagação da atribuição de acesso são documentados e monitorizados. |
| Experiência do Utilizador| Os utilizadores estão cientes das capacidades do Painel de Acesso e como usá-las.|
| Experiência do Utilizador| Os utilizadores podem gerir o seu acesso a aplicações e grupos.|
| Experiência do Utilizador| Os utilizadores podem gerir as suas contas. |
| Experiência do Utilizador| Os utilizadores estão cientes da compatibilidade do navegador. |
| Suporte| Os utilizadores podem encontrar suporte para problemas do Painel de Acesso. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Melhores práticas para a implementação do Painel de Acesso AD Azure

A funcionalidade do Painel de Acesso pode ser ativada gradualmente. Recomendamos a seguinte ordem de implantação:

1. As Minhas Aplicações
   * Lançador de aplicativos
   * Gestão de aplicativos self-service
   * Integração do Microsoft Office 365

1. Descoberta de aplicativo de self-service
   * Reposição personalizada de palavra-passe
   * Definições de autenticação multi-factor
   * Gestão de dispositivos
   * Termos de utilização
   * Gerir organizações

1. Os meus grupos
   * Gestão de grupos self-service
1. Revisões de acesso
   * Gestão de revisão de acessos

A partir das Minhas Apps introduz os utilizadores no portal como um local comum para aceder a recursos. A adição de uma descoberta de aplicações de self-service baseia-se na experiência My Apps. Os meus grupos e as avaliações de acesso baseiam-se nas capacidades de autosserviço.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Configurações do plano para o Painel de Acesso AD Azure

A tabela a seguir lista várias configurações importantes do Painel de Acesso e os valores típicos que pode utilizar:

| Configuração| Valores típicos |
| - | - |
| Determinar os grupos-piloto| Identifique o grupo de segurança Azure AD para ser utilizado e certifique-se de que todos os membros piloto fazem parte do grupo. |
| Determinar o grupo ou grupos a habilitar para a produção.| Identifique os grupos de segurança AZure AD, ou os grupos de Diretório Ativo sincronizados com a Azure AD, a serem utilizados. Certifique-se de que todos os membros piloto fazem parte do grupo. |
| Permitir que os utilizadores utilizem SSO para determinados tipos de aplicações| SSO Federado, OAuth, Password SSO, App Proxy |
| Permitir que os utilizadores utilizem o reset da palavra-passe de autosserviço | Yes |
| Permitir que os utilizadores utilizem a autenticação multi-factor| Yes |
| Permitir que os utilizadores utilizem a gestão do grupo self-service para determinados tipos de grupos| Grupos de segurança, Escritório 365 grupos |
| Permitir que os utilizadores utilizem a gestão de aplicações self-service| Yes |
| Permitir que os utilizadores utilizem comentários de acesso| Yes |

### <a name="plan-consent-strategy"></a>Estratégia de consentimento do plano

Os utilizadores ou administradores devem consentir com os termos de uso e políticas de privacidade de qualquer aplicação. Se possível, dadas as suas regras de negócio, use o consentimento do administrador, o que dá aos utilizadores uma melhor experiência.

Para utilizar o consentimento do administrador, tem de ser um administrador global da organização, e as aplicações devem ser:

* Registado na sua organização

* Registado em outra organização da AD Azure e previamente consentido por pelo menos um utilizador

Para obter mais informações, consulte [Configure a forma como os utilizadores finais concordam com uma aplicação no Azure Ative Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido às expectativas desajustadas sobre o impacto, os resultados e as responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md) e que as partes interessadas no projeto são bem compreendidas.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe proativamente os seus utilizadores como e quando a sua experiência mudará e como obter suporte, se necessário.

Embora o Painel de Acesso não crie normalmente problemas de utilização, é importante preparar. Crie guias e uma lista de todos os recursos para o seu pessoal de apoio antes do seu lançamento.

#### <a name="communications-templates"></a>Modelos de comunicações

A Microsoft fornece [modelos personalizáveis para e-mails e outras comunicações](https://aka.ms/APTemplates) para o Painel de Acesso. Pode adaptar estes ativos para uso em outros canais de comunicações, conforme apropriado para a sua cultura corporativa.

## <a name="plan-your-sso-configuration"></a>Planeie a sua configuração SSO

Quando um utilizador entra numa aplicação, passa por um processo de autenticação e é obrigado a provar quem é. Sem SSO, uma palavra-passe é armazenada na aplicação, sendo necessário que o utilizador saiba esta palavra-passe. Com o SSO, as credenciais dos utilizadores são passadas para a aplicação, pelo que não precisam de reentrar em palavras-passe para cada aplicação.

Para lançar aplicações nas Minhas Apps, o SSO tem de estar ativado.

A Azure AD suporta três formas diferentes de permitir um [único sign-on em aplicações](what-is-single-sign-on.md):

* **Inscrição única federada** 
    * Permite que uma aplicação redirecione para Azure AD para autenticação do utilizador, em vez de solicitar uma palavra-passe. 
    * É suportado para aplicações que usam protocolos, tais como SAML 2.0, WS-Federation, ou OpenID Connect, e é o modo mais rico de um único sign-on.

* **S-base único baseado em palavra-passe** 
    * Permite o armazenamento e repetição de passwords de aplicação seguras utilizando uma extensão do navegador web ou uma aplicação móvel. 
    * Aproveita o processo de inscrição existente fornecido pela aplicação, mas permite que um administrador gere as palavras-passe. O utilizador não é obrigado a saber a senha.

* **Inscrição única existente** 
    * Permite que o Azure AD aproveite qualquer sinal único existente que tenha sido configurado para a aplicação.
    * Permite que estas aplicações sejam ligadas aos portais do Office 365 ou Azure AD Access Panel. 
    * Permite relatórios adicionais em Azure AD quando as aplicações são lançadas lá. 
    * Inclui a utilização do Azure Application Proxy e o modo de inscrição único ligado.

Saiba como configurar o modo SSO de uma aplicação aqui: [Único s-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Para obter a melhor experiência com a página My Apps, comece pela integração de aplicações em nuvem que estão disponíveis para SSO federado. O SSO federado permite que os utilizadores tenham uma experiência consistente de um clique em todas as suas superfícies de lançamento de apps e tendem a ser mais robustos no controlo de configuração.

Utilize SSO Federado com Azure AD (OpenID Connect/SAML) quando uma aplicação o suporta, em vez de SSO e ADFS baseados em palavra-passe.

Para obter mais informações sobre como implementar e configurar as suas aplicações SaaS, consulte o [plano de implantação do SaaS SSO](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>Plano para implementar a extensão do navegador My Apps

Quando os utilizadores iniciarem sso com base em palavras-passe, precisam de instalar e utilizar a extensão de entrada segura das Minhas Apps. A extensão executa um script que transmite a palavra-passe no formulário de entrada da aplicação. Os utilizadores são solicitados a instalar a extensão quando lançarem pela primeira vez a aplicação SSO baseada em palavra-passe. Mais informações sobre a extensão podem ser encontradas nesta documentação sobre [a instalação da extensão do navegador Access Panel.](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)

Se tiver de integrar aplicações SSO baseadas em palavras-passe, deve definir um mecanismo para implantar a extensão em escala com [navegadores suportados.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) As opções incluem:

* [Política de Grupo para Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Gestor de configuração para Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Descarregamento e configuração orientados pelo utilizador para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Saiba mais: [Como configurar a palavra-passe de um único sinal](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Os utilizadores que não usam aplicações SSO baseadas em palavras-passe também beneficiam da extensão. Estes benefícios incluem a capacidade de lançar qualquer app a partir da sua barra de pesquisa, encontrar acesso a aplicações recentemente utilizadas e ter um link para a página My Apps.

Aqui está o que o utilizador verá ao lançar pela primeira vez uma aplicação SSO baseada em palavra-passe:

![Screenshot da extensão do navegador My Apps instalar ecrã ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Plano de acesso móvel

Um navegador protegido com a política do Intune (Microsoft Edge ou Intune Managed Browser) é necessário para os utilizadores móveis que lançam aplicações SSO baseadas em palavras-passe. Um navegador protegido por políticas permite a transferência da palavra-passe guardada para a aplicação. O Microsoft Edge ou o navegador gerido fornece um conjunto de funcionalidades de proteção de dados web. Também pode utilizar o Microsoft Edge para cenários empresariais em dispositivos iOS e Android. O Microsoft Edge suporta os mesmos cenários de gestão que o Navegador Gerido Intune e melhora a experiência do utilizador. Saiba mais: [Gerir o acesso à Web utilizando um navegador protegido por políticas do Microsoft Intune.](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Planeie a sua implementação de aplicações

A fundação do Painel de Acesso é o lançador de aplicações My Apps, a que os utilizadores [https://myapps.microsoft.com](https://myapps.microsoft.com/) acedem. As páginas My Apps dão aos utilizadores um único lugar para iniciarem o seu trabalho e chegarem às aplicações necessárias. Aqui, os utilizadores encontram uma lista de todas as aplicações a que têm acesso único. 

![Uma imagem do painel de aplicações](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

As mesmas aplicações serão apresentadas no lançador de aplicações do Office 365 quando os utilizadores estiverem a utilizar o portal Office 365.

Planeie a ordem na qual irá adicionar aplicações ao launcher My Apps e decida se irá lançá-las gradualmente ou todas de uma só vez. Para tal, crie um inventário de aplicações que liste o tipo de autenticação e quaisquer integrações SSO existentes para cada aplicação.

#### <a name="add-applications-to-the-my-apps-panel"></a>Adicionar aplicações ao painel My Apps

Qualquer aplicação ativada por Azure AD SSO pode ser adicionada ao lançador My Apps. Outras aplicações são adicionadas utilizando a opção SSO ligada. Pode configurar um azulejo de aplicação que se liga ao URL da sua aplicação web existente. O SSO ligado permite-lhe começar a direcionar os utilizadores para o portal My Apps sem migrar todas as aplicações para Azure AD SSO. Pode mover-se gradualmente para aplicações configuradas pelo Azure AD SSO sem perturbar a experiência dos utilizadores.

#### <a name="use-my-apps-collections"></a>Use as coleções My Apps

Por predefinição, todas as aplicações estão listadas numa única página. Mas pode usar coleções para agrupar aplicações relacionadas e apresentá-las num separador, tornando-as mais fáceis de encontrar. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicações para papéis de trabalho específicos, tarefas, projetos, e assim por diante. Para obter informações, consulte [como utilizar as coleções My Apps para personalizar painéis de acesso ao utilizador.](access-panel-collections.md) 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Planeie se usar as Minhas Apps ou um portal existente

Os seus utilizadores podem já ter uma aplicação ou portal diferente das Minhas Apps. Em caso afirmativo, decida se suporta ambos os portais ou se utiliza apenas um.

Se um portal já existente já estiver a ser utilizado como ponto de partida para os utilizadores, pode integrar a funcionalidade My Apps utilizando URLs de acesso ao utilizador. Os URLs de acesso ao utilizador funcionam como ligações diretas às aplicações disponíveis no portal My Apps. Estes URLs podem ser incorporados em qualquer website existente. Quando um utilizador seleciona o link, abre a aplicação a partir do portal My Apps.

Pode encontrar a propriedade URL de acesso ao utilizador na área **de Propriedades** da aplicação no portal Azure.

![Uma imagem do painel de aplicações](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Planear a descoberta e acesso a aplicações de autosserviço

Uma vez implementado um conjunto de aplicações principais para a página My Apps de um utilizador, deverá ativar funcionalidades de gestão de aplicações de self-service. A descoberta de aplicativos de self-service permite que os utilizadores:

* Encontre novas aplicações para adicionar às suas Aplicações My. 
* Adicione aplicações opcionais que podem não saber que precisam durante a configuração.

Fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para aplicações de acesso. Os utilizadores que sejam aprovadores receberão notificações dentro do portal My Apps quando houver pedido de acesso à aplicação.

## <a name="plan-self-service-group-membership"></a>Plano de adesão ao grupo de self-service 

Pode permitir aos utilizadores criar e gerir os seus próprios grupos de segurança ou grupos office 365 em Azure AD. O proprietário do grupo pode aprovar ou negar pedidos de adesão e delegar o controlo da adesão ao grupo. As funcionalidades de gestão do grupo self-service não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas por correio.

Para planear a adesão ao grupo de self-service, determine se permitirá que todos os utilizadores da sua organização criem e gerem grupos ou apenas um subconjunto de utilizadores. Se está a permitir um subconjunto de utilizadores, terá de criar um grupo ao qual essas pessoas são adicionadas. Consulte [Configurar a gestão do grupo self-service no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para obter detalhes sobre como permitir estes cenários.

## <a name="plan-reporting-and-auditing"></a>Relatórios e auditorias do plano

A Azure AD fornece [relatórios que oferecem insights técnicos e empresariais.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) Trabalhe com os seus empresários e gestores de aplicações técnicas para assumir a propriedade destes relatórios e consumi-los regularmente. A tabela seguinte fornece alguns exemplos de cenários típicos de reporte.

|   | Faça a gestão do risco.| Aumentar a produtividade| Governação e conformidade |
|  - |- | - | - |
| Tipos de relatório|  Permissões de aplicação e utilização| Atividade de provisionamento de conta| Rever quem está a aceder às aplicações |
| Ações potenciais| Acesso à auditoria; revogar permissões| Remediar quaisquer erros de provisionamento| Revogar o acesso |

A Azure AD mantém a maioria dos dados de auditoria durante 30 dias. Os dados estão disponíveis através do Azure Admin Portal ou API para que possa descarregar nos seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os registos de auditoria para acesso à aplicação estão disponíveis por 30 dias. Se a auditoria de segurança dentro da sua empresa necessitar de uma retenção mais longa, os registos precisam de ser exportados para uma ferramenta de Evento e Gestão de Informação de Segurança (SIEM), como a Splunk ou a ArcSight.

Para auditamentos, relatórios e backups de recuperação de desastres, documente a frequência necessária de download, qual é o sistema-alvo e quem é o responsável pela gestão de cada backup. Pode não precisar de auditorias separadas e de relatórios de backups. A sua cópia de segurança de recuperação de desastres deve ser uma entidade separada.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Implementar aplicações para o painel My Apps dos utilizadores

Depois de uma aplicação ter sido configurada para SSO, os grupos são atribuídos ao acesso. Os utilizadores dos grupos designados terão acesso e verão a aplicação nas suas Apps e no launcher da aplicação Office 365.

Consulte [atribuir utilizadores e grupos a uma aplicação no Ative Directory](methods-for-assigning-users-and-groups.md).

Se durante o teste ou implementação pretender adicionar os grupos mas ainda não permitir que as aplicações apareçam nas Minhas Apps, consulte [Ocultar uma aplicação da experiência do utilizador no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Implementar aplicações do Microsoft Office 365 para as minhas apps

Para aplicações do Office 365, os utilizadores recebem uma cópia do Office com base nas licenças que lhes são atribuídas. Um pré-requisito para o acesso às aplicações do Office é que os utilizadores sejam atribuídos as licenças corretas ligadas às aplicações do Office. Quando atribuir uma licença a um utilizador, verão automaticamente as aplicações que estão associadas à licença na sua página My Apps e no lançador de aplicações do Office 365.

Se pretender ocultar um conjunto de aplicações do Office dos utilizadores, existe uma opção para ocultar aplicações do portal My Apps, ao mesmo tempo que permite o acesso a partir do portal do Office 365. Encontre estas definições na parte das definições do Utilizador da aplicação. Saiba mais: [Esconda uma aplicação da experiência do utilizador no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Uma imagem de configuração de como esconder aplicações](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Implementar capacidades de autosserviço de aplicação

O acesso a aplicações self-service permite que os utilizadores se auto-descubram e solicitem o acesso às aplicações. Os utilizadores têm a liberdade de aceder às aplicações de que necessitam sem passar por um grupo de TI de cada vez para solicitar o acesso. Quando um utilizador solicita acesso e é aprovado, automaticamente ou manualmente por um proprietário de uma aplicação, é adicionado a um grupo na parte traseira. A informação está ativada sobre quem solicitou, aprovou ou removeu o acesso, e dá-lhe controlo sobre a gestão das funções atribuídas.

Pode delegar a aprovação dos pedidos de acesso à aplicação aos aprovadores empresariais. O aprovador do negócio pode definir as palavras-passe de acesso à aplicação a partir da página My Apps do aprovador do negócio.

Saiba mais: [Como utilizar o acesso a aplicações self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Uma imagem de configuração da gestão de aplicações de self-service](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Validar a sua implantação

Certifique-se de que a sua implementação do Painel de Acesso está completamente testada e que está em vigor um plano de reversão.

Os seguintes testes devem ser realizados com dispositivos corporativos e dispositivos pessoais. Estes casos de teste também devem refletir os casos de uso do seu negócio. Seguem-se alguns casos baseados nos requisitos de negócio da amostra neste documento e em cenários técnicos típicos. Adicione outros específicos às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Exemplos de casos de teste de acesso SSO de aplicação:


| Caso de negócios| Resultado esperado |
| - | -|
| O utilizador entra no portal My Apps| O utilizador pode iniciar sômposições e ver as suas aplicações |
| Utilizador lança aplicação SSO federada| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança pela primeira vez uma aplicação SSO de palavra-passe| O utilizador precisa de instalar a extensão My Apps |
| Utilizador lança uma aplicação SSO de senha num tempo subsequente| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança uma aplicação do Portal 365 do Office 365| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança uma aplicação a partir do Navegador Gerido| O utilizador é automaticamente inscrito na aplicação |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Aplicação de capacidades de autosserviço testam exemplos de caso


| Caso de negócios| Resultado esperado |
| - | - |
| O utilizador pode gerir a adesão à aplicação| O utilizador pode adicionar/remover membros que tenham acesso à app |
| O utilizador pode editar a aplicação| O utilizador pode editar a descrição e credenciais da aplicação para aplicações SSO de palavra-passe |

### <a name="rollback-steps"></a>Passos de reversão

É importante planear o que fazer se a sua implantação não correr como planeado. Se a configuração SSO falhar durante a implementação, deve entender como [resolver problemas de SSO](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) e reduzir o impacto para os seus utilizadores. Em circunstâncias extremas, talvez precise de reverter o [SSO.](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>Gerir a sua implementação

Deve utilizar o papel menos privilegiado para realizar uma tarefa necessária no Diretório Azure Ative. [Reveja as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolha a certa para resolver as suas necessidades para cada persona para esta aplicação. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a colocação concluída.

| Pessoas fictícias| Funções| Papel de AD AZure  |
| - | -| -|
| Administração Helpdesk| Suporte de nível 1| Nenhum |
| Administrador de identidade| Configurar e depurar quando as questões impactam Azure AD| Administrador global |
| Administrador de candidatura| Atestado de utilizador na aplicação, configuração em utilizadores com permissões| Nenhum |
| Administradores de infraestruturas| Cert proprietário de capotamento| Administrador global |
| Empresário/stakeholder| Atestado de utilizador na aplicação, configuração em utilizadores com permissões| Nenhum |

Pode utilizar a [Gestão de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) para gerir as suas funções para fornecer auditoria adicional, controlo e revisão de acesso aos utilizadores com permissões de diretório.

### <a name="troubleshoot-access-panel-issues"></a>Problemas no Painel de Acesso de Resolução de Problemas

Crie guias de resolução de problemas para a sua organização de suporte com cenários comuns, que apontam para a documentação da Microsoft nas suas resoluções. É melhor criar guias que rompam o suporte nos níveis utilizados pela sua organização.

Consulte estes guias de resolução de problemas para obter referência:

[Candidaturas não aparecendo](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Aplicações inesperadas que aparecem](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[O utilizador não pode iniciar sedução no Painel de Acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problemas na utilização do acesso a aplicações self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[Problemas com a extensão do navegador](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>Passos seguintes

[Planear uma implantação da Autenticação Multi-Factor Azure](https://aka.ms/deploymentplans/mfa)
