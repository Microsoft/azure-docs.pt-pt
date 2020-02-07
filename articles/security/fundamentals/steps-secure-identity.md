---
title: Proteja a sua infraestrutura de identidade Azure AD
titleSuffix: Azure Active Directory
description: Este documento descreve uma lista de ações importantes que os administradores devem implementar para ajudá-los a proteger sua organização usando os recursos do Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: d3afc784688a14e4d5d2fc6ae9dc5c1a9e45c4a0
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057214"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco etapas para proteger sua infraestrutura de identidade

Se você estiver lendo este documento, você está ciente do significado da segurança. Provavelmente você já tem a responsabilidade de proteger sua organização. Se precisar de convencer os outros da importância da segurança, envie-os para ler o mais recente relatório da [Microsoft Security Intelligence](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Este documento ajudará você a obter uma postura mais segura usando os recursos do Azure Active Directory usando uma lista de verificação de cinco etapas para inocularr sua organização contra ataques cibernéticos.

Esta lista de verificação ajudará você a implantar rapidamente as ações recomendadas críticas para proteger sua organização imediatamente explicando como:

* Fortaleça suas credenciais.
* Reduza a área da superfície de ataque.
* Automatize a resposta a ameaças.
* Utilize a inteligência na nuvem.
* Ativar o self-service do utilizador final.

Certifique-se de manter o controle de quais recursos e etapas estão completos durante a leitura desta lista de verificação.

> [!NOTE]
> Muitas das recomendações neste documento se aplicam somente a aplicativos que estão configurados para usar Azure Active Directory como seu provedor de identidade. Configurar aplicativos para logon único garante os benefícios das políticas de credencial, detecção de ameaças, auditoria, registro em log e outros recursos adicionados a esses aplicativos. [A única inscrição através do Azure Ative Directory](../../active-directory/manage-apps/configure-single-sign-on-portal.md) é a base - na qual todas estas recomendações se baseiam.

As recomendações deste documento estão alinhadas com a [Pontuação Segura](../../active-directory/fundamentals/identity-secure-score.md)de Identidade, uma avaliação automatizada da configuração de segurança de identidade do seu inquilino Azure AD. As organizações podem usar a página Identity Secure Score no portal Azure AD para encontrar lacunas na sua configuração de segurança atual para garantir que seguem as [melhores práticas](identity-management-best-practices.md) atuais da Microsoft para a segurança. A implementação de cada recomendação na página de Pontuação segura aumentará sua pontuação e permitirá que você acompanhe seu progresso, além de ajudá-lo a comparar sua implementação com outras organizações de tamanho similar ou seu setor.

![Pontuação segura de identidade](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Muitos dos recursos descritos aqui exigem uma assinatura Azure AD Premium, enquanto outros são gratuitos. Por favor, reveja os preços do nosso [Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/) e a lista de verificação de implantação de [anúncios azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) para obter mais informações.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: proteger contas com privilégios com MFA

Antes de começar esta lista de verificação, certifique-se de não ficar comprometido enquanto estiver lendo esta lista de verificação. Primeiro, você precisa proteger suas contas privilegiadas.

Os invasores que recebem o controle de contas com privilégios podem fazer danos enormes, portanto, é essencial proteger essas contas primeiro. Ative e exija a [autenticação de multi-factores](../../active-directory/authentication/multi-factor-authentication.md) (MFA) para todos os administradores da sua organização utilizando falhas de [segurança adatos azure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) ou [acesso condicional](../../active-directory/conditional-access/plan-conditional-access.md). Se você ainda não implementou o MFA, faça-o agora! É importante.

Tudo definido? Vamos começar na lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Etapa 1-reforçar suas credenciais

A maioria das violações de segurança corporativas se originam com uma conta comprometida com um dos diversos métodos, como a irrigação de senha, a repetição de violação ou o phishing. Saiba mais sobre esses ataques neste vídeo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Verifique se sua organização usa autenticação forte

Dada a frequência das palavras-passe que estão a ser adivinhadas, phished, roubadas com malware, ou reutilizadas, é fundamental apoiar a palavra-passe com alguma forma de credencial forte – saiba mais sobre a [Autenticação Multi-Factor Azure](../../active-directory/authentication/multi-factor-authentication.md).

Para ativar facilmente o nível básico de segurança de identidade, pode utilizar a ativação de um clique com falhas de [segurança ad a.c. azure](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Os padrões de segurança impõem o Azure MFA para todos os usuários em um locatário e bloqueia as entradas de protocolos herdados em todo o locatário.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Comece a proibir senhas comumente atacadas e desative a complexidade tradicional e as regras de expiração.

Muitas organizações usam a complexidade tradicional (exigindo caracteres especiais, números, letras maiúsculas e minúsculas) e regras de expiração de senha. A [pesquisa da Microsoft](https://aka.ms/passwordguidance) mostrou que estas políticas fazem com que os utilizadores escolham palavras-passe mais fáceis de adivinhar.

A funcionalidade de [senha proibida dinâmica](../../active-directory/authentication/concept-password-ban-bad.md) da Azure AD utiliza o comportamento atual do atacante para impedir que os utilizadores definam palavras-passe que podem ser facilmente adivinhadas. Esta capacidade está sempre acesada quando os utilizadores são criados na nuvem, mas agora também está disponível para organizações híbridas quando implementam a proteção de [palavras-passe Azure AD para o Diretório Ativo](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)do Windows Server . A proteção de senha do Azure AD impede que os usuários escolham essas senhas comuns e possam ser estendidos para bloquear a senha que contém palavras-chave personalizadas que você especificar. Por exemplo, você pode impedir que os usuários escolham senhas que contenham os nomes de produtos da sua empresa ou uma equipe de esporte local.

A Microsoft recomenda a adoção da seguinte política de senha moderna com base na [orientação do NIST:](https://pages.nist.gov/800-63-3/sp800-63b.html)

1. Exigir que as senhas tenham pelo menos 8 caracteres. Mais tempo não é necessariamente melhor, pois eles fazem com que os usuários escolham senhas previsíveis, salvem senhas em arquivos ou anotem-as.
2. Desative as regras de expiração, que levam os utilizadores a adivinhar facilmente palavras-passe como **a Spring2019!**
3. Desabilite os requisitos de composição de caracteres e impeça que os usuários escolham senhas comumente atacados, pois eles fazem com que os usuários escolham substituições de caracteres previsíveis em senhas.

Pode utilizar o PowerShell para evitar que as [palavras-passe expirem](../../active-directory/authentication/concept-sspr-policy.md) para os utilizadores se criar identidades diretamente em AD Azure. As organizações híbridas devem implementar estas políticas utilizando [definições](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) de políticas de grupo de domínio ou [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteger contra credenciais vazadas e adicionar resiliência contra interrupções

Se sua organização usa uma solução de identidade híbrida com autenticação de passagem ou Federação, você deve habilitar a sincronização de hash de senha pelos dois motivos a seguir:

* Os [Utilizadores com credenciais vazadas reportam](../../active-directory/reports-monitoring/concept-risk-events.md) na gestão da AD Azure alerta-o para os pares de nomes de utilizador e passwords, que foram expostos na "dark web". Um volume incrível de senhas é vazado por meio de phishing, malware e reutilização de senha em sites de terceiros que são violados posteriormente. A Microsoft encontra muitas destas credenciais vazadas e dir-lhe-á, neste relatório, se corresponderem às credenciais na sua organização – mas apenas se ativar a sincronização de [hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)password !
* Em caso de paragem no local (por exemplo, num ataque de ransomware) pode mudar para a utilização da [autenticação em nuvem utilizando](choose-ad-authn.md)a sincronização de hash password . Este método de autenticação de backup permitirá continuar a aceder a aplicações configuradas para autenticação com o Diretório Ativo Do Azure, incluindo o Office 365. Neste caso, os funcionários de TI não precisarão de recorrer a contas de e-mail pessoais para partilhar dados até que a paralisação no local seja resolvida.

Saiba mais sobre como funciona a [sincronização de hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) de senha.

> [!NOTE]
> Se você habilitar a sincronização de hash de senha e estiver usando os serviços de domínio do Azure AD, os hashes Kerberos (AES 256) e, opcionalmente, NTLM (RC4, nenhum Salt) também serão criptografados e sincronizados com o Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar AD FS bloqueio inteligente de extranet

As organizações, que configuram aplicações para autenticar diretamente o Azure AD beneficiam do [bloqueio inteligente da Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Se utilizar AD FS no Windows Server 2012R2, implemente a proteção de [bloqueio extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)AD FS . Se utilizar AD FS no Windows Server 2016, implemente o [bloqueio inteligente extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS bloqueio de extranet inteligente protege contra ataques de força bruta, que se destinam AD FS ao mesmo tempo em que impede que os usuários sejam bloqueados no Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Aproveite as credenciais intrinsecamente seguras e fáceis de usar

Utilizando o [Windows Hello,](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)pode substituir palavras-passe por autenticação forte de dois fatores em Computadores e dispositivos móveis. Essa autenticação consiste em um novo tipo de credencial de usuário que está vinculado de forma segura a um dispositivo e usa uma biométrica ou PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Etapa 2-reduzir a superfície de ataque

Devido à disseminação do comprometimento de senha, minimizar a superfície de ataque em sua organização é essencial. Eliminar o uso de protocolos mais antigos, menos seguros, limitar pontos de entrada de acesso e exercer um controle mais significativo do acesso administrativo aos recursos pode ajudar a reduzir a área da superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

Aplicativos que usam seus próprios métodos herdados para autenticar com o Azure AD e acessar dados da empresa, apresentam outro risco para as organizações. Exemplos de aplicativos que usam autenticação herdada são POP3, IMAP4 ou clientes SMTP. Os aplicativos de autenticação herdados são autenticados em nome do usuário e impedem que o Azure AD faça avaliações de segurança avançadas. A alternativa, a autenticação moderna, reduzirá o risco de segurança, pois ele dá suporte à autenticação multifator e ao acesso condicional. Recomendamos as três ações a seguir:

1. Bloqueie [a autenticação do legado se utilizar AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurar [o SharePoint Online e o Exchange Online para utilizar a autenticação moderna.](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)
3. Se tiver O Azure AD Premium, utilize [as políticas](../../active-directory/conditional-access/conditions.md) de Acesso Condicional para bloquear a autenticação do legado, caso contrário utilize falhas de segurança [Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Bloquear pontos de entrada de autenticação inválidos

Usando a mentalidade supor a violação, você deve reduzir o impacto das credenciais de usuário comprometidas quando elas acontecem. Para cada aplicativo em seu ambiente, considere os casos de uso válidos: quais grupos, quais redes, quais dispositivos e outros elementos estão autorizados e, em seguida, bloqueie o restante. Com o [Acesso Condicional Azure AD,](../../active-directory/conditional-access/overview.md)pode controlar como os utilizadores autorizados acedem às suas apps e recursos com base em condições específicas que define.

### <a name="restrict-user-consent-operations"></a>Restringir operações de consentimento do usuário

É importante compreender as várias experiências de consentimento de [aplicação da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience), os [tipos de permissões e consentimento,](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)e as suas implicações na postura de segurança da sua organização. Por padrão, todos os usuários no Azure AD podem conceder aplicativos que aproveitam a plataforma de identidade da Microsoft para acessar os dados da sua organização. Embora permitir que os usuários consentissem por si só permita que os usuários adquiram facilmente aplicativos úteis que se integram ao Microsoft 365, ao Azure e a outros serviços, ele pode representar um risco se não for usado e monitorado com cuidado.

A Microsoft recomenda [que desaperte futuras operações](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) de consentimento dos utilizadores para ajudar a reduzir a sua área de superfície e mitigar este risco. Se o consentimento do usuário final for desabilitado, as concessões de consentimento anteriores ainda serão respeitadas, mas todas as operações de consentimento futuras deverão ser executadas por um administrador. O consentimento da Administração pode ser solicitado pelos utilizadores através de um fluxo de trabalho integrado de pedido de [consentimento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) administrativo ou através dos seus próprios processos de suporte. Antes de desativar o consentimento do utilizador final, use as [nossas recomendações](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) para planear esta mudança na sua organização. Para as aplicações que pretende permitir o acesso a todos os utilizadores, considere conceder o consentimento em nome de todos os utilizadores , [certificando-se](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)de que os utilizadores que ainda não tenham consentido individualmente poderão aceder à aplicação. Caso não pretenda que estas aplicações estejam disponíveis para todos os utilizadores em todos os cenários, utilize a atribuição de [aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) e [o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) para restringir o acesso do utilizador às aplicações.

Verifique se os usuários podem solicitar aprovação de administrador para novos aplicativos para reduzir o conflito do usuário, minimizar o volume de suporte e impedir que os usuários se inscrevam em aplicativos que usam credenciais não Azure AD. Depois de você regulamentar suas operações de consentimento, os administradores devem auditar as permissões do aplicativo e consentidas regularmente.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar Azure AD Privileged Identity Management

Outro impacto de "supor a violação" é a necessidade de minimizar a probabilidade de que uma conta comprometida possa operar com uma função privilegiada.

[A Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) ajuda-o a minimizar os privilégios de conta ajudando-o:

* Identificar e gerenciar usuários atribuídos a funções administrativas.
* Entenda as funções de privilégio não utilizadas ou excessivas que devem ser removidas.
* Estabeleça regras para garantir que as funções privilegiadas sejam protegidas pela autenticação multifator.
* Estabeleça regras para garantir que as funções privilegiadas sejam concedidas somente por tempo suficiente para realizar a tarefa privilegiada.

Habilite o Azure AD PIM e exiba os usuários aos quais foram atribuídas funções administrativas e remova as contas desnecessárias nessas funções. Para usuários com privilégios restantes, mova-os de permanente para qualificado. Por fim, estabeleça políticas apropriadas para garantir quando elas precisam obter acesso a essas funções privilegiadas, elas podem fazer isso com segurança, com o controle de alteração necessário.

Como parte da implementação do seu processo privilegiado de conta, siga as [melhores práticas para criar pelo menos duas contas de emergência](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) para garantir que ainda tem acesso ao Azure AD se se trancar.

## <a name="step-3---automate-threat-response"></a>Etapa 3 – automatizar a resposta contra ameaças

Azure Active Directory tem muitos recursos que interceptam ataques automaticamente, a fim de remover a latência entre detecção e resposta. Você pode reduzir os custos e riscos ao reduzir o tempo que os criminosos usam para se incorporarem ao seu ambiente. Aqui estão as etapas concretas que você pode tomar.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança de risco do usuário usando o Azure AD Identity Protection

O risco do utilizador indica a probabilidade de a identidade de um utilizador ter sido comprometida e calculada com base nas [deteções](../../active-directory/identity-protection/overview.md) de risco do utilizador que estão associadas à identidade de um utilizador. Uma política de risco do usuário é uma política de acesso condicional que avalia o nível de risco para um usuário ou grupo específico. Com base no nível baixo, médio e alto de risco, uma política pode ser configurada para bloquear o acesso ou exigir uma alteração de senha segura usando a autenticação multifator. A recomendação da Microsoft é exigir uma alteração de senha segura para os usuários em alto risco.

![Utilizadores marcados em risco](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar a política de risco de entrada usando o Azure AD Identity Protection

O risco de entrada é a probabilidade de alguém que não o proprietário da conta tentar fazer logon usando a identidade. Uma [política de risco de inscrição](../../active-directory/identity-protection/overview.md) é uma política de acesso condicional que avalia o nível de risco a um utilizador ou grupo específico. Com base no nível de risco (alta/média/baixa), uma política pode ser configurada para bloquear o acesso ou forçar a autenticação multifator. Certifique-se de forçar a autenticação multifator em entradas de risco média ou superior.

![Entrar de IPs anônimos](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Passo 4 - Utilizar a inteligência em nuvem

A auditoria e o registro em log de eventos relacionados à segurança e alertas relacionados são componentes essenciais de uma estratégia de proteção eficiente. Os logs e relatórios de segurança fornecem um registro eletrônico de atividades suspeitas e ajudam você a detectar padrões que podem indicar a tentativa ou a penetração externa bem-sucedida da rede e ataques internos. Você pode usar a auditoria para monitorar a atividade do usuário, a conformidade regulatória do documento, a análise forense e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitorar o Azure AD

Microsoft Azure serviços e recursos fornecem opções de auditoria e de log de segurança configuráveis para ajudá-lo a identificar lacunas em suas políticas e mecanismos de segurança e resolver essas lacunas para ajudar a evitar violações. Pode utilizar a [Extração e Auditoria Azure](log-audit.md) e utilizar relatórios de atividade de [auditoria no portal de Diretório Ativo Azure](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorar Azure AD Connect Health em ambientes híbridos

[Monitorizar a AD FS com a Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) fornece-lhe uma maior visão sobre potenciais problemas e visibilidade dos ataques à sua infraestrutura AD FS. Azure AD Connect Health fornece alertas com detalhes, etapas de resolução e links para a documentação relacionada; análise de uso para várias métricas relacionadas ao tráfego de autenticação; monitoramento e relatórios de desempenho.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorar eventos de Azure AD Identity Protection

[A Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) é uma ferramenta de notificação, monitorização e reporte que pode utilizar para detetar potenciais vulnerabilidades que afetam as identidades da sua organização. Ele detecta as detecções de risco, como credenciais vazadas, viagem impossível e entradas de dispositivos infectados, endereços IP anônimos, endereços IP associados à atividade suspeita e locais desconhecidos. Habilite alertas de notificação para receber emails de usuários em risco e/ou um email de resumo semanal.

Azure AD Identity Protection fornece dois relatórios importantes que você deve monitorar diariamente:
1. Os relatórios de entrada arriscados orientarão as atividades de entrada do usuário que você deve investigar, o proprietário legítimo pode não ter executado a entrada.
2. Os relatórios de usuário arriscados orientarão as contas de usuário que podem ter sido comprometidas, como credenciais vazadas que foram detectadas ou que o usuário se conectou de diferentes locais, causando um evento de viagem impossível.

![Utilizadores marcados em risco](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Auditar aplicativos e permissões consentidas

Os usuários podem ser induzidos a navegar para um site comprometido ou aplicativos que obterão acesso às informações de perfil e aos dados do usuário, como seu email. Um ator mal-intencionado pode usar as permissões consentidas recebidas para criptografar o conteúdo da caixa de correio e solicitar um resgate para obter novamente os dados da caixa de correio. [Os administradores devem rever e auditar](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) as permissões dadas pelos utilizadores ou desativar a capacidade dos utilizadores de darem o seu consentimento por defeito.

Além de auditar as permissões dadas pelos utilizadores, pode [localizar aplicações OAuth arriscadas ou indesejadas](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) em ambientes premium.

## <a name="step-5---enable-end-user-self-service"></a>Etapa 5 – habilitar o autoatendimento do usuário final

Tanto quanto possível, você desejará balancear a segurança com a produtividade. Ao longo das mesmas linhas de abordagem da sua jornada com a mentalidade de que você está definindo uma base para segurança a longo prazo, você pode remover o conflito de sua organização, capacitando seus usuários enquanto estiverem vigilantes.

### <a name="implement-self-service-password-reset"></a>Implementar redefinição de senha de autoatendimento

O reset de [palavra-passe self-service](../../active-directory/authentication/quickstart-sspr.md) da Azure AD (SSPR) oferece um meio simples para os administradores de TI permitirem que os utilizadores reporem ou desbloqueiem as suas palavras-passe ou contas sem ajuda ao gabinete de apoio ou à intervenção do administrador. O sistema inclui relatórios detalhados que acompanham quando os usuários redefiniram suas senhas, juntamente com as notificações para alertá-lo sobre o uso indevido ou abuso.

### <a name="implement-self-service-group-and-application-access"></a>Implementar o acesso ao aplicativo e ao grupo de autoatendimento

A Azure AD oferece a capacidade de os não administradores gerirem o acesso aos recursos, utilizando grupos de segurança, grupos do Office 365, funções de aplicação e catálogos de pacotes de acesso.  [A gestão do grupo self-service](../../active-directory/users-groups-roles/groups-self-service-management.md) permite aos proprietários de grupos gerir os seus próprios grupos, sem necessidade de ser atribuído um papel administrativo. Os usuários também podem criar e gerenciar grupos do Office 365 sem depender de administradores para lidar com suas solicitações e os grupos não utilizados expiram automaticamente.  [A gestão do direito da Azure AD](../../active-directory/governance/entitlement-management-overview.md) permite ainda a delegação e visibilidade, com fluxos de trabalho de pedidos de acesso abrangente e expiração automática.  Você pode delegar a não-administradores a capacidade de configurar seus próprios pacotes de acesso para grupos, equipes, aplicativos e sites do SharePoint Online que eles possuem, com políticas personalizadas para quem é necessário para aprovar o acesso, incluindo a configuração de funcionários gerentes e patrocinadores de parceiros de negócios como aprovadores.

### <a name="implement-azure-ad-access-reviews"></a>Implementar revisões de acesso do Azure AD

Com as avaliações de acesso da [Azure AD,](../../active-directory/governance/access-reviews-overview.md)você pode gerir pacotes de acesso e membros de grupo, acesso a aplicações empresariais e atribuições de papéis privilegiados para garantir que você mantém um padrão de segurança.  A supervisão regular dos próprios utilizadores, proprietários de recursos e outros revisores garantem que os utilizadores não mantêm o acesso por longos períodos de tempo quando já não precisam.

## <a name="summary"></a>Resumo

Há muitos aspectos em uma infraestrutura de identidade segura, mas esta lista de verificação de cinco etapas ajudará você a realizar rapidamente uma infra-estrutura de identidade segura e mais segura:

* Fortaleça suas credenciais.
* Reduza a área da superfície de ataque.
* Automatize a resposta a ameaças.
* Utilize a inteligência na nuvem.
* Permita a segurança mais previsível e completa do usuário final com auto-ajuda.

Agradecemos o quão sério você assume a segurança da identidade e esperamos que este documento seja um roteiro útil para uma postura mais segura para sua organização.

## <a name="next-steps"></a>Passos seguintes

Se precisar de assistência para planear e implementar as recomendações, consulte os planos de implementação do [projeto Azure AD](https://aka.ms/deploymentplans) para obter ajuda.

Se estiver confiante de que todos estes passos estão completos, utilize a [Pontuação De Identidade Segura](../../active-directory/fundamentals/identity-secure-score.md)da Microsoft , que o manterá atualizado com as [mais recentes boas práticas](identity-management-best-practices.md) e ameaças à segurança.
