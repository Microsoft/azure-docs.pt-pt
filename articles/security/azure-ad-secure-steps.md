---
title: Cinco etapas para proteger a sua infraestrutura de identidade no Azure Active Directory
description: Este documento descreve uma lista de ações importantes, os administradores devem implementar para os ajudar a proteger sua organização com capacidades do Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: d81ab4283b408f7490f0dc40cce3199ca29c0af1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737234"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinco etapas para proteger a sua infraestrutura de identidade

Se estiver lendo este documento, está atento a importância da segurança. Provavelmente já possuem a responsabilidade de proteger a sua organização. Se precisar de convencer outros a importância da segurança, envie-lhes para ler a versão mais recente [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/intelligence-report).

Este documento ajuda-o a obter uma postura mais segura, utilizando as capacidades do Azure Active Directory utilizando uma lista de verificação de cinco etapas para Inocular a organização contra ataques informáticos.

Esta lista de verificação lhe ajudarão a implantar rapidamente críticas ações recomendadas para proteger a sua organização imediatamente ao explicar como:

* Reforce as suas credenciais.
* Reduza sua área de superfície de ataque.
* Automatize a resposta a ameaças.
* Aumente seu conhecimento sobre auditoria e monitorização.
* Ative mais previsível e completa pelos usuários finais segurança com a ajuda autónoma.

> [!NOTE]
> Muitas das recomendações neste documento aplicam-se apenas às aplicações que estão configuradas para utilizar o Azure Active Directory como respetivo fornecedor de identidade. Configurar aplicações para início de sessão único assegura os benefícios das políticas de credencial, deteção de ameaças, auditorias, registos e outras funcionalidades a adicionar a esses aplicativos. [Início de sessão único através do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) é a base - de que todos os baseiam-se estas recomendações.

As recomendações neste documento estão alinhadas com o [pontuação de proteger a identidade](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score), um automatizada de avaliação da configuração de segurança de identidade do seu inquilino do Azure AD. As organizações podem utilizar a página de pontuação de seguro de identidade no portal do Azure AD para encontrar lacunas na respetiva configuração de segurança atuais para garantir que eles seguem atuais melhores práticas da Microsoft para segurança. A implementação de cada recomendação na página de pontuação Secure irá aumentar a sua pontuação e permitem-lhe acompanhar seu progresso, além de ajudar a comparar a sua implementação em relação a outras organizações semelhantes do tamanho ou de seu setor.

