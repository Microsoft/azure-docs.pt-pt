---
title: Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure AD | Microsoft Docs
description: Este guia ajuda CEOs, CIOs, CISOs, arquitetos de identidade, arquitetos empresariais e tomadores de decisões de ti responsáveis por escolher um método de autenticação para sua solução de identidade híbrida do Azure AD em organizações de médio a grande porte.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 04/12/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e1910348b53cf147530037283442f453edf2694f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727552"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolha o método de autenticação certo para sua solução de identidade híbrida Azure Active Directory 

Este artigo inicia uma série de artigos que ajudam as organizações a implementar uma solução de identidade híbrida completa do Azure Active Directory (Azure AD). Essa solução foi descrita como a estrutura de [transformação digital de identidade híbrida](https://aka.ms/aadframework). Ele abrange os resultados de negócios e as metas às quais as organizações podem se concentrar para implementar uma solução de identidade híbrida robusta e segura. 

O primeiro resultado comercial da estrutura verifica os requisitos para que as organizações protejam o processo de autenticação quando os usuários acessam aplicativos de nuvem. A primeira meta de negócios no resultado comercial seguro da autenticação é a capacidade dos usuários de entrar em aplicativos de nuvem usando seus nomes de usuário e senhas locais. Esse processo de entrada e autenticação torna tudo na nuvem possível.

Escolher o método de autenticação correto é a primeira preocupação para as organizações que desejam mover seus aplicativos para a nuvem. Não tome essa decisão levemente, pelos seguintes motivos:

1. É a primeira decisão para uma organização que deseja migrar para a nuvem. 

2. O método de autenticação é um componente crítico da presença de uma organização na nuvem. Ele controla o acesso a todos os dados e recursos de nuvem.

3. É a base de todos os outros recursos avançados de segurança e experiência do usuário no Azure AD.

4. O método de autenticação é difícil de alterar depois de ser implementado.

Identidade é o novo plano de controle da segurança de ti. Portanto, a autenticação é a proteção de acesso de uma organização para o novo mundo da nuvem. As organizações precisam de um plano de controle de identidade que fortaleça sua segurança e mantenha seus aplicativos de nuvem protegidos contra intrusos.

### <a name="out-of-scope"></a>Fora do escopo
As organizações que não têm uma superfície de diretório local existente não são o foco deste artigo. Normalmente, essas empresas criam identidades somente na nuvem, o que não requer uma solução de identidade híbrida. As identidades somente em nuvem existem exclusivamente na nuvem e não estão associadas a identidades locais correspondentes.

## <a name="authentication-methods"></a>Métodos de autenticação
Quando a solução de identidade híbrida do Azure AD é seu novo plano de controle, a autenticação é a base do acesso à nuvem. Escolher o método de autenticação correto é uma primeira decisão crucial na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado usando Azure AD Connect, que também provisiona os usuários na nuvem.

Para escolher um método de autenticação, você precisa considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

O Azure AD dá suporte aos seguintes métodos de autenticação para soluções de identidade híbrida.

### <a name="cloud-authentication"></a>Autenticação na nuvem
Quando você escolhe esse método de autenticação, o Azure AD lida com o processo de entrada dos usuários. Junto com o SSO (logon único) contínuo, os usuários podem entrar em aplicativos de nuvem sem precisar reinserir suas credenciais. Com a autenticação na nuvem, você pode escolher entre duas opções: 

**Sincronização de hash de senha do Azure ad**. A maneira mais simples de habilitar a autenticação para objetos de diretório local no Azure AD. Os usuários podem usar o mesmo nome de usuário e senha que eles usam localmente sem precisar implantar nenhuma infraestrutura adicional. Alguns recursos premium do Azure AD, como a proteção de identidade e [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync), exigem a sincronização de hash de senha, não importa qual método de autenticação você escolher.

> [!NOTE] 
> As senhas nunca são armazenadas em texto não criptografado ou criptografadas com um algoritmo reversível no Azure AD. Para obter mais informações sobre o processo real de sincronização de hash de senha, consulte [implementar a sincronização de hash de senha com Azure ad Connect sincronização](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization). 

**Autenticação de passagem do Azure ad**. Fornece uma validação de senha simples para os serviços de autenticação do Azure AD usando um agente de software que é executado em um ou mais servidores locais. Os servidores validam os usuários diretamente com seu Active Directory local, o que garante que a validação de senha não aconteça na nuvem. 

As empresas com um requisito de segurança para impor imediatamente Estados de conta de usuário local, políticas de senha e horas de entrada podem usar esse método de autenticação. Para obter mais informações sobre o processo de autenticação de passagem real, consulte [entrada do usuário com autenticação de passagem do Azure ad](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta).

### <a name="federated-authentication"></a>Autenticação federada
Quando você escolhe esse método de autenticação, o Azure AD transmite o processo de autenticação para um sistema de autenticação confiável separado, como o Serviços de Federação do Active Directory (AD FS) local (AD FS), para validar a senha do usuário.

O sistema de autenticação pode fornecer requisitos adicionais de autenticação avançada. Os exemplos são autenticação baseada em cartão inteligente ou autenticação multifator de terceiros. Para obter mais informações, consulte Implantando [serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

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
5. Azure AD Identity Protection requer sincronização de hash de senha, independentemente do método de entrada escolhido, para fornecer ao relatório de *credenciais vazadas* . As organizações podem fazer failover para a sincronização de hash de senha se o seu método de entrada primário falhar e ele tiver sido configurado antes do evento de falha.

> [!NOTE]
> Azure AD Identity Protection exigir licenças do [Azure ad Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="detailed-considerations"></a>Considerações detalhadas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticação na nuvem: Sincronização de hash de palavra-passe

* **Esforço**. A sincronização de hash de senha requer o mínimo de esforço em relação à implantação, manutenção e infraestrutura.  Esse nível de esforço normalmente se aplica a organizações que precisam apenas de seus usuários para entrar no Office 365, aplicativos SaaS e outros recursos baseados no Azure AD. Quando ativado, a sincronização de hash de senha faz parte do processo de sincronização de Azure AD Connect e é executada a cada dois minutos.

* **Experiência do usuário**. Para melhorar a experiência de entrada dos usuários, implante o SSO contínuo com a sincronização de hash de senha. O SSO contínuo elimina avisos desnecessários quando os usuários estão conectados.

* **Cenários avançados**. Se as organizações escolherem, será possível usar informações de identidades com Azure AD Identity Protection relatórios com o Azure AD Premium P2. Um exemplo é o relatório de credenciais vazadas. O Windows Hello para empresas tem [requisitos específicos ao usar a sincronização de hash de senha](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync) exigir a sincronização de hash de senha para provisionar usuários com suas credenciais corporativas no domínio gerenciado.

    As organizações que exigem a autenticação multifator com a sincronização de hash de senha devem usar a autenticação multifator do Azure AD ou [controles personalizados de acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-preview). Essas organizações não podem usar métodos de autenticação multifator locais ou de terceiros que se baseiam na Federação.

> [!NOTE]
> O acesso condicional do Azure AD requer licenças [Azure ad Premium P1](https://azure.microsoft.com/pricing/details/active-directory/) .

* **Continuidade dos negócios**. Usar a sincronização de hash de senha com autenticação de nuvem é altamente disponível como um serviço de nuvem que é dimensionado para todos os datacenters da Microsoft. Para garantir que a sincronização de hash de senha não fique inoperante por períodos estendidos, implante um segundo servidor de Azure AD Connect no modo de preparo em uma configuração em espera.

* **Considerações**. Atualmente, a sincronização de hash de senha não impõe imediatamente as alterações nos Estados de conta local. Nessa situação, um usuário tem acesso a aplicativos de nuvem até que o estado da conta de usuário seja sincronizado com o Azure AD. As organizações podem querer superar essa limitação executando um novo ciclo de sincronização depois que os administradores realizam atualizações em massa nos Estados da conta de usuário local. Um exemplo é a desabilitação de contas.

> [!NOTE]
> A senha expirada e os Estados de conta bloqueada não estão sincronizados no momento com o Azure AD com o Azure AD Connect. Quando você altera a senha de um usuário e define o sinalizador o *usuário deve alterar a senha no próximo logon* , o hash de senha não será sincronizado com o Azure AD com o Azure ad Connect, até que o usuário altere sua senha.

Consulte Implementando a [sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) para obter as etapas de implantação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação na nuvem: Autenticação Pass-through  

* **Esforço**. Para autenticação de passagem, você precisa de um ou mais agentes leves (recomendamos três) instalados nos servidores existentes. Esses agentes devem ter acesso ao seu Active Directory Domain Services local, incluindo os controladores de domínio do AD local. Eles precisam de acesso de saída à Internet e ao acesso aos controladores de domínio. Por esse motivo, não há suporte para implantar os agentes em uma rede de perímetro. 

    A autenticação de passagem requer acesso irrestrito à rede para controladores de domínio. Todo o tráfego de rede é criptografado e limitado às solicitações de autenticação. Para obter mais informações sobre esse processo, consulte a aprofundamento de [segurança](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-security-deep-dive) sobre autenticação de passagem.

* **Experiência do usuário**. Para melhorar a experiência de entrada dos usuários, implante o SSO contínuo com autenticação de passagem. O SSO contínuo elimina prompts desnecessários depois que os usuários entram.

* **Cenários avançados**. A autenticação de passagem impõe a política de conta local no momento da entrada. Por exemplo, o acesso é negado quando o estado da conta de um usuário local é desabilitado, bloqueado ou a [senha expirou](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) ou fica fora das horas em que o usuário tem permissão para entrar. 

    As organizações que exigem autenticação multifator com autenticação de passagem devem usar a MFA (autenticação multifator do Azure) ou [controles personalizados de acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-preview). Essas organizações não podem usar um método de autenticação multifator local ou de terceiros que dependa da Federação. Os recursos avançados exigem que a sincronização de hash de senha seja implantada, independentemente de você escolher a autenticação de passagem. Um exemplo é o relatório de credenciais vazadas da proteção de identidade.

* **Continuidade dos negócios**. Recomendamos que você implante dois agentes de autenticação de passagem extra. Esses extras são além do primeiro agente no servidor de Azure AD Connect. Essa implantação adicional garante a alta disponibilidade de solicitações de autenticação. Quando você tem três agentes implantados, um agente ainda pode falhar quando outro agente está inoperante para manutenção. 

    Há outro benefício na implantação da sincronização de hash de senha, além da autenticação de passagem. Ele atua como um método de autenticação de backup quando o método de autenticação primária não está mais disponível.

* **Considerações**. Você pode usar a sincronização de hash de senha como um método de autenticação de backup para autenticação de passagem, quando os agentes não puderem validar as credenciais de um usuário devido a uma falha local significativa. O failover para a sincronização de hash de senha não ocorre automaticamente e você deve usar Azure AD Connect para alternar manualmente o método de logon. 

    Para obter outras considerações sobre a autenticação de passagem, incluindo o suporte à ID alternativa, consulte [perguntas frequentes](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq).

Consulte Implementando a [autenticação de passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) para obter as etapas de implantação.

### <a name="federated-authentication"></a>Autenticação federada

* **Esforço**. Um sistema de autenticação federada se baseia em um sistema externo confiável para autenticar usuários. Algumas empresas desejam reutilizar seu investimento de sistema federado existente com a solução de identidade híbrida do Azure AD. A manutenção e o gerenciamento do sistema federado ficam fora do controle do Azure AD. Cabe à organização usando o sistema federado para garantir que ele seja implantado com segurança e possa lidar com a carga de autenticação. 

* **Experiência do usuário**. A experiência do usuário de autenticação federada depende da implementação dos recursos, da topologia e da configuração do farm de Federação. Algumas organizações precisam dessa flexibilidade para adaptar e configurar o acesso ao farm de Federação para atender aos seus requisitos de segurança. Por exemplo, é possível configurar usuários e dispositivos conectados internamente para conectar usuários automaticamente, sem solicitar credenciais. Essa configuração funciona porque elas já entraram em seus dispositivos. Se necessário, alguns recursos de segurança avançados tornam o processo de entrada dos usuários mais difícil.

* **Cenários avançados**. Uma solução de autenticação federada geralmente é necessária quando os clientes têm um requisito de autenticação que o Azure AD não dá suporte nativamente. Consulte informações detalhadas para ajudá-lo [a escolher a opção de entrada correta](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Considere os seguintes requisitos comuns:

  * Autenticação que requer cartões inteligentes ou certificados.
  * Servidores MFA locais ou provedores multifatores de terceiros que exigem um provedor de identidade federada.
  * Autenticação usando soluções de autenticação de terceiros. Consulte a [lista de compatibilidade de Federação do Azure ad](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-compatibility).
  * Entrar que exige um sAMAccountName, por exemplo, domínio \ nomedeusuário, em vez de um UPN (nome principal do usuário), user@domain.compor exemplo,.

* **Continuidade dos negócios**. Os sistemas federados normalmente exigem uma matriz de balanceamento de carga de servidores, conhecida como um farm. Esse farm é configurado em uma rede interna e em uma topologia de rede de perímetro para garantir a alta disponibilidade para solicitações de autenticação.

    Implante a sincronização de hash de senha junto com a autenticação federada como um método de autenticação de backup quando o método de autenticação primária não estiver mais disponível. Um exemplo é quando os servidores locais não estão disponíveis. Algumas organizações empresariais de grande porte exigem uma solução de Federação para dar suporte a vários pontos de entrada da Internet configurados com DNS geográfico para solicitações de autenticação de baixa latência.

* **Considerações**. Normalmente, os sistemas federados exigem um investimento mais significativo na infraestrutura local. A maioria das organizações escolhe essa opção se já tiver um investimento de federação local. E se for um forte requisito de negócios para usar um provedor de identidade única. A Federação é mais complexa para operar e solucionar problemas em comparação com soluções de autenticação na nuvem.

Para um domínio não roteável que não pode ser verificado no Azure AD, você precisa de configuração extra para implementar a entrada de ID de usuário. Esse requisito é conhecido como suporte de ID de logon alternativo. Consulte Configurando a [ID de logon alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para obter as limitações e os requisitos. Se você optar por usar um provedor de autenticação multifator de terceiros com Federação, verifique se o provedor oferece suporte a WS-Trust para permitir que os dispositivos ingressem no Azure AD.

Consulte Implantando [servidores de Federação](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) para obter as etapas de implantação.

> [!NOTE] 
> Ao implantar sua solução de identidade híbrida do Azure AD, você deve implementar uma das topologias com suporte do Azure AD Connect. Saiba mais sobre configurações com e sem suporte em [topologias para Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

Os diagramas a seguir descrevem os componentes de arquitetura de alto nível necessários para cada método de autenticação que você pode usar com sua solução de identidade híbrida do Azure AD. Eles fornecem uma visão geral para ajudá-lo a comparar as diferenças entre as soluções.

* Simplicidade de uma solução de sincronização de hash de senha:

    ![Identidade híbrida do Azure AD com sincronização de hash de senha](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Requisitos de agente de autenticação de passagem, usando dois agentes para redundância:

    ![Identidade híbrida do Azure AD com autenticação de passagem](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Componentes necessários para Federação em seu perímetro e rede interna da sua organização:

    ![Identidade híbrida do Azure AD com autenticação federada](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Comparando métodos

|Reflexão|Sincronização de hash de senha + SSO contínuo|Autenticação de passagem + SSO contínuo|Federação com o AD FS|
|:-----|:-----|:-----|:-----|
|Onde ocorre a autenticação?|Na cloud|Na nuvem após uma troca de verificação de senha segura com o agente de autenticação local|No local|
|Quais são os requisitos de servidor local além do sistema de provisionamento: Azure AD Connect?|Nenhum|Um servidor para cada agente de autenticação adicional|Dois ou mais servidores AD FS<br><br>Dois ou mais servidores WAP na rede de perímetro/DMZ|
|Quais são os requisitos para Internet e rede local além do sistema de provisionamento?|Nenhum|[Acesso de Internet de saída](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) dos servidores que executam agentes de autenticação|[Acesso de Internet de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) a servidores WAP no perímetro<br><br>Acesso à rede de entrada para AD FS servidores de servidores WAP no perímetro<br><br>Balanceamento de carga de rede|
|Há um requisito de certificado SSL?|Não|Não|Sim|
|Existe uma solução de monitoramento de integridade?|Não necessário|Status do agente fornecido pelo [centro de administração Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|Os usuários obtêm logon único para recursos de nuvem de dispositivos ingressados no domínio na rede da empresa?|Sim com o [SSO contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Sim com o [SSO contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Sim|
|Quais tipos de entrada têm suporte?|UserPrincipalName + senha<br><br>Autenticação integrada do Windows usando [SSO contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[ID de logon alternativa](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)|UserPrincipalName + senha<br><br>Autenticação integrada do Windows usando [SSO contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[ID de logon alternativa](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq)|UserPrincipalName + senha<br><br>sAMAccountName + senha<br><br>Autenticação Integrada do Windows<br><br>[Certificado e autenticação de cartão inteligente](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[ID de logon alternativa](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Há suporte para o Windows Hello para empresas?|[Modelo de confiança de chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modelo de confiança de chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Requer o nível funcional de domínio do Windows Server 2016*|[Modelo de confiança de chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confiança de certificado](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quais são as opções de autenticação multifator?|[MFA do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Controles personalizados com acesso condicional *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|[MFA do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Controles personalizados com acesso condicional *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|[MFA do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Servidor do Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA de terceiros](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controles personalizados com acesso condicional *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|
|Quais Estados de conta de usuário têm suporte?|Contas desabilitadas<br>(atraso de até 30 minutos)|Contas desabilitadas<br><br>Conta bloqueada<br><br>Conta expirada<br><br>Palavra-passe expirada<br><br>Horas de entrada|Contas desabilitadas<br><br>Conta bloqueada<br><br>Conta expirada<br><br>Palavra-passe expirada<br><br>Horas de entrada|
|Quais são as opções de acesso condicional?|[Acesso condicional do Azure AD, com Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acesso condicional do Azure AD, com Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acesso condicional do Azure AD, com Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Regras de declaração de AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Está bloqueando protocolos herdados com suporte?|[Sim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)|[Sim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)|[Sim](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Você pode personalizar o logotipo, a imagem e a descrição nas páginas de entrada?|[Sim, com Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Sim, com Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Sim](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Quais cenários avançados têm suporte?|[Bloqueio de senha inteligente](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Relatórios de credenciais vazadas, com Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Bloqueio de senha inteligente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Sistema de autenticação de baixa latência multissite<br><br>[AD FS o bloqueio de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integração com sistemas de identidade de terceiros](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

> [!NOTE] 
> No momento, os controles personalizados no acesso condicional do Azure AD não dão suporte ao registro de dispositivo.

## <a name="recommendations"></a>Recomendações
Seu sistema de identidade garante o acesso de seus usuários a aplicativos de nuvem e os aplicativos de linha de negócios que você migra e disponibiliza na nuvem. Para manter os usuários autorizados produtivos e os atores ruins dos dados confidenciais de sua organização, a autenticação controla o acesso aos aplicativos.

Use ou habilite a sincronização de hash de senha para qualquer método de autenticação que você escolher, pelos seguintes motivos:

1. **Alta disponibilidade e recuperação de desastres**. A autenticação de passagem e a Federação dependem da infraestrutura local. Para a autenticação de passagem, a superfície local inclui o hardware de servidor e a rede que os agentes de autenticação de passagem exigem. Para Federação, a superfície local é ainda maior. Ele requer que os servidores em sua rede de perímetro sejam solicitações de autenticação de proxy e os servidores de Federação internos. 

    Para evitar pontos únicos de falhas, implante servidores redundantes. Em seguida, as solicitações de autenticação serão sempre atendidas se algum componente falhar. Tanto a autenticação de passagem quanto a Federação também dependem de controladores de domínio para responder às solicitações de autenticação, o que também pode falhar. Muitos desses componentes precisam de manutenção para permanecerem íntegros. As interrupções são mais prováveis quando a manutenção não é planejada e implementada corretamente. Evite interrupções usando a sincronização de hash de senha porque o serviço de autenticação de nuvem Microsoft Azure AD é dimensionado globalmente e está sempre disponível.

2. **Sobrevivência na interrupção local**.  As consequências de uma interrupção local devido a um ataque ou desastre na Cyber podem ser substanciais, variando de danos à marca de reputação a uma organização paralisaram não conseguem lidar com o ataque. Recentemente, muitas organizações eram vítimas de ataques de malware, incluindo ransomware direcionado, que fazia com que seus servidores locais estivessem inativos. Quando a Microsoft ajuda os clientes a lidar com esses tipos de ataques, ele vê duas categorias de organizações:

   * As organizações que ativaram anteriormente a sincronização de hash de senha alteraram o método de autenticação para usar a sincronização de hash de senha. Eles estavam online novamente em questão de horas. Ao usar o acesso ao email por meio do Office 365, eles trabalharam para resolver problemas e acessar outras cargas de trabalho baseadas em nuvem.

   * As organizações que anteriormente não habilitaram a sincronização de hash de senha tinham que recorrer a sistemas de email de consumidor externo não confiáveis para que as comunicações resolvam problemas. Nesses casos, levava semanas para restaurar sua infraestrutura de identidade local, antes que os usuários conseguissem entrar em aplicativos baseados em nuvem novamente.

3. **Proteção de identidade**. Uma das melhores maneiras de proteger os usuários na nuvem é Azure AD Identity Protection com o Azure AD Premium P2. A Microsoft examina continuamente a Internet em busca de listas de usuários e senhas que os atores ruins vendem e disponibilizam na Web escura. O Azure AD pode usar essas informações para verificar se qualquer um dos nomes de dados e senhas em sua organização estão comprometidos. Portanto, é essencial habilitar a sincronização de hash de senha, independentemente do método de autenticação usado, seja ela uma autenticação federada ou de passagem. As credenciais vazadas são apresentadas como um relatório. Use essas informações para bloquear ou forçar os usuários a alterarem suas senhas quando tentarem entrar com senhas vazadas.

Por fim, de acordo com a [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), a Microsoft tem o conjunto mais completo de funções de gerenciamento de identidade e acesso. A Microsoft manipula [450.000.000.000 solicitações de autenticação](https://www.microsoft.com/en-us/security/intelligence-report) todos os meses para fornecer acesso a milhares de aplicativos SaaS, como o Office 365, de praticamente qualquer dispositivo. 

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implantar para dar suporte ao acesso a aplicativos de nuvem. Para atender a vários requisitos técnicos, de segurança e de negócios, as organizações podem escolher entre a sincronização de hash de senha, a autenticação de passagem e a Federação. 

Considere cada método de autenticação. O esforço para implantar a solução e a experiência do usuário do processo de entrada atende a seus requisitos de negócios? Avalie se sua organização precisa dos cenários avançados e dos recursos de continuidade de negócios de cada método de autenticação. Por fim, avalie as considerações de cada método de autenticação. Qualquer um deles impede que você implemente sua escolha?

## <a name="next-steps"></a>Passos Seguintes

No mundo atual, as ameaças estão presentes 24 horas por dia e vêm de qualquer lugar. Implemente o método de autenticação correto, e isso reduzirá seus riscos de segurança e protegerá suas identidades.

[](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) Comece a usar o Azure AD e implante a solução de autenticação correta para sua organização.

Se você estiver pensando em migrar da autenticação federada para a nuvem, saiba mais sobre como [alterar o método de entrada](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Para ajudá-lo a planejar e implementar a migração, use [esses planos de implantação de projeto](https://aka.ms/deploymentplans).
