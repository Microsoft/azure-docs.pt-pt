---
title: Autenticação para soluções de identidade híbrida AZURE AD
titleSuffix: Active Directory
description: Este guia ajuda CEOs, CIOs, CISOs, Chief Identity Architects, Enterprise Architects e Security e decisores de TI responsáveis pela escolha de um método de autenticação para a sua solução de identidade híbrida AZure AD em média a grandes organizações.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 15d62f40b50617fd1f6e543cb404a0d38361d3bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836500"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory

Escolher o método de autenticação correto é a primeira preocupação para as organizações que querem mover as suas apps para a nuvem. Não tome esta decisão de ânimo leve, pelas seguintes razões:

1. É a primeira decisão para uma organização que quer passar para a nuvem.

2. O método de autenticação é um componente crítico da presença de uma organização na nuvem. Controla o acesso a todos os dados e recursos em nuvem.

3. É a base de todas as outras funcionalidades avançadas de segurança e experiência do utilizador no Azure AD.

A identidade é o novo plano de controlo da segurança de TI, por isso a autenticação é a guarda de acesso de uma organização ao novo mundo das nuvens. As organizações precisam de um plano de controlo de identidade que fortaleça a sua segurança e mantenha as suas aplicações em nuvem a salvo de intrusos.

> [!NOTE]
> Alterar o seu método de autenticação requer planeamento, teste e potencial inatividade. [O lançamento encenado](./how-to-connect-staged-rollout.md) é uma ótima maneira de testar a migração dos utilizadores da federação para a autenticação em nuvem.

### <a name="out-of-scope"></a>Fora de alcance
Organizações que não têm uma pegada de diretório existente no local não são o foco deste artigo. Normalmente, essas empresas criam identidades apenas na nuvem, o que não requer uma solução de identidade híbrida. As identidades apenas em nuvem existem apenas na nuvem e não estão associadas a identidades correspondentes no local.

## <a name="authentication-methods"></a>Métodos de autenticação
Quando a solução de identidade híbrida AZure AD é o seu novo plano de controlo, a autenticação é a base do acesso à nuvem. Escolher o método de autenticação correto é uma primeira decisão crucial na criação de uma solução de identidade híbrida AZure AD. Implemente o método de autenticação configurado utilizando o Azure AD Connect, que também fornece utilizadores na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, infraestrutura existente, complexidade e custo de implementação da sua escolha. Estes fatores são diferentes para cada organização e podem mudar com o tempo.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

A Azure AD suporta os seguintes métodos de autenticação para soluções de identidade híbrida.

### <a name="cloud-authentication"></a>Autenticação em nuvem
Ao escolher este método de autenticação, o Azure AD trata do processo de início de sação dos utilizadores. Juntamente com um único sign-on sem emenda (SSO), os utilizadores podem iniciar sposição em aplicações na nuvem sem terem de reentrar nas suas credenciais. Com a autenticação em nuvem, pode escolher entre duas opções:

**Sincronização de hash de palavra-passe do Azure AD**. A forma mais simples de permitir a autenticação para objetos de diretório no local em Azure AD. Os utilizadores podem usar o mesmo nome de utilizador e senha que utilizam no local sem terem de implementar qualquer infraestrutura adicional. Algumas funcionalidades premium do Azure AD, como a Proteção de Identidade e [os Serviços de Domínio AD Azure,](../../active-directory-domain-services/tutorial-create-instance.md)requerem sincronização de hash de palavra-passe, independentemente do método de autenticação que escolher.

