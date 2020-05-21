---
title: Configurar dispositivos híbridos associados ao Azure Active Directory manualmente | Microsoft Docs
description: Saiba como configurar manualmente os dispositivos híbridos Azure Ative Diretório.
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
ms.openlocfilehash: 596b47ecc0cf42e8cf1e7001c1462f55d34ff9c3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680285"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Tutorial: Configurar dispositivos híbridos associados ao Azure Active Directory manualmente.

Com a gestão de dispositivos no Azure Ative Directory (Azure AD), pode garantir que os utilizadores estão a aceder aos seus recursos a partir de dispositivos que cumprem os seus padrões de segurança e conformidade. Para mais informações, consulte Introdução à gestão de [dispositivos no Diretório Ativo do Azure](overview.md).

> [!TIP]
> Se utilizar o Azure AD Connect é uma opção para si, consulte os tutoriais relacionados para domínios [geridos](hybrid-azuread-join-managed-domains.md) ou [federados.](hybrid-azuread-join-federated-domains.md) Ao utilizar o Azure AD Connect, pode simplificar significativamente a configuração da adesão híbrida azure.

Se tiver um ambiente do Active Directory no local e quiser associar os seus dispositivos associados ao domínio ao Azure AD, pode fazê-lo ao configurar os dispositivos híbridos associados ao Azure AD. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Configure manualmente a ad azure híbrida
> * Configurar um ponto de ligação de serviço
> * Criação de reclamações
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolver problemas relacionados com a implementação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que está familiarizado com:

* [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introdução à gestão de dispositivos no Azure Active Directory)
* [Planeie o seu Diretório Ativo Azure híbrido aderir à implementação](hybrid-azuread-join-plan.md)
* [Controlar a associação híbrida do Azure AD dos seus dispositivos](hybrid-azuread-join-control.md)

Antes de começar a ativar dispositivos híbridos Azure AD na sua organização, certifique-se de que:

* Estás a executar uma versão atualizada do Azure AD Connect.
* O Azure AD Connect sincronizou os objetos informáticos dos dispositivos que pretende ser híbrido Azure AD juntou-se ao Azure AD. Se os objetos informáticos pertencerem a unidades organizacionais específicas (OUs), estas OUs também precisam de ser configuradas para sincronização no Azure AD Connect.

Azure AD Connect:

* Mantém a associação entre a conta de computador no seu diretório ativo no local e o objeto do dispositivo em Azure AD.
* Permite outras funcionalidades relacionadas com dispositivos, como o Windows Hello for Business.

Certifique-se de que os seguintes URLs estão acessíveis a partir de computadores dentro da rede da sua organização para registo de computadores para AD Azure:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* STS da sua organização (para domínios federados), que deve ser incluído nas definições de intranet locais do utilizador

Se a sua organização planeia utilizar O SSO SSO sem emenda, o url seguinte tem de ser acessível a partir dos computadores dentro da sua organização. Deve também ser adicionado à zona intranet local do utilizador.

* `https://autologon.microsoftazuread-sso.com`

Além disso, a definição seguinte deve ser ativada na zona de intranet do utilizador: “Permitir atualizações da barra de estado através de script”.

Se a sua organização utilizar a configuração gerida (não federada) com o Ative Directory no local e não utilizar os Serviços da Federação de Diretórios Ativos (AD FS) para federar com o Azure AD, então a adesão híbrida do Azure AD ao Windows 10 conta com os objetos informáticos no Ative Directory para serem sincronizados com a Azure AD. Certifique-se de que quaisquer OUs que contenham os objetos informáticos que precisam de ser híbridos Azure AD estão ativados para sincronização na configuração de sincronização Azure AD Connect.

Para dispositivos Windows 10 na versão 1703 ou mais cedo, se a sua organização necessitar de acesso à internet através de um proxy de saída, tem de implementar o Web Proxy Auto-Discovery (WPAD) para permitir que os computadores do Windows 10 se registem no Azure AD.

Começando pelo Windows 10 1803, mesmo que um Azure AD híbrido se junte a uma tentativa de adesão de um dispositivo num domínio federado através de falhas aAFS, e se o Azure AD Connect estiver configurado para sincronizar os objetos do computador/dispositivo para o Azure AD, o dispositivo tentará completar a adesão híbrida do Azure AD utilizando o computador/dispositivo sincronizado.

