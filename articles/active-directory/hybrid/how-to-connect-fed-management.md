---
title: Azure AD Connect - Gestão e personalização AD FS Microsoft Docs
description: Gestão aD FS com Azure AD Connect e personalização da experiência de entrada de ad FS do utilizador com Azure AD Connect e PowerShell.
keywords: AD FS, ADFS, Gestão AD FS, AAD Connect, Connect, sign-in, Personalização AD FS, reparação, O365, federação, partido de confiança
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
ms.openlocfilehash: 7249f2077666530964afa16ef47d69731cee846a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261596"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerir e personalizar serviços da Federação de Diretórios Ativos utilizando o Azure AD Connect
Este artigo descreve como gerir e personalizar os Serviços da Federação de Diretórios Ativos (AD FS) utilizando o Azure Ative Directory (Azure AD) Connect. Também inclui outras tarefas comuns da AD FS que você pode precisar fazer para uma configuração completa de uma fazenda AD FS.

| Tópico | O que cobre |
|:--- |:--- |
| **Gerir AD FS** | |
| [Reparar a confiança](#repairthetrust) |Como reparar a confiança da federação com o Escritório 365. |
| [Federato com Azure AD usando identificação de login alternativo](#alternateid) | Configure federação usando id de login alternativo  |
| [Adicione um servidor AD FS](#addadfsserver) |Como expandir uma quinta AD FS com um servidor AD FS adicional. |
| [Adicione um servidor proxy de aplicação web aD FS](#addwapserver) |Como expandir uma quinta AD FS com um servidor adicional de Proxy de Aplicações Web (WAP). |
| [Adicione um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado SSL](how-to-connect-fed-ssl-update.md)| Como atualizar o certificado SSL para uma exploração aD FS. |
| **Personalizar AD Fs** | |
| [Adicione um logotipo ou ilustração personalizado da empresa](#customlogo) |Como personalizar uma página de login AD FS com um logotipo da empresa e ilustração. |
| [Adicione uma descrição de sessão](#addsignindescription) |Como adicionar uma descrição da página de inscrição. |
| [Modificar as regras de reclamação da AD FS](#modclaims) |Como modificar as reivindicações da AD FS para vários cenários da federação. |

## <a name="manage-ad-fs"></a>Gerir AD FS
Pode executar várias tarefas relacionadas com AD FS no Azure AD Connect com a mínima intervenção do utilizador utilizando o assistente Azure AD Connect. Depois de terminar de instalar o Azure AD Connect executando o assistente, pode executar o assistente novamente para executar tarefas adicionais.

## <a name="repairthetrust"></a>Reparar a confiança 
Pode utilizar o Azure AD Connect para verificar a saúde atual do fundo AD FS e Azure AD e tomar as medidas adequadas para reparar o fundo. Siga estes passos para reparar a sua confiança Azure AD e AD FS.

1. Selecione **Repair AAD e ADFS Trust** na lista de tarefas adicionais.
   ![Reparação AAD e](./media/how-to-connect-fed-management/RepairADTrust1.PNG) ADFS Trust

2. Na página **'Connect to Azure AD',** forneça as suas credenciais de administrador global para a AD Azure e clique em **Next**.
   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na página de credenciais de **acesso Remoto,** introduza as credenciais para o administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Depois de clicar em **Next**, o Azure AD Connect verifica a saúde do certificado e mostra quaisquer problemas.

    ![Estado dos certificados](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    A página **Ready to configure** mostra a lista de ações que serão executadas para reparar o fundo.

    ![Preparado para configurar](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Clique **em Instalar** para reparar a confiança.

> [!NOTE]
> O Azure AD Connect só pode reparar ou atuar em certificados auto-assinados. O Azure AD Connect não consegue reparar certificados de terceiros.

## <a name="alternateid"></a>Federato com Anúncio Azure usando O Id Alternativo 
Recomenda-se que o nome principal do utilizador no local (UPN) e o nome principal do utilizador em nuvem sejam mantidos o mesmo. Se a UPN no local utilizar um domínio não repreensível (ex. Contoso.local) ou não pode ser alterado devido às dependências de aplicação locais, recomendamos a criação de identificação de login alternativa. O ID de login alternativo permite configurar uma experiência de login onde os utilizadores podem iniciar sessão com um atributo diferente do seu UPN, como o correio. A escolha para o nome principal do utilizador no Azure AD Connect predefinido para o atributo do userPrincipalName no Diretório Ativo. Se escolher qualquer outro atributo para o Nome Principal do Utilizador e estiver a federar usando AD FS, então o Azure AD Connect configurará AD FS para identificação de login alternativa. Um exemplo de escolha de um atributo diferente para o Nome Principal do Utilizador é mostrado abaixo:

![Seleção alternativa de atributo seleção de atributos de ID](./media/how-to-connect-fed-management/attributeselection.png)

Configurar o ID de login alternativo para AD FS consiste em dois passos principais:
1. **Configure o conjunto certo de reclamações de emissão**: As regras de reclamação de emissão no fundo de confiança da ad ad ida e volta para a AD Azure são modificadas para utilizar o atributo userprincipalname selecionado como id alternativo do utilizador.
2. **Ativar o ID de login alternativo na configuração AD FS**: A configuração AD FS é atualizada para que o AD FS possa procurar utilizadores nas florestas apropriadas utilizando o ID alternativo. Esta configuração é suportada para AD FS no Windows Server 2012 R2 (com KB2919355) ou posteriormente. Se os servidores AD FS forem 2012 R2, o Azure AD Connect verifica a presença do KB necessário. Se o KB não for detetado, será apresentado um aviso após a configuração estar concluída, como mostrado abaixo:

    ![Aviso por falta de KB em 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Para retificar a configuração em caso de falta de KB, instale o [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) necessário e, em seguida, repare o fundo utilizando [a Repair AAD e a AD FS Trust](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre id alternativo e passos para configurar manualmente, leia [Configurar o ID de login alternativo](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Adicione um servidor AD FS 

> [!NOTE]
> Para adicionar um servidor AD FS, o Azure AD Connect requer o certificado PFX. Por isso, só pode realizar esta operação se configurar a quinta AD FS utilizando o Azure AD Connect.

1. Selecione **Implementar um servidor da Federação adicional,** e clique em **Seguinte**.

   ![Servidor adicional da federação](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na página **'Connect to Azure AD',** introduza as credenciais do administrador global para a AD Azure e clique em **Next**.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Forneça as credenciais do administrador de domínio.

   ![Credenciais de administrador de domínio](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. O Azure AD Connect pede a palavra-passe do ficheiro PFX que forneceu enquanto configura a sua nova quinta AD FS com o Azure AD Connect. Clique **em Inserir Palavra-passe** para fornecer a palavra-passe para o ficheiro PFX.

   ![Senha de certificado](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Especificar certificado SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na página **AD FS Servers,** introduza o nome do servidor ou endereço IP a adicionar à quinta AD FS.

   ![Servidores AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Clique em **Seguinte**, e passe pela página final de **Configuração.** Depois do Azure AD Connect ter terminado de adicionar os servidores à quinta AD FS, será-lhe dada a opção de verificar a conectividade.

   ![Preparado para configurar](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalação concluída](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Adicione um servidor AD FS WAP 

> [!NOTE]
> Para adicionar um servidor WAP, o Azure AD Connect requer o certificado PFX. Portanto, só pode executar esta operação se configurar a quinta AD FS utilizando o Azure AD Connect.

1. Selecione Implementar procuração de **aplicações web** na lista de tarefas disponíveis.

   ![Implementar procuração de aplicações web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global azure.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na página de **certificado SSL Especificação,** forneça a palavra-passe para o ficheiro PFX que forneceu quando configurar a quinta AD FS com o Azure AD Connect.
   ](./media/how-to-connect-fed-management/WapServer3.PNG) de senha de certificado de ![

    ![Especificar certificado SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adicione o servidor a ser adicionado como um servidor WAP. Como o servidor WAP pode não ser unido ao domínio, o assistente pede credenciais administrativas para o servidor que está a ser adicionado.

   ![Credenciais de servidor administrativo](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na página de **credenciais de confiança proxy,** forneça credenciais administrativas para configurar o fundo proxy e aceder ao servidor primário na fazenda AD FS.

   ![Credenciais de confiança proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na página **Ready to configure,** o assistente mostra a lista de ações que serão executadas.

   ![Preparado para configurar](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Clique **em Instalar** para terminar a configuração. Depois de concluída a configuração, o assistente dá-lhe a opção de verificar a conectividade com os servidores. Clique em **Verificar** a conectividade.

   ![Instalação concluída](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Adicione um domínio federado 

É fácil adicionar um domínio para ser federado com Azure AD usando Azure AD Connect. O Azure AD Connect adiciona o domínio para a federação e modifica as regras de reclamação para refletir corretamente o emitente quando se tem vários domínios federados com AD Azure.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar um domínio AD Azure adicional**.

   ![Domínio Adicional da AD Azure](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na página seguinte do assistente, forneça as credenciais de administrador global para a AD Azure.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na página de credenciais de **acesso Remoto,** forneça as credenciais do administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na página seguinte, o assistente fornece uma lista de domínios DaD Azure com os quais pode federatar o seu diretório no local. Escolha o domínio da lista.

   ![Domínio DaA Azure](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornece-lhe informações apropriadas sobre outras ações que o assistente irá tomar e o impacto da configuração. Em alguns casos, se selecionar um domínio que ainda não esteja verificado em Azure AD, o assistente fornece-lhe informações para ajudá-lo a verificar o domínio. Consulte Adicionar o seu nome de [domínio personalizado ao Diretório Ativo Azure](../active-directory-domains-add-azure-portal.md) para mais detalhes.

5. Clique em **Seguinte**. A página **Ready to configure** mostra a lista de ações que o Azure AD Connect irá executar. Clique **em Instalar** para terminar a configuração.

   ![Preparado para configurar](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Os utilizadores do domínio federado adicionado devem ser sincronizados antes de poderem iniciar sessão no Azure AD.

## <a name="ad-fs-customization"></a>Personalização AD FS
As seguintes secções fornecem detalhes sobre algumas das tarefas comuns que poderá ter de executar quando personalizar a sua página de inscrição AD FS.

## <a name="customlogo"></a>Adicione um logotipo ou ilustração personalizado da empresa 
Para alterar o logótipo da empresa que está exposto na página **de Iniciar sessão,** utilize o seguinte cmdlet e sintaxe do Windows PowerShell.

> [!NOTE]
> As dimensões recomendadas para o logótipo são de 260 x 35 \@ 96 dpi com um tamanho de ficheiro não superior a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> É necessário o parâmetro *TargetName*. O tema padrão que é lançado com AD FS chama-se Padrão.

## <a name="addsignindescription"></a>Adicione uma descrição de sessão 
Para adicionar uma descrição da página de iniciar sessão na **página de iniciar**sessão, utilize o seguinte cmdlet e sintaxe do Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modificar as regras de reclamação da AD FS 
O AD FS suporta uma linguagem rica que pode usar para criar regras de reivindicação personalizadas. Para mais informações, consulte [o papel da linguagem da regra de reclamação.](https://technet.microsoft.com/library/dd807118.aspx)

As seguintes secções descrevem como pode escrever regras personalizadas para alguns cenários relacionados com a Federação Azure AD e AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Id imutável condicionar um valor presente no atributo
O Azure AD Connect permite especificar um atributo a ser usado como âncora de origem quando os objetos estão sincronizados com o Azure AD. Se o valor no atributo personalizado não estiver vazio, é possível que queira emitir uma reclamação imutável de identificação.

Por exemplo, pode selecionar **ms-ds-consistência-consistência** como o atributo para a âncora de origem e emitir **ImutableID** como **ms-ds-consistência-consistência** no caso do atributo ter um valor contra ele. Se não houver valor contra o atributo, emita **objectguidamente** como id imutável. Pode construir o conjunto de regras de reclamação personalizadas, conforme descrito na secção seguinte.

**Regra 1: Atributos de consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Nesta regra, está a consultar os valores de **ms-ds-consistência** e **objectGuid** para o utilizador do Ative Directory. Mude o nome da loja para um nome de loja apropriado na sua implantação AD FS. Altere também o tipo de reclamações para um tipo de reclamações adequado para a sua federação, conforme definido para **objectGuid** e **ms-ds-consistência .**

Além disso, ao utilizar **o add** e não **emitir,** evita adicionar uma questão de saída para a entidade, e pode usar os valores como valores intermédios. Emitirá a reclamação numa regra posterior depois de estabelecer qual o valor a utilizar como id imutável.

**Regra 2: Verifique se existe consistência ms-ds para o utilizador**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regra define uma bandeira temporária chamada **idflag** que está definida para **usar o guid** se não houver **ms-ds-consistência** povoada para o utilizador. A lógica por trás disto é o facto de a AD FS não permitir afirmações vazias. Assim, quando adiciona reclamações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid na Regra 1, acaba com uma reclamação **msdsconsistênciala** apenas se o valor for povoado para o utilizador. Se não for povoada, a AD FS vê que terá um valor vazio e o deixa cair imediatamente. Todos os objetos terão **objectguida**, para que essa reivindicação esteja sempre presente após a execução da Regra 1.

**Regra 3: Emitir ms-ds-consistência como ID imutável se estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Este é um cheque **existente** implícito. Se o valor da reclamação existir, então emita-o como id imutável. O exemplo anterior utiliza a alegação de **identificador** de nomes. Terá de mudar isto para o tipo de reclamação apropriado para a identificação imutável no seu ambiente.

**Regra 4: Emitir objectivaMenteGuid como ID imutável se ms-ds-consistênciaNão estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

Nesta regra, está sem ver a **bandeira**temporária da bandeira. Você decide se deve emitir a reclamação com base no seu valor.

> [!NOTE]
> A sequência destas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um upn subdomínio

Pode adicionar mais do que um domínio a ser federado utilizando o Azure AD Connect, como descrito em [Adicionar um novo domínio federado](how-to-connect-fed-management.md#addfeddomain). A versão 1.1.553.0 do Azure AD Connect cria automaticamente a regra de reclamação correta para o emitente. Se não puder utilizar a versão 1.1.553.0 do Azure AD Connect ou mais recente, recomenda-se que a ferramenta [Azure AD RPT Claim Rules](https://aka.ms/aadrptclaimrules) seja utilizada para gerar e definir regras de reclamação corretas para a confiança do partido azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [as opções de inscrição do utilizador](plan-connect-user-signin.md).
