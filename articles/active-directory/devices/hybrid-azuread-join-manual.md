---
title: Configurar dispositivos híbridos associados ao Azure Active Directory manualmente | Microsoft Docs
description: Saiba como configurar manualmente os dispositivos ingressados no Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56fcc45d4efb21c4904380d731d9e95584c39fd
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879416"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Tutorial: Configurar dispositivos ingressados no Azure Active Directory híbrido manualmente

Com o gerenciamento de dispositivos no Azure Active Directory (Azure AD), você pode garantir que os usuários acessem seus recursos de dispositivos que atendam aos seus padrões de segurança e conformidade. Para obter mais informações, consulte [introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md).

> [!TIP]
> Se estiver usando Azure AD Connect for uma opção para você, consulte os tutoriais relacionados [](hybrid-azuread-join-managed-domains.md) para domínios gerenciados ou [federados](hybrid-azuread-join-federated-domains.md) . Usando Azure AD Connect, você pode simplificar significativamente a configuração da junção híbrida do Azure AD.

Se tiver um ambiente do Active Directory no local e quiser associar os seus dispositivos associados ao domínio ao Azure AD, pode fazê-lo ao configurar os dispositivos híbridos associados ao Azure AD. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar manualmente a junção híbrida do Azure AD
> * Configurar um ponto de conexão de serviço
> * Configurar emissão de declarações
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolver problemas relacionados com a implementação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você esteja familiarizado com:

* [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introdução à gestão de dispositivos no Azure Active Directory)
* [Planejar sua implementação de junção de Azure Active Directory híbrida](hybrid-azuread-join-plan.md)
* [Controlar a associação híbrida do Azure AD dos seus dispositivos](hybrid-azuread-join-control.md)

Antes de começar a habilitar dispositivos ingressados no Azure AD híbrido em sua organização, verifique se:

* Você está executando uma versão atualizada do Azure AD Connect.
* Azure AD Connect sincronizou os objetos de computador dos dispositivos que você deseja que sejam ingressados no Azure AD híbrido no Azure AD. Se os objetos de computador pertencerem a UOs (unidades organizacionais) específicas, essas UOs precisarão ser configuradas para sincronização em Azure AD Connect também.

Azure AD Connect:

* Mantém a associação entre a conta de computador em sua instância de Active Directory local e o objeto de dispositivo no Azure AD.
* Habilita outros recursos relacionados ao dispositivo, como o Windows Hello para empresas.

Verifique se as URLs a seguir estão acessíveis de computadores na rede da sua organização para o registro de computadores para o Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* O STS da sua organização (para domínios federados), que deve ser incluído nas configurações de intranet local do usuário

Se sua organização planeja usar o SSO contínuo, a URL a seguir precisará ser acessada dos computadores dentro da sua organização. Ele também deve ser adicionado à zona da intranet local do usuário.

* `https://autologon.microsoftazuread-sso.com`

Além disso, a seguinte configuração deve ser habilitada na zona de intranet do usuário: "Permitir atualizações da barra de status via script".

Se sua organização usa a configuração gerenciada (não federada) com o Active Directory local e não usa Serviços de Federação do Active Directory (AD FS) (AD FS) para federar com o Azure AD, a junção híbrida do Azure AD no Windows 10 depende dos objetos de computador no Active Diretório a ser sincronizado com o Azure AD. Verifique se as UOs que contêm os objetos de computador que precisam ser ingressados no Azure AD híbrido estão habilitadas para sincronização na configuração de sincronização de Azure AD Connect.

Para dispositivos Windows 10 na versão 1703 ou anterior, se sua organização exigir acesso à Internet por meio de um proxy de saída, você deverá implementar a descoberta automática de proxy da Web (WPAD) para permitir que os computadores com Windows 10 se registrem no Azure AD.

A partir do Windows 10 1803, mesmo se uma tentativa de junção híbrida do Azure AD por um dispositivo em um domínio federado por meio de AD FS falhar e, se Azure AD Connect estiver configurada para sincronizar os objetos de computador/dispositivo com o Azure AD, o dispositivo tentará concluir a junção híbrida do Azure AD por nós ing o computador/dispositivo sincronizado.

