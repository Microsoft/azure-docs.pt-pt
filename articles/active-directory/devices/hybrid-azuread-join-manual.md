---
title: Configurar dispositivos híbridos associados ao Azure Active Directory manualmente | Microsoft Docs
description: Aprenda a configurar manualmente dispositivos híbridos Azure Ative Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1aafcd8ace846d7da65d95d4148872d5a6eddeee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587855"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Tutorial: Configurar dispositivos híbridos associados ao Azure Active Directory manualmente.

Com a gestão de dispositivos no Azure Ative Directory (Azure AD), pode garantir que os utilizadores estão a aceder aos seus recursos a partir de dispositivos que cumprem os seus padrões de segurança e conformidade. Para obter mais informações, consulte [Introdução à gestão de dispositivos no Azure Ative Directory](overview.md).

> [!TIP]
> Se utilizar o Azure AD Connect é uma opção para si, consulte os tutoriais relacionados para domínios [geridos](hybrid-azuread-join-managed-domains.md) ou [federados.](hybrid-azuread-join-federated-domains.md) Ao utilizar o Azure AD Connect, pode simplificar significativamente a configuração da junção híbrida Azure AD.

Se tiver um ambiente do Active Directory no local e quiser associar os seus dispositivos associados ao domínio ao Azure AD, pode fazê-lo ao configurar os dispositivos híbridos associados ao Azure AD. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar manualmente a ad híbrida Azure
> * Configurar um ponto de ligação de serviço
> * Configurar a emissão de sinistros
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolver problemas relacionados com a implementação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que esteja familiarizado com:

* [Introduction to device management in Azure Active Directory](./overview.md) (Introdução à gestão de dispositivos no Azure Active Directory)
* [Planeie o seu Azure Ative Directory híbrido junte-se à implementação](hybrid-azuread-join-plan.md)
* [Controlar a associação híbrida do Azure AD dos seus dispositivos](hybrid-azuread-join-control.md)

Antes de começar a ativar dispositivos híbridos Azure AD na sua organização, certifique-se de que:

* Está a executar uma versão atualizada do Azure AD Connect.
* O Azure AD Connect sincronizou os objetos de computador dos dispositivos que pretende ser híbrido Azure AD ligados ao Azure AD. Se os objetos do computador pertencerem a unidades organizacionais específicas (OUs), estas OUs também precisam de ser configuradas para sincronização no Azure AD Connect.

Azure AD Connect:

* Mantém a associação entre a conta do computador no seu local de identificação do Ative Directory e o objeto do dispositivo no Azure AD.
* Permite outras funcionalidades relacionadas com dispositivos, como o Windows Hello for Business.

Certifique-se de que os seguintes URLs estão acessíveis a partir de computadores dentro da rede da sua organização para registo de computadores para Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* STS da sua organização (para domínios federados), que deve ser incluído nas definições intranet locais do utilizador

> [!WARNING]
> Se a sua organização utilizar servidores proxy que intercetam tráfego SSL para cenários como prevenção de perda de dados ou restrições de inquilinos AZure AD, certifique-se de que o tráfego para https://device.login.microsoftonline.com ' ' é excluído do break-and-inspect TLS. A não exclusão https://device.login.microsoftonline.com pode causar interferência na autenticação do certificado do cliente, causando problemas com o registo do dispositivo e o acesso condicional baseado no dispositivo.

Se a sua organização planeia utilizar o Seamless SSO, o seguinte URL tem de ser acessível a partir dos computadores dentro da sua organização. Deve também ser adicionado à zona intranet local do utilizador.

* `https://autologon.microsoftazuread-sso.com`

Além disso, a definição seguinte deve ser ativada na zona de intranet do utilizador: “Permitir atualizações da barra de estado através de script”.

Se a sua organização utilizar a configuração gerida (não federada) com o Ative Directory no local e não utilizar os Serviços da Federação de Diretórios Ativos (AD FS) para federar com a Azure AD, então o Azure AD híbrido junta-se ao Windows 10, contando com os objetos de computador no Ative Directory para serem sincronizados com a AZURE AD. Certifique-se de que quaisquer OUs que contenham os objetos de computador que precisam de ser híbridos Azure AD estão ativados para sincronização na configuração de sincronização Azure AD Connect.

