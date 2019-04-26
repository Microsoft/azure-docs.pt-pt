---
title: Configurar dispositivos híbridos associados ao Azure Active Directory manualmente | Microsoft Docs
description: Saiba como configurar manualmente híbrida do Azure Active Directory associados a um dispositivos.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3944031101b954c380b5db6503002dae57ea16d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60352422"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Tutorial: Configurar manualmente o híbrida do Azure Active Directory associados a um dispositivos 

Gestão de dispositivos no Azure Active Directory (Azure AD), pode certificar-se de que os utilizadores estão a aceder ao seus recursos de dispositivos que cumprem as normas de segurança e conformidade. Para obter mais informações, consulte [introdução à gestão de dispositivos no Azure Active Directory](overview.md).


> [!TIP]
> Se utilizar o Azure AD Connect é uma opção para si, veja os tutoriais relacionados para [geridos](hybrid-azuread-join-managed-domains.md) ou [federado](hybrid-azuread-join-federated-domains.md) domínios. Ao utilizar o Azure AD Connect, pode simplificar significativamente a configuração de associação do Azure AD híbrido.

Se tiver um ambiente do Active Directory no local e quiser associar os seus dispositivos associados ao domínio ao Azure AD, pode fazê-lo ao configurar os dispositivos híbridos associados ao Azure AD. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar manualmente a associação do Azure AD híbrido
> * Configurar um ponto de ligação de serviço
> * Configurar a emissão de afirmações
> * Permitir dispositivos de nível inferior do Windows
> * Verificar dispositivos associados
> * Resolver problemas relacionados com a implementação
 




## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que esteja familiarizado com:
    
-  [Introduction to device management in Azure Active Directory](../device-management-introduction.md) (Introdução à gestão de dispositivos no Azure Active Directory)    
-  [Planear a sua implementação híbrida do Azure Active Directory](hybrid-azuread-join-plan.md)
-  [Controlar a associação híbrida do Azure AD dos seus dispositivos](hybrid-azuread-join-control.md)


Antes de iniciar a ativação híbridos associados ao Azure AD dispositivos na sua organização, certifique-se de que:

- Está a executar uma versão atualizada do Azure AD Connect.
- O Azure AD Connect tem sincronizados os objetos de computador dos dispositivos à que sua escolha para serem híbridos associados ao Azure AD para o Azure AD. Se os objetos de computador pertencem a específicas unidades organizacionais (UOs), tem de ser configurado para a sincronização no Azure AD Connect também esses UOs.

  

Azure AD Connect:

- Mantém a associação entre a conta de computador na sua instância do Active Directory no local e o objeto de dispositivo no Azure AD. 
- Permite que outros recursos relacionados com o dispositivo, como Windows Hello para empresas.

Certifique-se de que os seguintes URLs são acessíveis a partir de computadores dentro da rede da sua organização para o registo dos computadores com o Azure AD:

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- STS de sua organização (para domínios federados), que devem ser incluídas nas definições de local intranet do utilizador

Se sua organização planeja usar o SSO totalmente integrado, o seguinte URL tem de ser acessível a partir de computadores dentro de sua organização. Tem também de ser adicionado à zona de local intranet do usuário.

- https://autologon.microsoftazuread-sso.com

Além disso, a definição seguinte deve ser ativada na zona de intranet do utilizador: "Permitir atualizações da barra de estado por meio de script".

Se a organização utilizar a configuração (não federadas) gerida com o Active Directory no local e não utiliza serviços de Federação do Active Directory (AD FS) para federar com o Azure AD, em seguida, os objetos de computador no Active Directory depende da associação do Azure AD híbrido no Windows 10 Diretório a ser sincronizada com o Azure AD. Certifique-se de que quaisquer UOs que contêm o computador de objetos que precisam para serem híbridos associados ao Azure AD estão ativados para sincronização na configuração de sincronização do Azure AD Connect.

