---
title: Práticas recomendadas para acesso de administrador seguro – Azure AD | Microsoft Docs
description: Verifique se as contas de administrador e acesso administrativo da sua organização estão seguras. Para arquitetos de sistemas e profissionais de ti que configuram o Azure AD, o Azure e o Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063854"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Protegendo o acesso privilegiado para implantações híbridas e na nuvem no Azure AD

A segurança da maioria ou de todos os ativos de negócios na organização moderna depende da integridade das contas privilegiadas que administram e gerenciam sistemas de ti. Atores mal-intencionados, incluindo invasores cibernéticos, geralmente visam contas de administrador e outros elementos de acesso privilegiado para tentar obter acesso rápido a dados e sistemas confidenciais usando ataques de roubo de credenciais. Para serviços de nuvem, prevenção e resposta são as responsabilidades conjuntas do provedor de serviços de nuvem e do cliente. Para obter mais informações sobre as mais recentes ameaças aos pontos finais e à nuvem, consulte o Relatório de [Inteligência de Segurança](https://www.microsoft.com/security/operations/security-intelligence-report)da Microsoft . Este artigo pode ajudá-lo a desenvolver um roteiro para fechar as lacunas entre os planos atuais e as diretrizes descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com padrões e conformidade regulatória. Saiba mais sobre como a equipa de resposta a incidentes globais da Microsoft atenua os efeitos dos ataques contra os serviços na nuvem, e como a segurança é incorporada em produtos de negócios da Microsoft e serviços de nuvem no [Microsoft Trust Center - Security](https://www.microsoft.com/trustcenter/security) e Microsoft compliance targets at Microsoft Trust Center - [Compliance](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Para a maioria das organizações, a segurança dos ativos de negócios depende da integridade das contas privilegiadas que administram e gerenciam sistemas de ti. Invasores cibernéticos se concentram no acesso privilegiado a sistemas de infraestrutura (como Active Directory e Azure Active Directory) para obter acesso aos dados confidenciais de uma organização. 

As abordagens tradicionais que se concentram em proteger os pontos de entrada e saída de uma rede como o perímetro de segurança principal são menos eficazes devido ao aumento no uso de aplicativos SaaS e dispositivos pessoais na Internet. A substituição natural do perímetro de segurança da rede numa empresa moderna complexa é a autenticação e controlos de autorização na camada de identidade de uma organização.

Contas administrativas privilegiadas são efetivamente no controle desse novo "perímetro de segurança". É essencial proteger o acesso privilegiado, independentemente de o ambiente estar no local, na nuvem ou em serviços hospedados na nuvem e no local. Proteger o acesso administrativo contra determinados adversários exige que você faça uma abordagem completa e cuidadosa para isolar os sistemas de sua organização contra riscos. 

Proteger o acesso privilegiado requer alterações em

* Processos, práticas administrativas e gerenciamento de conhecimento
* Componentes técnicos, como defesas de host, proteções de conta e gerenciamento de identidade

Este documento se concentra principalmente na criação de um roteiro para proteger identidades e acessos que são gerenciados ou relatados no Azure AD, Microsoft Azure, Office 365 e outros serviços de nuvem. Para as organizações que tenham contas administrativas no local, consulte as orientações para o acesso privilegiado no local e híbrido gerido a partir do Diretório Ativo na Garantia de [Acesso Privilegiado.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) 

> [!NOTE] 
> As diretrizes neste artigo referem-se principalmente a recursos de Azure Active Directory incluídos nos planos de Azure Active Directory Premium P1 e P2. O Azure Active Directory Premium P2 está incluído no pacote do EMS E5 e Microsoft 365 o pacote e5. Este guia pressupõe que sua organização já tenha Azure AD Premium licenças P2 adquiridas para seus usuários. Se você não tiver essas licenças, algumas das diretrizes talvez não se apliquem à sua organização. Além disso, em todo este artigo, o termo administrador global (ou administrador global) é sinônimo de "administrador da empresa" ou "administrador de locatários".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro 

A Microsoft recomenda que você desenvolva e siga um roteiro para proteger o acesso privilegiado contra invasores cibernéticos. Você sempre pode ajustar seu roteiro para acomodar seus recursos existentes e requisitos específicos em sua organização. Cada estágio do roteiro deve aumentar o custo e a dificuldade dos adversários para atacar o acesso privilegiado para seus ativos locais, de nuvem e híbridos. A Microsoft recomenda os quatro estágios de roteiro a seguir: esse roteiro recomendado agenda as implementações mais eficientes e mais rápidas primeiro, com base nas experiências da Microsoft com a implementação de incidente e resposta de ataque cibernético. Os cronogramas deste roteiro são aproximados.

![Estágios do roteiro com linhas de tempo](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Estágio 1 (24-48 horas): itens críticos que recomendamos que você faça imediatamente

* Estágio 2 (2-4 semanas): reduzir as técnicas de ataque usadas com mais frequência

* Estágio 3 (1-3 meses): criar visibilidade e criar controle total da atividade do administrador

* Estágio 4 (seis meses e além): continuar criando defesas para proteger ainda mais sua plataforma de segurança

Esta estrutura de roteiro foi projetada para maximizar o uso de tecnologias da Microsoft que você já tenha implantado. Você também pode aproveitar as principais tecnologias de segurança atuais e futuras e integrar as ferramentas de segurança de outros fornecedores que você já implantou ou está considerando a implantação. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Etapa 1: itens críticos que recomendamos que você faça imediatamente

![Etapa 1 itens críticos para fazer primeiro](./media/directory-admin-roles-secure/stage-one.png)

O estágio 1 do roteiro se concentra em tarefas críticas que são rápidas e fáceis de implementar. Recomendamos que você faça alguns itens imediatamente dentro das primeiras 24-48 horas para garantir um nível básico de acesso privilegiado seguro. Este estágio do roteiro de acesso privilegiado protegido inclui as seguintes ações:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ativar Azure AD Privileged Identity Management

Se você ainda não tiver ativado Azure AD Privileged Identity Management (PIM), faça isso em seu locatário de produção. Depois de ativar Privileged Identity Management, você receberá mensagens de email de notificação para alterações de função de acesso privilegiado. Essas notificações fornecem um aviso antecipado quando usuários adicionais são adicionados a funções com altos privilégios em seu diretório.

Azure AD Privileged Identity Management está incluído no Azure AD Premium P2 ou EMS e5. Essas soluções ajudam a proteger o acesso a aplicativos e recursos em todo o ambiente local e na nuvem. Se ainda não tiver Azure AD Premium P2 ou EMS E5 e pretender avaliar mais das funcionalidades referidas neste roteiro, [inscreva-se](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)no ensaio gratuito de 90 dias da Enterprise Mobility + Security. Use essas avaliações de licença para experimentar Azure AD Privileged Identity Management e Azure AD Identity Protection para monitorar a atividade usando relatórios de segurança avançados do Azure AD, auditoria e alertas.

Depois de ativar Azure AD Privileged Identity Management:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta que é administradora global do seu inquilino de produção.

2. Para selecionar o locatário no qual você deseja usar Privileged Identity Management, selecione seu nome de usuário no canto superior direito do portal do Azure.

3. No menu do portal Azure, selecione **Todos os serviços** e filtre a lista para a Gestão de Identidade Privilegiada do **Azure AD.**

4. Abra a Gestão de Identidade Privilegiada da lista **de todos os serviços** e coloque-a no seu painel de instrumentos.

A primeira pessoa a utilizar a Azure AD Privileged Identity Management no seu inquilino é automaticamente atribuída ao administrador de **Segurança** e **funções** privilegiadas de administrador no inquilino. Somente administradores de função com privilégios podem gerenciar as atribuições de função de diretório do Azure AD dos usuários. Além disso, depois de adicionar Azure AD Privileged Identity Management, você será mostrado o assistente de segurança que o guiará pela experiência inicial de descoberta e atribuição. Você pode sair do assistente sem fazer nenhuma alteração adicional no momento. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificar e categorizar contas que estão em funções altamente privilegiadas 

Depois de ativar Azure AD Privileged Identity Management, exiba os usuários que estão no administrador global de funções de diretório, administrador de função com privilégios, administrador do Exchange Online e administrador do SharePoint Online. Se não tiver o Azure AD PIM no seu inquilino, pode utilizar a [API PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com a função de administrador global, pois essa função é genérica: um usuário ao qual é atribuída essa função de administrador tem as mesmas permissões em todos os serviços de nuvem para os quais sua organização assinou, independentemente de ter sido atribuída a essa função no Microsoft 365 Centro de administração, o portal do Azure ou usando o módulo do Azure AD para o Microsoft PowerShell. 

Remova todas as contas que não são mais necessárias nessas funções. Em seguida, categorize as contas restantes que são atribuídas a funções de administrador:

* Atribuído individualmente a usuários administrativos e também pode ser usado para fins não administrativos (por exemplo, email pessoal)
* Atribuído individualmente a usuários administrativos e designado apenas para fins administrativos
* Compartilhado entre vários usuários
* Para cenários de acesso de emergência de interrupção
* Para scripts automatizados
* Para usuários externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definir pelo menos duas contas de acesso de emergência 

Certifique-se de que você não tenha uma situação em que possa ser bloqueado inadvertidamente da administração do seu locatário do Azure AD devido a uma incapacidade de entrar ou ativar uma conta de usuário individual existente como administrador. Por exemplo, se a organização for federada para um provedor de identidade local, esse provedor de identidade poderá estar indisponível para que os usuários não possam entrar no local. Você pode mitigar o impacto da falta acidental de acesso administrativo armazenando duas ou mais contas de acesso de emergência em seu locatário.

As contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado em um ambiente de Azure Active Directory existente. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a emergências em cenários de "interrupção" em que as contas administrativas normais não podem ser usadas. As organizações devem garantir o objetivo de controlar e reduzir o uso da conta de emergência para o momento em que for necessário.

Avalie as contas atribuídas ou qualificadas para a função de administrador global. Se você não vê nenhuma conta somente de nuvem usando o domínio *. onmicrosoft.com (destinado ao acesso de emergência "interrupção"), crie-as. Para mais informações, consulte a Gestão de contas administrativas de [acesso de emergência em Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ativar a autenticação multifator e registrar todas as outras contas de administrador não federadas de usuário único com alta privilégios

Exigir a MFA (autenticação multifator) do Azure na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: administrador global, administrador de função com privilégios, administrador do Exchange Online e SharePoint Administrador online. Utilize o guia para ativar a [Autenticação Multifactor (MFA) para as suas contas de administração e certifique-se](../authentication/howto-mfa-userstates.md) de que todos os utilizadores se registaram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mais informações podem ser encontradas sob o passo 2 e passo 3 do guia Proteja o [acesso aos dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etapa 2: reduzir as técnicas de ataque usadas com mais frequência

![Estágio 2 mitigar ataques usados com frequência](./media/directory-admin-roles-secure/stage-two.png)

O estágio 2 do roteiro concentra-se na mitigação das técnicas de ataque usadas com mais frequência de roubo e abuso de credenciais e pode ser implementado em aproximadamente 2-4 semanas. Este estágio do roteiro de acesso privilegiado protegido inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Conduzir um inventário de serviços, proprietários e administradores

Com o aumento no BYOD (Traga seu próprio dispositivo) e nas políticas de trabalho de casa e o crescimento da conectividade sem fio nas empresas, é essencial que você monitore quem está se conectando à sua rede. Uma auditoria de segurança efetiva geralmente revela dispositivos, aplicativos e programas em execução em sua rede que não têm suporte de ti e, portanto, potencialmente não são seguros. Para mais informações, consulte a [gestão de segurança do Azure e a visão geral](../../security/fundamentals/management-monitoring-overview.md)do acompanhamento. Certifique-se de incluir todas as tarefas a seguir em seu processo de inventário. 

* Identifique os usuários que têm funções administrativas e os serviços onde eles podem gerenciar.
* Use o Azure AD PIM para descobrir quais usuários em sua organização têm acesso de administrador ao Azure AD, incluindo funções adicionais além das listadas no estágio 1.
* Além das funções definidas no Azure AD, o Office 365 vem com um conjunto de funções de administrador que você pode atribuir a usuários em sua organização. Cada papel de administrador mapeia para funções comerciais comuns, e dá às pessoas da sua organização permissões para fazer tarefas específicas no centro de administração da [Microsoft 365](https://admin.microsoft.com). Use o centro de administração Microsoft 365 para descobrir quais usuários em sua organização têm acesso de administrador ao Office 365, incluindo por meio de funções não gerenciadas no Azure AD. Para mais informações, consulte sobre as [365 funções de administrador do Office](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e as melhores práticas de segurança para o Office [365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Execute o inventário em outros serviços de que sua organização depende, como Azure, Intune ou Dynamics 365.
* Certifique-se de que suas contas de administrador (contas que são usadas para fins de administração, não apenas as contas do dia a dia dos usuários) tenham endereços de email anexados a elas e tenham se registrado para o Azure MFA ou use o MFA local.
* Peça aos usuários sua justificativa de negócios para acesso administrativo.
* Remova o acesso de administrador para os indivíduos e serviços que não precisam dele.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identificar contas da Microsoft em funções administrativas que precisam ser alternadas para contas corporativas ou de estudante

Às vezes, os administradores globais iniciais de uma organização reutilizam suas credenciais de conta Microsoft existentes quando começaram a usar o Azure AD. Essas contas da Microsoft devem ser substituídas por contas individuais baseadas em nuvem ou sincronizadas. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garantir contas de usuário separadas e encaminhamento de email para contas de administrador global 

As contas de administrador global não devem ter endereços de email pessoais, pois as contas de email pessoais são regularmente analisadas por invasores cibernéticos. Para ajudar a separar os riscos da Internet (ataques de phishing, navegação na Web não intencional) de privilégios administrativos, crie contas dedicadas para cada usuário com privilégios administrativos. 

Se você ainda não tiver feito isso, crie contas separadas para que os usuários executem tarefas de administração globais, para garantir que não abram emails inadvertidamente ou executem programas associados às suas contas de administrador. Certifique-se de que essas contas tenham seus emails encaminhados para uma caixa de correio funcional.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Verifique se as senhas das contas administrativas foram alteradas recentemente

Verifique se todos os usuários entraram em suas contas administrativas e alteraram suas senhas pelo menos uma vez nos últimos 90 dias. Além disso, verifique se as contas compartilhadas nas quais vários usuários sabem que a senha tiveram suas senhas alteradas recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ativar a sincronização de hash de senha

A sincronização de hash de senha é um recurso usado para sincronizar hashes de hashes de senha de usuário de uma instância de Active Directory local para uma instância do Azure AD baseada em nuvem. Mesmo que você decida usar a Federação com Serviços de Federação do Active Directory (AD FS) (AD FS) ou outros provedores de identidade, você pode opcionalmente configurar a sincronização de hash de senha como um backup, caso sua infraestrutura local, como AD ou servidores ADFS, falhe ou se torne temporariamente indisponível. Isso permite que os usuários entrem no serviço usando a mesma senha que eles usam para entrar em sua instância do AD local. Além disso, ele permite que a proteção de identidade detecte credenciais comprometidas comparando os hashes de senha com senhas conhecidas para serem comprometidos, caso um usuário tenha utilizado seu mesmo endereço de email e senha em outros serviços não conectados ao Azure AD.  Para mais informações, consulte A sincronização de hash de [palavra-passe implemente com sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Exigir autenticação multifator (MFA) para usuários em todas as funções privilegiadas, bem como usuários expostos

O Azure AD recomenda que você exija a MFA (autenticação multifator) para todos os seus usuários, incluindo administradores e todos os outros usuários que teriam um impacto significativo se sua conta foi comprometida (por exemplo, gerentes financeiros). Isso reduz o risco de um ataque devido a uma senha comprometida.

Ativar:

* [MFA utilizando políticas](../authentication/howto-mfa-getstarted.md) de Acesso Condicional para todos os utilizadores da sua organização.

Se você usar o Windows Hello para empresas, o requisito de MFA poderá ser atendido usando a experiência de entrada do Windows Hello. Para mais informações, consulte [o Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurar a proteção de identidade 

Azure AD Identity Protection é uma ferramenta de monitoramento e relatório baseada em algoritmos que você pode usar para detectar possíveis vulnerabilidades que afetam as identidades da sua organização. Você pode configurar respostas automatizadas para as atividades suspeitas detectadas e tomar as devidas medidas para resolvê-las. Para mais informações, consulte [Azure Ative Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obtenha sua pontuação segura do Office 365 (se estiver usando o Office 365)

A pontuação segura descobre quais serviços do Office 365 você está usando (como OneDrive, SharePoint e Exchange) e examina suas configurações e atividades e os compara com uma linha de base estabelecida pela Microsoft. Você receberá uma pontuação com base em quão alinhado você está com as melhores práticas de segurança. Qualquer pessoa que tenha permissões de administração (administração global ou um papel de administrador personalizado) para uma subscrição do Office 365 Business Premium ou Da Enterprise pode aceder à Pontuação Segura em [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Examine as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem de como um cliente do Office 365 deve configurar o Office 365 e alavancar outras capacidades em EMS. Em seguida, reveja as etapas 3-6 de como proteger o [acesso aos dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia para monitorizar a segurança e o cumprimento no Office [365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configurar o monitoramento de atividades do Office 365 (se estiver usando o Office 365)

Você pode monitorar como as pessoas em sua organização estão usando os serviços do Office 365, permitindo que você identifique os usuários que têm uma conta administrativa e que talvez não precisem de acesso do Office 365 devido a não entrar nesses portais. Para mais informações, consulte [os relatórios de Atividade no centro de administração da Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer proprietários de planos de resposta a incidentes/emergência

A execução eficiente de resposta a incidentes é uma tarefa complexa. Portanto, o estabelecimento de uma funcionalidade de resposta a incidentes bem-sucedida requer planejamento e recursos substanciais. É essencial que você monitore continuamente os ataques cibernéticos e estabeleça procedimentos para priorizar a manipulação de incidentes. Métodos eficazes de coleta, análise e relatórios de dados são vitais para criar relações e estabelecer comunicação com outros grupos internos e planejar proprietários. Para mais informações, consulte [o Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteger contas administrativas com privilégios locais, se ainda não tiver feito isso

Se o seu inquilino do Diretório Ativo Azure estiver sincronizado com o Diretório Ativo no local, siga as orientações no Roteiro de [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)de Segurança : Fase 1. Isso inclui a criação de contas de administrador separadas para os usuários que precisam realizar tarefas administrativas locais, implantar estações de trabalho com acesso privilegiado para administradores de Active Directory e criar senhas exclusivas de administrador local para estações de trabalho e servidores.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para organizações que gerenciam o acesso ao Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de assinaturas

Use o Enterprise Portal e o portal do Azure para identificar as assinaturas em sua organização que hospedam aplicativos de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover contas da Microsoft de funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook, não devem ser usadas como contas de administrador para assinaturas organizacionais. Remova o estado de administração de todas as contas da Microsoft e substitua-o por Azure Ative Directory (por exemplo, chris@contoso.com) trabalho ou contas escolares.

#### <a name="monitor-azure-activity"></a>Monitorar a atividade do Azure

O log de atividades do Azure fornece um histórico de eventos de nível de assinatura no Azure. Ele oferece informações sobre quem criou, atualizou e excluiu quais recursos e quando esses eventos ocorreram. Para mais informações, consulte [Audit e receba notificações sobre ações importantes na sua subscrição Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para organizações que gerenciam o acesso a outros aplicativos de nuvem por meio do Azure AD

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Prepare políticas de acesso condicional para aplicativos locais e hospedados na nuvem. Se tiver utilizadores no local de trabalho, obtenha mais informações a partir da configuração do Acesso Condicional no local utilizando o registo do [dispositivo De Diretório Ativo Azure](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Estágio 3: criar visibilidade e assumir controle total da atividade do administrador

![Estágio 3 assumir o controle da atividade do administrador](./media/directory-admin-roles-secure/stage-three.png)

O estágio 3 baseia-se nas mitigações do estágio 2 e foi projetado para ser implementado em aproximadamente 1-3 meses. Este estágio do roteiro de acesso privilegiado protegido inclui os componentes a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Concluir uma revisão de acesso de usuários em funções de administrador

Mais usuários corporativos estão ganhando acesso privilegiado por meio de serviços de nuvem, o que pode levar a uma plataforma não gerenciada crescente. Isso inclui os usuários que se tornam administradores globais do Office 365, administradores de assinatura do Azure e usuários que têm acesso de administrador a VMs ou por meio de aplicativos SaaS. Em vez disso, as organizações devem ter todos os funcionários, especialmente administradores, lidar com as transações comerciais cotidianas como usuários sem privilégios e assumir apenas os direitos de administrador, conforme necessário. Como o número de usuários nas funções de administrador pode ter crescido desde a adoção inicial, as revisões de acesso completas para identificar e confirmar cada usuário que está qualificado para ativar os privilégios de administrador. 

Faça o seguinte:

* Determine quais usuários são administradores do Azure AD, habilite o acesso de administrador sob demanda e Just-in-time e controles de segurança baseados em função.
* Converta os usuários que não têm nenhuma justificativa clara para acesso privilegiado de administrador a uma função diferente (se não houver função qualificada, remova-os).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuar a distribuição de autenticação mais forte para todos os usuários 

Exija executivos do C-Suite, gerentes de alto nível, equipe de ti e segurança crítica e outros usuários altamente expostos para ter autenticação moderna e forte, como o Azure MFA ou o Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Usar estações de trabalho dedicadas para administração do Azure AD

Os invasores podem tentar direcionar contas com privilégios para obter acesso aos dados e sistemas de uma organização para que possam interromper a integridade e a autenticidade dos dados, por meio de código mal-intencionado que altera a lógica do programa ou espiona o administrador inserindo uma credencial. As Estações de Trabalho de Acesso Privilegiado (PAWs) fornecem um sistema operativo dedicado para tarefas sensíveis protegidas contra ataques de Internet e vetores de ameaças. Separar essas tarefas e contas confidenciais das estações de trabalho e dispositivos de uso diário fornece uma proteção muito forte contra ataques de phishing, vulnerabilidades de aplicativo e sistema operacional, vários ataques de representação e ataques de roubo de credenciais, como pressionamentos de tecla registro em log, Pass-the-hash e Pass-the-ticket. Ao implantar estações de trabalho com acesso privilegiado, você pode reduzir o risco de os administradores inserirem credenciais de administrador, exceto em um ambiente de área de trabalho que foi protegido. Para mais informações, consulte Postos de Trabalho de [Acesso Privilegiado.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Examine o Instituto Nacional de padrões e as recomendações de tecnologia para lidar com incidentes 

O National Institute of Standards and Technology (NIST) fornece diretrizes para tratamento de incidentes, especialmente para analisar dados relacionados a incidentes e determinar a resposta apropriada para cada incidente. Para mais informações, consulte o Guia de Manuseamento de Incidentes de [Segurança Informática (SP 800-61, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Privileged Identity Management (PIM) para JIT em funções administrativas adicionais

Para o Diretório Ativo Azure, utilize a capacidade de Gestão de [Identidade Privilegiada da Azure AD.](../privileged-identity-management/pim-configure.md) A ativação limitada por tempo de funções privilegiadas funciona permitindo que você:

* Ativar privilégios de administrador para executar uma tarefa específica
* Impor a MFA durante o processo de ativação
* Usar alertas para informar os administradores sobre alterações fora de banda
* Permitir que os usuários mantenham determinados privilégios por um período pré-configurado
* Permitir que os administradores de segurança descubram todas as identidades com privilégios, exibam relatórios de auditoria e criem revisões de acesso para identificar cada usuário qualificado para ativar privilégios de administrador

Se você já estiver usando Azure AD Privileged Identity Management, ajuste os períodos de tempo para os privilégios associados à hora conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determinar a exposição a protocolos de entrada baseados em senha (se estiver usando o Exchange Online)

No passado, os protocolos assumiram que as combinações de nome de usuário/senha foram inseridas em dispositivos, contas de email, telefones e assim por diante. Mas, agora, com o risco de ataques cibernéticos na nuvem, recomendamos que você identifique todos os usuários potenciais que, se suas credenciais foram comprometidos, possam ser catastróficos para a organização e excluí-los da capacidade de entrar em seu email por meio de nome de usuário/senha implementando requisitos de autenticação fortes e acesso condicional. Pode bloquear [a autenticação do legado através](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)do Acesso Condicional . Verifique os detalhes sobre [como bloquear a autenticação básica](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) através do Exchange online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Concluir uma avaliação de análise de funções para funções do Office 365 (se estiver usando o Office 365)

Avalie se todos os usuários administradores estão nas funções corretas (excluir e reatribuir de acordo com essa avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Examine a abordagem de gerenciamento de incidentes de segurança usada no Office 365 e compare com sua própria organização

Pode descarregar este relatório da [Security Incident Management no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a proteger contas administrativas privilegiadas locais

Se o seu Diretório Ativo Azure estiver ligado ao Diretório Ativo no local, siga as orientações no Roteiro de [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)de Segurança : Fase 2. Isso inclui a implantação de estações de trabalho com acesso privilegiado para todos os administradores, exigindo MFA, usando apenas administrador suficiente para manutenção de DC, reduzindo a superfície de ataque de domínios, implantando o ATA para detecção de ataques.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para organizações que gerenciam o acesso ao Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitoramento integrado

O [Azure Security Center](../../security-center/security-center-intro.md) fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure, ajuda a detetar ameaças que de outra forma podem passar despercebidas, e trabalha com um vasto ecossistema de soluções de segurança.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventariar suas contas com privilégios em máquinas virtuais hospedadas

Na maioria dos casos, você não precisa fornecer permissões irrestritas aos usuários para todas as suas assinaturas ou recursos do Azure. Você pode usar as funções de administrador do Azure AD para separar as tarefas dentro de sua organização e conceder apenas a quantidade de acesso a usuários que precisam executar trabalhos específicos. Por exemplo, use as funções de administrador do Azure AD para permitir que um administrador gerencie somente VMs em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura. Para mais informações, consulte [Iniciar o Controlo de Acesso baseado em Funções no portal Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar o PIM para funções de administrador do Azure AD

Use o Privileged Identity Management com funções de administrador do Azure AD para gerenciar, controlar e monitorar o acesso aos recursos do Azure. O uso do PIM protege contas privilegiadas contra ataques cibernéticos reduzindo o tempo de exposição de privilégios e aumentando sua visibilidade em relação ao uso por meio de relatórios e alertas. Para mais informações, consulte [Gerir o acesso rBAC aos recursos Azure com Gestão de Identidade Privilegiada.](../../role-based-access-control/pim-azure-resource.md)

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usar integrações de log do Azure para enviar logs relevantes do Azure para seus sistemas SIEM 

A integração de log do Azure permite que você integre logs brutos de seus recursos do Azure para os sistemas de SIEM (informações de segurança e gerenciamento de eventos) existentes da sua organização. [A integração de registos Azure](../../security/fundamentals/azure-log-integration-overview.md) recolhe eventos windows a partir de registos do Windows Event Viewer, e recursos Azure a partir de Registos de Atividade seleções do Azure, alertas do Azure Security Center e registos de Diagnóstico Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para organizações que gerenciam o acesso a outros aplicativos de nuvem por meio do Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento de usuários para aplicativos conectados

O Azure AD permite automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos de nuvem (SaaS), como dropbox, Salesforce, ServiceNow e assim por diante. Para mais informações, consulte o fornecimento e o fornecimento de utilizadores [da Automatização para aplicações SaaS com a AD Azure](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar a proteção de informações

O MCAS permite investigar arquivos e definir políticas com base nos rótulos de classificação da proteção de informações do Azure, permitindo maior visibilidade e controle de seus dados na nuvem. Examine e classifique os arquivos na nuvem e aplique os rótulos da proteção de informações do Azure. Para mais informações, consulte a [integração da Proteção de Informação do Azure.](https://docs.microsoft.com/cloud-app-security/azip-integration)

#### <a name="configure-conditional-access"></a>Configurar o acesso condicional

Configure acesso condicional com base num grupo, localização e sensibilidade de aplicação para [aplicações SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicações conectadas com AD Azure. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorar a atividade em aplicativos de nuvem conectados

Para garantir que o acesso dos utilizadores também está protegido em aplicações conectadas, recomendamos que aproveite o [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Isso protege o acesso corporativo a aplicativos de nuvem, além de proteger suas contas de administrador, permitindo que você:

* Estenda a visibilidade e o controle aos aplicativos de nuvem
* Criar políticas de acesso, atividades e compartilhamento de dados
* Identifique automaticamente atividades arriscadas, comportamentos anormais e ameaças
* Evitar vazamento de dados
* Minimize o risco e a prevenção automática de ameaças e a imposição de políticas

O agente SIEM Cloud App Security integra Cloud App Security com seu servidor SIEM para habilitar o monitoramento centralizado de alertas e atividades do Office 365. Ele é executado no servidor e efetua pull de alertas e atividades de Cloud App Security e os transmite para o servidor SIEM. Para mais informações, consulte a [integração do SIEM.](https://docs.microsoft.com/cloud-app-security/siem)

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Estágio 4: continuar criando defesas para uma postura de segurança mais proativa

![O estágio 4 adota uma postura de segurança proativa](./media/directory-admin-roles-secure/stage-four.png)

O estágio 4 do roteiro se baseia na visibilidade do estágio 3 e foi projetado para ser implementado em seis meses e além. A conclusão de um roteiro ajuda a desenvolver proteções de acesso privilegiado fortes de ataques em potencial que são atualmente conhecidos e disponíveis atualmente. Infelizmente, as ameaças à segurança evoluem e mudam constantemente, portanto, é recomendável que você veja a segurança como um processo contínuo com foco no aumento do custo e na redução da taxa de sucesso dos adversários direcionados ao seu ambiente.

Proteger o acesso privilegiado é uma primeira etapa crítica para estabelecer garantias de segurança para ativos de negócios em uma organização moderna, mas não é a única parte de um programa de segurança completo que incluiria elementos como, por exemplo, políticas, operações, informações segurança, servidores, aplicativos, computadores, dispositivos, malha de nuvem e outros componentes fornecem garantias de segurança em andamento. 

Além de gerenciar suas contas de acesso privilegiado, recomendamos que você revise o seguinte em uma base contínua:

* Verifique se os administradores estão fazendo seus negócios diários como usuários sem privilégios.
* Conceda acesso privilegiado somente quando necessário e remova-o posteriormente (just-in-time).
* Reter e examinar a atividade de auditoria relacionada a contas privilegiadas.

Para obter mais informações sobre a construção de um roteiro completo de segurança, consulte [os recursos de arquitetura de TI da Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)cloud . Para obter mais informações sobre o envolvimento dos serviços da Microsoft para ajudar em qualquer destes tópicos, contacte o seu representante da Microsoft ou consulte [a Build defesas cibernéticas críticas para proteger a sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Esse estágio final contínuo do roteiro de acesso privilegiado seguro inclui os componentes a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-active-directory"></a>Examinar funções de administrador no Azure Active Directory 

Determine se as funções de administrador internas atuais do Azure AD ainda estão atualizadas e certifique-se de que os usuários estejam apenas nas funções e nas delegações necessárias para as permissões correspondentes. Com o Azure AD, você pode designar administradores separados para atender a funções diferentes. Para mais informações, consulte as [funções de administrador de atribuição no Diretório Ativo azure](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Examine os usuários que têm a administração de dispositivos ingressados no Azure AD

Para mais informações, consulte [Como configurar dispositivos híbridos Azure Ative Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Reveja os membros das funções de administrador do [Office 365 incorporadas](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se você estiver usando o Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Validar plano de resposta a incidentes

Para melhorar seu plano, a Microsoft recomenda que você valide regularmente que seu plano Opera conforme o esperado:

* Passe pelo mapa de estrada existente para ver o que foi perdido
* Com base na análise de postmortem, revise as novas práticas recomendadas existentes ou defina-as
* Certifique-se de que seu plano de resposta a incidentes atualizado e as práticas recomendadas sejam distribuídos em toda a sua organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para organizações que gerenciam o acesso ao Azure 

Determine se precisa transferir a [propriedade de uma subscrição Azure para outra conta.](../../cost-management-billing/manage/billing-subscription-transfer.md)
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Ruptura": o que fazer em uma emergência

![Contas para acesso à interrupção de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os principais gerentes e responsáveis pela segurança com informações pertinentes sobre o incidente.

2. Examine o manual do ataque. 

3. Acesse a combinação de nome de usuário/senha da conta de "interrupção" para entrar no Azure AD. 

4. Obtenha ajuda da Microsoft [abrindo um pedido](../../azure-portal/supportability/how-to-create-azure-support-request.md)de suporte Azure .

5. Veja os [relatórios de inscrição da AD Azure.](../reports-monitoring/overview-reports.md) Pode haver um atraso entre um evento que ocorre e quando ele é incluído no relatório.

6. Para ambientes híbridos, se federado e seu servidor de AD FS não estiverem disponíveis, talvez seja necessário alternar temporariamente da autenticação federada para usar a sincronização de hash de senha. Isso reverte a Federação de domínio de volta para a autenticação gerenciada até que o servidor de AD FS se torne disponível.

7. Monitore email para contas com privilégios.

8. Lembre-se de salvar os backups de logs relevantes para uma investigação forense e jurídica potencial.

Para obter mais informações sobre como o Microsoft Office 365 lida com incidentes de segurança, consulte [security incident management no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Perguntas frequentes: perguntas comuns que recebemos em relação à proteção de acesso privilegiado  

**Q:** O que faço se ainda não implementei componentes de acesso seguros?

**Resposta:** Defina pelo menos duas contas de break-glass, atribua MFA às suas contas de administração privilegiadas e separe as contas de utilizador das contas de administração global.

**Q:** Depois de uma violação, qual é a questão principal que precisa de ser abordada primeiro?

**Resposta:** Certifique-se de que está a exigir a autenticação mais forte para indivíduos altamente expostos.

**Q:** O que acontece se os nossos administradores privilegiados tiverem sido desativados?

**Resposta:** Crie uma conta global de administração que seja sempre mantida atualizada.

**Q:** O que acontece se só restar um administrador global e não puderem ser contactados? 

**Resposta:** Use uma das suas contas de vidro partido para obter acesso privilegiado imediato.

**Q:** Como posso proteger os administradores dentro da minha organização?

**Resposta:** Os administradores fazem sempre o seu dia-a-dia como utilizadores "desfavorecidos" padrão.

**Q:** Quais são as melhores práticas para criar contas de administração dentro da AD Azure?

**Resposta:** Reserve acesso privilegiado para tarefas administrativas específicas.

**Q:** Que ferramentas existem para reduzir o acesso persistente à administração?

**Resposta:** Funções de administração privilegiada de Gestão de Identidade (PIM) e Azure AD.

**Q:** Qual é a posição da Microsoft sobre contas de administração sincronizadas para a AD Azure?

**Resposta:** As contas de administração de nível 0 (incluindo contas, grupos e outros ativos que tenham controlo administrativo direto ou indireto da floresta, domínios ou controladores de domínios, e todos os ativos) são utilizadas apenas para contas ad no local e não são tipicamente sincronizadas para a AD Azure para a nuvem.

**Q:** Como impedimos os administradores de atribuir acesso a administração aleatório no portal?

**Resposta:** Utilize contas não privilegiadas para todos os utilizadores e a maioria dos administradores. Comece desenvolvendo uma superfície da organização para determinar quais poucas contas de administrador devem ser privilegiadas. E o monitor para usuários administrativos recém-criados.

## <a name="next-steps"></a>Passos seguintes

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) – Funcionalidades de segurança dos produtos e serviços da Nuvem da Microsoft

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – O conjunto abrangente de ofertas de conformidade da Microsoft para serviços na nuvem

* [Orientação sobre como realizar uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Gerir requisitos de segurança e conformidade para os serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros serviços online da Microsoft

* [A Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune fornece capacidades de gestão de dispositivos móveis, gestão de aplicações móveis e capacidades de gestão de PC a partir da nuvem.

* [Segurança Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 é a solução baseada na nuvem da Microsoft que unifica as capacidades de gestão de relacionamento com clientes (CRM) e de planeamento de recursos empresariais (ERP).
