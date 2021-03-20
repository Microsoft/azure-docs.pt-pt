---
title: 'Azure AD Connect: | de sação do utilizador Microsoft Docs'
description: Azure AD Conecte o pedido de inscrição do utilizador para configurações personalizadas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997788"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect opções de inscrição do utilizador
O Azure Ative Directory (Azure AD) Connect permite que os seus utilizadores entrem em súmia e recursos no local utilizando as mesmas palavras-passe. Este artigo descreve conceitos-chave para cada modelo de identidade para ajudá-lo a escolher a identidade que pretende usar para iniciar sessão no Azure AD.

Se já está familiarizado com o modelo de identidade AZure AD e quer saber mais sobre um método específico, consulte o link apropriado:

* [Sincronização de hash](#password-hash-synchronization) de [palavra-passe com sso único sem emenda (SSO)](how-to-connect-sso.md)
* [Autenticação pass-through](how-to-connect-pta.md) com [sign-on única sem emenda (SSO)](how-to-connect-sso.md)
* [SSO Federado (com Serviços da Federação de Diretório Ativo (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federação com o PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> É importante lembrar que ao configurar a federação para a Azure AD, você estabelece a confiança entre o seu inquilino Azure AD e seus domínios federados. Com esta confiança, os utilizadores de domínio federados terão acesso aos recursos em nuvem AZure AD dentro do inquilino.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolher o método de inscrição do utilizador para a sua organização
A primeira decisão de implementar o Azure AD Connect é escolher qual o método de autenticação que os seus utilizadores utilizarão para iniciar sing. É importante ter certeza de que escolhe o método certo que satisfaz a segurança da sua organização e requisitos avançados. A autenticação é fundamental, pois irá validar as identidades dos utilizadores para aceder a apps e dados na nuvem. Para escolher o método de autenticação certo, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação da sua escolha. Estes fatores são diferentes para cada organização e podem mudar com o tempo.

A Azure AD suporta os seguintes métodos de autenticação: 

* **Autenticação em nuvem** - Ao escolher este método de autenticação, o Azure AD trata do processo de autenticação para a inscrição do utilizador. Com a autenticação em nuvem pode escolher entre duas opções: 
   * **Sincronização de hash de palavra-passe (PHS)** - O Password Hash Sync permite que os utilizadores utilizem o mesmo nome de utilizador e palavra-passe que utilizam no local sem terem de implantar qualquer infraestrutura adicional para além do Azure AD Connect. 
   * **Autenticação pass-through (PTA)** - Esta opção é semelhante à sincronização de haxixe de palavra-passe, mas fornece uma validação simples de senha usando agentes de software no local para organizações com políticas de segurança e conformidade fortes.
* **Autenticação federada** - Quando escolher este método de autenticação, o Azure AD entregará o processo de autenticação a um sistema de autenticação de confiança independente, como o AD FS ou um sistema de federação de terceiros, para validar a inscrição do utilizador. 

Para a maioria das organizações que apenas pretendem ativar o acesso do utilizador ao Microsoft 365, aplicações SaaS e outros recursos baseados em AD Azure, recomendamos a opção de sincronização de hash de palavra-passe padrão.
 
Para obter informações detalhadas sobre a escolha de um método de autenticação, consulte [Escolha o método de autenticação adequado para a sua solução de identidade híbrida Azure Ative Directory](./choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe
Com a sincronização de hash de palavra-passe, as hashes das palavras-passe do utilizador são sincronizadas desde o Ative Directory até ao Azure AD. Quando as palavras-passe são alteradas ou reiniciadas no local, as novas hashes de palavra-passe são sincronizadas imediatamente com a AZure AD para que os seus utilizadores possam sempre usar a mesma palavra-passe para recursos na nuvem e recursos no local. As palavras-passe nunca são enviadas para Azure AD ou armazenadas em Azure AD em texto claro. Pode utilizar a sincronização de hash de palavra-passe juntamente com a gravação de password para permitir a redefinição da palavra-passe de autosserviço no AZure AD.

Além disso, pode ativar [o Seamless SSO](how-to-connect-sso.md) para utilizadores em máquinas de domínio que estão na rede corporativa. Com um único s-on, os utilizadores ativados apenas precisam de introduzir um nome de utilizador para ajudá-los a aceder de forma segura aos recursos na nuvem.

![Sincronização de hash de palavra-passe](./media/plan-connect-user-signin/passwordhash.png)

Para mais informações, consulte o artigo [de sincronização de haxixe de palavra-passe.](how-to-connect-password-hash-synchronization.md)

### <a name="pass-through-authentication"></a>Autenticação pass-through
Com a autenticação pass-through, a palavra-passe do utilizador é validada contra o controlador Ative Directory no local. A palavra-passe não precisa de estar presente no Azure AD de qualquer forma. Isto permite que as políticas no local, como as restrições de hora de entrada, sejam avaliadas durante a autenticação em serviços na nuvem.

A autenticação pass-through utiliza um agente simples numa máquina de domínio R2012 R2 no ambiente de instalações. Este agente ouve pedidos de validação de palavras-passe. Não requer que portas de entrada estejam abertas à Internet.

Além disso, também pode ativar um único sinal de inscrição para utilizadores em máquinas de domínio que estão na rede corporativa. Com um único s-on, os utilizadores ativados apenas precisam de introduzir um nome de utilizador para ajudá-los a aceder de forma segura aos recursos na nuvem.
![Autenticação pass-through](./media/plan-connect-user-signin/pta.png)

Para obter mais informações, consulte:
- [Autenticação pass-through](how-to-connect-pta.md)
- [Início de sessão único](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federação que utiliza uma quinta nova ou existente com AD FS no Windows Server 2012 R2
Com o súmis federado, os seus utilizadores podem iniciar sômposições nos serviços baseados em Azure com as suas senhas no local. Enquanto estão na rede corporativa, nem sequer têm de introduzir as suas palavras-passe. Ao utilizar a opção da federação com AD FS, pode implementar uma quinta nova ou existente com AD FS no Windows Server 2012 R2. Se optar por especificar uma quinta existente, o Azure AD Connect configura a confiança entre a sua quinta e a Azure AD para que os seus utilizadores possam iniciar súpido.

<center>

![Federação com AD FS no Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Implementar federação com AD FS no Windows Server 2012 R2

Se está a implantar uma nova quinta, precisa:

* Um servidor R2 do Windows Server 2012 para o servidor da federação.
* Um servidor R2 do Windows Server 2012 para o Proxy da Aplicação Web.
* Um ficheiro .pfx com um certificado TLS/SSL para o nome de serviço da federação pretendido. Por exemplo: fs.contoso.com.

Se estiver a implantar uma nova quinta ou a utilizar uma quinta existente, precisa:

* Credenciais de administrador local nos servidores da federação.
* Credenciais de administrador local em quaisquer servidores de grupos de trabalho (não ligados ao domínio) em que pretende implementar a função de Procuração de Aplicação Web.
* A máquina em que executou o assistente pode ligar-se a quaisquer outras máquinas que pretende instalar AD FS ou Web Application Proxy utilizando a Gestão Remota do Windows.

Para obter mais informações, consulte [o SSO de Configuração com AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federação com o PingFederate
Com o súmis federado, os seus utilizadores podem iniciar sômposições nos serviços baseados em Azure com as suas senhas no local. Enquanto estão na rede corporativa, nem sequer têm de introduzir as suas palavras-passe.

Para obter mais informações sobre a configuração do PingFederate para uso com o Diretório Ativo Azure, consulte [a Integração pingFederate com o Diretório Ativo Azure e o Office 365](https://www.pingidentity.com/AzureADConnect)

Para obter informações sobre a configuração do Azure AD Connect utilizando o PingFederate, consulte [a instalação personalizada AZure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Inscreva-se utilizando uma versão anterior de AD FS ou uma solução de terceiros
Se já configurar o início de saúde em nuvem utilizando uma versão anterior de AD FS (como AD FS 2.0) ou um provedor de federação de terceiros, pode optar por ignorar a configuração de entrada do utilizador através do Azure AD Connect. Isto irá permitir-lhe obter a mais recente sincronização e outras capacidades do Azure AD Connect enquanto ainda utiliza a solução existente para iniciar sing-in.

Para mais informações, consulte a [lista de compatibilidades da federação de terceiros Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Inscrição do utilizador e nome principal do utilizador
### <a name="understanding-user-principal-name"></a>Compreensão do nome principal do utilizador
No Ative Directory, o sufixo de nome principal do utilizador predefinido (UPN) é o nome DNS do domínio onde a conta de utilizador foi criada. Na maioria dos casos, este é o nome de domínio que está registado como domínio da empresa na Internet. No entanto, pode adicionar mais sufixos UPN utilizando domínios e fidedignos de Diretório Ativo.

A UPN do utilizador tem o username@domain formato. Por exemplo, para um domínio de Diretório Ativo chamado "contoso.com", um utilizador chamado John pode ter a UPN john@contoso.com " A UPN do utilizador baseia-se no RFC 822. Embora a UPN e o e-mail partilhem o mesmo formato, o valor da UPN para um utilizador pode ou não ser o mesmo que o endereço de e-mail do utilizador.

### <a name="user-principal-name-in-azure-ad"></a>Nome principal do utilizador em Azure AD
O assistente Azure AD Connect utiliza o atributo UserPrincipalName ou permite especificar o atributo (numa instalação personalizada) para ser utilizado a partir de instalações como o nome principal do utilizador em Azure AD. Este é o valor que é usado para iniciar sessão no Azure AD. Se o valor do atributo UserPrincipalName não corresponder a um domínio verificado em Ad Azure, então a Azure AD substitui-o por um valor padrão .onmicrosoft.com.

Cada diretório no Azure Ative Directory vem com um nome de domínio incorporado, com o formato contoso.onmicrosoft.com, que permite começar a usar Azure ou outros serviços da Microsoft. Pode melhorar e simplificar a experiência de inscrição utilizando domínios personalizados. Para obter informações sobre nomes de domínio personalizados em AD AZure e como verificar um domínio, consulte [Adicionar o seu nome de domínio personalizado ao Azure Ative Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Configuração do início de sessão do Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração de inscrição AD AD azure com Azure AD Connect
A experiência de inscrição AZure AD depende se a Azure AD pode corresponder ao sufixo do nome principal do utilizador de um utilizador que está a ser sincronizado com um dos domínios personalizados que são verificados no diretório AD Azure. O Azure AD Connect fornece ajuda enquanto configura as definições de inscrição Azure AD, de modo a que a experiência de inscrição do utilizador na nuvem seja semelhante à experiência no local.

O Azure AD Connect lista os sufixos UPN que são definidos para os domínios e tenta compará-los com um domínio personalizado em Azure AD. Então ajuda-o com as medidas apropriadas que precisam de ser tomadas.
A página de inscrição AZure AD lista os sufixos UPN que são definidos para o Ative Directory no local e apresenta o estado correspondente em cada sufixo. Os valores de estado podem ser um dos seguintes:

| Estado | Descrição | Medidas necessárias |
|:--- |:--- |:--- |
| Verificado |Azure AD Connect encontrou um domínio verificado correspondente em Azure AD. Todos os utilizadores deste domínio podem iniciar sômposições utilizando as suas credenciais no local. |Não é necessária nenhuma ação. |
| Não verificado |O Azure AD Connect encontrou um domínio personalizado correspondente no AZure AD, mas não é verificado. O sufixo UPN dos utilizadores deste domínio será alterado para o sufixo padrão .onmicrosoft.com sufixo após a sincronização se o domínio não for verificado. | [Verifique o domínio personalizado em Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Não adicionado |O Azure AD Connect não encontrou um domínio personalizado que correspondia ao sufixo UPN. O sufixo UPN dos utilizadores deste domínio será alterado para o sufixo padrão .onmicrosoft.com se o domínio não for adicionado e verificado em Azure. | [Adicione e verifique um domínio personalizado que corresponda ao sufixo UPN.](../fundamentals/add-custom-domain.md) |

A página de inscrição AZure AD lista os sufixos UPN que são definidos para o Ative Directory no local e o domínio personalizado correspondente em AD Azure com o estado de verificação atual. Numa instalação personalizada, pode agora selecionar o atributo para o nome principal do utilizador na página **de insusição Azure AD.**

![Página de seduque AD AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Pode clicar no botão de atualização para reencambar o estado mais recente dos domínios personalizados a partir do Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selecionando o atributo para o nome principal do utilizador em Azure AD
O utilizador do atributoPrincipalName é o atributo que os utilizadores usam quando fazem súmico a AD e Microsoft 365. Deve verificar os domínios (também conhecidos como sufixos UPN) que são utilizados em Azure AD antes de os utilizadores serem sincronizados.

Recomendamos vivamente que mantenha o nome de utilizador predefinidoPrincipal. Se este atributo não for exequível e não puder ser verificado, então é possível selecionar outro atributo (e-mail, por exemplo) como o atributo que detém o ID de inscrição. Isto é conhecido como o ID Alternativo. O valor do atributo ID alternativo deve seguir a norma RFC 822. Pode utilizar um ID alternativo com a palavra-passe SSO e a federação SSO como solução de entrada.

> [!NOTE]
> A utilização de um ID alternativo não é compatível com todas as cargas de trabalho do Microsoft 365. Para obter mais informações, consulte o artigo [Configurar um ID de Início de Sessão Alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Diferentes estados de domínio personalizados e o seu efeito na experiência de inscrição do Azure
É muito importante entender a relação entre os estados de domínio personalizados no seu diretório AD Azure e os sufixos upn que são definidos no local. Vamos analisar as diferentes experiências possíveis de inscrição do Azure quando estiver a configurar a sincronização utilizando o Azure AD Connect.

Para as seguintes informações, vamos assumir que estamos preocupados com o sufixo upn contoso.com, que é usado no diretório no local como parte da UPN -- por exemplo user@contoso.com .

###### <a name="express-settingspassword-hash-synchronization"></a>Definições expressas/Sincronização de hash password

| Estado | Efeito na experiência de inscrição do utilizador Azure |
|:---:|:--- |
| Não adicionado |Neste caso, não foi adicionado nenhum domínio personalizado para contoso.com no diretório AD Azure. Os utilizadores que tenham UPN no local com o sufixo @contoso.com não poderão utilizar a UPN no local para se inscreverem no Azure. Em vez disso, terão de usar uma nova UPN que lhes é fornecida pela Azure AD adicionando o sufixo para o diretório AD Ad padrão. Por exemplo, se estiver a sincronizar os utilizadores com o diretório AD AZure azurecontoso.onmicrosoft.com, então o utilizador no local user@contoso.com receberá uma UPN de user@azurecontoso.onmicrosoft.com . |
| Não verificado |Neste caso, temos um domínio personalizado contoso.com que é adicionado no diretório AD Azure. No entanto, ainda não está verificado. Se avançar com os utilizadores sincronizados sem verificar o domínio, os utilizadores serão atribuídos um novo UPN pela Azure AD, tal como no cenário "Não adicionado". |
| Verificado |Neste caso, temos um domínio personalizado contoso.com que já foi adicionado e verificado em Azure AD para o sufixo UPN. Os utilizadores poderão utilizar o seu nome principal de utilizador no local, por user@contoso.com exemplo, para iniciar seduca no Azure depois de estarem sincronizados com o AZure AD. |

###### <a name="ad-fs-federation"></a>Federação AD FS
Não é possível criar uma federação com o domínio padrão .onmicrosoft.com em Azure AD ou um domínio personalizado não verificado em Azure AD. Quando estiver a executar o assistente Azure AD Connect, se selecionar um domínio não verificado para criar uma federação, então o Azure AD Connect solicita-lhe os registos necessários a serem criados onde o seu DNS está hospedado para o domínio. Para obter mais informações, consulte [verifique o domínio AZure AD selecionado para a federação](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se selecionou a Federação de Sedupção de Inscrição do utilizador **com AD FS,** então deve ter um domínio personalizado para continuar a criar uma federação em Azure AD. Para a nossa discussão, isto significa que devemos ter um domínio personalizado contoso.com adicionado no diretório AD Azure.

| Estado | Efeito na experiência de inscrição do utilizador Azure |
|:---:|:--- |
| Não adicionado |Neste caso, o Azure AD Connect não encontrou um domínio personalizado correspondente para o sufixo UPN contoso.com no diretório AD Azure. É necessário adicionar um domínio personalizado contoso.com se precisar que os utilizadores entrem em sôm, utilizando AD FS com a SUA UPN user@contoso.com (como). |
| Não verificado |Neste caso, o Azure AD Connect solicita-lhe detalhes apropriados sobre como pode verificar o seu domínio numa fase posterior. |
| Verificado |Neste caso, pode avançar com a configuração sem qualquer outra ação. |

## <a name="changing-the-user-sign-in-method"></a>Alterar o método de início de sessão do utilizador
Pode alterar o método de entrada do utilizador a partir da federação, sincronização de hash de palavra-passe ou autenticação pass-through utilizando as tarefas disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect com o assistente. Execute novamente o assistente Azure AD Connect e verá uma lista de tarefas que pode executar. Selecione Alterar o **pedido de utilizadores** da lista de tarefas.

![Alterar o pedido de in do utilizador](./media/plan-connect-user-signin/changeusersignin.png)

Na página seguinte, é-lhe pedido que forneça as credenciais para a Azure AD.

![Screenshot que mostra onde deve escrever as credenciais para Azure AD.](./media/plan-connect-user-signin/changeusersignin2.png)

Na página **de insusição do Utilizador,** selecione o pedido de inscrição do utilizador.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se estiver apenas a fazer uma troca temporária para sincronização de hash de palavra-passe, então selecione a caixa de verificação **de contas de utilizador não convertida.** Não verificar a opção converterá cada utilizador em federado, podendo demorar várias horas.
>
>

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a integração das suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
- Saiba mais sobre [os conceitos de design Azure AD Connect](plan-connect-design-concepts.md).