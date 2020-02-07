---
title: Autenticação para soluções de identidade híbrida Azure AD
titleSuffix: Active Directory
description: Este guia ajuda CEOs, CIOs, CISOs, arquitetos de identidade, arquitetos empresariais e tomadores de decisões de ti responsáveis por escolher um método de autenticação para sua solução de identidade híbrida do Azure AD em organizações de médio a grande porte.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: f32980b736232449d24de8721f354d9ca5dd03ab
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064432"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolha o método de autenticação certo para sua solução de identidade híbrida Azure Active Directory

Escolher o método de autenticação correto é a primeira preocupação para as organizações que desejam mover seus aplicativos para a nuvem. Não tome essa decisão levemente, pelos seguintes motivos:

1. É a primeira decisão para uma organização que deseja migrar para a nuvem.

2. O método de autenticação é um componente crítico da presença de uma organização na nuvem. Ele controla o acesso a todos os dados e recursos de nuvem.

3. É a base de todos os outros recursos avançados de segurança e experiência do usuário no Azure AD.

Identidade é o novo plano de controle da segurança de ti, então a autenticação é a proteção de acesso de uma organização para o novo mundo da nuvem. As organizações precisam de um plano de controle de identidade que fortaleça sua segurança e mantenha seus aplicativos de nuvem protegidos contra intrusos.

> [!NOTE]
> Alterar seu método de autenticação requer planejamento, teste e potencialmente tempo de inatividade. [O lançamento encenado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) é uma ótima maneira de testar e migrar gradualmente da federação para a autenticação em nuvem.

### <a name="out-of-scope"></a>Fora do escopo
As organizações que não têm uma superfície de diretório local existente não são o foco deste artigo. Normalmente, essas empresas criam identidades somente na nuvem, o que não requer uma solução de identidade híbrida. As identidades somente em nuvem existem exclusivamente na nuvem e não estão associadas a identidades locais correspondentes.

## <a name="authentication-methods"></a>Métodos de autenticação
Quando a solução de identidade híbrida do Azure AD é seu novo plano de controle, a autenticação é a base do acesso à nuvem. Escolher o método de autenticação correto é uma primeira decisão crucial na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado usando Azure AD Connect, que também provisiona os usuários na nuvem.

Para escolher um método de autenticação, você precisa considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

O Azure AD dá suporte aos seguintes métodos de autenticação para soluções de identidade híbrida.

### <a name="cloud-authentication"></a>Autenticação na nuvem
Quando você escolhe esse método de autenticação, o Azure AD lida com o processo de entrada dos usuários. Junto com o SSO (logon único) contínuo, os usuários podem entrar em aplicativos de nuvem sem precisar reinserir suas credenciais. Com a autenticação na nuvem, você pode escolher entre duas opções:

**Sincronização de hash de senha Azure AD**. A maneira mais simples de habilitar a autenticação para objetos de diretório local no Azure AD. Os usuários podem usar o mesmo nome de usuário e senha que eles usam localmente sem precisar implantar nenhuma infraestrutura adicional. Algumas funcionalidades premium da Azure AD, como AD de Proteção de Identidade e Serviços de [Domínio Azure AD,](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)requerem sincronização de hash de senha, independentemente do método de autenticação que escolher.