Para dispositivos Windows 10 versão 1703 ou anterior, se sua organização necessita de acesso à internet através de um proxy de saída, tem de implementar descoberta automática WPAD (Web Proxy) para permitir que os computadores Windows 10 para se registrar para o Azure AD. 

A partir do Windows 10 1803, mesmo que a falha de uma tentativa de associação do Azure AD híbrido por um dispositivo num domínio federado através do AD FS e se o Azure AD Connect estiver configurado para sincronizar os objetos de computador/dispositivo para o Azure AD, o dispositivo tentará concluir a associação do Azure AD híbrido por nós ing do computador/dispositivo sincronizado.

## <a name="verify-configuration-steps"></a>Certifique-se passos de configuração

Pode configurar os dispositivos híbridos associados ao Azure AD para vários tipos de plataformas de dispositivos Windows. Este tópico inclui os passos necessários para todos os cenários de configuração comuns.  

Utilize a tabela abaixo para obter uma descrição geral dos passos necessários para o seu cenário:  



| Passos                                      | Dispositivos Windows atuais e sincronização de hash de palavra-passe | Dispositivos Windows atuais e federação | Dispositivos Windows de nível inferior |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Configurar o ponto de ligação do serviço | ![Marcar][1]                            | ![Marcar][1]                    | ![Marcar][1]        |
| Configurar a emissão de afirmações           |                                        | ![Marcar][1]                    | ![Marcar][1]        |
| Permitir dispositivos não Windows 10      |                                        |                                | ![Marcar][1]        |
| Verificar dispositivos associados          | ![Marcar][1]                            | ![Marcar][1]                    | ![Marcar][1]        |



## <a name="configure-a-service-connection-point"></a>Configurar um ponto de ligação de serviço

Os dispositivos utilizam um objeto de (SCP) do ponto de ligação de serviço durante o registo para detetar informações de inquilino do Azure AD. Na sua instância do Active Directory no local, o objeto de SCP para dispositivos associados ao Azure AD híbrido tem de existir na configuração da atribuição de nomes de partição de contexto da floresta do computador. Só existe um contexto de nomenclatura de configuração por floresta. Numa configuração várias floresta do Active Directory, o ponto de ligação de serviço tem de existir em todas as florestas que contêm os computadores associados a um domínio.

Pode utilizar o cmdlet [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) para obter o contexto de nomenclatura da configuração da sua floresta.  

Para uma floresta com o nome de domínio do Active Directory *fabrikam.com*, o contexto de nomenclatura da configuração é:

`CN=Configuration,DC=fabrikam,DC=com`

Na sua floresta, o objeto SCP do registo automático dos dispositivos associados a um domínio está localizado em:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Dependendo de como tiver implementado o Azure AD Connect, o objeto de SCP poderá ter já sido configurado.
Pode verificar a existência do objeto e obter os valores de deteção, com o seguinte script do Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

O **$scp. Palavras-chave** resultado mostra as informações de inquilino do Azure AD. Segue-se um exemplo:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se o ponto de ligação do serviço não existir, pode executar o cmdlet `Initialize-ADSyncDomainJoinedComputerSync` no servidor do Azure AD Connect para criá-lo. Credenciais de administrador de empresa são necessários para executar este cmdlet.  

O cmdlet:

- Cria o ponto de ligação de serviço em que o Azure AD Connect está ligado à floresta do Active Directory. 
- Requer que seja especificado o parâmetro `AdConnectorAccount`. Esta conta está configurada como conta de conector do Active Directory no Azure AD Connect. 


