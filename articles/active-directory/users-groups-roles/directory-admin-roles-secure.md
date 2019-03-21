---
title: Melhores práticas para proteger o acesso de administrador - Azure Active Directory | Documentos da Microsoft
description: Certifique-se de que as contas de acesso e administrador administrativas da sua organização são seguras. Para arquitetos de sistemas e profissionais de TI que configurar o Azure AD, Azure e Serviços Online da Microsoft.
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 03/18/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81d09978c3333a5b76c09f8c7dac85998d342f03
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287235"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Proteger o acesso privilegiado para implementações híbridas e na cloud no Azure AD

A segurança da maioria ou todos os ativos empresariais numa organização moderna depende da integridade das contas com privilégios que administram e gerem os sistemas de TI. Atores maliciosos, incluindo os ciberatacantes, muitas vezes, direcionar contas de administrador e outros elementos de acesso privilegiado à tentativa de forma a obter acesso a dados confidenciais e sistemas com ataques de roubo de credenciais. Para a cloud services, a prevenção e resposta são as responsabilidades conjuntas do fornecedor de serviços cloud e o cliente. Para obter mais informações sobre as ameaças mais recentes para os pontos finais e a nuvem, consulte a [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report). Este artigo pode ajudá-lo a desenvolver um plano para fechar as lacunas entre seus planos atuais e as orientações descritas aqui.

