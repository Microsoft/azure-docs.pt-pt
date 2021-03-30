---
title: Azure AD Connect - AD FS gestão e personalização | Microsoft Docs
description: Gestão de FS AD com Azure AD Connect e personalização da experiência de sind de súm em AD FS do utilizador com Azure AD Connect e PowerShell.
keywords: AD FS, ADFS, gestão AD FS, AAD Connect, Connect, sign-in, Personalização AD FS, confiança de reparação, M365, federação, festa de confiança
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
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91274117"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerir e personalizar os Serviços da Federação de Diretórios Ativos utilizando o Azure AD Connect
Este artigo descreve como gerir e personalizar os Serviços da Federação de Diretório Ativo (AD FS) utilizando o Azure Ative Directory (Azure AD) Connect. Também inclui outras tarefas comuns de AD FS que você pode precisar fazer para uma configuração completa de uma fazenda AD FS.

| Tópico | O que cobre |
|:--- |:--- |
| **Gerir AD FS** | |
| [Reparar a confiança](#repairthetrust) |Como reparar a confiança da federação com a Microsoft 365. |
| [Federate com AD AZure usando ID de login alternativo](#alternateid) | Federação de configuração usando iD de login alternativo  |
| [Adicione um servidor AD FS](#addadfsserver) |Como expandir uma fazenda AD FS com um servidor AD FS adicional. |
| [Adicione um servidor de procuração de aplicação web AD FS](#addwapserver) |Como expandir uma fazenda AD FS com um servidor adicional de Proxy de Aplicação Web (WAP). |
| [Adicione um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado TLS/SSL](how-to-connect-fed-ssl-update.md)| Como atualizar o certificado TLS/SSL para uma exploração AD FS. |
| **Personalize O AD FS** | |
| [Adicione um logotipo ou ilustração personalizada da empresa](#customlogo) |Como personalizar uma página de sind FS AD com um logotipo da empresa e ilustração. |
| [Adicione uma descrição de inscrição](#addsignindescription) |Como adicionar uma descrição da página de inscrição. |
| [Modificar as regras de reclamação da AD FS](#modclaims) |Como modificar as alegações da AD FS para vários cenários da federação. |

## <a name="manage-ad-fs"></a>Gerir AD FS
Pode executar várias tarefas relacionadas com AD FS no Azure AD Connect com a mínima intervenção do utilizador utilizando o assistente Azure AD Connect. Depois de ter terminado de instalar o Azure AD Connect executando o assistente, pode voltar a executar o assistente para executar tarefas adicionais.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Reparar a confiança 
Você pode usar Azure AD Connect para verificar a saúde atual da confiança AD FS e Azure AD e tomar as medidas apropriadas para reparar a confiança. Siga estes passos para reparar a confiança da Azure AD e da AD FS.

1. Selecione **Reparo AAD e ADFS Trust** da lista de tarefas adicionais.
   ![Reparar AAD e ADFS Trust](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na página **Connect to Azure AD,** forneça as suas credenciais de administrador global para AZure AD e clique em **Seguinte**.
   ![Screenshot que mostra a página "Connect to Azure AD" com credenciais de exemplo inseridas.](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na página de **credenciais de acesso remoto,** insira as credenciais para o administrador de domínio.

   ![Screenshot que mostra a página "Credenciais de acesso remoto" com credenciais de exemplo inseridas.](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Depois de clicar em **Next**, Azure AD Connect verifica a saúde do certificado e mostra quaisquer problemas.

    ![Estado dos certificados](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    A página **Ready to Configure** mostra a lista de ações que serão realizadas para reparar o fundo.

    ![Screenshot que mostra a página "Pronto a configurar" com uma lista de ações.](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Clique **em Instalar** para reparar a confiança.

> [!NOTE]
> O Azure AD Connect só pode reparar ou agir em certificados auto-assinados. O Azure AD Connect não consegue reparar certificados de terceiros.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federate com AD Azure usando O SUPLENTEID 
Recomenda-se que o nome principal do utilizador no local (UPN) e o nome principal do utilizador em nuvem sejam mantidos da mesma forma. Se o UPN no local utiliza um domínio não encaminháveis internos (ex. Contoso.local) ou não pode ser alterado devido às dependências de aplicações locais, recomendamos a criação de ID de login alternativo. O ID de login alternativo permite-lhe configurar uma experiência de login onde os utilizadores podem iniciar sessão com um atributo diferente do seu UPN, como o correio. A escolha para Nome Principal do Utilizador em Azure AD Connect predefine o atributo UserPrincipalName no Ative Directory. Se escolher qualquer outro atributo para o Nome Principal do Utilizador e estiver a federar usando AD FS, então o Azure AD Connect configurará AD FS para iD de login alternativo. Um exemplo de escolha de um atributo diferente para o nome principal do utilizador é mostrado abaixo:

![Seleção de atributos de ID alternativo](./media/how-to-connect-fed-management/attributeselection.png)

Configurar o ID de login alternativo para AD FS consiste em dois passos principais:
1. **Configure o conjunto certo de reclamações de emissão**: As regras de reclamação de emissão no Azure AD que confia na parte do partido são modificadas para utilizar o atributo UserPrincipalName selecionado como iD alternativo do utilizador.
2. **Ativar o ID de login alternativo na configuração AD FS**: A configuração AD FS é atualizada para que o AD FS possa procurar os utilizadores nas florestas apropriadas utilizando o ID alternativo. Esta configuração é suportada para AD FS no Windows Server 2012 R2 (com KB2919355) ou mais tarde. Se os servidores AD FS forem 2012 R2, a Azure AD Connect verifica a presença do KB necessário. Se o KB não for detetado, será apresentado um aviso após a conclusão da configuração, como mostra abaixo:

    ![Aviso para kB desaparecido em 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Para retificar a configuração em caso de falta de KB, instale o [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) necessário e, em seguida, repare o fundo utilizando [o Reparo AAD e o AD FS Trust](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre oID alternativo e os passos para configurar manualmente, leia [o ID de Login Alternativo Configurado](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Adicione um servidor AD FS 

> [!NOTE]
> Para adicionar um servidor AD FS, o Azure AD Connect requer o certificado PFX. Portanto, só pode efetuar esta operação se configurar a quinta AD FS utilizando o Azure AD Connect.

1. Selecione **Implementar um servidor de federação adicional** e clique em **Seguinte**.

   ![Servidor adicional da federação](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na página **Connect to Azure AD,** insira as credenciais de administrador global para Azure AD e clique em **Seguinte**.

   ![Screenshot que mostra a página "Connect to Azure AD" com credenciais de amostra inseridas.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Fornecer as credenciais de administrador de domínio.

   ![Credenciais de administrador de domínio](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. O Azure AD Connect pede a palavra-passe do ficheiro PFX que forneceu enquanto configura a sua nova quinta AD FS com o Azure AD Connect. Clique **em Introduzir Palavra-passe** para fornecer a palavra-passe para o ficheiro PFX.

   ![Screenshot que mostra a página "Especificar certificado SSL" com a janela "Password de Certificado" aberta.](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Screenshot que mostra a página "Especificar certificado SSL" depois de uma palavra-passe para o ficheiro PFX ser introduzido.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na página **AD FS Servers, insira** o nome do servidor ou endereço IP a ser adicionado à fazenda AD FS.

   ![Servidores AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Clique **em Seguida**, e veja a página final do **Configure.** Depois de o Azure AD Connect ter terminado de adicionar os servidores à quinta AD FS, será-lhe dada a opção de verificar a conectividade.

   ![Screenshot que mostra a página "Pronto a configurar" com uma lista de ações a preencher depois de clicar em "Instalar".](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Screenshot que mostra a página "Instalação completa" com uma mensagem "A configuração Intranet foi verificada com sucesso". ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Adicione um servidor WAP AD FS 

> [!NOTE]
> Para adicionar um servidor WAP, o Azure AD Connect requer o certificado PFX. Portanto, só pode efetuar esta operação se configurar a quinta AD FS utilizando o Azure AD Connect.

1. Selecione Implementar Proxy de **aplicação web** a partir da lista de tarefas disponíveis.

   ![Implementar Proxy de Aplicação Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global da Azure.

   ![Screenshot que mostra a página "Connect to Azure AD" com um nome de utilizador e senha de exemplo introduzidos.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na página de **certificados Specify SSL,** forneça a palavra-passe para o ficheiro PFX que forneceu quando configurar a quinta AD FS com Azure AD Connect.
   ![Senha de certificado](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Especificar certificado TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adicione o servidor a ser adicionado como um servidor WAP. Como o servidor WAP pode não ser associado ao domínio, o assistente pede credenciais administrativas para o servidor ser adicionado.

   ![Credenciais de servidor administrativo](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na página de **credenciais de confiança Proxy,** forneça credenciais administrativas para configurar a confiança de procuração e aceder ao servidor primário na fazenda AD FS.

   ![Credenciais de confiança proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na página **Pronto para configurar,** o assistente mostra a lista de ações que serão executadas.

   ![Screenshot que mostra a página "Pronto a configurar" com uma lista de ações a serem executadas.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Clique **em Instalar** para terminar a configuração. Após a configuração estar concluída, o assistente dá-lhe a opção de verificar a conectividade com os servidores. Clique **em Verificar** para verificar a conectividade.

   ![Instalação concluída](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Adicione um domínio federado 

É fácil adicionar um domínio a ser federado com Azure AD usando Azure AD Connect. O Azure AD Connect adiciona o domínio para a federação e modifica as regras de reclamação para refletir corretamente o emitente quando tem vários domínios federados com Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicione um domínio AD adicional**.

   ![Domínio adicional de Azure AD](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na página seguinte do assistente, forneça as credenciais de administrador global para Azure AD.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na página de **credenciais de acesso remoto,** forneça as credenciais de administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na página seguinte, o assistente fornece uma lista de domínios AD Azure com os quais pode federar o seu diretório no local. Escolha o domínio da lista.

   ![Domínio AD AZure](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornece-lhe informações apropriadas sobre outras ações que o assistente irá tomar e o impacto da configuração. Em alguns casos, se selecionar um domínio que ainda não foi verificado no Azure AD, o assistente fornece-lhe informações para o ajudar a verificar o domínio. Consulte [o nome de domínio personalizado ao Azure Ative Directory](../fundamentals/add-custom-domain.md) para obter mais detalhes.

5. Clique em **Seguinte**. A página **Ready to Configure** mostra a lista de ações que o Azure AD Connect irá realizar. Clique **em Instalar** para terminar a configuração.

   ![Preparado para configurar](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Os utilizadores do domínio federado adicionado devem ser sincronizados antes de poderem iniciar sessão no Azure AD.

## <a name="ad-fs-customization"></a>A personalização do AD FS
As secções seguintes fornecem detalhes sobre algumas das tarefas comuns que poderá ter de executar quando personaliza a sua página de sposição AD FS.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Adicione um logotipo ou ilustração personalizada da empresa 
Para alterar o logótipo da empresa que está na página **de início de sposição,** utilize o seguinte cmdlet e sintaxe do Windows PowerShell.

> [!NOTE]
> As dimensões recomendadas para o logótipo são de 260 x \@ 35 96 dpi com um tamanho de ficheiro não superior a 10 KB.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> É necessário o parâmetro *TargetName.* O tema padrão que é lançado com AD FS chama-se Predefinido.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Adicione uma descrição de inscrição 
Para adicionar uma descrição da página de entrada na **página de iniciar sing,** utilize o cmdlet e sintaxe do Windows PowerShell.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Modificar as regras de reclamação da AD FS 
A AD FS suporta uma linguagem de reivindicação rica que pode usar para criar regras de reivindicação personalizadas. Para mais informações, consulte [O Papel da Língua de Regra de Reclamação.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11))

As secções seguintes descrevem como pode escrever regras personalizadas para alguns cenários relacionados com a Azure AD e a federação de FS AD.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID imutável condiciona-se a um valor presente no atributo
O Azure AD Connect permite especificar um atributo a ser utilizado como âncora de origem quando os objetos são sincronizados com a Azure AD. Se o valor no atributo personalizado não estiver vazio, é melhor emitir uma reclamação de ID imutável.

Por exemplo, pode selecionar **o ms-ds-consistência como** o atributo para a âncora de origem e emitir **ImuttableID** como **ms-ds-consistência-yguid** no caso de o atributo ter um valor contra ele. Se não houver valor contra o atributo, emita **o objectGuid** como imutável ID. Pode construir o conjunto de regras de reclamação personalizadas, conforme descrito na secção seguinte.

**Regra 1: Atributos de consulta**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

Nesta regra, está a consultar os **valores de ms-ds-consistência e** **objectGuid** para o utilizador do Ative Directory. Mude o nome da loja para um nome de loja apropriado na sua implementação AD FS. Altere também o tipo de reclamações para um tipo de reclamação adequado para a sua federação, conforme definido para **objectGuid** e **ms-ds-consistênciaguid**.

Além disso, ao utilizar **o add** e não **emitir,** evita adicionar um problema de saída para a entidade, e pode usar os valores como valores intermédios. Emitirá a reclamação numa regra posterior depois de determinar qual o valor a utilizar como Imutável ID.

**Regra 2: Verifique se existe um guia de consistência ms-ds para o utilizador**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Esta regra define uma bandeira temporária chamada **idflag** que é definida para **usar o guia** se não houver **ms-ds-consistência-yguid** povoada para o utilizador. A lógica por trás disto é o facto de a AD FS não permitir reclamações vazias. Assim, quando se adicionam reclamações `http://contoso.com/ws/2016/02/identity/claims/objectguid` e `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` na regra 1, acaba-se com uma reclamação **de msdsconsistencyguid apenas** se o valor for preenchido para o utilizador. Se não for povoado, a AD FS vê que terá um valor vazio e o deixará cair imediatamente. Todos os objetos terão **objecteGuid,** de modo que a reivindicação estará sempre lá depois da regra 1 ser executada.

**Regra 3: Emite ms-ds-consistênciaguid como ID imutável se estiver presente**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Este é um cheque implícito **exist.** Se o valor da reclamação existir, então emita-o como imutável IM. O exemplo anterior utiliza a **alegação do identificador de nomes.** Terá de alterar isto para o tipo de reclamação apropriado para o imutável ID no seu ambiente.

**Regra 4: Emitir objectGuid como ID imutável se ms-ds-consistênciaGuid não estiver presente**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

Nesta regra, está simplesmente a verificar a **idflag** temporária da bandeira. Decide se emite a reclamação com base no seu valor.

> [!NOTE]
> A sequência destas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN

Pode adicionar mais do que um domínio a ser federado utilizando o Azure AD Connect, conforme descrito no [Add um novo domínio federado](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect versão 1.1.553.0 e mais recente cria automaticamente a regra de reclamação correta para o emitenteID. Se não puder utilizar a versão 1.1.553.0 ou mais recente do Azure AD, recomenda-se que a ferramenta [Azure AD RPT Claim Rules](https://aka.ms/aadrptclaimrules) seja utilizada para gerar e definir regras de reclamação corretas para a confiança do partido Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [as opções de inscrição do utilizador](plan-connect-user-signin.md).