O script abaixo mostra um exemplo da utilização do cmdlet. Neste script, `$aadAdminCred = Get-Credential` requer que indique um nome de utilizador. Tem de indicar o nome de utilizador no formato de nome principal de utilizador (UPN) (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

O cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

- Utiliza o módulo do PowerShell do Active Directory e as ferramentas do Azure Active Directory Domain Services (Azure AD DS). Essas ferramentas confiam em serviços da Web do Active Directory em execução num controlador de domínio. Os Serviços Web do Active Directory são suportados em controladores de domínio em execução no Windows Server 2008 R2 e posterior.
- Só é suportada a versão 1.1.166.0 do módulo do PowerShell do MSOnline. Para transferir este módulo, utilize [esta ligação](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Se não estiverem instaladas as ferramentas do Azure AD DS, `Initialize-ADSyncDomainJoinedComputerSync` irá falhar. Pode instalar as ferramentas do Azure AD DS através do Gestor de servidor em **funcionalidades** > **ferramentas de administração remota do servidor** > **ferramentas de administração de função** .

Para controladores de domínio a executar o Windows Server 2008 ou versões anteriores, utilize o seguinte script para criar o ponto de ligação de serviço. Numa configuração de várias floresta, utilize o seguinte script para criar o ponto de ligação de serviço em cada floresta onde existem computadores.
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your Active Directory configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

No script anterior, `$verifiedDomain = "contoso.com"` é um marcador de posição. Substitua-o com um dos seus nomes de domínio verificado no Azure AD. Tem de ter o nome de domínio antes de poder utilizá-lo.

Para obter mais informações sobre nomes de domínio verificado, consulte [adicionar um nome de domínio personalizado ao Azure Active Directory](../active-directory-domains-add-azure-portal.md). 

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Lista de domínios de empresa](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Configurar a emissão de afirmações

Num Azure federado configuração do AD, dispositivos dependem do AD FS ou um serviço de Federação no local de um parceiro da Microsoft para autenticar para o Azure AD. Os dispositivos autenticam-se para obterem um token de acesso para se registarem no Registo de Dispositivos do Azure Active Directory (Azure DRS).

Autenticam dispositivos atuais do Windows utilizando a autenticação integrada do Windows para um ponto de extremidade do WS-Trust Active Directory (versões de 1,3 ou 2005) alojado pelo serviço de Federação no local.

> [!NOTE]
> Quando estiver a utilizar o AD FS, seja **adfs/serviços/confiança/13/windowstransport** ou **adfs/serviços/confiança/2005/windowstransport** tem de estar ativada. Se estiver a utilizar o Proxy de autenticação da Web, certifique-se também que este ponto final é publicado através do proxy. Pode ver quais pontos de extremidade habilitados por meio da consola de gestão do AD FS sob **serviço** > **pontos finais**.
>
>Se não tiver o AD FS que o seu serviço de Federação no local, siga as instruções do seu fornecedor para se certificar de que eles oferecem suporte a WS-Trust 1.3 ou pontos finais de 2005 e estas são publicadas através do ficheiro de troca de metadados (MEX).

Para o registo de dispositivos concluir, as seguintes declarações tem de existir no token que recebe o DRS do Azure. DRS do Azure irá criar um objeto de dispositivo no Azure AD com algumas destas informações. O Azure AD Connect, em seguida, utiliza estas informações para associar o objeto de dispositivo recentemente criada com o computador conta no local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se tiver vários nomes de domínio verificados, tem de indicar a seguinte afirmação para os computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se já emitir uma afirmação de ImmutableID (por exemplo, o ID de início de sessão alternativo), tem de fornecer uma afirmação correspondente para computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nas secções abaixo, vai encontrar informações sobre:
 
- Os valores que deve ter cada afirmação.
- O que uma definição de teria o seguinte aspeto no AD FS.

A definição ajuda-o a verificar se os valores estão presentes ou se precisa de os criar.

> [!NOTE]
> Se não utilizar o AD FS para o seu servidor de federação no local, siga as instruções do seu fornecedor para criar a configuração adequada para emitir estas afirmações.

### <a name="issue-account-type-claim"></a>Emitir afirmação de tipo de conta

O `http://schemas.microsoft.com/ws/2012/01/accounttype` afirmação tem de conter um valor de **DJ**, que identifica o dispositivo como um computador associado a um domínio. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Emitir o objectGUID da conta de computador no local

O `http://schemas.microsoft.com/identity/claims/onpremobjectguid` afirmação tem de conter o **objectGUID** valor da conta de computador no local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Emitir o objectSID da conta de computador no local

O `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` afirmação tem de conter o **objectSid** valor da conta de computador no local. No AD FS, pode adicionar uma regra de transformação de emissão semelhante à seguinte:

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Emitir issuerID para o computador quando vários nomes de domínio verificado no Azure AD

O `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` afirmação tem de conter o identificador URI (Uniform Resource) de qualquer um dos nomes de domínio verificado que se ligar ao serviço de Federação no local (AD FS ou o parceiro) emitir o token. No AD FS, pode adicionar regras de transformação de emissão que parecem com os seguintes por essa ordem específica, após os anteriores. Observe que uma regra para emitir explicitamente a regra para os utilizadores é necessária. Nas seguintes regras, é adicionada uma primeira regra que identifica o utilizador em comparação com a autenticação de computador.

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


Na afirmação anterior, `<verified-domain-name>` é um marcador de posição. Substitua-o com um dos seus nomes de domínio verificado no Azure AD. Por exemplo, utilizar `Value = "http://contoso.com/adfs/services/trust/"`.



Para obter mais informações sobre nomes de domínio verificado, consulte [adicionar um nome de domínio personalizado ao Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Para obter uma lista dos domínios verificados da sua empresa, pode utilizar o cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Lista de domínios de empresa](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Emitir ImmutableID para o computador quando existe por um para os utilizadores (por exemplo, um início de sessão alternativo ID está definido)

O `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` afirmação tem de conter um valor válido para computadores. No AD FS, pode criar uma regra de transformação de emissão da seguinte forma:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script de programa auxiliar para criar as regras de transformação de emissão do AD FS

O script seguinte ajuda-o com a criação de emissão descritas anteriormente de regras de transformação.

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

#### <a name="remarks"></a>Observações 

- Este script anexa as regras às já existentes. Não execute o script duas vezes, uma vez que o conjunto de regras seria adicionado duas vezes. Antes de voltar a executar o script, confirme que não existem regras correspondentes para estas afirmações (com as condições correspondentes).

- Se tiver vários nomes de domínio verificado (conforme mostrado no portal do Azure AD ou através da **Get-MsolDomain** cmdlet), defina o valor da **$multipleVerifiedDomainNames** no script para **$true** . Certifique-se de que remova qualquer existente também **issuerid** afirmação que poderá ter sido criada pelo Azure AD Connect ou através de outros meios. Eis um exemplo para esta regra:

      c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Se já tiver emitido uma afirmação **ImmutableID** para as contas de utilizador, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script como **$true**.

## <a name="enable-windows-down-level-devices"></a>Permitir dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio são dispositivos de nível inferior do Windows, terá de:

- Definir uma política no Azure AD para permitir que os utilizadores registem dispositivos. 
- Configure o seu serviço de Federação no local para emitir afirmações para suportar a autenticação integrada do Windows (IWA) para o registo do dispositivo. 
- Adicione o ponto de final de autenticação de dispositivo do Azure AD para a zona da intranet local, para evitar avisos de certificado ao autenticar o dispositivo.
- Controle os dispositivos de nível inferior do Windows. 


### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir uma política no Azure AD para permitir que os utilizadores registar dispositivos

Para registar dispositivos de nível inferior do Windows, certifique-se de que a definição para permitir que os utilizadores registar dispositivos no Azure AD está ativada. No portal do Azure, pode encontrar esta definição em **do Azure Active Directory** > **utilizadores e grupos** > **definições do dispositivo**.
    
A seguinte política tem de ser definida **todos os**: **Os utilizadores podem registar os seus dispositivos com o Azure AD**.

![O botão de todos os que permite aos utilizadores registar dispositivos](./media/hybrid-azuread-join-manual/23.png)


### <a name="configure-the-on-premises-federation-service"></a>Configurar o serviço de Federação no local 

O serviço de Federação no local tem de suportar a emitir a **authenticationmethod** e **wiaormultiauthn** afirmações quando recebe um pedido de autenticação à parte da entidade confiadora do Azure AD que contém um parâmetro de resource_params com o seguinte valor codificado:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Diz respeito este pedido, o serviço de Federação no local tem de autenticar o utilizador ao utilizar a autenticação integrada do Windows. Quando autenticação for bem-sucedida, o serviço de federação tem de emitir as afirmações de duas seguintes:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

No AD FS, tem de adicionar uma regra de transformação de emissão que passa pelo método de autenticação. Para adicionar esta regra:

1. Na consola de gestão do AD FS, aceda a **do AD FS** > **relações de confiança** > **entidade Confiadora confianças**.
2. Clique com o botão direito do rato no objeto de confiança da entidade confiadora da Plataforma de Identidade do Microsoft Office 365 e selecione **Edit Claim Rules** (Editar Regras de Emissão).
3. No separador **Issuance Transform Rules** (Regras de Transformação de Emissão), selecione **Add Rule** (Adicionar Regra).
4. Na lista de modelos **Claim rule** (Regra de afirmação), selecione **Send Claims Using a Custom Rule** (Enviar Afirmações com uma Regra Personalizada).
5. Selecione **Seguinte**.
6. Na **nome da regra de afirmação** , introduza **regra de afirmação de método de autenticação**.
7. Na **regra de afirmação** , introduza a seguinte regra:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. No servidor de Federação, introduza o seguinte comando do PowerShell. Substitua **\<RPObjectName\>** com o nome de objeto de terceiros entidade confiadora para seu objeto de confiança de terceiros da entidade confiadora do AD do Azure. Normalmente, este objeto é denominado **Plataforma de Identidade do Microsoft Objeto 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Adicionar o ponto de final de autenticação de dispositivo do Azure AD para a zona da intranet local

Para evitar avisos de certificado quando a autenticação de utilizadores dos dispositivos registados para o Azure AD, pode enviar por push uma política para os seus dispositivos associados a um domínio para adicionar o seguinte URL para a zona de local intranet no Internet Explorer:

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>Controlar dispositivos de nível inferior do Windows 

Para registar dispositivos de nível inferior do Windows, tem de transferir e instalar um pacote do Windows Installer (.msi) do Centro de Transferências. Para obter mais informações, consulte [controlar a associação do Azure AD híbrido dos seus dispositivos](hybrid-azuread-join-control.md#control-windows-down-level-devices). 



## <a name="verify-joined-devices"></a>Verificar dispositivos associados

Pode verificar para dispositivos associados com êxito na sua organização com o [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

A saída deste cmdlet mostra os dispositivos que estão registados e associados ao Azure AD. Para obter todos os dispositivos, utilize o **-todos os** parâmetro e, em seguida, filtrá-los utilizando o **deviceTrustType** propriedade. Dispositivos associados a um domínio têm um valor de **associados a um domínio**.



## <a name="troubleshoot-your-implementation"></a>Resolver problemas relacionados com a implementação

Se estiver a ter problemas com a conclusão da associação do Azure AD híbrido para dispositivos associados a domínios do Windows, consulte:

- [Troubleshooting Hybrid Azure AD join for Windows current devices](troubleshoot-hybrid-join-windows-current.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows atuais)
- [Troubleshooting Hybrid Azure AD join for Windows down-level devices](troubleshoot-hybrid-join-windows-legacy.md) (Resolver problemas com a associação híbrida ao Azure AD para dispositivos Windows de nível inferior)

## <a name="next-steps"></a>Passos Seguintes

* [Introduction to device management in Azure Active Directory](overview.md) (Introdução à gestão de dispositivos no Azure Active Directory)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
