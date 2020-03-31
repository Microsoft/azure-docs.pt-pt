---
title: Autenticação para soluções de identidade híbrida Azure AD
titleSuffix: Active Directory
description: Este guia ajuda os CEO, CIOs, CISOs, Chief Identity Architects, Enterprise Architects e Os decisores de Segurança e TI responsáveis pela escolha de um método de autenticação para a sua solução de identidade híbrida Azure AD em média a grandes organizações.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e263ecde532a8aaed420932bf355910da201723e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365837"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory

Escolher o método de autenticação correto é a primeira preocupação para as organizações que querem mover as suas apps para a nuvem. Não tome esta decisão de ânimo leve, pelas seguintes razões:

1. É a primeira decisão para uma organização que quer passar para a nuvem.

2. O método de autenticação é um componente crítico da presença de uma organização na nuvem. Controla o acesso a todos os dados e recursos da nuvem.

3. É a base de todas as outras funcionalidades avançadas de segurança e experiência do utilizador em Azure AD.

A identidade é o novo plano de controlo da segurança das TI, por isso a autenticação é o guarda de acesso de uma organização ao novo mundo das nuvens. As organizações precisam de um plano de controlo de identidade que fortaleça a sua segurança e mantenha as suas aplicações em nuvem a salvo de intrusos.

> [!NOTE]
> Alterar o seu método de autenticação requer planeamento, testes e potencialmente tempo de inatividade. [O lançamento encenado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) é uma ótima maneira de testar e migrar gradualmente da federação para a autenticação em nuvem.

### <a name="out-of-scope"></a>Fora de âmbito
Organizações que não têm uma pegada de diretório no local não são o foco deste artigo. Normalmente, essas empresas criam identidades apenas na nuvem, o que não requer uma solução de identidade híbrida. As identidades apenas em nuvem existem apenas na nuvem e não estão associadas com as identidades correspondentes no local.

## <a name="authentication-methods"></a>Métodos de autenticação
Quando a solução de identidade híbrida Azure AD é o seu novo plano de controlo, a autenticação é a base do acesso à nuvem. Escolher o método de autenticação correto é uma primeira decisão crucial na criação de uma solução de identidade híbrida Azure AD. Implementar o método de autenticação configurado utilizando o Azure AD Connect, que também disponibiliza utilizadores na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação da sua escolha. Estes fatores são diferentes para cada organização e podem mudar com o tempo.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

A Azure AD suporta os seguintes métodos de autenticação para soluções de identidade híbrida.

### <a name="cloud-authentication"></a>Autenticação em nuvem
Ao escolher este método de autenticação, o Azure AD trata do processo de entrada dos utilizadores. Juntamente com um único sinal individual sem emenda (SSO), os utilizadores podem iniciar sessão em aplicações na nuvem sem terem de reentrar nas suas credenciais. Com a autenticação em nuvem, pode escolher entre duas opções:

**Sincronização de hash de senha Azure AD**. A forma mais simples de permitir a autenticação de objetos de diretório no local em Azure AD. Os utilizadores podem usar o mesmo nome de utilizador e senha que utilizam no local sem terem de implementar qualquer infraestrutura adicional. Algumas funcionalidades premium da Azure AD, como AD de Proteção de Identidade e Serviços de [Domínio Azure AD,](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)requerem sincronização de hash de senha, independentemente do método de autenticação que escolher.

