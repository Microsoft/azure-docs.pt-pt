---
title: O Azure AD Connect - gestão do AD FS e personalização | Documentos da Microsoft
description: Gestão do AD FS com o Azure AD Connect e personalização do AD FS início de sessão experiência do usuário com o Azure AD Connect e o PowerShell.
keywords: AD FS, ADFS, AD FS gestão, o AAD Connect, Connect, início de sessão, do AD FS personalização, reparar o Federação de confiança, do Office 365, entidade confiadora
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 021e13dafcc659337d4096a068e224312e69db1b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870946"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerir e personalizar os serviços de Federação do Active Directory com o Azure AD Connect
Este artigo descreve como gerir e personalizar os serviços de Federação do Active Directory (AD FS) com o Azure Active Directory (Azure AD) Connect. Ele também inclui outras tarefas de AD FS comuns que poderá ter de fazer para uma configuração completa de um farm do AD FS.

| Tópico | O que abrange |
|:--- |:--- |
| **Gerir o AD FS** | |
| [Repare a confiança](#repairthetrust) |Como reparar a confiança da federação com o Office 365. |
| [Federar com o Azure AD com o ID de início de sessão alternativo](#alternateid) | Configurar a Federação com o ID de início de sessão alternativo  |
| [Adicionar um servidor do AD FS](#addadfsserver) |Como expandir um farm do AD FS com um servidor do AD FS adicional. |
| [Adicionar um servidor de Proxy de aplicações Web do AD FS](#addwapserver) |Como expandir um farm do AD FS com um servidor de Proxy de aplicações Web (WAP) adicional. |
| [Adicionar um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado SSL](how-to-connect-fed-ssl-update.md)| Como atualizar o certificado SSL para um farm do AD FS. |
| **Personalizar o AD FS** | |
| [Adicionar um logótipo da empresa personalizado ou a ilustração](#customlogo) |Como personalizar uma página de início de sessão do AD FS com um logótipo da empresa e a ilustração. |
| [Adicione uma descrição de início de sessão](#addsignindescription) |Como adicionar uma descrição de página de início de sessão. |
| [Modificar regras de afirmações do AD FS](#modclaims) |Como modificar afirmações do AD FS para vários cenários de Federação. |

## <a name="manage-ad-fs"></a>Gerir o AD FS
Pode efetuar várias tarefas relacionadas com FS do AD no Azure AD Connect, sem intervenção do utilizador mínima, utilizando o Assistente do Azure AD Connect. Depois de terminar a instalação do Azure AD Connect ao executar o assistente, pode executar o assistente novamente para efetuar tarefas adicionais.

## <a name="repairthetrust"></a>Repare a confiança 
Pode utilizar o Azure AD Connect para verificar o estado de funcionamento atual do AD FS e do Azure AD confia e executar as ações apropriadas para reparar a confiança. Siga estes passos para reparar o seu Azure AD e fidedignidade do AD FS.

1. Selecione **AAD de reparação e a confiança do ADFS** na lista de tarefas adicionais.
   ![Reparar o AAD e o AD FS de confiança](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Sobre o **ligar para o Azure AD** página, forneça as suas credenciais de administrador global para o Azure AD e clique em **próxima**.
   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Sobre o **credenciais de acesso remoto** página, introduza as credenciais de administrador do domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Depois de clicar em **seguinte**, o Azure AD Connect verifica o estado de funcionamento do certificado e mostra todos os problemas.

    ![Estado de certificados](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    O **pronto para configurar** página mostra a lista de ações que serão executadas para reparar a confiança.

    ![Preparado para configurar](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Clique em **instalar** para reparar a confiança.

> [!NOTE]
> Reparação única do AD Connect pode do Azure ou tomar medidas relacionadas com certificados que são assinados automaticamente. O Azure AD Connect não consegue reparar os certificados de terceiros.

## <a name="alternateid"></a>Federar com o Azure AD através do AlternateID 
Recomenda-se que o nome de Principal de utilizador no local e na cloud Nome Principal de utilizador se mantêm a mesma. Se o UPN no local utiliza um domínio não encaminháveis internos (ex. Contoso. local) ou não pode ser alterado devido a dependências de aplicações locais, recomendamos a configuração de ID de início de sessão alternativo. ID de início de sessão alternativo permite-lhe configurar uma experiência de início de sessão em que os utilizadores podem iniciar sessão com um atributo que não seja o respetivo UPN, como o mail. A escolha de nome Principal de utilizador no Azure AD Connect a predefinição é o atributo userPrincipalName no Active Directory. Se escolhe qualquer outro atributo de nome Principal de utilizador e estiver a federar com o AD FS, em seguida, o Azure AD Connect irá configurar o AD FS para o ID de início de sessão alternativo. Um exemplo de escolher um atributo diferente para o nome Principal de utilizador é apresentado abaixo:

![Seleção de atributo ID alternativa](./media/how-to-connect-fed-management/attributeselection.png)

Configurar o ID de início de sessão alternativo para o AD FS consiste em dois passos principais:
1. **Configurar o conjunto certo de emissão de afirmações**: As regras de afirmação de emissão a confiança de entidade confiadora do Azure AD são modificadas para utilizar o atributo UserPrincipalName selecionado como o ID alternativo do utilizador.
2. **Ativar o ID de início de sessão alternativo na configuração do AD FS**: A configuração do AD FS é atualizada para que o AD FS pode procurar utilizadores nas florestas adequadas com o ID alternativo. Esta configuração é suportada para o AD FS no Windows Server 2012 R2 (com o KB2919355) ou posterior. Se os servidores AD FS 2012 R2, do Azure AD Connect verifica a presença do KB necessária. Se não for detetado o KB, um aviso será apresentado após a conclusão da configuração, conforme mostrado abaixo:

    ![Aviso de ausentes KB no 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Para retificar a configuração em caso de KB em falta, instale o necessários [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) e, em seguida, reparar a confiança utilizando [reparar AAD e confiança do AD FS](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre alternateID e os passos para configurar manualmente, leia [configurar ID de início de sessão alternativo](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Adicionar um servidor do AD FS 

> [!NOTE]
> Para adicionar um servidor do AD FS, o Azure AD Connect requer o certificado PFX. Por conseguinte, pode efetuar esta operação apenas se tiver configurado o farm do AD FS com o Azure AD Connect.

1. Selecione **implementar um servidor de Federação adicionais**e clique em **próxima**.

   ![Servidor de Federação adicionais](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Sobre o **ligar para o Azure AD** página, introduza as suas credenciais de administrador global para o Azure AD e clique em **próxima**.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Fornece credenciais de administrador de domínio.

   ![Credenciais de administrador de domínio](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. O Azure AD Connect solicita a palavra-passe do ficheiro PFX que forneceu ao configurar o seu novo farm do AD FS com o Azure AD Connect. Clique em **introduza a palavra-passe** para fornecer a palavra-passe para o ficheiro PFX.

   ![Palavra-passe de certificado](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Especificar certificado SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Sobre o **servidores do AD FS** página, introduza o nome do servidor ou endereço IP a ser adicionado ao farm do AD FS.

   ![Servidores AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Clique em **próxima**e siga o último **configurar** página. Após terminar o Azure AD Connect ao adicionar os servidores ao farm do AD FS, terá a opção de verificar a conectividade.

   ![Preparado para configurar](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalação concluída](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Adicionar um servidor do WAP do AD FS 

> [!NOTE]
> Para adicionar um servidor WAP, o Azure AD Connect requer o certificado PFX. Por conseguinte, só pode efetuar esta operação se tiver configurado o farm do AD FS com o Azure AD Connect.

1. Selecione **Proxy de aplicações Web implementar** na lista de tarefas disponíveis.

   ![Implementar o Proxy de aplicações Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global do Azure.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Sobre o **certificado SSL especificar** , indique a palavra-passe para o ficheiro PFX que indicou quando configurou o farm do AD FS com o Azure AD Connect.
   ![Palavra-passe do certificado](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Especificar certificado SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adicione o servidor para ser adicionado como um servidor do WAP. Uma vez que o servidor do WAP não pode estar associado ao domínio, o assistente solicita credenciais administrativas para o servidor que está a ser adicionado.

   ![Credenciais administrativas do servidor](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Sobre o **as credenciais de confiança do Proxy** , indique as credenciais administrativas para configurar o proxy de confiança e acedam ao servidor primário do farm AD FS.

   ![Credenciais de confiança do proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Sobre o **pronto para configurar** página, o assistente mostra a lista de ações que serão executadas.

   ![Preparado para configurar](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Clique em **instalar** para concluir a configuração. Depois da configuração estiver concluída, o assistente dá-lhe a opção de verificar a conectividade aos servidores. Clique em **Verifique se** para verificar a conectividade.

   ![Instalação concluída](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Adicionar um domínio federado 

É fácil adicionar um domínio a ser Federado com o Azure AD com o Azure AD Connect. O Azure AD Connect adiciona o domínio para Federação e modifica as regras de afirmação para refletir corretamente o emissor quando tem vários domínios federados com o Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **adicionar um domínio do Azure AD**.

   ![Adicionais domínio do Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na página seguinte do assistente, forneça as credenciais de administrador global para o Azure AD.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Sobre o **credenciais de acesso remoto** , indique as credenciais de administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na página seguinte, o assistente fornece uma lista de domínios do Azure AD que pode federar o seu diretório no local com. Escolha o domínio na lista.

   ![Domínio do Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornece-lhe as informações apropriadas sobre mais ações que o assistente executará e o impacto da configuração. Em alguns casos, se selecionar um domínio que ainda não está a ser verificado no Azure AD, o assistente fornece informações para ajudar a verificar o domínio. Ver [adicionar o seu nome de domínio personalizado ao Azure Active Directory](../active-directory-domains-add-azure-portal.md) para obter mais detalhes.

5. Clique em **Seguinte**. O **pronto para configurar** página mostra a lista de ações que o Azure AD Connect irá executar. Clique em **instalar** para concluir a configuração.

   ![Preparado para configurar](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Utilizadores do domínio federado adicionado têm de ser sincronizados antes que eles serão capazes de iniciar sessão com o Azure AD.

## <a name="ad-fs-customization"></a>Personalização do AD FS
As secções seguintes fornecem detalhes sobre algumas das tarefas comuns que poderá ter de realizar ao personalizar a página de início de sessão do AD FS.

## <a name="customlogo"></a>Adicionar um logótipo da empresa personalizado ou a ilustração 
Para alterar o logótipo da empresa que é apresentado no **início de sessão** página, utilize o seguinte cmdlet do Windows PowerShell e sintaxe.

> [!NOTE]
> As dimensões recomendadas para o logótipo são 260 x 35 a \@ 96 dpi com um tamanho de ficheiro não superior a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> O *TargetName* é necessário o parâmetro. O tema predefinido que é lançado com o AD FS com o nome predefinido.

## <a name="addsignindescription"></a>Adicione uma descrição de início de sessão 
Para adicionar uma descrição de página de início de sessão para o **página de início de sessão**, utilize o seguinte cmdlet do Windows PowerShell e sintaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modificar regras de afirmações do AD FS 
AD FS suporta uma linguagem de afirmação avançado que pode utilizar para criar regras de afirmação personalizadas. Para obter mais informações, consulte [a função de linguagem de regras de afirmação](https://technet.microsoft.com/library/dd807118.aspx).

As secções seguintes descrevem como pode escrever regras personalizadas para alguns cenários relacionados com o Azure AD e AD FS Federação.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID imutável condicional num valor que estejam presentes no atributo
O Azure AD Connect permite-lhe especificar um atributo a ser utilizado como uma âncora de origem quando objetos são sincronizados com o Azure AD. Se o valor no atributo personalizado não está vazio, poderá querer emitir uma afirmação de ID imutável.

Por exemplo, poderá selecionar **ms-ds-consistencyguid** como o atributo de âncora de origem e problema **ImmutableID** como **ms-ds-consistencyguid** caso o atributo tem um valor em relação a ele. Se não houver nenhum valor com o atributo, emitir **objectGuid** como o ID imutável. Pode construir o conjunto de regras de afirmação personalizada, conforme descrito na secção seguinte.

**Regra 1: Atributos de consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Esta regra, estiver a consultar os valores dos **ms-ds-consistencyguid** e **objectGuid** para o utilizador do Active Directory. Altere o nome de arquivo para um nome de arquivo apropriado na sua implementação do AD FS. Também altere o tipo de afirmações para uma boa afirmações de tipo para a Federação, conforme definido para **objectGuid** e **ms-ds-consistencyguid**.

Além disso, utilizando **adicione** e não **problema**, evitar a adição de um problema de saída para a entidade e pode utilizar os valores como valores intermediários. Irá emitir afirmações de numa regra posterior depois de estabelecer o qual o valor para utilizar como o ID imutável.

**Regra 2: Verificar se o ms-ds-consistencyguid existe para o utilizador**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regra define um sinalizador temporário chamado **idflag** que está definido como **useguid** se não houver nenhum **ms-ds-consistencyguid** preenchidos para o utilizador. A lógica por trás disso é o fato de que o AD FS não permite afirmações vazias. Assim, quando adicionar afirmações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid na regra 1, acaba tendo uma **msdsconsistencyguid** afirmação apenas se o valor é preenchido para o utilizador. Se não estiver preenchida, o AD FS vê que terão um valor vazio e ignora-lo imediatamente. Todos os objetos terão **objectGuid**, por isso, essa afirmação sempre estará lá após 1 de regra é executada.

**Regra 3: Emitir ms-ds-consistencyguid como ID imutável, se estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Esta é uma implícita **Exist** verificar. Se o valor para a afirmação existe, em seguida, emitir que como o ID imutável. O exemplo anterior utiliza o **nameidentifier** de afirmação. Terá de alterá-la para o tipo de afirmação apropriado para o ID imutável no seu ambiente.

**Regra de 4: Emitir objectGuid como ID imutável se ms-ds-consistencyGuid não estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Esta regra, simplesmente está verificando o sinalizador temporário **idflag**. Decida se irá emita a afirmação com base no respetivo valor.

> [!NOTE]
> A sequência dessas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN

Pode adicionar mais de um domínio a ser Federado com o Azure AD Connect, conforme descrito em [adicionar um novo domínio federado](how-to-connect-fed-management.md#addfeddomain). A versão do Azure AD Connect 1.1.553.0 e a versão mais recente cria a regra de afirmação correto para issuerID automaticamente. Se não é possível utilizar o Azure AD Connect versão 1.1.553.0 ou mais recente, recomenda-se que [as regras de afirmação do Azure AD RPT](https://aka.ms/aadrptclaimrules) ferramenta é utilizada para gerar e defina regras de afirmação correto para a fidedignidade de entidade confiadora do Azure AD.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [opções de início de sessão do utilizador](plan-connect-user-signin.md).
