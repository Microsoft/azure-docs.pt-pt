---
title: Proteja a sua infraestrutura de identidade Azure AD
titleSuffix: Azure Active Directory
description: Este documento descreve uma lista de ações importantes que os administradores devem implementar para ajudá-los a garantir a sua organização usando capacidades de AD Azure
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77565541"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco passos para garantir a sua infraestrutura de identidade

Se está a ler este documento, sabe o significado da segurança. Provavelmente já tem a responsabilidade de assegurar a sua organização. Se precisar de convencer os outros da importância da segurança, envie-os para ler o mais recente relatório da [Microsoft Security Intelligence](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Este documento irá ajudá-lo a obter uma postura mais segura usando as capacidades do Azure Ative Directory usando uma lista de verificação de cinco passos para inocular a sua organização contra ciberataques.

Esta lista de verificação irá ajudá-lo a implementar rapidamente ações recomendadas críticas para proteger a sua organização imediatamente, explicando como:

* Fortaleça as suas credenciais.
* Reduza a sua área de superfície de ataque.
* Automatizar a resposta à ameaça.
* Utilize a inteligência na nuvem.
* Ativar o self-service do utilizador final.

Certifique-se de que mantém o registo das funcionalidades e passos completos durante a leitura desta lista de verificação.

> [!NOTE]
> Muitas das recomendações deste documento aplicam-se apenas a aplicações que estão configuradas para utilizar o Azure Ative Directory como seu fornecedor de identidade. Configurar aplicações para Single Sign-On garante os benefícios de políticas credenciais, deteção de ameaças, auditoria, registo e outras funcionalidades adicionam a essas aplicações. [A única inscrição através do Azure Ative Directory](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) é a base - na qual todas estas recomendações se baseiam.

As recomendações deste documento estão alinhadas com a [Pontuação Segura](../../active-directory/fundamentals/identity-secure-score.md)de Identidade, uma avaliação automatizada da configuração de segurança de identidade do seu inquilino Azure AD. As organizações podem usar a página Identity Secure Score no portal Azure AD para encontrar lacunas na sua configuração de segurança atual para garantir que seguem as [melhores práticas](identity-management-best-practices.md) atuais da Microsoft para a segurança. Implementar cada recomendação na página Secure Score aumentará a sua pontuação e permitirá acompanhar o seu progresso, além de ajudá-lo a comparar a sua implementação com outras organizações de tamanho semelhante ou com a sua indústria.

![Pontuação segura de identidade](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Muitas das funcionalidades aqui descritas requerem uma subscrição Azure AD Premium, enquanto algumas são gratuitas. Por favor, reveja os preços do nosso [Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/) e a lista de verificação de implantação de [anúncios azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) para obter mais informações.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: Proteja contas privilegiadas com MFA

Antes de começar esta lista de verificação, certifique-se de que não fica comprometido enquanto estiver a ler esta lista de verificação. Primeiro tens de proteger as tuas contas privilegiadas.

Os agressores que obtêm o controlo de contas privilegiadas podem causar danos tremendos, por isso é fundamental proteger estas contas primeiro. Ative e exija a [autenticação de multi-factores](../../active-directory/authentication/multi-factor-authentication.md) (MFA) para todos os administradores da sua organização utilizando falhas de [segurança adatos azure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) ou [acesso condicional](../../active-directory/conditional-access/plan-conditional-access.md). Se ainda não implementou a MFA, fá-lo agora! É assim tão importante.

Tudo pronto? Vamos começar na lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Passo 1 - Fortaleça as suas credenciais

A maioria das falhas de segurança da empresa origina-se com uma conta comprometida com um punhado de métodos, tais como spray de senha, repetição de violações ou phishing. Saiba mais sobre estes ataques neste vídeo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Certifique-se de que a sua organização utiliza autenticação forte

Dada a frequência das palavras-passe que estão a ser adivinhadas, phished, roubadas com malware, ou reutilizadas, é fundamental apoiar a palavra-passe com alguma forma de credencial forte – saiba mais sobre a [Autenticação Multi-Factor Azure](../../active-directory/authentication/multi-factor-authentication.md).

Para ativar facilmente o nível básico de segurança de identidade, pode utilizar a ativação de um clique com falhas de [segurança ad a.c. azure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Os incumprimentos de segurança impõem o Azure MFA para todos os utilizadores de um inquilino e bloqueiam os inscrições a partir de protocolos antigos em todo o país.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Comece a proibir senhas comumente atacadas e desligue a complexidade tradicional e as regras de expiração.

Muitas organizações usam a complexidade tradicional (requerendo caracteres especiais, números, maiúsculas e minúsculas) e regras de expiração de senhas. A [pesquisa da Microsoft](https://aka.ms/passwordguidance) mostrou que estas políticas fazem com que os utilizadores escolham palavras-passe mais fáceis de adivinhar.

A funcionalidade de [senha proibida dinâmica](../../active-directory/authentication/concept-password-ban-bad.md) da Azure AD utiliza o comportamento atual do atacante para impedir que os utilizadores definam palavras-passe que podem ser facilmente adivinhadas. Esta capacidade está sempre acesada quando os utilizadores são criados na nuvem, mas agora também está disponível para organizações híbridas quando implementam a proteção de [palavras-passe Azure AD para o Diretório Ativo](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)do Windows Server . A proteção da palavra-passe Azure AD bloqueia os utilizadores de escolher estas palavras-passe comuns e pode ser estendida para bloquear palavras-passe que contenham palavras-chave personalizadas que especifique. Por exemplo, pode impedir que os seus utilizadores escolham senhas que contenham os nomes de produtos da sua empresa ou uma equipa de desporto local.

A Microsoft recomenda a adoção da seguinte política de senha moderna com base na [orientação do NIST:](https://pages.nist.gov/800-63-3/sp800-63b.html)

1. As palavras-passe têm pelo menos 8 caracteres. Mais tempo não é necessariamente melhor, pois fazem com que os utilizadores escolham senhas previsíveis, guardem palavras-passe em ficheiros ou as escrevam.
2. Desative as regras de expiração, que levam os utilizadores a adivinhar facilmente palavras-passe como **a Spring2019!**
3. Desative os requisitos de composição de caracteres e impeça os utilizadores de escolherem senhas comummente atacadas, uma vez que fazem com que os utilizadores escolham substituições previsíveis de caracteres em palavras-passe.

Pode utilizar o PowerShell para evitar que as [palavras-passe expirem](../../active-directory/authentication/concept-sspr-policy.md) para os utilizadores se criar identidades diretamente em AD Azure. As organizações híbridas devem implementar estas políticas utilizando [definições](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) de políticas de grupo de domínio ou [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteja-se contra credenciais vazadas e adicione resiliência contra interrupções

Se a sua organização utilizar uma solução de identidade híbrida com autenticação ou federação pass-through, então deve ativar o sincron de hash de senha pelas seguintes duas razões:

* Os [Utilizadores com credenciais vazadas reportam](../../active-directory/reports-monitoring/concept-risk-events.md) na gestão da AD Azure alerta-o para os pares de nomes de utilizador e passwords, que foram expostos na "dark web". Um incrível volume de senhas é vazado através de phishing, malware e reutilização de palavras-passe em sites de terceiros que são posteriormente violados. A Microsoft encontra muitas destas credenciais vazadas e dir-lhe-á, neste relatório, se corresponderem às credenciais na sua organização – mas apenas se ativar a sincronização de [hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)password !
* Em caso de paragem no local (por exemplo, num ataque de ransomware) pode mudar para a utilização da [autenticação em nuvem utilizando](choose-ad-authn.md)a sincronização de hash password . Este método de autenticação de backup permitirá continuar a aceder a aplicações configuradas para autenticação com o Diretório Ativo Do Azure, incluindo o Office 365. Neste caso, os funcionários de TI não precisarão de recorrer a contas de e-mail pessoais para partilhar dados até que a paralisação no local seja resolvida.

Saiba mais sobre como funciona a [sincronização de hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) de senha.

> [!NOTE]
> Se ativar a sincronização de hash de palavra-passe e estiver a utilizar os serviços de Domínio Azure AD, as hashes Kerberos (AES 256) e opcionalmente os hashes NTLM (RC4, sem sal) também serão encriptados e sincronizados para a AD Azure.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar bloqueio inteligente extranet AD FS

As organizações, que configuram aplicações para autenticar diretamente o Azure AD beneficiam do [bloqueio inteligente da Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Se utilizar AD FS no Windows Server 2012R2, implemente a proteção de [bloqueio extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)AD FS . Se utilizar AD FS no Windows Server 2016, implemente o [bloqueio inteligente extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). O bloqueio da AD FS Smart Extranet protege contra ataques de força bruta, que visam a AD FS, evitando que os utilizadores sejam bloqueados no Ative Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Aproveite intrinsecamente seguros, mais fáceis de usar credenciais

Utilizando o [Windows Hello,](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)pode substituir palavras-passe por autenticação forte de dois fatores em Computadores e dispositivos móveis. Esta autenticação consiste num novo tipo de credencial do utilizador que está ligada de forma segura a um dispositivo e utiliza um biométrico ou PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Passo 2 - Reduza a sua superfície de ataque

Dada a pervasividade do compromisso de senha, minimizar a superfície de ataque na sua organização é fundamental. Eliminar a utilização de protocolos mais antigos e menos seguros, limitar os pontos de entrada de acesso e exercer um controlo mais significativo do acesso administrativo aos recursos pode ajudar a reduzir a área de superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

As aplicações que utilizam os seus próprios métodos legados para autenticar com a AD Azure e aceder aos dados da empresa, representam outro risco para as organizações. Exemplos de aplicações que utilizam a autenticação antiga são os clientes POP3, IMAP4 ou SMTP. As aplicações de autenticação legacy autenticam em nome do utilizador e impedem o Azure AD de fazer avaliações avançadas de segurança. A autenticação alternativa e moderna, reduzirá o risco de segurança, pois suporta a autenticação de vários fatores e o Acesso Condicional. Recomendamos as seguintes três ações:

1. Bloqueie [a autenticação do legado se utilizar AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurar [o SharePoint Online e o Exchange Online para utilizar a autenticação moderna.](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)
3. Se tiver O Azure AD Premium, utilize [as políticas](../../active-directory/conditional-access/overview.md) de Acesso Condicional para bloquear a autenticação do legado, caso contrário utilize falhas de segurança [Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Bloquear pontos de autenticação inválidos

Utilizando a mentalidade de quebra de sutimar, deve reduzir o impacto das credenciais de utilizador comprometidas quando elas acontecerem. Para cada aplicação no seu ambiente, considere os casos de utilização válidos: quais os grupos, quais as redes, quais os dispositivos e outros elementos autorizados – bloqueie depois os restantes. Com o [Acesso Condicional Azure AD,](../../active-directory/conditional-access/overview.md)pode controlar como os utilizadores autorizados acedem às suas apps e recursos com base em condições específicas que define.

### <a name="restrict-user-consent-operations"></a>Restringir as operações de consentimento dos utilizadores

É importante compreender as várias experiências de consentimento de [aplicação da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience), os [tipos de permissões e consentimento,](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)e as suas implicações na postura de segurança da sua organização. Por padrão, todos os utilizadores do Azure AD podem conceder aplicações que aproveitem a plataforma de identidade da Microsoft para aceder aos dados da sua organização. Embora permitir que os utilizadores consintam por si mesmos permite que os utilizadores adquiram facilmente aplicações úteis que se integrem com o Microsoft 365, Azure e outros serviços, pode representar um risco se não for usado e monitorizado cuidadosamente.

A Microsoft recomenda [que desaperte futuras operações](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) de consentimento dos utilizadores para ajudar a reduzir a sua área de superfície e mitigar este risco. Se o consentimento do utilizador final for desativado, as subvenções de consentimento anteriores continuarão a ser honradas, mas todas as futuras operações de consentimento devem ser realizadas por um administrador. O consentimento da Administração pode ser solicitado pelos utilizadores através de um fluxo de trabalho integrado de pedido de [consentimento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) administrativo ou através dos seus próprios processos de suporte. Antes de desativar o consentimento do utilizador final, use as [nossas recomendações](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) para planear esta mudança na sua organização. Para as aplicações que pretende permitir o acesso a todos os utilizadores, considere conceder o consentimento em nome de todos os utilizadores , [certificando-se](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)de que os utilizadores que ainda não tenham consentido individualmente poderão aceder à aplicação. Caso não pretenda que estas aplicações estejam disponíveis para todos os utilizadores em todos os cenários, utilize a atribuição de [aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) e [o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) para restringir o acesso do utilizador às aplicações.

Certifique-se de que os utilizadores podem solicitar a aprovação de novas aplicações para reduzir o atrito do utilizador, minimizar o volume de suporte e impedir que os utilizadores se inscrevam para aplicações utilizando credenciais aD não-Azure. Uma vez que regula as suas operações de consentimento, os administradores devem auditar a app e permissões consentidas regularmente.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar AD Gestão de Identidade Privilegiada

Outro impacto da "quebra de supor" é a necessidade de minimizar a probabilidade de uma conta comprometida poder operar com um papel privilegiado.

[A Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) ajuda-o a minimizar os privilégios de conta ajudando-o:

* Identificar e gerir os utilizadores atribuídos a funções administrativas.
* Compreenda papéis de privilégio não utilizados ou excessivos que deve remover.
* Estabeleça regras para garantir que as funções privilegiadas sejam protegidas por autenticação multifactor.
* Estabelecer regras para garantir que os papéis privilegiados só sejam concedidos tempo suficiente para realizar a tarefa privilegiada.

Enable Azure AD PIM, em seguida, ver os utilizadores a quem são atribuídas funções administrativas e remover contas desnecessárias nessas funções. Para os restantes utilizadores privilegiados, transferi-los de permanentes para elegíveis. Por último, estabelecer políticas adequadas para garantir que, quando precisam de ter acesso a esses papéis privilegiados, podem fazê-lo de forma segura, com o necessário controlo das alterações.

Como parte da implementação do seu processo privilegiado de conta, siga as [melhores práticas para criar pelo menos duas contas de emergência](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) para garantir que ainda tem acesso ao Azure AD se se trancar.

## <a name="step-3---automate-threat-response"></a>Passo 3 - Resposta de ameaça automatizada

O Azure Ative Directory tem muitas capacidades que intercetam automaticamente ataques, para remover a latência entre deteção e resposta. Pode reduzir os custos e riscos, quando reduz o tempo que os criminosos usam para se incorporarem no seu ambiente. Aqui estão os passos concretos que pode tomar.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança do risco do utilizador utilizando a Proteção de Identidade Azure AD

O risco do utilizador indica a probabilidade de a identidade de um utilizador ter sido comprometida e calculada com base nas [deteções](../../active-directory/identity-protection/overview.md) de risco do utilizador que estão associadas à identidade de um utilizador. Uma política de risco do utilizador é uma política de acesso condicional que avalia o nível de risco a um utilizador ou grupo específico. Com base em baixo, médio, alto nível de risco, uma política pode ser configurada para bloquear o acesso ou exigir uma mudança de senha segura usando a autenticação de vários fatores. A recomendação da Microsoft é exigir uma mudança de senha segura para os utilizadores de alto risco.

![Utilizadores marcados em risco](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar política de risco de entrada utilizando a Proteção de Identidade Azure AD

O risco de inscrição é a probabilidade de alguém que não o dono da conta estar a tentar assinar usando a identidade. Uma [política de risco de inscrição](../../active-directory/identity-protection/overview.md) é uma política de acesso condicional que avalia o nível de risco a um utilizador ou grupo específico. Com base no nível de risco (alto/médio/baixo), uma política pode ser configurada para bloquear o acesso ou forçar a autenticação de vários fatores. Certifique-se de que força a autenticação de vários fatores em inscrições de risco média ou superior.

![Inscreva-se em IPs anónimos](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Passo 4 - Utilizar a inteligência em nuvem

A auditoria e o registo de eventos relacionados com a segurança e alertas conexos são componentes essenciais de uma estratégia de proteção eficiente. Registos e relatórios de segurança fornecem-lhe um registo eletrónico de atividades suspeitas e ajudam-no a detetar padrões que podem indicar uma tentativa ou sucesso de penetração externa da rede, e ataques internos. Pode utilizar auditorias para monitorizar a atividade do utilizador, documentar a conformidade regulamentar, fazer análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitor Azure AD

Os serviços e funcionalidades do Microsoft Azure fornecem-lhe opções de auditoria e registo de segurança configuráveis para ajudá-lo a identificar lacunas nas suas políticas e mecanismos de segurança e a resolver essas lacunas para ajudar a prevenir falhas. Pode utilizar a [Extração e Auditoria Azure](log-audit.md) e utilizar relatórios de atividade de [auditoria no portal de Diretório Ativo Azure](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitor Azure AD Connect Health em ambientes híbridos

[Monitorizar a AD FS com a Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) fornece-lhe uma maior visão sobre potenciais problemas e visibilidade dos ataques à sua infraestrutura AD FS. A Azure AD Connect Health fornece alertas com detalhes, etapas de resolução e ligações à documentação relacionada; utilização de análises para várias métricas relacionadas com o tráfego de autenticação; monitorização de desempenho e relatórios.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitor Azure Eventos de Proteção de Identidade aD

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) é uma ferramenta de notificação, monitorização e reporte que pode utilizar para detetar potenciais vulnerabilidades que afetam as identidades da sua organização. Deteta deteções de risco, tais como credenciais vazadas, viagens impossíveis e inscrições de dispositivos infetados, endereços IP anónimos, endereços IP associados à atividade suspeita e locais desconhecidos. Ativar alertas de notificação para receber e-mail de utilizadores em risco e/ou um e-mail semanal de digestão.

A Azure AD Identity Protection fornece dois relatórios importantes que deve monitorizar diariamente:
1. Os relatórios de inscrição de risco irão surgir nas atividades de inscrição do utilizador que deve investigar, o legítimo proprietário pode não ter realizado o registo.
2. Os relatórios de utilizador arriscados surgirão em contas de utilizadores que possam ter sido comprometidas, tais como credenciais vazadas que foram detetadas ou o utilizador que assinou em diferentes locais, causando um evento de viagem impossível.

![Utilizadores marcados em risco](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Aplicações de auditoria e permissões consentidas

Os utilizadores podem ser enganados a navegar para um site ou aplicações comprometidos que terão acesso às informações dos seus perfis e dados do utilizador, como o seu e-mail. Um ator malicioso pode usar as permissões consentidas que recebeu para encriptar o seu conteúdo de caixa de correio e exigir um resgate para recuperar os seus dados da caixa de correio. [Os administradores devem rever e auditar](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) as permissões dadas pelos utilizadores ou desativar a capacidade dos utilizadores de darem o seu consentimento por defeito.

Além de auditar as permissões dadas pelos utilizadores, pode [localizar aplicações OAuth arriscadas ou indesejadas](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) em ambientes premium.

## <a name="step-5---enable-end-user-self-service"></a>Passo 5 - Ativar o self-service do utilizador final

Tanto quanto possível, vai querer equilibrar a segurança com a produtividade. Na mesma linha de aproximação da sua viagem com a mentalidade de que está a estabelecer uma base para a segurança a longo prazo, pode remover o atrito da sua organização, capacitando os seus utilizadores enquanto permanece vigilante.

### <a name="implement-self-service-password-reset"></a>Implementar o reset da palavra-passe self-service

O reset de [palavra-passe self-service](../../active-directory/authentication/quickstart-sspr.md) da Azure AD (SSPR) oferece um meio simples para os administradores de TI permitirem que os utilizadores reporem ou desbloqueiem as suas palavras-passe ou contas sem ajuda ao gabinete de apoio ou à intervenção do administrador. O sistema inclui relatórios detalhados que rastreiam quando os utilizadores redefinirem as suas palavras-passe, juntamente com notificações para alertá-lo para uso indevido ou abuso.

### <a name="implement-self-service-group-and-application-access"></a>Implementar o autosserviço e o acesso à aplicação

A Azure AD oferece a capacidade de os não administradores gerirem o acesso aos recursos, utilizando grupos de segurança, grupos do Office 365, funções de aplicação e catálogos de pacotes de acesso.  [A gestão do grupo self-service](../../active-directory/users-groups-roles/groups-self-service-management.md) permite aos proprietários de grupos gerir os seus próprios grupos, sem necessidade de ser atribuído um papel administrativo. Os utilizadores também podem criar e gerir grupos do Office 365 sem depender dos administradores para lidar em seus pedidos, e os grupos não utilizados expiram automaticamente.  [A gestão do direito da Azure AD](../../active-directory/governance/entitlement-management-overview.md) permite ainda a delegação e visibilidade, com fluxos de trabalho de pedidos de acesso abrangente e expiração automática.  Pode delegar a não administradores a capacidade de configurar os seus próprios pacotes de acesso para grupos, equipas, aplicações e sites SharePoint Online que possuam, com políticas personalizadas para quem é obrigado a aprovar o acesso, incluindo configurar os gestores dos colaboradores e patrocinadores de parceiros de negócios como aprovadores.

### <a name="implement-azure-ad-access-reviews"></a>Implementar avaliações de acesso a Anúncio saqueadas

Com as avaliações de acesso da [Azure AD,](../../active-directory/governance/access-reviews-overview.md)você pode gerir pacotes de acesso e membros de grupo, acesso a aplicações empresariais e atribuições de papéis privilegiados para garantir que você mantém um padrão de segurança.  A supervisão regular dos próprios utilizadores, proprietários de recursos e outros revisores garantem que os utilizadores não mantêm o acesso por longos períodos de tempo quando já não precisam.

## <a name="summary"></a>Resumo

Há muitos aspetos para uma infraestrutura de identidade segura, mas esta lista de verificação de cinco etapas irá ajudá-lo rapidamente a realizar uma infraestrutura de identidade mais segura e segura:

* Fortaleça as suas credenciais.
* Reduza a sua área de superfície de ataque.
* Automatizar a resposta à ameaça.
* Utilize a inteligência na nuvem.
* Ative uma segurança mais previsível e completa do utilizador final com autoajuda.

Agradecemos a seriedade com que leva a Segurança de Identidade e esperamos que este documento seja um roteiro útil para uma postura mais segura para a sua organização.

## <a name="next-steps"></a>Passos seguintes

Se precisar de assistência para planear e implementar as recomendações, consulte os planos de implementação do [projeto Azure AD](https://aka.ms/deploymentplans) para obter ajuda.

Se estiver confiante de que todos estes passos estão completos, utilize a [Pontuação De Identidade Segura](../../active-directory/fundamentals/identity-secure-score.md)da Microsoft , que o manterá atualizado com as [mais recentes boas práticas](identity-management-best-practices.md) e ameaças à segurança.
