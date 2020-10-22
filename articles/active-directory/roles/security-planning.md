---
title: Práticas de acesso seguro para administradores em Azure AD / Microsoft Docs
description: Certifique-se de que o acesso administrativo e as contas administrativas da sua organização estão seguros. Para arquitetos de sistema e profissionais de TI que configuram Azure AD, Azure e Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf41a16975505b76e4b49fdf4e73faeb187ddfe
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378244"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD

A segurança dos ativos empresariais depende da integridade das contas privilegiadas que administram os seus sistemas de TI. Os ciber-atacantes usam ataques de roubo credenciais para direcionar contas de administração e outros acessos privilegiados para tentar obter acesso a dados sensíveis.

Para os serviços na nuvem, a prevenção e resposta são as responsabilidades conjuntas do prestador de serviços na nuvem e do cliente. Para obter mais informações sobre as ameaças mais recentes aos pontos finais e à nuvem, consulte o [Relatório de Inteligência de Segurança](https://www.microsoft.com/security/operations/security-intelligence-report)da Microsoft . Este artigo pode ajudá-lo a desenvolver um roteiro para colmatar as lacunas entre os seus planos atuais e as orientações descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com padrões e conformidade regulamentar. Saiba mais sobre como a equipa global de resposta a incidentes da Microsoft atenua os efeitos dos ataques contra os serviços na nuvem e como a segurança é incorporada nos produtos empresariais da Microsoft e nos serviços de cloud no [Microsoft Trust Center - Objetivos](https://www.microsoft.com/trustcenter/security) de segurança e conformidade da Microsoft no Microsoft Trust Center - [Compliance](https://www.microsoft.com/trustcenter/compliance).

Tradicionalmente, a segurança organizacional focava-se nos pontos de entrada e saída de uma rede como perímetro de segurança. No entanto, aplicações SaaS e dispositivos pessoais na Internet tornaram esta abordagem menos eficaz. No Azure AD, substituímos o perímetro de segurança da rede por autenticação na camada de identidade da sua organização, com utilizadores atribuídos a funções administrativas privilegiadas no controlo. O seu acesso deve ser protegido, seja no local, na nuvem ou num híbrido.

Garantir o acesso privilegiado requer alterações a:

* Processos, práticas administrativas e gestão do conhecimento
* Componentes técnicos tais como defesas hospedeiras, proteções de contas e gestão de identidade

Proteja o seu acesso privilegiado de uma forma que seja gerida e reportada nos serviços da Microsoft que lhe interessam. Se tiver contas de administração no local, consulte as orientações para acesso privilegiado no local e acesso privilegiado híbrido no Ative Directory na [Secureing Privileged Access](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> A orientação deste artigo refere-se principalmente às características do Azure Ative Directory que estão incluídas nos planos Azure Ative Directory Premium P1 e P2. O Azure Ative Directory Premium P2 está incluído na suite EMS E5 e na suite Microsoft 365 E5. Esta orientação pressupõe que a sua organização já tem licenças Azure AD Premium P2 compradas para os seus utilizadores. Se não tiver estas licenças, algumas das orientações podem não se aplicar à sua organização. Além disso, ao longo deste artigo, o termo administrador global (ou administrador global) significa a mesma coisa que "administrador da empresa" ou "administrador de inquilinos".

## <a name="develop-a-roadmap"></a>Desenvolver um roteiro

A Microsoft recomenda que desenvolva e siga um roteiro para garantir o acesso privilegiado contra os ciberataques. Pode sempre ajustar o seu roteiro para acomodar as suas capacidades e requisitos específicos existentes dentro da sua organização. Cada etapa do roteiro deve aumentar o custo e a dificuldade para os adversários atacarem o acesso privilegiado para os seus ativos no local, nuvem e híbridos. A Microsoft recomenda as seguintes quatro fases de roteiro. Agende primeiro as implementações mais eficazes e rápidas. Este artigo pode ser o seu guia, baseado nas experiências da Microsoft com incidentes de ciberataque e implementação de resposta. Os prazos para este roteiro são aproximações.

![Estágios do roteiro com linhas de tempo](./media/security-planning/roadmap-timeline.png)

* Fase 1 (24-48 horas): Itens críticos que recomendamos que faça imediatamente

* Fase 2 (2-4 semanas): Atenuar as técnicas de ataque mais utilizadas

* Fase 3 (1-3 meses): Construir visibilidade e construir o controlo total da atividade de administração

* Fase 4 (seis meses e mais): Continue a construir defesas para endurecer ainda mais a sua plataforma de segurança

Esta estrutura de roteiro destina-se a maximizar o uso das tecnologias da Microsoft que já pode ter implementado. Considere aloque-se a quaisquer ferramentas de segurança de outros fornecedores que já tenha implementado ou esteja a considerar implementar.

## <a name="stage-1-critical-items-to-do-right-now"></a>Fase 1: Itens críticos a fazer agora

![Fase 1 Itens críticos para fazer primeiro](./media/security-planning/stage-one.png)

A fase 1 do roteiro está focada em tarefas críticas que são rápidas e fáceis de implementar. Recomendamos que faça estes poucos itens imediatamente nas primeiras 24-48 horas para garantir um nível básico de acesso privilegiado seguro. Esta fase do roteiro de acesso privilegiado garantido inclui as seguintes ações:

### <a name="general-preparation"></a>Preparação geral

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ligue a Azure AD Gestão de Identidade Privilegiada

Recomendamos que ligue a Azure AD Privileged Identity Management (PIM) no seu ambiente de produção Azure AD. Depois de ligar o PIM, receberá mensagens de correio de notificação para alterações privilegiadas na função de acesso. As notificações fornecem aviso precoce quando utilizadores adicionais são adicionados a funções altamente privilegiadas.

A Azure AD Gestão de Identidade Privilegiada está incluída no Azure AD Premium P2 ou EMS E5. Para ajudá-lo a proteger o acesso a aplicações e recursos no local e na nuvem, inscreva-se no teste gratuito de [90 dias](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)da Enterprise Mobility + Security. A Azure AD Gestão de Identidade Privilegiada e Proteção de Identidade Azure AD monitorizam a atividade de segurança usando relatórios, auditorias e alertas da Azure AD.

Depois de ligar a Azure AD Gestão de Identidade Privilegiada:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta que é um administrador global da sua organização de produção AZure AD.

2. Para selecionar a organização Azure AD onde pretende utilizar a Gestão de Identidade Privilegiada, selecione o seu nome de utilizador no canto superior direito do portal Azure.

3. No menu do portal Azure, selecione **Todos os serviços** e filtre a lista para **Gestão de Identidade Privilegiada AZURE AD**.

4. Abrir Gestão de Identidade Privilegiada a partir da lista **de todos os serviços** e fixá-lo no seu painel de instrumentos.

Certifique-se de que a primeira pessoa a utilizar o PIM na sua organização é atribuída ao **administrador de Segurança** e às funções de administrador **privilegiado.** Apenas os administradores privilegiados podem gerir as atribuições de funções de diretório AD Azure dos utilizadores. O assistente de segurança PIM acompanha-o através da experiência inicial de descoberta e atribuição. Pode sair do assistente sem fazer alterações adicionais neste momento.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificar e categorizar contas que estão em funções altamente privilegiadas

Depois de ligar a Azure AD Gestão de Identidade Privilegiada, consulte os utilizadores que estão nas seguintes funções de AD Azure:

* Administrador global
* Administrador privilegiado
* Administrador do Exchange
* Administrador do SharePoint

Se não tiver Azure AD Gestão de Identidade Privilegiada na sua organização, pode utilizar a [API powerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Comece com o papel de administrador global porque um administrador global tem as mesmas permissões em todos os serviços na nuvem para os quais a sua organização subscreveu. Estas permissões são concedidas independentemente do local onde foram atribuídas: no centro de administração microsoft 365, no portal Azure ou no módulo AD AD Azure para o Microsoft PowerShell.

Remova quaisquer contas que já não sejam necessárias nessas funções. Em seguida, categorize as restantes contas que são atribuídas a funções de administração:

* Atribuído a utilizadores administrativos, mas também utilizado para fins não administrativos (por exemplo, e-mail pessoal)
* Atribuído a utilizadores administrativos e utilizado apenas para fins administrativos
* Partilhado em vários utilizadores
* Para cenários de acesso de emergência de vidro de rutura
* Para scripts automatizados
* Para utilizadores externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definir pelo menos duas contas de acesso de emergência

É possível que um utilizador seja acidentalmente excluído do seu papel. Por exemplo, se um fornecedor de identidade federado no local não estiver disponível, os utilizadores não podem iniciar sômposições ou ativar uma conta de administrador existente. Pode preparar-se para a falta acidental de acesso armazenando duas ou mais contas de acesso de emergência.

As contas de acesso de emergência ajudam a restringir o acesso privilegiado dentro de uma organização AZure AD. Estas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários de "break glass" onde não podem ser utilizadas contas administrativas normais. Certifique-se de que controla e reduz o uso da conta de emergência para apenas o tempo para o qual é necessário.

Avaliar as contas que são atribuídas ou elegíveis para o papel de administrador global. Se não vir nenhuma conta apenas na nuvem utilizando o \* domínio .onmicrosoft.com (para acesso de emergência "quebrar vidros"), crie-as. Para obter mais informações, consulte [gerir as contas administrativas de acesso de emergência em Azure AD](security-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ligue a autenticação de vários fatores e registe todas as outras contas de administração não federadas altamente privilegiadas

Exija a autenticação multi-factor (MFA) no início de sessão para todos os utilizadores individuais que estejam permanentemente atribuídos a uma ou mais funções de administração Admin da Azure: Administrador global, administrador de funções privilegiadas, administrador de câmbio e administrador do SharePoint. Utilize o guia para ativar a [autenticação multi-factor (MFA) para as suas contas de administração e certifique-se](../authentication/howto-mfa-userstates.md) de que todos os utilizadores se registaram em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Mais informações podem ser encontradas no passo 2 e no passo 3 do guia Proteja o [acesso a dados e serviços na Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Fase 2: Mitigar ataques frequentemente utilizados

![Fase 2 Mitigar ataques frequentemente usados](./media/security-planning/stage-two.png)

A fase 2 do roteiro centra-se na mitigação das técnicas de ataque mais frequentemente utilizadas de roubo e abuso credenciais e pode ser implementada em aproximadamente 2-4 semanas. Esta fase do roteiro de acesso privilegiado garantido inclui as seguintes ações.

### <a name="general-preparation"></a>Preparação geral

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Realizar um inventário de serviços, proprietários e administradores

O aumento do "traga o seu próprio dispositivo" e o trabalho a partir das políticas domésticas e o crescimento da conectividade sem fios tornam fundamental monitorizar quem está a ligar-se à sua rede. Uma auditoria de segurança pode revelar dispositivos, aplicações e programas na sua rede que a sua organização não suporta e que representam um alto risco. Para obter mais informações, consulte [a gestão da segurança da Azure e a visão geral da monitorização.](../../security/fundamentals/management-monitoring-overview.md) Certifique-se de que inclui todas as seguintes tarefas no seu processo de inventário.

* Identifique os utilizadores que tenham funções administrativas e os serviços onde podem gerir.
* Utilize o Azure AD PIM para descobrir quais os utilizadores da sua organização que têm acesso administrativo ao Azure AD.
* Além das funções definidas no AD AZure, o Microsoft 365 vem com um conjunto de funções de administração que pode atribuir aos utilizadores da sua organização. Cada função de administração mapeia para funções comerciais comuns, e dá às pessoas da sua organização permissões para fazer tarefas específicas no [centro de administração microsoft 365.](https://admin.microsoft.com) Utilize o centro de administração Microsoft 365 para descobrir quais os utilizadores da sua organização que têm acesso administrativo à Microsoft 365, incluindo através de funções não geridas no Azure AD. Para obter mais informações, consulte sobre as funções de administração da [Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e [as práticas de segurança para o Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Faça o inventário em serviços em que a sua organização conta, como Azure, Intune ou Dynamics 365.
* Certifique-se de que as suas contas que são utilizadas para fins de administração:

  * Ter endereços de e-mail de trabalho anexados a eles
  * Registre-se para autenticação multi-factor Azure ou utilize MFA no local
* Peça aos utilizadores a justificação do seu negócio para o acesso administrativo.
* Remova o acesso de administração para os indivíduos e serviços que não precisam dele.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifique as contas da Microsoft em funções administrativas que precisam de ser mudadas para contas de trabalho ou escolas

Se os seus administradores globais iniciais reutilizarem as suas credenciais de conta Microsoft existentes quando começaram a utilizar o AZure AD, substitua as contas da Microsoft por contas individuais baseadas na nuvem ou sincronizadas.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Garantir contas de utilizador separadas e encaminhamento de correio para contas de administrador global

As contas de email pessoais são regularmente phished por ciberataques, um risco que torna os endereços de e-mail pessoais inaceitáveis para contas de administradores globais. Para ajudar a separar os riscos da Internet dos privilégios administrativos, crie contas dedicadas para cada utilizador com privilégios administrativos.

* Certifique-se de criar contas separadas para os utilizadores fazerem tarefas de administração global
* Certifique-se de que os seus administradores globais não abrem acidentalmente e-mails ou executam programas com as suas contas de administração
* Certifique-se de que essas contas têm o seu e-mail reencaminhado para uma caixa de correio funcionando

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as palavras-passe das contas administrativas mudaram recentemente

Certifique-se de que todos os utilizadores assinaram as suas contas administrativas e alteraram as suas palavras-passe pelo menos uma vez nos últimos 90 dias. Além disso, verifique se quaisquer contas partilhadas tiveram as suas palavras-passe alteradas recentemente.

#### <a name="turn-on-password-hash-synchronization"></a>Ligue a sincronização do hash da palavra-passe

O Azure AD Connect sincroniza um haxixe da palavra-passe de um utilizador desde o Ative Directory até uma organização AZure AD baseada na nuvem. Pode utilizar a sincronização de hash de palavra-passe como cópia de segurança se utilizar a federação com serviços da Federação de Diretórios Ativos (AD FS). Esta cópia de segurança pode ser útil se os seus servidores Ative Directory ou AD FS estiverem temporariamente indisponíveis.

O hash sync da palavra-passe permite que os utilizadores entrem num serviço utilizando a mesma palavra-passe que usam para iniciar súm na sua instância ative Directy. O hash de palavra-passe permite que a Proteção de Identidade detete credenciais comprometidas comparando hashes de palavras-passe com palavras-passe conhecidas por estarem comprometidas. Para obter mais informações, consulte [implementar a sincronização de hash de palavra-passe com a sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Exigir autenticação multi-factor para utilizadores em funções privilegiadas e utilizadores expostos

A Azure AD recomenda que necessite de autenticação multi-factor (MFA) para todos os seus utilizadores. Não se esqueça de considerar os utilizadores que teriam um impacto significativo se a sua conta fosse comprometida (por exemplo, os gestores financeiros). O MFA reduz o risco de um ataque por causa de uma senha comprometida.

Ligue:

* [MFA usando políticas de Acesso Condicional](../authentication/howto-mfa-getstarted.md) para todos os utilizadores da sua organização.

Se utilizar o Windows Hello for Business, o requisito de MFA pode ser cumprido utilizando a experiência de sinserção do Windows Hello. Para mais informações, consulte [o Windows Hello](/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Configure a proteção de identidade

A Azure AD Identity Protection é uma ferramenta de monitorização e reporte baseada em algoritmos que deteta potenciais vulnerabilidades que afetam as identidades da sua organização. Pode configurar respostas automatizadas às atividades suspeitas detetadas e tomar as medidas adequadas para as resolver. Para obter mas informações, veja [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Obtenha a sua Microsoft 365 Secure Score (se utilizar o Microsoft 365)

O Secure Score analisa as suas definições e atividades para os serviços Microsoft 365 que está a utilizar e compara-os a uma linha de base estabelecida pela Microsoft. Terá uma pontuação baseada no seu alinhamento com as práticas de segurança. Qualquer pessoa que tenha as permissões de administração para uma subscrição do Microsoft 365 Business Standard ou Enterprise pode aceder à Pontuação Segura em [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Reveja a orientação de segurança e conformidade da Microsoft 365 (se utilizar o Microsoft 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descreve a abordagem para um cliente do Office 365 configurar o Office 365 e permitir outras capacidades em EMS. Em seguida, rever os passos 3-6 de como proteger o [acesso a dados e serviços na Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia para como monitorizar a [segurança e o cumprimento na Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Configure o Microsoft 365 Activity Monitoring (se utilizar o Microsoft 365)

Monitorize a sua organização para utilizadores que estejam a usar o Microsoft 365 para identificar pessoal que tenha uma conta de administração, mas que possa não precisar de acesso ao Microsoft 365 porque não faz sê-lo. Para obter mais informações, consulte [relatórios de atividade no centro de administração Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer proprietários de planos de resposta a incidentes/emergências

Estabelecer uma capacidade de resposta a incidentes bem sucedida requer um planeamento e recursos consideráveis. Deve monitorizar continuamente os ciberataques e estabelecer prioridades para o tratamento de incidentes. Recolher, analisar e reportar dados de incidentes para construir relações e estabelecer comunicação com outros grupos internos e planear proprietários. Para obter mais informações, consulte [o Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Contas administrativas privilegiadas seguras no local, se não já feitas

Se a sua organização Azure Ative Directory estiver sincronizada com o Ative Directory no local, siga as orientações no Roteiro de [Acesso Privilegiado de Segurança](/windows-server/identity/securing-privileged-access/securing-privileged-access): Esta fase inclui:

* Criação de contas de administração separadas para utilizadores que precisam de realizar tarefas administrativas no local
* Implantação de postos de trabalho privilegiados de acesso para administradores de diretórios ativos
* Criação de senhas de administração locais únicas para estações de trabalho e servidores

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Medidas adicionais para organizações que gerem o acesso ao Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Complete um inventário de subscrições

Utilize o portal Enterprise e o portal Azure para identificar as subscrições na sua organização que acolhe aplicações de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remova as contas da Microsoft das funções de administrador

As contas da Microsoft de outros programas, como Xbox, Live e Outlook, não devem ser usadas como contas de administrador para as subscrições da sua organização. Remova o estado de administração de todas as contas da Microsoft e substitua-o por Azure AD (por chris@contoso.com exemplo,) trabalho ou contas escolares. Para fins administrativos, depende de contas que sejam autenticadas em Azure AD e não em outros serviços.

#### <a name="monitor-azure-activity"></a>Monitorizar atividade do Azure

O Registo de Atividades do Azure disponibiliza um histórico de eventos ao nível da subscrição no Azure. Oferece informações sobre quem criou, atualizou e apagou quais os recursos, e quando estes eventos ocorreram. Para mais informações, consulte [a Auditoria e receba notificações sobre ações importantes na sua subscrição do Azure.](../../azure-monitor/platform/alerts-activity-log.md)

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Medidas adicionais para organizações que gerem o acesso a outras aplicações em nuvem através do Azure AD

#### <a name="configure-conditional-access-policies"></a>Configure políticas de acesso condicional

Prepare políticas de acesso condicional para aplicações no local e aplicações hospedadas na nuvem. Se tiver dispositivos de acesso ao local de trabalho dos utilizadores, obtenha mais informações a partir da configuração do [Acesso Condicional no local utilizando o registo do dispositivo Azure Ative Directory](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Fase 3: Assumir o controlo da atividade de administração

![Fase 3: assumir o controlo da atividade de administração](./media/security-planning/stage-three.png)

A fase 3 baseia-se nas mitigações da Fase 2 e deve ser implementada em aproximadamente 1-3 meses. Esta fase do roteiro de acesso privilegiado garantido inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Complete uma revisão de acesso dos utilizadores em funções de administrador

Mais utilizadores corporativos estão a obter acesso privilegiado através de serviços na nuvem, o que pode levar a um acesso não gerido. Os utilizadores podem hoje em dia tornar-se administradores globais para o Microsoft 365, administradores de subscrição Azure, ou ter acesso a VMs ou através de aplicações SaaS.

A sua organização deve ter todos os colaboradores a lidar com transações comerciais comuns como utilizadores desfavorecidos e, em seguida, conceder direitos de administração apenas quando necessário. Complete as análises de acesso para identificar e confirmar os utilizadores elegíveis para ativar privilégios de administração.

É recomendável que:

1. Determinar quais os utilizadores que são administradores AD AD Aure, permitir o acesso a administração a pedido, just-in-time e controlos de segurança baseados em funções.
2. Converter utilizadores que não tenham uma justificação clara para o acesso privilegiado a uma função diferente (se não tiver papel elegível, remova-os).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continue a implantação de autenticação mais forte para todos os utilizadores

Requerem que os utilizadores altamente expostos tenham uma autenticação moderna e forte, como o Azure MFA ou o Windows Hello. Exemplos de utilizadores altamente expostos incluem:

* Executivos de suíte C
* Gestores de alto nível
* Pessoal crítico de TI e segurança

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Utilize estações de trabalho dedicadas para administração da Azure AD

Os atacantes podem tentar direcionar as contas privilegiadas para que possam perturbar a integridade e autenticidade dos dados. Muitas vezes usam código malicioso que altera a lógica do programa ou bisbilhota o administrador entrando numa credencial. As Estações de Trabalho de Acesso Privilegiado (PAWs) fornecem um sistema operativo dedicado para tarefas confidenciais protegidas contra ataques de Internet e vetores de ameaça. A separação destas tarefas e contas sensíveis dos postos de trabalho de uso diário e dos dispositivos proporciona uma forte proteção contra:

* Ataques de phishing
* Vulnerabilidades de aplicação e sistema operativo
* Ataques de personificação
* Ataques de roubo credenciais como madeireiros de teclas, Pass-the-Hash e Pass-The-Ticket

Ao implantar estações de trabalho de acesso privilegiada, pode reduzir o risco de os administradores entrarem nas suas credenciais num ambiente de trabalho que não foi endurecido. Para mais informações, consulte [as estações de trabalho de acesso privilegiada.](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Rever recomendações do Instituto Nacional de Normas e Tecnologia para lidar com incidentes

O Instituto Nacional de Normalização e Tecnologia (NIST) fornece orientações para o tratamento de incidentes, nomeadamente para analisar dados relacionados com incidentes e determinar a resposta adequada a cada incidente. Para obter mais informações, consulte [o Guia de Tratamento de Incidentes de Segurança Informática (SP 800-61, Revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar Gestão de Identidade Privilegiada (PIM) para JIT a funções administrativas adicionais

Para o Azure Ative Directory, utilize a capacidade [de Gestão de Identidade Privilegiada Azure AD.](../privileged-identity-management/pim-configure.md) A ativação limitada do tempo de funções privilegiadas funciona permitindo-lhe:

* Ativar privilégios de administração para fazer uma tarefa específica
* Impor MFA durante o processo de ativação
* Use alertas para informar os administradores sobre alterações fora da banda
* Permitir que os utilizadores mantenham o seu acesso privilegiado por um período de tempo pré-configurado
* Permitir que os administradores de segurança:

  * Descubra todas as identidades privilegiadas
  * Ver relatórios de auditoria
  * Crie comentários de acesso para identificar todos os utilizadores que são elegíveis para ativar privilégios de administração

Se já estiver a utilizar a Gestão de Identidade Privilegiada AD Azure, ajuste os prazos para privilégios com prazos necessários (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determinar a exposição a protocolos de entrada baseados em palavras-passe (se utilizar o Exchange Online)

Recomendamos que identifique todos os potenciais utilizadores que possam ser catastróficos para a organização se as suas credenciais forem comprometidas. Para esses utilizadores, coloque em vigor requisitos de autenticação fortes e utilize o Acesso Condicional Azure AD para evitar que se inscrevam no seu email usando o nome de utilizador e senha. Pode bloquear [a autenticação do legado utilizando o Acesso Condicional,](../conditional-access/block-legacy-authentication.md)e pode [bloquear a autenticação básica](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) através do Exchange online.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Complete uma avaliação de avaliação de funções para as funções da Microsoft 365 (se utilizar o Microsoft 365)

Avaliar se todos os administradores estão nas funções corretas (excluir e reatribuir de acordo com esta avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Reveja a abordagem de gestão de incidentes de segurança usada na Microsoft 365 e compare com a sua própria organização

Você pode baixar este relatório da [Security Incident Management na Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continue a garantir contas administrativas privilegiadas no local

Se o seu Diretório Ativo Azure estiver ligado ao Ative Directory no local, siga as orientações no Roteiro de [Acesso Privilegiado de Segurança](/windows-server/identity/securing-privileged-access/securing-privileged-access): Fase 2. Nesta fase, você:

* Implementar estações de trabalho de acesso privilegiada para todos os administradores
* Requerer MFA
* Use O Administrador Suficiente para manutenção do controlador de domínio, reduzindo a superfície de ataque dos domínios
* Implementar avaliação avançada de ameaças para deteção de ataques

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Medidas adicionais para organizações que gerem o acesso ao Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitorização integrada

O [Centro de Segurança Azure:](../../security-center/security-center-introduction.md)

* Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições Azure
* Ajuda a detetar ameaças que de outra forma podem passar despercebidas
* Trabalha com um vasto leque de soluções de segurança

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Coumente as suas contas privilegiadas dentro de Máquinas Virtuais hospedadas

Normalmente não precisa de dar aos utilizadores permissões ilimitadas a todas as suas subscrições ou recursos Azure. Utilize funções de administração Azure AD para conceder apenas o acesso que os seus utilizadores precisam para fazer o seu trabalho. Pode utilizar as funções de administrador AD da Azure para permitir que um administrador gere apenas VMs numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição. Para obter mais informações, consulte [o que é o controlo de acesso baseado em funções do Azure.](../../active-directory-b2c/overview.md)

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar pim para funções de administrador ad Azure

Use gestão de identidade privilegiada com funções de administrador da Ad Azure para gerir, controlar e monitorizar o acesso aos recursos da Azure. A utilização de PIM protege reduzindo o tempo de exposição dos privilégios e aumentando a sua visibilidade para a sua utilização através de relatórios e alertas. Para mais informações, consulte [o que é Azure AD Gestão de Identidade Privilegiada.](../privileged-identity-management/pim-configure.md)

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Utilize integrações de registos Azure para enviar registos Azure relevantes para os seus sistemas SIEM

A integração de registos Azure permite-lhe integrar registos brutos dos seus recursos Azure para os sistemas existentes de Informação de Segurança e Gestão de Eventos (SIEM) da sua organização. [A integração de registos Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) recolhe eventos Windows a partir de registos do Windows Event Viewer e recursos Azure a partir de:

* Registos de atividades Azure
* Alertas do Centro de Segurança do Azure
* Registos de recursos do Azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Medidas adicionais para organizações que gerem o acesso a outras aplicações em nuvem através do Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o provisionamento do utilizador para aplicações conectadas

O Azure AD permite automatizar a criação e manutenção de identidades de utilizadores em aplicações na nuvem como Dropbox, Salesforce e ServiceNow. Para obter mais informações, consulte [automatizar o fornecimento e desprovisionamento do utilizador para aplicações SaaS com Azure AD](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar proteção de informação

O Microsoft Cloud App Security permite-lhe investigar ficheiros e definir políticas com base em etiquetas de classificação de Proteção de Informação Azure, permitindo uma maior visibilidade e controlo dos seus dados em nuvem. Digitalize e classifique os ficheiros na nuvem e aplique etiquetas de proteção de informação Azure. Para mais informações, consulte [a integração da Azure Information Protection](/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configure acesso condicional

Configurar o Acesso Condicional com base num grupo, localização e sensibilidade à aplicação para [aplicações SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicações conectadas a Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorizar a atividade em aplicativos de nuvem conectados

Recomendamos a utilização do [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) para garantir que o acesso do utilizador também está protegido em aplicações conectadas. Esta funcionalidade garante o acesso da empresa a aplicações na nuvem e assegura as suas contas de administração, permitindo-lhe:

* Alargar a visibilidade e o controlo às aplicações na nuvem
* Criar políticas de acesso, atividades e partilha de dados
* Identificar automaticamente atividades de risco, comportamentos anormais e ameaças
* Prevenir fugas de dados
* Minimizar o risco e a prevenção automática de ameaças e a aplicação de políticas

O agente SIEM de Segurança cloud App integra a Cloud App Security com o seu servidor SIEM para permitir uma monitorização centralizada dos alertas e atividades do Microsoft 365. Funciona no seu servidor e retira alertas e atividades da Cloud App Security e transmite-os para o servidor SIEM. Para mais informações, consulte [a integração do SIEM.](/cloud-app-security/siem)

## <a name="stage-4-continue-building-defenses"></a>Fase 4: Continuar a construir defesas

![Fase 4: adotar uma postura de segurança ativa](./media/security-planning/stage-four.png)

A fase 4 do roteiro deve ser implementada a seis meses ou mais. Complete o seu roteiro para fortalecer as suas proteções privilegiadas de acesso contra potenciais ataques que são conhecidos hoje. Para as ameaças à segurança de amanhã, recomendamos ver a segurança como um processo contínuo para aumentar os custos e reduzir a taxa de sucesso dos adversários que visam o seu ambiente.

Garantir o acesso privilegiado é importante para estabelecer garantias de segurança para os seus ativos empresariais. No entanto, deve fazer parte de um programa de segurança completo que fornece garantias de segurança contínuas. Este programa deve incluir elementos como:

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
* Conceder acesso privilegiado apenas quando necessário, e removê-lo depois (just-in-time)
* Manter registos de atividades de auditoria relativos a contas privilegiadas

Para obter mais informações sobre a construção de um roteiro completo de segurança, consulte [os recursos de arquitetura de TI na nuvem da Microsoft.](https://almbok.com/office365/microsoft_cloud_it_architecture_resources) Para se envolver com os serviços da Microsoft para o ajudar a implementar qualquer parte do seu roteiro, contacte o seu representante da Microsoft ou consulte [a Build critical cyber defenses para proteger a sua empresa.](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)

Esta fase final em curso do roteiro de acesso privilegiado garantido inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação geral

#### <a name="review-admin-roles-in-azure-ad"></a>Rever funções de administração em Azure AD

Determine se as atuais funções de administração Azure AD incorporadas ainda estão atualizadas e certifique-se de que os utilizadores estão apenas nas funções de que necessitam. Com a Azure AD, pode atribuir administradores separados para servirem diferentes funções. Para obter mais informações, consulte [atribuindo funções de administrador no Diretório Ativo Azure](permissions-reference.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Rever utilizadores que tenham administração de Azure AD aderiram a dispositivos

Para obter mais informações, consulte [como configurar dispositivos híbridos Azure Ative Directory](../devices/hybrid-azuread-join-plan.md).

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Reveja membros de [funções de administração da Microsoft 365 incorporadas](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Salta este passo se não estiveres a usar o Microsoft 365.
‎
#### <a name="validate-incident-response-plan"></a>Validar plano de resposta a incidentes

Para melhorar o seu plano, a Microsoft recomenda que valide regularmente que o seu plano funciona como esperado:

* Veja o seu roteiro existente para ver o que foi perdido
* Com base na análise pós-morte, reveja as práticas existentes ou defina novas práticas
* Certifique-se de que o seu plano e práticas de resposta a incidentes atualizados são distribuídos por toda a sua organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Medidas adicionais para organizações que gerem o acesso ao Azure 

Determine se precisa transferir a [propriedade de uma subscrição do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Quebrar vidro": o que fazer em caso de emergência

![Contas para o acesso de vidro de rutura de emergência](./media/security-planning/emergency.jpeg)

1. Notifique os gestores-chave e os oficiais de segurança com informações sobre o incidente.

2. Reveja o seu livro de jogadas de ataque.

3. Aceda ao nome de utilizador da conta "break glass" e à combinação de palavra-passe para iniciar sação no Azure AD.

4. Obtenha ajuda da Microsoft abrindo um pedido de [suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Veja os [relatórios de inscrição da AD Azure.](../reports-monitoring/overview-reports.md) Pode haver algum tempo entre um evento que ocorre e quando está incluído no relatório.

6. Para ambientes híbridos, se a sua infraestrutura no local federada e o seu servidor AD FS não estiver disponível, pode mudar temporariamente de autenticação federada para utilizar a sincronização de haxixe de palavra-passe. Este switch reverte a federação de domínio de volta para a autenticação gerida até que o servidor AD FS fique disponível.

7. Monitorize o e-mail para contas privilegiadas.

8. Certifique-se de guardar cópias de registos relevantes para uma potencial investigação forense e legal.

Para obter mais informações sobre como o Microsoft Office 365 lida com incidentes de segurança, consulte [a Gestão de Incidentes de Segurança no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>FAQ: Respostas para garantir acesso privilegiado  

**Q:** O que faço se ainda não implementei componentes de acesso seguro?

**Resposta:** Defina pelo menos duas contas de break-glass, atribua MFA às suas contas de administração privilegiadas e contas de utilizadores separadas das contas de administração Global.

**Q:** Depois de uma violação, qual é a questão principal que tem de ser abordada primeiro?

**Resposta:** Certifique-se de que precisa da autenticação mais forte para indivíduos altamente expostos.

**Q:** O que acontece se os nossos administradores privilegiados tiverem sido desativados?

**Resposta:** Crie uma conta de administração Global que seja sempre atualizada.

**Q:** O que acontece se só restam um administrador global e não puderem ser contactados?

**Resposta:** Use uma das suas contas de break-glass para obter acesso privilegiado imediato.

**Q:** Como posso proteger os administradores da minha organização?

**Resposta:** Os administradores fazem sempre o seu dia-a-dia como utilizadores "desfavorecidos".

**Q:** Quais são as melhores práticas para criar contas de administração dentro da Azure AD?

**Resposta:** Reserve acesso privilegiado para tarefas específicas de administração.

**Q:** Que ferramentas existem para reduzir o acesso persistente à administração?

**Resposta:** Gestão privilegiada de identidade (PIM) e funções de administração Ad Ad Azure.

**Q:** Qual é a posição da Microsoft sobre sincronizar contas de administração para a Azure AD?

**Resposta:** As contas de administração de nível 0 são utilizadas apenas para contas AD no local. Tais contas não são tipicamente sincronizadas com Azure AD na nuvem. As contas administrativas de nível 0 incluem contas, grupos e outros ativos que possuam controlo administrativo direto ou indireto da floresta, domínios, controladores de domínio e ativos do Diretório Ativo.

**Q:** Como evitar que os administradores atribuam acesso a administradores aleatórios no portal?

**Resposta:** Utilize contas não privilegiadas para todos os utilizadores e a maioria dos administradores. Comece por desenvolver uma pegada da organização para determinar quais as poucas contas de administrador que devem ser privilegiadas. E monitor para utilizadores administrativos recém-criados.

## <a name="next-steps"></a>Passos seguintes

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) – Funcionalidades de segurança dos produtos e serviços da Microsoft na nuvem

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) - O conjunto abrangente de ofertas de conformidade da Microsoft para serviços na nuvem

* [Orientações sobre como fazer uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Gerir os requisitos de segurança e conformidade para os serviços na nuvem da Microsoft

### <a name="other-microsoft-online-services"></a>Outros serviços online da Microsoft

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune fornece capacidades de gestão de dispositivos móveis, gestão de aplicações móveis e gestão de PC a partir da nuvem.

* [Segurança Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 é a solução baseada na nuvem da Microsoft que unifica as capacidades de gestão de relacionamento com o cliente (CRM) e planeamento de recursos empresariais (ERP).