Para dispositivos Windows 10 na versão 1703 ou mais cedo, se a sua organização necessitar de acesso à internet através de um representante de saída, tem de implementar o Web Proxy Auto-Discovery (WPAD) para permitir que os computadores do Windows 10 se registem no Azure AD.

Começando pelo Windows 10 1803, mesmo que um AD híbrido Azure se junte à tentativa de um dispositivo num domínio federado através do AD FS falhar, e se o Azure AD Connect estiver configurado para sincronizar os objetos do computador/dispositivo para o AZure AD, o dispositivo tentará completar a adada híbrida Azure através do computador/dispositivo sincronizado.

> [!NOTE]
> Para obter a sincronização do registo do dispositivo para ter sucesso, como parte da configuração de registo do dispositivo, não exclua os atributos predefinidos do dispositivo da sua configuração de sincronização Azure AD Connect. Para saber mais sobre os atributos padrão do dispositivo sincronizados com AZure AD, consulte [Atributos sincronizados pelo Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Para verificar se o dispositivo é capaz de aceder aos recursos acima da Microsoft sob a conta do sistema, pode utilizar o script [de Conectividade de Registo do Dispositivo de Teste.](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/)

## <a name="verify-configuration-steps"></a>Verificar etapas de configuração

Pode configurar os dispositivos híbridos associados ao Azure AD para vários tipos de plataformas de dispositivos Windows. Este tópico inclui os passos necessários para todos os cenários de configuração comuns.  

Utilize a tabela abaixo para obter uma descrição geral dos passos necessários para o seu cenário:  

| Passos | Dispositivos Windows atuais e sincronização de hash de palavra-passe | Dispositivos Windows atuais e federação | Dispositivos Windows de nível inferior |
| :--- | :---: | :---: | :---: |
| Configurar o ponto de ligação do serviço | ![Marcar][1] | ![Marcar][1] | ![Marcar][1] |
| Configurar a emissão de sinistros |     | ![Marcar][1] | ![Marcar][1] |
| Permitir dispositivos não Windows 10 |       |        | ![Marcar][1] |
| Verificar dispositivos associados | ![Marcar][1] | ![Marcar][1] | ![Marcar][1] |

## <a name="configure-a-service-connection-point"></a>Configurar um ponto de ligação de serviço

Os seus dispositivos utilizam um objeto de ligação de serviço (SCP) durante o registo para descobrir informações sobre o inquilino da Azure AD. No seu exemplo de Ative Directory no local, o objeto SCP para os dispositivos híbridos Azure AD devem existir na divisão de contexto de configuração da floresta do computador. Só existe um contexto de nomenclatura de configuração por floresta. Numa configuração multi-forest Ative Directory, o ponto de ligação de serviço deve existir em todas as florestas que contenham computadores ligados ao domínio.

Pode utilizar o cmdlet [**Get-ADRootDSE**](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee617246(v=technet.10)) para obter o contexto de nomenclatura da configuração da sua floresta.  

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

O **$scp. A produção de palavras-chave** mostra a informação do inquilino Azure AD. Eis um exemplo:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Se o ponto de ligação do serviço não existir, pode executar o cmdlet `Initialize-ADSyncDomainJoinedComputerSync` no servidor do Azure AD Connect para criá-lo. As credenciais de administração da empresa são necessárias para executar este cmdlet.  

O cmdlet:

* Cria o ponto de ligação de serviço na floresta ative diretório a que o Azure AD Connect está ligado.
* Requer que seja especificado o parâmetro `AdConnectorAccount`. Esta conta está configurada como a conta de conector ative directory em Azure AD Connect.


O script abaixo mostra um exemplo da utilização do cmdlet. Neste script, `$aadAdminCred = Get-Credential` requer que indique um nome de utilizador. Tem de indicar o nome de utilizador no formato de nome principal de utilizador (UPN) (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

O cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

* Utiliza as ferramentas PowerShell do Diretório Ativo e serviços de domínio de diretório ativo (DS AD). Estas ferramentas dependem de Serviços Web ative directory em execução num controlador de domínio. Os Serviços Web do Active Directory são suportados em controladores de domínio em execução no Windows Server 2008 R2 e posterior.
* Só são suportados pela versão 1.1.166.0 do módulo MSOnline do PowerShell. Para descarregar este módulo, utilize [este link](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0).
* Se as ferramentas DS AD não estiverem instaladas, `Initialize-ADSyncDomainJoinedComputerSync` falhará. Pode instalar as ferramentas AD DS através do Gestor do Servidor sob **funcionalidades**  >  **ferramentas de administração de ferramentas de administração de**  >  **servidores remotos .**

Para controladores de domínio que executam as versões do Windows Server 2008 ou anteriores, utilize o seguinte script para criar o ponto de ligação de serviço. Numa configuração multi-floresta, utilize o seguinte script para criar o ponto de ligação de serviço em cada floresta onde existem computadores.

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

No guião anterior, `$verifiedDomain = "contoso.com"` é um espaço reservado. Substitua-o por um dos seus nomes de domínio verificados no Azure AD. Tem que ser dono do domínio antes de poder usá-lo.

Para obter mais informações sobre os nomes de domínio verificados, consulte [adicionar um nome de domínio personalizado ao Azure Ative Directory](../fundamentals/add-custom-domain.md).

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Configurar a emissão de sinistros

Numa configuração AD federada, os dispositivos contam com FS AD ou um serviço de federação no local de um parceiro da Microsoft para autenticar a Azure AD. Os dispositivos autenticam-se para obterem um token de acesso para se registarem no Registo de Dispositivos do Azure Active Directory (Azure DRS).

Os dispositivos atuais do Windows autenticam-se utilizando a Autenticação Integrada do Windows num ponto final de WS-Trust ativo (versões 1.3 ou 2005) hospedados pelo serviço da federação no local.

Quando estiver a utilizar OD FS, tem de ativar os seguintes pontos finais WS-Trust
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Tanto **adfs/serviços/trust/2005/windowstransport** e **adfs/services/trust/13/windowstransport** devem ser ativados apenas como pontos finais virados para a intranet e NÃO devem ser expostos como pontos finais virados para a extranet através do Proxy da Aplicação Web. Para saber mais sobre como desativar WS-Trust pontos finais do Windows, consulte [pontos finais do Windows desativar WS-Trust no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pode ver quais os pontos finais que estão ativados através da consola de gestão AD FS em **Service**  >  **Endpoints**.

> [!NOTE]
>Se não tiver AD FS como serviço da federação no local, siga as instruções do seu fornecedor para se certificar de que suporta WS-Trust pontos finais 1.3 ou 2005 e que estes são publicados através do ficheiro Metadadata Exchange (MEX).

Para que o registo do dispositivo termine, devem existir as seguintes alegações no sinal que a Azure DRS recebe. O Azure DRS criará um objeto de dispositivo em Azure AD com algumas destas informações. O Azure AD Connect utiliza então esta informação para associar o objeto do dispositivo recém-criado à conta do computador no local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se tiver vários nomes de domínio verificados, tem de indicar a seguinte afirmação para os computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se já está a emitir uma reclamação ImutávelID (por exemplo, utilizando `mS-DS-ConsistencyGuid` ou outro atributo como valor de origem para o ImuttableID), tem de fornecer uma reclamação correspondente para computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nas secções abaixo, vai encontrar informações sobre:

* Os valores que cada reivindicação deve ter.
* Como seria uma definição em AD FS.

A definição ajuda-o a verificar se os valores estão presentes ou se precisa de os criar.

> [!NOTE]
> Se não utilizar o AD FS para o seu servidor de federação no local, siga as instruções do seu fornecedor para criar a configuração adequada para emitir estas afirmações.

### <a name="issue-account-type-claim"></a>Emitir afirmação de tipo de conta

A `http://schemas.microsoft.com/ws/2012/01/accounttype` alegação deve conter um valor de **DJ**, que identifica o dispositivo como um computador de domínio. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

A `http://schemas.microsoft.com/identity/claims/onpremobjectguid` reclamação deve conter o valor **objectGUID** da conta informática no local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` reclamação deve conter o **valor do objectSid** da conta informática no local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Emite emitenteID para o computador quando vários nomes de domínio verificados estão em Azure AD

A `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` reclamação deve conter o identificador de recursos uniforme (URI) de qualquer um dos nomes de domínio verificados que se conectem com o serviço da federação no local (AD FS ou parceiro) que emite o token. Em AD FS, pode adicionar regras de transformação de emissão que se parecem com as seguintes nessa ordem específica, após as anteriores. Note que é necessária uma regra para emitir explicitamente a regra para os utilizadores. Nas seguintes regras, é adicionada uma primeira regra que identifica o utilizador contra a autenticação do computador.

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

Na reivindicação anterior, `<verified-domain-name>` é um espaço reservado. Substitua-o por um dos seus nomes de domínio verificados no Azure AD. Por exemplo, usar `Value = "http://contoso.com/adfs/services/trust/"` .

Para obter mais informações sobre os nomes de domínio verificados, consulte [adicionar um nome de domínio personalizado ao Azure Ative Directory](../fundamentals/add-custom-domain.md).  

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>Emissão ImutávelID para o computador quando existe um para os utilizadores (por exemplo, usando mS-DS-ConsistênciaGuid como fonte para ImutávelID)

A `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` reclamação deve conter um valor válido para computadores. No AD FS, pode criar uma regra de transformação de emissão da seguinte forma:

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

O seguinte script ajuda-o na criação das regras de transformação de emissão descritas anteriormente.

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
* Se tiver vários nomes de domínio verificados (como mostrado no portal AD AZure ou através do cmdlet **Get-MsolDomain),** deslote o valor de **$multipleVerifiedDomainNames** no script para **$true**. Certifique-se também de que remove qualquer reclamação **emitida** existente que possa ter sido criada pelo Azure AD Connect ou por outros meios. Aqui está um exemplo para esta regra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Se já tiver emitido uma afirmação **ImmutableID** para as contas de utilizador, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script como **$true**.

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio são dispositivos de nível inferior do Windows, terá de:

* Definir uma política no Azure AD para permitir que os utilizadores registem dispositivos.
* Configurar o serviço de federação no local para emitir afirmações para suportar a Autenticação Integrada do Windows (IWA) para o registo de dispositivos.
* Adicione o ponto final de autenticação do dispositivo Azure AD às zonas intranet locais para evitar indicações de certificado ao autenticar o dispositivo.
* Controlar dispositivos de nível inferior do Windows.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir uma política em Azure AD para permitir que os utilizadores registem dispositivos

Para registar dispositivos de nível inferior do Windows, certifique-se de que a definição para permitir que os utilizadores registem dispositivos em Azure AD está ativada. No portal Azure, pode encontrar esta definição sob a **azure Ative Directory**  >  **Users e grupos**  >  **Configurações do Dispositivo**.

A seguinte política deve ser definida para **Todos**: **Os utilizadores podem registar os seus dispositivos com Azure AD**.

![O botão All que permite aos utilizadores registarem dispositivos](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Configurar o serviço da federação no local

O seu serviço de federação no local deve apoiar a emissão das reclamações de **método de autenticação** e **wiaormultiauthn** quando receber um pedido de autenticação ao Azure AD que detém um parâmetro resource_params com o seguinte valor codificado:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Quando tal pedido chegar, o serviço da federação no local deve autenticar o utilizador utilizando a Autenticação Integrada do Windows. Quando a autenticação for bem sucedida, o serviço da federação deve emitir as seguintes duas reclamações:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

Em AD FS, deve adicionar uma regra de transformação de emissão que passa pelo método de autenticação. Para adicionar esta regra:

1. Na consola de gestão de FS AD, vá para **AD FS**  >  **Trust Relationships**  >  **Relying Party Trusts**.
1. Clique com o botão direito do rato no objeto de confiança da entidade confiadora da Plataforma de Identidade do Microsoft Office 365 e selecione **Edit Claim Rules** (Editar Regras de Emissão).
1. No separador **Issuance Transform Rules** (Regras de Transformação de Emissão), selecione **Add Rule** (Adicionar Regra).
1. Na lista de modelos **Claim rule** (Regra de afirmação), selecione **Send Claims Using a Custom Rule** (Enviar Afirmações com uma Regra Personalizada).
1. Selecione **Seguinte**.
1. Na caixa **de nome da regra reivindicação,** insira a Regra de **Reclamação do Método Auth**.
1. Na caixa **de regras 'Reclamação',** insira a seguinte regra:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. No servidor da federação, insira o seguinte comando PowerShell. **\<RPObjectName\>** Substitua-o pelo nome do objeto do partido que conta para o seu azure AD confiando no objeto de confiança do partido. Normalmente, este objeto é denominado **Plataforma de Identidade do Microsoft Objeto 365**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Adicione o ponto final de autenticação do dispositivo Azure às zonas intranet locais

Para evitar instruções de certificado quando os utilizadores de dispositivos registados autenticam a AZure AD, pode empurrar uma política para os seus dispositivos unidos pelo domínio para adicionar o seguinte URL à zona intranet local no Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Controlar dispositivos de nível inferior do Windows

Para registar dispositivos de nível inferior do Windows, tem de transferir e instalar um pacote do Windows Installer (.msi) do Centro de Transferências. Para obter mais informações, consulte a secção [Validação controlada do Ad híbrido Azure adere em dispositivos de nível inferior do Windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Verificar dispositivos associados

Aqui estão 3 formas de localizar e verificar o estado do dispositivo:

### <a name="locally-on-the-device"></a>Localmente no dispositivo

1. Abra o Windows PowerShell.
2. Introduza `dsregcmd /status`.
3. Verifique se tanto **o AzureAdJoined** como **o DomainJoined** estão definidos para **SIM**.
4. Pode utilizar o **DeviceId** e comparar o estado do serviço utilizando o portal Azure ou o PowerShell.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

1. Aceda à página dos dispositivos utilizando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informações sobre como localizar um dispositivo podem ser encontradas em [Como gerir as identidades do dispositivo utilizando o portal Azure](./device-management-azure-portal.md#manage-devices).
3. Se a coluna **Registada** **disser Pendente,** então a Hybrid Azure AD Join não está concluída. Em ambientes federados, isto só pode acontecer se não se registar e a ligação AAD estiver configurada para sincronizar os dispositivos.
4. Se a coluna **Registada** contiver uma **data/hora,** então a Hybrid Azure AD Join foi concluída.

### <a name="using-powershell"></a>Com o PowerShell

Verifique o estado de registo do dispositivo no seu inquilino Azure utilizando **[a Get-MsolDevice](/powershell/module/msonline/get-msoldevice)**. Este cmdlet está no [módulo PowerShell do Diretório Ativo Azure](/powershell/azure/active-directory/install-msonlinev1).

Quando utilizar o **cmdlet Get-MSolDevice** para verificar os detalhes do serviço:

- Deve existir um objeto com o **ID** do dispositivo que corresponda ao ID do cliente Windows.
- O valor para **DeviceTrustType** é **o Domínio Unido**. Esta definição é equivalente ao **AD Híbrido Azure aderiu ao** estado na página **dispositivos** no portal AD Azure.
- Para dispositivos utilizados no Acesso Condicional, o valor para **Ativado** é **Verdadeiro** e **o DeviceTrustLevel** é **Gerido**.

1. Abra o Windows PowerShell Como um administrador.
2. Entre `Connect-MsolService` para ligar ao seu inquilino Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Conte todos os dispositivos híbridos Azure AD (excluindo estado **pendente)**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Conte todos os AD híbridos Ad AD unidos com estado **pendente**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Listar todos os dispositivos híbridos Azure AD

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Listar todos os dispositivos híbridos Azure AD com estado **pendente**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Listar detalhes de um único dispositivo:

1. `get-msoldevice -deviceId <deviceId>`Insira (Este é o **DeviceId** obtido localmente no dispositivo).
2. Certifique-se de que **Ativado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se sentir problemas em completar a ad AD híbrida para dispositivos Windows unidos pelo domínio, consulte:

- [Dispositivos de resolução de problemas utilizando comando dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Resolver problemas de dispositivos associados ao Azure Active Directory híbrido](troubleshoot-hybrid-join-windows-current.md)
- [O Azure Ative Directory híbrido de resolução de problemas juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

* [Introduction to device management in Azure Active Directory](overview.md) (Introdução à gestão de dispositivos no Azure Active Directory)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
