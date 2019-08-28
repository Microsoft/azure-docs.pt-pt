---
title: Gerenciamento e personalização de AD FS de Azure AD Connect | Microsoft Docs
description: AD FS gerenciamento com Azure AD Connect e personalização da experiência de entrada de AD FS de usuário com o Azure AD Connect e o PowerShell.
keywords: AD FS, ADFS, gerenciamento de AD FS, AAD Connect, conectar, entrar, AD FS personalização, reparar confiança, O365, Federação, terceira parte confiável
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085231"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerenciar e personalizar Serviços de Federação do Active Directory (AD FS) usando Azure AD Connect
Este artigo descreve como gerenciar e personalizar o Serviços de Federação do Active Directory (AD FS) (AD FS) usando o Azure Active Directory Connect (Azure AD). Ele também inclui outras tarefas comuns de AD FS que talvez você precise fazer para uma configuração completa de um farm de AD FS.

| Tópico | O que ele abrange |
|:--- |:--- |
| **Gerenciar AD FS** | |
| [Reparar a relação de confiança](#repairthetrust) |Como reparar a relação de confiança de Federação com o Office 365. |
| [Federar com o Azure AD usando a ID de logon alternativa](#alternateid) | Configurar a Federação usando a ID de logon alternativa  |
| [Adicionar um servidor de AD FS](#addadfsserver) |Como expandir um farm de AD FS com um servidor AD FS adicional. |
| [Adicionar um servidor proxy de aplicativo Web AD FS](#addwapserver) |Como expandir um farm de AD FS com um servidor de proxy de aplicativo Web (WAP) adicional. |
| [Adicionar um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado SSL](how-to-connect-fed-ssl-update.md)| Como atualizar o certificado SSL para um farm de AD FS. |
| **Personalizar AD FS** | |
| [Adicionar um logotipo ou uma ilustração da empresa personalizada](#customlogo) |Como personalizar uma página de entrada AD FS com um logotipo e uma ilustração da empresa. |
| [Adicionar uma descrição de entrada](#addsignindescription) |Como adicionar uma descrição de página de entrada. |
| [Modificar AD FS regras de declaração](#modclaims) |Como modificar declarações de AD FS para vários cenários de Federação. |

## <a name="manage-ad-fs"></a>Gerenciar AD FS
Você pode executar várias tarefas relacionadas ao AD FS no Azure AD Connect com intervenção mínima do usuário usando o assistente de Azure AD Connect. Depois de concluir a instalação do Azure AD Connect executando o assistente, você pode executar o assistente novamente para executar tarefas adicionais.

## <a name="repairthetrust"></a>Reparar a relação de confiança 
Você pode usar Azure AD Connect para verificar a integridade atual do AD FS e a confiança do AD do Azure e tomar as medidas apropriadas para reparar a relação de confiança. Siga estas etapas para reparar o Azure AD e AD FS confiança.

1. Selecione **reparar AAD e confiança do ADFS** na lista de tarefas adicionais.
   ![Reparar o AAD e a confiança do ADFS](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na página **conectar ao Azure ad** , forneça suas credenciais de administrador global para o Azure AD e clique em **Avançar**.
   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na página **credenciais de acesso remoto** , insira as credenciais para o administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Depois de clicar em **Avançar**, o Azure ad Connect verifica a integridade do certificado e mostra os problemas.

    ![Estado dos certificados](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    A página **pronto para configurar** mostra a lista de ações que serão executadas para reparar a relação de confiança.

    ![Pronto para configurar](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Clique em **instalar** para reparar a relação de confiança.

> [!NOTE]
> Azure AD Connect só pode reparar ou agir em certificados assinados automaticamente. Azure AD Connect não pode reparar certificados de terceiros.

## <a name="alternateid"></a>Federar com o Azure AD usando o Alternativoid 
É recomendável que o nome principal do usuário (UPN) local e o nome da entidade de usuário da nuvem sejam mantidos iguais. Se o UPN local usar um domínio não roteável (por exemplo, Contoso. local) ou não pode ser alterado devido a dependências do aplicativo local, é recomendável configurar a ID de logon alternativa. A ID de logon alternativa permite que você configure uma experiência de entrada em que os usuários podem entrar com um atributo diferente do UPN, como email. A escolha para o nome UPN no Azure AD Connect usa como padrão o atributo userPrincipalName no Active Directory. Se você escolher qualquer outro atributo para o nome UPN e estiver Federando usando AD FS, Azure AD Connect irá configurar AD FS para a ID de logon alternativa. Um exemplo de como escolher um atributo diferente para o nome principal do usuário é mostrado abaixo:

![Seleção de atributo de ID alternativa](./media/how-to-connect-fed-management/attributeselection.png)

A configuração da ID de logon alternativa para o AD FS consiste em duas etapas principais:
1. **Configure o conjunto certo de declarações de emissão**: As regras de declaração de emissão no confiança de terceira parte confiável do Azure AD são modificadas para usar o atributo UserPrincipalName selecionado como a ID alternativa do usuário.
2. **Habilite a ID de logon alternativa na configuração de AD FS**: A configuração de AD FS é atualizada para que AD FS possa Pesquisar usuários nas florestas apropriadas usando a ID alternativa. Essa configuração tem suporte para AD FS no Windows Server 2012 R2 (com KB2919355) ou posterior. Se os servidores de AD FS forem 2012 R2, Azure AD Connect verificará a presença do KB necessário. Se o KB não for detectado, um aviso será exibido após a conclusão da configuração, conforme mostrado abaixo:

    ![Aviso para KB ausentes em 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Para retificar a configuração no caso de KB ausentes, instale o [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) necessário e, em seguida, repare a relação de confiança usando [reparar o AAD e AD FS confiança](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre o alternativoid e as etapas para configurar manualmente, leia Configurando a [ID de logon alternativa](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Adicionar um servidor de AD FS 

> [!NOTE]
> Para adicionar um servidor de AD FS, Azure AD Connect requer o certificado PFX. Portanto, você só poderá executar essa operação se tiver configurado o farm de AD FS usando Azure AD Connect.

1. Selecione **implantar um servidor de Federação adicional**e clique em **Avançar**.

   ![Servidor de Federação adicional](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na página **conectar ao Azure ad** , insira suas credenciais de administrador global para o Azure AD e clique em **Avançar**.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Forneça as credenciais de administrador de domínio.

   ![Credenciais de administrador de domínio](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect solicita a senha do arquivo PFX que você forneceu ao configurar o novo farm de AD FS com o Azure AD Connect. Clique em **digitar senha** para fornecer a senha para o arquivo PFX.

   ![Palavra-passe de certificado](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Especificar certificado SSL](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na página **servidores AD FS** , insira o nome do servidor ou o endereço IP a ser adicionado ao farm de AD FS.

   ![Servidores AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Clique em **Avançar**e vá até a página **configuração** final. Depois que o Azure AD Connect terminar de adicionar os servidores ao farm de AD FS, você terá a opção de verificar a conectividade.

   ![Pronto para configurar](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Instalação concluída](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Adicionar um servidor WAP AD FS 

> [!NOTE]
> Para adicionar um servidor WAP, Azure AD Connect requer o certificado PFX. Portanto, você só poderá executar essa operação se tiver configurado o farm de AD FS usando Azure AD Connect.

1. Selecione **implantar proxy de aplicativo Web** na lista de tarefas disponíveis.

   ![Implantar proxy de aplicativo Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global do Azure.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na página **especificar certificado SSL** , forneça a senha para o arquivo PFX que você forneceu quando configurou o farm de AD FS com Azure ad Connect.
   ![Senha do certificado](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Especificar certificado SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adicione o servidor a ser adicionado como um servidor WAP. Como o servidor WAP pode não estar ingressado no domínio, o assistente solicita credenciais administrativas para o servidor que está sendo adicionado.

   ![Credenciais do servidor administrativo](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na página **credenciais de confiança de proxy** , forneça credenciais administrativas para configurar a relação de confiança de proxy e acessar o servidor primário no farm de AD FS.

   ![Credenciais de confiança do proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na página **pronto para configurar** , o assistente mostra a lista de ações que serão executadas.

   ![Pronto para configurar](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Clique em **instalar** para concluir a configuração. Depois que a configuração for concluída, o assistente lhe dará a opção de verificar a conectividade com os servidores. Clique em **verificar** para verificar a conectividade.

   ![Instalação concluída](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Adicionar um domínio federado 

É fácil adicionar um domínio para ser federado com o Azure AD usando Azure AD Connect. Azure AD Connect adiciona o domínio para Federação e modifica as regras de declaração para refletir corretamente o emissor quando você tem vários domínios federados com o Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar um domínio do Azure ad adicional**.

   ![Domínio do Azure AD adicional](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na próxima página do assistente, forneça as credenciais de administrador global para o Azure AD.

   ![Ligar ao Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na página **credenciais de acesso remoto** , forneça as credenciais de administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na próxima página, o assistente fornece uma lista de domínios do Azure AD com os quais você pode federar seu diretório local. Escolha o domínio na lista.

   ![Domínio do Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornecerá as informações apropriadas sobre outras ações que o assistente executará e o impacto da configuração. Em alguns casos, se você selecionar um domínio que ainda não foi verificado no Azure AD, o assistente fornecerá informações para ajudá-lo a verificar o domínio. Consulte [Adicionar seu nome de domínio personalizado para Azure Active Directory](../active-directory-domains-add-azure-portal.md) para obter mais detalhes.

5. Clique em **Seguinte**. A página **pronto para configurar** mostra a lista de ações que Azure ad Connect será executada. Clique em **instalar** para concluir a configuração.

   ![Pronto para configurar](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Os usuários do domínio federado adicionado devem ser sincronizados antes que possam fazer logon no Azure AD.

## <a name="ad-fs-customization"></a>AD FS personalização
As seções a seguir fornecem detalhes sobre algumas das tarefas comuns que talvez você precise executar ao personalizar sua página de entrada do AD FS.

## <a name="customlogo"></a>Adicionar um logotipo ou uma ilustração da empresa personalizada 
Para alterar o logotipo da empresa que é exibido na página de **entrada** , use o seguinte cmdlet e sintaxe do Windows PowerShell.

> [!NOTE]
> As dimensões recomendadas para o logotipo são 260 x \@ 35 96 DPI com um tamanho de arquivo superior a 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> O parâmetro *TargetName* é obrigatório. O tema padrão que é liberado com AD FS é denominado padrão.

## <a name="addsignindescription"></a>Adicionar uma descrição de entrada 
Para adicionar uma descrição de página de entrada à **página de entrada**, use o seguinte cmdlet e sintaxe do Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modificar AD FS regras de declaração 
O AD FS dá suporte a uma linguagem de declaração avançada que você pode usar para criar regras de declaração personalizada. Para obter mais informações, consulte [a função do idioma da regra de declaração](https://technet.microsoft.com/library/dd807118.aspx).

As seções a seguir descrevem como você pode escrever regras personalizadas para alguns cenários relacionados ao Azure AD e AD FS Federação.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID imutável condicional em um valor que está presente no atributo
Azure AD Connect permite especificar um atributo a ser usado como uma âncora de origem quando os objetos são sincronizados com o Azure AD. Se o valor no atributo personalizado não estiver vazio, talvez você queira emitir uma declaração de ID imutável.

Por exemplo, você pode selecionar **MS-DS-consistencyguid** como o atributo para a âncora de origem e emitir imutávelid como **MS-DS-consistencyguid** , caso o atributo tenha um valor em relação a ele. Se não houver nenhum valor em relação ao atributo, emita objectGUID como a ID imutável. Você pode construir o conjunto de regras de declaração personalizadas, conforme descrito na seção a seguir.

**Regra 1: Atributos de consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Nesta regra, você está consultando os valores de **MS-DS-consistencyguid** e objectGUID para o usuário de Active Directory. Altere o nome do repositório para um nome de repositório apropriado em sua implantação do AD FS. Altere também o tipo de declarações para um tipo de declaração adequado para sua Federação, conforme definido para objectGUID e **MS-DS-consistencyguid**.

Além disso, ao usar **Adicionar** e não **emitir**, você evita adicionar um problema de saída para a entidade e pode usar os valores como valores intermediários. Você emitirá a declaração em uma regra posterior depois de estabelecer o valor a ser usado como a ID imutável.

**Regra 2: Verificar se o MS-DS-consistencyguid existe para o usuário**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Essa regra define um sinalizador temporário chamado **idflag** que é definido como **useguid** se não houver **MS-DS-consistencyguid** preenchido para o usuário. A lógica por trás disso é o fato de que AD FS não permite declarações vazias. Assim, quando você adicionar http://contoso.com/ws/2016/02/identity/claims/objectguid declarações http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid e na regra 1, acabará com uma declaração **msdsconsistencyguid** somente se o valor for populado para o usuário. Se não estiver populado, AD FS verá que ele terá um valor vazio e o descartará imediatamente. Todos os objetos terão **objectGUID**, de modo que a declaração sempre estará lá depois que a regra 1 for executada.

**Regra 3: Emita ms-DS-consistencyguid como a ID imutável se ela estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);

Essa é uma verificação de **existência** implícita. Se o valor da declaração existir, emita-a como a ID imutável. O exemplo anterior usa a Declaração **nameidentifier** . Você precisará alterar isso para o tipo de declaração apropriado para a ID imutável em seu ambiente.

**Regra 4: Emitir objectGUID como a ID imutável se ms-DS-consistencyGuid não estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);

Nessa regra, você está simplesmente verificando o sinalizador temporário **idflag**. Você decide se deve emitir a declaração com base em seu valor.

> [!NOTE]
> A sequência dessas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um UPN de subdomínio

Você pode adicionar mais de um domínio para ser federado usando Azure AD Connect, conforme descrito em [Adicionar um novo domínio federado](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect versão 1.1.553.0 e mais recente criam a regra de declaração correta para issuerID automaticamente. Se você não puder usar Azure AD Connect versão 1.1.553.0 ou mais recente, é recomendável que a ferramenta de [regras de declaração do Azure ad RPT](https://aka.ms/aadrptclaimrules) seja usada para gerar e definir regras de declaração corretas para a relação de confiança de terceira parte confiável do Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [as opções de entrada do usuário](plan-connect-user-signin.md).
