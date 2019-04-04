---
title: Gerir um Principal de serviço para o Azure Stack | Documentos da Microsoft
description: Descreve como gerir um novo principal de serviço que pode ser utilizado com o controlo de acesso baseado em funções no Azure Resource Manager para gerir o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 0f5a4dc76830740d69547a01ce40b5e10cf4a74b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499413"
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornecimento de acesso de aplicações ao Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Quando uma aplicação precisa de aceder para implementar ou configurar recursos através do Azure Resource Manager no Azure Stack, criar um serviço principal, que é uma credencial para a sua aplicação. Em seguida, pode delegar as permissões necessárias para esse principal de serviço.  

Por exemplo, pode ter uma ferramenta de gerenciamento de configuração que utiliza o Azure Resource Manager para Inventariar os recursos do Azure. Neste cenário, pode criar um principal de serviço, conceder a função de leitor a esse principal de serviço e limitar a ferramenta de gestão de configuração para acesso só de leitura. 

Principais de serviço são preferíveis a executar o aplicativo em suas próprias credenciais porque:

 - Pode atribuir permissões ao principal de que são diferentes de suas próprias permissões de conta de serviço. Normalmente, estas permissões estão restritas a exatamente aquilo que a aplicação precisa de fazer.
 - Não é necessário que alterar as credenciais da aplicação se alterar as suas responsabilidades.
 - Pode utilizar um certificado para automatizar a autenticação ao executar um script automático.  

## <a name="getting-started"></a>Introdução

Dependendo de como tiver implementado o Azure Stack, comece por criar um serviço principal. Este documento descreve a criação de um serviço principal para:

- Azure Active Directory (Azure AD). O Azure AD é um diretório de multi-inquilino, com base na cloud e o serviço de gestão de identidade. Pode utilizar o Azure AD com um ligado do Azure Stack.
- Serviços de Federação do Active Directory (AD FS). O AD FS proporciona Federação de identidade simplificada segura e capacidades de início de sessão único (SSO) Web. Pode utilizar o AD FS com instâncias do Azure Stack conectadas ou desconectadas.

Depois de criar o principal de serviço, um conjunto de passos comuns para AD FS e o Azure Active Directory são usados para delegar permissões para a função.

## <a name="manage-service-principal-for-azure-ad"></a>Gerir o principal de serviço para o Azure AD

Se tiver implementado o Azure Stack com o Azure Active Directory (Azure AD) que o seu serviço de gestão de identidade, pode criar principais de serviço, tal como fazer para o Azure. Esta secção mostra como efetuar os passos no portal. Verifique se tem o [do Azure AD permissões obrigatórias](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) antes de começar.

### <a name="create-service-principal"></a>Criar um principal de serviço

Nesta secção, vai criar uma aplicação (principal de serviço) no Azure AD que representa a sua aplicação.