Para verificar se o dispositivo é capaz de acessar os recursos da Microsoft acima na conta do sistema, você pode usar o script de [conectividade de registro de dispositivo de teste](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="verify-configuration-steps"></a>Verificar as etapas de configuração

Pode configurar os dispositivos híbridos associados ao Azure AD para vários tipos de plataformas de dispositivos Windows. Este tópico inclui os passos necessários para todos os cenários de configuração comuns.  

Utilize a tabela abaixo para obter uma descrição geral dos passos necessários para o seu cenário:  

| Passos | Dispositivos Windows atuais e sincronização de hash de palavra-passe | Dispositivos Windows atuais e federação | Dispositivos Windows de nível inferior |
| :--- | :---: | :---: | :---: |
| Configurar o ponto de ligação do serviço | ![Verificação][1] | ![Verificação][1] | ![Verificação][1] |
| Configurar emissão de declarações |     | ![Verificação][1] | ![Verificação][1] |
| Permitir dispositivos não Windows 10 |       |        | ![Verificação][1] |
| Verificar dispositivos associados | ![Verificação][1] | ![Verificação][1] | [Verificação][1] |

## <a name="configure-a-service-connection-point"></a>Configurar um ponto de conexão de serviço

Seus dispositivos usam um objeto de ponto de conexão de serviço (SCP) durante o registro para descobrir informações de locatário do Azure AD. Em sua instância de Active Directory local, o objeto SCP para os dispositivos ingressados no Azure AD híbrido deve existir na partição de contexto de nomenclatura de configuração da floresta do computador. Só existe um contexto de nomenclatura de configuração por floresta. Em uma configuração de Active Directory de várias florestas, o ponto de conexão de serviço deve existir em todas as florestas que contêm computadores ingressados no domínio.

Pode utilizar o cmdlet [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) para obter o contexto de nomenclatura da configuração da sua floresta.  

Para uma floresta com o nome de domínio do Active Directory *fabrikam.com*, o contexto de nomenclatura da configuração é:

`CN=Configuration,DC=fabrikam,DC=com`

Na sua floresta, o objeto SCP do registo automático dos dispositivos associados a um domínio está localizado em:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Dependendo de como você implantou o Azure AD Connect, o objeto SCP pode já ter sido configurado.
Você pode verificar a existência do objeto e recuperar os valores de descoberta usando o seguinte script do Windows PowerShell:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

O **$SCP. A saída de palavras-chave** mostra as informações de locatário do Azure AD. Segue-se um exemplo:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Se o ponto de ligação do serviço não existir, pode executar o cmdlet `Initialize-ADSyncDomainJoinedComputerSync` no servidor do Azure AD Connect para criá-lo. As credenciais de administrador corporativo são necessárias para executar este cmdlet.  

O cmdlet:

* Cria o ponto de conexão de serviço na floresta Active Directory à qual Azure AD Connect está conectado.
* Requer que seja especificado o parâmetro `AdConnectorAccount`. Essa conta é configurada como a conta do conector de Active Directory no Azure AD Connect.


O script abaixo mostra um exemplo da utilização do cmdlet. Neste script, `$aadAdminCred = Get-Credential` requer que indique um nome de utilizador. Tem de indicar o nome de utilizador no formato de nome principal de utilizador (UPN) (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

O cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

* Usa o módulo Active Directory do PowerShell e as ferramentas do Azure Active Directory Domain Services (AD DS do Azure). Essas ferramentas dependem de Active Directory Web Services em execução em um controlador de domínio. Os Serviços Web do Active Directory são suportados em controladores de domínio em execução no Windows Server 2008 R2 e posterior.
* Só é suportado pelo módulo MSOnline do PowerShell versão 1.1.166.0. Para baixar este módulo, use [este link](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).
* Se as ferramentas de AD DS não estiverem instaladas, `Initialize-ADSyncDomainJoinedComputerSync` o falhará. Você pode instalar as ferramentas de AD DS por meio de Gerenciador do servidor em **recursos** > **ferramentas de administração de servidor remoto** > **ferramentas de administração de funções**.

Para controladores de domínio que executam o Windows Server 2008 ou versões anteriores, use o script a seguir para criar o ponto de conexão de serviço. Em uma configuração de várias florestas, use o script a seguir para criar o ponto de conexão de serviço em cada floresta em que os computadores existem.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

No script anterior, `$verifiedDomain = "contoso.com"` é um espaço reservado. Substitua-o por um dos nomes de domínio verificados no Azure AD. Você precisa ter o domínio antes de poder usá-lo.

Para obter mais informações sobre nomes de domínio verificados, consulte [Adicionar um nome de domínio personalizado a Azure Active Directory](../active-directory-domains-add-azure-portal.md).

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Configurar emissão de declarações

Em uma configuração federada do Azure AD, os dispositivos contam com AD FS ou um serviço de federação local de um parceiro da Microsoft para se autenticar no Azure AD. Os dispositivos autenticam-se para obterem um token de acesso para se registarem no Registo de Dispositivos do Azure Active Directory (Azure DRS).

Os dispositivos atuais do Windows são autenticados usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust ativo (versões 1,3 ou 2005) hospedados pelo serviço de federação local.

Quando você estiver usando AD FS, será necessário habilitar os seguintes pontos de extremidade do WS-Trust
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> O **ADFS/Services/Trust/2005/windowstransport** ou **ADFS/Services/Trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e não devem ser expostos como pontos de extremidade voltados para a extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desabilitar os pontos de extremidade do WIndows do WS-Trust, confira [desabilitar pontos de extremidade do Windows do WS-Trust no proxy](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Você pode ver quais pontos de extremidade são habilitados por meio do console de gerenciamento de AD FS em**pontos de extremidade**de **serviço** > .

> [!NOTE]
>Se você não tiver AD FS como seu serviço de federação local, siga as instruções do seu fornecedor para certificar-se de que eles dão suporte aos pontos de extremidade WS-Trust 1,3 ou 2005 e que eles são publicados por meio do arquivo de troca de metadados (MEX).

Para que o registro do dispositivo seja concluído, as declarações a seguir devem existir no token que o Azure DRS recebe. O DRS do Azure criará um objeto de dispositivo no Azure AD com algumas dessas informações. Azure AD Connect, em seguida, usa essas informações para associar o objeto de dispositivo recém-criado à conta de computador local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se tiver vários nomes de domínio verificados, tem de indicar a seguinte afirmação para os computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se você já estiver emitindo uma declaração imutávelid (por exemplo, ID de logon alternativa), precisará fornecer uma declaração correspondente para computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nas secções abaixo, vai encontrar informações sobre:

* Os valores que cada declaração deve ter.
* Como seria a aparência de uma definição em AD FS.

A definição ajuda-o a verificar se os valores estão presentes ou se precisa de os criar.

> [!NOTE]
> Se não utilizar o AD FS para o seu servidor de federação no local, siga as instruções do seu fornecedor para criar a configuração adequada para emitir estas afirmações.

### <a name="issue-account-type-claim"></a>Emitir afirmação de tipo de conta

A `http://schemas.microsoft.com/ws/2012/01/accounttype` declaração deve conter um valor de **DJ**, que identifica o dispositivo como um computador ingressado no domínio. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Emitir o objectGUID da conta de computador no local

A `http://schemas.microsoft.com/identity/claims/onpremobjectguid` declaração deve conter o valor objectGUID da conta de computador local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Emitir o objectSID da conta de computador no local

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` declaração deve conter o valor de **objectSid** da conta de computador local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Emitir issuerID para o computador quando vários nomes de domínio verificados estiverem no Azure AD

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` declaração deve conter o Uniform Resource Identifier (URI) de qualquer um dos nomes de domínio verificados que se conectam ao serviço de federação local (AD FS ou parceiro) que emite o token. No AD FS, você pode adicionar regras de transformação de emissão que se parecem com as seguintes nessa ordem específica, após as anteriores. Observe que uma regra para emitir explicitamente a regra para os usuários é necessária. Nas regras a seguir, uma primeira regra que identifica o usuário versus a autenticação do computador é adicionada.

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

Na declaração anterior, `<verified-domain-name>` é um espaço reservado. Substitua-o por um dos nomes de domínio verificados no Azure AD. Por exemplo, use `Value = "http://contoso.com/adfs/services/trust/"`.

Para obter mais informações sobre nomes de domínio verificados, consulte [Adicionar um nome de domínio personalizado a Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Emitir imutável para o computador quando houver um para os usuários (por exemplo, uma ID de logon alternativa é definida)

A `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` declaração deve conter um valor válido para computadores. No AD FS, pode criar uma regra de transformação de emissão da seguinte forma:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script de programa auxiliar para criar as regras de transformação de emissão do AD FS

O script a seguir o ajuda com a criação das regras de transformação de emissão descritas anteriormente.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Observações

* Este script anexa as regras às já existentes. Não execute o script duas vezes, porque o conjunto de regras seria adicionado duas vezes. Antes de voltar a executar o script, confirme que não existem regras correspondentes para estas afirmações (com as condições correspondentes).
* Se você tiver vários nomes de domínio verificados (conforme mostrado no portal do Azure AD ou por meio do cmdlet **Get-MsolDomain** ), defina o valor de **$multipleVerifiedDomainNames** no script como **$true**. Além disso, certifique-se de remover qualquer declaração **issuerid** existente que possa ter sido criada por Azure ad Connect ou por outros meios. Aqui está um exemplo para esta regra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Se já tiver emitido uma afirmação **ImmutableID** para as contas de utilizador, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script como **$true**.

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio são dispositivos de nível inferior do Windows, terá de:

* Definir uma política no Azure AD para permitir que os utilizadores registem dispositivos.
* Configure seu serviço de federação local para emitir declarações para dar suporte à autenticação integrada do Windows (IWA) para o registro de dispositivos.
* Adicione o ponto de extremidade de autenticação de dispositivo do Azure AD às zonas da intranet local para evitar prompts de certificado ao autenticar o dispositivo.
* Controlar dispositivos de nível inferior do Windows.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir uma política no Azure AD para permitir que os usuários registrem dispositivos

Para registrar dispositivos de nível inferior do Windows, certifique-se de que a configuração para permitir que os usuários registrem dispositivos no Azure AD esteja habilitada. Na portal do Azure, você pode encontrar essa configuração em **Azure Active Directory** > **configurações do dispositivo** **usuários e grupos** > .

A política a seguir deve ser definida como **todos**: **Os usuários podem registrar seus dispositivos com o Azure ad**.

![O botão tudo que permite aos usuários registrar dispositivos](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Configurar o serviço de federação local

Seu serviço de federação local deve dar suporte à emissão de declarações **AuthenticationMethod** e **wiaormultiauthn** quando receber uma solicitação de autenticação para a parte confiável do Azure AD que contém um parâmetro resource_params com o seguinte valor codificado:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Quando essa solicitação chega, o serviço de federação local deve autenticar o usuário usando a autenticação integrada do Windows. Quando a autenticação for bem-sucedida, o serviço de Federação deverá emitir as duas declarações a seguir:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

No AD FS, você deve adicionar uma regra de transformação de emissão que passe pelo método de autenticação. Para adicionar esta regra:

1. No console de gerenciamento do AD FS, acesse **AD FS** > **relações** > de confiança confianças de terceira**parte confiável**.
1. Clique com o botão direito do rato no objeto de confiança da entidade confiadora da Plataforma de Identidade do Microsoft Office 365 e selecione **Edit Claim Rules** (Editar Regras de Emissão).
1. No separador **Issuance Transform Rules** (Regras de Transformação de Emissão), selecione **Add Rule** (Adicionar Regra).
1. Na lista de modelos **Claim rule** (Regra de afirmação), selecione **Send Claims Using a Custom Rule** (Enviar Afirmações com uma Regra Personalizada).
1. Selecione **Seguinte**.
1. Na caixa **nome da regra de declaração** , digite **regra de declaração do método de autenticação**.
1. Na caixa **regra de declaração** , insira a seguinte regra:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. No servidor de Federação, insira o comando do PowerShell a seguir. **Substitua\<RPObjectName\>** pelo nome do objeto de terceira parte confiável para o objeto de confiança de terceira parte confiável do Azure AD. Normalmente, este objeto é denominado **Plataforma de Identidade do Microsoft Objeto 365**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Adicionar o ponto de extremidade de autenticação de dispositivo do Azure AD às zonas da intranet local

Para evitar prompts de certificado quando os usuários de dispositivos registrados são autenticados no Azure AD, você pode enviar uma política para seus dispositivos ingressados no domínio para adicionar a seguinte URL à zona de intranet local no Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Controlar dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, tem de transferir e instalar um pacote do Windows Installer (.msi) do Centro de Transferências. Para obter mais informações, consulte a seção [validação controlada do ingresso do Azure ad híbrido em dispositivos de nível inferior do Windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Verificar dispositivos associados

Você pode verificar se há dispositivos ingressados com êxito na sua organização usando o cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

A saída deste cmdlet mostra os dispositivos que estão registados e associados ao Azure AD. Para obter todos os dispositivos, use o parâmetro **-All** e filtre-os usando a propriedade **deviceTrustType** . Os dispositivos ingressados no domínio têm um valor de ingressado no **domínio**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se você estiver tendo problemas com a conclusão da junção híbrida do Azure AD para dispositivos Windows ingressados no domínio, consulte:

* [Troubleshooting Hybrid Azure AD join for Windows current devices](troubleshoot-hybrid-join-windows-current.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows atuais)
* [Troubleshooting Hybrid Azure AD join for Windows down-level devices](troubleshoot-hybrid-join-windows-legacy.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows de nível inferior)

## <a name="next-steps"></a>Passos Seguintes

* [Introduction to device management in Azure Active Directory](overview.md) (Introdução à gestão de dispositivos no Azure Active Directory)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
