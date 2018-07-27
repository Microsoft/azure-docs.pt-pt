---
title: Escolha o método de autenticação correta para sua solução de identidade híbrida do Azure AD | Documentos da Microsoft
description: Este guia ajuda CEOs, Diretores de informática, officers, Arquitetos de identidade do diretor, Enterprise Architects e segurança e tomadores de decisão de TI responsáveis por escolher um método de autenticação para a sua solução de identidade híbrida do Azure AD em organizações de médio a grande porte.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 7acf18c4624373dff8994a1996e9082770b90270
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283692"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolha o método de autenticação correta para sua solução de identidade híbrida do Azure Active Directory 

Este artigo começa uma série de artigos que ajudam as organizações a implementar uma solução completa de identidade de híbrida do Azure Active Directory (Azure AD). Esta solução foi descrita como o [arquitetura de transformação Digital identidade híbrida](https://aka.ms/aadframework). Ele aborda os resultados de negócios e organizações de objetivos podem se concentrar em para implementar uma solução de identidade híbrida robusto e seguro. 

O primeiro resultado do negócio do framework descreve os requisitos para as organizações a proteger o processo de autenticação quando os utilizadores acedem a aplicações na cloud. A primeira meta de negócios no resultado do negócio seguro de autenticação é a capacidade dos próprios utilizadores para iniciar sessão em aplicações na cloud com seus nomes de utilizador no local e as palavras-passe. Este processo de início de sessão para e a forma como autenticar os utilizadores possibilitam tudo na cloud.

Escolher o método de autenticação correto é a principal preocupação para as organizações que desejam mover seus aplicativos para a cloud. Não tome esta decisão apenas superficialmente, pelos seguintes motivos:

1. É a primeira decisão para uma organização que pretenda mover para a cloud. 

2. O método de autenticação é um componente fundamental da presença de uma organização na cloud. Ele controla o acesso a todos os dados na cloud e recursos.

3. É a base de todos os outros segurança avançada e recursos da experiência do utilizador no Azure AD.

4. O método de autenticação é difícil alterar após esta ser implementada.

A identidade é o novo plano de controlo de segurança de TI. Por isso, a autenticação é a proteção de acesso de uma organização para o novo mundo de cloud. As organizações precisam de um plano de controlo de identidade que reforça a segurança dos mesmos e mantém as suas aplicações na cloud seguro contra invasores.

### <a name="out-of-scope"></a>Fora do âmbito
Organizações que não tenham uma pegada de diretório no local existentes não são o foco deste artigo. Normalmente, essas empresas criam identidades apenas na cloud, que não exige uma solução de identidade híbrida. Identidades apenas na cloud existem apenas na nuvem e não estão associadas a identidades de locais correspondentes.

## <a name="authentication-methods"></a>Métodos de autenticação
Quando a solução de identidade híbrida do Azure AD é seu novo plano de controlo, a autenticação é a base de acesso à nuvem. Escolher o método de autenticação correta é uma primeira decisão fundamental na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado através do Azure AD Connect, que também Aprovisiona utilizadores na cloud.

Para escolher um método de autenticação, que é preciso considerar o tempo, a infraestrutura existente, a complexidade e o custo da implementação à sua escolha. Esses fatores são diferentes para cada organização e podem ser alterados ao longo do tempo. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

O Azure AD suporta os seguintes métodos de autenticação para as soluções de identidade híbrida.

### <a name="cloud-authentication"></a>Autenticação na nuvem
Ao escolher este método de autenticação, do Azure AD processa o processo de início de sessão dos utilizadores. Juntamente com o simples início de sessão único (SSO), os utilizadores podem iniciar sessão aplicações na cloud sem ter de reintroduzir as suas credenciais. Com a autenticação na nuvem, pode escolher entre duas opções: 

**Sincronização de hash de palavra-passe do Azure AD**. A forma mais simples para ativar a autenticação para objetos de diretório no local no Azure AD. Os utilizadores podem utilizar o mesmo nome de utilizador e palavra-passe que utilizam no local sem ter de implementar qualquer infraestrutura adicional. Alguns recursos premium do Azure AD, como o Identity Protection, requerem a sincronização de hash de palavra-passe para não importa qual o método de autenticação que escolher.

> [!NOTE] 
> Palavras-passe nunca são armazenadas em texto não encriptado ou criptografadas com um algoritmo reversível no Azure AD. Para obter mais informações sobre o processo real de sincronização de hash de palavra-passe, consulte [implementar a sincronização de hash de palavra-passe com o Azure AD Connect sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Autenticação do Azure AD pass-through**. Fornece uma validação de palavra-passe simples para serviços de autenticação do Azure AD através da utilização de um agente de software que é executado num ou mais servidores no local. Os servidores de validam os utilizadores diretamente com o seu diretório de Active Directory no local, que garante que a validação da palavra-passe não acontece na cloud. 

Estados, políticas de palavra-passe de contas de empresas com um requisito de segurança para impor imediatamente o utilizador no local e horas de início de sessão, poderão utilizar este método de autenticação. Para obter mais informações sobre o processo de autenticação pass-through real, consulte [utilizador inicie sessão com a autenticação pass-through do Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Autenticação federada
Ao escolher este método de autenticação, mãos do Azure AD desativado o processo de autenticação para um sistema de autenticação confiável separado, como no local Active Directory Federation Services (AD FS), para validar a palavra-passe do utilizador.

O sistema de autenticação pode fornecer os requisitos de autenticação avançadas adicionais. Os exemplos são a autenticação baseada em smart card ou de terceiros a autenticação multifator. Para obter mais informações, consulte [Implantando serviços de Federação do Active Directory](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

A secção seguinte ajuda-o a decidir que método de autenticação é adequado para si através de uma árvore de decisão. Ele ajuda a determinar se pretende implementar na cloud ou autenticação federada para a sua solução de identidade híbrida do Azure AD.

## <a name="decision-tree"></a>Árvore de decisões

![Árvore de decisão de autenticação do Azure AD](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Considerações detalhadas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticação na nuvem: sincronização de hash de palavra-passe

* **Esforço**. Sincronização de hash de palavra-passe requer o mínimo de esforço em relação à implantação, manutenção e infra-estrutura.  Este nível de esforço aplica-se, normalmente, as organizações que precisam apenas aos utilizadores iniciar sessão no Office 365, aplicações SaaS e outros recursos do Azure baseada no AD. Quando ativada, a sincronização de hash de palavra-passe é parte do processo de sincronização do Azure AD Connect e é executada a cada dois minutos.

* **Experiência do usuário**. Para melhorar a experiência de início de sessão dos utilizadores, implemente o SSO totalmente integrado com a sincronização de hash de palavra-passe. SSO totalmente integrado elimina prompts desnecessários quando os utilizadores com sessão iniciados.

* **Cenários avançados**. Se a organizações optam por, é possível utilizar as informações de identidades com relatórios do Azure AD Identity Protection. Um exemplo é como o relatório fugas de credenciais. Windows Hello para empresas é outra opção que tenha [requisitos específicos, ao utilizar a sincronização de hash de palavra-passe](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    As organizações que requerem a autenticação multifator com sincronização de hash de palavra-passe tem de utilizar a autenticação multifator do Azure AD. Essas organizações não é possível utilizar os métodos de autenticação multifator de terceiros ou no local.

* **Continuidade do negócio**. Utilizar a sincronização de hash de palavra-passe com autenticação em nuvem é de elevada disponibilidade como um serviço cloud que pode ser dimensionada para todos os datacenters da Microsoft. Para garantir que a sincronização de hash de palavra-passe não descer por períodos prolongados, implemente um segundo servidor do Azure AD Connect no modo numa configuração em modo de espera de teste.

* **Considerações sobre**. Atualmente, a sincronização de hash de palavra-passe não a impor imediatamente as alterações nos Estados de conta no local. Nesta situação, um utilizador tem acesso a aplicações na cloud até que o estado da conta de utilizador está sincronizado com o Azure AD. As organizações podem querer superar essa limitação ao executar um novo ciclo de sincronização, depois dos administradores em massa atualizações aos Estados de conta de utilizador no local. Um exemplo é desabilitar contas.

> [!NOTE]
> A palavra-passe expirou e Estados de saída bloqueado do conta atualmente não estão sincronizados com o Azure AD com o Azure AD Connect. 

Consulte a [implementar a sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) para obter os passos de implementação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação na nuvem: autenticação pass-through  

* **Esforço**. Para a autenticação pass-through, terá de uma ou mais (Recomendamos três) leves agentes instalados nos servidores existentes. Estes agentes tem de ter acesso ao seu local Active Directory Domain Services, incluindo no local controladores de domínio do AD. Eles precisam de acesso de saída à Internet e o acesso aos controladores de domínio. Por esse motivo, não é suportada para implantar os agentes numa rede de perímetro. 

    Autenticação pass-through requer acesso à rede irrestrita para controladores de domínio. Todo o tráfego de rede é encriptado e é limitado a pedidos de autenticação. Para obter mais informações sobre este processo, consulte a [detalhada da segurança](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) autenticação pass-through.

* **Experiência do usuário**. Para melhorar a experiência de início de sessão dos utilizadores, implemente o SSO totalmente integrado com a autenticação pass-through. SSO totalmente integrado elimina solicitações desnecessárias depois dos utilizadores iniciam sessão.

* **Cenários avançados**. Autenticação pass-through impõe a política de conta no local ao tempo de início de sessão. Por exemplo, o acesso é negado quando a conta de um utilizador no local, estado estiver desativado, bloqueada, ou [palavra-passe expirada](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) ou está fora das horas quando o utilizador tem permissão para iniciar sessão. 

    As organizações que requerem a autenticação multifator com a autenticação pass-through tem de utilizar Azure multi-factor Authentication (MFA). Essas organizações não é possível utilizar um método de autenticação multifator de terceiros ou no local. Funcionalidades avançadas exigem que a sincronização de hash de palavra-passe é implementada independentemente de escolher a autenticação pass-through. Um exemplo é o relatório fugas de credenciais de proteção de identidade.

* **Continuidade do negócio**. Recomendamos que implemente dois agentes de autenticação extra pass-through. São esses extras além do primeiro agente no servidor do Azure AD Connect. Esta implementação adicional garante elevada disponibilidade dos pedidos de autenticação. Quando tem três agentes implementados, um agente ainda pode falhar quando outro agente está inativo para manutenção. 

    Há um outro benefício para a implantação de sincronização de hash de palavra-passe para além da autenticação pass-through. Ele atua como um método de cópia de segurança de autenticação quando o método de autenticação principal já não está disponível.

* **Considerações sobre**. Pode usar a sincronização de hash de palavra-passe como um método de autenticação de cópia de segurança para a autenticação pass-through, quando os agentes não é possível validar as credenciais de um utilizador devido a uma falha significativa no local. Ativação pós-falha para a sincronização de hash de palavra-passe não ocorre automaticamente e tem de utilizar o Azure AD Connect para mudar o método de início de sessão manualmente. 

    Para oferecer suporte a outras considerações sobre a autenticação pass-through, incluindo o ID alternativo, consulte [perguntas mais frequentes sobre](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Consulte a [implementando a autenticação pass-through](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) para obter os passos de implementação.

### <a name="federated-authentication"></a>Autenticação federada

* **Esforço**. Um sistema de autenticação federada se baseia num sistema externo fidedigno para autenticar os utilizadores. Algumas empresas querem reutilizar seus investimentos existentes em sistema Federado com a solução de identidade híbrida do Azure AD. A manutenção e a gestão do sistema federado cair fora do controlo do Azure AD. Cabe à organização ao utilizar o sistema federado para certificar-se de que ele é implementado de forma segura e pode processar a carga de autenticação. 

* **Experiência do usuário**. A experiência de utilizador de autenticação federada depende a implementação de recursos, a topologia e a configuração de farm de Federação. Algumas organizações precisam essa flexibilidade para se adaptar e configurar o acesso ao farm de Federação para atender às suas necessidades de segurança. Por exemplo, é possível configurar dispositivos e utilizadores ligados internamente para iniciar sessão dos utilizadores automaticamente, sem pedir aos mesmos credenciais. Esta configuração funciona porque eles já iniciou sessão nos seus dispositivos. Se necessário, algumas funcionalidades de segurança avançada dificultam processo de início de sessão dos utilizadores.

* **Cenários avançados**. Uma solução de autenticação federada é geralmente necessária quando os clientes têm um requisito de autenticação do Azure AD não suporta nativamente. Ver informações detalhadas para ajudá-lo [escolha a opção neste início de sessão](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Considere os seguintes requisitos comuns:

    * Autenticação que necessita de smart cards ou certificados.
    * Servidores MFA no local ou fornecedores de terceiros multifator.
    * Autenticação através de soluções de autenticação de terceiros. Consulte a [lista de compatibilidades de Federação do Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Início de sessão que requer uma sAMAccountName, por exemplo, domínio \ nomedeutilizador, em vez de um utilizador nome Principal (UPN), por exemplo, user@domain.com.

* **Continuidade do negócio**. Sistemas federados normalmente exigem uma matriz com balanceamento de carga dos servidores, conhecido como um farm. Este farm está configurado numa rede interna e a topologia de rede de perímetro para garantir a elevada disponibilidade para pedidos de autenticação.

    Implemente a sincronização de hash de palavra-passe, juntamente com a autenticação federada como um método de cópia de segurança de autenticação quando o método de autenticação principal já não está disponível. Um exemplo é quando os servidores no local não estão disponíveis. Algumas organizações de grande empresa necessitam de uma solução de Federação para dar suporte a vários pontos de entrada de Internet configurados com geo-DNS para pedidos de autenticação de baixa latência.

* **Considerações sobre**. Sistemas federados normalmente requerem um investimento significativo mais na infraestrutura no local. A maioria das organizações Escolha esta opção se já tiver um investimento de Federação no local. E se é um requisito de negócio forte para utilizar um fornecedor de identidade única. Federação é mais complexo para operar e solucionar problemas em comparação com soluções de autenticação na cloud.

Para um domínio de nonroutable que não pode ser verificado no Azure AD, terá de uma configuração adicional para implementar o início de sessão de ID de utilizador. Este requisito é conhecido como início de sessão alternativo suporte de ID. Ver [configurar o ID de início de sessão alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para requisitos e limitações. Se optar por utilizar um fornecedor de autenticação multifator de terceiros com a Federação, certifique-se de que o fornecedor suporta o WS-Trust para permitir que os dispositivos associados ao Azure AD.

Consulte a [implementar servidores de Federação](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) para obter os passos de implementação.

> [!NOTE] 
> Ao implementar sua solução de identidade híbrida do Azure AD, tem de implementar uma das topologias com suporte do Azure AD Connect. Saber mais sobre suportam e configurações em não suportadas [topologias do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

Os diagramas seguintes descrevem os componentes de arquitetura de alto nível necessários para cada método de autenticação que pode utilizar com a sua solução de identidade híbrida do Azure AD. Eles fornecem uma visão geral para o ajudar a comparar as diferenças entre as soluções.

* Simplicidade de uma solução de sincronização de hash de palavra-passe:

    ![Identidade de híbrida do Azure AD com a sincronização de hash de palavra-passe](media/azure-ad/azure-ad-authn-image2.png)

* Requisitos de agente de autenticação pass-through:

    ![Identidade de híbrida do Azure AD com a autenticação pass-through](media/azure-ad/azure-ad-authn-image3.png)

* Componentes necessários para a Federação no seu perímetro e a rede interna da sua organização:

    ![Identidade de híbrida do Azure AD com a autenticação federada](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Comparando métodos

|Consideração|Sincronização de hash de palavra-passe + SSO totalmente integrado|Autenticação pass-through + SSO totalmente integrado|Federação com o AD FS|
|:-----|:-----|:-----|:-----|
|Onde acontecer autenticação?|Na cloud|Na cloud após uma troca de verificação de palavra-passe segura com o agente de autenticação no local|Local|
|Quais são os requisitos de servidor no local para além do sistema de provisionamento: o Azure AD Connect?|Nenhuma|Um servidor para cada agente de autenticação adicional|Dois ou mais servidores do AD FS<br><br>Dois ou mais servidores do WAP na rede de perímetro/rede de Perímetro|
|Quais são os requisitos para a Internet no local e o funcionamento em rede para além do sistema de provisionamento?|Nenhuma|[Acesso de Internet de saída](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) dos servidores a executar agentes de autenticação|[Acesso à Internet de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) para os servidores WAP no perímetro<br><br>Acesso de rede de entrada para servidores do AD FS dos servidores WAP no perímetro<br><br>Balanceamento de carga de rede|
|Existe um requisito do certificado SSL?|Não|Não|Sim|
|Existe um solução de monitorização de estado de funcionamento?|Não necessário|Estado do agente fornecido pelo [Centro de administração do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Os utilizadores proceder para início de sessão único a recursos na cloud a partir de dispositivos associados a domínios dentro da rede da empresa?|Sim com [SSO totalmente integrado](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)|Sim com [SSO totalmente integrado](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)|Sim|
|Que tipos de início de sessão são suportados?|UserPrincipalName + palavra-passe<br><br>Autenticação integrada do Windows utilizando [SSO totalmente integrado](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[ID de início de sessão alternativo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + palavra-passe<br><br>Autenticação integrada do Windows utilizando [SSO totalmente integrado](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[ID de início de sessão alternativo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + palavra-passe<br><br>sAMAccountName + palavra-passe<br><br>Autenticação Integrada do Windows<br><br>[Autenticação de certificados e smart card](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[ID de início de sessão alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|É o Windows Hello para empresas suportadas?|[Modelo de confiança de chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confiança de certificado com o Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modelo de confiança de chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confiança de certificado com o Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modelo de confiança de chave](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confiança de certificado](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quais são as opções de autenticação multifator?|[MFA do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/)|[MFA do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/)|[MFA do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Servidor MFA do Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA de terceiros](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Os Estados de conta de utilizador são suportados?|Contas desativadas<br>(até o atraso de 30 minutos)|Contas desativadas<br><br>Conta bloqueada<br><br>Palavra-passe expirada<br><br>Horas de início de sessão|Contas desativadas<br><br>Conta bloqueada<br><br>Palavra-passe expirada<br><br>Horas de início de sessão|
|Quais são as opções de acesso condicional?|[Acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[As regras de afirmação de AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Está a bloquear protocolos legados suportados?|[Sim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Sim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Sim](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Pode personalizar o logótipo, a imagem e a descrição nas páginas de início de sessão?|[Sim, com o Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Sim, com o Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Sim](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Quais cenários avançados são suportados?|[Bloqueio inteligente da palavra-passe](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Fuga de relatórios de credenciais](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Bloqueio inteligente da palavra-passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Sistema de autenticação de baixa latência múltiplos sites<br><br>[Bloqueio de extranet do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integração com sistemas de identidade de terceiros](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Recomendações
O sistema de identidade garante o acesso dos seus utilizadores para aplicações na cloud e as aplicações de linha de negócio que migrar e disponibilizar na nuvem. Para manter os utilizadores autorizados produtivos e mal-intencionadas fora de dados confidenciais da sua organização, autenticação controla o acesso às aplicações.

Utilize ou ativar a sincronização de hash de palavra-passe para qualquer método de autenticação que escolher, pelos seguintes motivos:

1. **Elevada disponibilidade e recuperação após desastre**. Autenticação pass-through e Federação baseiam-se na infraestrutura no local. Para a autenticação pass-through, os requisitos de espaço no local incluem o hardware de servidor e a autenticação pass-through de rede necessitam de agentes. Para a Federação, os requisitos de espaço no local é ainda maiores. Ele requer servidores na sua rede de perímetro para pedidos de autenticação de proxy e os servidores de Federação interno. 

    Para evitar pontos únicos de falhas, implemente servidores redundantes. Em seguida, pedidos de autenticação serão sempre ser atendidos se falhar de qualquer componente. Autenticação pass-through e Federação também contam com controladores de domínio para responder a pedidos de autenticação, que também podem falhar. Muitos destes componentes tem de manutenção para se manter saudável. As falhas são mais provável que, quando a manutenção não planeada e implementada corretamente. Evite interrupções ao utilizar a sincronização de hash de palavra-passe porque o serviço de autenticação de cloud do Microsoft Azure AD dimensiona globalmente e está sempre disponível.

2. **No local sobrevivência de indisponibilidade**.  As conseqüências de uma falha no local devido a uma ataques informáticos ou um desastre podem ser substanciais, variando de danos de marca reputational a uma organização paralisaram não é possível lidar com o ataque. Recentemente, muitas organizações foram vítimas do ataques de software maligno, incluindo o ransomware destino, o que causou a seus servidores no local descer. Quando a Microsoft ajuda os clientes a lidar com esses tipos de ataques, o que ele vê duas categorias de organizações:

   * As organizações que anteriormente ativado a sincronização de hash de palavra-passe alterada seu método de autenticação a utilizar a sincronização de hash de palavra-passe. Eles foram novamente online dentro de uma questão de horas. Ao utilizar o acesso ao e-mail através do Office 365, eles trabalharam para resolver problemas e aceder a outras cargas de trabalho com base na cloud.

   * As organizações que anteriormente não tiver ativado a sincronização de hash de palavra-passe, tinham que recorrer a sistemas de e-mail de consumidor externos não fidedignos para comunicações e resolução de problemas. Nesses casos, que o necessário semanas ou mais para ser novamente em funcionamento.

3. **Proteção de identidade**. Uma das melhores maneiras de proteger os usuários na cloud é o Azure AD Identity Protection. Microsoft analisa continuamente a Internet para o utilizador e palavra-passe apresenta uma lista que pessoas mal-intencionadas vender e disponibilizar na web escuro. Azure AD pode utilizar estas informações para verificar se qualquer um dos nomes de utilizador e palavras-passe na sua organização sejam comprometidos. Portanto, é fundamental para ativar a sincronização de hash de palavra-passe, não importa qual método de autenticação a utilizar, se de que está a ser federado ou a autenticação pass-through. Fuga de credenciais é apresentada como um relatório. Utilize estas informações para bloquear ou forçar os usuários para alterar as palavras-passe quando o utilizador tentar iniciar sessão com palavras-passe de fugas.

Por último, acordo com a [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), a Microsoft tem o conjunto mais completo de funções de gestão de identidades e acessos. Identificadores de Microsoft [pedidos de autenticação de 450 milhares de milhões](https://www.microsoft.com/en-us/security/intelligence-report) todos os meses para fornecer acesso a milhares de aplicações SaaS como o Office 365 a partir de praticamente qualquer dispositivo. 

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implementar para suportar o acesso a aplicações na cloud. Para atender a vários, segurança, requisitos técnicos e empresariais, as organizações podem optar entre a sincronização de hash de palavra-passe, autenticação pass-through e Federação. 

Considere cada método de autenticação. É que o esforço necessário para implementar a solução e a experiência do usuário do processo de início de sessão, abordar os requisitos de negócio? Avalie se a sua organização necessita de cenários avançados e funcionalidades de continuidade de negócio de cada método de autenticação. Por fim, avalie as considerações de cada método de autenticação. Qualquer um deles impedir a implementação de sua preferência?

## <a name="next-steps"></a>Passos Seguintes

No mundo de hoje, as ameaças estão presentes 24 horas por dia e são fornecidos a partir de qualquer lugar. Implementar o método de autenticação corretas, e irá reduzir os riscos de segurança e proteger as suas identidades.

[Introdução ao](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) com o Azure AD e implementar a solução de autenticação correta para sua organização.

Se estiver pensando em migrar do federado para autenticação da cloud, saiba mais sobre [alterar o método de início de sessão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Para ajudar a planear e implementar a migração, utilize [estes planos de implantação do projeto](http://aka.ms/deploymentplans).
