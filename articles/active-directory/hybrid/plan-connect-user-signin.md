---
title: 'Azure AD Connect: Entrada no utilizador Microsoft Docs'
description: Entrada de utilizador Azure AD Connect para configurações personalizadas.
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
ms.openlocfilehash: a08120b98c7a08bca50453df59df313b1645c5c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331273"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opções de entrada de utilizador azure AD Connect
O Azure Ative Directory (Azure AD) Connect permite que os seus utilizadores acedam tanto aos recursos em nuvem como no local, utilizando as mesmas palavras-passe. Este artigo descreve conceitos-chave para cada modelo de identidade para ajudá-lo a escolher a identidade que pretende usar para iniciar sessão no Azure AD.

Se já está familiarizado com o modelo de identidade Azure AD e quer saber mais sobre um método específico, consulte a ligação adequada:

* [Sincronização](#password-hash-synchronization) de hash password com [insígnia single seamless (SSO)](how-to-connect-sso.md)
* [Autenticação pass-through](how-to-connect-pta.md) com [insígnia single sign-on (SSO)](how-to-connect-sso.md)
* [SSO Federado (com Serviços da Federação de Diretório Ativo (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federação com o PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> É importante lembrar que ao configurar a federação para a AD Azure, estabelece a confiança entre o seu inquilino Azure AD e os seus domínios federados. Com esta confiança, os utilizadores de domínio federado terão acesso aos recursos da nuvem Azure AD dentro do inquilino.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolher o método de inscrição do utilizador para a sua organização
A primeira decisão de implementação do Azure AD Connect é escolher qual o método de autenticação que os seus utilizadores utilizarão para iniciar sessão. É importante certificar-se de que escolhe o método certo que satisfaz a segurança da sua organização e os requisitos avançados. A autenticação é fundamental, pois irá validar as identidades dos utilizadores para aceder a apps e dados na nuvem. Para escolher o método de autenticação certo, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação da sua escolha. Estes fatores são diferentes para cada organização e podem mudar com o tempo.

A Azure AD suporta os seguintes métodos de autenticação: 

* **Autenticação em Nuvem** - Ao escolher este método de autenticação, a AD AD ad ocabo com o processo de autenticação para o início de sessão do utilizador. Com a autenticação em nuvem pode escolher entre duas opções: 
   * **Sincronização de hash password (PHS)** - O Password Hash Sync permite que os utilizadores utilizem o mesmo nome de utilizador e senha que utilizam no local sem terem de implementar qualquer infraestrutura adicional para além do Azure AD Connect. 
   * **Autenticação pass-through (PTA)** - Esta opção é semelhante à sincronização de hash password, mas fornece uma simples validação de palavra-passe utilizando agentes de software no local para organizações com políticas de segurança e conformidade fortes.
* **Autenticação federada** - Ao escolher este método de autenticação, o Azure AD entregará o processo de autenticação a um sistema de autenticação fidedigno separado, como a AD FS ou um sistema de federação de terceiros, para validar a entrada do utilizador. 

Para a maioria das organizações que apenas querem ativar o acesso ao utilizador no Office 365, aplicações SaaS e outros recursos baseados em Azure AD, recomendamos a opção de sincronização de hash de senha padrão.
 
Para obter informações detalhadas sobre a escolha de um método de autenticação, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe
Com a sincronização de hash de palavra-passe, as hashes das palavras-passe dos utilizadores são sincronizadas de diretório ativo no local para Azure AD. Quando as palavras-passe são alteradas ou redefinidas no local, as novas hashes de senha são sincronizadas para a AD Azure imediatamente para que os seus utilizadores possam sempre usar a mesma palavra-passe para recursos na nuvem e recursos no local. As palavras-passe nunca são enviadas para a AD Azure ou armazenadas em Azure AD em texto claro. Pode utilizar a sincronização de hash de palavra-passe juntamente com o reescrito de palavra-passe para permitir o reset da palavra-passe de self-service em Azure AD.

Além disso, pode ativar [o SSO SSO sem emenda](how-to-connect-sso.md) para os utilizadores em máquinas unidas ao domínio que estão na rede corporativa. Com um único sinal, os utilizadores habilitados apenas precisam de introduzir um nome de utilizador para ajudá-los a aceder de forma segura aos recursos da nuvem.

![Sincronização de hash de palavra-passe](./media/plan-connect-user-signin/passwordhash.png)

Para mais informações, consulte o artigo de sincronização de hash de [palavra-passe.](how-to-connect-password-hash-synchronization.md)

### <a name="pass-through-authentication"></a>Autenticação pass-through
Com a autenticação pass-through, a palavra-passe do utilizador é validada contra o controlador de Diretório Ativo no local. A palavra-passe não precisa de estar presente em Azure AD de qualquer forma. Isto permite que políticas no local, como restrições de hora de inscrição, sejam avaliadas durante a autenticação aos serviços na nuvem.

A autenticação pass-through utiliza um agente simples numa máquina de domínio R2 do Windows Server 2012 no ambiente no local. Este agente ouve pedidos de validação de passwords. Não requer que quaisquer portos de entrada estejam abertos à Internet.

Além disso, também pode ativar um único sinal para os utilizadores em máquinas unidas ao domínio que estão na rede corporativa. Com um único sinal, os utilizadores habilitados apenas precisam de introduzir um nome de utilizador para ajudá-los a aceder de forma segura aos recursos da nuvem.
![Autenticação pass-through](./media/plan-connect-user-signin/pta.png)

Para obter mais informações, consulte:
- [Autenticação pass-through](how-to-connect-pta.md)
- [Início de sessão único](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federação que usa uma quinta nova ou existente com AD FS no Windows Server 2012 R2
Com o acesso federado, os seus utilizadores podem iniciar sessão nos serviços baseados em AD Azure com as suas palavras-passe no local. Enquanto estão na rede corporativa, nem sequer têm de introduzir as suas palavras-passe. Ao utilizar a opção da federação com AD FS, pode implementar uma quinta nova ou existente com AD FS no Windows Server 2012 R2. Se optar por especificar uma exploração existente, o Azure AD Connect confunde a confiança entre a sua quinta e a Azure AD para que os seus utilizadores possam iniciar sessão.

<center>

![Federação com AD FS no Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Implementar federação com AD FS no Windows Server 2012 R2

Se estás a implantar uma nova quinta, precisas de:

* Um servidor R2 Do Windows Server 2012 para o servidor da federação.
* Um servidor R2 do Windows Server 2012 para o Proxy da Aplicação Web.
* Um ficheiro .pfx com um certificado TLS/SSL para o seu nome de serviço da federação pretendido. Por exemplo: fs.contoso.com.

Se estiver a implantar uma nova quinta ou a utilizar uma quinta existente, precisa de:

* Credenciais de administrador local nos seus servidores da federação.
* Credenciais de administrador local em quaisquer servidores de grupo de trabalho (não unidos pelo domínio) em que pretende implementar a função proxy de aplicação web.
* A máquina em que executa o assistente para poder ligar-se a quaisquer outras máquinas que pretenda instalar AD FS ou Web Application Proxy através da utilização da Gestão Remota do Windows.

Para mais informações, consulte [Configurar SSO com AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federação com o PingFederate
Com o acesso federado, os seus utilizadores podem iniciar sessão nos serviços baseados em AD Azure com as suas palavras-passe no local. Enquanto estão na rede corporativa, nem sequer têm de introduzir as suas palavras-passe.

Para obter mais informações sobre a configuração do PingFederate para utilização com o Diretório Ativo Azure, consulte [integração pingFederate com diretório ativo azure e Office 365](https://www.pingidentity.com/AzureADConnect)

Para obter informações sobre a criação do Azure AD Connect utilizando o PingFederate, consulte a [instalação personalizada Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Inscreva-se utilizando uma versão anterior de AD FS ou uma solução de terceiros
Se já configuraram o sessão em nuvem utilizando uma versão anterior do AD FS (como AD FS 2.0) ou um fornecedor de federação de terceiros, pode optar por ignorar a configuração de entrada do utilizador através do Azure AD Connect. Isto permitir-lhe-á obter a mais recente sincronização e outras capacidades do Azure AD Connect enquanto ainda utiliza a sua solução existente para iniciar sessão.

Para mais informações, consulte a lista de compatibilidade de terceiros da [AD Azure.](how-to-connect-fed-compatibility.md)


## <a name="user-sign-in-and-user-principal-name"></a>Nome principal de sessão do utilizador e utilizador
### <a name="understanding-user-principal-name"></a>Compreender o nome principal do utilizador
No Diretório Ativo, o sufixo principal do utilizador padrão (UPN) é o nome DNS do domínio onde a conta de utilizador foi criada. Na maioria dos casos, este é o nome de domínio que está registado como o domínio da empresa na Internet. No entanto, pode adicionar mais sufixos UPN utilizando domínios e trusts de Diretório Ativo.

A UPN do utilizador username@domaintem o formato . Por exemplo, para um domínio de Diretório Ativo chamado "contoso.com",john@contoso.comum utilizador chamado John pode ter a UPN ". A UPN do utilizador baseia-se no RFC 822. Embora a UPN e o e-mail partilhem o mesmo formato, o valor da UPN para um utilizador pode ou não ser o mesmo que o endereço de e-mail do utilizador.

### <a name="user-principal-name-in-azure-ad"></a>Nome principal do utilizador em Azure AD
O assistente Azure AD Connect utiliza o atributo do nome principal do utilizador ou permite especificar o atributo (numa instalação personalizada) a ser utilizado a partir do local como o nome principal do utilizador em Azure AD. Este é o valor que é usado para iniciar sessão na Azure AD. Se o valor do atributo do userPrincipalName não corresponder a um domínio verificado em Azure AD, então o Azure AD substitui-o por um valor padrão .onmicrosoft.com.

Todos os diretórios do Azure Ative Directory vêm com um nome de domínio incorporado, com o formato contoso.onmicrosoft.com, que permite começar a usar o Azure ou outros serviços da Microsoft. Pode melhorar e simplificar a experiência de inscrição utilizando domínios personalizados. Para obter informações sobre nomes de domínio personalizados em Azure AD e como verificar um domínio, consulte Adicionar o seu nome de [domínio personalizado ao Diretório Ativo Azure](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Configuração do início de sessão do Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração de entrada em AD Azure com Azure AD Connect
A experiência de entrada em Anúncios Azure depende se o Azure AD pode corresponder ao sufixo principal do utilizador de um utilizador que está a ser sincronizado com um dos domínios personalizados que são verificados no diretório Azure AD. O Azure AD Connect fornece ajuda enquanto configura as definições de entrada de AD Azure, de modo que a experiência de entrada do utilizador na nuvem seja semelhante à experiência no local.

O Azure AD Connect lista os sufixos UPN que são definidos para os domínios e tenta compará-los com um domínio personalizado em Azure AD. Então ajuda-o com as medidas adequadas que devem ser tomadas.
A página de entrada de adato azure lista os sufixos UPN que são definidos para o diretório ativo no local e exibe o estado correspondente contra cada sufixo. Os valores de estado podem ser um dos seguintes:

| Estado | Descrição | Medidas necessárias |
|:--- |:--- |:--- |
| Verificado |A Azure AD Connect encontrou um domínio verificado correspondente em Azure AD. Todos os utilizadores deste domínio podem iniciar sessão utilizando as suas credenciais no local. |Não é necessária nenhuma ação. |
| Não verificado |O Azure AD Connect encontrou um domínio personalizado correspondente em Azure AD, mas não é verificado. O sufixo UPN dos utilizadores deste domínio será alterado para o sufixo padrão .onmicrosoft.com após a sincronização se o domínio não for verificado. | [Verifique o domínio personalizado em Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Não adicionado |O Azure AD Connect não encontrou um domínio personalizado que correspondesse ao sufixo UPN. O sufixo UPN dos utilizadores deste domínio será alterado para o padrão .onmicrosoft.com sufixo se o domínio não for adicionado e verificado no Azure. | [Adicione e verifique um domínio personalizado que corresponda ao sufixo UPN.](../fundamentals/add-custom-domain.md) |

A página de entrada de adato azure lista os sufixos UPN que são definidos para o diretório ativo no local e o domínio personalizado correspondente em Azure AD com o estado de verificação atual. Numa instalação personalizada, pode agora selecionar o atributo para o nome principal do utilizador na página de entrada de **anúncios da AD Azure.**

![Página de entrada de entrada da AD Azure](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Pode clicar no botão de atualização para reobter o estado mais recente dos domínios personalizados a partir de Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selecionando o atributo para o nome principal do utilizador em Azure AD
O utilizador atributoPrincipalNome é o atributo que os utilizadores usam quando entram no Azure AD e office 365. Deve verificar os domínios (também conhecidos como sufixos UPN) que são utilizados em AD Azure antes de os utilizadores serem sincronizados.

Recomendamos vivamente que mantenha o utilizador de atributo predefinidoPrincipalName. Se este atributo é intransponível e não pode ser verificado, então é possível selecionar outro atributo (e-mail, por exemplo) como o atributo que detém o ID de entrada. Isto é conhecido como a identidade alternativa. O valor do atributo de id alternativo deve seguir a norma RFC 822. Pode utilizar um ID alternativo com a palavra-passe SSO e a federação SSO como solução de entrada.

> [!NOTE]
> Usar um ID alternativo não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte o artigo [Configurar um ID de Início de Sessão Alternativo](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Diferentes estados de domínio personalizado e o seu efeito na experiência de entrada em Azure
É muito importante entender a relação entre os estados de domínio personalizados no seu diretório Azure AD e os sufixos UPN que são definidos no local. Vamos passar pelas diferentes experiências de entrada possíveis do Azure quando estivera a configurar a sincronização utilizando o Azure AD Connect.

Para as seguintes informações, vamos supor que estamos preocupados com o sufixo upn contoso.com, que é usado user@contoso.comno diretório no local como parte da UPN -- por exemplo .

###### <a name="express-settingspassword-hash-synchronization"></a>Definições expressas/Sincronização de hash de palavra-passe

| Estado | Efeito na experiência de entrada do utilizador Azure |
|:---:|:--- |
| Não adicionado |Neste caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório Azure AD. Os utilizadores que tenham UPN no local @contoso.com com o sufixo não poderão usar as suas UPN no local para iniciar o contrato com o Azure. Em vez disso, terão de usar uma nova UPN que lhes é fornecida pela Azure AD adicionando o sufixo para o diretório ad. Por exemplo, se estiver a sincronizar os utilizadores com o diretório DaD user@contoso.com Azure azurecontoso.onmicrosoft.com, user@azurecontoso.onmicrosoft.comentão o utilizador no local receberá uma UPN de . |
| Não verificado |Neste caso, temos um domínio personalizado contoso.com que é adicionado no diretório Azure AD. No entanto, ainda não foi verificado. Se avançar com utilizadores sincronizados sem verificar o domínio, os utilizadores serão atribuídos a uma nova UPN pela Azure AD, tal como no cenário "Não adicionado". |
| Verificado |Neste caso, temos um domínio personalizado contoso.com que já foi adicionado e verificado em Azure AD para o sufixo UPN. Os utilizadores poderão utilizar o seu nome principal de user@contoso.comutilizador no local, por exemplo, para iniciar sessão no Azure depois de sincronizados com a AD Azure. |

###### <a name="ad-fs-federation"></a>Federação AD FS
Não é possível criar uma federação com o domínio padrão .onmicrosoft.com em Azure AD ou um domínio personalizado não verificado em Azure AD. Quando estiver a executar o assistente Azure AD Connect, se selecionar um domínio não verificado para criar uma federação com, então o Azure AD Connect solicita-lhe os registos necessários para serem criados onde o seu DNS está hospedado para o domínio. Para mais informações, [consulte Verifique o domínio Azure AD selecionado para a federação](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se selecionou a Federação de Opções de Utilizador **com AD FS,** então deve ter um domínio personalizado para continuar a criar uma federação em Azure AD. Para a nossa discussão, isto significa que devemos ter um domínio personalizado contoso.com adicionado no diretório Azure AD.

| Estado | Efeito na experiência de entrada do utilizador Azure |
|:---:|:--- |
| Não adicionado |Neste caso, o Azure AD Connect não encontrou um domínio personalizado correspondente para o sufixo UPN contoso.com no diretório Azure AD. Você precisa adicionar um domínio personalizado contoso.com se precisar que os utilizadores se inscrevam usando user@contoso.comAD FS com as suas UPN no local (como). |
| Não verificado |Neste caso, o Azure AD Connect solicita-lhe detalhes adequados sobre como pode verificar o seu domínio numa fase posterior. |
| Verificado |Neste caso, pode avançar com a configuração sem qualquer outra ação. |

## <a name="changing-the-user-sign-in-method"></a>Alterar o método de início de sessão do utilizador
Pode alterar o método de entrada do utilizador da federação, sincronização de hash de palavra-passe ou autenticação pass-through utilizando as tarefas que estão disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect com o assistente. Execute novamente o assistente Azure AD Connect e verá uma lista de tarefas que poderá executar. Selecione Alterar o **sessão do utilizador** na lista de tarefas.

![Alterar o sessão de inscrição do utilizador](./media/plan-connect-user-signin/changeusersignin.png)

Na página seguinte, é-lhe pedido que forneça as credenciais para o Azure AD.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na página **de sessão de sessão do Utilizador,** selecione o sinal de utilizador pretendido.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se estiver apenas a fazer uma troca temporária para sincronização de hash de palavra-passe, então selecione a caixa de verificação de contas de utilizador Não converter contas de **utilizador.** Não verificar a opção converterá cada utilizador em federado, e pode demorar várias horas.
>
>

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a integração das suas identidades no local com o Diretório Ativo Azure.](whatis-hybrid-identity.md)
- Saiba mais sobre os conceitos de [design Azure AD Connect.](plan-connect-design-concepts.md)
