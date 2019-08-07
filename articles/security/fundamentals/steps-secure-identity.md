---
title: Cinco etapas para proteger sua infraestrutura de identidade no Azure Active Directory
description: Este documento descreve uma lista de ações importantes que os administradores devem implementar para ajudá-los a proteger sua organização usando os recursos do Azure AD
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martinco
ms.openlocfilehash: c5cdd12c3075d48ff32c40d686b32a650ec43d8e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779784"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco etapas para proteger sua infraestrutura de identidade

Se você estiver lendo este documento, você está ciente do significado da segurança. Provavelmente você já tem a responsabilidade de proteger sua organização. Se você precisar convencer outras pessoas da importância da segurança, envie-as para ler o relatório mais recente de [inteligência de segurança da Microsoft](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Este documento ajudará você a obter uma postura mais segura usando os recursos do Azure Active Directory usando uma lista de verificação de cinco etapas para inocularr sua organização contra ataques cibernéticos.

Esta lista de verificação ajudará você a implantar rapidamente as ações recomendadas críticas para proteger sua organização imediatamente explicando como:

* Fortaleça suas credenciais.
* Reduza a área da superfície de ataque.
* Automatize a resposta a ameaças.
* Aumente a conscientização da auditoria e do monitoramento.
* Permita a segurança mais previsível e completa do usuário final com auto-ajuda.

> [!NOTE]
> Muitas das recomendações neste documento se aplicam somente a aplicativos que estão configurados para usar Azure Active Directory como seu provedor de identidade. Configurar aplicativos para logon único garante os benefícios das políticas de credencial, detecção de ameaças, auditoria, registro em log e outros recursos adicionados a esses aplicativos. O [logon único por meio de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) é a base em que todas essas recomendações se baseiam.

As recomendações neste documento estão alinhadas com a [Pontuação](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)de segurança de identidade, uma avaliação automatizada da configuração de segurança de identidade do locatário do Azure AD. As organizações podem usar a página de Pontuação de segurança de identidade no portal do Azure AD para encontrar lacunas em sua configuração de segurança atual para garantir que sigam as práticas recomendadas atuais da Microsoft para segurança. A implementação de cada recomendação na página de Pontuação segura aumentará sua pontuação e permitirá que você acompanhe seu progresso, além de ajudá-lo a comparar sua implementação com outras organizações de tamanho similar ou seu setor.

![Classificação de Segurança de Identidade](./media/steps-secure-identity/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: Proteger contas privilegiadas com MFA

Antes de começar esta lista de verificação, certifique-se de não ficar comprometido enquanto estiver lendo esta lista de verificação. Primeiro, você precisa proteger suas contas privilegiadas.

Os invasores que recebem o controle de contas com privilégios podem fazer danos enormes, portanto, é essencial proteger essas contas primeiro. Habilite e exija a MFA (autenticação multifator) do [Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) para todos os administradores em sua organização usando a [proteção de linha de base](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Se você ainda não implementou o MFA, faça-o agora! É importante.

Tudo definido? Vamos começar na lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Etapa 1-reforçar suas credenciais 

A maioria das violações de segurança corporativas se originam com uma conta comprometida com um dos diversos métodos, como a irrigação de senha, a repetição de violação ou o phishing. Saiba mais sobre esses ataques neste vídeo (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Verifique se sua organização usa autenticação forte

Dada a frequência de adivinhação de senhas, phishinged, roubadas com malware ou reutilizado, é essencial voltar à senha com alguma forma de credencial forte – saiba mais sobre a [autenticação multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Comece a proibir senhas comumente atacadas e desative a complexidade tradicional e as regras de expiração.

Muitas organizações usam a complexidade tradicional (exigindo caracteres especiais, números, letras maiúsculas e minúsculas) e regras de expiração de senha. A [pesquisa da Microsoft](https://aka.ms/passwordguidance) mostrou que essas políticas fazem com que os usuários escolham senhas que são mais fáceis de adivinhar.

O recurso de [senha banida dinâmica](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) do Azure ad usa o comportamento do invasor atual para impedir que os usuários definam senhas que podem ser facilmente adivinhadas. Esse recurso está sempre ativado quando os usuários são criados na nuvem, mas agora também estão disponíveis para organizações híbridas quando implantam [a proteção de senha do Azure ad para o Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). A proteção de senha do Azure AD impede que os usuários escolham essas senhas comuns e possam ser estendidos para bloquear a senha que contém palavras-chave personalizadas que você especificar. Por exemplo, você pode impedir que os usuários escolham senhas que contenham os nomes de produtos da sua empresa ou uma equipe de esporte local.

A Microsoft recomenda adotar a seguinte política de senha moderna com base nas [diretrizes do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Exigir que as senhas tenham pelo menos 8 caracteres. Mais tempo não é necessariamente melhor, pois eles fazem com que os usuários escolham senhas previsíveis, salvem senhas em arquivos ou anotem-as.
2. Desabilite as regras de expiração, que orientam os usuários a adivinhar senhas facilmente, como **Spring2019!**
3. Desabilite os requisitos de composição de caracteres e impeça que os usuários escolham senhas comumente atacados, pois eles fazem com que os usuários escolham substituições de caracteres previsíveis em senhas.

Você pode usar o [PowerShell para impedir que as senhas expirem](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) para os usuários se você criar identidades no Azure ad diretamente. As organizações híbridas devem implementar essas políticas usando [as configurações de diretiva de grupo de domínio](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou o [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteger contra credenciais vazadas e adicionar resiliência contra interrupções

Se sua organização usa uma solução de identidade híbrida com autenticação de passagem ou Federação, você deve habilitar a sincronização de hash de senha pelos dois motivos a seguir:

* O relatório [usuários com credenciais vazadas](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) no gerenciamento do AD do Azure avisa você sobre os pares de nome de usuário e senha, que foram expostos na "Web escura". Um volume incrível de senhas é vazado por meio de phishing, malware e reutilização de senha em sites de terceiros que são violados posteriormente. A Microsoft encontra muitas dessas credenciais vazadas e lhe dirá, neste relatório, se elas corresponderem às credenciais em sua organização – mas somente se você [habilitar a sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* No caso de uma interrupção local (por exemplo, em um ataque de ransomware), você poderá alternar para usar a [autenticação de nuvem usando a sincronização de hash de senha](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). Esse método de autenticação de backup permitirá que você continue acessando aplicativos configurados para autenticação com Azure Active Directory, incluindo o Office 365. Nesse caso, a equipe de ti não precisará recorrer a contas de email pessoais para compartilhar dados até que a interrupção local seja resolvida.

Saiba mais sobre como funciona a [sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) .

> [!NOTE]
> Se você habilitar a sincronização de hash de senha e estiver usando os serviços de domínio do Azure AD, os hashes Kerberos (AES 256) e, opcionalmente, NTLM (RC4, nenhum Salt) também serão criptografados e sincronizados com o Azure AD. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar AD FS bloqueio inteligente de extranet

Organizações, que configuram aplicativos para autenticar diretamente no Azure AD se beneficiam do [bloqueio inteligente do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Se você usar AD FS no Windows Server 2012R2, implemente AD FS [proteção de bloqueio de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se você usar AD FS no Windows Server 2016, implemente o [bloqueio inteligente da extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS bloqueio de extranet inteligente protege contra ataques de força bruta, que se destinam AD FS ao mesmo tempo em que impede que os usuários sejam bloqueados no Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Aproveite as credenciais intrinsecamente seguras e fáceis de usar

Usando o [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), você pode substituir senhas com autenticação forte de dois fatores em PCs e dispositivos móveis. Essa autenticação consiste em um novo tipo de credencial de usuário que está vinculado de forma segura a um dispositivo e usa uma biométrica ou PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Etapa 2-reduzir a superfície de ataque

Devido à disseminação do comprometimento de senha, minimizar a superfície de ataque em sua organização é essencial. Eliminar o uso de protocolos mais antigos, menos seguros, limitar pontos de entrada de acesso e exercer um controle mais significativo do acesso administrativo aos recursos pode ajudar a reduzir a área da superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

Aplicativos que usam seus próprios métodos herdados para autenticar com o Azure AD e acessar dados da empresa, apresentam outro risco para as organizações. Exemplos de aplicativos que usam autenticação herdada são POP3, IMAP4 ou clientes SMTP. Os aplicativos de autenticação herdados são autenticados em nome do usuário e impedem que o Azure AD faça avaliações de segurança avançadas. A alternativa, a autenticação moderna, reduzirá o risco de segurança, pois ele dá suporte à autenticação multifator e ao acesso condicional. Recomendamos as três ações a seguir:

1. Bloqueie a [autenticação herdada se você usar AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configure o [SharePoint Online e o Exchange Online para usar a autenticação moderna](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Use [políticas de acesso condicional para bloquear a autenticação herdada](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions).

### <a name="block-invalid-authentication-entry-points"></a>Bloquear pontos de entrada de autenticação inválidos

Usando a mentalidade supor a violação, você deve reduzir o impacto das credenciais de usuário comprometidas quando elas acontecem. Para cada aplicativo em seu ambiente, considere os casos de uso válidos: quais grupos, quais redes, quais dispositivos e outros elementos estão autorizados e, em seguida, bloqueie o restante. Com o [acesso condicional do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), você pode controlar como os usuários autorizados acessam seus aplicativos e recursos com base em condições específicas que você define.

### <a name="block-end-user-consent"></a>Bloquear consentimento do usuário final

Por padrão, todos os usuários no Azure AD têm permissão para conceder aplicativos que aproveitam o OAuth 2,0 e as permissões do Microsoft Identity consentince [Framework](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) para acessar os dados da empresa. Embora o consentimento permita que os usuários adquiram facilmente aplicativos úteis que se integram ao Microsoft 365 e ao Azure, ele pode representar um risco se não for usado e monitorado com cuidado. [Desabilitar todas as operações futuras de consentimento do usuário](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) pode ajudar a reduzir a área da superfície e reduzir esse risco. Se o consentimento do usuário final for desabilitado, as concessões de consentimento anteriores ainda serão respeitadas, mas todas as operações de consentimento futuras deverão ser executadas por um administrador. Antes de desabilitar essa funcionalidade, é recomendável garantir que os usuários compreendam como solicitar aprovação de administrador para novos aplicativos; fazer isso deve ajudar a reduzir o conflito do usuário, minimizar o volume de suporte e garantir que os usuários não se inscrevam em aplicativos que usam credenciais não Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar Azure AD Privileged Identity Management

Outro impacto de "supor a violação" é a necessidade de minimizar a probabilidade de que uma conta comprometida possa operar com uma função privilegiada. 

O [Azure ad Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ajuda você a minimizar privilégios de conta, ajudando-o a:

* Identificar e gerenciar usuários atribuídos a funções administrativas.
* Entenda as funções de privilégio não utilizadas ou excessivas que devem ser removidas.
* Estabeleça regras para garantir que as funções privilegiadas sejam protegidas pela autenticação multifator.
* Estabeleça regras para garantir que as funções privilegiadas sejam concedidas somente por tempo suficiente para realizar a tarefa privilegiada.

Habilite o Azure AD PIM e exiba os usuários aos quais foram atribuídas funções administrativas e remova as contas desnecessárias nessas funções. Para usuários com privilégios restantes, mova-os de permanente para qualificado. Por fim, estabeleça políticas apropriadas para garantir quando elas precisam obter acesso a essas funções privilegiadas, elas podem fazer isso com segurança, com o controle de alteração necessário.

Como parte da implantação do processo de conta com privilégios, siga a [prática recomendada para criar pelo menos duas contas de emergência](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) para garantir que você tenha acesso ao AD do Azure se você se bloquear.

## <a name="step-3---automate-threat-response"></a>Etapa 3 – automatizar a resposta contra ameaças

Azure Active Directory tem muitos recursos que interceptam ataques automaticamente, a fim de remover a latência entre detecção e resposta. Você pode reduzir os custos e riscos ao reduzir o tempo que os criminosos usam para se incorporarem ao seu ambiente. Aqui estão as etapas concretas que você pode tomar.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança de risco do usuário usando o Azure AD Identity Protection

Risco do usuário indica a probabilidade de a identidade de um usuário ter sido comprometida e é calculada com base nos [eventos de risco do usuário](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) que estão associados à identidade de um usuário. Uma política de risco do usuário é uma política de acesso condicional que avalia o nível de risco para um usuário ou grupo específico. Com base no nível baixo, médio e alto de risco, uma política pode ser configurada para bloquear o acesso ou exigir uma alteração de senha segura usando a autenticação multifator. A recomendação da Microsoft é exigir uma alteração de senha segura para os usuários em alto risco.

![Utilizadores marcados em risco](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar a política de risco de entrada usando o Azure AD Identity Protection

O risco de entrada é a probabilidade de alguém que não o proprietário da conta tentar fazer logon usando a identidade. Uma [política de risco de entrada](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma política de acesso condicional que avalia o nível de risco para um usuário ou grupo específico. Com base no nível de risco (alta/média/baixa), uma política pode ser configurada para bloquear o acesso ou forçar a autenticação multifator. Certifique-se de forçar a autenticação multifator em entradas de risco média ou superior.

![Entrar de IPs anônimos](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Etapa 4-aumentar sua conscientização

A auditoria e o registro em log de eventos relacionados à segurança e alertas relacionados são componentes essenciais de uma estratégia de proteção eficiente. Os logs e relatórios de segurança fornecem um registro eletrônico de atividades suspeitas e ajudam você a detectar padrões que podem indicar a tentativa ou a penetração externa bem-sucedida da rede e ataques internos. Você pode usar a auditoria para monitorar a atividade do usuário, a conformidade regulatória de documentos, realizar análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitorar o Azure AD

Microsoft Azure serviços e recursos fornecem opções de auditoria e de log de segurança configuráveis para ajudá-lo a identificar lacunas em suas políticas e mecanismos de segurança e resolver essas lacunas para ajudar a evitar violações. Você pode usar o [log do Azure e a auditoria](https://docs.microsoft.com/azure/security/fundamentals/log-audit) e usar [relatórios de atividade de auditoria no portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorar Azure AD Connect Health em ambientes híbridos

O [monitoramento de AD FS com o Azure ad Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) fornece mais informações sobre possíveis problemas e visibilidade de ataques na sua infraestrutura de AD FS. Azure AD Connect Health fornece alertas com detalhes, etapas de resolução e links para a documentação relacionada; análise de uso para várias métricas relacionadas ao tráfego de autenticação; monitoramento e relatórios de desempenho.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorar eventos de Azure AD Identity Protection

[Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma ferramenta de notificação, monitoramento e relatório que pode ser usada para detectar possíveis vulnerabilidades que afetam as identidades da sua organização. Ele detecta eventos de risco, como credenciais vazadas, viagem impossível e entradas de dispositivos infectados, endereços IP anônimos, endereços IP associados à atividade suspeita e locais desconhecidos. Habilite alertas de notificação para receber emails de usuários em risco e/ou um email de resumo semanal.

Azure AD Identity Protection fornece dois relatórios importantes que você deve monitorar diariamente:
1. Os relatórios de entrada arriscados orientarão as atividades de entrada do usuário que você deve investigar, o proprietário legítimo pode não ter executado a entrada.
2. Os relatórios de usuário arriscados orientarão as contas de usuário que podem ter sido comprometidas, como credenciais vazadas que foram detectadas ou que o usuário se conectou de diferentes locais, causando um evento de viagem impossível. 

![Utilizadores marcados em risco](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Auditar aplicativos e permissões consentidas

Os usuários podem ser induzidos a navegar para um site ou aplicativos comprometidos que obterão acesso às informações de perfil e aos dados do usuário, como seu email. Um ator mal-intencionado pode usar as permissões consentidas recebidas para criptografar o conteúdo da caixa de correio e solicitar um resgate para obter novamente os dados da caixa de correio. [Os administradores devem revisar e auditar](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) as permissões fornecidas pelos usuários.

## <a name="step-5---enable-end-user-self-help"></a>Etapa 5 – habilitar a autoajuda do usuário final

Tanto quanto possível, você desejará balancear a segurança com a produtividade. Ao longo das mesmas linhas de abordagem da sua jornada com a mentalidade de que você está definindo uma base para segurança a longo prazo, você pode remover o conflito de sua organização, capacitando seus usuários enquanto estiverem vigilantes. 

### <a name="implement-self-service-password-reset"></a>Implementar redefinição de senha de autoatendimento

A [SSPR (redefinição de senha de autoatendimento)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) do Azure oferece um meio simples para os administradores de ti permitirem que os usuários redefinam ou desbloqueiem suas senhas ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que controlam quando os utilizadores acedem ao sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos. 

### <a name="implement-self-service-group-management"></a>Implementar o gerenciamento de grupo de autoatendimento

O Azure AD fornece a capacidade de gerenciar o acesso a recursos usando grupos de segurança e grupos do Office 365. Esses grupos podem ser gerenciados por proprietários de grupo em vez de administradores de ti. Conhecido como [Gerenciamento de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)de autoatendimento, esse recurso permite que proprietários de grupo que não são atribuídos a uma função administrativa criem e gerenciem grupos sem depender de administradores para lidar com suas solicitações.

### <a name="implement-azure-ad-access-reviews"></a>Implementar revisões de acesso do Azure AD

Com as [revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), você pode gerenciar associações de grupo, acesso a aplicativos empresariais e atribuições de função com privilégios para garantir que você mantenha um padrão de segurança que não dê acesso aos usuários por longos períodos de tempo quando eles não necessário.

## <a name="summary"></a>Resumo

Há muitos aspectos em uma infraestrutura de identidade segura, mas esta lista de verificação de cinco etapas ajudará você a realizar rapidamente uma infra-estrutura de identidade segura e mais segura:

* Fortaleça suas credenciais.
* Reduza a área da superfície de ataque.
* Automatize a resposta a ameaças.
* Aumente a conscientização da auditoria e do monitoramento.
* Permita a segurança mais previsível e completa do usuário final com auto-ajuda.

Agradecemos o quão sério você assume a segurança da identidade e esperamos que este documento seja um roteiro útil para uma postura mais segura para sua organização.

## <a name="next-steps"></a>Passos Seguintes
Se você precisar de assistência para planejar e implantar as recomendações, consulte os [planos de implantação de projetos do Azure ad](https://aka.ms/deploymentplans) para obter ajuda.
