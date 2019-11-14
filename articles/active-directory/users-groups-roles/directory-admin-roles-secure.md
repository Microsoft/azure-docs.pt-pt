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
ms.openlocfilehash: e65714f67dde79847bf07efda358a4e1f9ea938d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028442"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Protegendo o acesso privilegiado para implantações híbridas e na nuvem no Azure AD

A segurança da maioria ou de todos os ativos de negócios na organização moderna depende da integridade das contas privilegiadas que administram e gerenciam sistemas de ti. Atores mal-intencionados, incluindo invasores cibernéticos, geralmente visam contas de administrador e outros elementos de acesso privilegiado para tentar obter acesso rápido a dados e sistemas confidenciais usando ataques de roubo de credenciais. Para serviços de nuvem, prevenção e resposta são as responsabilidades conjuntas do provedor de serviços de nuvem e do cliente. Para obter mais informações sobre as ameaças mais recentes a pontos de extremidade e a nuvem, consulte o [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report). Este artigo pode ajudá-lo a desenvolver um roteiro para fechar as lacunas entre os planos atuais e as diretrizes descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com padrões e conformidade regulatória. Saiba mais sobre como a equipe de resposta a incidentes globais da Microsoft reduz os efeitos dos ataques contra serviços de nuvem e como a segurança é incorporada aos produtos comerciais da Microsoft e aos serviços de nuvem na [central de confiabilidade da Microsoft-segurança](https://www.microsoft.com/trustcenter/security) e destinos de conformidade da Microsoft na [central de confiabilidade da Microsoft-conformidade](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Para a maioria das organizações, a segurança dos ativos de negócios depende da integridade das contas privilegiadas que administram e gerenciam sistemas de ti. Invasores cibernéticos se concentram no acesso privilegiado a sistemas de infraestrutura (como Active Directory e Azure Active Directory) para obter acesso aos dados confidenciais de uma organização. 

As abordagens tradicionais que se concentram em proteger os pontos de entrada e saída de uma rede como o perímetro de segurança principal são menos eficazes devido ao aumento no uso de aplicativos SaaS e dispositivos pessoais na Internet. A substituição natural do perímetro de segurança de rede em uma empresa moderna e complexa é a autenticação e os controles de autorização na camada de identidade de uma organização.

Contas administrativas privilegiadas são efetivamente no controle desse novo "perímetro de segurança". É essencial proteger o acesso privilegiado, independentemente de o ambiente estar no local, na nuvem ou em serviços hospedados na nuvem e no local. Proteger o acesso administrativo contra determinados adversários exige que você faça uma abordagem completa e cuidadosa para isolar os sistemas de sua organização contra riscos. 

Proteger o acesso privilegiado requer alterações em

* Processos, práticas administrativas e gerenciamento de conhecimento
* Componentes técnicos, como defesas de host, proteções de conta e gerenciamento de identidade

Este documento se concentra principalmente na criação de um roteiro para proteger identidades e acessos que são gerenciados ou relatados no Azure AD, Microsoft Azure, Office 365 e outros serviços de nuvem. Para organizações que têm contas administrativas locais, consulte as diretrizes para acesso privilegiado local e híbrido gerenciado de Active Directory em [protegendo o acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

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

Azure AD Privileged Identity Management está incluído no Azure AD Premium P2 ou EMS e5. Essas soluções ajudam a proteger o acesso a aplicativos e recursos em todo o ambiente local e na nuvem. Se você ainda não tem o Azure AD Premium P2 ou o EMS E5 e deseja avaliar mais dos recursos mencionados neste roteiro, Inscreva-se para a [Enterprise Mobility + Security avaliação gratuita de 90 dias](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Use essas avaliações de licença para experimentar Azure AD Privileged Identity Management e Azure AD Identity Protection para monitorar a atividade usando relatórios de segurança avançados do Azure AD, auditoria e alertas.

Depois de ativar Azure AD Privileged Identity Management:

1. Entre no [portal do Azure](https://portal.azure.com/) com uma conta que seja um administrador global do seu locatário de produção.

2. Para selecionar o locatário no qual você deseja usar Privileged Identity Management, selecione seu nome de usuário no canto superior direito do portal do Azure.

3. Selecione **todos os serviços** e filtre a lista para **Azure ad Privileged Identity Management**.

4. Abra Privileged Identity Management na lista **todos os serviços** e fixe-o em seu painel.

A primeira pessoa a ser usada Azure AD Privileged Identity Management em seu locatário recebe automaticamente as funções **administrador de segurança** e administrador de função com **privilégios** no locatário. Somente administradores de função com privilégios podem gerenciar as atribuições de função de diretório do Azure AD dos usuários. Além disso, depois de adicionar Azure AD Privileged Identity Management, você será mostrado o assistente de segurança que o guiará pela experiência inicial de descoberta e atribuição. Você pode sair do assistente sem fazer nenhuma alteração adicional no momento. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificar e categorizar contas que estão em funções altamente privilegiadas 

Depois de ativar Azure AD Privileged Identity Management, exiba os usuários que estão no administrador global de funções de diretório, administrador de função com privilégios, administrador do Exchange Online e administrador do SharePoint Online. Se você não tiver o PIM do Azure AD em seu locatário, poderá usar a [API do PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com a função de administrador global, pois essa função é genérica: um usuário ao qual é atribuída essa função de administrador tem as mesmas permissões em todos os serviços de nuvem para os quais sua organização assinou, independentemente de ter sido atribuída a essa função no Microsoft 365 Centro de administração, o portal do Azure ou usando o módulo do Azure AD para o Microsoft PowerShell. 

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

Avalie as contas atribuídas ou qualificadas para a função de administrador global. Se você não vê nenhuma conta somente de nuvem usando o domínio *. onmicrosoft.com (destinado ao acesso de emergência "interrupção"), crie-as. Para obter mais informações, consulte [Gerenciando contas administrativas de acesso de emergência no Azure ad](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ativar a autenticação multifator e registrar todas as outras contas de administrador não federadas de usuário único com alta privilégios

Exigir a MFA (autenticação multifator) do Azure na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do Azure AD: administrador global, administrador de função com privilégios, administrador do Exchange Online e SharePoint Administrador online. Use o guia para habilitar a [MFA (autenticação multifator) para suas contas de administrador](../authentication/howto-mfa-userstates.md) e garantir que todos os usuários se registraram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mais informações podem ser encontradas na etapa 2 e a etapa 3 do guia [proteger o acesso a dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Etapa 2: reduzir as técnicas de ataque usadas com mais frequência

![Estágio 2 mitigar ataques usados com frequência](./media/directory-admin-roles-secure/stage-two.png)

O estágio 2 do roteiro concentra-se na mitigação das técnicas de ataque usadas com mais frequência de roubo e abuso de credenciais e pode ser implementado em aproximadamente 2-4 semanas. Este estágio do roteiro de acesso privilegiado protegido inclui as ações a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Conduzir um inventário de serviços, proprietários e administradores

Com o aumento no BYOD (Traga seu próprio dispositivo) e nas políticas de trabalho de casa e o crescimento da conectividade sem fio nas empresas, é essencial que você monitore quem está se conectando à sua rede. Uma auditoria de segurança efetiva geralmente revela dispositivos, aplicativos e programas em execução em sua rede que não têm suporte de ti e, portanto, potencialmente não são seguros. Para obter mais informações, consulte [visão geral de gerenciamento e monitoramento de segurança do Azure](../../security/fundamentals/management-monitoring-overview.md). Certifique-se de incluir todas as tarefas a seguir em seu processo de inventário. 

* Identifique os usuários que têm funções administrativas e os serviços onde eles podem gerenciar.
* Use o Azure AD PIM para descobrir quais usuários em sua organização têm acesso de administrador ao Azure AD, incluindo funções adicionais além das listadas no estágio 1.
* Além das funções definidas no Azure AD, o Office 365 vem com um conjunto de funções de administrador que você pode atribuir a usuários em sua organização. Cada função de administrador é mapeada para funções comerciais comuns e concede às pessoas em sua organização permissões para realizar tarefas específicas no [centro de administração Microsoft 365](https://admin.microsoft.com). Use o centro de administração Microsoft 365 para descobrir quais usuários em sua organização têm acesso de administrador ao Office 365, incluindo por meio de funções não gerenciadas no Azure AD. Para obter mais informações, consulte [sobre as funções de administrador do office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e [as práticas recomendadas de segurança para o Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
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

A sincronização de hash de senha é um recurso usado para sincronizar hashes de hashes de senha de usuário de uma instância de Active Directory local para uma instância do Azure AD baseada em nuvem. Mesmo que você decida usar a Federação com Serviços de Federação do Active Directory (AD FS) (AD FS) ou outros provedores de identidade, você pode opcionalmente configurar a sincronização de hash de senha como um backup, caso sua infraestrutura local, como AD ou servidores ADFS, falhe ou se torne temporariamente indisponível. Isso permite que os usuários entrem no serviço usando a mesma senha que eles usam para entrar em sua instância do AD local. Além disso, ele permite que a proteção de identidade detecte credenciais comprometidas comparando os hashes de senha com senhas conhecidas para serem comprometidos, caso um usuário tenha utilizado seu mesmo endereço de email e senha em outros serviços não conectados ao Azure AD.  Para obter mais informações, consulte [implementar a sincronização de hash de senha com Azure ad Connect sincronização](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Exigir autenticação multifator (MFA) para usuários em todas as funções privilegiadas, bem como usuários expostos

O Azure AD recomenda que você exija a MFA (autenticação multifator) para todos os seus usuários, incluindo administradores e todos os outros usuários que teriam um impacto significativo se sua conta foi comprometida (por exemplo, gerentes financeiros). Isso reduz o risco de um ataque devido a uma senha comprometida.

Ativar:

* [MFA usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) para todos os usuários em sua organização.

Se você usar o Windows Hello para empresas, o requisito de MFA poderá ser atendido usando a experiência de entrada do Windows Hello. Para obter mais informações, consulte [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurar a proteção de identidade 

Azure AD Identity Protection é uma ferramenta de monitoramento e relatório baseada em algoritmos que você pode usar para detectar possíveis vulnerabilidades que afetam as identidades da sua organização. Você pode configurar respostas automatizadas para as atividades suspeitas detectadas e tomar as devidas medidas para resolvê-las. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obtenha sua pontuação segura do Office 365 (se estiver usando o Office 365)

A pontuação segura descobre quais serviços do Office 365 você está usando (como OneDrive, SharePoint e Exchange) e examina suas configurações e atividades e os compara com uma linha de base estabelecida pela Microsoft. Você receberá uma pontuação com base em quão alinhado você está com as melhores práticas de segurança. Qualquer pessoa que tenha permissões de administrador (administrador global ou uma função de administrador personalizada) para uma assinatura do Office 365 Business Premium ou Enterprise pode acessar a pontuação segura em [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Examine as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem de como um cliente do Office 365 deve configurar o Office 365 e aproveitar outros recursos do EMS. Em seguida, examine as etapas 3-6 de como [proteger o acesso a dados e serviços no office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e no guia de como [monitorar a segurança e a conformidade no Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configurar o monitoramento de atividades do Office 365 (se estiver usando o Office 365)

Você pode monitorar como as pessoas em sua organização estão usando os serviços do Office 365, permitindo que você identifique os usuários que têm uma conta administrativa e que talvez não precisem de acesso do Office 365 devido a não entrar nesses portais. Para obter mais informações, consulte [relatórios de atividade no centro de administração do Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer proprietários de planos de resposta a incidentes/emergência

A execução eficiente de resposta a incidentes é uma tarefa complexa. Portanto, o estabelecimento de uma funcionalidade de resposta a incidentes bem-sucedida requer planejamento e recursos substanciais. É essencial que você monitore continuamente os ataques cibernéticos e estabeleça procedimentos para priorizar a manipulação de incidentes. Métodos eficazes de coleta, análise e relatórios de dados são vitais para criar relações e estabelecer comunicação com outros grupos internos e planejar proprietários. Para obter mais informações, consulte [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteger contas administrativas com privilégios locais, se ainda não tiver feito isso

Se seu locatário de Azure Active Directory estiver sincronizado com o Active Directory local, siga as orientações em [roteiro de acesso com privilégios de segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): estágio 1. Isso inclui a criação de contas de administrador separadas para os usuários que precisam realizar tarefas administrativas locais, implantar estações de trabalho com acesso privilegiado para administradores de Active Directory e criar senhas exclusivas de administrador local para estações de trabalho e servidores.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para organizações que gerenciam o acesso ao Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de assinaturas

Use o Enterprise Portal e o portal do Azure para identificar as assinaturas em sua organização que hospedam aplicativos de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover contas da Microsoft de funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook, não devem ser usadas como contas de administrador para assinaturas organizacionais. Remova o status do administrador de todas as contas da Microsoft e substitua por contas corporativas ou de estudante de Azure Active Directory (por exemplo, chris@contoso.com).

#### <a name="monitor-azure-activity"></a>Monitorar a atividade do Azure

O log de atividades do Azure fornece um histórico de eventos de nível de assinatura no Azure. Ele oferece informações sobre quem criou, atualizou e excluiu quais recursos e quando esses eventos ocorreram. Para obter mais informações, consulte [auditar e receber notificações sobre ações importantes em sua assinatura do Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para organizações que gerenciam o acesso a outros aplicativos de nuvem por meio do Azure AD

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Prepare políticas de acesso condicional para aplicativos locais e hospedados na nuvem. Se você tiver usuários ingressados no local de trabalho, obtenha mais informações em [Configurando o acesso condicional local usando Azure Active Directory registro de dispositivo](../active-directory-device-registration-on-premises-setup.md).


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

Os invasores podem tentar direcionar contas com privilégios para obter acesso aos dados e sistemas de uma organização para que possam interromper a integridade e a autenticidade dos dados, por meio de código mal-intencionado que altera a lógica do programa ou espiona o administrador inserindo uma credencial. As estações de trabalho com acesso privilegiado (PAWs) fornecem um sistema operacional dedicado para tarefas confidenciais que são protegidas contra ataques da Internet e vetores de ameaça. Separar essas tarefas e contas confidenciais das estações de trabalho e dispositivos de uso diário fornece uma proteção muito forte contra ataques de phishing, vulnerabilidades de aplicativo e sistema operacional, vários ataques de representação e ataques de roubo de credenciais, como pressionamentos de tecla registro em log, Pass-the-hash e Pass-the-ticket. Ao implantar estações de trabalho com acesso privilegiado, você pode reduzir o risco de os administradores inserirem credenciais de administrador, exceto em um ambiente de área de trabalho que foi protegido. Para obter mais informações, consulte [estações de trabalho com acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Examine o Instituto Nacional de padrões e as recomendações de tecnologia para lidar com incidentes 

O National Institute of Standards and Technology (NIST) fornece diretrizes para tratamento de incidentes, especialmente para analisar dados relacionados a incidentes e determinar a resposta apropriada para cada incidente. Para obter mais informações, consulte [o guia de tratamento de incidentes de segurança do computador (NIST) (SP 800-61, revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Privileged Identity Management (PIM) para JIT em funções administrativas adicionais

Por Azure Active Directory, use a funcionalidade [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) . A ativação limitada por tempo de funções privilegiadas funciona permitindo que você:

* Ativar privilégios de administrador para executar uma tarefa específica
* Impor a MFA durante o processo de ativação
* Usar alertas para informar os administradores sobre alterações fora de banda
* Permitir que os usuários mantenham determinados privilégios por um período pré-configurado
* Permitir que os administradores de segurança descubram todas as identidades com privilégios, exibam relatórios de auditoria e criem revisões de acesso para identificar cada usuário qualificado para ativar privilégios de administrador

Se você já estiver usando Azure AD Privileged Identity Management, ajuste os períodos de tempo para os privilégios associados à hora conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determinar a exposição a protocolos de entrada baseados em senha (se estiver usando o Exchange Online)

No passado, os protocolos assumiram que as combinações de nome de usuário/senha foram inseridas em dispositivos, contas de email, telefones e assim por diante. Mas, agora, com o risco de ataques cibernéticos na nuvem, recomendamos que você identifique todos os usuários potenciais que, se suas credenciais foram comprometidos, possam ser catastróficos para a organização e excluí-los da capacidade de entrar em seu email por meio de nome de usuário/senha implementando requisitos de autenticação fortes e acesso condicional. Você pode bloquear a [autenticação herdada usando o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Consulte os detalhes sobre [como bloquear a autenticação básica](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) por meio do exchnage online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Concluir uma avaliação de análise de funções para funções do Office 365 (se estiver usando o Office 365)

Avalie se todos os usuários administradores estão nas funções corretas (excluir e reatribuir de acordo com essa avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Examine a abordagem de gerenciamento de incidentes de segurança usada no Office 365 e compare com sua própria organização

Você pode baixar esse relatório do [Gerenciamento de incidentes de segurança no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a proteger contas administrativas privilegiadas locais

Se sua Azure Active Directory estiver conectada à Active Directory local, siga as orientações no [roteiro de acesso privilegiado à segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): estágio 2. Isso inclui a implantação de estações de trabalho com acesso privilegiado para todos os administradores, exigindo MFA, usando apenas administrador suficiente para manutenção de DC, reduzindo a superfície de ataque de domínios, implantando o ATA para detecção de ataques.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para organizações que gerenciam o acesso ao Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitoramento integrado

A [central de segurança do Azure](../../security-center/security-center-intro.md) fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure, ajuda a detectar ameaças que, de outra forma, passam despercebidas e trabalha com um amplo ecossistema de soluções de segurança.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventariar suas contas com privilégios em máquinas virtuais hospedadas

Na maioria dos casos, você não precisa fornecer permissões irrestritas aos usuários para todas as suas assinaturas ou recursos do Azure. Você pode usar as funções de administrador do Azure AD para separar as tarefas dentro de sua organização e conceder apenas a quantidade de acesso a usuários que precisam executar trabalhos específicos. Por exemplo, use as funções de administrador do Azure AD para permitir que um administrador gerencie somente VMs em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura. Para obter mais informações, consulte Introdução [ao controle de acesso baseado em função no portal do Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar o PIM para funções de administrador do Azure AD

Use o Privileged Identity Management com funções de administrador do Azure AD para gerenciar, controlar e monitorar o acesso aos recursos do Azure. O uso do PIM protege contas privilegiadas contra ataques cibernéticos reduzindo o tempo de exposição de privilégios e aumentando sua visibilidade em relação ao uso por meio de relatórios e alertas. Para obter mais informações, consulte [gerenciar o acesso RBAC aos recursos do Azure com Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usar integrações de log do Azure para enviar logs relevantes do Azure para seus sistemas SIEM 

A integração de log do Azure permite que você integre logs brutos de seus recursos do Azure para os sistemas de SIEM (informações de segurança e gerenciamento de eventos) existentes da sua organização. A [integração de log do Azure](../../security/fundamentals/azure-log-integration-overview.md) coleta eventos do Windows de logs do Windows visualizador de eventos e recursos do Azure de logs de atividades do Azure, alertas da central de segurança do Azure e logs de diagnóstico do Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Etapas adicionais para organizações que gerenciam o acesso a outros aplicativos de nuvem por meio do Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento de usuários para aplicativos conectados

O Azure AD permite automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos de nuvem (SaaS), como dropbox, Salesforce, ServiceNow e assim por diante. Para obter mais informações, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure ad](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar a proteção de informações

O MCAS permite investigar arquivos e definir políticas com base nos rótulos de classificação da proteção de informações do Azure, permitindo maior visibilidade e controle de seus dados na nuvem. Examine e classifique os arquivos na nuvem e aplique os rótulos da proteção de informações do Azure. Para obter mais informações, consulte [integração do Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurar o acesso condicional

Configure o acesso condicional com base em um grupo, local e sensibilidade de aplicativo para [aplicativos SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicativos conectados ao Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorar a atividade em aplicativos de nuvem conectados

Para garantir que o acesso dos usuários seja protegido em aplicativos conectados também, recomendamos que você aproveite [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Isso protege o acesso corporativo a aplicativos de nuvem, além de proteger suas contas de administrador, permitindo que você:

* Estenda a visibilidade e o controle aos aplicativos de nuvem
* Criar políticas de acesso, atividades e compartilhamento de dados
* Identifique automaticamente atividades arriscadas, comportamentos anormais e ameaças
* Evitar vazamento de dados
* Minimize o risco e a prevenção automática de ameaças e a imposição de políticas

O agente SIEM Cloud App Security integra Cloud App Security com seu servidor SIEM para habilitar o monitoramento centralizado de alertas e atividades do Office 365. Ele é executado no servidor e efetua pull de alertas e atividades de Cloud App Security e os transmite para o servidor SIEM. Para obter mais informações, consulte [integração da SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Estágio 4: continuar criando defesas para uma postura de segurança mais proativa

![O estágio 4 adota uma postura de segurança proativa](./media/directory-admin-roles-secure/stage-four.png)

O estágio 4 do roteiro se baseia na visibilidade do estágio 3 e foi projetado para ser implementado em seis meses e além. A conclusão de um roteiro ajuda a desenvolver proteções de acesso privilegiado fortes de ataques em potencial que são atualmente conhecidos e disponíveis atualmente. Infelizmente, as ameaças à segurança evoluem e mudam constantemente, portanto, é recomendável que você veja a segurança como um processo contínuo com foco no aumento do custo e na redução da taxa de sucesso dos adversários direcionados ao seu ambiente.

Proteger o acesso privilegiado é uma primeira etapa crítica para estabelecer garantias de segurança para ativos de negócios em uma organização moderna, mas não é a única parte de um programa de segurança completo que incluiria elementos como, por exemplo, políticas, operações, informações segurança, servidores, aplicativos, computadores, dispositivos, malha de nuvem e outros componentes fornecem garantias de segurança em andamento. 

Além de gerenciar suas contas de acesso privilegiado, recomendamos que você revise o seguinte em uma base contínua:

* Verifique se os administradores estão fazendo seus negócios diários como usuários sem privilégios.
* Conceda acesso privilegiado somente quando necessário e remova-o posteriormente (just-in-time).
* Reter e examinar a atividade de auditoria relacionada a contas privilegiadas.

Para obter mais informações sobre como criar um roteiro de segurança completo, consulte [recursos de arquitetura de ti em nuvem da Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Para obter mais informações sobre como envolver os serviços da Microsoft para ajudá-lo em qualquer um desses tópicos, entre em contato com seu representante da Microsoft ou veja [criar defesas cibernéticos críticas para proteger sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Esse estágio final contínuo do roteiro de acesso privilegiado seguro inclui os componentes a seguir.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-active-directory"></a>Examinar funções de administrador no Azure Active Directory 

Determine se as funções de administrador internas atuais do Azure AD ainda estão atualizadas e certifique-se de que os usuários estejam apenas nas funções e nas delegações necessárias para as permissões correspondentes. Com o Azure AD, você pode designar administradores separados para atender a funções diferentes. Para obter mais informações, consulte [atribuindo funções de administrador no Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Examine os usuários que têm a administração de dispositivos ingressados no Azure AD

Para obter mais informações, consulte [como configurar dispositivos ingressados no Azure Active Directory híbrido](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Examinar membros de [funções de administrador internas do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se você estiver usando o Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Validar plano de resposta a incidentes

Para melhorar seu plano, a Microsoft recomenda que você valide regularmente que seu plano Opera conforme o esperado:

* Passe pelo mapa de estrada existente para ver o que foi perdido
* Com base na análise de postmortem, revise as novas práticas recomendadas existentes ou defina-as
* Certifique-se de que seu plano de resposta a incidentes atualizado e as práticas recomendadas sejam distribuídos em toda a sua organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Etapas adicionais para organizações que gerenciam o acesso ao Azure 

Determine se você precisa [transferir a propriedade de uma assinatura do Azure para outra conta](../../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Ruptura": o que fazer em uma emergência

![Contas para acesso à interrupção de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os principais gerentes e responsáveis pela segurança com informações pertinentes sobre o incidente.

2. Examine o manual do ataque. 

3. Acesse a combinação de nome de usuário/senha da conta de "interrupção" para entrar no Azure AD. 

4. Obtenha ajuda da Microsoft [abrindo uma solicitação de suporte do Azure](../../azure-supportability/how-to-create-azure-support-request.md).

5. Examine os [relatórios de entrada do Azure ad](../reports-monitoring/overview-reports.md). Pode haver um atraso entre um evento que ocorre e quando ele é incluído no relatório.

6. Para ambientes híbridos, se federado e seu servidor de AD FS não estiverem disponíveis, talvez seja necessário alternar temporariamente da autenticação federada para usar a sincronização de hash de senha. Isso reverte a Federação de domínio de volta para a autenticação gerenciada até que o servidor de AD FS se torne disponível.

7. Monitore email para contas com privilégios.

8. Lembre-se de salvar os backups de logs relevantes para uma investigação forense e jurídica potencial.

Para obter mais informações sobre como Microsoft Office 365 trata incidentes de segurança, consulte [Gerenciamento de incidentes de segurança no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Perguntas frequentes: perguntas comuns que recebemos em relação à proteção de acesso privilegiado  

**P:** O que devo fazer se ainda não tiver implementado nenhum componente de acesso seguro?

**Resposta:** Defina pelo menos duas contas de interrupção, atribua o MFA às suas contas de administrador privilegiado e separe as contas de usuário das contas de administrador global.

**P:** Após uma violação, qual é o principal problema que precisa ser resolvido primeiro?

**Resposta:** Certifique-se de que você está exigindo a autenticação mais forte para indivíduos altamente expostos.

**P:** O que acontece se nossos administradores com privilégios tiverem sido desativados?

**Resposta:** Crie uma conta de administrador global que sempre seja mantida atualizada.

**P:** O que acontecerá se houver apenas um administrador global e eles não puderem ser acessados? 

**Resposta:** Use uma de suas contas de vidro para obter acesso privilegiado imediato.

**P:** Como posso proteger os administradores na minha organização?

**Resposta:** Os administradores sempre fazem seus negócios diários como usuários "sem privilégios" padrão.

**P:** Quais são as práticas recomendadas para a criação de contas de administrador no Azure AD?

**Resposta:** Reserve acesso privilegiado para tarefas de administração específicas.

**P:** Quais ferramentas existem para reduzir o acesso de administrador persistente?

**Resposta:** Privileged Identity Management (PIM) e funções de administrador do Azure AD.

**P:** Qual é a posição da Microsoft na sincronização de contas de administrador para o Azure AD?

**Resposta:** As contas de administrador da camada 0 (incluindo contas, grupos e outros ativos que têm controle administrativo direto ou indireto da floresta do AD, domínios ou controladores de domínio e todos os ativos) são utilizados somente para contas do AD local e normalmente não são sincronizadas para o Azure AD para a nuvem.

**P:** Como podemos impedir que os administradores atribuam acesso de administrador aleatório no portal?

**Resposta:** Use contas sem privilégios para todos os usuários e para a maioria dos administradores. Comece desenvolvendo uma superfície da organização para determinar quais poucas contas de administrador devem ser privilegiadas. E o monitor para usuários administrativos recém-criados.

## <a name="next-steps"></a>Passos seguintes

* [Microsoft Trust Center para segurança de produtos](https://www.microsoft.com/trustcenter/security) – recursos de segurança dos produtos e serviços em nuvem da Microsoft

* [Central de confiabilidade da Microsoft-conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – conjunto abrangente de ofertas de conformidade da Microsoft para serviços de nuvem

* [Orientação sobre como executar uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -gerenciar requisitos de segurança e conformidade para os serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros serviços online da Microsoft

* [Segurança do Microsoft Intune](https://www.microsoft.com/trustcenter/security/intune-security) – o Intune fornece recursos de gerenciamento de dispositivo móvel, gerenciamento de aplicativos móveis e gerenciamento de PC da nuvem.

* [Microsoft Dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – o Dynamics 365 é a solução baseada em nuvem da Microsoft que UNIFICA o CRM (gerenciamento de relacionamento com o cliente) e os recursos de ERP (planejamento de recursos empresariais).
