---
title: Integração de datacenter do Azure Stack - identidade
description: Saiba como integrar o Azure Stack AD FS com o seu datacenter do AD FS
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 5f34991dca4dbb4275033c764981c44492b9920e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257813"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integração de datacenter do Azure Stack - identidade
Pode implementar o Azure Stack com o Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS) como os fornecedores de identidade. Deve fazer a escolha antes de implementar o Azure Stack. Num cenário conectado, pode escolher o Azure AD ou AD FS. Para um cenário de desligado, é suportado apenas do AD FS.

> [!IMPORTANT]
> Não é possível mudar o fornecedor de identidade sem implementar novamente toda a solução do Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Serviços de Federação do Active Directory e do Graph

Implementar com o AD FS permite que as identidades numa floresta existente do Active Directory para autenticar com recursos no Azure Stack. Esta floresta existente do Active Directory requer uma implementação do AD FS para permitir a criação de uma relação de confiança de Federação do AD FS.

A autenticação é uma parte da identidade. Para gerir a função de acesso baseado em controle (RBAC) no Azure Stack, tem de configurar o componente de gráfico. Quando o acesso a um recurso é delegado, o componente de gráfico procura a conta de utilizador na floresta do Active Directory existente, utilizando o protocolo LDAP.

![Arquitetura de pilha do AD FS do Azure](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

O AD FS existente é o conta token serviço de segurança (STS) que envia declarações para o Azure Stack AD FS (o recurso STS). No Azure Stack, a automatização cria a confiança do fornecedor de afirmações com o ponto final de metadados para o AD FS existente.

No AD FS existente, tem de ser configurada uma confiança de entidade confiadora. Este passo não é feito com a automação e tem de ser configurado pelo operador. O ponto de final do Azure Stack VIP para o AD FS pode ser criado usando o padrão `https://adfs.<Region>.<ExternalFQDN>/`.

Configuração da entidade confiadora fidedignidade de entidade também exige que configure as regras de transformação de afirmações que são fornecidas pela Microsoft.

Para a configuração de gráfico, uma conta de serviço tem de ser desde que tenha permissão de leitura no Active Directory existente. Esta conta é necessária como entrada para a automação ativar cenários RBAC.

Para a última etapa, um novo proprietário está configurado para a subscrição do fornecedor predefinido. Esta conta tem acesso total a todos os recursos quando tiver sessão iniciada no portal de administrador do Azure Stack.

Requisitos:

|Componente|Requisito|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configurar a integração de gráfico

Gráfico só suporta a integração com uma única floresta do Active Directory. Se existirem várias florestas, apenas a floresta especificada na configuração do que será utilizada para obtenção de utilizadores e grupos.

As seguintes informações são necessárias como entradas para os parâmetros de automatização:

|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomADGlobalCatalog|FQDN do destino de floresta do Active Directory<br>que pretende integrar com o|Contoso.com|
|CustomADAdminCredentials|Um utilizador com permissão de leitura de LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Configurar Sites do Active Directory

Para implementações de Active Directory ter vários sites, configure o Site do Active Directory mais próximo à sua implementação do Azure Stack. A configuração evita ter o serviço do Azure Stack Graph resolver consultas com um servidor de Catálogo Global a partir de um site remoto.

Adicionar o Azure Stack [rede VIP pública](azure-stack-network.md#public-vip-network) sub-rede para o Site do Active Directory mais próxima ao Azure Stack. Por exemplo, se o Active Directory tem dois sites de Seattle e Redmond com o Azure Stack, implementado no site de Seattle, seria adicionar a sub-rede da rede VIP público do Azure Stack para o site do Active Directory de Seattle.

Para obter mais informações sobre locais do Active Directory, consulte [criando a topologia de site](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Se o Active Directory são compostas por um único Site pode ignorar este passo. No caso de ter uma sub-rede de catch-all configurada validar que a sub-rede da rede VIP público do Azure Stack não é parte do mesmo.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Criar conta de utilizador no Active Directory existente (opcional)

Opcionalmente, pode criar uma conta para o serviço de gráficos no Active Directory existente. Efetue este passo se já não tiver uma conta que pretende utilizar.

1. No Active Directory existente, crie a seguinte conta de utilizador (recomendação):
   - **Nome de utilizador**: graphservice
   - **Palavra-passe**: utilizar uma palavra-passe segura<br>Configure a palavra-passe para nunca expirar.

   Não existem permissões especiais ou a associação é necessária.

#### <a name="trigger-automation-to-configure-graph"></a>Acionar a automatização para configurar o gráfico

Para este procedimento, utilize um computador na sua rede de centro de dados que pode se comunicar com o ponto final com privilégios no Azure Stack.

1. Abra uma sessão elevada do Windows PowerShell (executar como administrador) e ligar ao endereço IP do ponto final com privilégios. Utilizar as credenciais para **CloudAdmin** para autenticar.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que está conectado ao ponto final com privilégios, execute o seguinte comando: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Quando lhe for pedido, especifique a credencial da conta de utilizador que pretende utilizar para o serviço de gráfico (por exemplo, graphservice). A entrada para o cmdlet Register-DirectoryService tem de ser o nome de floresta / domínio na floresta, em vez de qualquer outro domínio na floresta de raiz.

   > [!IMPORTANT]
   > Aguarde que as credenciais de pop-up (Get-Credential não é suportado o ponto final com privilégios) e introduza as credenciais da conta de serviço do gráfico.

#### <a name="graph-protocols-and-ports"></a>Gráfico de protocolos e portas

Serviço de gráficos no Azure Stack utiliza as seguintes portas e protocolos para comunicar com um gravável Catálogo Global Server (GC) e a chave de centro de distribuição (KDC) que pode processar pedidos de início de sessão no destino floresta do Active Directory.

Serviço de gráficos no Azure Stack utiliza as seguintes portas e protocolos para comunicar com o destino do Active Directory:

|Type|Porta|Protocolo|
|---------|---------|---------|
|LDAP|389|TCP E UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|SSL LDAP DE GC|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configurar a integração do AD FS ao transferir os metadados de Federação

As seguintes informações são necessárias como entrada para os parâmetros de automatização:

|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do fornecedor de afirmações.<br>Parece dessa forma, na página de aterrissagem do AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Ligação de metadados de Federação| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Acionar a automatização para configurar a confiança do fornecedor de afirmações no Azure Stack

Para este procedimento, utilize um computador que possa comunicar com o ponto final com privilégios no Azure Stack. Espera-se que o certificado utilizado pela conta **STS AD FS** é considerado fidedigno pelo Azure Stack.

1. Abra uma sessão elevada do Windows PowerShell e ligue-se para o ponto final com privilégios.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que está conectado ao ponto final com privilégios, execute o seguinte comando utilizando os parâmetros adequados para o seu ambiente:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Execute o seguinte comando para atualizar o proprietário da subscrição do fornecedor padrão, utilizando os parâmetros adequados para o seu ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configurar a integração do AD FS ao fornecer o ficheiro de metadados de Federação

A partir da versão 1807, utilize este método se a qualquer uma das seguintes condições forem verdadeira:

- A cadeia de certificados é diferente para o AD FS em comparação comparado todos os outros pontos de extremidade no Azure Stack.
- Não é nenhuma conectividade de rede para o servidor do AD FS existente da instância do Azure Stack do AD FS.

As seguintes informações são necessárias como entrada para os parâmetros de automatização:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do fornecedor de afirmações. Parece dessa forma, na página de aterrissagem do AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Conteúdo de metadados|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Criar ficheiro de metadados de Federação

Para o seguinte procedimento, tem de utilizar um computador que tem conectividade de rede para a implementação de AD FS existente, o que torna-se a conta de STS. Além disso, os certificados necessários tem de ser instalados.

1. Abra uma sessão elevada do Windows PowerShell e execute o seguinte comando, utilizando os parâmetros adequados para o seu ambiente:

   ```PowerShell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Copie o ficheiro de metadados para um computador que possa comunicar com o ponto final com privilégios.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Acionar a automatização para configurar a confiança do fornecedor de afirmações no Azure Stack

Para este procedimento, utilize um computador que possa comunicar com o ponto final com privilégios no Azure Stack e tem acesso ao ficheiro de metadados que criou no passo anterior.

1. Abra uma sessão elevada do Windows PowerShell e ligue-se para o ponto final com privilégios.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que está conectado ao ponto final com privilégios, execute o seguinte comando utilizando os parâmetros adequados para o seu ambiente:

    ```PowerShell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Execute o seguinte comando para atualizar o proprietário da subscrição do fornecedor padrão, utilizando os parâmetros adequados para o seu ambiente:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Ao rodar o certificado no AD FS existente (conta STS) tem de configurar a integração do AD FS novamente. Tem de configurar a integração, mesmo que o ponto final de metadados está acessível ou foi configurado, fornecendo o ficheiro de metadados.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurar a entidade confiadora na implementação do AD FS existente (conta STS)

A Microsoft fornece um script que configura a confiança de entidade confiadora, incluindo as regras de transformação de afirmações. Usando o script é opcional, como pode executar os comandos manualmente.

Pode transferir o script do programa auxiliar [ferramentas do Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) no GitHub.

Se optar por executar os comandos manualmente, siga estes passos:

1. Copie o seguinte conteúdo para um ficheiro. txt (por exemplo, guardado como c:\ClaimRules.txt) em membro de instância ou farm de AD FS do seu datacenter:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Validar que a autenticação baseada em formulários do Windows para extranet e a intranet é ativada. Validar primeiro se o seu já ativado executando o seguinte cmdlet:

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > O Windows autenticação integrada (WIA) podem de cadeias de caracteres do agente de utilizador suportados desatualizado para a implementação de FS AD pode exigir a ser atualizado para oferecer suporte a clientes mais recentes. Pode ler mais sobre a atualização o WIA suportados cadeias de caracteres de agente de utilizador no artigo [authentication configurar baseada em formulários da intranet para dispositivos que não suportam WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>Os passos para ativar a política de autenticação baseada em formulários estão documentados no artigo [configurar políticas de autenticação](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Para adicionar a confiança de entidade confiadora, execute o seguinte comando do Windows PowerShell na sua instância do AD FS ou um membro de farm. Certifique-se atualizar o ponto final do AD FS e apontar para o ficheiro criado no passo 1.

   **Para o AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Para o AD FS 2012/2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Tem de utilizar o snap-in do MMC do AD FS para configurar as regras de autorização de emissão ao utilizar o Windows Server 2012 ou 2012 R2 AD FS.

4. Quando utiliza o Internet Explorer ou o browser Microsoft Edge para aceder ao Azure Stack, deve ignorar os enlaces de token. Caso contrário, as tentativas de início de sessão falharem. Na sua instância do AD FS ou um membro de farm, execute o seguinte comando:

   > [!note]  
   > Este passo não é aplicável ao utilizar o Windows Server 2012 ou 2012 R2 AD FS. É seguro ignorar este comando e avançar com a integração.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Criação de SPN

Existem muitos cenários que requerem a utilização de um nome de principal de serviço (SPN) para autenticação. Seguem-se alguns exemplos:

- Utilização da CLI com a implementação do AD FS do Azure Stack
- Pacote de gestão do System Center para o Azure Stack quando implantado com o AD FS
- Fornecedores de recursos no Azure Stack quando implantado com o AD FS
- Vários aplicativos
- Necessitam de um início de sessão não interativo

> [!Important]  
> AD FS suporta apenas as sessões de início de sessão interativo. Se necessitar de um início de sessão não interativo para um cenário de automatizada, tem de utilizar um SPN.

Para obter mais informações sobre como criar um SPN, consulte [criar principal de serviço para o AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).


## <a name="troubleshooting"></a>Resolução de problemas

### <a name="configuration-rollback"></a>Reversão da configuração

Se ocorrer um erro que deixa o ambiente num Estado em que já não pode autenticar, uma opção de reversão está disponível.

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes comandos:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   ```PowerShell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Depois de executar a ação de reversão, todas as alterações de configuração são revertidas. Apenas a autenticação com o incorporado **CloudAdmin** utilizador é possível.

   > [!IMPORTANT]
   > Tem de configurar o proprietário original da subscrição do fornecedor de predefinição

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Recolher registos adicionais

Se qualquer um dos cmdlets falharem, pode recolher registos adicionais ao utilizar o `Get-Azurestacklogs` cmdlet.

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes comandos:

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Passos Seguintes

[Integrar soluções de monitorização externas](azure-stack-integrate-monitor.md)