Para verificar se o dispositivo é capaz de aceder aos recursos da Microsoft acima na conta do sistema, pode utilizar o script de conectividade de registo de [dispositivos de teste.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="verify-configuration-steps"></a>Verificar passos de configuração

Pode configurar os dispositivos híbridos associados ao Azure AD para vários tipos de plataformas de dispositivos Windows. Este tópico inclui os passos necessários para todos os cenários de configuração comuns.  

Utilize a tabela abaixo para obter uma descrição geral dos passos necessários para o seu cenário:  

| Passos | Dispositivos Windows atuais e sincronização de hash de palavra-passe | Dispositivos Windows atuais e federação | Dispositivos Windows de nível inferior |
| :--- | :---: | :---: | :---: |
| Configurar o ponto de ligação do serviço | ![Marcar][1] | ![Marcar][1] | ![Marcar][1] |
| Criação de reclamações |     | ![Marcar][1] | ![Marcar][1] |
| Permitir dispositivos não Windows 10 |       |        | ![Marcar][1] |
| Verificar dispositivos associados | ![Marcar][1] | ![Marcar][1] | ![Marcar][1] |

## <a name="configure-a-service-connection-point"></a>Configurar um ponto de ligação de serviço

Os seus dispositivos utilizam um objeto de ponto de ligação de serviço (SCP) durante o registo para descobrir informações do inquilino DaD Azure. No seu caso de Diretório Ativo no local, o objeto SCP para os dispositivos híbridos azure ad deve existir na configuração do contexto de divisão da floresta do computador. Só existe um contexto de nomenclatura de configuração por floresta. Numa configuração multi-florestal do Diretório Ativo, o ponto de ligação de serviço deve existir em todas as florestas que contenham computadores unidos pelo domínio.

Pode utilizar o cmdlet [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) para obter o contexto de nomenclatura da configuração da sua floresta.  

Para uma floresta com o nome de domínio do Active Directory *fabrikam.com*, o contexto de nomenclatura da configuração é:

`CN=Configuration,DC=fabrikam,DC=com`

Na sua floresta, o objeto SCP do registo automático dos dispositivos associados a um domínio está localizado em:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Dependendo da forma como implementou o Azure AD Connect, o objeto SCP pode já ter sido configurado.
Pode verificar a existência do objeto e recuperar os valores de descoberta utilizando o seguinte script Windows PowerShell:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

O **$scp. A saída de palavras-chave** mostra a informação do inquilino da AD Azure. Eis um exemplo:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Se o ponto de ligação do serviço não existir, pode executar o cmdlet `Initialize-ADSyncDomainJoinedComputerSync` no servidor do Azure AD Connect para criá-lo. As credenciais de administração da empresa são necessárias para executar este cmdlet.  

O cmdlet:

* Cria o ponto de ligação de serviço na floresta de Diretório Ativo a que o Azure AD Connect está ligado.
* Requer que seja especificado o parâmetro `AdConnectorAccount`. Esta conta está configurada como a conta de conector de Diretório Ativo no Azure AD Connect.


O script abaixo mostra um exemplo da utilização do cmdlet. Neste script, `$aadAdminCred = Get-Credential` requer que indique um nome de utilizador. Tem de indicar o nome de utilizador no formato de nome principal de utilizador (UPN) (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

O cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

* Utiliza o módulo PowerShell de Diretório Ativo e as ferramentas Azure Ative Directory Domain Services (Azure AD DS). Estas ferramentas dependem de Serviços Web de Diretório Ativo que executam um controlador de domínio. Os Serviços Web do Active Directory são suportados em controladores de domínio em execução no Windows Server 2008 R2 e posterior.
* Só são suportados pela versão 1.1.166.0 do módulo MSOnline do PowerShell. Para descarregar este módulo, utilize [este link](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0).
* Se as ferramentas AD DS não estiverem instaladas, `Initialize-ADSyncDomainJoinedComputerSync` falhará. Pode instalar as ferramentas AD DS através do Gestor de Servidores em **recursos**ferramentas de administração de  >  **servidores**  >  **remotos Ferramentas**de administração de funções .

Para controladores de domínio que executem o Windows Server 2008 ou versões anteriores, utilize o seguinte script para criar o ponto de ligação ao serviço. Numa configuração multiflorestal, utilize o seguinte script para criar o ponto de ligação de serviço em cada floresta onde existem computadores.

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

No guião anterior, `$verifiedDomain = "contoso.com"` é um espaço reservado. Substitua-o por um dos seus nomes de domínio verificados em Azure AD. Tens de ser dono do domínio antes de o poderes usar.

Para mais informações sobre nomes de domínio verificados, consulte Adicionar um nome de [domínio personalizado ao Diretório Ativo Azure](../active-directory-domains-add-azure-portal.md).

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Criação de reclamações

Numa configuração de AD Azure federada, os dispositivos contam com AD FS ou um serviço da federação no local de um parceiro da Microsoft para autenticar a AD Azure. Os dispositivos autenticam-se para obterem um token de acesso para se registarem no Registo de Dispositivos do Azure Active Directory (Azure DRS).

Os dispositivos atuais do Windows autenticam utilizando a Autenticação Integrada do Windows num ponto final ativo do WS-Trust (versões 1.3 ou 2005) hospedados pelo serviço da federação no local.

Quando estiver a usar AD FS, precisa de ativar os seguintes pontos finais wS-Trust
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Tanto as **adfs/services/trust/2005/windowstransport** e **adfs/services/trust/13/windowstransport** devem ser ativadas apenas como pontos finais intranet e NÃO devem ser expostas como pontos finais virados para a extranet através do Proxy de Aplicação Web. Para saber mais sobre como desativar os pontos finais do WS-Trust Windows, consulte [desativar os pontos finais do WS-Trust Windows no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pode ver quais os pontos finais ativados através **Service**da consola de gestão AD FS em  >  **pontos finais**de serviço .

> [!NOTE]
>Se não tiver AD FS como serviço da federação no local, siga as instruções do seu fornecedor para se certificar de que suporta pontos finais WS-Trust 1.3 ou 2005 e que estes sejam publicados através do ficheiro Demetadata Exchange (MEX).

Para que o registo do dispositivo termine, devem existir as seguintes reclamações no símbolo que o Azure DRS recebe. O Azure DRS criará um objeto de dispositivo em Azure AD com algumas destas informações. O Azure AD Connect utiliza então esta informação para associar o objeto do dispositivo recém-criado à conta de computador no local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se tiver vários nomes de domínio verificados, tem de indicar a seguinte afirmação para os computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se já está a emitir uma reclamação Imutável (por exemplo, utilizando `mS-DS-ConsistencyGuid` ou outro atributo como valor de origem para o IdI Imutável), tem de fornecer uma reclamação correspondente para computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nas secções abaixo, vai encontrar informações sobre:

* Os valores que cada reivindicação deve ter.
* Como seria uma definição em AD FS.

A definição ajuda-o a verificar se os valores estão presentes ou se precisa de os criar.

> [!NOTE]
> Se não utilizar o AD FS para o seu servidor de federação no local, siga as instruções do seu fornecedor para criar a configuração adequada para emitir estas afirmações.

### <a name="issue-account-type-claim"></a>Emitir afirmação de tipo de conta

A `http://schemas.microsoft.com/ws/2012/01/accounttype` alegação deve conter um valor de **DJ**, que identifica o dispositivo como um computador unido ao domínio. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

A `http://schemas.microsoft.com/identity/claims/onpremobjectguid` reclamação deve conter o valor **objectuguida** da conta de computador no local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` reclamação deve conter o **valor do objectoSid** da conta de computador no local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Emitir emite emidóide para o computador quando vários nomes de domínio verificados estão em Azure AD

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` reclamação deve conter o Identificador uniforme de recursos (URI) de qualquer um dos nomes de domínio verificados que se conectem com o serviço da federação no local (AD FS ou parceiro) que emite o símbolo. Em AD FS, pode adicionar emissão transformar regras que se parecem com as seguintes nessa ordem específica, após as anteriores. Note que uma regra para emitir explicitamente a regra para os utilizadores é necessária. Nas seguintes regras, é adicionada uma primeira regra que identifica o utilizador contra a autenticação do computador.

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

Na reivindicação anterior, `<verified-domain-name>` é um espaço reservado. Substitua-o por um dos seus nomes de domínio verificados em Azure AD. Por exemplo, utilize `Value = "http://contoso.com/adfs/services/trust/"` .

Para mais informações sobre nomes de domínio verificados, consulte Adicionar um nome de [domínio personalizado ao Diretório Ativo Azure](../active-directory-domains-add-azure-portal.md).  

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>Emita ID imutável para o computador quando existe um para utilizadores (por exemplo, utilizando mS-DS-ConsistênciaGuid como fonte de ID Imutável)

A `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` reclamação deve conter um valor válido para os computadores. No AD FS, pode criar uma regra de transformação de emissão da seguinte forma:

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

O seguinte guião ajuda-o na criação da emissão de regras de transformação descritas anteriormente.

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

* Este script anexa as regras às já existentes. Não faça o guião duas vezes, porque o conjunto de regras seria adicionado duas vezes. Antes de voltar a executar o script, confirme que não existem regras correspondentes para estas afirmações (com as condições correspondentes).
* Se tiver vários nomes de domínio verificados (como mostrado no portal Azure AD ou através do cmdlet **Get-MsolDomain),** detete o valor da **$multipleVerifiedDomainNames** no script para **$true**. Certifique-se também de que remove qualquer reclamação **emitida** existente que possa ter sido criada pelo Azure AD Connect ou por outros meios. Aqui está um exemplo para esta regra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Se já tiver emitido uma afirmação **ImmutableID** para as contas de utilizador, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script como **$true**.

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio são dispositivos de nível inferior do Windows, terá de:

* Definir uma política no Azure AD para permitir que os utilizadores registem dispositivos.
* Configurar o serviço de federação no local para emitir afirmações para suportar a Autenticação Integrada do Windows (IWA) para o registo de dispositivos.
* Adicione o ponto final de autenticação do dispositivo Azure AD às zonas intranet locais para evitar solicitações de certificado ao autenticar o dispositivo.
* Controle os dispositivos de nível inferior do Windows.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir uma política em AD Azure para permitir aos utilizadores registar em dispositivos

Para registar dispositivos de nível inferior do Windows, certifique-se de que a definição para permitir que os utilizadores registem dispositivos em AD Azure está ativada. No portal Azure, pode encontrar esta definição no âmbito de utilizadores de **Diretório Ativo Azure**e configurações de  >  **Users and groups**  >  **dispositivos**de grupos .

A seguinte política deve ser definida para **Todos:** **Os utilizadores podem registar os seus dispositivos com a AD Azure**.

![O botão All que permite aos utilizadores registar em dispositivos](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Configure o serviço da federação no local

O seu serviço de federação no local deve apoiar a emissão do método de **autenticação** e das reclamações **wiaormultiauthn** quando receber um pedido de autenticação à parte da AD Azure que detenha um parâmetro resource_params com o seguinte valor codificado:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Quando esse pedido vier, o serviço da federação no local deve autenticar o utilizador utilizando a Autenticação Integrada do Windows. Quando a autenticação for bem sucedida, o serviço da federação deve emitir as seguintes duas reclamações:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

Em AD FS, deve adicionar uma regra de transformação de emissão que passa pelo método de autenticação. Para adicionar esta regra:

1. Na consola de gestão AD FS, vá para **AD FS**  >  **Trust Relationships**Trust Trust  >  **Trusting Party Trusts**.
1. Clique com o botão direito do rato no objeto de confiança da entidade confiadora da Plataforma de Identidade do Microsoft Office 365 e selecione **Edit Claim Rules** (Editar Regras de Emissão).
1. No separador **Issuance Transform Rules** (Regras de Transformação de Emissão), selecione **Add Rule** (Adicionar Regra).
1. Na lista de modelos **Claim rule** (Regra de afirmação), selecione **Send Claims Using a Custom Rule** (Enviar Afirmações com uma Regra Personalizada).
1. Selecione **Seguinte**.
1. Na caixa de **nome sinuoso** pedido, introduza a Regra de Reivindicação do **Método Auth**.
1. Na caixa de **regras de reclamação,** introduza a seguinte regra:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. No servidor da federação, introduza o seguinte comando PowerShell. Substitua o ** \< RPObjectName \> ** pelo nome do objeto de parte de confiança para o seu objeto fidedigno de confiança da parte azure AD. Normalmente, este objeto é denominado **Plataforma de Identidade do Microsoft Objeto 365**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Adicione o ponto final de autenticação do dispositivo Azure AD às zonas intranet locais

Para evitar solicitações de certificadoquando os utilizadores de dispositivos registados autenticarem a AD Azure, pode empurrar uma política para os seus dispositivos unidos pelo domínio para adicionar o seguinte URL à zona intranet local no Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Controlar dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, tem de transferir e instalar um pacote do Windows Installer (.msi) do Centro de Transferências. Para mais informações, consulte a secção [Validação controlada de AD híbrido Azure em dispositivos de nível inferior windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Verificar dispositivos associados

Pode verificar se há dispositivos unidos com sucesso na sua organização utilizando o cmdlet [Get-MsolDevice](/powershell/msonline/v1/get-msoldevice) no módulo PowerShell do [Diretório Ativo Azure](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

A saída deste cmdlet mostra os dispositivos que estão registados e associados ao Azure AD. Para obter todos os dispositivos, utilize o **parâmetro -All** e, em seguida, filtre-os utilizando a propriedade **do dispositivoTrustType.** Os dispositivos unidos pelo domínio têm um valor de **Domínio Unido .**

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se estiver a ter problemas com a conclusão do Anúncio Híbrido Azure, junte-se a dispositivos Windows ligados ao domínio, consulte:

* [Troubleshooting Hybrid Azure AD join for Windows current devices](troubleshoot-hybrid-join-windows-current.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows atuais)
* [Troubleshooting Hybrid Azure AD join for Windows down-level devices](troubleshoot-hybrid-join-windows-legacy.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows de nível inferior)

## <a name="next-steps"></a>Próximos passos

* [Introduction to device management in Azure Active Directory](overview.md) (Introdução à gestão de dispositivos no Azure Active Directory)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
