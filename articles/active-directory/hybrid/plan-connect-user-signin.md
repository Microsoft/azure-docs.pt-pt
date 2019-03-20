---
title: 'Azure AD Connect: Sessão do utilizador | Documentos da Microsoft'
description: O Azure AD Connect-sessão do utilizador para definições personalizadas.
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
ms.openlocfilehash: cb44c64540cc461bca4e305f7783f7c6b612591b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996335"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opções do Azure AD Connect utilizador início de sessão
O Azure Active Directory (Azure AD) Connect permite aos utilizadores iniciar sessão nos recursos de nuvem e no local utilizando as mesmas palavras-passe. Este artigo descreve conceitos chave para cada modelo de identidade ajudar a escolher a identidade que pretende utilizar para iniciar sessão no Azure AD.

Se já estiver familiarizado com o modelo de identidade do Azure AD e pretende obter mais informações sobre um método específico, consulte o link apropriado:

* [Sincronização de hash de palavra-passe](#password-hash-synchronization) com [totalmente integrada início de sessão único (SSO)](how-to-connect-sso.md)
* [Autenticação pass-through](how-to-connect-pta.md) com [totalmente integrada início de sessão único (SSO)](how-to-connect-sso.md)
* [SSO Federado (com serviços de Federação do Active Directory (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federação com PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> É importante lembrar-se de que ao configurar a Federação para o Azure AD, estabelecer confiança entre o seu inquilino do Azure AD e os domínios federados. Com este domínio Federado de confiança, os usuários terão acesso aos recursos da cloud do Azure AD no inquilino.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolher o método de início de sessão do utilizador para a sua organização
A primeira decisão da implementação do Azure AD Connect é escolher que método de autenticação aos seus utilizadores irão utilizar para iniciar sessão. É importante certificar-se de que escolher o método certo de que cumpre os requisitos de avançada e de segurança da sua organização. A autenticação é crítica, porque irá validar as identidades de utilizador para aceder às aplicações e dados na cloud. Para escolher o método de autenticação corretas, que é preciso considerar o tempo, a infraestrutura existente, a complexidade e o custo da implementação à sua escolha. Esses fatores são diferentes para cada organização e podem ser alterados ao longo do tempo.

O Azure AD suporta os seguintes métodos de autenticação: 

* **Autenticação da cloud** - quando escolher este método de autenticação do Azure AD processa o processo de autenticação para um início de sessão do utilizador. Com a autenticação na nuvem pode escolher entre duas opções: 
   * **Sincronização de hash de palavra-passe (PHS)** -sincronização de Hash de palavra-passe permite que os utilizadores utilizem o mesmo nome de utilizador e palavra-passe que utilizam no local sem ter de implementar qualquer infraestrutura adicional além do Azure AD Connect. 
   * **Autenticação pass-through (PTA)** -esta opção é semelhante à sincronização de hash de palavra-passe, mas fornece uma validação de palavra-passe simples usando agentes de software no local para organizações com políticas de segurança e conformidade fortes.
* **Autenticação federada** - quando escolher este método de autenticação do Azure AD irá passá o processo de autenticação para um sistema de autenticação confiável separado, como o AD FS ou um sistema de Federação de terceiros, para validar o utilizador estejam início de sessão-no. 

Na maioria das organizações que apenas pretende ativar o utilizador iniciar sessão no Office 365, aplicações SaaS e outros recursos do Azure baseada no AD, recomendamos que a opção de sincronização de hash de palavra-passe predefinida.
 
Para obter informações detalhadas sobre como escolher um método de autenticação, consulte [escolher o método de autenticação correta para a sua solução de identidade híbrida do Azure Active Directory](../../security/azure-ad-choose-authn.md)

### <a name="password-hash-synchronization"></a>Sincronização de hash de palavra-passe
Com a sincronização de hash de palavra-passe, os hashes de palavras-passe do utilizador são sincronizados do Active Directory no local para o Azure AD. Quando as palavras-passe são alteradas ou repostas no local, os hashes de palavra-passe nova são sincronizados com o Azure AD imediatamente para que seus usuários podem sempre usar a mesma palavra-passe para recursos de nuvem e recursos no local. As palavras-passe nunca são enviadas para o Azure AD ou armazenadas no Azure AD em texto não criptografado. Pode utilizar a sincronização de hash de palavra-passe em conjunto com a repetição de escrita de palavras-passe para ativar a gestão personalizada reposição palavra-passe no Azure AD.

Além disso, pode habilitar [SSO totalmente integrado](how-to-connect-sso.md) para usuários em computadores associados a um domínio que estão na rede empresarial. Com início de sessão único, utilizadores ativados apenas tem de introduzir um nome de utilizador para ajudá-los em segurança aceder a recursos na cloud.

![Sincronização de hash de palavra-passe](./media/plan-connect-user-signin/passwordhash.png)

Para obter mais informações, consulte a [sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) artigo.

### <a name="pass-through-authentication"></a>Autenticação pass-through
Com a autenticação pass-through, a senha do usuário é validada com o controlador do Active Directory no local. A palavra-passe não precisa estar presente no Azure AD de nenhuma forma. Isso permite que as políticas no local, tais como restrições de hora de início de sessão, a ser avaliada durante a autenticação com a nuvem de serviços.

Autenticação pass-through usa um agente de simple numa máquina de associados a um domínio do Windows Server 2012 R2 no ambiente no local. Este agente escuta pedidos de validação da palavra-passe. Ele não requer nenhuma porta de entrada seja aberta à Internet.

Além disso, também pode ativar início de sessão único para utilizadores em computadores associados a um domínio que estão na rede empresarial. Com início de sessão único, utilizadores ativados apenas tem de introduzir um nome de utilizador para ajudá-los em segurança aceder a recursos na cloud.
![Autenticação pass-through](./media/plan-connect-user-signin/pta.png)

Para obter mais informações, consulte:
- [Autenticação pass-through](how-to-connect-pta.md)
- [Início de sessão único](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federação que utiliza um farm de novo ou existente com o AD FS no Windows Server 2012 R2
Com o início de sessão federado, os utilizadores podem iniciar sessão serviços com base no AD do Azure com suas senhas locais. Enquanto eles estão na rede empresarial, até mesmo não tem de introduzir as palavras-passe. Ao utilizar a opção de federação com o AD FS, pode implementar um farm de novo ou existente com o AD FS no Windows Server 2012 R2. Se optar por especificar um farm existente, o Azure AD Connect configura a confiança entre o seu farm e o Azure AD para que os utilizadores podem iniciar sessão.

<center>

![Federação com o AD FS no Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Implementar a Federação com o AD FS no Windows Server 2012 R2

Se estiver implantando um novo farm, tem de:

* Um servidor Windows Server 2012 R2 para o servidor de Federação.
* Um servidor Windows Server 2012 R2 para o Proxy de aplicações Web.
* Um ficheiro. pfx com um certificado SSL para o nome do serviço de Federação pretendido. Por exemplo: fs.contoso.com.

Se estiver a implementar um novo farm ou a utilizar um farm existente, tem de:

* Credenciais de administrador local nos seus servidores de Federação.
* Credenciais de administrador local em qualquer servidor de grupo de trabalho (não associados a um domínio) que pretende implementar a função de Proxy de aplicações Web no.
* A máquina que executa o assistente em ser capaz de ligar a quaisquer outras máquinas que deseja instalar o AD FS ou o Proxy de aplicações Web no utilizando a gestão remota do Windows.

Para obter mais informações, consulte [configurar o SSO com o AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federação com o PingFederate
Com o início de sessão federado, os utilizadores podem iniciar sessão serviços com base no AD do Azure com suas senhas locais. Enquanto eles estão na rede empresarial, até mesmo não tem de introduzir as palavras-passe.

Para obter mais informações sobre como configurar o PingFederate para utilização com o Azure Active Directory, consulte [PingFederate integração com o Azure Active Directory e do Office 365](https://www.pingidentity.com/AzureADConnect)

Para obter informações sobre como configurar o Azure AD Connect com PingFederate, consulte [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Inicie sessão com uma versão anterior do AD FS ou de uma solução de terceiros
Se já tiver configurado início de sessão na cloud ao utilizar uma versão anterior do AD FS (por exemplo, o AD FS 2.0) ou um fornecedor de Federação de terceiros, pode optar por ignorar a configuração de sessão do utilizador através do Azure AD Connect. Isso permitirá que obter a sincronização mais recente e outros recursos do Azure AD Connect ao mesmo tempo que continua a utilizar a solução existente para início de sessão.

Para obter mais informações, consulte a [lista de compatibilidades de Federação de terceiros do Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Sessão do utilizador e nome principal de utilizador
### <a name="understanding-user-principal-name"></a>Nome principal de utilizador de compreensão
No Active Directory, o sufixo de nome principal (UPN) de utilizador predefinido é o nome DNS do domínio em que a conta de utilizador foi criada. Na maioria dos casos, este é o nome de domínio que está registado como domínio da empresa na Internet. No entanto, pode adicionar mais sufixos do UPN com domínios do Active Directory e relações de confiança.

O UPN do utilizador tem o formato username@domain. Por exemplo, para um domínio do Active Directory com o nome "contoso.com", um utilizador com o nome João pode ter o UPN "john@contoso.com". O UPN do utilizador é baseado em RFC 822. Embora o UPN e o e-mail compartilham o mesmo formato, o valor do UPN para um utilizador pode ou não ser o mesmo que o endereço de e-mail do utilizador.

### <a name="user-principal-name-in-azure-ad"></a>Nome principal de utilizador no Azure AD
O Assistente do Azure AD Connect utiliza o atributo userPrincipalName ou permite-lhe especificar o atributo (numa instalação personalizada) a ser utilizado no local como o nome principal de utilizador no Azure AD. Este é o valor que é utilizado para iniciar sessão com o Azure AD. Se o valor do atributo userPrincipalName não corresponde a um domínio verificado no Azure AD, em seguida, do Azure AD substitui por um predefinido. onmicrosoft.com valor.

Cada diretório no Azure Active Directory é fornecido com um nome de domínio interno, com o formato de contoso.onmicrosoft.com, que lhe permite começar a utilizar o Azure ou outros serviços Microsoft. Pode melhorar e simplificar a experiência de início de sessão através da utilização de domínios personalizados. Para obter informações sobre nomes de domínio personalizados no Azure AD e como verificar um domínio, consulte [adicionar o seu nome de domínio personalizado ao Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Configuração do início de sessão do Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração do Azure AD início de sessão com o Azure AD Connect
A experiência de início de sessão do Azure AD depende se o Azure AD pode corresponder o sufixo de nome principal de utilizador de um utilizador que está a ser sincronizado para um dos domínios personalizados que são verificados no diretório do Azure AD. O Azure AD Connect fornece ajuda enquanto configura o início de sessão definições do Azure AD, para que a experiência de início de sessão do usuário na cloud é semelhante à experiência no local.

O Azure AD Connect lista os sufixos do UPN que são definidos para os domínios e tenta correspondê-los com um domínio personalizado no Azure AD. Em seguida, ajuda-o com as medidas adequadas, que é necessário realizar.
Página de início de sessão do Azure AD lista os sufixos do UPN que são definidos para o Active Directory no local e apresenta o estado correspondente em relação a cada sufixo. Os valores de estado podem ser um dos seguintes:

| Estado | Descrição | Ação necessária |
|:--- |:--- |:--- |
| Verificado |O Azure AD Connect foi encontrada que uma correspondência de domínio verificado no Azure AD. Todos os utilizadores para este domínio podem iniciar sessão através das respetivas credenciais no local. |Não é necessária nenhuma ação. |
| Não verificado |O Azure AD Connect foi encontrado um domínio personalizado correspondente no Azure AD, mas não é verificado. O sufixo UPN dos utilizadores deste domínio será alterado para a predefinição. sufixo onmicrosoft.com após a sincronização, se o domínio não está verificado. | [Verifique se o domínio personalizado no Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Não adicionado |O Azure AD Connect não foram encontradas num domínio personalizado que correspondessem ao sufixo UPN. O sufixo UPN dos utilizadores deste domínio será alterado para a predefinição. sufixo onmicrosoft.com se o domínio não estiver adicionado e verificado no Azure. | [Adicionar e verificar um domínio personalizado que corresponda ao sufixo de UPN.](../fundamentals/add-custom-domain.md) |

Página de início de sessão do Azure AD lista os sufixos do UPN definidos para o Active Directory no local e o domínio personalizado correspondente no Azure AD com o atual estado de verificação. Numa instalação personalizada, agora, pode selecionar o atributo do nome principal de utilizador sobre o **início de sessão no Azure AD** página.

![Azure página de início de sessão do AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Pode clicar no botão Atualizar para obter novamente o estado mais recente dos domínios personalizados do Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selecionar o atributo do nome principal de utilizador no Azure AD
O atributo userPrincipalName é o atributo que os utilizadores utilizam quando iniciam sessão com o Azure AD e o Office 365. Deve verificar os domínios (também conhecido como sufixos do UPN), que são utilizados no Azure AD antes dos utilizadores são sincronizados.

Recomendamos vivamente que mantenha o atributo predefinido userPrincipalName. Se este atributo é nonroutable e não pode ser verificado, em seguida, é possível selecionar outro atributo (e-mail, por exemplo) como o atributo que contém o ID de início de sessão. Isso é conhecido como o ID alternativo. O valor do atributo ID alternativo tem de seguir o padrão de RFC 822. Pode utilizar um ID alternativo com o SSO de palavra-passe e o Federação SSO como a solução de início de sessão.

> [!NOTE]
> Utilizar um ID alternativo não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte [configurar o ID de início de sessão alternativo](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Estados de domínio personalizado diferentes e seus efeitos sobre a experiência de início de sessão do Azure
É muito importante compreender a relação entre os Estados de domínio personalizado no diretório do Azure AD e os sufixos do UPN que estão definidos no local. Vamos percorrer as possíveis Azure início de sessão experiências diferentes quando estiver configurando a sincronização com o Azure AD Connect.

Para obter as informações seguintes, vamos supor que estamos preocupados com o contoso.com de sufixo UPN, que serve no diretório no local como parte do UPN – por exemplo user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Expressar a sincronização de hash de definições/palavra-passe

| Estado | Efeito na experiência do usuário, o início de sessão do Azure |
|:---:|:--- |
| Não adicionado |Neste caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Os utilizadores que têm o UPN no local com o sufixo @contoso.com não será possível utilizar o respetivo UPN no local para iniciar sessão no Azure. Em vez disso, eles terá que usar um UPN novo, que é fornecido a eles, adicionando o sufixo de diretório do Azure AD predefinido pelo Azure AD. Por exemplo, se estiver a sincronizar os utilizadores azurecontoso.onmicrosoft.com de diretório do Azure AD, em seguida, o utilizador no local user@contoso.com receberão um UPN de user@azurecontoso.onmicrosoft.com. |
| Não verificado |Neste caso, temos um contoso.com de domínio personalizado é adicionado ao diretório do Azure AD. No entanto, ainda não é verificado. Se prosseguir com a sincronizar os utilizadores sem verificar o domínio, em seguida, os utilizadores serão atribuídos um UPN novo pelo Azure AD, tal como no cenário de "Não adicionar". |
| Verificado |Neste caso, temos um contoso.com de domínio personalizado que já tenha adicionado e verificados no Azure AD para o sufixo UPN. Os utilizadores poderão utilizar o nome principal de utilizador seus locais, por exemplo user@contoso.com, para iniciar sessão no Azure depois de eles estão sincronizados com o Azure AD. |

###### <a name="ad-fs-federation"></a>Federação do AD FS
Não é possível criar uma federação com o padrão. domínio onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Quando estiver a executar o Assistente do Azure AD Connect, se selecionar um domínio não verificado para criar uma federação com o, em seguida, o Azure AD Connect pede-lhe com os registos necessários a ser criada em que o seu DNS está hospedado para o domínio. Para obter mais informações, consulte [verificar o domínio do Azure AD selecionado para Federação](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se tiver selecionado a opção de início de sessão de utilizador **federação com o AD FS**, em seguida, tem de ter um domínio personalizado para continuar a criação de uma federação no Azure AD. Para nossa discussão, isso significa que temos um contoso.com de domínio personalizado adicionado no diretório do Azure AD.

| Estado | Efeito sobre o utilizador a experiência de início de sessão do Azure |
|:---:|:--- |
| Não adicionado |Neste caso, o Azure AD Connect não localizar um domínio personalizado correspondente para a contoso.com de sufixo UPN no diretório do Azure AD. Precisa adicionar um domínio personalizado contoso.com, se precisar dos utilizadores iniciem sessão através da utilização do AD FS com o respetivo UPN no local (como user@contoso.com). |
| Não verificado |Neste caso, o Azure AD Connect irá pedir-lhe com detalhes adequados sobre como pode verificar o seu domínio num estágio posterior. |
| Verificado |Neste caso, pode prosseguir com a configuração sem qualquer ação adicional. |

## <a name="changing-the-user-sign-in-method"></a>Alterar o método de início de sessão do utilizador
Pode alterar o método de início de sessão do utilizador de Federação, a sincronização de hash de palavra-passe ou a autenticação pass-through utilizando as tarefas que estão disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect com o assistente. Execute novamente o Assistente do Azure AD Connect, e verá uma lista de tarefas que pode realizar. Selecione **alterar utilizador inicie sessão** na lista de tarefas.

![Alterar início de sessão do utilizador](./media/plan-connect-user-signin/changeusersignin.png)

Na página seguinte, são-lhe pedido para fornecer as credenciais para o Azure AD.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Sobre o **sessão do utilizador** , selecione o usuário que desejarem início de sessão.

![Ligar ao Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se um comutador temporário apenas estiver a efetuar a sincronização de hash de palavra-passe, em seguida, selecione o **não converter contas de utilizador** caixa de verificação. Não marcando a opção converterá a cada utilizador federado e pode demorar várias horas.
>
>

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
- Saiba mais sobre [conceitos de design do Azure AD Connect](plan-connect-design-concepts.md).
