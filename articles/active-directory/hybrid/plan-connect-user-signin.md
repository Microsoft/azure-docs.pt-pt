---
title: 'Azure AD Connect: Entrada do usuário | Microsoft Docs'
description: Azure AD Connect entrada do usuário para configurações personalizadas.
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
ms.openlocfilehash: dbcc05093d801261493745c61dc5f68878d338b0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607659"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect opções de entrada do usuário
O Azure Active Directory (Azure AD) Connect permite que os usuários entrem em recursos de nuvem e locais usando as mesmas senhas. Este artigo descreve os principais conceitos de cada modelo de identidade para ajudá-lo a escolher a identidade que deseja usar para entrar no Azure AD.

Se você já estiver familiarizado com o modelo de identidade do Azure AD e quiser saber mais sobre um método específico, consulte o link apropriado:

* [Sincronização de hash de senha](#password-hash-synchronization) com [SSO (logon único) contínuo](how-to-connect-sso.md)
* [Autenticação de passagem](how-to-connect-pta.md) com [SSO (logon único) contínuo](how-to-connect-sso.md)
* [SSO Federado (com Serviços de Federação do Active Directory (AD FS) (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federação com PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> É importante lembrar que, ao configurar a Federação para o Azure AD, você estabelece a confiança entre seu locatário do Azure AD e seus domínios federados. Com essa confiança, os usuários do domínio federado terão acesso aos recursos de nuvem do Azure AD dentro do locatário.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolhendo o método de entrada do usuário para sua organização
A primeira decisão de implementar Azure AD Connect é escolher o método de autenticação que os usuários usarão para entrar. É importante ter certeza de escolher o método certo que atenda aos requisitos avançados e de segurança da sua organização. A autenticação é crítica, pois validará as identidades do usuário para acessar aplicativos e dados na nuvem. Para escolher o método de autenticação certo, você precisa considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo.

O Azure AD dá suporte aos seguintes métodos de autenticação: 

* **Autenticação na nuvem** -quando você escolhe esse método de autenticação, o Azure ad manipula o processo de autenticação para a entrada do usuário. Com a autenticação na nuvem, você pode escolher entre duas opções: 
   * **Sincronização de hash de senha (PHS)** -a sincronização de hash de senha permite que os usuários usem o mesmo nome de usuário e senha que eles usam localmente sem precisar implantar nenhuma infraestrutura adicional além Azure ad Connect. 
   * **Autenticação de passagem (PTA)** – essa opção é semelhante à sincronização de hash de senha, mas fornece uma validação de senha simples usando agentes de software local para organizações com políticas de conformidade e segurança fortes.
* **Autenticação federada** – quando você escolhe esse método de autenticação, o Azure ad irá entregar o processo de autenticação para um sistema de autenticação confiável separado, como AD FS ou um sistema de Federação de terceiros, para validar a entrada do usuário. 

Para a maioria das organizações que desejam apenas habilitar a entrada do usuário no Office 365, em aplicativos SaaS e em outros recursos baseados no Azure AD, recomendamos a opção de sincronização de hash de senha padrão.
 
Para obter informações detalhadas sobre como escolher um método de autenticação, consulte [escolher o método de autenticação correto para sua solução de identidade híbrida Azure Active Directory](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe
Com a sincronização de hash de senha, os hashes de senhas de usuário são sincronizados do Active Directory local para o Azure AD. Quando as senhas são alteradas ou redefinidas localmente, os novos hashes de senha são sincronizados com o Azure AD imediatamente para que os usuários sempre possam usar a mesma senha para recursos de nuvem e recursos locais. As senhas nunca são enviadas ao Azure AD ou armazenadas no Azure AD em texto não criptografado. Você pode usar a sincronização de hash de senha junto com o Write-back de senha para habilitar a redefinição de senha de autoatendimento no Azure AD.

Além disso, você pode habilitar o [SSO contínuo](how-to-connect-sso.md) para usuários em computadores ingressados no domínio que estão na rede corporativa. Com o logon único, os usuários habilitados precisam apenas inserir um nome de usuário para ajudá-los a acessar com segurança os recursos de nuvem.

![Sincronização de hash de palavra-passe](./media/plan-connect-user-signin/passwordhash.png)

Para obter mais informações, consulte o artigo [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) .

### <a name="pass-through-authentication"></a>Autenticação pass-through
Com a autenticação de passagem, a senha do usuário é validada no controlador de Active Directory local. A senha não precisa estar presente no Azure AD em nenhum formulário. Isso permite que as políticas locais, como restrições de hora de entrada, sejam avaliadas durante a autenticação para os serviços de nuvem.

A autenticação de passagem usa um agente simples em um computador ingressado no domínio do Windows Server 2012 R2 no ambiente local. Esse agente escuta solicitações de validação de senha. Ele não exige que nenhuma porta de entrada seja aberta na Internet.

Além disso, você também pode habilitar o logon único para usuários em computadores ingressados no domínio que estão na rede corporativa. Com o logon único, os usuários habilitados precisam apenas inserir um nome de usuário para ajudá-los a acessar com segurança os recursos de nuvem.
![Autenticação pass-through](./media/plan-connect-user-signin/pta.png)

Para obter mais informações, consulte:
- [Autenticação pass-through](how-to-connect-pta.md)
- [Início de sessão único](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federação que usa um farm novo ou existente com AD FS no Windows Server 2012 R2
Com a entrada federada, os usuários podem entrar em serviços baseados no Azure AD com suas senhas locais. Embora estejam na rede corporativa, eles nem precisam inserir suas senhas. Usando a opção de Federação com AD FS, você pode implantar um farm novo ou existente com AD FS no Windows Server 2012 R2. Se você optar por especificar um farm existente, Azure AD Connect configurará a relação de confiança entre seu farm e o Azure AD para que os usuários possam entrar.

<center>

![Federação com AD FS no Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Implantar a Federação com o AD FS no Windows Server 2012 R2

Se você estiver implantando um novo farm, precisará de:

* Um servidor Windows Server 2012 R2 para o servidor de Federação.
* Um servidor Windows Server 2012 R2 para o proxy de aplicativo Web.
* Um arquivo. pfx com um certificado SSL para o nome do serviço de Federação desejado. Por exemplo: fs.contoso.com.

Se estiver implantando um novo farm ou usando um farm existente, você precisará de:

* Credenciais de administrador local em seus servidores de Federação.
* As credenciais de administrador local em qualquer servidor de grupo de trabalho (não ingressado no domínio) em que você pretende implantar a função de proxy de aplicativo Web.
* O computador no qual você executa o assistente para poder se conectar a qualquer outro computador no qual você deseja instalar AD FS ou proxy de aplicativo Web no usando Gerenciamento Remoto do Windows.

Para obter mais informações, consulte [Configuring SSO with AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federação com o PingFederate
Com a entrada federada, os usuários podem entrar em serviços baseados no Azure AD com suas senhas locais. Embora estejam na rede corporativa, eles nem precisam inserir suas senhas.

Para obter mais informações sobre como configurar o PingFederate para uso com o Azure Active Directory, consulte [integração do PingFederate com o Azure Active Directory e o Office 365](https://www.pingidentity.com/AzureADConnect)

Para obter informações sobre como configurar Azure AD Connect usando o PingFederate, consulte [Azure ad Connect instalação personalizada](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Entrar usando uma versão anterior do AD FS ou uma solução de terceiros
Se você já tiver configurado a entrada na nuvem usando uma versão anterior do AD FS (como AD FS 2,0) ou um provedor de Federação de terceiros, poderá optar por ignorar a configuração de entrada do usuário por meio de Azure AD Connect. Isso permitirá que você obtenha a sincronização mais recente e outros recursos do Azure AD Connect enquanto ainda usa sua solução existente para entrar.

Para obter mais informações, consulte a lista de compatibilidade de Federação de terceiros [do Azure ad](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Entrada e nome UPN do usuário
### <a name="understanding-user-principal-name"></a>Compreendendo o nome principal do usuário
No Active Directory, o sufixo UPN (nome principal de usuário) padrão é o nome DNS do domínio em que a conta de usuário foi criada. Na maioria dos casos, esse é o nome de domínio registrado como o domínio corporativo na Internet. No entanto, você pode adicionar mais sufixos UPN usando Active Directory domínios e relações de confiança.

O UPN do usuário tem o formato username@domain. Por exemplo, para um domínio Active Directory chamado "contoso.com", um usuário chamado João pode ter o UPN "john@contoso.com". O UPN do usuário se baseia no RFC 822. Embora o UPN e o email compartilhem o mesmo formato, o valor do UPN para um usuário pode ou não ser o mesmo que o endereço de email do usuário.

### <a name="user-principal-name-in-azure-ad"></a>Nome principal do usuário no Azure AD
O assistente de Azure AD Connect usa o atributo userPrincipalName ou permite que você especifique o atributo (em uma instalação personalizada) a ser usado no local como o nome principal do usuário no Azure AD. Esse é o valor usado para entrar no Azure AD. Se o valor do atributo userPrincipalName não corresponder a um domínio verificado no Azure AD, o Azure AD o substituirá por um valor default. onmicrosoft.com.

Cada diretório no Azure Active Directory vem com um nome de domínio interno, com o formato contoso.onmicrosoft.com, que permite que você comece a usar o Azure ou outros serviços da Microsoft. Você pode melhorar e simplificar a experiência de entrada usando domínios personalizados. Para obter informações sobre nomes de domínio personalizados no Azure AD e como verificar um domínio, consulte [Adicionar seu nome de domínio personalizado a Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Configuração de início de sessão no Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração de entrada do Azure AD com o Azure AD Connect
A experiência de conexão do Azure AD depende se o Azure AD pode corresponder ao sufixo do nome UPN de um usuário que está sendo sincronizado com um dos domínios personalizados que são verificados no diretório do AD do Azure. Azure AD Connect fornece ajuda enquanto você configura as configurações de entrada do Azure AD, para que a experiência de entrada do usuário na nuvem seja semelhante à experiência local.

Azure AD Connect lista os sufixos UPN definidos para os domínios e tenta corresponder a eles com um domínio personalizado no Azure AD. Em seguida, ele ajuda você com a ação apropriada que precisa ser executada.
A página de entrada do Azure AD lista os sufixos UPN definidos para Active Directory locais e exibe o status correspondente em relação a cada sufixo. Os valores de status podem ser um dos seguintes:

| Estado | Descrição | Ação necessária |
|:--- |:--- |:--- |
| Verificado |Azure AD Connect encontrou um domínio verificado correspondente no Azure AD. Todos os usuários desse domínio podem entrar usando suas credenciais locais. |Não é necessária nenhuma ação. |
| Não verificado |Azure AD Connect encontrou um domínio personalizado correspondente no Azure AD, mas ele não é verificado. O sufixo UPN dos usuários desse domínio será alterado para o sufixo default. onmicrosoft.com após a sincronização se o domínio não for verificado. | [Verifique o domínio personalizado no Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Não adicionado |Azure AD Connect não encontrou um domínio personalizado que correspondesse ao sufixo UPN. O sufixo UPN dos usuários desse domínio será alterado para o sufixo default. onmicrosoft.com se o domínio não for adicionado e verificado no Azure. | [Adicione e verifique um domínio personalizado que corresponde ao sufixo UPN.](../fundamentals/add-custom-domain.md) |

A página de entrada do Azure AD lista os sufixos UPN definidos para Active Directory locais e o domínio personalizado correspondente no Azure AD com o status de verificação atual. Em uma instalação personalizada, agora você pode selecionar o atributo para o nome principal do usuário na página de **entrada do Azure ad** .

![Página de entrada do Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Você pode clicar no botão atualizar para buscar novamente o status mais recente dos domínios personalizados do Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selecionando o atributo para o nome principal do usuário no Azure AD
O atributo userPrincipalName é o atributo que os usuários usam ao entrarem no Azure AD e no Office 365. Você deve verificar os domínios (também conhecidos como sufixos UPN) que são usados no Azure AD antes que os usuários sejam sincronizados.

É altamente recomendável que você mantenha o atributo padrão userPrincipalName. Se esse atributo for não roteável e não puder ser verificado, será possível selecionar outro atributo (email, por exemplo) como o atributo que contém a ID de entrada. Isso é conhecido como a ID alternativa. O valor do atributo de ID alternativo deve seguir o padrão RFC 822. Você pode usar uma ID alternativa com SSO de senha e SSO de Federação como a solução de entrada.

> [!NOTE]
> O uso de uma ID alternativa não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte [Configuring Alternate login id](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Diferentes Estados de domínio personalizados e seus efeitos na experiência de entrada do Azure
É muito importante entender a relação entre os Estados de domínio personalizados em seu diretório do Azure AD e os sufixos UPN definidos no local. Vamos percorrer as diferentes experiências de entrada do Azure possíveis quando você estiver configurando a sincronização usando Azure AD Connect.

Para as informações a seguir, vamos supor que estamos preocupados com o sufixo UPN contoso.com, que é usado no diretório local como parte do UPN, por exemplo user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Configurações expressas/sincronização de hash de senha

| Estado | Efeito na experiência de entrada do usuário do Azure |
|:---:|:--- |
| Não adicionado |Nesse caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório do AD do Azure. Os usuários que têm o UPN local com o sufixo @contoso.com não poderão usar seu UPN local para entrar no Azure. Em vez disso, eles precisarão usar um novo UPN fornecido a eles pelo Azure AD adicionando o sufixo para o diretório padrão do AD do Azure. Por exemplo, se você estiver sincronizando usuários com o diretório do AD do Azure azurecontoso.onmicrosoft.com, o usuário user@contoso.com local receberá um UPN de. user@azurecontoso.onmicrosoft.com |
| Não verificado |Nesse caso, temos um contoso.com de domínio personalizado que é adicionado no diretório do AD do Azure. No entanto, ele ainda não foi verificado. Se você prosseguir com a sincronização de usuários sem verificar o domínio, os usuários receberão um novo UPN pelo Azure AD, assim como no cenário "não adicionado". |
| Verificado |Nesse caso, temos um contoso.com de domínio personalizado que já foi adicionado e verificado no Azure AD para o sufixo UPN. Os usuários poderão usar seu nome UPN local, por exemplo user@contoso.com, para entrar no Azure depois que eles forem sincronizados com o Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS Federação
Você não pode criar uma federação com o domínio default. onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Quando você estiver executando o assistente de Azure AD Connect, se selecionar um domínio não verificado para criar uma federação, Azure AD Connect solicitará que os registros necessários sejam criados onde o DNS está hospedado para o domínio. Para obter mais informações, consulte [verificar o domínio do Azure ad selecionado para Federação](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você selecionou a Federação de opção de entrada do usuário **com AD FS**, deverá ter um domínio personalizado para continuar criando uma federação no Azure AD. Para nossa discussão, isso significa que devemos ter um domínio personalizado contoso.com adicionado no diretório do AD do Azure.

| Estado | Efeito na experiência de entrada do usuário do Azure |
|:---:|:--- |
| Não adicionado |Nesse caso, Azure AD Connect não encontrou um domínio personalizado correspondente para o sufixo UPN contoso.com no diretório do Azure AD. Você precisará adicionar um domínio personalizado contoso.com se precisar que os usuários entrem usando AD FS com seu UPN local (como user@contoso.com). |
| Não verificado |Nesse caso, Azure AD Connect solicita os detalhes apropriados sobre como você pode verificar seu domínio em um estágio posterior. |
| Verificado |Nesse caso, você pode prosseguir com a configuração sem nenhuma ação adicional. |

## <a name="changing-the-user-sign-in-method"></a>Alterando o método de entrada do usuário
Você pode alterar o método de entrada do usuário de Federação, sincronização de hash de senha ou autenticação de passagem usando as tarefas que estão disponíveis no Azure AD Connect após a configuração inicial de Azure AD Connect com o assistente. Execute o assistente de Azure AD Connect novamente e você verá uma lista de tarefas que você pode executar. Selecione **alterar entrada do usuário** na lista de tarefas.

![Alterar início de sessão do utilizador](./media/plan-connect-user-signin/changeusersignin.png)

Na próxima página, será solicitado que você forneça as credenciais do Azure AD.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na página de **entrada do usuário** , selecione a entrada do usuário desejada.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se você estiver apenas fazendo um comutador temporário para a sincronização de hash de senha, marque a caixa de seleção não **converter contas de usuário** . Não marcar a opção converterá cada usuário em federado e poderá levar várias horas.
>
>

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre como [integrar suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).
- Saiba mais sobre os [conceitos de design de Azure ad Connect](plan-connect-design-concepts.md).