> [!NOTE]
> As palavras-passe nunca são armazenadas em texto claro ou encriptadas com um algoritmo reversível em Azure AD. Para obter mais informações sobre o processo real de sincronização de hash de palavra-passe, consulte [Implementação de sincronização de hash com sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Autenticação de passagem de AD Azure.** Fornece uma simples validação de palavra-passe para serviços de autenticação AD Azure utilizando um agente de software que funciona em um ou mais servidores no local. Os servidores validam os utilizadores diretamente com o seu Diretório Ativo no local, que garante que a validação da palavra-passe não acontece na nuvem.

As empresas com o requisito de segurança para impor imediatamente no local os estados da conta de utilizador, as políticas de password e o horário de inscrição podem utilizar este método de autenticação. Para obter mais informações sobre o processo de autenticação pass-through real, consulte o utilizador de início de sessão com a [autenticação pass-through da AD Azure](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Autenticação federada
Ao escolher este método de autenticação, a Azure AD entrega o processo de autenticação a um sistema de autenticação fidedigno separado, como os Serviços da Federação de Diretório Ativo (AD FS), para validar a palavra-passe do utilizador.

O sistema de autenticação pode fornecer requisitos adicionais de autenticação avançada. Exemplos são a autenticação baseada em smartcard ou a autenticação multifactor de terceiros. Para mais informações, consulte a Implementação de [Serviços da Federação de DiretórioActivo](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

A secção seguinte ajuda-o a decidir qual o método de autenticação certo para si, utilizando uma árvore de decisão. Ajuda-o a determinar se deve implementar a autenticação em nuvem ou federada para a sua solução de identidade híbrida Azure AD.

## <a name="decision-tree"></a>Árvore de decisões

![Árvore de decisão de autenticação da AD Azure](./media/choose-ad-authn/azure-ad-authn-image1.png)

Detalhes sobre questões de decisão:

1. O Azure AD pode lidar com o check-in para os utilizadores sem depender de componentes no local para verificar as palavras-passe.
2. O Azure AD pode entregar o início de sessão do utilizador a um fornecedor de autenticação fidedigno, como o AD FS da Microsoft.
3. Se necessitar de aplicar políticas de segurança de diretório ativo ao nível do utilizador, tais como conta caducada, conta desativada, senha expirada, conta bloqueada e horas de início de cada utilizador, o Azure AD requer alguns componentes no local.
4. Características de entrada não suportadas de forma nativa pela Azure AD:
   * Inscreva-se utilizando cartões inteligentes ou certificados.
   * Iniciar sessão utilizando o MFA Server no local.
   * Iniciar sessão utilizando solução de autenticação de terceiros.
   * Solução de autenticação multi-local no local.
5. A Proteção de Identidade Azure AD requer password Hash Sync independentemente do método de inscrição que escolher, para fornecer aos Utilizadores um relatório de *credenciais vazado.* As organizações podem falhar com o Password Hash Sync se o seu método principal de entrada falhar e foi configurado antes do evento de falha.

> [!NOTE]
> A Proteção de Identidade Azure AD requer licenças [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Considerações pormenorizadas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticação em nuvem: Sincronização de hash de palavra-passe

* **Esforço.** A sincronização do hash de senha requer o menor esforço no que diz respeito à implantação, manutenção e infraestrutura.  Este nível de esforço aplica-se tipicamente a organizações que apenas precisam que os seus utilizadores assinem no Office 365, aplicações SaaS e outros recursos baseados em AD Azure. Quando ligado, a sincronização de hash de senha faz parte do processo de sincronização Azure AD Connect e funciona a cada dois minutos.

* **Experiência do utilizador.** Para melhorar a experiência de entrada dos utilizadores, implemente sso sem emenda com sincronização de hash de palavra-passe. O SSO sem emenda elimina as solicitações desnecessárias quando os utilizadores são inscritos.

* **Cenários avançados.** Se as organizações optarem por fazê-lo, é possível utilizar insights de identidades com relatórios de Proteção de Identidade Azure AD com o Azure AD Premium P2. Um exemplo é o relatório de credenciais vazado. O Windows Hello for Business tem [requisitos específicos quando utiliza](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)a sincronização de hash de palavra-passe . [O Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) requer sincronização de hash de palavra-passe para fornecer aos utilizadores as suas credenciais corporativas no domínio gerido.

    As organizações que necessitem de autenticação multifactor com sincronização de hash de palavra-passe devem utilizar a autenticação multifactor Azure AD ou [controlos personalizados de Acesso Condicional](../../active-directory/conditional-access/controls.md#custom-controls-preview). Essas organizações não podem usar métodos de autenticação multifactor de terceiros ou no local que dependem da federação.

> [!NOTE]
> O Acesso Condicional Azure AD requer licenças [Azure AD Premium P1.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Continuidade do negócio.** A utilização de sincronização de hash com autenticação em nuvem está altamente disponível como um serviço na nuvem que escala para todos os datacenters da Microsoft. Para garantir que a sincronização do hash da palavra-passe não se desliga durante longos períodos, implemente um segundo servidor Azure AD Connect no modo de preparação numa configuração de espera.

* **Considerações.** Atualmente, a sincronização de hash de palavra-passe não impõe imediatamente alterações nos estados da conta no local. Nesta situação, um utilizador tem acesso a aplicações na nuvem até que o estado da conta de utilizador seja sincronizado para a AD Azure. As organizações podem querer ultrapassar esta limitação executando um novo ciclo de sincronização depois de os administradores fazerem atualizações a granel para os estados da conta de utilizador no local. Um exemplo é a desativação das contas.

> [!NOTE]
> A palavra-passe expirada e os estados de bloqueio de conta não estão atualmente sincronizados com o Azure AD Connect. Quando alterar a palavra-passe de um utilizador e definir o *utilizador deve alterar a palavra-passe na próxima* bandeira de início de sessão, o hash de senha não será sincronizado com o Azure AD Connect até que o utilizador altere a sua palavra-passe.

Consulte a implementação da [sincronização de hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) de senha para passos de implementação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação em nuvem: Autenticação pass-through  

* **Esforço.** Para a autenticação pass-through, precisa de um ou mais (recomendamos três) agentes leves instalados nos servidores existentes. Estes agentes devem ter acesso aos seus Serviços de Domínio de Diretório Ativo no local, incluindo os seus controladores de domínio AD no local. Precisam de acesso de saída à Internet e acesso aos seus controladores de domínio. Por esta razão, não é suportado para colocar os agentes numa rede de perímetro.

    A autenticação pass-through requer acesso sem restrições à rede aos controladores de domínio. Todo o tráfego de rede está encriptado e limitado a pedidos de autenticação. Para obter mais informações sobre este processo, consulte a segurança profunda [na](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) autenticação pass-through.

* **Experiência do utilizador.** Para melhorar a experiência de inscrição dos utilizadores, implemente sso sem emenda com autenticação pass-through. SSO sem emenda elimina solicitações desnecessárias após o insessão dos utilizadores.

* **Cenários avançados.** A Autenticação Pass-through aplica a política de conta no local no momento do início de sessão. Por exemplo, o acesso é negado quando o estado de conta de um utilizador no local é desativado, bloqueado ou a sua [palavra-passe expira](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) ou a tentativa de logon cai fora das horas em que o utilizador é autorizado a iniciar sessão.

    As organizações que necessitem de autenticação multifactor com autenticação pass-through devem utilizar [controlos personalizados](../../active-directory/conditional-access/controls.md#custom-controls-preview)de autenticação Multifactor (MFA) ou Acesso Condicional. Essas organizações não podem usar um método de autenticação multifactor de terceiros ou no local que dependa da federação. As funcionalidades avançadas requerem que a sincronização do hash de palavra-passe seja implementada quer escolha ou não a autenticação pass-through. Exemplo disso é o relatório de credenciais vazados da Proteção de Identidade.

* **Continuidade do negócio.** Recomendamos que disque dois agentes de autenticação de passagem extra. Estes extras são além do primeiro agente no servidor Azure AD Connect. Esta implementação adicional garante uma elevada disponibilidade de pedidos de autenticação. Quando se tem três agentes destacados, um agente ainda pode falhar quando outro agente está em baixo para manutenção.

    Há outro benefício para implementar sincronização de hash de senha, além da autenticação pass-through. Funciona como um método de autenticação de reserva quando o método de autenticação primária já não está disponível.

* **Considerações.** Pode utilizar a sincronização de hash de palavra-passe como um método de autenticação de backup para autenticação pass-through, quando os agentes não podem validar as credenciais de um utilizador devido a uma falha significativa no local. Não a sincronização de hash de palavra-passe não acontece automaticamente e tem de utilizar o Azure AD Connect para mudar manualmente o método de início de sessão.

    Para outras considerações sobre a autenticação pass-through, incluindo suporte de ID alternativo, consulte [frequentemente perguntas](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Consulte a implementação da [autenticação pass-through](../../active-directory/hybrid/how-to-connect-pta.md) para as etapas de implementação.

### <a name="federated-authentication"></a>Autenticação federada

* **Esforço.** Um sistema de autenticação federado baseia-se num sistema de confiança externo para autenticar os utilizadores. Algumas empresas querem reutilizar o investimento do sistema federado existente com a sua solução de identidade híbrida Azure AD. A manutenção e gestão do sistema federado fica fora do controlo da Azure AD. Cabe à organização usar o sistema federado para se certificar de que está implantado de forma segura e que consegue lidar com a carga de autenticação.

* **Experiência do utilizador.** A experiência do utilizador da autenticação federada depende da implementação das funcionalidades, topologia e configuração da quinta da federação. Algumas organizações precisam desta flexibilidade para adaptar e configurar o acesso à quinta da federação de acordo com os seus requisitos de segurança. Por exemplo, é possível configurar utilizadores e dispositivos ligados internamente para iniciar sessão automática nos utilizadores, sem os solicitar credenciais. Esta configuração funciona porque já assinaram nos seus dispositivos. Se necessário, algumas funcionalidades de segurança avançadas dificultam o processo de início de sessão dos utilizadores.

* **Cenários avançados.** Uma solução de autenticação federada é geralmente necessária quando os clientes têm um requisito de autenticação que a Azure AD não suporta de forma nativa. Consulte informações detalhadas para o ajudar [a escolher a opção de inscrição certa](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Considere os seguintes requisitos comuns:

  * Autenticação que requer cartões inteligentes ou certificados.
  * No local, servidores MFA ou fornecedores multifactor de terceiros que requerem um fornecedor de identidade federado.
  * Autenticação utilizando soluções de autenticação de terceiros. Consulte a lista de compatibilidade da [Federação Azure.](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)
  * Assine o que requer um nome sAMAccount, por exemplo DOMAIN\username, em vez user@domain.comde um Nome Principal do Utilizador (UPN), por exemplo, .

* **Continuidade do negócio.** Os sistemas federados normalmente requerem um conjunto de servidores equilibrados em carga, conhecidos como uma fazenda. Esta quinta está configurada numa rede interna e topologia de rede de perímetro para garantir uma elevada disponibilidade para pedidos de autenticação.

    Implemente a sincronização de hash de palavra-passe juntamente com a autenticação federada como método de autenticação de backup quando o método de autenticação primária já não estiver disponível. Um exemplo é quando os servidores no local não estão disponíveis. Algumas grandes organizações empresariais requerem uma solução da federação para suportar vários pontos de entrada na Internet configurados com geo-DNS para pedidos de autenticação de baixa latência.

* **Considerações.** Os sistemas federados normalmente exigem um investimento mais significativo em infraestruturas no local. A maioria das organizações escolhe esta opção se já tiver um investimento da federação no local. E se é um forte requisito de negócio para usar um fornecedor de identidade única. A Federação é mais complexa para operar e resolver problemas em comparação com as soluções de autenticação em nuvem.

Para um domínio não repreensível que não possa ser verificado no Azure AD, precisa de uma configuração extra para implementar o sessão de ID do utilizador. Este requisito é conhecido como suporte alternativo de iD de login. Consulte configurar o [ID de login alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para obter limitações e requisitos. Se optar por utilizar um fornecedor de autenticação multifactor de terceiros com a federação, certifique-se de que o fornecedor suporta o WS-Trust para permitir que os dispositivos se juntem à Azure AD.

Consulte os [Servidores da Federação](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) de Implantação para os passos de implementação.

> [!NOTE]
> Quando implementar a sua solução de identidade híbrida Azure AD, deve implementar uma das topoologias suportadas do Azure AD Connect. Saiba mais sobre configurações suportadas e não suportadas em [Topologies para Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

Os seguintes diagramas descrevem os componentes de arquitetura de alto nível necessários para cada método de autenticação que pode utilizar com a sua solução de identidade híbrida Azure AD. Eles fornecem uma visão geral para ajudá-lo a comparar as diferenças entre as soluções.

* Simplicidade de uma solução de sincronização de hash de senha:

    ![Identidade híbrida Azure AD com sincronização de hash password](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Requisitos de agente para autenticação pass-through, utilizando dois agentes para despedimento:

    ![Identidade híbrida Azure AD com Autenticação Pass-through](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Componentes necessários para a federação no seu perímetro e rede interna da sua organização:

    ![Identidade híbrida Azure AD com autenticação federada](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Métodos de comparação

|Consideração|Sincronização de hash de palavra-passe + SSO sem emenda|Autenticação pass-through + SSO sem emenda|Federação com o AD FS|
|:-----|:-----|:-----|:-----|
|Onde é que a autenticação acontece?|Na cloud|Na nuvem após uma troca segura de verificação de senha com o agente de autenticação no local|Local|
|Quais são os requisitos do servidor no local para além do sistema de provisionamento: Azure AD Connect?|Nenhuma|Um servidor para cada agente de autenticação adicional|Dois ou mais servidores AD FS<br><br>Dois ou mais servidores WAP na rede perímetro/DMZ|
|Quais são os requisitos para a Internet no local e para a rede para além do sistema de provisionamento?|Nenhuma|[Acesso à Internet de saída](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) dos servidores que executam agentes de autenticação|[Acesso à Internet de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) a servidores WAP no perímetro<br><br>Acesso à rede de entrada aos servidores AD FS dos servidores WAP no perímetro<br><br>Balanceamento de carga de rede|
|Existe um requisito de certificado TLS/SSL?|Não|Não|Sim|
|Existe uma solução de monitorização da saúde?|Não é necessária|Estatuto de agente fornecido pelo Centro de [Administração do Diretório Ativo azure](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Os utilizadores obtêm um único sinal para os recursos na nuvem a partir de dispositivos ligados ao domínio dentro da rede da empresa?|Sim com [SSO SSO Sem Emenda](../../active-directory/hybrid/how-to-connect-sso.md)|Sim com [SSO SSO Sem Emenda](../../active-directory/hybrid/how-to-connect-sso.md)|Sim|
|Que tipos de inscrição são suportados?|UserPrincipalName + palavra-passe<br><br>Autenticação integrada no Windows utilizando [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Id de login alternativo](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + palavra-passe<br><br>Autenticação integrada no Windows utilizando [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Id de login alternativo](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + palavra-passe<br><br>sAMAccountName + palavra-passe<br><br>Autenticação Integrada no Windows<br><br>[Autenticação de certificado e cartão inteligente](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Id de login alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|O Windows Hello for Business é suportado?|[Modelo de confiança chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modelo de confiança chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Requer o nível funcional do Windows Server 2016*|[Modelo de confiança chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de fidedignidade de certificado](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quais são as opções de autenticação multifactor?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Servidor Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA de terceiros](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|
|Que estados de conta de utilizador são suportados?|Contas com deficiência<br>(atraso até 30 minutos)|Contas com deficiência<br><br>Conta bloqueada<br><br>Conta vencida<br><br>Senha expirada<br><br>Horário de inscrição|Contas com deficiência<br><br>Conta bloqueada<br><br>Conta vencida<br><br>Senha expirada<br><br>Horário de inscrição|
|Quais são as opções de Acesso Condicional?|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Regras de reclamação da AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|O bloqueio dos protocolos antigos é suportado?|[Sim](../../active-directory/conditional-access/overview.md)|[Sim](../../active-directory/conditional-access/overview.md)|[Sim](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Pode personalizar o logótipo, imagem e descrição nas páginas de inscrição?|[Sim, com Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim, com Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Que cenários avançados são apoiados?|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Relatórios de credenciais vazados, com Azure AD Premium P2](../../active-directory/reports-monitoring/concept-risk-events.md)|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)|Sistema multirsite de autenticação de baixa latência<br><br>[Bloqueio extranet AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integração com sistemas de identidade de terceiros](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Os controlos personalizados no Acesso Condicional Azure AD não suportam atualmente o registo do dispositivo.

## <a name="recommendations"></a>Recomendações
O seu sistema de identidade garante o acesso dos seus utilizadores a aplicações na nuvem e às aplicações de linha de negócio que migra e disponibiliza na nuvem. Para manter os utilizadores autorizados produtivos e maus atores fora dos dados sensíveis da sua organização, a autenticação controla o acesso a apps.

Utilize ou ative a sincronização de hash por palavra-passe para qualquer método de autenticação que escolha, pelas seguintes razões:

1. **Alta disponibilidade e recuperação de desastres.** A Autenticação Pass-through e a Federação dependem da infraestrutura no local. Para autenticação pass-through, a pegada no local inclui o hardware do servidor e a rede que os agentes de autenticação Pass-through exigem. Para a federação, a pegada no local é ainda maior. Requer servidores na sua rede de perímetro para pedidos de autenticação por procuração e servidores da federação interna.

    Para evitar pontos de falha individuais, implemente servidores redundantes. Em seguida, os pedidos de autenticação serão sempre reparados se algum componente falhar. Tanto a autenticação pass-through como a federação também contam com controladores de domínio para responder a pedidos de autenticação, o que também pode falhar. Muitos destes componentes precisam de manutenção para se manterem saudáveis. As interrupções são mais prováveis quando a manutenção não é planeada e implementada corretamente. Evite interrupções utilizando a sincronização de hash de palavra-passe porque o serviço de autenticação em nuvem AD Microsoft Azure escala globalmente e está sempre disponível.

2. **No local, a sobrevivência da paralisação.**  As consequências de uma paralisação no local devido a um ciberataque ou desastre podem ser substanciais, desde danos reputacionais à marca para uma organização paralisada incapaz de lidar com o ataque. Recentemente, muitas organizações foram vítimas de ataques de malware, incluindo ransomware direcionado, o que fez com que os seus servidores no local fossem abatidos. Quando a Microsoft ajuda os clientes a lidar com este tipo de ataques, vê duas categorias de organizações:

   * As organizações que anteriormente ativaram a sincronização de hash de senha alteraram o seu método de autenticação para utilizar a sincronização de hash de senha. Voltaram a funcionar numa questão de horas. Ao usar o acesso ao e-mail via Office 365, trabalharam para resolver problemas e aceder a outras cargas de trabalho baseadas na nuvem.

   * As organizações que anteriormente não permitiam a sincronização de hash de palavra-passe tiveram de recorrer a sistemas de email externos não confiáveis para comunicações para resolver problemas. Nesses casos, demoraram semanas a restaurar a sua infraestrutura de identidade no local, antes de os utilizadores poderem voltar a inscrever-se em aplicações baseadas na nuvem.

3. **Proteção de identidade.** Uma das melhores formas de proteger os utilizadores na nuvem é a Azure AD Identity Protection com o Azure AD Premium P2. A Microsoft digitaliza continuamente a Internet para listas de utilizadores e palavras-passe que os maus atores vendem e disponibilizam na dark web. A Azure AD pode utilizar estas informações para verificar se algum dos nomes de utilizador e palavras-passe da sua organização está comprometido. Portanto, é fundamental permitir a sincronização de hash de palavra-passe independentemente do método de autenticação que utilize, seja federado ou autenticação pass-through. As credenciais vazadas são apresentadas como um relatório. Utilize estas informações para bloquear ou forçar os utilizadores a alterar as suas palavras-passe quando tentam iniciar sessão com senhas vazadas.

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implementar para apoiar o acesso a aplicações na nuvem. Para satisfazer vários requisitos de negócio, segurança e requisitos técnicos, as organizações podem escolher entre a sincronização de hash de senha, a autenticação pass-through e a federação.

Considere cada método de autenticação. O esforço para implementar a solução e a experiência do utilizador no processo de iniciar sessão aborda maçadas? Avalie se a sua organização precisa dos cenários avançados e das características de continuidade do negócio de cada método de autenticação. Por fim, avalie as considerações de cada método de autenticação. Algum deles impede-o de implementar a sua escolha?

## <a name="next-steps"></a>Passos seguintes

No mundo de hoje, as ameaças estão presentes 24 horas por dia e vêm de todo o lado. Implemente o método de autenticação correto e irá mitigar os seus riscos de segurança e proteger as suas identidades.

[Inicie-se](../../active-directory/fundamentals/get-started-azure-ad.md) com a Azure AD e implemente a solução de autenticação certa para a sua organização.

Se está a pensar em migrar da autenticação federada para a nuvem, saiba mais sobre [a alteração do método de inscrição](../../active-directory/hybrid/plan-connect-user-signin.md). Para ajudá-lo a planear e implementar a migração, use estes planos de [implementação do projeto](https://aka.ms/deploymentplans) ou considere usar a nova funcionalidade Staged [Rollout](../../active-directory/hybrid/how-to-connect-staged-rollout.md) para migrar utilizadores federados para usar a autenticação em nuvem numa abordagem encenada.
