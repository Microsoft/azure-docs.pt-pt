---
title: Boas práticas para acesso a administrador seguro - Azure AD / Microsoft Docs
description: Certifique-se de que as contas de acesso administrativo e de administração da sua organização estão seguras. Para arquitetos de sistema e profissionais de TI que configuram Azure AD, Azure e Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/29/2020
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a83593b96d14261bbfb957e802cc16ee028466e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583058"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD

A segurança da maioria ou de todos os ativos empresariais na organização moderna depende da integridade das contas privilegiadas que administram e gerem sistemas informáticos. Atores maliciosos, incluindo ciber-atacantes, muitas vezes visam contas de administração e outros elementos de acesso privilegiado para tentar rapidamente ter acesso a dados e sistemas sensíveis usando ataques de roubo de credenciais. Para os serviços na nuvem, a prevenção e a resposta são as responsabilidades conjuntas do prestador de serviços na nuvem e do cliente. Para obter mais informações sobre as mais recentes ameaças aos pontos finais e à nuvem, consulte o Relatório de [Inteligência de Segurança](https://www.microsoft.com/security/operations/security-intelligence-report)da Microsoft . Este artigo pode ajudá-lo a desenvolver um roteiro para colmatar as lacunas entre os seus planos atuais e as orientações aqui descritas.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com as normas e conformidade regulamentar. Saiba mais sobre como a equipa de resposta a incidentes globais da Microsoft atenua os efeitos dos ataques contra os serviços na nuvem, e como a segurança é incorporada em produtos de negócios da Microsoft e serviços de nuvem no [Microsoft Trust Center - Security](https://www.microsoft.com/trustcenter/security) e Microsoft compliance targets at Microsoft Trust Center - [Compliance](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Para a maioria das organizações, a segurança dos ativos empresariais depende da integridade das contas privilegiadas que administram e gerem sistemas informáticos. Os ciber-atacantes focam-se no acesso privilegiado a sistemas de infraestruturas (como o Ative Directory e o Azure Ative Directory) para ter acesso aos dados sensíveis de uma organização. 

As abordagens tradicionais que se concentram em garantir os pontos de entrada e saída de uma rede, uma vez que o perímetro de segurança primário é menos eficaz devido ao aumento do uso de aplicações SaaS e dispositivos pessoais na Internet. A substituição natural do perímetro de segurança de rede numa empresa moderna complexa são os controlos de autenticação e autorização numa camada de identidade da organização.

As contas administrativas privilegiadas controlam efetivamente este novo "perímetro de segurança". É fundamental proteger o acesso privilegiado, independentemente de o ambiente estar no local, na nuvem ou nos serviços híbridos no local e nos serviços alojados em nuvem. Proteger o acesso administrativo contra adversários determinados requer que tome uma abordagem completa e ponderada para isolar os sistemas da sua organização de riscos. 

Garantir o acesso privilegiado requer alterações

* Processos, práticas administrativas e gestão do conhecimento
* Componentes técnicos, tais como defesas hospedeiras, proteções de contas e gestão de identidade

Este documento centra-se principalmente na criação de um roteiro para proteger identidades e acessos que são geridos ou reportados em Azure AD, Microsoft Azure, Office 365 e outros serviços na nuvem. Para as organizações que tenham contas administrativas no local, consulte as orientações para o acesso privilegiado no local e híbrido gerido a partir do Diretório Ativo na Garantia de [Acesso Privilegiado.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) 

> [!NOTE]
> A orientação deste artigo refere-se principalmente às funcionalidades do Azure Ative Directory que estão incluídas nos planos Azure Ative Directory Premium P1 e P2. O Azure Ative Directory Premium P2 está incluído na suite EMS E5 e na suite Microsoft 365 E5. Esta orientação pressupõe que a sua organização já tem licenças Azure AD Premium P2 adquiridas para os seus utilizadores. Se não tiver estas licenças, algumas das orientações podem não se aplicar à sua organização. Além disso, ao longo deste artigo, o termo administrador global (ou administrador global) significa o mesmo que "administrador da empresa" ou "administrador de inquilinos".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro

A Microsoft recomenda que desenvolva e siga um roteiro para garantir o acesso privilegiado contra ciberataques. Pode sempre ajustar o seu roteiro para acomodar as suas capacidades e requisitos específicos dentro da sua organização. Cada etapa do roteiro deve aumentar o custo e a dificuldade para os adversários atacarem o acesso privilegiado para os seus ativos no local, nuvem e híbridos. A Microsoft recomenda as seguintes quatro fases do roteiro: Este roteiro recomendado programa as implementações mais eficazes e rápidas primeiro, com base nas experiências da Microsoft com incidentes de ciberataque e implementação de resposta. As cronologias deste roteiro são aproximadas.

![Etapas do roteiro com linhas temporais](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fase 1 (24-48 horas): Itens críticos que recomendamos que faça imediatamente

* Fase 2 (2-4 semanas): Atenuar as técnicas de ataque mais usadas

* Fase 3 (1-3 meses): Construir visibilidade e construir o controlo total da atividade de administração

* Fase 4 (seis meses ou mais): Continue a construir defesas para endurecer ainda mais a sua plataforma de segurança

Esta estrutura de roteiro foi concebida para maximizar o uso das tecnologias da Microsoft que já pode ter implementado. Também pode aproveitar as principais tecnologias de segurança atuais e futuras e integrar ferramentas de segurança de outros fornecedores que já implementou ou está a considerar implementar. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fase 1: Itens críticos que recomendamos que faça imediatamente

![Fase 1 Itens críticos para fazer primeiro](./media/directory-admin-roles-secure/stage-one.png)

A fase 1 do roteiro está focada em tarefas críticas que são rápidas e fáceis de implementar. Recomendamos que faça estes itens imediatamente nas primeiras 24-48 horas para garantir um nível básico de acesso privilegiado seguro. Esta fase do roteiro de acesso privilegiado seguro inclui as seguintes ações:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ligue a Azure AD Privileged Identity Management

Se ainda não ligou a Azure AD Privileged Identity Management (PIM), faça-o na sua organização de produção Azure AD. Depois de ativar a Gestão de Identidade Privilegiada, receberá mensagens de correio eletrónico de notificação para alterações de funções de acesso privilegiadas. Estas notificações fornecem um alerta precoce quando os utilizadores adicionais são adicionados a papéis altamente privilegiados no seu diretório.

A Azure AD Privileged Identity Management está incluída no Azure AD Premium P2 ou emems E5. Estas soluções ajudam-no a proteger o acesso a aplicações e recursos através do ambiente no local e na nuvem. Se ainda não tiver Azure AD Premium P2 ou EMS E5 e pretender avaliar mais das funcionalidades referidas neste roteiro, [inscreva-se](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)no ensaio gratuito de 90 dias da Enterprise Mobility + Security. Utilize estes testes de licença para tentar a Azure AD Privileged Identity Management e azure AD Identity Protection, para monitorizar a atividade utilizando relatórios avançados de segurança, auditoria e alertas de segurança da Azure AD.

Depois de ter ligado a Azure AD Privileged Identity Management:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta que é administradora global da sua organização de produção Azure AD.

2. Para selecionar a organização Azure AD onde pretende utilizar a Gestão de Identidade Privilegiada, selecione o seu nome de utilizador no canto superior direito do portal Azure.

3. No menu do portal Azure, selecione **Todos os serviços** e filtre a lista para a Gestão de Identidade Privilegiada do **Azure AD.**

4. Abra a Gestão de Identidade Privilegiada da lista **de todos os serviços** e coloque-a no seu painel de instrumentos.

A primeira pessoa a utilizar a Azure AD Privileged Identity Management na sua organização é automaticamente atribuída ao administrador de **Segurança** e funções de **administrador privilegiado** na organização. Apenas administradores privilegiados podem gerir as atribuições de diretório seletiva seletiva dos utilizadores. Além disso, depois de adicionar a Azure AD Privileged Identity Management, é-lhe mostrado o assistente de segurança que o acompanha através da experiência inicial de descoberta e atribuição. Pode sair do assistente sem fazer alterações adicionais neste momento. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificar e categorizar contas que estejam em papéis altamente privilegiados 

Depois de ligar a Azure AD Privileged Identity Management, consulte os utilizadores que estão nas funções de administrador global, administrador de papéis privilegiados, administrador exchange online e administrador sharePoint Online. Se não tiver o Azure AD PIM na sua organização, pode utilizar a [API PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com o papel de administrador global como este papel é genérico: um utilizador a quem é atribuída esta função de administrador tem as mesmas permissões em todos os serviços na nuvem para os quais a sua organização subscreveu, independentemente de terem sido atribuídos este papel no centro de administração da Microsoft 365, no portal Azure, ou utilizando o módulo AD Azure para o Microsoft PowerShell. 

Remova quaisquer contas que já não sejam necessárias nessas funções. Em seguida, categorize as restantes contas que são atribuídas às funções de administrador:

* Individualmente atribuído a utilizadores administrativos, e também pode ser usado para fins não administrativos (por exemplo, e-mail pessoal)
* Individualmente atribuído aos utilizadores administrativos e designado apenas para fins administrativos
* Partilhado em vários utilizadores
* Para cenários de acesso de emergência de vidro partido
* Para scripts automatizados
* Para utilizadores externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definir pelo menos duas contas de acesso de emergência 

Certifique-se de que não se mete numa situação em que possam ser inadvertidamente bloqueados fora da administração da sua organização Azure AD devido à incapacidade de iniciar sessão ou ativar a conta de um utilizador individual existente como administrador. Por exemplo, se a organização for federada a um fornecedor de identidade no local, esse fornecedor de identidade pode estar indisponível para que os utilizadores não possam inscrever-se no local. Pode mitigar o impacto da falta acidental de acesso administrativo armazenando duas ou mais contas de acesso de emergência na sua organização.

As contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado dentro de um ambiente de Diretório Ativo Azure existente. Estas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a situações de emergência para cenários de "vidro partido", em que as contas administrativas normais não podem ser utilizadas. As organizações devem assegurar o objetivo de controlar e reduzir a utilização da conta de emergência apenas para o tempo necessário.

Avalie as contas atribuídas ou elegíveis para o papel de administradora global. Se não viu contas apenas em nuvem utilizando o domínio *.onmicrosoft.com (destinado a acesso de emergência "break glass"), crie-as. Para mais informações, consulte a Gestão de contas administrativas de [acesso de emergência em Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ligue a autenticação multifactor e registe todas as outras contas de administração não federadas altamente privilegiadas

Requerer a Autenticação Multi-Factor Azure (MFA) no início do registo para todos os utilizadores individuais que estejam permanentemente atribuídos a uma ou mais funções de administrador da AD Azure: Administrador global, administrador de funções privilegiada, administrador exchange online e administrador do SharePoint Online. Utilize o guia para ativar a [Autenticação Multifactor (MFA) para as suas contas de administração e certifique-se](../authentication/howto-mfa-userstates.md) de que todos os utilizadores se registaram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Mais informações podem ser encontradas sob o passo 2 e passo 3 do guia Proteja o [acesso aos dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fase 2: Mitigar as técnicas de ataque mais usadas

![Fase 2 Atenuar ataques frequentemente usados](./media/directory-admin-roles-secure/stage-two.png)

A fase 2 do roteiro centra-se na mitigação das técnicas de ataque mais utilizadas de roubo e abuso de credenciais e pode ser implementada em aproximadamente 2-4 semanas. Esta fase do roteiro de acesso privilegiado seguro inclui as seguintes ações.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Realizar um inventário de serviços, proprietários e administradores

Com o aumento das políticas de bring-your-your-own-device (BYOD) e as políticas de trabalho a partir de casa e o crescimento da conectividade sem fios nas empresas, é fundamental que monitorize quem está a ligar-se à sua rede. Uma auditoria de segurança eficaz revela frequentemente dispositivos, aplicações e programas em execução na sua rede que não são suportados por TI e, portanto, potencialmente não seguros. Para mais informações, consulte a [gestão de segurança do Azure e a visão geral](../../security/fundamentals/management-monitoring-overview.md)do acompanhamento. Certifique-se de que inclui todas as seguintes tarefas no seu processo de inventário. 

* Identifique os utilizadores que tenham funções administrativas e os serviços onde possam gerir.
* Utilize o Azure AD PIM para saber quais os utilizadores da sua organização que têm acesso ao Azure AD, incluindo funções adicionais para além das listadas na Fase 1.
* Para além das funções definidas no Azure AD, o Office 365 vem com um conjunto de funções de administrador que pode atribuir aos utilizadores da sua organização. Cada papel de administrador mapeia para funções comerciais comuns, e dá às pessoas da sua organização permissões para fazer tarefas específicas no centro de administração da [Microsoft 365](https://admin.microsoft.com). Utilize o centro de administração da Microsoft 365 para saber quais os utilizadores da sua organização que têm acesso ao Office 365, incluindo através de funções não geridas em Azure AD. Para mais informações, consulte sobre as [365 funções de administrador do Office](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e as melhores práticas de segurança para o Office [365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Realizar o inventário em outros serviços em que a sua organização conta, como Azure, Intune ou Dynamics 365.
* Certifique-se de que as suas contas de administração (contas que são utilizadas para fins administrativos, e não apenas contas diárias dos utilizadores) têm endereços de e-mail de trabalho anexados a eles e registaram-se para o Azure MFA ou utilizam mfa no local.
* Peça aos utilizadores a sua justificação de negócio para acesso administrativo.
* Remova o acesso à administração dos indivíduos e serviços que não precisam dele.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique as contas da Microsoft em funções administrativas que precisam de ser transferidas para contas de trabalho ou escolares

Por vezes, os administradores globais iniciais de uma organização reutilizam as credenciais de conta da Microsoft quando começaram a usar o Azure AD. Essas contas da Microsoft devem ser substituídas por contas individuais baseadas em nuvem ou sincronizadas. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garantir contas separadas de utilizadores e reencaminhamento de correio para contas de administrador global 

As contas globais do administrador não devem ter endereços de e-mail pessoais, uma vez que as contas de e-mail pessoais são regularmente phished por ciberataques. Para ajudar a separar os riscos de internet (ataques de phishing, navegação web não intencional) a partir de privilégios administrativos, crie contas dedicadas para cada utilizador com privilégios administrativos. 

Se ainda não o fez, crie contas separadas para os utilizadores realizarem tarefas de administração global, para garantir que não abrem inadvertidamente e-mails ou executam programas associados às suas contas de administração. Certifique-se de que essas contas têm o seu e-mail encaminhado para uma caixa de correio de trabalho.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as palavras-passe das contas administrativas mudaram recentemente

Certifique-se de que todos os utilizadores assinaram as suas contas administrativas e alteraram as suas palavras-passe pelo menos uma vez nos últimos 90 dias. Além disso, certifique-se de que quaisquer contas partilhadas nas quais vários utilizadores saibam que a palavra-passe foi alterada recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ligue a sincronização de hash de senha

A sincronização de hash de palavra-passe é uma funcionalidade usada para sincronizar hashes de palavra-passe do utilizador hashes de uma instância de Diretório Ativo no local para uma instância azure ad baseada na nuvem. Mesmo que decida utilizar a federação com serviços da Federação de Diretórios Ativos (AD FS) ou outros fornecedores de identidade, pode configurar opcionalmente a sincronização de hash de palavra-passe como cópia de segurança caso a sua infraestrutura no local, como servidores AD ou ADFS, falhe ou fique temporariamente indisponível. Isto permite que os utilizadores acedam ao serviço utilizando a mesma senha que usam para iniciar sessão na sua instância ad.a. Além disso, permite que a Proteção de Identidade detete credenciais comprometidas comparando essas hashes de senha com senhas conhecidas por estarem comprometidas, se um utilizador tiver alavancado o mesmo endereço de e-mail e senha em outros serviços não ligados à AD Azure.  Para mais informações, consulte A sincronização de hash de [palavra-passe implemente com sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Exigir autenticação multifactor (MFA) para os utilizadores em todas as funções privilegiadas, bem como utilizadores expostos

A Azure AD recomenda que necessite de autenticação multi-factor (MFA) para todos os seus utilizadores, incluindo administradores e todos os outros utilizadores que teriam um impacto significativo se a sua conta fosse comprometida (por exemplo, oficiais financeiros). Isto reduz o risco de um ataque devido a uma senha comprometida.

Ligar:

* [MFA utilizando políticas](../authentication/howto-mfa-getstarted.md) de Acesso Condicional para todos os utilizadores da sua organização.

Se utilizar o Windows Hello for Business, o requisito do MFA pode ser cumprido utilizando o sinal do Windows Hello em experiência. Para mais informações, consulte [o Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurar a Proteção de Identidade 

A Azure AD Identity Protection é uma ferramenta de monitorização e reporte baseada em algoritmos que pode utilizar para detetar potenciais vulnerabilidades que afetam as identidades da sua organização. Pode configurar respostas automatizadas para as atividades suspeitas detetadas e tomar as medidas adequadas para as resolver. Para obter mas informações, veja [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obtenha o seu Office 365 Secure Score (se utilizar o Office 365)

Secure Score descobre quais os serviços do Office 365 que está a utilizar (como o OneDrive, SharePoint e Exchange) e depois olha para as suas definições e atividades e compara-os a uma linha de base estabelecida pela Microsoft. Terá uma pontuação baseada no alinhamento que tem com as melhores práticas de segurança. Qualquer pessoa que tenha permissões de administração (administração global ou um papel de administrador personalizado) para uma subscrição do Office 365 Business Premium ou Da Enterprise pode aceder à Pontuação Segura em [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Reveja a orientação de segurança e conformidade do Office 365 (se utilizar o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem de como um cliente do Office 365 deve configurar o Office 365 e alavancar outras capacidades em EMS. Em seguida, reveja as etapas 3-6 de como proteger o [acesso aos dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia para monitorizar a segurança e o cumprimento no Office [365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configure Office 365 Activity Monitoring (se utilizar o Office 365)

Pode monitorizar como as pessoas da sua organização estão a usar os serviços do Office 365, permitindo identificar utilizadores que tenham uma conta administrativa e que possam não precisar de acesso ao Office 365 por não se inscreverem nesses portais. Para mais informações, consulte [os relatórios de Atividade no centro de administração da Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer os proprietários de planos de resposta a incidentes/emergências

Realizar uma resposta de incidente eficaz é uma tarefa complexa. Por conseguinte, o estabelecimento de uma capacidade de resposta a incidentes bem sucedida requer um planeamento e recursos substanciais. É essencial que monitorize continuamente os ciberataques e estabeleça procedimentos para dar prioridade ao tratamento dos incidentes. Métodos eficazes de recolha, análise e reporte de dados são vitais para construir relações e estabelecer comunicação com outros grupos internos e proprietários de planos. Para mais informações, consulte [o Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Contas administrativas privilegiadas no local, se não já feitas

Se a sua organização Azure Ative Directory estiver sincronizada com o Diretório Ativo no local, siga as orientações no Roteiro de [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)de Segurança : Fase 1. Isto inclui a criação de contas administrativas separadas para os utilizadores que precisam de realizar tarefas administrativas no local, a implementação de Postos de Trabalho de Acesso Privilegiado para administradores de Diretórios Ativos e a criação de senhas de administração locais únicas para estações de trabalho e servidores.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para organizações que gerem o acesso ao Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de subscrições

Utilize o portal Enterprise e o portal Azure para identificar as subscrições na sua organização que acolhem aplicações de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover as contas da Microsoft das funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook não devem ser utilizadas como contas de administrador para subscrições organizacionais. Remova o estado de administração de todas as contas da Microsoft chris@contoso.come substitua-o por Azure Ative Directory (por exemplo, ) trabalho ou contas escolares.

#### <a name="monitor-azure-activity"></a>Monitorizar a atividade do Azure

O Registo de Atividades do Azure disponibiliza um histórico de eventos ao nível da subscrição no Azure. Oferece informações sobre quem criou, atualizou e apagou quais os recursos, e quando estes eventos ocorreram. Para mais informações, consulte [Audit e receba notificações sobre ações importantes na sua subscrição Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passos adicionais para organizações que gerem o acesso a outras aplicações na nuvem via Azure AD

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Preparar políticas de acesso condicional para aplicações no local e em nuvem. Se tiver utilizadores no local de trabalho, obtenha mais informações a partir da configuração do Acesso Condicional no local utilizando o registo do [dispositivo De Diretório Ativo Azure](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fase 3: Construir visibilidade e assumir o controlo total da atividade de administração

![Fase 3 assumir o controlo da atividade de administração](./media/directory-admin-roles-secure/stage-three.png)

A fase 3 baseia-se nas mitigações da fase 2 e foi concebida para ser implementada em aproximadamente 1-3 meses. Esta fase do roteiro de acesso privilegiado seguro inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Complete uma revisão de acesso dos utilizadores nas funções de administrador

Mais utilizadores corporativos estão a obter acesso privilegiado através de serviços na nuvem, o que pode levar a uma plataforma cada vez mais não gerida. Isto inclui utilizadores que se tornam administradores globais para o Office 365, administradores de subscrição Azure e utilizadores que tenham acesso a VMs ou através de aplicações SaaS. Em vez disso, as organizações devem ter todos os colaboradores, especialmente administradores, a lidar com transações comerciais diárias como utilizadores desfavorecidos, e apenas assumir direitos de administração conforme necessário. Uma vez que o número de utilizadores em funções de administrador pode ter crescido desde a adoção inicial, avaliações completas de acesso para identificar e confirmar todos os utilizadores elegíveis para ativar privilégios de administração. 

Faça o seguinte:

* Determine quais os utilizadores que são administradores da AD Azure, permitem o acesso a pedido, o acesso a administrador atempo just-in-time e os controlos de segurança baseados em papéis.
* Converter utilizadores que não tenham uma justificação clara para o acesso privilegiado da administração a uma função diferente (se não houver uma função elegível, remova-os).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continue o lançamento de autenticação mais forte para todos os utilizadores 

Requerem executivos c-suite, gestores de alto nível, pessoal crítico de TI e segurança, e outros utilizadores altamente expostos para ter uma autenticação moderna e forte, como O MFA Azure ou Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Utilize postos de trabalho dedicados para administração para a AD Azure

Os atacantes podem tentar direcionar contas privilegiadas para ter acesso aos dados e sistemas de uma organização para que possam perturbar a integridade e autenticidade dos dados, através de código malicioso que altere a lógica do programa ou snoops o administrador entrando numa credencial. As Estações de Trabalho de Acesso Privilegiado (PAWs) fornecem um sistema operativo dedicado para tarefas confidenciais protegidas contra ataques de Internet e vetores de ameaça. A separação destas contas e tarefas confidenciais e da utilização diária das estações de trabalho e dos dispositivos fornece uma proteção muito forte contra ataques de phishing, vulnerabilidades do SO e de aplicações, vários ataques de representação e ataques de roubo de credenciais, tal como registo de batimentos de tecla, PtH e PtT. Ao implementar postos de trabalho privilegiados de acesso, pode reduzir o risco de os administradores entrarem em credenciais de administração, exceto num ambiente de trabalho que foi endurecido. Para mais informações, consulte Postos de Trabalho de [Acesso Privilegiado.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Rever recomendações do Instituto Nacional de Normalização e Tecnologia para lidar com incidentes 

O Instituto Nacional de Normalização e Tecnologia (NIST) fornece orientações para o tratamento de incidentes, nomeadamente para analisar dados relacionados com incidentes e determinar a resposta adequada a cada incidente. Para mais informações, consulte o Guia de Manuseamento de Incidentes de [Segurança Informática (SP 800-61, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Gestão de Identidade Privilegiada (PIM) para o JIT para funções administrativas adicionais

Para o Diretório Ativo Azure, utilize a capacidade de Gestão de [Identidade Privilegiada da Azure AD.](../privileged-identity-management/pim-configure.md) A ativação limitada do tempo de funções privilegiadas funciona permitindo-lhe:

* Ativar privilégios de administração para executar uma tarefa específica
* Impor MFA durante o processo de ativação
* Use alertas para informar os administradores sobre alterações fora da banda
* Permitir que os utilizadores mantenham certos privilégios por um período de tempo pré-configurado
* Permitir que os administradores de segurança descubram todas as identidades privilegiadas, vejam relatórios de auditoria e criem avaliações de acesso para identificar todos os utilizadores elegíveis para ativar privilégios de administração

Se já estiver a utilizar a Azure AD Privileged Identity Management, ajuste os prazos para privilégios limitados ao tempo, conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determine a exposição a protocolos de entrada baseados em palavras-passe (se utilizar o Exchange Online)

No passado, os protocolos assumiram que as combinações de nomes de utilizador/palavra-passe estavam incorporadas em dispositivos, contas de e-mail, telefones, e assim por diante. Mas agora com o risco de ciberataques na nuvem, recomendamos que identifique todos os potenciais utilizadores que, se as suas credenciais estivessem comprometidas, pudessem ser catastróficos para a organização, e excluí-los de poderem iniciar sessão no seu email através do username/password, implementando requisitos de autenticação forte e acesso condicional. Pode bloquear [a autenticação do legado através](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)do Acesso Condicional . Verifique os detalhes sobre [como bloquear a autenticação básica](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) através do Exchange online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Concluir uma avaliação de revisão de funções para as funções do Office 365 (se utilizar o Office 365)

Avaliar se todos os administradores estão nas funções corretas (excluir e reatribuir de acordo com esta avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Reveja a abordagem de gestão de incidentes de segurança usada no Office 365 e compare com a sua própria organização

Pode descarregar este relatório da [Security Incident Management no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a assegurar contas administrativas privilegiadas no local

Se o seu Diretório Ativo Azure estiver ligado ao Diretório Ativo no local, siga as orientações no Roteiro de [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)de Segurança : Fase 2. Isto inclui a implantação de postos de trabalho privilegiados de acesso para todos os administradores, exigindo MFA, utilizando O Administrador Just o suficiente para manutenção em DC, baixando a superfície de ataque de domínios, implantando ATA para deteção de ataques.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para organizações que gerem o acesso ao Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitorização integrada

O [Azure Security Center](../../security-center/security-center-intro.md) fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure, ajuda a detetar ameaças que de outra forma podem passar despercebidas, e trabalha com um vasto ecossistema de soluções de segurança.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventário das suas contas privilegiadas dentro de máquinas virtuais hospedadas

Na maioria dos casos, não precisa de dar permissões sem restrições aos utilizadores de todas as suas subscrições ou recursos Do Azure. Pode utilizar funções de administrador aD Azure para segregar tarefas dentro da sua organização e conceder apenas a quantidade de acesso aos utilizadores que necessitem de realizar trabalhos específicos. Por exemplo, utilize funções de administrador da AD Azure para permitir que um administrador gere apenas VMs numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição. Para mais informações, consulte [Iniciar o Controlo de Acesso baseado em Funções no portal Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar PIM para funções de administrador da AD Azure

Utilize a Gestão de Identidade Privilegiada com funções de administrador da AD Azure para gerir, controlar e monitorizar o acesso aos recursos do Azure. A utilização da PIM protege as contas privilegiadas de ciberataques, reduzindo o tempo de exposição dos privilégios e aumentando a sua visibilidade para o seu uso através de relatórios e alertas. Para mais informações, consulte [Gerir o acesso rBAC aos recursos Azure com Gestão de Identidade Privilegiada.](../../role-based-access-control/pim-azure-resource.md)

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Utilize integrações de log Azure para enviar registos Azure relevantes para os seus sistemas SIEM 

A integração de registos Azure permite-lhe integrar registos brutos dos seus recursos Azure aos sistemas existentes de Informação de Segurança e Gestão de Eventos (SIEM) da sua organização. [A integração de registos Azure](../../security/fundamentals/azure-log-integration-overview.md) recolhe eventos windows a partir de registos do Windows Event Viewer, e os recursos do Azure a partir de Registos de atividade do Azure, alertas do Azure Security Center e registos de recursos Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passos adicionais para organizações que gerem o acesso a outras aplicações na nuvem via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento de utilizadores para aplicações conectadas

A Azure AD permite automatizar a criação, manutenção e remoção de identidades de utilizadores em aplicações cloud (SaaS) como Dropbox, Salesforce, ServiceNow, e assim por diante. Para mais informações, consulte o fornecimento e o fornecimento de utilizadores [da Automatização para aplicações SaaS com a AD Azure](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar a proteção da informação

O MCAS permite-lhe investigar ficheiros e definir políticas baseadas em etiquetas de classificação de proteção de informação Azure, permitindo uma maior visibilidade e controlo dos seus dados na nuvem. Scaneie e classifique ficheiros na nuvem e aplique etiquetas de proteção de informação Azure. Para mais informações, consulte a [integração da Proteção de Informação do Azure.](https://docs.microsoft.com/cloud-app-security/azip-integration)

#### <a name="configure-conditional-access"></a>Configurar acesso condicional

Configure acesso condicional com base num grupo, localização e sensibilidade de aplicação para [aplicações SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicações conectadas com AD Azure. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorizar a atividade em aplicações de nuvem conectadas

Para garantir que o acesso dos utilizadores também está protegido em aplicações conectadas, recomendamos que aproveite o [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Isto garante o acesso da empresa a aplicações em nuvem, além de garantir as suas contas de administração, permitindo-lhe:

* Alargar a visibilidade e o controlo às aplicações em nuvem
* Criar políticas de acesso, atividades e partilha de dados
* Identificar automaticamente atividades de risco, comportamentos anormais e ameaças
* Prevenir fugas de dados
* Minimizar os riscos e a prevenção automatizada de ameaças e a aplicação das políticas

O agente Cloud App Security SIEM integra a Cloud App Security com o seu servidor SIEM para permitir a monitorização centralizada dos alertas e atividades do Office 365. Funciona no seu servidor e retira alertas e atividades da Cloud App Security e transmite-os para o servidor SIEM. Para mais informações, consulte a [integração do SIEM.](https://docs.microsoft.com/cloud-app-security/siem)

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fase 4: Continuar a construir defesas para uma postura de segurança mais pró-ativa

![Fase 4 adotar uma postura de segurança proativa](./media/directory-admin-roles-secure/stage-four.png)

A fase 4 do roteiro baseia-se na visibilidade da fase 3 e foi concebida para ser implementada em seis meses ou mais. Completar um roteiro ajuda-o a desenvolver fortes proteções de acesso privilegiadas contra potenciais ataques que são atualmente conhecidos e disponíveis. Infelizmente, as ameaças à segurança evoluem e mudam constantemente, por isso recomendamos que enverede a segurança como um processo contínuo focado em aumentar os custos e reduzir a taxa de sucesso dos adversários que visam o seu ambiente.

Garantir o acesso privilegiado é um primeiro passo crítico para estabelecer garantias de segurança para os ativos empresariais numa organização moderna, mas não é a única parte de um programa de segurança completo que incluiria elementos, tais como política, operações, segurança de informação, servidores, aplicações, computadores, dispositivos, tecido sinuoso e outros componentes fornecem garantias de segurança em curso. 

Além de gerir as suas contas de acesso privilegiadas, recomendamos que reveja o seguinte de forma contínua:

* Certifique-se de que os administradores estão a fazer o seu dia-a-dia como utilizadores desfavorecidos.
* Só concede acesso privilegiado quando necessário, e remova-o depois (apenas no tempo).
* Manter e rever a atividade de auditoria relativa a contas privilegiadas.

Para obter mais informações sobre a construção de um roteiro completo de segurança, consulte [os recursos de arquitetura de TI da Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)cloud . Para obter mais informações sobre o envolvimento dos serviços da Microsoft para ajudar em qualquer destes tópicos, contacte o seu representante da Microsoft ou consulte [a Build defesas cibernéticas críticas para proteger a sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Esta fase final em curso do roteiro de acesso privilegiado seguro inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-active-directory"></a>Rever as funções de administrador no Diretório Ativo azure 

Determine se as atuais funções de administração da AD Azure incorporada ainda estão atualizadas e certifique-se de que os utilizadores estão apenas nas funções e delegações de que necessitam para as permissões correspondentes. Com a AD Azure, pode designar administradores separados para desempenhar diferentes funções. Para mais informações, consulte as [funções de administrador de atribuição no Diretório Ativo azure](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Reveja os utilizadores que tenham administração da Azure AD aderiram a dispositivos

Para mais informações, consulte [Como configurar dispositivos híbridos Azure Ative Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Reveja os membros das funções de administrador do [Office 365 incorporadas](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se estiver a usar o Escritório 365.
‎
#### <a name="validate-incident-response-plan"></a>Validar o plano de resposta a incidentes

Para melhorar o seu plano, a Microsoft recomenda que valide regularmente que o seu plano funciona como esperado:

* Percorra o seu roteiro existente para ver o que faltava
* Com base na análise pós-morte, reveja as práticas existentes ou defina novas práticas
* Certifique-se de que o seu plano de resposta a incidentes atualizado e as melhores práticas são distribuídas por toda a sua organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para organizações que gerem o acesso ao Azure 

Determine se precisa transferir a [propriedade de uma subscrição Azure para outra conta.](../../cost-management-billing/manage/billing-subscription-transfer.md)
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Quebrar vidro": o que fazer em caso de emergência

![Contas de acesso de vidro de rutura de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os principais gestores e oficiais de segurança com informações pertinentes sobre o incidente.

2. Reveja o seu livro de jogadas de ataque. 

3. Aceda à sua conta "break glass" username/password para iniciar sessão no Azure AD. 

4. Obtenha ajuda da Microsoft [abrindo um pedido](../../azure-portal/supportability/how-to-create-azure-support-request.md)de suporte Azure .

5. Veja os [relatórios de inscrição da AD Azure.](../reports-monitoring/overview-reports.md) Pode haver um desfasamento entre um evento que ocorre e quando está incluído no relatório.

6. Para ambientes híbridos, se o seu servidor AD FS não estiver disponível, poderá ter de mudar temporariamente da autenticação federada para utilizar a sincronização de hash password. Isto reverte a federação de domínio de volta à autenticação gerida até que o servidor AD FS fique disponível.

7. Monitorize o e-mail para contas privilegiadas.

8. Certifique-se de guardar cópias de bordo de registos relevantes para uma possível investigação forense e legal.

Para obter mais informações sobre como o Microsoft Office 365 lida com incidentes de segurança, consulte [security incident management no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>FAQ: Perguntas comuns que recebemos sobre garantir acesso privilegiado  

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

**Resposta:** Utilize contas não privilegiadas para todos os utilizadores e a maioria dos administradores. Comece por desenvolver uma pegada da organização para determinar quais poucas contas de administração devem ser privilegiadas. E monitorizar os utilizadores administrativos recém-criados.

## <a name="next-steps"></a>Passos seguintes

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) – Funcionalidades de segurança dos produtos e serviços da Nuvem da Microsoft

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – O conjunto abrangente de ofertas de conformidade da Microsoft para serviços na nuvem

* [Orientação sobre como realizar uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Gerir requisitos de segurança e conformidade para os serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros Serviços Online da Microsoft

* [A Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune fornece capacidades de gestão de dispositivos móveis, gestão de aplicações móveis e capacidades de gestão de PC a partir da nuvem.

* [Segurança Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 é a solução baseada na nuvem da Microsoft que unifica as capacidades de gestão de relacionamento com clientes (CRM) e de planeamento de recursos empresariais (ERP).
