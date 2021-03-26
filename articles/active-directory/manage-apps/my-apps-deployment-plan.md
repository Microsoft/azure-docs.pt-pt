---
title: Plan Azure Ative Directory My Apps configuração
description: Guia de planeamento para utilizar eficazmente as Minhas Apps na sua organização.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 5184639d8c34be705aeeb691f1cf38486f850673
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543963"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>Plan Azure Ative Directory My Apps configuração

> [!NOTE]
> Este artigo é projetado para profissionais de TI que precisam planear a configuração do portal My Apps da sua organização. 
>
> **Para obter documentação do utilizador final, consulte [iniciar sôm e inicie aplicações a partir do portal My Apps](../user-help/my-apps-portal-end-user-access.md)**.

Azure Ative Directory (Azure AD) My Apps é um portal baseado na web para o lançamento e gestão de apps. A página My Apps dá aos utilizadores um único lugar para iniciarem o seu trabalho e encontrarem todas as aplicações a que têm acesso. Os utilizadores acedem às Minhas Apps em [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>Por que Configure as minhas apps

O portal My Apps encontra-se disponível para os utilizadores por padrão e não pode ser desligado. É importante configubá-lo para que tenham a melhor experiência possível, e o portal permanece útil. 

Qualquer aplicação na lista de aplicações da empresa Azure Ative Directory aparece quando ambas as seguintes condições são satisfeitas:

* A propriedade de visibilidade para a app está definida como verdadeira. 

* A aplicação é atribuída a qualquer utilizador ou grupo. Parece para utilizadores designados.

Configurar o portal garante que as pessoas certas podem facilmente encontrar as aplicações certas.

 
### <a name="how-is-the-my-apps-portal-used"></a>Como é utilizado o portal My Apps?

Os utilizadores acedem ao portal My Apps para:

* Descubra e aceda a todas as aplicações ligadas ao Azure AD da sua organização às quais têm acesso.

   * É melhor garantir que as aplicações são configuradas para um único sign-on (SSO) para proporcionar aos utilizadores a melhor experiência.

* Solicite acesso a novas aplicações configuradas para auto-atendimento.

* Crie coleções pessoais de apps.

* Gerir o acesso a apps para outros quando lhe for atribuído o papel de proprietário do grupo ou controlo delegado para o grupo utilizado para conceder acesso às aplicações.

Os administradores podem configurar:

* [Experiências de consentimento,](../manage-apps/configure-user-consent.md)  incluindo termos de serviço.

* [Pedidos de descoberta e acesso de aplicações de autosserviço.](../manage-apps/access-panel-manage-self-service-access.md)

* [Coleções de aplicações.](../manage-apps/access-panel-collections.md)

* Atribuição de ícones a aplicações

* Nomes fáceis de utilizar para aplicações

* Marca da empresa mostrada nas Minhas Apps

 

## <a name="plan-consent-configuration"></a>Configuração do consentimento do plano

### <a name="user-consent-for-applications"></a>Consentimento do utilizador para aplicações 

Antes que um utilizador possa iniciar seduca a uma aplicação e a aplicação possa aceder aos dados da sua organização, um utilizador ou um administrador deve conceder as permissões de aplicação. Pode configurar se o consentimento do utilizador é permitido e em que condições. **A Microsoft recomenda que apenas permita o consentimento do utilizador para aplicações de editores verificados.**

Para mais informações, consulte [Configure como os utilizadores finais concordam com as aplicações](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>Consentimento do proprietário do grupo para apps que acedem aos dados

Os proprietários de grupos e equipas podem autorizar aplicações, como aplicações publicadas por fornecedores de terceiros, a aceder aos dados da sua organização associados a um grupo. Consulte [o consentimento específico do recurso nas Equipas da Microsoft](https://docs.microsoft.com/microsoftteams/resource-specific-consent) para saber mais. 

Pode configurar se pretende permitir ou desativar esta funcionalidade.

Para obter mais informações, consulte [as permissões de consentimento do grupo Configure](../manage-apps/configure-user-consent-groups.md).

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Informe proativamente os seus utilizadores como e quando a sua experiência mudará e como obter suporte, se necessário.

Embora as minhas apps não criem problemas de utilizadores, é importante preparar. Crie guias e uma lista de todos os recursos para o seu pessoal de apoio antes do seu lançamento.

#### <a name="communications-templates"></a>Modelos de comunicações

A Microsoft fornece [modelos personalizáveis para e-mails e outras comunicações](https://aka.ms/APTemplates) para as Minhas Apps. Pode adaptar estes ativos para uso em outros canais de comunicações, conforme apropriado para a sua cultura corporativa.

 

## <a name="plan-your-sso-configuration"></a>Planeie a sua configuração SSO

É melhor que o SSO esteja ativado para todas as aplicações no portal My Apps para que os utilizadores tenham uma experiência perfeita sem necessidade de introduzir as suas credenciais.

A Azure AD suporta várias opções SSO. 

* Para saber mais, consulte [as opções de inscrição única no Azure AD](sso-options.md).

* Para saber mais sobre a utilização do AZure AD como fornecedor de identidade para uma aplicação, consulte a [Quickstart Series on Application Management](../manage-apps/view-applications-portal.md).

### <a name="use-federated-sso-if-possible"></a>Utilizar SSO federado se possível

Para obter a melhor experiência do utilizador com a página My Apps, comece pela integração de aplicações em nuvem disponíveis para SSO federado (OpenID Connect ou SAML). O SSO federado permite que os utilizadores tenham uma experiência consistente de um clique em todas as superfícies de lançamento de apps e tendem a ser mais robustos no controlo de configuração.

Para obter mais informações sobre como configurar o seu software como aplicações de serviço (SaaS) para SSO, consulte o plano de implementação [SaaS SSO].. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>Considerações para circunstâncias especiais do SSO

> [!TIP]
> Para uma melhor experiência de utilizador, utilize SSO Federado com AZure AD (OpenID Connect/SAML) quando uma aplicação o suporta, em vez de SSO e ADFS baseados em palavra-passe.

Para iniciar sômedagem nas aplicações SSO baseadas em palavras-passe ou para aplicações que são acedidas pelo Azure AD Application Proxy, os utilizadores precisam de instalar e utilizar a extensão de entrada segura das Minhas Apps. Os utilizadores são solicitados a instalar a extensão quando lançarem pela primeira vez a aplicação SSO ou Application Proxy baseada em palavra-passe. 

![Screenshot de](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

Para obter informações detalhadas sobre a extensão, consulte [instalar a extensão do navegador My Apps](../user-help/my-apps-portal-end-user-access.md).

Se tiver de integrar estas aplicações, deverá definir um mecanismo para implantar a extensão em escala com [navegadores suportados.](../user-help/my-apps-portal-end-user-access.md) As opções incluem:

* [Descarregamento e configuração orientados pelo utilizador para Chrome, Firefox, Microsoft Edge ou IE](../user-help/my-apps-portal-end-user-access.md)

* [Gestor de configuração para Internet Explorer](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

A extensão permite que os utilizadores lancem qualquer app a partir da sua barra de pesquisa, encontrando acesso a aplicações recentemente utilizadas, e tendo um link para a página My Apps.

![Screenshot da pesquisa de extensão de aplicativos](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>Plano de acesso móvel

Para aplicações que utilizem SSO baseado em palavras-passe ou acedidas através do [Microsoft Azure AD Application Proxy](../manage-apps/application-proxy.md), tem de utilizar o microsoft Edge mobile. Para outras aplicações, qualquer navegador móvel pode ser usado. 

### <a name="linked-sso"></a>SSO ligado

As aplicações podem ser adicionadas utilizando a opção SSO ligada. Pode configurar um azulejo de aplicação que se liga ao URL da sua aplicação web existente. O SSO ligado permite-lhe começar a direcionar os utilizadores para o portal My Apps sem migrar todas as aplicações para Azure AD SSO. Pode mover-se gradualmente para aplicações configuradas pelo Azure AD SSO sem perturbar a experiência dos utilizadores.

## <a name="plan-the-user-experience"></a>Planeie a experiência do utilizador

Por predefinição, todas as aplicações a que o utilizador tem acesso e todas as aplicações configuradas para a descoberta de self-service aparecem no painel My Apps do utilizador. Para muitas organizações, esta pode ser uma lista muito grande, que pode tornar-se onerosa se não organizada

### <a name="plan-my-apps-collections"></a>Planeie as coleções de Aplicações do Plano

Todas as aplicações AD AZure a que um utilizador tem acesso aparecerão nas Minhas Apps na coleção All Apps. Utilize coleções para agrupar aplicações relacionadas e apresentá-las num separador, facilitando a sua mente. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicações para papéis de trabalho específicos, tarefas, projetos, e assim por diante. 

Os utilizadores finais também podem personalizar a sua experiência através

* Criando as suas próprias coleções de aplicações.

* [Ocultar e reordenar coleções de aplicações.](access-panel-collections.md)

![Screenshot da configuração de self-service](./media/my-apps-deployment-plan/collections.png)

Existe uma opção para ocultar aplicações a partir do portal My Apps, ao mesmo tempo que permite o acesso a partir de outros locais, como é o caso do portal Microsoft 365. Saiba mais: [Esconda uma aplicação da experiência do utilizador no Azure Ative Directory](hide-application-from-user-portal.md).

> [!IMPORTANT]
> Apenas 950 aplicações a que um utilizador tem acesso podem ser acedidas através das Minhas Apps. Isto inclui aplicações ocultas pelo utilizador ou pelo administrador. 

### <a name="plan-self-service-group-management-membership"></a>Plano de gestão de grupo de self-service

Pode permitir que os utilizadores criem e gerem os seus próprios grupos de segurança ou grupos Microsoft 365 em Azure AD. O proprietário do grupo pode aprovar ou negar pedidos de adesão e delegar o controlo da adesão ao grupo. As funcionalidades de gestão do grupo self-service não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas por correio.

Para planear a adesão ao grupo de self-service, determine se permitirá que todos os utilizadores da sua organização criem e gerem grupos ou apenas um subconjunto de utilizadores. Se está a permitir um subconjunto de utilizadores, terá de criar um grupo ao qual essas pessoas são adicionadas. 

Consulte [Configurar a gestão do grupo self-service no Azure Ative Directory](../enterprise-users/groups-self-service-management.md) para obter detalhes sobre como permitir estes cenários.

### <a name="plan-self-service-application-access"></a>Planear o acesso a aplicações de autosserviço

Pode permitir que os utilizadores descubram e solicitem acesso às aplicações através do painel My Apps. Para isso, tem de ser primeiro. 

* permitir a gestão do grupo self-service

* ativar aplicativo para SSO

* criar um grupo para acesso a aplicações

![Imagem de tela da configuração do serviço de self service my Apps](./media/my-apps-deployment-plan/my-apps-self-service.png)

Quando os utilizadores solicitam acesso, estão a solicitar acesso ao grupo subjacente, e os proprietários do grupo podem ser permissão de delegado para gerir a filiação do grupo e, assim, o acesso à aplicação. Fluxos de trabalho de aprovação estão disponíveis para aprovação explícita para aplicações de acesso. Os utilizadores que sejam aprovadores receberão notificações dentro do portal My Apps quando houver pedido de acesso à aplicação.

## <a name="plan-reporting-and-auditing"></a>Relatórios e auditorias do plano

A Azure AD fornece [relatórios que oferecem insights técnicos e empresariais].. /reports-monitoring/visão geral-reports.md). Trabalhe com os seus empresários e gestores de aplicações técnicas para assumir a propriedade destes relatórios e consumi-los regularmente. A tabela seguinte fornece alguns exemplos de cenários típicos de reporte.

| Exemplo| Faça a gestão do risco.| Aumentar a produtividade| Governação e conformidade |
| - | - | - | -|
| Tipos de relatório| Permissões de aplicação e utilização| Atividade de provisionamento de conta| Rever quem está a aceder às aplicações |
| Ações potenciais| Acesso à auditoria; revogar permissões| Remediar quaisquer erros de provisionamento| Revogar o acesso |


A Azure AD mantém a maioria dos dados de auditoria durante 30 dias. Os dados estão disponíveis através do Azure Admin Portal ou API para que possa descarregar nos seus sistemas de análise.

#### <a name="auditing"></a>Auditoria

Os registos de auditoria para acesso à aplicação estão disponíveis por 30 dias. Se a sua organização necessitar de uma retenção mais longa, exporte os registos para uma ferramenta de Evento e Gestão de Informação de Segurança (SIEM), como a Splunk ou a ArcSight.

Para auditamentos, relatórios e backups de recuperação de desastres, documente a frequência necessária de download, qual é o sistema-alvo e quem é o responsável pela gestão de cada backup. Pode não precisar de auditorias separadas e de relatórios de backups. A sua cópia de segurança de recuperação de desastres deve ser uma entidade separada.

## <a name="validate-your-deployment"></a>Validar a sua implantação

Certifique-se de que a sua implementação my Apps está completamente testada e que está em vigor um plano de reversão.

Realizar os seguintes testes com dispositivos corporativos e dispositivos pessoais. Estes casos de teste também devem refletir os casos de uso do seu negócio. Seguem-se alguns casos baseados em cenários técnicos típicos. Adicione outros específicos às suas necessidades.

#### <a name="application-sso-access-test-case-examples"></a>Exemplos de casos de teste de acesso SSO de aplicação:


| Caso de negócios| Resultado esperado |
| - | - |
| O utilizador entra no portal My Apps| O utilizador pode iniciar sômposições e ver as suas aplicações |
| Utilizador lança aplicação SSO federada| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança pela primeira vez uma aplicação SSO de palavra-passe| O utilizador precisa de instalar a extensão My Apps |
| Utilizador lança uma aplicação SSO de senha num tempo subsequente| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança uma aplicação a partir do Portal Microsoft 365| O utilizador é automaticamente inscrito na aplicação |
| Utilizador lança uma aplicação a partir do Navegador Gerido| O utilizador é automaticamente inscrito na aplicação |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Aplicação de capacidades de autosserviço testam exemplos de caso


| Caso de negócios| Resultado esperado |
| - | - |
| O utilizador pode gerir a adesão à aplicação| O utilizador pode adicionar/remover membros que tenham acesso à app |
| O utilizador pode editar a aplicação| O utilizador pode editar a descrição e credenciais da aplicação para aplicações SSO de palavra-passe |


### <a name="rollback-steps"></a>Passos de reversão

É importante planear o que fazer se a sua implantação não correr como planeado. Se a configuração SSO falhar durante a implementação, deve entender como [resolver problemas de SSO](../hybrid/tshoot-connect-sso.md) e reduzir o impacto para os seus utilizadores. Em circunstâncias extremas, talvez precise de reverter o [SSO.](plan-sso-deployment.md)

## <a name="manage-your-implementation"></a>Gerir a sua implementação

Utilize o papel menos privilegiado para realizar uma tarefa necessária no Diretório Ativo Azure. [Reveja as diferentes funções disponíveis](../roles/permissions-reference.md) e escolha a certa para resolver as suas necessidades para cada persona para esta aplicação. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a colocação concluída.

| Pessoas fictícias| Funções| Papel de AD AZure |
| - | - | - |
| Administração Helpdesk| Suporte de nível 1| Nenhum |
| Administrador de identidade| Configurar e depurar quando as questões impactam Azure AD| Administrador global |
| Administrador de candidatura| Atestado de utilizador na aplicação, configuração em utilizadores com permissões| Nenhum |
| Administradores de infraestruturas| Cert proprietário de capotamento| Administrador global |
| Empresário/stakeholder| Atestado de utilizador na aplicação, configuração em utilizadores com permissões| Nenhum |


Pode utilizar a [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) para gerir as suas funções para fornecer auditoria adicional, controlo e revisão de acesso aos utilizadores com permissões de diretório.

## <a name="next-steps"></a>Passos seguintes

[Planear uma implantação da Autenticação Multi-Factor AZURE AD](../authentication/howto-mfa-getstarted.md)

[Planear uma implementação do Proxy da Aplicação](application-proxy-deployment-plan.md)