> [!NOTE]
> A Microsoft está comprometida com os mais altos níveis de confiança, transparência, conformidade com as normas e a conformidade a normas. Saiba mais sobre como a equipe de resposta a incidentes globais da Microsoft atenua os efeitos dos ataques lançados contra a serviços cloud e como a segurança é criada em produtos comerciais da Microsoft e serviços em nuvem da [Microsoft Trust Center - Security](https://www.microsoft.com/trustcenter/security)e os objetivos de conformidade da Microsoft na [Microsoft Trust Center - conformidade](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Na maioria das organizações, a segurança dos ativos empresariais depende da integridade das contas com privilégios que administram e gerem os sistemas de TI. Os ciberatacantes concentrar-se no acesso privilegiado para sistemas de infraestrutura (por exemplo, Active Directory e Azure Active Directory) para obter acesso a dados confidenciais de uma organização. 

As abordagens tradicionais que se concentram em proteger os pontos de entrada e saída de uma rede como perímetro segurança principal são menos eficazes devido ao aumento na utilização de aplicações SaaS e dispositivos pessoais na Internet. A substituição natural do perímetro de segurança de rede numa empresa moderna complexa é os controlos de autenticação e autorização na camada de identidade da organização.

Contas administrativas privilegiadas estão efetivamente controlar este novo "perímetro de segurança." É fundamental para proteger o acesso privilegiado, independentemente se o ambiente está no local, na cloud ou híbrida no local e na cloud dos serviços hospedados. Proteger o acesso administrativo contra adversários requer a adotar uma abordagem completa e meticulosa para isolar os sistemas da sua organização de riscos. 

Proteger o acesso privilegiado requer alterações ao

* Processos e práticas administrativas e gerenciamento de conhecimentos
* Componentes técnicos como defesas de anfitriões, proteções de contas e gestão de identidades

Este documento se concentra principalmente em criar um mapa para proteger identidades e acesso que são geridos ou comunicado no Azure AD, Microsoft Azure, serviços do Office 365 e outros na cloud. Para organizações que tenham no local contas administrativas, consulte a documentação de orientação para locais e híbridas privilegiado acesso gerido a partir do Active Directory em [proteger o acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> As orientações neste artigo refere-se principalmente para recursos do Azure Active Directory que estão incluídos nos planos do Azure Active Directory Premium P1 e P2. O Azure Active Directory Premium P2 está incluído no EMS E5 suite e o pacote do Microsoft 365 E5. Esta orientação assume que sua organização já tiver licenças do Azure AD Premium P2 compradas para os seus utilizadores. Se não tiver estas licenças, algumas das orientações podem não ser aplicadas à sua organização. Além disso, ao longo deste artigo, o administrador global do termo (ou o administrador global) é sinônimo de "administrador de empresa" ou "administrador de inquilino".

## <a name="develop-a-roadmap"></a>Desenvolver um Roteiro 

A Microsoft recomenda que desenvolve e siga um plano para proteger o acesso privilegiado contra invasores de cibersegurança. Sempre pode ajustar o seu plano para suportar as suas funcionalidades existentes e requisitos específicos da sua organização. Cada fase do plano deve aumentar o custo e a dificuldade dos adversários em ataques ao acesso privilegiado para no local, na cloud e ativos híbrida. A Microsoft recomenda as quatro fases de mapa seguintes: Esta opção recomendada agendas de plano mais eficazes e as implementações mais rápidas em primeiro lugar, com base nas experiências da Microsoft com a implementação de resposta de incidentes e de ataques informáticos. As linhas cronológicas para este plano são aproximadas.

![Fases do mapa com linhas de tempo](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fase 1 (24-48 horas): Itens críticos que recomendamos que faça agora mesmo

* Fase 2 (2 a 4 semanas): Mitigar as técnicas de ataque utilizadas com mais frequência

* Fase 3 (1 a 3 meses): Criar visibilidade e controlo total da atividade de administração de compilação

* Fase 4 (seis meses e muito mais): Continuar a criar defensas para proteger ainda mais a sua plataforma de segurança

Essa estrutura de plano foi concebida para maximizar a utilização de tecnologias da Microsoft que já tenha implementado. Também pode tirar partido das tecnologias de chave de segurança atuais e futuras e integrar ferramentas de segurança de outros fornecedores que já tiver implementado ou que está pensando em implantar. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fase 1: Itens críticos que recomendamos que faça agora mesmo

![Itens de críticas a fase 1 fazer primeiro](./media/directory-admin-roles-secure/stage-one.png)

Fase 1 do plano se concentra em tarefas críticas que são rápidos e fáceis de implementar. Recomendamos que efetue estes itens alguns imediatamente dentro das primeira 24-48 horas para garantir um nível básico de proteger o acesso privilegiado. Nesta fase do plano protegida acesso privilegiado inclui as seguintes ações:

### <a name="general-preparation"></a>Preparação gerais

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Ativar o Azure AD Privileged Identity Management

Se já não tiver ativado o Azure AD Privileged Identity Management (PIM), faça-o no seu inquilino de produção. Depois de ativar a Privileged Identity Management, irá receber uma notificação de e-mail mensagens para acesso privilegiado alterações de função. Estas notificações fornecem aviso antecipado quando os utilizadores adicionais são adicionados a funções com privilégios elevados no seu diretório.

O Azure AD Privileged Identity Management está incluído no Azure AD Premium P2 ou EMS E5. Estas soluções ajudam a proteger o acesso aos recursos e aplicações em todo o ambiente no local e na nuvem. Se já não tiver o Azure AD Premium P2 ou EMS E5 e pretende avaliar mais dos recursos referenciados neste plano, inscreva-se para o [Enterprise Mobility + Security avaliação gratuita de 90 dias](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Utilize estas versões de avaliação de licença para experimentar o Azure AD Privileged Identity Management e o Azure AD Identity Protection, para monitorizar a atividade com o Azure AD avançadas de relatórios de segurança, auditoria e alertas.

Depois de ter ativado no Azure AD Privileged Identity Management:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) com uma conta que seja um administrador global do seu inquilino de produção.

2. Para selecionar o inquilino em que pretende utilizar Privileged Identity Management, selecione o seu nome de utilizador no canto superior direito do portal do Azure.

3. Selecione **todos os serviços** e filtre a lista de **Azure AD Privileged Identity Management**.

4. Abra o Privileged Identity Management, do **todos os serviços** listar e afixá-la ao seu dashboard.

A primeira pessoa a utilizar o Azure AD Privileged Identity Management no seu inquilino será automaticamente atribuída a **administrador de segurança** e **administrador com função privilegiada** funções no inquilino. Apenas os administradores de função com privilégios podem gerir as atribuições de funções de diretório do Azure AD dos utilizadores. Além disso, depois de adicionar o Azure AD Privileged Identity Management, são apresentados o Assistente de segurança que o orienta através da experiência de deteção e atribuição inicial. Pode sair do assistente sem fazer alterações adicionais neste momento. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificar e categorizar as contas que são em funções com privilégios elevados 

Depois de ativar o Azure AD Privileged Identity Management, veja os utilizadores que estão no Administrador Global de funções de diretório, o administrador com função privilegiada, o administrador do Exchange Online e o administrador do SharePoint Online. Se não tiver o PIM do Azure AD no seu inquilino, pode utilizar o [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Iniciar com a função de administrador global, pois esta função é genérica: um utilizador a quem é atribuído esta função de administrador tem as mesmas permissões em todos os serviços de cloud para o qual a sua organização tem subscritas, independentemente de eles já foi atribuídos esta função no Microsoft 365 Centro de administração do Azure portal, ou utilizando o módulo do Azure AD para o Microsoft PowerShell. 

Remova as contas que já não são necessários nessas funções. Em seguida, categorize as contas restantes são atribuídas às funções de administrador:

* Individualmente atribuídas a utilizadores administrativos e também podem ser utilizadas para fins não administrativos (por exemplo, e-mail pessoal)
* Individualmente designada para fins administrativos apenas e atribuídas a utilizadores administrativos
* Compartilhado entre vários utilizadores
* Para cenários de acesso de emergência break glass
* Para scripts automatizados
* Para utilizadores externos

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definir, pelo menos, duas contas de acesso de emergência 

Certifique-se de que não obtém com uma situação onde eles poderiam ser inadvertidamente impedidos de aceder a administração de inquilino do Azure AD devido a uma incapacidade para iniciar sessão ou ativar a conta de um usuário individual existente como administrador. Por exemplo, se a organização estiver federada para um fornecedor de identidade no local, esse fornecedor de identidade pode estar indisponível, para que os utilizadores não podem iniciar sessão no local. Pode mitigar o impacto de acidental falta de acesso administrativo ao armazenar duas ou mais contas de acesso de emergência no seu inquilino.

Contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado num ambiente do Azure Active Directory existente. Estas contas são altamente privilegiadas e não estão atribuídas a indivíduos específicos. Contas de acesso de emergência estão limitadas a emergência para "break glass" cenários onde não não possível utilizar contas administrativas normais. As organizações devem garantir o objetivo de controlar e reduzir a utilização da conta de emergência para apenas esse tempo para os quais é necessário.

Avalie as contas que são atribuídas ou elegíveis para a função de administrador global. Se não vir nenhum apenas na cloud contas com o *. onmicrosoft.com domínio (previstas para acesso de emergência "break glass"), criá-los. Para obter mais informações, consulte [gerir contas de administrador de acesso de emergência no Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Ativar a autenticação multifator e registar todas as outras contas de administrador de não federadas de utilizador único de privilégios elevados

Precisa do Azure multi-factor Authentication (MFA), no início de sessão para todos os utilizadores individuais que são permanentemente atribuídos a um ou mais funções de administrador do Azure AD: Administrador global, o administrador com função privilegiada, o administrador do Exchange Online e o administrador do SharePoint Online. Utilize o guia para habilitar [multi-factor Authentication (MFA) para as suas contas de administrador](../authentication/howto-mfa-userstates.md) e certifique-se de que todos esses utilizadores registarem no [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Podem encontrar mais informações no passo 2 e o passo 3 do guia [proteger o acesso a dados e serviços do Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fase 2: Mitigar as técnicas de ataque utilizadas com mais frequência

![Ataques de Mitigate usado com freqüência de fase 2](./media/directory-admin-roles-secure/stage-two.png)

Estágio 2 dos focos de mapa na mitigação mais frequentemente utilizadas técnicas de ataque de roubo de credenciais e abuso e pode ser implementado em aproximadamente 2-4 semanas. Nesta fase do plano protegida acesso privilegiado inclui as seguintes ações.

### <a name="general-preparation"></a>Preparação gerais

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Conduzir um inventário dos serviços, os proprietários e os administradores

Com o aumento no bring-your-own device (BYOD) e políticas de trabalho a partir de casa e o crescimento de conectividade sem fio em empresas, é fundamental monitorizar que estiver a ligar à sua rede. Uma auditoria de segurança efetivas revela, muitas vezes, dispositivos, aplicações e programas em execução na sua rede que não são suportadas pelo IT e, portanto, potencialmente não segura. Para obter mais informações, consulte [gestão de segurança do Azure e a descrição geral da monitorização](../../security/security-management-and-monitoring-overview.md). Certifique-se de que inclui todas as tarefas seguintes no processo de inventário. 

* Identificar os utilizadores que têm funções administrativas e os serviços onde pode gerir.
* Utilize o PIM do Azure AD para saber quais os utilizadores na sua organização tenham acesso de administrador para o Azure AD, incluindo funções adicionais além daqueles listados na fase 1.
* Além das funções definidas no Azure AD, o Office 365 é fornecido com um conjunto de funções de administrador pode atribuir aos utilizadores na sua organização. Cada função de administrador é mapeado para funções de negócios comuns e, com as pessoas em suas permissões de organização para fazer tarefas específicas no [Centro de administração do Microsoft 365](https://admin.microsoft.com). Utilize o Centro de administração do Microsoft 365 para saber quais os utilizadores na sua organização têm acesso administrativo ao Office 365, incluindo através de funções não são geridas no Azure AD. Para obter mais informações, consulte [funções de administrador sobre o Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e [melhores práticas de segurança para o Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Execute o inventário de outros serviços na que sua organização baseia-se, por exemplo, o Azure, o Intune ou o Dynamics 365.
* Certifique-se de que as contas de administrador (contas que são utilizadas para fins de administração, não apenas contas diárias de utilizador) tem que trabalhar com os endereços de e-mail anexados e tenham registado para MFA do Azure ou utilizam a MFA no local.
* Peça aos utilizadores para a justificação de negócio para acesso administrativo.
* Remova o acesso de administrador para esses indivíduos e serviços que não precisam dela.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identificar as contas Microsoft funções administrativas que têm de ser mudado para contas escolares ou

Às vezes, os administradores globais iniciais para uma organização reutilizar as credenciais da conta Microsoft já quando eles começaram a utilizar o Azure AD. Essas contas Microsoft devem ser substituídas por contas individuais, com base na cloud ou sincronizadas. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Certifique-se de que as contas de utilizador em separado e reencaminhamento para contas de administrador global de correio 

As contas de administrador global não devem ter endereços de e-mail pessoal, como contas de e-mail pessoais são regularmente phished pelos atacantes de cibersegurança. Para ajudar a separar os riscos da internet (ataques de phishing, navegação na web não intencional) dos privilégios administrativos, crie contas dedicadas para cada utilizador com privilégios administrativos. 

Se ainda não o fez, crie contas separadas para os usuários executem tarefas de administrador global, para se certificar-se de que não inadvertidamente abrir e-mails ou executar programas associados com as suas contas de administrador. Certifique-se de que essas contas têm o respetivo e-mail reencaminhado para uma caixa de correio do trabalho.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Certifique-se de que as palavras-passe de contas administrativas tem alterado recentemente

Certifique-se de que todos os utilizadores tem sessão iniciada nas respetivas contas administrativas e alterar as palavras-passe, pelo menos, uma vez nos últimos 90 dias. Além disso, certifique-se de que todas as contas partilhadas em que vários utilizadores sabem a palavra-passe tenha tido as palavras-passe recentemente alteradas.

#### <a name="turn-on-password-hash-synchronization"></a>Ativar sincronização de hash de palavra-passe

Sincronização de hash de palavra-passe é uma funcionalidade utilizada para sincronizar os hashes de hashes de palavra-passe de utilizador de uma instância do Active Directory no local para um Azure baseado na nuvem de instância do AD. Mesmo que se decidir usar a Federação com os serviços de Federação do Active Directory (AD FS) ou de outros fornecedores de identidade, opcionalmente, pode configurar a sincronização de hash de palavra-passe como uma cópia de segurança caso sua infraestrutura no local como o AD ou servidores ADFS falhar ou ficar temporariamente indisponível. Isto permite aos utilizadores iniciar sessão serviço ao utilizar a mesma palavra-passe que utiliza para iniciar sessão no local instância do AD. Além disso, ele permite a proteção de identidade detetar credenciais comprometidas, ao comparar os hashes de palavra-passe com palavras-passe conhecidas para ficar comprometida, se um utilizador tirou o mesmo endereço de e-mail e a palavra-passe noutros serviços não ligada ao Azure AD.  Para obter mais informações, consulte [implementar a sincronização de hash de palavra-passe com o Azure AD Connect sync](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Exigir autenticação multifator (MFA) para utilizadores nas funções privilegiadas tudo, bem como de utilizadores expostos

O Azure AD recomenda que exigem autenticação multifator (MFA) para todos os seus utilizadores, incluindo administradores e todos os outros utilizadores que teriam um impacto significativo se a sua conta foi comprometida (por exemplo, responsáveis financeiros). Isso reduz o risco de um ataque devido a uma palavra-passe comprometida.

Ative:

* [MFA através de políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) para todos os utilizadores na sua organização.

Se utilizar o Windows Hello para empresas, o requisito de MFA pode ser atendido usando o Windows Hello início de sessão na experiência. Para obter mais informações, consulte [Hello do Windows](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurar Identity Protection 

O Azure AD Identity Protection é uma ferramenta que pode utilizar para detetar potenciais vulnerabilidades que afetam as identidades da sua organização de relatórios e monitorização baseada no algoritmo. Pode configurar respostas automáticas para essas atividades suspeitas detetadas e tomar as medidas adequadas para resolvê-los. Para obter mais informações, consulte [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obter a sua pontuação de segurança do Office 365 (se utilizar o Office 365)

Proteja a pontuação, descobre que serviços do Office 365 estiver a utilizar (como o OneDrive, SharePoint e Exchange), em seguida, analise as suas definições e as atividades e compara-as com uma linha de base estabelecida pela Microsoft. Receberá uma pontuação com base em como alinhados são com as melhores práticas de segurança. Qualquer pessoa que tenha permissões de administrador (administrador global ou uma função de administrador personalizadas) para uma subscrição do Office 365 empresas – versão Premium ou Enterprise pode aceder a pontuação de seguro de mensagens em fila em [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Reveja as orientações de segurança e conformidade do Office 365 (se utilizar o Office 365)

O [plano de segurança e conformidade](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) traça a abordagem de como o cliente do Office 365 deve configurar o Office 365 e tirar partido de outras funcionalidades do EMS. Em seguida, reveja os passos 3 a 6 de como [proteger o acesso a dados e serviços do Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e o guia para saber como [monitorizar a segurança e conformidade do Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configurar a monitorização da atividade do Office 365 (se utilizar o Office 365)

Pode monitorizar como as pessoas na sua organização estiver a utilizar serviços do Office 365, permitindo-lhe identificar os utilizadores que tenham uma conta de administrador e que não precisa do Office 365 acessem devido a não iniciar sessão nesses portais. Para obter mais informações, consulte [relatórios de atividade no Centro de administração do Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Estabelecer os proprietários de plano de resposta de incidente/emergência

Executar com eficiência a resposta a incidentes é uma tarefa complexa. Por conseguinte, estabelecer uma capacidade de resposta a incidentes bem-sucedido requer um planeamento considerável e recursos. É essencial que continuamente monitorizar para ataques informáticos e estabelecer procedimentos para priorizar o tratamento de incidentes. Os métodos em vigor a partir de recolha de, analisar e dados de relatórios são essenciais para criar relações e para estabelecer comunicação com outros grupos internos e planear os proprietários. Para obter mais informações, consulte [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>No local segura privilegiado contas administrativas, se não tiver feito

Se o seu inquilino do Azure Active Directory está sincronizado com o Active Directory no local, em seguida, siga as orientações no [plano do acesso privilegiado segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Fase 1. Isto inclui a criação de contas de administrador separada para os utilizadores que precisam para realizar tarefas administrativas de no local, a implementação de estações de trabalho de acesso privilegiado para administradores do Active Directory e criar palavras-passe de administrador local exclusivas para estações de trabalho e servidores.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para as organizações a gerir o acesso para o Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Concluir um inventário das subscrições

Utilize o portal da empresa e o portal do Azure para identificar as subscrições na sua organização que alojam aplicações de produção.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Remover contas Microsoft de funções de administrador

As contas Microsoft de outros programas, como Xbox Live e Outlook não devem ser utilizadas como contas de administrador para as subscrições organizacionais. Remover o estado de administrador de todas as contas Microsoft e substitua com o Azure Active Directory (por exemplo, chris@contoso.com) contas escolares ou.

#### <a name="monitor-azure-activity"></a>Monitorizar a atividade do Azure

O registo de atividades do Azure fornece um histórico de eventos ao nível da subscrição no Azure. Oferece informações sobre quem criadas, atualizadas e eliminadas que recursos, e quando esses eventos ocorreram. Para obter mais informações, consulte [auditoria e receber notificações sobre ações importantes efetuadas na sua subscrição do Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passos adicionais para as organizações a gerir o acesso a outras aplicações na cloud através do Azure AD

#### <a name="configure-conditional-access-policies"></a>Configurar políticas de acesso condicional

Prepare as políticas de acesso condicional para aplicações alojadas na cloud e no local. Se tiver dispositivos associados à área de utilizadores, obter mais informações sobre o [configurar no local acesso condicional com o registo de dispositivos do Azure Active Directory](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fase 3: Criar visibilidade e controle completo da atividade de administrador

![Fase 3 assumir o controlo da atividade de administração](./media/directory-admin-roles-secure/stage-three.png)

Fase 3 baseia-se nas mitigações da fase 2 e foi concebida para ser implementada em cerca de 1 a 3 meses. Nesta fase do plano protegida acesso privilegiado inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação gerais

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Concluir uma revisão de acesso de utilizadores nas funções de administrador

Mais os usuários estão ganhando acesso privilegiado através de serviços cloud, que pode levar a uma plataforma de cada vez maior de não-gerenciada. Inclui os utilizadores se tornando os administradores globais para o Office 365, os administradores de subscrição do Azure e os utilizadores que têm acesso administrativo às VMs ou através de aplicações SaaS. Em vez disso, as organizações devem ter todos os funcionários, especialmente os administradores, manipular transações comerciais diárias como utilizadores sem privilégios e demorar apenas nos direitos administrativos, conforme necessário. Uma vez que o número de utilizadores nas funções de administrador pode cresceram desde adoção inicial, revisões de acesso completo para identificar e confirme a todos os utilizadores a quem é elegível para ativar os privilégios de administrador. 

Faça o seguinte:

* Determine quais usuários são administradores, ativar a pedido, acesso de administrador just-in-time e controlos de segurança baseada em funções do Azure AD.
* Converter os utilizadores que têm não justificativa clara para acesso de administrador com privilégios para uma função diferente (se nenhuma função elegível, removê-los o).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuar a implementação de uma autenticação mais forte para todos os utilizadores 

Exigir executivos de C do conjunto de aplicações, gestores de alto nível, críticas IT e equipe de segurança e outros utilizadores e altamente expostos ter autenticação forte e modernos, como o MFA do Azure ou o Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Utilizar estações de trabalho dedicadas para a administração para o Azure AD

Os atacantes podem tentar atingir contas privilegiadas para obter acesso a dados e sistemas de uma organização para que eles podem interromper a integridade e a autenticidade de dados, por meio de código mal-intencionado que altera a lógica do programa ou snoops o introduzir uma credencial de administrador. Estações de trabalho acesso privilegiado (PAWs) fornecem um sistema operativo dedicado para tarefas confidenciais sejam protegidos contra ataques de Internet e vetores de ameaças. Separar estas contas e tarefas confidenciais diária utilizar estações de trabalho e dispositivos fornece proteção muito forte contra ataques de phishing, aplicação e OS vulnerabilidades vários ataques de representação e ataques de roubo de credenciais, tais como o pressionamento de tecla Registro em log, Pass-the-Hash e Pass-The-Ticket. Ao implementar estações de trabalho de acesso privilegiado, pode reduzir o risco de que os administradores de introduzir as credenciais de administrador, exceto num ambiente de área de trabalho que tenha sido protegido. Para obter mais informações, consulte [estações de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Rever as recomendações do Instituto nacional de normas e tecnologia para processamento de incidentes 

O National Institute of Standards e a tecnologia (NIST) fornece diretrizes para tratamento de incidentes, particularmente para analisar dados relacionados com o incidente e determinar a resposta adequada para cada incidente. Para obter mais informações, consulte [(NIST) o computador incidente manipulação de guia de segurança (SP 800 61, revisão 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementar o Privileged Identity Management (PIM) para o JIT para funções administrativas adicionais

Para o Azure Active Directory, utilize [do Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) capacidade. Ativação de tempo limitado de funções privilegiadas funciona ao permitir-lhe:

* Ativar os privilégios de administrador para executar uma tarefa específica
* Impor o MFA durante o processo de ativação
* Utilizar alertas para informar os administradores sobre as alterações de fora de banda
* Permitir que os utilizadores manter a determinados privilégios durante um período de tempo pré-configurado
* Permitir que os administradores de segurança detetar todas as identidades privilegiadas, ver relatórios de auditoria e criar as revisões de acesso para identificar todos os utilizadores a quem é elegível para ativar os privilégios de administrador

Se já estiver a utilizar o Azure AD Privileged Identity Management, ajuste os períodos de tempo limite de tempo privilégios conforme necessário (por exemplo, janelas de manutenção).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determinar a exposição a protocolos baseados em palavra-passe de início de sessão (se utilizar o Exchange Online)

No passado, protocolos, pressupõe-se de que as combinações de nome de utilizador/palavra-passe foram incorporadas em dispositivos, contas de e-mail, telefones e assim por diante. Mas agora com o risco de ataques informáticos na cloud, recomendamos que identifique todos os utilizadores potenciais que, se as credenciais foram comprometidas, poderiam ser catastrófica na organização e excluí-los de conseguir iniciar sessão no respetivo e-mail através do nome de utilizador / palavra-passe através da implementação de acesso condicional e os requisitos de autenticação forte. Pode bloquear [antigos de autenticação através do acesso condicional](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/block-legacy-authentication). Verifique os detalhes no [como bloquear a autenticação básica](https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) por meio de Exchnage online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Concluir uma avaliação de revisão de funções para funções do Office 365 (se utilizar o Office 365)

Avalie se a todos os utilizadores administradores são nas funções de corretas (eliminar e reatribuir, de acordo com esta avaliação).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Reveja a abordagem de gestão de incidentes de segurança utilizada no Office 365 e comparar com a sua organização

Pode transferir este relatório a partir da [gestão de incidentes de segurança no Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuar a proteger contas no local com privilégios administrativos

Se o Azure Active Directory é ligado ao Active Directory no local, em seguida, siga as orientações a [plano do acesso privilegiado segurança](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): Fase 2. Isto inclui a implementação de estações de trabalho de acesso privilegiado para todos os administradores, exigir a MFA, usando apenas suficiente administração para manutenção de DC, reduzir a superfície de ataque de domínios, implementar o ATA para a deteção de ataque.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para as organizações a gerir o acesso para o Azure

#### <a name="establish-integrated-monitoring"></a>Estabelecer monitorização integrada

O [Centro de segurança do Azure](../../security-center/security-center-intro.md) fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de segurança soluções.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventário de contas privilegiadas dentro de máquinas virtuais alojadas

Na maioria dos casos, não precisa fornecer permissões sem restrições de utilizadores a todas as suas subscrições do Azure ou recursos. Pode utilizar funções de administrador do Azure AD para separar responsabilidades dentro da sua organização e conceder apenas a quantidade de acesso a utilizadores que precisam para realizar tarefas específicas. Por exemplo, use funções de administrador do Azure AD para permitir que um administrador gerir apenas as VMs numa subscrição, enquanto outro pode gerir bases de dados do SQL na mesma subscrição. Para obter mais informações, consulte [introdução ao controlo de acesso baseado em funções no portal do Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementar o PIM para funções de administrador do Azure AD

Utilize o Privileged identity Management com funções de administrador do Azure AD para gerir, controlar e monitorizar o acesso aos recursos do Azure. Utilizar o PIM protege as contas com privilégios de ataques informáticos ao reduzir o tempo de exposição dos privilégios e aumentar a visibilidade sobre seu uso por meio de relatórios e alertas. Para obter mais informações, consulte [acesso de gerir o RBAC para recursos do Azure Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Utilize as integrações de registos do Azure para enviar relevantes do Azure regista-se aos seus sistemas SIEM 

Integração de registos do Azure permite-lhe integrar registos não processados do seus recursos do Azure aos sistemas de gestão de eventos (SIEM) e de informações de segurança existentes da sua organização. [Integração de registos do Azure](../../security/security-azure-log-integration-overview.md) recolhe eventos do Windows a partir de registos do Visualizador de eventos do Windows e recursos do Azure de registos de atividades do Azure, alertas do Centro de segurança do Azure e os registos de diagnóstico do Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passos adicionais para as organizações a gerir o acesso a outras aplicações na cloud através do Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementar o aprovisionamento de utilizadores para aplicações ligadas

O Azure AD permite-lhe automatizar a criação, manutenção e remoção de identidades de utilizador em aplicações de cloud (SaaS) como o Dropbox, Salesforce, ServiceNow e assim por diante. Para obter mais informações, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure AD de desaprovisionamento](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrar a proteção de informações

MCAS permite-lhe investigar ficheiros e definir políticas com base em etiquetas de classificação do Azure Information Protection, proporcionando uma maior visibilidade e controlo dos seus dados na cloud. Analise e classificar ficheiros na cloud e aplique etiquetas de proteção de informações do Azure. Para obter mais informações, consulte [integração do Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurar o acesso condicional

Configurar o acesso condicional com base num grupo, a localização e a sensibilidade das aplicações para [aplicações SaaS](https://azure.microsoft.com/overview/what-is-saas/) e aplicações ligadas do Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorizar a atividade em aplicações na cloud ligadas

Para garantir o acesso dos utilizadores está protegido no aplicativos conectados, bem, recomendamos que aproveitar [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Isso protege o acesso da empresa para a cloud a aplicações, além de proteger as suas contas de administrador, permitindo que:

* Expandir a visibilidade e controlo de aplicações na cloud
* Criar políticas de acesso, atividades e partilha de dados
* Identifique automaticamente as atividades de risco, comportamentos anormais e ameaças
* Evitar a fuga de dados
* Minimizar o risco e a prevenção de ameaças automática e a imposição de políticas

O agente SIEM do Cloud App Security se integra o Cloud App Security com o seu servidor SIEM para ativar a monitorização centralizada de atividades e alertas do Office 365. Ele é executado no seu servidor e extrai atividades e alertas do Cloud App Security e transmite-os para o servidor SIEM. Para obter mais informações, consulte [integração da SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fase 4: Continuar a criar defensas para uma postura de segurança proativa

![Fase 4 Adote uma postura de segurança proativa](./media/directory-admin-roles-secure/stage-four.png)

Fase 4 do plano baseia-se a visibilidade da fase 3 e foi concebida para ser implementada em seis meses e muito mais. Concluir um ajuda de mapa desenvolver strong privilegiado proteções de acesso de potenciais ataques que estão atualmente conhecidos e disponíveis hoje em dia. Infelizmente, as ameaças de segurança constantemente evoluam e mudança, pelo que recomendamos que encare de segurança como um processo contínuo focado no aumento do custo e reduzindo a taxa de êxito dos adversários direcionamento de seu ambiente.

Com privilégios de proteger o acesso é um primeiro passo crítico para estabelecer garantias de segurança para ativos empresariais numa organização moderna, mas não é apenas uma parte de um programa de segurança completa, que incluem elementos, tais como a política, as operações informações segurança, servidores, aplicações, PCs, dispositivos, recursos de infraestrutura de nuvem e outros componentes fornecem garantias de segurança em curso. 

Além de gerir as contas de acesso privilegiado, recomendamos que reveja o seguinte numa base contínua:

* Certifique-se de que os administradores estão a fazer suas cotidianas de negócios como utilizadores sem privilégios.
* Só conceder o acesso com privilégios quando necessário e removê-lo mais tarde (just-in-time).
* Retenção e a análise de atividade de auditoria relativas a contas com privilégios.

Para obter mais informações sobre como criar um plano de segurança completo, consulte [recursos de arquitetura de TI da Microsoft cloud](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Para obter mais informações sobre como utilizar os serviços da Microsoft para ajudá-lo em qualquer um destes tópicos, contacte o seu representante da Microsoft ou consulte [criar defesas informáticos críticos, para proteger a sua empresa](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Neste estágio final em curso do plano protegida acesso privilegiado inclui os seguintes componentes.

### <a name="general-preparation"></a>Preparação gerais

#### <a name="review-admin-roles-in-azure-active-directory"></a>Reveja as funções de administrador no Azure Active Directory 

Determinar se as funções de administrador do atual incorporado do Azure AD estão ainda atualizadas e certifique-se de que os usuários são apenas nas funções e as delegações que precisam de permissões correspondentes. Com o Azure AD, pode designar administradores separados para servir as funções diferentes. Para obter mais informações, consulte [atribuir funções de administrador no Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Dispositivos associados ao utilizadores de revisão que têm de administração do Azure AD

Para obter mais informações, consulte [como configurar híbrida do Azure Active Directory dispositivos associados ao](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Reveja os membros do [funções de administrador incorporadas do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se estiver a utilizar o Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Validar o plano de resposta a incidentes

Para melhorar seu plano, a Microsoft recomenda que regularmente a validar que o seu plano funciona conforme esperado:

* Percorrer o mapa da estrada existente para ver o que estava em falta
* Com base na análise postmortem, rever existente ou definir novas práticas recomendadas
* Certifique-se de que o seu plano de resposta a incidentes atualizada e as melhores práticas são distribuídas em toda a organização


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passos adicionais para as organizações a gerir o acesso para o Azure 

Determinar se necessita [transferir a propriedade de uma subscrição do Azure para outra conta](../../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break glass": o que fazer numa emergência

![Contas para acesso de vidro de garantia de reparação de emergência](./media/directory-admin-roles-secure/emergency.jpeg)

1. Notifique os principais gerentes e responsáveis pela segurança com informações pertinentes relacionadas com o incidente.

2. Reveja o playbook de ataque. 

3. Aceda a combinação de nome de utilizador/palavra-passe de conta "break glass" para iniciar sessão com o Azure AD. 

4. Obtenha ajuda da Microsoft pela [abrir um pedido de suporte do Azure](../../azure-supportability/how-to-create-azure-support-request.md).

5. Examinar os [relatórios de início de sessão do Azure AD](../reports-monitoring/overview-reports.md). Pode haver um atraso entre um evento que ocorre e quando este está incluído no relatório.

6. Para ambientes híbridos, se federada e o AD FS servidor estiver indisponível, poderá ter de mudar temporariamente da autenticação federada para utilizar a sincronização de hash de palavra-passe. Isso reverte a Federação do domínio para a autenticação gerida até que o servidor do AD FS fique disponível.

7. Monitorizar o e-mail para contas com privilégios.

8. Certifique-se de que guardar as cópias de segurança de registos relevantes para potencial investigação forense e legal.

Para obter mais informações sobre como o Microsoft Office 365 lida com incidentes de segurança, consulte [gestão de incidentes de segurança no Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>FAQ: Perguntas comuns que recebemos relativamente à proteção de acesso privilegiado  

**P:** O que devo fazer se eu ainda não Implementei quaisquer componentes de acesso seguro?

**Resposta:** Definir a conta de efeito de vidro break, pelo menos, dois, atribuir MFA às suas contas de administrador com privilégios e contas de utilizador em separado das contas de Administrador Global.

**P:** Depois de uma violação, o que é o problema principal que precisa ser resolvidos primeiro?

**Resposta:** Certifique-se de que está a exigir a autenticação mais forte para indivíduos e altamente expostos.

**P:** O que acontece se foram desativados nossos administradores com privilégios?

**Resposta:** Crie uma conta de Administrador Global que é sempre mantida atualizada.

**P:** O que acontece se houver apenas um global esquerda de administrador e não podem ser alcançados? 

**Resposta:** Utilize uma das suas contas de quebra de vidro para obter acesso privilegiado de imediato.

**P:** Como posso proteger administradores dentro da minha organização?

**Resposta:** Ter administradores sempre realizar seus negócios diários como usuários padrão de "sem privilégios".

**P:** Quais são as melhores práticas para a criação de contas de administrador no Azure AD?

**Resposta:** Acesso privilegiado de reserva para tarefas de administrador específico.

**P:** Quais ferramentas existem para reduzir o acesso de administrador persistentes?

**Resposta:** Funções de administrador do Identity Management (PIM) e o Azure AD com privilégios.

**P:** O que é a posição da Microsoft em sincronizar as contas de administrador para o Azure AD?

**Resposta:** Camada 0 contas de administrador (incluindo contas, grupos e outros recursos que têm controlo administrativo direto ou indireto da floresta do AD, domínios ou controladores de domínio e todos os ativos) são utilizadas apenas para contas AD e locais são geralmente não sincronizadas para o Azure AD para a nuvem.

**P:** Como podemos impedir que os administradores a atribuir acesso aleatório de administrador no portal do?

**Resposta:** Utilize contas sem privilégios para todos os utilizadores e a maioria dos administradores. Comece com o desenvolvimento de uma impressão digital da organização para determinar qual devem ser privilegiadas a algumas contas de administrador. E monitorize os utilizadores administrativos recentemente criado.

## <a name="next-steps"></a>Passos Seguintes

* [Microsoft Trust Center para segurança de produto](https://www.microsoft.com/trustcenter/security) – recursos de segurança do Microsoft cloud os produtos e serviços

* [Microsoft Trust Center - conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – conjunto abrangente da Microsoft de ofertas de conformidade para os serviços cloud

* [Documentação de orientação sobre como realizar uma avaliação de risco](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -gerir requisitos de segurança e conformidade para serviços cloud da Microsoft

### <a name="other-microsoft-online-services"></a>Outros serviços Online da Microsoft

* [Segurança do Microsoft Intune](https://www.microsoft.com/trustcenter/security/intune-security) – o Intune fornece gestão de dispositivos móveis, gestão de aplicações móveis e capacidades de gestão de PC da cloud.

* [Segurança do Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) – do Dynamics 365 é a solução com base na cloud da Microsoft que unifica o gerenciamento de relacionamento de cliente (CRM) e o planeamento de recursos (ERP) de recursos empresariais.
