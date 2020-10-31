---
title: Proteja a sua infraestrutura de identidade Azure AD
titleSuffix: Azure Active Directory
description: Este documento descreve uma lista de importantes ações que os administradores devem implementar para ajudá-los a proteger a sua organização usando as capacidades AD do Azure
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: 43974258008e3813a368ab31891251db15060aff
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127064"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco passos para garantir a sua infraestrutura de identidade

Se está a ler este documento, está ciente da importância da segurança. Provavelmente já tem a responsabilidade de assegurar a sua organização. Se precisar de convencer os outros da importância da segurança, envie-os para ler o mais recente [relatório da Microsoft Security Intelligence](https://www.microsoft.com/security/business/security-intelligence-report).

Este documento irá ajudá-lo a obter uma postura mais segura usando as capacidades do Azure Ative Directory usando uma lista de verificação de cinco etapas para inocular a sua organização contra ciberataques.

Esta lista de verificação irá ajudá-lo a implementar rapidamente ações recomendadas críticas para proteger a sua organização imediatamente, explicando como:

* Fortaleça as suas credenciais.
* Reduza a área da superfície do ataque.
* Automatizar a resposta da ameaça.
* Utilize a inteligência das nuvens.
* Ativar o autosserviço do utilizador final.

Certifique-se de que mantém as características e passos completos durante a leitura desta lista de verificação.

> [!NOTE]
> Muitas das recomendações deste documento aplicam-se apenas a aplicações configuradas para utilizar o Azure Ative Directory como seu fornecedor de identidade. Configurar aplicações para single Sign-On assegura os benefícios de políticas credenciais, deteção de ameaças, auditoria, registo de madeira e outras funcionalidades adicionam a essas aplicações. [A Azure AD Application Management](../../active-directory/manage-apps/what-is-application-management.md) é a base - na qual todas estas recomendações se baseiam.

As recomendações neste documento estão alinhadas com a [Pontuação De Identidade Segura,](../../active-directory/fundamentals/identity-secure-score.md)uma avaliação automatizada da configuração de segurança de identidade do seu inquilino Azure. As organizações podem usar a página Identity Secure Score no portal AD Azure para encontrar lacunas na sua configuração de segurança atual para garantir que seguem as [melhores práticas](identity-management-best-practices.md) atuais da Microsoft para a segurança. A implementação de cada recomendação na página Secure Score aumentará a sua pontuação e permitir-lhe-á acompanhar o seu progresso, além de ajudá-lo a comparar a sua implementação com outras organizações de tamanho semelhante ou a sua indústria.

![Pontuação segura de identidade](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Muitas das funcionalidades descritas aqui requerem uma subscrição Azure AD Premium, enquanto algumas são gratuitas. Por favor, reveja [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/) e [a lista de verificação de implementação Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) para obter mais informações.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: Proteja as contas privilegiadas com o MFA

Antes de iniciar esta lista de verificação, certifique-se de que não fica comprometido enquanto lê esta lista de verificação. Primeiro precisa proteger as suas contas privilegiadas.

Os atacantes que têm o controlo de contas privilegiadas podem causar danos tremendos, por isso é fundamental proteger estas contas primeiro. Ative e exija [autenticação multi-factor](../../active-directory/authentication/multi-factor-authentication.md) (MFA) para todos os administradores da sua organização utilizando [Predefinições de Segurança AD Azure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) ou [Acesso Condicional](../../active-directory/conditional-access/plan-conditional-access.md). Se ainda não implementou o MFA, faça-o agora! É tão importante.

Tudo preparado? Vamos começar a lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Passo 1 - Fortaleça as suas credenciais

A maioria das falhas de segurança da empresa origina-se com uma conta comprometida com um de um punhado de métodos como spray de palavra-passe, repetição de violação ou phishing. Saiba mais sobre estes ataques neste vídeo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Certifique-se de que a sua organização utiliza a autenticação forte

Dada a frequência das palavras-passe a serem adivinhadas, phished, roubadas com malware, ou reutilizadas, é fundamental apoiar a palavra-passe com alguma forma de credencial forte – saiba mais sobre [a Autenticação Multi-Factor Azure](../../active-directory/authentication/multi-factor-authentication.md).

Para ativar facilmente o nível básico de segurança de identidade, pode utilizar o enabler de um clique com [Predefinições de Segurança AD Azure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Os incumprimentos de segurança impõem a Azure MFA para todos os utilizadores de um inquilino e bloqueiam as inscrições dos protocolos antigos em todo o inquilino.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Comece a proibir senhas comumente atacadas e desligue a complexidade tradicional e as regras de validade.

Muitas organizações usam a complexidade tradicional (que requer caracteres especiais, números, maiúsculas e minúsculas) e regras de validade da palavra-passe. [A pesquisa da Microsoft](https://aka.ms/passwordguidance) mostrou que estas políticas fazem com que os utilizadores escolham senhas que são mais fáceis de adivinhar.

A funcionalidade [de senha proibida dinâmica](../../active-directory/authentication/concept-password-ban-bad.md) do AD AD utiliza o comportamento atual do atacante para impedir que os utilizadores estabeleçam senhas que podem ser facilmente adivinhadas. Esta capacidade está sempre ativa quando os utilizadores são criados na nuvem, mas agora também está disponível para organizações híbridas quando implementam [a proteção de senha AD AD para](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)o Windows Server Ative Directory . A proteção da palavra-passe AD AZure impede os utilizadores de escolherem estas palavras-passe comuns e podem ser estendidas para bloquear a palavra-passe que contém palavras-chave personalizadas que especifique. Por exemplo, pode impedir que os seus utilizadores escolham senhas que contenham os nomes de produto da sua empresa ou uma equipa desportiva local.

A Microsoft recomenda a adoção da seguinte política de palavra-passe moderna baseada na [orientação do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Requer que as palavras-passe tenham pelo menos 8 caracteres. Mais tempo não é necessariamente melhor, pois fazem com que os utilizadores escolham senhas previsíveis, guardem palavras-passe em ficheiros ou escrevam-nas.
2. Desativar as regras de expiração, que levam os utilizadores a senhas facilmente adivinhadas como **a primavera de 2019!**
3. Desativar os requisitos de composição de caracteres e impedir que os utilizadores escolham senhas comumente atacadas, uma vez que fazem com que os utilizadores escolham substituições previsíveis de caracteres em palavras-passe.

Pode utilizar [o PowerShell para evitar que as palavras-passe expirem](../../active-directory/authentication/concept-sspr-policy.md) para os utilizadores se criar identidades no AZure AD diretamente. As organizações híbridas devem implementar estas políticas utilizando [definições de política de grupo de domínio](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou Windows [PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteja contra as credenciais vazadas e adicione resiliência contra falhas

Se a sua organização utilizar uma solução de identidade híbrida com autenticação ou federação pass-through, então deve ativar a sincronização de haxixe de palavra-passe pelas seguintes razões:

* Os [Utilizadores com credenciais vazadas reportam](../../active-directory/reports-monitoring/concept-risk-events.md) na gestão AZure AD alerta para o nome de utilizador e os pares de palavras-passe, que foram expostos na "dark web". Um volume incrível de palavras-passe é vazado através de phishing, malware e reutilização de palavras-passe em sites de terceiros que são posteriormente violados. A Microsoft encontra muitas destas credenciais vazadas e dir-lhe-á, neste relatório, se correspondem às credenciais da sua organização – mas apenas se [ativar a sincronização de haxixe de palavra-passe](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)!
* Em caso de paragem no local (por exemplo, num ataque de ransomware) pode passar a utilizar a autenticação na [nuvem utilizando a sincronização de haxixe de palavra-passe](choose-ad-authn.md). Este método de autenticação de backup permitirá continuar a aceder a aplicações configuradas para autenticação com o Azure Ative Directory, incluindo o Microsoft 365. Neste caso, o pessoal de TI não precisará recorrer a contas de email pessoais para partilhar dados até que a paralisação no local seja resolvida.

Saiba mais sobre como funciona a [sincronização de haxixe de palavra-passe.](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)

> [!NOTE]
> Se ativar a sincronização de haxixe de palavra-passe e estiver a utilizar os serviços de domínio Azure AD, os hashes Kerberos (AES 256) e os hashes opcionalmente NTLM (RC4, sem sal) também serão encriptados e sincronizados para a Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar o bloqueio inteligente da extranet AD FS

As organizações, que configuram aplicações para autenticar diretamente para a Azure AD beneficiam do [bloqueio inteligente AZURE AD](../../active-directory/authentication/concept-sspr-howitworks.md). Se utilizar O FS AD no Windows Server 2012R2, implemente [a proteção de bloqueio de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)AD FS . Se utilizar O FS AD no Windows Server 2016, implemente [o bloqueio inteligente da extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). O bloqueio da Extranet Inteligente AD FS protege contra ataques de força bruta, que visam a AD FS, evitando que os utilizadores sejam bloqueados no Ative Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Aproveite intrinsecamente seguros, mais fáceis de usar credenciais

Utilizando [o Windows Hello,](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)pode substituir palavras-passe por uma autenticação forte de dois fatores em Computadores e dispositivos móveis. Esta autenticação consiste num novo tipo de credencial do utilizador que está ligada de forma segura a um dispositivo e utiliza um biométrico ou PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Passo 2 - Reduza a sua superfície de ataque

Dada a pervasividade do compromisso de senha, minimizar a superfície de ataque na sua organização é fundamental. Eliminar o uso de protocolos mais antigos e menos seguros, limitar os pontos de entrada de acesso e exercer um controlo mais significativo do acesso administrativo aos recursos pode ajudar a reduzir a área da superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

As aplicações que usam os seus próprios métodos de legado para autenticar com a Azure AD e aceder aos dados da empresa, representam outro risco para as organizações. Exemplos de aplicações que usam a autenticação antiga são os clientes POP3, IMAP4 ou SMTP. As aplicações de autenticação legacy autenticam-se em nome do utilizador e impedem o Azure AD de fazer avaliações avançadas de segurança. A autenticação moderna e alternativa reduzirá o seu risco de segurança, pois suporta a autenticação de vários fatores e o Acesso Condicional. Recomendamos as seguintes três ações:

1. Bloqueie [a autenticação do legado se utilizar AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurar [SharePoint Online e Exchange Online para utilizar a autenticação moderna.](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)
3. Se tiver Azure AD Premium, utilize políticas de acesso condicional para bloquear a [autenticação do legado,](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md)caso contrário utilize [Predefinições de Segurança AD AZure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Bloquear pontos de entrada de autenticação por invalidez

Utilizando a mentalidade de violação de assumir, deve reduzir o impacto das credenciais de utilizador comprometidas quando elas acontecem. Para cada aplicação no seu ambiente considere os casos de utilização válidos: quais grupos, quais as redes, quais os dispositivos e outros elementos autorizados – e depois bloqueie o resto. Com [o Azure AD Conditional Access,](../../active-directory/conditional-access/overview.md)pode controlar como os utilizadores autorizados acedem às suas apps e recursos com base em condições específicas que define.

### <a name="restrict-user-consent-operations"></a>Restringir as operações de consentimento do utilizador

É importante compreender as várias experiências de consentimento da [aplicação Azure AD](../../active-directory/develop/application-consent-experience.md), os [tipos de permissões e consentimento](../../active-directory/develop/v2-permissions-and-consent.md), e as suas implicações na postura de segurança da sua organização. Por padrão, todos os utilizadores em Azure AD podem conceder aplicações que aproveitam a plataforma de identidade da Microsoft para aceder aos dados da sua organização. Apesar de permitir que os utilizadores consintam por si mesmos, permite que os utilizadores adquiram facilmente aplicações úteis que se integrem com o Microsoft 365, Azure e outros serviços, pode representar um risco se não for utilizado e monitorizado cuidadosamente.

A Microsoft recomenda restringir o consentimento do utilizador para ajudar a reduzir a sua área de superfície e mitigar este risco. Também pode utilizar políticas de [consentimento de aplicações (pré-visualização)](../../active-directory/manage-apps/configure-user-consent.md) para restringir o consentimento do utilizador final a apenas editores verificados e apenas para permissões que selecione. Se o consentimento do utilizador final for restrito, as subvenções de consentimento anteriores continuarão a ser honradas, mas todas as operações de consentimento futuras devem ser realizadas por um administrador. Para casos restritos, o consentimento administrativo pode ser solicitado pelos utilizadores através de um pedido de consentimento de [administração](../../active-directory/manage-apps/configure-admin-consent-workflow.md) integrado ou através dos seus próprios processos de suporte. Antes de restringir o consentimento do utilizador final, use [as nossas recomendações](../../active-directory/manage-apps/manage-consent-requests.md) para planear esta mudança na sua organização. Para aplicações que pretende permitir o acesso de todos os utilizadores, considere [conceder o consentimento em nome de todos os utilizadores , certificando-se](../../active-directory/develop/v2-admin-consent.md)de que os utilizadores que ainda não tenham consentido individualmente poderão aceder à aplicação. Se não pretender que estas aplicações estejam disponíveis para todos os utilizadores em todos os cenários, utilize [a atribuição de aplicações](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) e o Acesso Condicional para restringir o acesso do utilizador a [aplicações específicas.](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md)

Certifique-se de que os utilizadores podem solicitar aprovação de administração para novas aplicações para reduzir o atrito do utilizador, minimizar o volume de suporte e impedir que os utilizadores se inscrevam para aplicações usando credenciais AD não-Azure. Uma vez que regula as suas operações de consentimento, os administradores devem auditar a app e permissões consentidas regularmente.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar Azure AD Gestão de Identidade Privilegiada

Outro impacto da "violação de assumir" é a necessidade de minimizar a probabilidade de uma conta comprometida poder operar com um papel privilegiado.

[A Azure AD Gestão de Identidade Privilegiada (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) ajuda-o a minimizar os privilégios de conta ajudando-o:

* Identificar e gerir os utilizadores atribuídos a funções administrativas.
* Compreenda os papéis de privilégio não reutilizados ou excessivos que deve remover.
* Estabeleça regras para garantir que as funções privilegiadas sejam protegidas por autenticação multi-factor.
* Estabeleça regras para garantir que os papéis privilegiados sejam concedidos apenas o tempo suficiente para realizar a tarefa privilegiada.

Ativar O Azure AD PIM, depois ver os utilizadores que lhes são atribuídas funções administrativas e remover contas desnecessárias nessas funções. Para os restantes utilizadores privilegiados, movê-los de permanente para elegível. Por último, estabelecer políticas adequadas para garantir que, quando precisam de ter acesso a essas funções privilegiadas, podem fazê-lo de forma segura, com o necessário controlo de mudança.

Como parte da implementação do seu processo de conta privilegiada, siga as [melhores práticas para criar pelo menos duas contas de emergência](../../active-directory/roles/security-planning.md) para se certificar de que ainda tem acesso à Azure AD se se trancar fora.

## <a name="step-3---automate-threat-response"></a>Passo 3 - Automatizar a resposta à ameaça

O Azure Ative Directory tem muitas capacidades que intercetam automaticamente ataques, para remover a latência entre a deteção e a resposta. Pode reduzir os custos e riscos, quando reduz o tempo que os criminosos usam para se incorporarem no seu ambiente. Aqui estão os passos concretos que pode dar.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança do risco do utilizador usando a Azure AD Identity Protection

O risco do utilizador indica a probabilidade de a identidade de um utilizador ter sido comprometida e é calculada com base nas deteções de risco do utilizador que estão [associadas](../../active-directory/identity-protection/overview.md) à identidade de um utilizador. Uma política de risco do utilizador é uma política de acesso condicional que avalia o nível de risco para um utilizador ou grupo específico. Com base em Baixo, Médio, Alto nível de risco, uma política pode ser configurada para bloquear o acesso ou exigir uma mudança segura de palavra-passe usando a autenticação de vários fatores. A recomendação da Microsoft é exigir uma alteração segura da palavra-passe para os utilizadores de alto risco.

![A screenshot mostra os utilizadores sinalizados para o risco, com um utilizador selecionado.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar a política de risco de inscrição utilizando a proteção de identidade Azure AD

O risco de inscrição é a probabilidade de alguém além do proprietário da conta estar a tentar assinar usando a identidade. Uma [política de risco de acesso](../../active-directory/identity-protection/overview.md) a um sinal é uma política de acesso condicional que avalia o nível de risco para um utilizador ou grupo específico. Com base no nível de risco (alto/médio/baixo), uma política pode ser configurada para bloquear o acesso ou forçar a autenticação de vários fatores. Certifique-se de que força a autenticação de vários fatores em entradas de risco médias ou superiores.

![Inscreva-se em IPs anónimos](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Passo 4 - Utilizar a inteligência em nuvem

A auditoria e registo de eventos relacionados com a segurança e alertas conexos são componentes essenciais de uma estratégia de proteção eficiente. Os registos de segurança e os relatórios fornecem-lhe um registo eletrónico de atividades suspeitas e ajudam-no a detetar padrões que podem indicar uma tentativa ou sucesso da penetração externa da rede e ataques internos. Pode utilizar a auditoria para monitorizar a atividade do utilizador, documentar a conformidade regulatória, fazer análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitor Azure AD

Os serviços e funcionalidades do Microsoft Azure fornecem-lhe opções de auditoria e registo de segurança configuráveis para o ajudar a identificar lacunas nas suas políticas e mecanismos de segurança e a resolver essas lacunas para ajudar a prevenir falhas. Pode utilizar [o Azure Logging and Auditing](log-audit.md) e utilizar relatórios de [atividades de Auditoria no portal Azure Ative Directory](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitor Azure AD Connect Health em ambientes híbridos

[A monitorização do AD FS com Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) proporciona-lhe uma maior visão sobre potenciais problemas e visibilidade dos ataques à sua infraestrutura AD FS. A Azure AD Connect Health fornece alertas com detalhes, etapas de resolução e ligações à documentação relacionada; análise de utilização de várias métricas relacionadas com o tráfego de autenticação; monitorização de desempenho e relatórios.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitor Azure AD Identity Protection events

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) é uma ferramenta de notificação, monitorização e reporte que pode utilizar para detetar potenciais vulnerabilidades que afetam as identidades da sua organização. Deteta deteções de riscos, tais como credenciais vazadas, viagens impossíveis e insusitadas de dispositivos infetados, endereços IP anónimos, endereços IP associados à atividade suspeita e locais desconhecidos. Ativar alertas de notificação para receber e-mails de utilizadores em risco e/ou um e-mail semanal de digestão.

A Azure AD Identity Protection fornece dois relatórios importantes que deve monitorizar diariamente:
1. Relatórios de inscrição arriscados irão emergir atividades de inscrição do utilizador que você deve investigar, o proprietário legítimo pode não ter realizado a inscrição.
2. Relatórios de utilizador arriscados irão surgir contas de utilizadores que possam ter sido comprometidas, como credenciais vazadas que foram detetadas ou o utilizador assinado em diferentes locais causando um evento de viagem impossível.

![A screenshot mostra o painel de proteção de identidade Azure A D com os utilizadores e os seus níveis de risco.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Aplicações de auditoria e permissões consentidas

Os utilizadores podem ser enganados a navegar para um web site comprometido ou aplicações que terão acesso às informações do seu perfil e dados dos utilizadores, como o seu e-mail. Um ator malicioso pode usar as permissões consentidas que recebeu para encriptar o conteúdo da sua caixa de correio e exigir um resgate para recuperar os dados da sua caixa de correio. [Os administradores devem rever e auditar](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) as permissões dadas pelos utilizadores ou desativar a capacidade dos utilizadores de darem o seu consentimento por defeito.

Além de auditar as permissões dadas pelos utilizadores, pode [localizar aplicações OAuth arriscadas ou indesejadas](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) em ambientes premium.

## <a name="step-5---enable-end-user-self-service"></a>Passo 5 - Ativar o autosserviço do utilizador final

Tanto quanto possível, vai querer equilibrar a segurança com a produtividade. Na mesma linha de abordar a sua viagem com a mentalidade de que está a estabelecer uma base para a segurança a longo prazo, pode remover o atrito da sua organização, capacitando os seus utilizadores enquanto permanece vigilante.

### <a name="implement-self-service-password-reset"></a>Implementar reset de senha de autosserviço

A redefinição da palavra-passe de autosserviço da Azure AD [(SSPR)](../../active-directory/authentication/quickstart-sspr.md) oferece um meio simples para os administradores de TI permitirem que os utilizadores reiniciem ou desbloqueiem as suas palavras-passe ou contas sem ajuda no balcão ou intervenção do administrador. O sistema inclui relatórios detalhados que rastreia quando os utilizadores redefiniram as suas palavras-passe, juntamente com notificações para alertá-lo para uso indevido ou abuso.

### <a name="implement-self-service-group-and-application-access"></a>Implementar o grupo de self-service e o acesso à aplicação

O Azure AD fornece a capacidade de não administradores gerirem o acesso aos recursos, utilizando grupos de segurança, grupos microsoft 365, funções de aplicação e catálogos de pacotes de acesso.  [A gestão do grupo de self-service](../../active-directory/enterprise-users/groups-self-service-management.md) permite aos proprietários do grupo gerir os seus próprios grupos, sem necessidade de serem atribuídos um papel administrativo. Os utilizadores também podem criar e gerir grupos microsoft 365 sem depender de administradores para lidar com os seus pedidos, e os grupos não reutilizados expiram automaticamente.  [A gestão de direitos Azure AD](../../active-directory/governance/entitlement-management-overview.md) permite ainda delegação e visibilidade, com fluxos de trabalho de pedido de acesso abrangente e expiração automática.  Pode delegar a não administradores a capacidade de configurar os seus próprios pacotes de acesso para grupos, equipas, aplicações e sites SharePoint Online que possuam, com políticas personalizadas para quem é obrigado a aprovar o acesso, incluindo configurar os gestores dos colaboradores e patrocinadores de parceiros de negócio como aprovadores.

### <a name="implement-azure-ad-access-reviews"></a>Implementar comentários de acesso a Azure AD

Com [avaliações de acesso Azure AD,](../../active-directory/governance/access-reviews-overview.md)você pode gerir pacotes de acesso e membros do grupo, acesso a aplicações empresariais e atribuições de funções privilegiadas para garantir que você mantém um padrão de segurança.  A supervisão regular dos próprios utilizadores, proprietários de recursos e outros revisores garantem que os utilizadores não retêm o acesso por longos períodos de tempo quando já não precisam dele.

## <a name="summary"></a>Resumo

Existem muitos aspetos para uma infraestrutura de identidade segura, mas esta lista de verificação de cinco etapas irá ajudá-lo rapidamente a realizar uma infraestrutura de identidade mais segura e segura:

* Fortaleça as suas credenciais.
* Reduza a área da superfície do ataque.
* Automatizar a resposta da ameaça.
* Utilize a inteligência das nuvens.
* Ativar uma segurança final mais previsível e completa com autoajuda.

Agradecemos a seriedade com que leva a Segurança identitária e esperamos que este documento seja um roteiro útil para uma postura mais segura para a sua organização.

## <a name="next-steps"></a>Passos seguintes

Se precisar de assistência para planear e implementar as recomendações, consulte os planos de implementação do [projeto Azure AD](https://aka.ms/deploymentplans) para obter ajuda.

Se está confiante de que todos estes passos estão completos, use o [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md)da Microsoft , que o manterá atualizado com as mais recentes boas [práticas](identity-management-best-practices.md) e ameaças à segurança.
