---
title: Práticas de acesso seguras para administradores em Azure AD Microsoft Docs
description: Certifique-se de que as contas de acesso administrativo e de administração da sua organização estão seguras. Para arquitetos de sistema e profissionais de TI que configuram Azure AD, Azure e Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c87eabec8f09f082c258a439a17b9f3aa79336
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759035"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD

A segurança dos ativos empresariais depende da integridade das contas privilegiadas que administram os seus sistemas informáticos. Os ciber-atacantes usam ataques de roubo de credenciais para direcionar contas de administração e outros acessos privilegiados para tentar ter acesso a dados sensíveis.

Para os serviços na nuvem, a prevenção e a resposta são as responsabilidades conjuntas do prestador de serviços na nuvem e do cliente. Para obter mais informações sobre as mais recentes ameaças aos pontos finais e à nuvem, consulte o Relatório de [Inteligência de Segurança](https://www.microsoft.com/security/operations/security-intelligence-report)da Microsoft . Este artigo pode ajudá-lo a desenvolver um roteiro para colmatar as lacunas entre os seus planos atuais e as orientações aqui descritas.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com as normas e conformidade regulamentar. Saiba mais sobre como a equipa de resposta a incidentes globais da Microsoft atenua os efeitos dos ataques contra os serviços na nuvem, e como a segurança é incorporada em produtos de negócios da Microsoft e serviços de nuvem no [Microsoft Trust Center - Security](https://www.microsoft.com/trustcenter/security) e Microsoft compliance targets at Microsoft Trust Center - [Compliance](https://www.microsoft.com/trustcenter/compliance).

Tradicionalmente, a segurança organizacional focava-se nos pontos de entrada e saída de uma rede como perímetro de segurança. No entanto, aplicações SaaS e dispositivos pessoais na Internet tornaram esta abordagem menos eficaz. No Azure AD, substituímos o perímetro de segurança da rede por autenticação na camada de identidade da sua organização, com utilizadores atribuídos a funções administrativas privilegiadas no controlo. O seu acesso deve ser protegido, quer o ambiente esteja no local, na nuvem ou num híbrido.

Garantir o acesso privilegiado requer alterações para:

* Processos, práticas administrativas e gestão do conhecimento
* Componentes técnicos, tais como defesas hospedeiras, proteções de contas e gestão de identidade

Garanta o seu acesso privilegiado de uma forma que seja gerida e reportada nos serviços da Microsoft que lhe interessa. Se tiver contas de administração no local, consulte as orientações para o acesso privilegiado no local e híbrido no Diretório Ativo no [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> A orientação deste artigo refere-se principalmente às funcionalidades do Azure Ative Directory que estão incluídas nos planos Azure Ative Directory Premium P1 e P2. O Azure Ative Directory Premium P2 está incluído na suite EMS E5 e na suite Microsoft 365 E5. Esta orientação pressupõe que a sua organização já tem licenças Azure AD Premium P2 adquiridas para os seus utilizadores. Se não tiver estas licenças, algumas das orientações podem não se aplicar à sua organização. Além disso, ao longo deste artigo, o termo administrador global (ou administrador global) significa o mesmo que "administrador da empresa" ou "administrador de inquilinos".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro

A Microsoft recomenda que desenvolva e siga um roteiro para garantir o acesso privilegiado contra ciberataques. Pode sempre ajustar o seu roteiro para acomodar as suas capacidades e requisitos específicos dentro da sua organização. Cada etapa do roteiro deve aumentar o custo e a dificuldade para os adversários atacarem o acesso privilegiado para os seus ativos no local, nuvem e híbridos. A Microsoft recomenda as seguintes quatro fases do roteiro. Agende primeiro as implementações mais eficazes e rápidas. Este artigo pode ser o seu guia, com base nas experiências da Microsoft com incidentes de ciberataque e implementação de resposta. As cronologias deste roteiro são aproximações.

![Etapas do roteiro com linhas temporais](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fase 1 (24-48 horas): Itens críticos que recomendamos que faça imediatamente

* Fase 2 (2-4 semanas): Atenuar as técnicas de ataque mais usadas

* Fase 3 (1-3 meses): Construir visibilidade e construir o controlo total da atividade de administração

* Fase 4 (seis meses ou mais): Continue a construir defesas para endurecer ainda mais a sua plataforma de segurança

Esta estrutura de roteiro foi concebida para maximizar o uso das tecnologias da Microsoft que já pode ter implementado. Considere ligar-se a quaisquer ferramentas de segurança de outros fornecedores que já implementou ou que esteja a considerar a sua implementação.

## <a name="stage-1-critical-items-to-do-right-now"></a>Fase 1: Itens críticos para fazer agora

![Fase 1 Itens críticos para fazer primeiro](./media/directory-admin-roles-secure/stage-one.png)

A fase 1 do roteiro está focada em tarefas críticas que são rápidas e fáceis de implementar. Recomendamos que faça estes itens imediatamente nas primeiras 24-48 horas para garantir um nível básico de acesso privilegiado seguro. Esta fase do roteiro de acesso privilegiado seguro inclui as seguintes ações:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ligue a Azure AD Privileged Identity Management

Recomendamos que ligue a Azure AD Privileged Identity Management (PIM) no seu ambiente de produção da Azure AD. Depois de ligar a PIM, receberá mensagens de correio eletrónico de notificação para alterações de funções de acesso privilegiadas. As notificações fornecem um alerta precoce quando os utilizadores adicionais são adicionados a papéis altamente privilegiados.

A Azure AD Privileged Identity Management está incluída no Azure AD Premium P2 ou emems E5. Para ajudá-lo a proteger o acesso a aplicações e recursos no local e na nuvem, inscreva-se no teste gratuito de [90](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)dias da Enterprise Mobility + Security . A Azure AD Privileged Identity Management e a Azure AD Identity Protection monitorizam a atividade de segurança utilizando relatórios, auditorias e alertas da Azure AD.

Depois de ligar a Azure AD Privileged Identity Management:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta que é administradora global da sua organização de produção Azure AD.

2. Para selecionar a organização Azure AD onde pretende utilizar a Gestão de Identidade Privilegiada, selecione o seu nome de utilizador no canto superior direito do portal Azure.

3. No menu do portal Azure, selecione **Todos os serviços** e filtre a lista para a Gestão de Identidade Privilegiada do **Azure AD.**

4. Abra a Gestão de Identidade Privilegiada da lista **de todos os serviços** e coloque-a no seu painel de instrumentos.

Certifique-se de que a primeira pessoa a utilizar PIM na sua organização é atribuída ao administrador de **segurança** e às funções de **administrador de funções privilegiadas.** Apenas administradores privilegiados podem gerir as atribuições de diretório seletiva seletiva dos utilizadores. O assistente de segurança pim acompanha-o através da experiência inicial de descoberta e atribuição. Pode sair do assistente sem fazer alterações adicionais neste momento.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificar e categorizar contas que estejam em papéis altamente privilegiados

Depois de ligar a Azure AD Privileged Identity Management, consulte os utilizadores que se encontram nas seguintes funções de AD Azure:

* Administrador global
* Administrador privilegiado
* Administrador online de troca
* Administrador online SharePoint

Se não tiver a Azure AD Privileged Identity Management na sua organização, pode utilizar a [API PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com o papel de administrador global porque um administrador global tem as mesmas permissões em todos os serviços na nuvem para os quais a sua organização subscreveu. Estas permissões são concedidas independentemente do local onde foram atribuídas: no centro de administração da Microsoft 365, no portal Azure ou no módulo Azure AD para o Microsoft PowerShell.

Remova quaisquer contas que já não sejam necessárias nessas funções. Em seguida, categorize as restantes contas que são atribuídas às funções de administrador:

* Atribuído a utilizadores administrativos, mas também utilizado para fins não administrativos (por exemplo, e-mail pessoal)
* Atribuído a utilizadores administrativos e utilizado apenas para fins administrativos
* Partilhado em vários utilizadores
* Para cenários de acesso de emergência de vidro partido
* Para scripts automatizados
* Para utilizadores externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definir pelo menos duas contas de acesso de emergência

É possível que um utilizador seja acidentalmente bloqueado fora do seu papel. Por exemplo, se um fornecedor de identidade federado no local não estiver disponível, os utilizadores não podem iniciar sessão ou ativar uma conta de administrador existente. Pode preparar-se para a falta acidental de acesso armazenando duas ou mais contas de acesso de emergência.

As contas de acesso de emergência ajudam a restringir o acesso privilegiado dentro de uma organização da AD Azure. Estas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a situações de emergência para cenários de "vidro partido" onde contas administrativas normais não podem ser utilizadas. Certifique-se de que controla e reduz a utilização da conta de emergência para apenas o tempo necessário.

Avalie as contas atribuídas ou elegíveis para o papel de administradora global. Se não vir contas apenas em nuvem utilizando o \* domínio .onmicrosoft.com (para acesso de emergência "break glass"), crie-as. Para mais informações, consulte a Gestão de contas administrativas de [acesso de emergência em Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ligue a autenticação multifactor e registe todas as outras contas de administração não federadas altamente privilegiadas

Requerer a Autenticação Multi-Factor Azure (MFA) no início do registo para todos os utilizadores individuais que estejam permanentemente atribuídos a uma ou mais funções de administrador da AD Azure: Administrador global, administrador de funções privilegiada, administrador exchange online e administrador do SharePoint Online. Utilize o guia para ativar a [Autenticação Multifactor (MFA) para as suas contas de administração e certifique-se](../authentication/howto-mfa-userstates.md) de que todos os utilizadores se registaram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Mais informações podem ser encontradas sob o passo 2 e passo 3 do guia Proteja o [acesso aos dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Fase 2: Mitigar ataques frequentemente utilizados

![Fase 2 Atenuar ataques frequentemente usados](./media/directory-admin-roles-secure/stage-two.png)

A fase 2 do roteiro centra-se na mitigação das técnicas de ataque mais utilizadas de roubo e abuso de credenciais e pode ser implementada em aproximadamente 2-4 semanas. Esta fase do roteiro de acesso privilegiado seguro inclui as seguintes ações.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Realizar um inventário de serviços, proprietários e administradores

O aumento do "bring your own device" e o trabalho a partir de políticas domésticas e o crescimento da conectividade sem fios tornam fundamental monitorizar quem está a ligar-se à sua rede. Uma auditoria de segurança pode revelar dispositivos, aplicações e programas na sua rede que a sua organização não suporta e que representam um alto risco. Para mais informações, consulte a [gestão de segurança do Azure e a visão geral](../../security/fundamentals/management-monitoring-overview.md)do acompanhamento. Certifique-se de que inclui todas as seguintes tarefas no seu processo de inventário.

* Identifique os utilizadores que tenham funções administrativas e os serviços onde possam gerir.
* Utilize o Azure AD PIM para saber quais os utilizadores da sua organização que têm acesso ao Azure AD.
* Para além das funções definidas no Azure AD, o Office 365 vem com um conjunto de funções de administrador que pode atribuir aos utilizadores da sua organização. Cada papel de administrador mapeia para funções comerciais comuns, e dá às pessoas da sua organização permissões para fazer tarefas específicas no centro de administração da [Microsoft 365](https://admin.microsoft.com). Utilize o centro de administração da Microsoft 365 para saber quais os utilizadores da sua organização que têm acesso ao Office 365, incluindo através de funções não geridas em Azure AD. Para mais informações, consulte sobre as [365 funções de administrador](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) do Office e as práticas de segurança para o [Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Faça o inventário em serviços em que a sua organização conta, como Azure, Intune ou Dynamics 365.
* Certifique-se de que as suas contas são utilizadas para fins administrativos:

  * Tenha endereços de e-mail de trabalho anexados a eles
  * Registrei-se para autenticação multi-factor Azure ou use MFA no local
* Peça aos utilizadores a sua justificação de negócio para acesso administrativo.
* Remova o acesso à administração dos indivíduos e serviços que não precisam dele.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique as contas da Microsoft em funções administrativas que precisam de ser transferidas para contas de trabalho ou escolares

Se os seus administradores globais iniciais reutilizarem as credenciais de conta microsoft existentes quando começaram a utilizar o Azure AD, substitua as contas da Microsoft por contas individuais baseadas na nuvem ou sincronizadas.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garantir contas separadas de utilizadores e reencaminhamento de correio para contas de administrador global

As contas de e-mail pessoais são regularmente falsificadas por ciberataques, um risco que torna os endereços de e-mail pessoais inaceitáveis para contas de administrador global. Para ajudar a separar os riscos da Internet dos privilégios administrativos, crie contas dedicadas para cada utilizador com privilégios administrativos.

* Certifique-se de criar contas separadas para os utilizadores fazerem tarefas de administração global
* Certifique-se de que os seus administradores globais não abrem acidentalmente e-mails ou executam programas com as suas contas de administração
* Certifique-se de que essas contas têm o seu e-mail encaminhado para uma caixa de correio de trabalho

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as palavras-passe das contas administrativas mudaram recentemente

Certifique-se de que todos os utilizadores assinaram as suas contas administrativas e alteraram as suas palavras-passe pelo menos uma vez nos últimos 90 dias. Além disso, verifique se quaisquer contas partilhadas tiveram as suas palavras-passe alteradas recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ligue a sincronização de hash de senha

O Azure AD Connect sincroniza um hash do hash da palavra-passe de um utilizador desde o Ative Directory no local até uma organização azure ad baseada na nuvem. Pode utilizar a sincronização de hash de palavra-passe como cópia de segurança se utilizar a federação com serviços da Federação de Diretório Ativo (AD FS). Esta cópia de segurança pode ser útil se os servidores Ative Directory ou AD FS estiverem temporariamente indisponíveis.

O sync de hash de palavra-passe permite que os utilizadores acedam a um serviço utilizando a mesma palavra-passe que usam para iniciar sessão na sua instância de Diretório Ativo no local. O sync de hash de palavra-passe permite que a Proteção de Identidade detete credenciais comprometidas comparando hashes de palavra-passe com palavras-passe conhecidas por estarem comprometidas. Para mais informações, consulte A sincronização de hash de [palavra-passe implemente com sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Exigir autenticação de vários fatores para os utilizadores em funções privilegiadas e utilizadores expostos

A Azure AD recomenda que necessite de autenticação multi-factor (MFA) para todos os seus utilizadores. Certifique-se de considerar os utilizadores que teriam um impacto significativo se a sua conta fosse comprometida (por exemplo, os funcionários financeiros). A MFA reduz o risco de um ataque por causa de uma senha comprometida.

Ligar:

* [MFA utilizando políticas](../authentication/howto-mfa-getstarted.md) de Acesso Condicional para todos os utilizadores da sua organização.

Se utilizar o Windows Hello for Business, o requisito do MFA pode ser cumprido utilizando a experiência de iniciar sessão do Windows Hello. Para mais informações, consulte [o Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Configurar a Proteção de Identidade

A Azure AD Identity Protection é uma ferramenta de monitorização e reporte baseada em algoritmos que deteta potenciais vulnerabilidades que afetam as identidades da sua organização. Pode configurar respostas automatizadas para as atividades suspeitas detetadas e tomar as medidas adequadas para as resolver. Para obter mas informações, veja [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obtenha o seu Office 365 Secure Score (se utilizar o Office 365)

O Secure Score analisa as suas configurações e atividades para os serviços do Office 365 que está a utilizar e compara-as a uma linha de base estabelecida pela Microsoft. Terá uma pontuação baseada no alinhamento que tem com as práticas de segurança. Qualquer pessoa que tenha permissões de administração para uma subscrição do Office 365 Business Premium ou da Enterprise pode aceder à Pontuação Segura em [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Reveja a orientação de segurança e conformidade do Office 365 (se utilizar o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem de um cliente do Office 365 para configurar o Office 365 e permitir outras capacidades em EMS. Em seguida, reveja as etapas 3-6 de como proteger o [acesso aos dados e serviços no Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia para monitorizar a segurança e o cumprimento no Office [365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configure Office 365 Activity Monitoring (se utilizar o Office 365)

Monitorize a sua organização para utilizadores que estejam a usar o Office 365 para identificar funcionários que tenham uma conta de administração, mas que podem não precisar de acesso ao Office 365 porque não assinam esses portais. Para mais informações, consulte [os relatórios de Atividade no centro de administração da Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer os proprietários de planos de resposta a incidentes/emergências

O estabelecimento de uma capacidade de resposta a incidentes bem sucedida requer um planeamento e recursos consideráveis. Deve monitorizar continuamente os ciberataques e estabelecer prioridades para o tratamento de incidentes. Recolher, analisar e reportar dados de incidentes para construir relações e estabelecer comunicação com outros grupos internos e planear proprietários. Para mais informações, consulte [o Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Contas administrativas privilegiadas no local, se não já feitas

Se a sua organização Azure Ative Directory estiver sincronizada com o Diretório Ativo no local, siga as orientações no Roteiro de [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)de Segurança : Esta fase inclui:

* Criação de contas administrativas separadas para os utilizadores que precisam de realizar tarefas administrativas no local
* Implantação de postos de trabalho privilegiados de acesso para administradores de diretórios ativos
* Criação de senhas de administração locais únicas para estações de trabalho e servidores

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para organizações que gerem o acesso ao Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário de subscrições

Utilize o portal Enterprise e o portal Azure para identificar as subscrições na sua organização que acolhem aplicações de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover as contas da Microsoft das funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook, não devem ser usadas como contas de administrador para as subscrições da sua organização. Remova o estado de administração de todas as contas da Microsoft e substitua-o por Azure AD (por exemplo, chris@contoso.com ) trabalho ou contas escolares. Para efeitos de administração, dependa de contas autenticadas em Azure AD e não em outros serviços.

#### <a name="monitor-azure-activity"></a>Monitorizar a atividade do Azure

O Registo de Atividades do Azure disponibiliza um histórico de eventos ao nível da subscrição no Azure. Oferece informações sobre quem criou, atualizou e apagou quais os recursos, e quando estes eventos ocorreram. Para mais informações, consulte [Audit e receba notificações sobre ações importantes na sua subscrição Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passos adicionais para organizações que gerem o acesso a outras aplicações na nuvem via Azure AD

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Preparar políticas de acesso condicional para aplicações no local e em nuvem. Se tiver utilizadores no local de trabalho, obtenha mais informações a partir da configuração do Acesso Condicional no local utilizando o registo do [dispositivo De Diretório Ativo Azure](../active-directory-device-registration-on-premises-setup.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Fase 3: Assumir o controlo da atividade de administração

![Fase 3: assumir o controlo da atividade de administração](./media/directory-admin-roles-secure/stage-three.png)

A fase 3 baseia-se nas mitigações da fase 2 e deve ser implementada em aproximadamente 1-3 meses. Esta fase do roteiro de acesso privilegiado seguro inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Complete uma revisão de acesso dos utilizadores nas funções de administrador

Mais utilizadores corporativos estão a ter acesso privilegiado através de serviços na nuvem, o que pode levar a um acesso não gerido. Os utilizadores de hoje podem tornar-se administradores globais para o Office 365, administradores de subscrição Azure, ou ter acesso administrativo a VMs ou através de aplicações SaaS.

A sua organização deve ter todos os colaboradores a lidar com transações comerciais normais como utilizadores desfavorecidos e, em seguida, conceder direitos de administração apenas se necessário. Complete as avaliações de acesso para identificar e confirmar os utilizadores elegíveis para ativar privilégios de administração.

É recomendável que:

1. Determine quais os utilizadores que são administradores da AD Azure, permitem o acesso a pedido, o acesso a administrador atempo just-in-time e os controlos de segurança baseados em papéis.
2. Converter utilizadores que não tenham uma justificação clara para o acesso privilegiado da administração a uma função diferente (se não houver uma função elegível, remova-os).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continue o lançamento de autenticação mais forte para todos os utilizadores

Requerem que os utilizadores altamente expostos tenham autenticação moderna e forte, como o Azure MFA ou o Windows Hello. Exemplos de utilizadores altamente expostos incluem:

* Executivos da Suíte C
* Gestores de alto nível
* Ti crítico e pessoal de segurança

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Utilize postos de trabalho dedicados para administração para a AD Azure

Os atacantes podem tentar direcionar contas privilegiadas para que possam perturbar a integridade e autenticidade dos dados. Muitas vezes usam código malicioso que altera a lógica do programa ou bisbilhota o administrador entrando numa credencial. As Estações de Trabalho de Acesso Privilegiado (PAWs) fornecem um sistema operativo dedicado para tarefas confidenciais protegidas contra ataques de Internet e vetores de ameaça. Separar estas tarefas e contas sensíveis dos postos de trabalho e dispositivos de uso diário proporciona uma forte proteção contra:

* Ataques de phishing
* Vulnerabilidades de aplicação e sistema operativo
* Ataques de personificação
* Ataques de roubo credenciais como a exploração madeireira, pass-the-Hash e Pass-The-Ticket

Ao implementar postos de trabalho privilegiados de acesso, pode reduzir o risco de os administradores introduzirem as suas credenciais num ambiente de trabalho que não foi endurecido. Para mais informações, consulte Postos de Trabalho de [Acesso Privilegiado.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Rever recomendações do Instituto Nacional de Normalização e Tecnologia para lidar com incidentes

O Instituto Nacional de Normalização e Tecnologia (NIST) fornece orientações para o tratamento de incidentes, nomeadamente para analisar dados relacionados com incidentes e determinar a resposta adequada a cada incidente. Para mais informações, consulte o Guia de Manuseamento de Incidentes de [Segurança Informática (SP 800-61, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Gestão de Identidade Privilegiada (PIM) para o JIT para funções administrativas adicionais

Para o Diretório Ativo Azure, utilize a capacidade de Gestão de [Identidade Privilegiada da Azure AD.](../privileged-identity-management/pim-configure.md) A ativação limitada do tempo de funções privilegiadas funciona permitindo-lhe:

* Ativar privilégios de administração para fazer uma tarefa específica
* Impor MFA durante o processo de ativação
* Use alertas para informar os administradores sobre alterações fora da banda
* Permitir que os utilizadores mantenham o seu acesso privilegiado por um período de tempo pré-configurado
* Permitir que os administradores de segurança:

  * Descubra todas as identidades privilegiadas
  * Ver relatórios de auditoria
  * Criar avaliações de acesso para identificar todos os utilizadores elegíveis para ativar privilégios de administração

Se já estiver a utilizar a Azure AD Privileged Identity Management, ajuste os prazos para privilégios limitados ao tempo, conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determine a exposição a protocolos de entrada baseados em palavras-passe (se utilizar o Exchange Online)

Recomendamos que identifique todos os potenciais utilizadores que possam ser catastróficos para a organização se as suas credenciais estiverem comprometidas. Para esses utilizadores, coloque em prática requisitos de autenticação forte e utilize o Acesso Condicional Azure AD para evitar que se inscrevam no seu e-mail usando o nome de utilizador e a palavra-passe. Pode bloquear [a autenticação do legado através](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)do Acesso Condicional, e pode bloquear a [autenticação básica](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) através do Exchange online.

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Concluir uma avaliação de revisão de funções para as funções do Office 365 (se utilizar o Office 365)

Avaliar se todos os administradores estão nas funções corretas (excluir e reatribuir de acordo com esta avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Reveja a abordagem de gestão de incidentes de segurança usada no Office 365 e compare com a sua própria organização

Pode descarregar este relatório da [Security Incident Management no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a assegurar contas administrativas privilegiadas no local

Se o seu Diretório Ativo Azure estiver ligado ao Diretório Ativo no local, siga as orientações no Roteiro de [Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)de Segurança : Fase 2. Nesta fase, tu:

* Implementar postos de trabalho privilegiados de acesso para todos os administradores
* Requerer MFA
* Use apenas o Administrador suficiente para manutenção do controlador de domínio, reduzindo a superfície de ataque dos domínios
* Implementar avaliação avançada de ameaças para deteção de ataques

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para organizações que gerem o acesso ao Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitorização integrada

O [Centro de Segurança Azure:](../../security-center/security-center-intro.md)

* Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure
* Ajuda a detetar ameaças que de outra forma podem passar despercebidas
* Trabalha com um vasto leque de soluções de segurança

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventário das suas contas privilegiadas dentro de máquinas virtuais hospedadas

Normalmente não é necessário dar aos utilizadores permissões ilimitadas a todas as suas subscrições ou recursos Do Azure. Utilize funções de administrador a DA Azure para conceder apenas o acesso que os seus utilizadores precisam de fazer o seu trabalho. Pode utilizar funções de administrador da AD Azure para permitir que um administrador gere apenas VMs numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição. Para mais informações, consulte [Iniciar o Controlo de Acesso baseado em Funções no portal Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar PIM para funções de administrador da AD Azure

Utilize a Gestão de Identidade Privilegiada com funções de administrador da AD Azure para gerir, controlar e monitorizar o acesso aos recursos do Azure. A utilização da PIM protege reduzindo o tempo de exposição dos privilégios e aumentando a sua visibilidade para a sua utilização através de relatórios e alertas. Para mais informações, consulte [Gerir o acesso rBAC aos recursos Azure com Gestão de Identidade Privilegiada.](../../role-based-access-control/pim-azure-resource.md)

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Utilize integrações de log Azure para enviar registos Azure relevantes para os seus sistemas SIEM

A integração de registos Azure permite-lhe integrar registos brutos dos seus recursos Azure aos sistemas existentes de Informação de Segurança e Gestão de Eventos (SIEM) da sua organização. [A integração de registos Azure](../../security/fundamentals/azure-log-integration-overview.md) recolhe eventos windows a partir de registos do Windows Event Viewer e recursos Azure a partir de:

* Registos de atividade azure
* Alertas do Centro de Segurança Azure
* Registos de recursos azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passos adicionais para organizações que gerem o acesso a outras aplicações na nuvem via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento de utilizadores para aplicações conectadas

O Azure AD permite-lhe automatizar a criação e manutenção de identidades de utilizadores em aplicações na nuvem como dropbox, Salesforce e ServiceNow. Para mais informações, consulte o fornecimento e o fornecimento de utilizadores [da Automatização para aplicações SaaS com a AD Azure](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar a proteção da informação

O Microsoft Cloud App Security permite-lhe investigar ficheiros e definir políticas com base nas etiquetas de classificação de Proteção de Informação do Azure, permitindo uma maior visibilidade e controlo dos seus dados na nuvem. Scaneie e classifique ficheiros na nuvem e aplique etiquetas de proteção de informação Azure. Para mais informações, consulte a [integração da Proteção de Informação do Azure.](https://docs.microsoft.com/cloud-app-security/azip-integration)

#### <a name="configure-conditional-access"></a>Configurar acesso condicional

Configure acesso condicional com base num grupo, localização e sensibilidade de aplicação para [aplicações SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicações conectadas com AD Azure. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorizar a atividade em aplicações de nuvem conectadas

Recomendamos a utilização do [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para garantir que o acesso ao utilizador também está protegido em aplicações conectadas. Esta funcionalidade garante o acesso da empresa a aplicações na nuvem e assegura as suas contas de administração, permitindo-lhe:

* Alargar a visibilidade e o controlo às aplicações em nuvem
* Criar políticas de acesso, atividades e partilha de dados
* Identificar automaticamente atividades de risco, comportamentos anormais e ameaças
* Prevenir fugas de dados
* Minimizar os riscos e a prevenção automatizada de ameaças e a aplicação das políticas

O agente Cloud App Security SIEM integra a Cloud App Security com o seu servidor SIEM para permitir a monitorização centralizada dos alertas e atividades do Office 365. Funciona no seu servidor e retira alertas e atividades da Cloud App Security e transmite-os para o servidor SIEM. Para mais informações, consulte a [integração do SIEM.](https://docs.microsoft.com/cloud-app-security/siem)

## <a name="stage-4-continue-building-defenses"></a>Fase 4: Continuar a construir defesas

![Fase 4: adotar uma postura de segurança ativa](./media/directory-admin-roles-secure/stage-four.png)

A fase 4 do roteiro deve ser implementada em seis meses ou mais. Complete o seu roteiro para fortalecer as suas proteções de acesso privilegiadas contra potenciais ataques que são conhecidos hoje. Para as ameaças à segurança de amanhã, recomendamos que a segurança seja um processo contínuo para aumentar os custos e reduzir a taxa de sucesso dos adversários que visam o seu ambiente.

Garantir o acesso privilegiado é importante para estabelecer garantias de segurança para os seus ativos comerciais. No entanto, deve fazer parte de um programa de segurança completo que forneça garantias de segurança em curso. Este programa deve incluir elementos como:

* Política
* Operações
* Segurança da informação
* Servidores
* Aplicações
* PCs
* Dispositivos
* Tecido em nuvem

Recomendamos as seguintes práticas quando estiver a gerir contas de acesso privilegiadas:

* Certifique-se de que os administradores estão a fazer o seu dia-a-dia como utilizadores desfavorecidos
* Conceder acesso privilegiado apenas quando necessário, e removê-lo depois (apenas no tempo)
* Manter registos de atividades de auditoria relativos a contas privilegiadas

Para obter mais informações sobre a construção de um roteiro completo de segurança, consulte [os recursos de arquitetura de TI da Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)cloud . Para se envolver com os serviços da Microsoft para ajudá-lo a implementar qualquer parte do seu roteiro, contacte o seu representante da Microsoft ou consulte [a Build defesas cibernéticas críticas para proteger a sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Esta fase final em curso do roteiro de acesso privilegiado seguro inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-ad"></a>Rever as funções de administrador em Azure AD

Determine se as atuais funções de administração da AD Azure incorporada ainda estão atualizadas e certifique-se de que os utilizadores estão apenas nas funções de que necessitam. Com a AD Azure, pode atribuir administradores separados para desempenhar diferentes funções. Para mais informações, consulte as [funções de administrador de atribuição no Diretório Ativo azure](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Reveja os utilizadores que tenham administração da Azure AD aderiram a dispositivos

Para mais informações, consulte [Como configurar dispositivos híbridos Azure Ative Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Reveja os membros das funções de administrador do [Office 365 incorporadas](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Ignore este passo se não estiver a usar o Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Validar o plano de resposta a incidentes

Para melhorar o seu plano, a Microsoft recomenda que valide regularmente que o seu plano funciona como esperado:

* Percorra o seu roteiro existente para ver o que faltava
* Com base na análise pós-morte, reveja as práticas existentes ou defina novas práticas
* Certifique-se de que o seu plano e práticas atualizados de resposta a incidentes são distribuídos por toda a sua organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para organizações que gerem o acesso ao Azure 

Determine se precisa transferir a [propriedade de uma subscrição Azure para outra conta.](../../cost-management-billing/manage/billing-subscription-transfer.md)
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Quebrar vidro": o que fazer em caso de emergência

![Contas de acesso de vidro de rutura de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os principais gestores e oficiais de segurança com informações sobre o incidente.

2. Reveja o seu livro de jogadas de ataque.

3. Aceda ao seu nome de utilizador da conta "break glass" e à combinação de passwords para iniciar sessão no Azure AD.

4. Obtenha ajuda da Microsoft [abrindo um pedido](../../azure-portal/supportability/how-to-create-azure-support-request.md)de suporte Azure .

5. Veja os [relatórios de inscrição da AD Azure.](../reports-monitoring/overview-reports.md) Pode haver algum tempo entre um evento que ocorre e quando está incluído no relatório.

6. Para ambientes híbridos, se a sua infraestrutura no local federado e o seu servidor AD FS não estiverem disponíveis, pode mudar temporariamente de autenticação federada para utilizar a sincronização de hash password. Este interruptor reverte a federação de domínio de volta à autenticação gerida até que o servidor AD FS fique disponível.

7. Monitorize o e-mail para contas privilegiadas.

8. Certifique-se de guardar cópias de bordo de registos relevantes para uma possível investigação forense e legal.

Para obter mais informações sobre como o Microsoft Office 365 lida com incidentes de segurança, consulte [security incident management no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>FAQ: Respostas para garantir acesso privilegiado  

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

**Resposta:** As contas de administração de nível 0 são utilizadas apenas para contas ad.a. Tais contas não são tipicamente sincronizadas com Azure AD na nuvem. As contas de administração de nível 0 incluem contas, grupos e outros ativos que tenham controlo administrativo direto ou indireto da floresta de directórioactivo, domínios, controladores de domínio e ativos.

**Q:** Como impedimos os administradores de atribuir acesso a administração aleatório no portal?

**Resposta:** Utilize contas não privilegiadas para todos os utilizadores e a maioria dos administradores. Comece por desenvolver uma pegada da organização para determinar quais poucas contas de administração devem ser privilegiadas. E monitorizar os utilizadores administrativos recém-criados.

## <a name="next-steps"></a>Passos seguintes

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) – Funcionalidades de segurança dos produtos e serviços da Nuvem da Microsoft

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – O conjunto abrangente de ofertas de conformidade da Microsoft para serviços na nuvem

* [Orientação sobre como fazer uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Gerir requisitos de segurança e conformidade para os serviços de nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros Serviços Online da Microsoft

* [A Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune fornece capacidades de gestão de dispositivos móveis, gestão de aplicações móveis e capacidades de gestão de PC a partir da nuvem.

* [Segurança Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 é a solução baseada na nuvem da Microsoft que unifica as capacidades de gestão de relacionamento com clientes (CRM) e de planeamento de recursos empresariais (ERP).