![Pontuação de seguro de identidade](media/azure-ad/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Antes de começar: Proteger as contas com privilégios com a MFA

Antes de começar esta lista de verificação, certifique-se de que não são comprometidas enquanto estiver lendo esta lista de verificação. Primeiro tem de proteger as suas contas com privilégios.

Os invasores que obtém controle de contas com privilégios podem fazê-lo enorme danos, é fundamental para proteger estas contas pela primeira vez. Ativar e exigir [multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) para todos os administradores na sua organização ao utilizar [proteção de linha de base](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Se ainda não implementado MFA, fazê-lo agora! É tão importante.

Tudo pronto? Vamos começar a lista de verificação.

## <a name="step-1---strengthen-your-credentials"></a>Passo 1 – reforçar as suas credenciais 

A maioria das falhas de segurança empresarial têm origem com uma conta comprometida com um dos vários métodos como spray de palavra-passe, a repetição de violação ou phishing. Saiba mais sobre estes ataques neste vídeo (45 minutos):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Certifique-se a sua organização utilizar uma autenticação segura

Tendo em conta a frequência de senhas que está a ser percebido, phished, roubado com software maligno ou reutilizados, é fundamental para fazer uma cópia da palavra-passe com alguma forma de credencial forte – Saiba mais sobre [multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Início banir normalmente atacado palavras-passe e desativar a complexidade tradicional e regras de expiração.

Muitas organizações utilizam a complexidade tradicional (exigir carateres especiais, números, letras maiúsculas e minúsculas) e as regras de expiração de palavra-passe. [Pesquisa da Microsoft](https://aka.ms/passwordguidance) tenha mostrado aos utilizadores escolher palavras-passe que são mais fáceis de adivinhar de com que estas políticas.

O Azure AD [palavra-passe de banidas dinâmica](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) recurso usa o comportamento atual do invasor para impedir que os utilizadores a definição de palavras-passe que podem ser facilmente adivinhadas. Esta capacidade está sempre ativado quando os utilizadores são criados na cloud, mas agora também está disponível para organizações híbridas quando implementam [proteção de palavra-passe do Azure AD para o Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Proteção de palavra-passe do Azure AD bloqueia os utilizadores escolham estas palavras-passe comuns e pode ser estendida para bloquear a palavra-passe que contém palavras-chave personalizadas que especificar. Por exemplo, pode impedir que os utilizadores escolher palavras-passe que contém nomes de produtos da sua empresa ou uma equipe de esporte local.

A Microsoft recomenda a adotar a seguinte política de palavra-passe modernas com base na [documentação de orientação do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Exigir que as senhas têm, pelo menos, 8 carateres. Já não é necessariamente a melhor, pois estes provocarem aos utilizadores escolher palavras-passe previsível, guardar palavras-passe nos ficheiros ou anotá-las.
2. Desativar regras de expiração, o que levar os seus utilizadores para palavras-passe facilmente adivinháveis como **Summer2018!**
3. Desative a requisitos de composição de caracteres e impedir que os utilizadores escolham normalmente atacadas palavras-passe, como eles fazem com que os usuários escolham substituições de caráter previsível em palavras-passe.

Pode usar [PowerShell para impedir que as palavras-passe prestes a expirar](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) para os utilizadores se criar as identidades no Azure AD diretamente. Organizações híbridas devem implementar estas políticas usando [definições de política de grupo do domínio](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) ou [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteger contra fugas de credenciais e adicionar resiliência contra falhas

Se a sua organização utilizar uma solução de identidade híbrida com a autenticação pass-through ou Federação, em seguida, deve ativar sincronização de hash de palavra-passe para os dois motivos seguintes:

* O [os utilizadores com fuga de credenciais](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) avisa-o de pares de nome de utilizador e palavra-passe, que tenham sido apresentadas na "web escura". o relatório na gestão do Azure AD Um volume incrível de palavras-passe é perdido por meio de reutilização de phishing, malware e palavra-passe em sites de terceiros que são mais tarde infringido. Microsoft encontra muitas dessas fuga de credenciais e informará a, neste relatório, se corresponderem credenciais na sua organização – mas apenas se [ativar a sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* Em caso de interrupção no local (por exemplo, num ataque de ransomware) poderá mudar para utilizar [autenticação de cloud com a sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Este método de autenticação de cópia de segurança, poderá continuar a aceder a aplicações configuradas para a autenticação com o Azure Active Directory, incluindo o Office 365. Neste caso a equipe de TI não precisarão de recorrer a contas de e-mail pessoal para partilhar dados até que a resolução da indisponibilidade de no local.

Saiba mais sobre como [sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) funciona.

> [!NOTE]
> Se ativar a sincronização de hash de palavra-passe e estiver a utilizar o Azure AD Domain services, hashes de Kerberos (AES 256) e, opcionalmente, hashes NTLM (RC4, sem salt) serão também ser encriptados e sincronizados com o Azure AD. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementar o bloqueio de extranet inteligente do AD FS

As organizações, que configura as aplicações para autenticar diretamente para o Azure AD se beneficiar [bloqueio inteligente do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Se utilizar o AD FS no Windows Server 2012R2, implementar o AD FS [proteção de bloqueio de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se utilizar o AD FS no Windows Server 2016, implemente [bloqueio de extranet inteligente](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS inteligente Extranet lockout protege contra força bruta ataques, de destino que do AD FS, enquanto impede os utilizadores de que está a ser bloqueada no Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Tire partido de intrinsecamente seguro, mais fácil de usar credenciais

Usando [Hello do Windows](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), pode substituir as palavras-passe com autenticação de dois fatores forte em PCs e dispositivos móveis. Esta autenticação é composta por um novo tipo de credencial do usuário que está associado com segurança a um dispositivo e utiliza um biometria ou PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Passo 2 - reduzir a superfície de ataque

Tendo em conta a permeabilidade do comprometimento de palavra-passe, minimizar a superfície de ataque da sua organização é fundamental. Eliminar o uso de protocolos mais antigos e menos seguros, limitando a entrada de acesso a pontos e executamos mais significativo controlo de acesso administrativo aos recursos podem ajudar a reduzir a área de superfície de ataque.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada

Aplicações com seus próprios métodos herdados para autenticar com o Azure AD e aceder aos dados da empresa, representar outro risco para as organizações. Exemplos de aplicações que utilizam autenticação herdada são clientes POP3, IMAP4 ou SMTP. As aplicações de autenticação autenticar em nome do utilizador e impedem que ao fazê-lo advanced avaliações de segurança do Azure AD. A autenticação moderna, alternativa, irá reduzir o risco de segurança, uma vez que suporta a autenticação multifator e acesso condicional. Recomendamos as seguintes três ações:

1. Bloco [antigos de autenticação se utilizar o AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Programa de configuração [SharePoint Online e Exchange Online, para utilizar a autenticação moderna](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Uso [políticas de acesso condicional para bloquear antigos de autenticação](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions).

### <a name="block-invalid-authentication-entry-points"></a>Pontos de entrada de autenticação inválido de bloco

Com a mentalidade de violação de assumir, deve reduzir o impacto de credenciais de utilizador comprometidas quando eles ocorrem. Para cada aplicação no seu ambiente, considere os casos de uso válidos: os grupos, quais as redes, quais os dispositivos e outros elementos estão autorizados – em seguida, bloquear o resto. Com o [acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), pode controlar o acesso de utilizadores como autorizado, as aplicações e recursos com base em condições específicas que definir.

### <a name="block-end-user-consent"></a>Consentimento de utilizador final do bloco

Por predefinição, todos os utilizadores no Azure AD têm permissão para conceder as aplicações que tiram partido de OAuth 2.0 e a identidade da Microsoft [consentimento framework](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) permissões para aceder aos dados da empresa. Enquanto consentir permitir aos utilizadores obter facilmente aplicativos úteis que se integram com o Microsoft 365 e do Azure, ele pode representar um risco de se não for utilizado e cuidadosamente monitorizada. [Desativar todas as operações de consentimento do utilizador futuras](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) pode ajudar a reduzir sua área de superfície e diminuir esse risco. Se o consentimento do utilizador final estiver desativado anterior ainda serão cumpridas as concessões de consentimento, mas todas as operações de consentimento futuras devem ser executadas por um administrador. Antes de desativar esta funcionalidade é recomendado para garantir que os utilizadores saberá como pedir aprovação de administrador para novos aplicativos; Isso deve ajudar a reduzir o atrito de utilizador, minimize o volume de suporte e certificar-se de que os utilizadores não fizer login para aplicações utilizando as credenciais do não pertencente ao Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementar o Azure AD Privileged Identity Management

Outro impacto de "assumir que houve violação" é a necessidade de minimizar a probabilidade de que uma conta comprometida pode operar com uma função com privilégios. 

[O Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) ajuda minimizado privilégios de conta ao ajudá-lo:

* Identificar e gerir os utilizadores atribuídos a funções administrativas.
* Compreenda as funções de privilégio não utilizadas ou excessiva, que deve remover.
* Estabeleça as regras para tornar as funções privilegiadas-se de que estão protegidas pelo multi-factor authentication.
* Estabeleça as regras para se certificar de que funções com privilégios são concedidas tempo suficiente para realizar a tarefa com privilégios.

Ativar PIM do Azure AD, em seguida, ver os utilizadores que são atribuídos a funções administrativas e remover contas desnecessárias essas funções. Para os restantes utilizadores com privilégios, movê-los de permanente para elegíveis. Por fim, estabelecer políticas adequadas para garantir que quando precisam de obter acesso a essas funções com privilégios, pode fazê-lo em segurança, com as informações necessárias de controle de alterações.

Como parte da implementação de seu processo de conta com privilégios, siga os [melhor prática para criar, pelo menos, duas contas de emergência](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) para se certificar de que tem acesso ao Azure AD Se preso.

## <a name="step-3---automate-threat-response"></a>Passo 3 - automatizar a resposta de ameaças

O Azure Active Directory tem muitos recursos que automaticamente interceptar ataques, para remover a latência entre a deteção e resposta. Pode reduzir os custos e riscos, quando reduz os criminosos tempo utilizam para incorporar próprios em seu ambiente. Eis os passos concretos que pode tomar.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementar a política de segurança de risco do utilizador com o Azure AD Identity Protection

Risco de utilizador indica a probabilidade de a identidade do utilizador foi comprometida e é calculada com base na [eventos de risco do utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) que estão associados com a identidade do utilizador. Uma política de risco do utilizador é uma política de acesso condicional que avalia o nível de risco para um grupo ou utilizador específico. Com base na baixa, média, alta de nível de risco, uma política pode ser configurada para bloquear o acesso ou exigir uma alteração de palavra-passe segura através da autenticação multifator. A Microsoft recomenda exigir uma alteração de palavra-passe segura para os utilizadores em risco elevado.

![Utilizadores marcados em risco](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementar política de risco de início de sessão com o Azure AD Identity Protection

Início de sessão de risco é a probabilidade de que alguém que não seja o proprietário da conta está a tentar iniciar sessão utilizando a identidade. R [política de risco de início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma política de acesso condicional que avalia o nível de risco para um grupo ou utilizador específico. Com base no nível de risco (baixo/médio/alto), uma política pode ser configurada para bloquear o acesso ou forçar autenticação multifator. Certifique-se de que força autenticação multifator no Medium ou acima inícios de sessão de risco.

![Iniciar sessão de IPs anónimo](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Passo 4 - aumentar o seu conhecimento

Auditoria e Registro em log de eventos relacionados com a segurança e alertas relacionados são componentes essenciais de uma estratégia de proteção eficiente. Registos de segurança e de relatórios proporcionam um registo eletrónico de atividades suspeitas e ajuda detectar padrões que podem indicar uma tentativa ou com êxito penetração externa da rede e ataques internos. Pode utilizar a auditoria para monitorizar a atividade do utilizador, a conformidade a normas documento, executar análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="monitor-azure-ad"></a>Monitor do Azure AD

Funcionalidades e serviços do Microsoft Azure oferecem segurança configuráveis, auditoria e registo de opções para ajudar a identificar lacunas na sua mecanismos e as políticas de segurança e abordar essas lacunas para ajudar a evitar falhas. Pode utilizar [do Azure de registo e auditoria](https://docs.microsoft.com/azure/security/azure-log-audit) e utilizar [relatórios de atividade no portal do Azure Active Directory de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorizar o Azure AD Connect Health em ambientes híbridos

[Monitorizar o AD FS com o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) fornece-lhe uma visão mais clara potenciais problemas e a visibilidade de ataques na sua infraestrutura do AD FS. O Azure AD Connect Health fornece alertas com detalhes, passos de resolução e ligações para documentação relacionada; análise de utilização do várias métricas relacionadas com o tráfego de autenticação: monitorização de desempenho e relatórios.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorizar eventos do Azure AD Identity Protection

[O Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma notificação, monitorização e relatórios a ferramenta que pode utilizar para detetar potenciais vulnerabilidades que afetam as identidades da sua organização. Detetar eventos de risco, como a fuga de credenciais, deslocação impossível, e inícios de sessão de infectado dispositivos, endereços IP anónimos, endereços IP associados à atividade suspeita e localizações desconhecidas. Ative alertas de notificação receber o e-mail de utilizadores em risco e/ou um e-mail de resumo semanal.

O Azure AD Identity Protection fornece dois relatórios importantes, que deve monitorar os diários:
1. Relatórios de início de sessão arriscados irão descobrir o início de sessão atividades do utilizador que deve investigar, o proprietário legítimo poderá não tiver executado o início de sessão.
2. Relatórios de utilizador de risco irão descobrir a contas de utilizador que poderão ter sido comprometidas, por exemplo, a fuga de credenciais que foi detectada ou o utilizador iniciou sessão a partir de diferentes localizações, fazendo com que um evento de deslocação impossível. 

![Utilizadores marcados em risco](media/azure-ad/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Aplicações de auditoria e permissões autorizadas

Os utilizadores podem ser induzidos a navegar para um web site comprometido ou as aplicações que irão obter acesso aos seus informações e utilizador dados de perfil, como o respetivo e-mail. Um ator malicioso pode utilizar as permissões autorizadas recebida para encriptar o seu conteúdo da caixa de correio e exigem uma ransom para recuperar os dados de caixa de correio. [Os administradores devem rever e auditar](https://blogs.technet.microsoft.com/office365security/defending-against-illicit-consent-grants/) as permissões concedidas por utilizadores.

## <a name="step-5---enable-end-user-self-help"></a>Passo 5 – ativar auto-ajuda de utilizador final

Tanto quanto possível vai querer equilibrar a segurança com a produtividade. Um modo geral de atingir o seu percurso com a mentalidade que estiver a configurar uma base de segurança a longo prazo, pode remover o atrito da sua organização ao capacitando seus usuários ao mesmo tempo restante vigilante. 

### <a name="implement-self-service-password-reset"></a>Implementar a reposição de palavra-passe self-service

Do Azure [reposição de palavra-passe self-service (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) oferece uma forma simples para os administradores de TI permitir que os utilizadores a repor ou desbloquear as suas palavras-passe ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que controlam quando os utilizadores acedem ao sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos. 

### <a name="implement-self-service-group-management"></a>Implementar a gestão de grupos self-service

Azure AD fornece a capacidade para gerir o acesso a recursos através de grupos de segurança e grupos do Office 365. Estes grupos podem ser geridos por proprietários do grupo em vez dos administradores de TI. Conhecido como [gestão de grupos self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), esse recurso permite que os proprietários do grupo que não estão atribuídos uma função administrativa para criar e gerir grupos sem depender de administradores para processar os pedidos.

### <a name="implement-azure-ad-access-reviews"></a>Revisões de acesso de implementar do Azure AD

Com o [revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), pode gerir as associações de grupo, acesso a aplicações empresariais, e estendido de atribuições de funções privilegiadas para se certificar de que mantém uma norma de segurança que não dar acesso aos utilizadores para períodos de hora quando eles não precisam dela.

## <a name="summary"></a>Resumo

Há muitos aspectos de uma infraestrutura de identidade segura, mas esta lista de verificação de cinco etapas irá ajudá-lo a realizar com rapidez uma infraestrutura de identidade segura e mais segura:

* Reforce as suas credenciais.
* Reduza sua área de superfície de ataque.
* Automatize a resposta a ameaças.
* Aumente seu conhecimento sobre auditoria e monitorização.
* Ative mais previsível e completa pelos usuários finais segurança com a ajuda autónoma.

Agradecemos muito a sério como levar a segurança de identidade e Espero que este documento é um Roteiro úteis para uma postura mais segura para a sua organização.

## <a name="next-steps"></a>Passos Seguintes
Se precisar de assistência para planear e implementar as recomendações, consulte a [planos de implantação do projeto do Azure AD](https://aka.ms/deploymentplans) para obter ajuda.