> [!NOTE]
> As palavras-passe nunca são armazenadas em texto claro ou encriptadas com um algoritmo reversível em Azure AD. Para obter mais informações sobre o processo real de sincronização de hash de palavra-passe, consulte [implementar a sincronização de hash de palavra-passe com a sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Autenticação pass-through Azure AD**. Fornece uma validação simples de senha para serviços de autenticação Azure AD utilizando um agente de software que funciona em um ou mais servidores no local. Os servidores validam os utilizadores diretamente com o seu Ative Directory no local, o que garante que a validação da palavra-passe não acontece na nuvem.

As empresas com um requisito de segurança para impor imediatamente os estados da conta de utilizador no local, as políticas de senha e o horário de entrada podem utilizar este método de autenticação. Para obter mais informações sobre o processo de autenticação real de passagem, consulte [o utilizador insin in com autenticação de passagem AD Azure](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Autenticação federada
Ao escolher este método de autenticação, a Azure AD entrega o processo de autenticação a um sistema de autenticação de confiança separada, como os Serviços da Federação De Diretório Ativo no local (AD FS), para validar a palavra-passe do utilizador.

O sistema de autenticação pode fornecer requisitos de autenticação avançados adicionais. Exemplos são a autenticação baseada em smartcard ou a autenticação multifactor de terceiros. Para mais informações, consulte [a Implementação dos Serviços da Federação de Diretórios Ativos.](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)

A secção seguinte ajuda-o a decidir qual o método de autenticação adequado para si utilizando uma árvore de decisão. Ajuda-o a determinar se implementa a autenticação em nuvem ou federada para a sua solução de identidade híbrida AZure AD.

## <a name="decision-tree"></a>Árvore de decisões

![Árvore de decisão de autenticação da AD AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Detalhes sobre questões de decisão:

1. O Azure AD pode lidar com o sing-in para os utilizadores sem depender de componentes no local para verificar palavras-passe.
2. O Azure AD pode entregar o sômin do utilizador a um fornecedor de autenticação fidedigno, como o AD FS da Microsoft.
3. Se precisar de aplicar, as políticas de segurança do Ative Directory ao nível do utilizador, tais como conta expirada, conta desativada, senha expirada, conta bloqueada e horas de inscrição em cada utilizador, a Azure AD requer alguns componentes no local.
4. Funcionalidades de inscrição não suportadas de forma nativa pela Azure AD:
   * Iniciar sação com cartões inteligentes ou certificados.
   * Iniciar sômpa utilizando o MFA Server no local.
   * Iniciar sposição utilizando a solução de autenticação de terceiros.
   * Solução de autenticação multi-local no local.
5. A Azure AD Identity Protection requer password Hash Sync independentemente do método de inscrição que escolher, para fornecer aos Utilizadores um relatório de *credenciais vazado.* As organizações podem falhar no Password Hash Sync se o seu método de entrada primária falhar e ele foi configurado antes do evento de falha.

> [!NOTE]
> A Azure AD Identity Protection requer licenças [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Considerações detalhadas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticação em nuvem: Sincronização de hash de palavra-passe

* **Esforço.** A sincronização de hash de palavra-passe requer o menor esforço no que diz respeito à implantação, manutenção e infraestrutura.  Este nível de esforço aplica-se normalmente a organizações que apenas precisam que os seus utilizadores assinem o microsoft 365, aplicações SaaS e outros recursos baseados em AD AZure. Quando ligado, a sincronização de hash de palavra-passe faz parte do processo de sincronização Azure AD Connect e funciona a cada dois minutos.

* **Experiência do utilizador.** Para melhorar a experiência de entrada de utilizadores, implemente SSO sem costura com sincronização de hash de palavra-passe. O SSO sem emenda elimina solicitações desnecessárias quando os utilizadores estão inscritos.

* **Cenários avançados.** Se as organizações optarem, é possível utilizar insights de identidades com relatórios de Proteção de Identidade AZure AD com Azure AD Premium P2. Um exemplo é o relatório de credenciais vazado. O Windows Hello for Business tem [requisitos específicos quando utiliza a sincronização de hash de palavra-passe](/windows/access-protection/hello-for-business/hello-identity-verification). [Os Serviços de Domínio Azure AD](../../active-directory-domain-services/tutorial-create-instance.md) requer sincronização de hash de palavra-passe para a provisionar os utilizadores com as suas credenciais corporativas no domínio gerido.

    As organizações que necessitam de autenticação multi-factor com sincronização de hash de palavra-passe devem utilizar a autenticação multi-factor AD Azure ou [os controlos personalizados do Acesso Condicional](../../active-directory/conditional-access/controls.md#custom-controls-preview). Essas organizações não podem usar métodos de autenticação multifactor de terceiros ou no local que dependem da federação.

> [!NOTE]
> O acesso condicional Azure AD requer licenças [Azure AD Premium P1.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Continuidade do negócio.** A utilização de sincronização de hash de palavra-passe com autenticação em nuvem está altamente disponível como um serviço de cloud que escala para todos os datacenters da Microsoft. Para garantir que a sincronização de hash de palavra-passe não desça por períodos prolongados, inser uma segunda ligação Azure AD connect no modo de preparação numa configuração de espera.

* **Considerações.** Atualmente, a sincronização de hash de palavra-passe não impõe imediatamente alterações nos estados da conta no local. Nesta situação, um utilizador tem acesso a aplicações na nuvem até que o estado da conta do utilizador seja sincronizado com a Azure AD. As organizações podem querer ultrapassar esta limitação executando um novo ciclo de sincronização depois de os administradores fazerem atualizações a granel para estados de conta de utilizador no local. Um exemplo é desativar as contas.

> [!NOTE]
> A palavra-passe expirou e os estados bloqueados da conta não estão atualmente sincronizados com o AZure AD com o Azure AD Connect. Quando alterar a palavra-passe de um utilizador e definir a *palavra-passe* no próximo design de logo, o hash da palavra-passe não será sincronizado com Azure AD Connect até que o utilizador altere a sua palavra-passe.

Consulte a [implementação da sincronização de hash de palavra-passe](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) para etapas de implementação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação em nuvem: Autenticação pass-through  

* **Esforço.** Para a autenticação pass-through, precisa de um ou mais (recomendamos três) agentes leves instalados nos servidores existentes. Estes agentes devem ter acesso aos seus serviços de domínio de diretório ativo no local, incluindo os seus controladores de domínio AD no local. Precisam de acesso de saída à Internet e acesso aos seus controladores de domínio. Por esta razão, não é suportado para colocar os agentes numa rede de perímetro.

    A autenticação pass-through requer acesso de rede sem restrições aos controladores de domínio. Todo o tráfego de rede é encriptado e limitado a pedidos de autenticação. Para obter mais informações sobre este processo, consulte o [mergulho profundo](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) de segurança na autenticação pass-through.

* **Experiência do utilizador.** Para melhorar a experiência de inscrição dos utilizadores, implemente SSO sem costura com autenticação pass-through. O SSO sem emenda elimina solicitações desnecessárias após o utilizadores iniciarem sinsuposições.

* **Cenários avançados.** A autenticação pass-through aplica a política de conta no local no momento da inscrição. Por exemplo, o acesso é negado quando o estado da conta de um utilizador no local é desativado, bloqueado, ou a sua [palavra-passe expira](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) ou a tentativa de início de sê-lo fica fora das horas em que o utilizador pode iniciar scontabilidade.

    As organizações que necessitam de autenticação multi-factor com autenticação pass-through devem utilizar [controlos personalizados](../../active-directory/conditional-access/controls.md#custom-controls-preview)Azure AD Multi-Factor (MFA) ou acesso condicional . Essas organizações não podem usar um método de autenticação multifactor de terceiros ou no local que dependa da federação. As funcionalidades avançadas requerem que a sincronização de hash de palavra-passe seja implementada quer escolha ou não a autenticação pass-through. Um exemplo é o relatório de credenciais vazado da Proteção de Identidade.

* **Continuidade do negócio.** Recomendamos que implante dois agentes de autenticação extra pass-through. Estes extras são além do primeiro agente no servidor AZure AD Connect. Esta implementação adicional garante uma elevada disponibilidade de pedidos de autenticação. Quando se tem três agentes destacados, um agente ainda pode falhar quando outro agente está em baixo para manutenção.

    Há outro benefício em implementar a sincronização de hash de palavra-passe, além da autenticação pass-through. Funciona como um método de autenticação de reserva quando o método de autenticação primária já não está disponível.

* **Considerações.** Pode utilizar a sincronização de hash de palavra-passe como um método de autenticação de backup para a autenticação pass-through, quando os agentes não podem validar as credenciais de um utilizador devido a uma falha significativa no local. A falha na sincronização de hash de palavra-passe não acontece automaticamente e tem de utilizar o Azure AD Connect para ligar manualmente o método de entrada.

    Para outras considerações sobre a autenticação pass-through, incluindo o suporte de identificação alternativa, consulte [perguntas frequentes](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Consulte a [implementação da autenticação pass-through](../../active-directory/hybrid/how-to-connect-pta.md) para as etapas de implantação.

### <a name="federated-authentication"></a>Autenticação federada

* **Esforço.** Um sistema de autenticação federado conta com um sistema externo de confiança para autenticar os utilizadores. Algumas empresas querem reutilizar o seu investimento existente no sistema federado com a sua solução de identidade híbrida AZURE. A manutenção e gestão do sistema federado fica fora do controlo da Azure AD. Cabe à organização utilizar o sistema federado para se certificar de que é implantado de forma segura e que pode suportar a carga de autenticação.

* **Experiência do utilizador.** A experiência do utilizador da autenticação federada depende da implementação das funcionalidades, topologia e configuração da quinta da federação. Algumas organizações precisam desta flexibilidade para adaptar e configurar o acesso à exploração da federação de acordo com os seus requisitos de segurança. Por exemplo, é possível configurar utilizadores e dispositivos ligados internamente para iniciar súmula nos utilizadores, sem os solicitar credenciais. Esta configuração funciona porque já se inscrevem nos seus dispositivos. Se necessário, algumas funcionalidades avançadas de segurança dificultam o processo de inscrição dos utilizadores.

* **Cenários avançados.** É necessária uma solução de autenticação federada quando os clientes têm um requisito de autenticação que a Azure AD não suporta de forma nativa. Consulte informações detalhadas para [ajudá-lo a escolher a opção de inscrição certa](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Considere os seguintes requisitos comuns:

  * Autenticação que requer smartcards ou certificados.
  * Servidores MFA no local ou fornecedores multifactores de terceiros que necessitem de um fornecedor de identidade federado.
  * Autenticação utilizando soluções de autenticação de terceiros. Consulte a lista de [compatibilidade da federação AD Azure](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Inscreva-se que requer um nome sAMAccountName, por exemplo, nome de utilizador DOMAIN\, em vez de um Nome Principal do Utilizador (UPN), por exemplo, user@domain.com .

* **Continuidade do negócio.** Os sistemas federados normalmente requerem uma gama de servidores equilibrados em carga, conhecidos como uma fazenda. Esta quinta está configurada numa topologia interna da rede e da rede de perímetro para garantir uma elevada disponibilidade para pedidos de autenticação.

    Implemente a sincronização de hash de palavra-passe juntamente com a autenticação federada como método de autenticação de backup quando o método de autenticação primária já não estiver disponível. Um exemplo é quando os servidores no local não estão disponíveis. Algumas grandes organizações empresariais exigem uma solução de federação para suportar vários pontos de entrada na Internet configurados com geo-DNS para pedidos de autenticação de baixa latência.

* **Considerações.** Os sistemas federados normalmente exigem um investimento mais significativo em infraestruturas no local. A maioria das organizações escolhe esta opção se já tem um investimento da federação no local. E se for um forte requisito de negócio para usar um fornecedor de identidade única. A Federação é mais complexa para operar e resolver problemas em comparação com soluções de autenticação em nuvem.

Para um domínio não-encaminhável que não pode ser verificado no Azure AD, você precisa de configuração extra para implementar o iD do utilizador iniciar súm. Este requisito é conhecido como suporte de identificação de login alternativo. Consulte [o ID de Acesso Alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para configurar as limitações e requisitos. Se optar por utilizar um fornecedor de autenticação multi-factor de terceiros com a federação, certifique-se de que o fornecedor suporta WS-Trust para permitir que os dispositivos se juntem ao Azure AD.

Consulte [os Servidores da Federação](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) de Implementação para obter etapas de implementação.

> [!NOTE]
> Ao implementar a sua solução de identidade híbrida Azure AD, tem de implementar uma das topologias suportadas do Azure AD Connect. Saiba mais sobre configurações suportadas e não apoiadas nas [Topologias do Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

Os seguintes diagramas descrevem os componentes de arquitetura de alto nível necessários para cada método de autenticação que pode utilizar com a sua solução de identidade híbrida AZURE AD. Eles fornecem uma visão geral para ajudá-lo a comparar as diferenças entre as soluções.

* Simplicidade de uma solução de sincronização de haxixe de palavra-passe:

    ![Identidade híbrida AZURE AD com sincronização de hash password](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Requisitos do agente de autenticação pass-through, utilizando dois agentes para redundância:

    ![Identidade híbrida AZURE AD com autenticação pass-through](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Componentes necessários para a federação no seu perímetro e rede interna da sua organização:

    ![Identidade híbrida AD AD com autenticação federada](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Comparar métodos

|Consideração|Sincronização de hash de palavra-passe + SSO sem emenda|Autenticação pass-through + SSO sem emenda|Federação com o AD FS|
|:-----|:-----|:-----|:-----|
|Onde acontece a autenticação?|Na cloud|Na nuvem após uma troca segura de verificação de senha com o agente de autenticação no local|No local|
|Quais são os requisitos do servidor no local para além do sistema de provisionamento: Azure AD Connect?|Nenhum|Um servidor para cada agente de autenticação adicional|Dois ou mais servidores AD FS<br><br>Dois ou mais servidores WAP na rede perimeter/DMZ|
|Quais são os requisitos para a Internet no local e a rede para além do sistema de provisionamento?|Nenhum|[Acesso à Internet de saída](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) dos servidores que executam agentes de autenticação|[Acesso à Internet de entrada](/windows-server/identity/ad-fs/overview/ad-fs-requirements) para servidores WAP no perímetro<br><br>Acesso à rede de entrada para servidores AD FS a partir de servidores WAP no perímetro<br><br>Balanceamento de carga de rede|
|Existe um requisito de certificado TLS/SSL?|No|No|Yes|
|Existe uma solução de acompanhamento da saúde?|não é necessário|Estatuto de agente fornecido pelo [Azure Ative Directory centro de administração](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Os utilizadores obtêm um único sinal de saúde na nuvem de dispositivos ligados ao domínio dentro da rede da empresa?|Sim com [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)|Sim com [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)|Yes|
|Que tipos de inscrição são suportados?|Nome do UtilizadorPrincipal + senha<br><br>Windows-Integrated Autenticação utilizando [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[ID de login alternativo](../../active-directory/hybrid/how-to-connect-install-custom.md)|Nome do UtilizadorPrincipal + senha<br><br>Windows-Integrated Autenticação utilizando [SSO sem emenda](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[ID de login alternativo](../../active-directory/hybrid/how-to-connect-pta-faq.md)|Nome do UtilizadorPrincipal + senha<br><br>sAMAccountName + senha<br><br>Autenticação Windows-Integrated<br><br>[Autenticação de certificados e cartões inteligentes](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[ID de login alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|O Windows Hello para o Negócios está suportado?|[Modelo de confiança chave](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modelo de confiança chave](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Requer o nível funcional do Windows Server 2016*|[Modelo de confiança chave](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confiança de certificado](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quais são as opções de autenticação multifactor?|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|[Azure AD MFA](/azure/multi-factor-authentication/)<br><br>[Servidor Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA de terceiros](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controlos personalizados com acesso condicional*](../../active-directory/conditional-access/controls.md)|
|Que estados de conta de utilizador são suportados?|Contas desativadas<br>(atraso de até 30 minutos)|Contas desativadas<br><br>Conta bloqueada<br><br>Conta caducada<br><br>Senha expirada<br><br>Horário de inscrição|Contas desativadas<br><br>Conta bloqueada<br><br>Conta caducada<br><br>Senha expirada<br><br>Horário de inscrição|
|Quais são as opções de Acesso Condicional?|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional Azure AD, com Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Regras de reclamação da AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|O bloqueio dos protocolos do legado está suportado?|[Sim](../../active-directory/conditional-access/overview.md)|[Sim](../../active-directory/conditional-access/overview.md)|[Sim](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Pode personalizar o logótipo, imagem e descrição nas páginas de inscrição?|[Sim, com Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim, com Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Que cenários avançados são apoiados?|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Relatórios de credenciais vazados, com Azure AD Premium P2](../identity-protection/overview-identity-protection.md)|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)|Sistema de autenticação de baixa latência multissesite<br><br>[Bloqueio de extranet AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integração com sistemas de identidade de terceiros](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Os controlos personalizados no Azure AD Conditional Access não suportam atualmente o registo do dispositivo.

## <a name="recommendations"></a>Recomendações
O seu sistema de identidade garante o acesso dos seus utilizadores a aplicações na nuvem e às aplicações de linha de negócios que migra e disponibiliza na nuvem. Para manter os utilizadores autorizados produtivos e maus atores fora dos dados sensíveis da sua organização, a autenticação controla o acesso às apps.

Utilize ou ative a sincronização de haxixe de palavra-passe para o método de autenticação que escolher, pelas seguintes razões:

1. **Alta disponibilidade e recuperação de desastres.** A autenticação pass-through e a federação contam com infraestruturas no local. Para a autenticação pass-through, a pegada no local inclui o hardware do servidor e a rede que os agentes de autenticação pass-through exigem. Para a federação, a pegada no local é ainda maior. Requer servidores na sua rede de perímetro para pedidos de autenticação por procuração e os servidores da federação interna.

    Para evitar pontos únicos de falha, implemente servidores redundantes. Em seguida, os pedidos de autenticação serão sempre reparados se algum componente falhar. Tanto a autenticação pass-through como a federação também dependem de controladores de domínio para responder a pedidos de autenticação, que também podem falhar. Muitos destes componentes precisam de manutenção para se manterem saudáveis. As interrupções são mais prováveis quando a manutenção não é planeada e implementada corretamente. Evite interrupções utilizando a sincronização de hash de palavra-passe porque o serviço de autenticação em nuvem AD do Microsoft Azure escala globalmente e está sempre disponível.

2. **No local, a sobrevivência da paralisação.**  As consequências de uma paragem no local devido a um ciberataque ou desastre podem ser substanciais, desde danos reputacionais à marca a uma organização paralisada incapaz de lidar com o ataque. Recentemente, muitas organizações foram vítimas de ataques de malware, incluindo ransomware direcionado, o que fez com que os seus servidores no local fossem abatidos. Quando a Microsoft ajuda os clientes a lidar com este tipo de ataques, vê duas categorias de organizações:

   * As organizações que anteriormente também ligavam a sincronização de hash de palavra-passe em cima da autenticação federada ou pass-through alteraram o seu método de autenticação primária para depois utilizarem a sincronização de hash de palavra-passe. Voltaram a estar online numa questão de horas. Ao utilizar o acesso ao e-mail através do Microsoft 365, trabalharam para resolver problemas e aceder a outras cargas de trabalho baseadas na nuvem.

   * As organizações que não habilitavam anteriormente a sincronização de hash de palavra-passe tiveram de recorrer a sistemas de e-mail externos de consumo externos falsos para as comunicações resolverem problemas. Nesses casos, demoraram semanas a restaurar a sua infraestrutura de identidade no local, antes que os utilizadores pudessem voltar a inscrever-se em aplicações baseadas na nuvem.

3. **Proteção de identidade**. Uma das melhores formas de proteger os utilizadores na nuvem é a Proteção de Identidade AZURE AD com Azure AD Premium P2. A Microsoft verifica continuamente a Internet para listas de utilizadores e passwords que os maus atores vendem e disponibilizam na dark web. O Azure AD pode usar estas informações para verificar se algum dos nomes de utilizador e palavras-passe da sua organização está comprometido. Portanto, é fundamental ativar a sincronização de hash de palavra-passe independentemente do método de autenticação que utilize, seja ele federado ou a autenticação pass-through. As credenciais vazadas são apresentadas como um relatório. Utilize estas informações para bloquear ou forçar os utilizadores a alterar as suas palavras-passe quando tentam iniciar súm na súmia com palavras-passe vazadas.

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implementar para apoiar o acesso a aplicações na nuvem. Para atender a vários requisitos empresariais, de segurança e técnicos, as organizações podem escolher entre a sincronização de hash de palavra-passe, a autenticação pass-through e a federação.

Considere cada método de autenticação. O esforço de implantação da solução e a experiência do utilizador no processo de inscrição satisfaz os requisitos do seu negócio? Avalie se a sua organização precisa dos cenários avançados e das características de continuidade do negócio de cada método de autenticação. Finalmente, avalie as considerações de cada método de autenticação. Algum deles impede-o de implementar a sua escolha?

## <a name="next-steps"></a>Passos seguintes

No mundo de hoje, as ameaças estão presentes 24 horas por dia e vêm de todo o lado. Implemente o método de autenticação correto e irá mitigar os seus riscos de segurança e proteger as suas identidades.

[Começa](../fundamentals/active-directory-whatis.md) com o Azure AD e implementa a solução de autenticação certa para a tua organização.

Se está a pensar em migrar de federado para autenticação em nuvem, saiba mais sobre [a alteração do método de inscrição](../../active-directory/hybrid/plan-connect-user-signin.md). Para ajudá-lo a planear e implementar a migração, utilize [estes planos de implementação](../fundamentals/active-directory-deployment-plans.md) do projeto ou considere usar a nova funcionalidade [de Lançamento Encenado](../../active-directory/hybrid/how-to-connect-staged-rollout.md) para migrar utilizadores federados para usar a autenticação em nuvem numa abordagem faseada.