1. Inicie sessão na sua conta do Azure através da [portal do Azure](https://portal.azure.com).
2. Selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo de aplicação**
3. Indique um nome e um URL para a aplicação. Selecione **aplicação Web / API** ou **nativo** para o tipo de aplicação que pretende criar. Depois de definir os valores, selecione **criar**.

Criou um principal de serviço para a sua aplicação.

### <a name="get-credentials"></a>Obter credenciais

Quando iniciar sessão programaticamente, utilize o ID para a sua aplicação e para uma aplicação Web / API, uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. Partir **registos das aplicações** no Active Directory, selecione a aplicação.

2. Copie o **ID da Aplicação** e armazene-o no código da aplicação. As aplicações na secção de aplicações de exemplo referência a este valor como o ID de cliente.

     ![ID de cliente](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação para uma aplicação Web / API, selecione **configurações** > **chaves**. 

4. Indique uma descrição e uma duração para a chave. Quando terminar, selecione **Guardar**.

Depois de guardar a chave, o valor da mesma é apresentado. Copie este valor para o bloco de notas ou noutra localização temporária, porque não é possível obter a chave mais tarde. Forneça o valor da chave com o ID da aplicação para iniciar sessão como o aplicativo. Store o valor da chave num local em que o seu aplicativo pode recuperá-la.

![chave guardada](./media/azure-stack-create-service-principal/image15.png)

Depois de concluído, pode atribuir a aplicação uma função.

## <a name="manage-service-principal-for-ad-fs"></a>Gerir o principal de serviço para o AD FS

Se tiver implementado o Azure Stack com serviços de Federação do Active Directory (AD FS) que o seu serviço de gestão de identidade, utilize o PowerShell para criar um principal de serviço, atribuir uma função de acesso e inicie sessão com essa identidade.

Pode utilizar um dos dois métodos para criar o seu serviço principal com o AD FS. Pode:
 - [Criar um principal de serviço a utilizar um certificado](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Criar um principal de serviço com um segredo do cliente](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Principais de serviço de tarefas para gerir o AD FS.

| Type | Ação |
| --- | --- |
| Certificado do AD FS | [Criar](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certificado do AD FS | [Atualização](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Certificado do AD FS | [remover](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Segredo do AD FS cliente | [Criar](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Segredo do AD FS cliente | [Atualização](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Segredo do AD FS cliente | [remover](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Criar um principal de serviço a utilizar um certificado

Ao criar um principal de serviço ao utilizar o AD FS para a identidade, pode utilizar um certificado.

#### <a name="certificate"></a>Certificado

É necessário um certificado.

**Requisitos de certificado**

 - O fornecedor de serviços criptográficos (CSP) tem de ser fornecedor de chave de legado.
 - O formato de certificado tem de estar no ficheiro PFX, que as chaves públicas e privadas são necessárias. Servidores do Windows utilizam ficheiros PFX, que contêm o ficheiro de chave pública (ficheiro de certificado SSL) e o ficheiro de chave privada associado.
 - Para a produção, tem de ser emitido o certificado de uma autoridade de certificação interna ou uma autoridade de certificação pública. Se utilizar uma autoridade de certificação pública, tem incluído a autoridade na imagem do sistema operacional base como parte do programa autoridade de raiz fidedigna Microsoft. Pode encontrar a lista completa em [Microsoft Trusted Root Certificate Program: Os participantes](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - A infraestrutura do Azure Stack tem de ter acesso à rede para a localização de lista de revogação de certificados (CRL) da autoridade de certificação publicada no certificado. Esta CRL tem de ser um ponto final HTTP.

#### <a name="parameters"></a>Parâmetros

As seguintes informações são necessárias como entrada para os parâmetros de automatização:

|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|Name|Nome da conta SPN|MyAPP|
|ClientCertificates|Matriz de objetos de certificado|X509 certificado|
|ClientRedirectUris<br>(Opcional)|URI de redirecionamento da aplicação|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Utilizar o PowerShell para criar um principal de serviço

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes cmdlets:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Para fins de validação de um certificado autoassinado pode ser criado usando o exemplo abaixo:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Depois de concluída a automação, ele exibe os detalhes necessários para utilizar o SPN. Recomenda-se para armazenar a saída para utilização posterior.

   Por exemplo:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Atualizar o certificado para o principal de serviço para o AD FS

Se tiver implementado o Azure Stack com o AD FS, pode utilizar o PowerShell para atualizar o segredo para um principal de serviço.

O script é executado a partir do ponto final com privilégios numa máquina virtual ERCS.

#### <a name="parameters"></a>Parâmetros

As seguintes informações são necessárias como entrada para os parâmetros de automatização:

|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|Name|Nome da conta SPN|MyAPP|
|ApplicationIdentifier|Identificador exclusivo|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Matriz de objetos de certificado|X509 certificado|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Exemplo de atualização principal de serviço para o AD FS

O exemplo cria um certificado autoassinado. Ao executar os cmdlets numa implementação de produção, utilize [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) para recuperar o objeto de certificado para o certificado que pretende utilizar.

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes cmdlets:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Depois de concluída a automação, ele exibe o valor do thumbprint atualizado necessário para a autenticação SPN.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Criar um principal de serviço com um segredo do cliente

Ao criar um principal de serviço ao utilizar o AD FS para a identidade, pode utilizar um certificado. Irá utilizar o ponto final com privilégios para executar os cmdlets.

Estes scripts são executados a partir do ponto final com privilégios numa máquina virtual ERCS. Para obter mais informações sobre o ponto final com privilégios, consulte [utilizando o ponto final com privilégios no Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

#### <a name="parameters"></a>Parâmetros

As seguintes informações são necessárias como entrada para os parâmetros de automatização:

| Parâmetro | Descrição | Exemplo |
|----------------------|--------------------------|---------|
| Name | Nome da conta SPN | MyAPP |
| GenerateClientSecret | Criar segredo |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Utilize o PrivilegedEndpoint ERCS para criar o principal de serviço

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes cmdlets:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Depois de executar cmdlets, o shell apresenta os detalhes necessários para utilizar o SPN. Certifique-se de que armazenar o segredo do cliente.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Atualizar o segredo do cliente para um principal de serviço para o AD FS

Um novo segredo de cliente é automaticamente gerado pelo cmdlet do PowerShell.

O script é executado a partir do ponto final com privilégios numa máquina virtual ERCS.

##### <a name="parameters"></a>Parâmetros

As seguintes informações são necessárias como entrada para os parâmetros de automatização:

| Parâmetro | Descrição | Exemplo |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Identificador exclusivo. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Altera o segredo do cliente com um período de rollover de 2880 minutos em que o segredo antigo ainda é válido. |  |
| ResetClientSecret | Alterar o segredo do cliente imediatamente |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Exemplo de atualizar um segredo do cliente para o AD FS

O exemplo utiliza a **ResetClientSecret** parâmetro, que muda imediatamente o segredo do cliente.

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes cmdlets:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Depois de concluída a automação, ele exibe o segredo recém-gerado necessário para a autenticação SPN. Certifique-se de que armazenar o novo segredo do cliente.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Remover um principal de serviço para o AD FS

Se tiver implementado o Azure Stack com o AD FS, pode utilizar o PowerShell para eliminar um principal de serviço.

O script é executado a partir do ponto final com privilégios numa máquina virtual ERCS.

#### <a name="parameters"></a>Parâmetros

As seguintes informações são necessárias como entrada para os parâmetros de automatização:

|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
| Parâmetro | Descrição | Exemplo |
| ApplicationIdentifier | Identificador exclusivo | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Para ver uma lista de todos os principais de serviço existente e seu identificador de aplicação, pode ser utilizado o comando get-graphapplication.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Exemplo de como remover o principal de serviço para o AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Atribuir uma função

Para acessar recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida que função representa as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte o artigo [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md).

Pode definir o âmbito no nível da subscrição, no grupo de recursos ou ao recurso. As permissões são herdadas para níveis inferiores de âmbito. Por exemplo, adicionar uma aplicação à função do leitor para um grupo de recursos significa pode ler o grupo de recursos e todos os recursos que nele contidos.

1. No portal do Azure Stack, navegue para o nível de âmbito que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito da subscrição, selecione **subscrições**. Em vez disso, pode selecionar um grupo de recursos ou recurso.

2. Selecione a subscrição específica (grupo de recursos ou recurso) para atribuir a aplicação.

     ![Selecione a subscrição para atribuição](./media/azure-stack-create-service-principal/image16.png)

3. Selecione **controlo de acesso (IAM)**.

     ![Selecione o acesso](./media/azure-stack-create-service-principal/image17.png)

4. Selecione **adicionar atribuição de função**.

5. Selecione a função que pretende atribuir à aplicação.

6. Procure a sua aplicação e selecioná-lo.

7. Selecione **OK** para concluir a atribuir a função. Pode ver a sua aplicação na lista de utilizadores atribuídos a uma função para esse âmbito.

Agora que já criou um principal de serviço e uma função atribuída, poderá começar a usar isso na sua aplicação para aceder aos recursos do Azure Stack.  

## <a name="next-steps"></a>Passos Seguintes

[Adicionar utilizadores ao AD FS](azure-stack-add-users-adfs.md)  
[Gerir permissões de utilizador](azure-stack-manage-permissions.md)  
[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