> [!NOTE]
> As senhas nunca são armazenadas em texto não criptografado ou criptografadas com um algoritmo reversível no Azure AD. Para obter mais informações sobre o processo real de sincronização de hash de palavra-passe, consulte [Implementação de sincronização de hash com sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Autenticação de passagem de AD Azure.** Fornece uma validação de senha simples para os serviços de autenticação do Azure AD usando um agente de software que é executado em um ou mais servidores locais. Os servidores validam os usuários diretamente com seu Active Directory local, o que garante que a validação de senha não aconteça na nuvem.

As empresas com um requisito de segurança para impor imediatamente Estados de conta de usuário local, políticas de senha e horas de entrada podem usar esse método de autenticação. Para obter mais informações sobre o processo de autenticação pass-through real, consulte o utilizador de início de sessão com a [autenticação pass-through da AD Azure](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Autenticação federada
Quando você escolhe esse método de autenticação, o Azure AD transmite o processo de autenticação para um sistema de autenticação confiável separado, como o Serviços de Federação do Active Directory (AD FS) local (AD FS), para validar a senha do usuário.

O sistema de autenticação pode fornecer requisitos adicionais de autenticação avançada. Os exemplos são autenticação baseada em cartão inteligente ou autenticação multifator de terceiros. Para mais informações, consulte a Implementação de [Serviços da Federação de DiretórioActivo](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

A seção a seguir ajuda você a decidir qual método de autenticação é adequado para você usando uma árvore de decisão. Ele ajuda a determinar se deseja implantar a autenticação de nuvem ou federada para sua solução de identidade híbrida do Azure AD.

## <a name="decision-tree"></a>Árvore de decisões

![Árvore de decisão de autenticação do Azure AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Detalhes sobre perguntas de decisão:

1. O Azure AD pode manipular a entrada para usuários sem depender de componentes locais para verificar senhas.
2. O Azure AD pode entregar a entrada do usuário a um provedor de autenticação confiável, como o AD FS da Microsoft.
3. Se você precisar aplicar políticas de segurança Active Directory em nível de usuário, como conta expirada, conta desabilitada, senha expirada, conta bloqueada e horas de entrada em cada entrada de usuário, o Azure AD exigirá alguns componentes locais.
4. Recursos de entrada não suportados nativamente pelo Azure AD:
   * Entre usando cartões inteligentes ou certificados.
   * Entre usando o servidor MFA local.
   * Entre usando a solução de autenticação de terceiros.
   * Solução de autenticação local de vários sites.
5. A Proteção de Identidade Azure AD requer password Hash Sync independentemente do método de inscrição que escolher, para fornecer aos Utilizadores um relatório de *credenciais vazado.* As organizações poderão fazer failover para a sincronização de hash de senha se o seu método de entrada primário falhar e ele tiver sido configurado antes do evento de falha.

> [!NOTE]
> A Proteção de Identidade Azure AD requer licenças [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Considerações detalhadas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticação de nuvem: sincronização de hash de senha

* **Esforço.** A sincronização de hash de senha requer o mínimo de esforço em relação à implantação, manutenção e infraestrutura.  Esse nível de esforço normalmente se aplica a organizações que precisam apenas de seus usuários para entrar no Office 365, aplicativos SaaS e outros recursos baseados no Azure AD. Quando ativado, a sincronização de hash de senha faz parte do processo de sincronização de Azure AD Connect e é executada a cada dois minutos.

* **Experiência do utilizador.** Para melhorar a experiência de entrada dos usuários, implante o SSO contínuo com a sincronização de hash de senha. O SSO contínuo elimina avisos desnecessários quando os usuários estão conectados.

* **Cenários avançados.** Se as organizações escolherem, será possível usar informações de identidades com Azure AD Identity Protection relatórios com o Azure AD Premium P2. Um exemplo é o relatório de credenciais vazadas. O Windows Hello for Business tem [requisitos específicos quando utiliza](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)a sincronização de hash de palavra-passe . [O Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) requer sincronização de hash de palavra-passe para fornecer aos utilizadores as suas credenciais corporativas no domínio gerido.

    As organizações que necessitem de autenticação multifactor com sincronização de hash de palavra-passe devem utilizar a autenticação multifactor Azure AD ou [controlos personalizados de Acesso Condicional](../../active-directory/conditional-access/controls.md#custom-controls-preview). Essas organizações não podem usar métodos de autenticação multifator locais ou de terceiros que dependem da Federação.

> [!NOTE]
> O Acesso Condicional Azure AD requer licenças [Azure AD Premium P1.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Continuidade do negócio.** Usar a sincronização de hash de senha com autenticação de nuvem é altamente disponível como um serviço de nuvem que é dimensionado para todos os datacenters da Microsoft. Para garantir que a sincronização de hash de senha não fique inoperante por períodos estendidos, implante um segundo servidor de Azure AD Connect no modo de preparo em uma configuração em espera.

* **Considerações.** Atualmente, a sincronização de hash de senha não impõe imediatamente as alterações nos Estados de conta local. Nessa situação, um usuário tem acesso a aplicativos de nuvem até que o estado da conta de usuário seja sincronizado com o Azure AD. As organizações podem querer superar essa limitação executando um novo ciclo de sincronização depois que os administradores realizam atualizações em massa nos Estados da conta de usuário local. Um exemplo é a desabilitação de contas.

> [!NOTE]
> A senha expirada e os Estados de conta bloqueada não estão sincronizados no momento com o Azure AD com o Azure AD Connect. Quando alterar a palavra-passe de um utilizador e definir o *utilizador deve alterar a palavra-passe na próxima* bandeira de início de sessão, o hash de senha não será sincronizado com o Azure AD Connect até que o utilizador altere a sua palavra-passe.

Consulte a implementação da [sincronização de hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) de senha para passos de implementação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação de nuvem: autenticação de passagem  

* **Esforço.** Para autenticação de passagem, você precisa de um ou mais agentes leves (recomendamos três) instalados nos servidores existentes. Esses agentes devem ter acesso ao seu Active Directory Domain Services local, incluindo os controladores de domínio do AD local. Eles precisam de acesso de saída à Internet e ao acesso aos controladores de domínio. Por esse motivo, não há suporte para implantar os agentes em uma rede de perímetro.

    A autenticação de passagem requer acesso irrestrito à rede para controladores de domínio. Todo o tráfego de rede é criptografado e limitado às solicitações de autenticação. Para obter mais informações sobre este processo, consulte a segurança profunda [na](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) autenticação pass-through.

* **Experiência do utilizador.** Para melhorar a experiência de entrada dos usuários, implante o SSO contínuo com autenticação de passagem. O SSO contínuo elimina prompts desnecessários depois que os usuários entram.

* **Cenários avançados.** A autenticação de passagem impõe a política de conta local no momento da entrada. Por exemplo, o acesso é negado quando o estado de conta de um utilizador no local é desativado, bloqueado ou a sua [palavra-passe expira](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) ou a tentativa de logon cai fora das horas em que o utilizador é autorizado a iniciar sessão.

    As organizações que necessitem de autenticação multifactor com autenticação pass-through devem utilizar [controlos personalizados](../../active-directory/conditional-access/controls.md#custom-controls-preview)de autenticação Multifactor (MFA) ou Acesso Condicional. Essas organizações não podem usar um método de autenticação multifator local ou de terceiros que dependa da Federação. Os recursos avançados exigem que a sincronização de hash de senha seja implantada, independentemente de você escolher a autenticação de passagem. Um exemplo é o relatório de credenciais vazadas da proteção de identidade.

* **Continuidade do negócio.** Recomendamos que você implante dois agentes de autenticação de passagem extra. Esses extras são além do primeiro agente no servidor de Azure AD Connect. Essa implantação adicional garante a alta disponibilidade de solicitações de autenticação. Quando você tem três agentes implantados, um agente ainda pode falhar quando outro agente está inoperante para manutenção.

    Há outro benefício na implantação da sincronização de hash de senha, além da autenticação de passagem. Ele atua como um método de autenticação de backup quando o método de autenticação primária não está mais disponível.

* **Considerações.** Você pode usar a sincronização de hash de senha como um método de autenticação de backup para autenticação de passagem, quando os agentes não puderem validar as credenciais de um usuário devido a uma falha local significativa. O failover para a sincronização de hash de senha não ocorre automaticamente e você deve usar Azure AD Connect para alternar manualmente o método de logon.

    Para outras considerações sobre a autenticação pass-through, incluindo suporte de ID alternativo, consulte [frequentemente perguntas](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Consulte a implementação da [autenticação pass-through](../../active-directory/hybrid/how-to-connect-pta.md) para as etapas de implementação.

### <a name="federated-authentication"></a>Autenticação federada

* **Esforço.** Um sistema de autenticação federada se baseia em um sistema externo confiável para autenticar usuários. Algumas empresas desejam reutilizar seu investimento de sistema federado existente com a solução de identidade híbrida do Azure AD. A manutenção e o gerenciamento do sistema federado ficam fora do controle do Azure AD. Cabe à organização usando o sistema federado para garantir que ele seja implantado com segurança e possa lidar com a carga de autenticação.

* **Experiência do utilizador.** A experiência do usuário de autenticação federada depende da implementação dos recursos, da topologia e da configuração do farm de Federação. Algumas organizações precisam dessa flexibilidade para adaptar e configurar o acesso ao farm de Federação para atender aos seus requisitos de segurança. Por exemplo, é possível configurar usuários e dispositivos conectados internamente para conectar usuários automaticamente, sem solicitar credenciais. Essa configuração funciona porque elas já entraram em seus dispositivos. Se necessário, alguns recursos de segurança avançados tornam o processo de entrada dos usuários mais difícil.

* **Cenários avançados.** Uma solução de autenticação federada geralmente é necessária quando os clientes têm um requisito de autenticação que o Azure AD não dá suporte nativamente. Consulte informações detalhadas para o ajudar [a escolher a opção de inscrição certa](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Considere os seguintes requisitos comuns:

  * Autenticação que requer cartões inteligentes ou certificados.
  * Servidores MFA locais ou provedores multifatores de terceiros que exigem um provedor de identidade federada.
  * Autenticação usando soluções de autenticação de terceiros. Consulte a lista de compatibilidade da [Federação Azure.](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)
  * Assine o que requer um nome sAMAccount, por exemplo DOMAIN\username, em vez de um Nome Principal do Utilizador (UPN), por exemplo, user@domain.com.

* **Continuidade do negócio.** Os sistemas federados normalmente exigem uma matriz de balanceamento de carga de servidores, conhecida como um farm. Esse farm é configurado em uma rede interna e em uma topologia de rede de perímetro para garantir a alta disponibilidade para solicitações de autenticação.

    Implante a sincronização de hash de senha junto com a autenticação federada como um método de autenticação de backup quando o método de autenticação primária não estiver mais disponível. Um exemplo é quando os servidores locais não estão disponíveis. Algumas organizações empresariais de grande porte exigem uma solução de Federação para dar suporte a vários pontos de entrada da Internet configurados com DNS geográfico para solicitações de autenticação de baixa latência.

* **Considerações.** Normalmente, os sistemas federados exigem um investimento mais significativo na infraestrutura local. A maioria das organizações escolhe essa opção se já tiver um investimento de federação local. E se for um forte requisito de negócios para usar um provedor de identidade única. A Federação é mais complexa para operar e solucionar problemas em comparação com soluções de autenticação na nuvem.

Para um domínio não roteável que não pode ser verificado no Azure AD, você precisa de uma configuração extra para implementar a entrada de ID de usuário. Esse requisito é conhecido como suporte de ID de logon alternativo. Consulte configurar o [ID de login alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para obter limitações e requisitos. Se você optar por usar um provedor de autenticação multifator de terceiros com Federação, verifique se o provedor oferece suporte a WS-Trust para permitir que os dispositivos ingressem no Azure AD.

Consulte os [Servidores da Federação](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) de Implantação para os passos de implementação.

> [!NOTE]
> Ao implantar sua solução de identidade híbrida do Azure AD, você deve implementar uma das topologias com suporte do Azure AD Connect. Saiba mais sobre configurações suportadas e não suportadas em [Topologies para Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

Os diagramas a seguir descrevem os componentes de arquitetura de alto nível necessários para cada método de autenticação que você pode usar com sua solução de identidade híbrida do Azure AD. Eles fornecem uma visão geral para ajudá-lo a comparar as diferenças entre as soluções.

* Simplicidade de uma solução de sincronização de hash de senha:

    ![Identidade híbrida do Azure AD com sincronização de hash de senha](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Requisitos de agente de autenticação de passagem, usando dois agentes para redundância:

    ![Identidade híbrida do Azure AD com autenticação de passagem](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Componentes necessários para Federação em seu perímetro e rede interna da sua organização:

    ![Identidade híbrida do Azure AD com autenticação federada](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Comparando métodos

|Consideração|Sincronização de hash de senha + SSO contínuo|Autenticação de passagem + SSO contínuo|Federação com o AD FS|
|:-----|:-----|:-----|:-----|
|Onde ocorre a autenticação?|Na cloud|Na nuvem após uma troca de verificação de senha segura com o agente de autenticação local|Local|
|Quais são os requisitos do servidor local além do sistema de provisionamento: Azure AD Connect?|Nenhum|Um servidor para cada agente de autenticação adicional|Dois ou mais servidores AD FS<br><br>Dois ou mais servidores WAP na rede de perímetro/DMZ|
|Quais são os requisitos para Internet e rede local além do sistema de provisionamento?|Nenhum|[Acesso à Internet de saída](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) dos servidores que executam agentes de autenticação|[Acesso à Internet de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) a servidores WAP no perímetro<br><br>Acesso à rede de entrada para AD FS servidores de servidores WAP no perímetro<br><br>Balanceamento de carga de rede|
|Há um requisito de certificado SSL?|Não|Não|Sim|
|Existe uma solução de monitoramento de integridade?|Não é necessário|Estatuto de agente fornecido pelo Centro de [Administração do Diretório Ativo azure](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Os usuários obtêm logon único para recursos de nuvem de dispositivos ingressados no domínio na rede da empresa?|Sim com [SSO SSO Sem Emenda](../../active-directory/hybrid/how-to-connect-sso.md)|Sim com [SSO SSO Sem Emenda](../../active-directory/hybrid/how-to-connect-sso.md)|Sim|
|Quais tipos de entrada têm suporte?|UserPrincipalName + senha<br><br>Autenticação integrada no Windows utilizando [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Id de login alternativo](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + senha<br><br>Autenticação integrada no Windows utilizando [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Id de login alternativo](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + senha<br><br>sAMAccountName + senha<br><br>Autenticação integrada do Windows<br><br>[Autenticação de certificado e cartão inteligente](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Id de login alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Há suporte para o Windows Hello para empresas?|[Modelo de confiança chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modelo de confiança chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Requer o nível funcional do Windows Server 2016*|[Modelo de confiança chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de fidedignidade de certificado](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quais são as opções de autenticação multifator?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Servidor Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA de terceiros](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|
|Quais Estados de conta de usuário têm suporte?|Contas desabilitadas<br>(atraso de até 30 minutos)|Contas desabilitadas<br><br>Conta bloqueada<br><br>Conta expirada<br><br>Senha expirada<br><br>Horas de entrada|Contas desabilitadas<br><br>Conta bloqueada<br><br>Conta expirada<br><br>Senha expirada<br><br>Horas de entrada|
|Quais são as opções de acesso condicional?|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Regras de reclamação da AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Está bloqueando protocolos herdados com suporte?|[Sim](../../active-directory/conditional-access/conditions.md)|[Sim](../../active-directory/conditional-access/conditions.md)|[Sim](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Você pode personalizar o logotipo, a imagem e a descrição nas páginas de entrada?|[Sim, com Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim, com Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Quais cenários avançados têm suporte?|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Relatórios de credenciais vazados, com Azure AD Premium P2](../../active-directory/reports-monitoring/concept-risk-events.md)|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)|Sistema de autenticação de baixa latência multissite<br><br>[Bloqueio extranet AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integração com sistemas de identidade de terceiros](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> No momento, os controles personalizados no acesso condicional do Azure AD não dão suporte ao registro de dispositivo.

## <a name="recommendations"></a>Recomendações
Seu sistema de identidade garante o acesso de seus usuários a aplicativos de nuvem e os aplicativos de linha de negócios que você migra e disponibiliza na nuvem. Para manter os usuários autorizados produtivos e os atores ruins dos dados confidenciais de sua organização, a autenticação controla o acesso aos aplicativos.

Use ou habilite a sincronização de hash de senha para qualquer método de autenticação que você escolher, pelos seguintes motivos:

1. **Alta disponibilidade e recuperação de desastres.** A autenticação de passagem e a Federação dependem da infraestrutura local. Para a autenticação de passagem, a superfície local inclui o hardware de servidor e a rede que os agentes de autenticação de passagem exigem. Para Federação, a superfície local é ainda maior. Ele requer que os servidores em sua rede de perímetro sejam solicitações de autenticação de proxy e os servidores de Federação internos.

    Para evitar pontos únicos de falha, implante servidores redundantes. Em seguida, as solicitações de autenticação serão sempre atendidas se algum componente falhar. Tanto a autenticação de passagem quanto a Federação também dependem de controladores de domínio para responder às solicitações de autenticação, o que também pode falhar. Muitos desses componentes precisam de manutenção para permanecerem íntegros. As interrupções são mais prováveis quando a manutenção não é planejada e implementada corretamente. Evite interrupções usando a sincronização de hash de senha porque o serviço de autenticação de nuvem Microsoft Azure AD é dimensionado globalmente e está sempre disponível.

2. **No local, a sobrevivência da paralisação.**  As consequências de uma interrupção local devido a um ataque ou desastre na Cyber podem ser substanciais, variando de danos à marca de reputação a uma organização paralisaram não conseguem lidar com o ataque. Recentemente, muitas organizações eram vítimas de ataques de malware, incluindo ransomware direcionado, que fazia com que seus servidores locais estivessem inativos. Quando a Microsoft ajuda os clientes a lidar com esses tipos de ataques, ele vê duas categorias de organizações:

   * As organizações que ativaram anteriormente a sincronização de hash de senha alteraram o método de autenticação para usar a sincronização de hash de senha. Eles estavam online novamente em questão de horas. Ao usar o acesso ao email por meio do Office 365, eles trabalharam para resolver problemas e acessar outras cargas de trabalho baseadas em nuvem.

   * As organizações que anteriormente não habilitaram a sincronização de hash de senha tinham que recorrer a sistemas de email de consumidor externo não confiáveis para que as comunicações resolvam problemas. Nesses casos, levava semanas para restaurar sua infraestrutura de identidade local, antes que os usuários conseguissem entrar em aplicativos baseados em nuvem novamente.

3. **Proteção de identidade.** Uma das melhores maneiras de proteger os usuários na nuvem é Azure AD Identity Protection com o Azure AD Premium P2. A Microsoft examina continuamente a Internet em busca de listas de usuários e senhas que os atores ruins vendem e disponibilizam na Web escura. O Azure AD pode usar essas informações para verificar se qualquer um dos nomes de dados e senhas em sua organização estão comprometidos. Portanto, é essencial habilitar a sincronização de hash de senha, não importa qual método de autenticação você usa, se é uma autenticação federada ou de passagem. As credenciais vazadas são apresentadas como um relatório. Use essas informações para bloquear ou forçar os usuários a alterarem suas senhas quando tentarem entrar com senhas vazadas.

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implantar para dar suporte ao acesso a aplicativos de nuvem. Para atender a vários requisitos técnicos, de segurança e de negócios, as organizações podem escolher entre a sincronização de hash de senha, a autenticação de passagem e a Federação.

Considere cada método de autenticação. O esforço para implantar a solução e a experiência do usuário do processo de entrada atende aos seus requisitos de negócios? Avalie se sua organização precisa dos cenários avançados e dos recursos de continuidade de negócios de cada método de autenticação. Por fim, avalie as considerações de cada método de autenticação. Qualquer um deles impede que você implemente sua escolha?

## <a name="next-steps"></a>Passos seguintes

No mundo atual, as ameaças estão presentes 24 horas por dia e vêm de qualquer lugar. Implemente o método de autenticação correto, e isso reduzirá seus riscos de segurança e protegerá suas identidades.

[Inicie-se](../../active-directory/fundamentals/get-started-azure-ad.md) com a Azure AD e implemente a solução de autenticação certa para a sua organização.

Se está a pensar em migrar da autenticação federada para a nuvem, saiba mais sobre [a alteração do método de inscrição](../../active-directory/hybrid/plan-connect-user-signin.md). Para ajudá-lo a planear e implementar a migração, use estes planos de [implementação do projeto](https://aka.ms/deploymentplans) ou considere usar a nova funcionalidade Staged [Rollout](../../active-directory/hybrid/how-to-connect-staged-rollout.md) para migrar utilizadores federados para usar a autenticação em nuvem numa abordagem encenada.
