---
title: Adicionar o Kubernetes para o mercado do Azure Stack | Documentos da Microsoft
description: Saiba como adicionar o Kubernetes no Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: e11db0cacb14ab94c40ebbf6cac356a08cc016f1
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352687"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Adicionar o Kubernetes para o mercado do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!note]  
> Kubernetes no Azure Stack está em pré-visualização.

Pode oferecer Kubernetes como um item do mercado para os seus utilizadores. Os utilizadores podem, em seguida, implemente o Kubernetes numa operação única e coordenada.

O seguinte artigo examinar o uso de um modelo Azure Resource Manager para implementar e aprovisionar os recursos para um cluster de Kubernetes autónomo. O item do Marketplace de Cluster de Kubernetes 0.3.0 requer a versão do Azure Stack 1808. Antes de começar, verifique o Azure Stack e as definições de inquilino do Azure global. Recolha as informações necessárias sobre o Azure Stack. Adicione recursos necessários para o seu inquilino e a pilha do Azure Marketplace. O cluster depende de um Ubuntu server, o script personalizado e os itens do Kubernetes para ser no marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Criar um plano, uma oferta e uma subscrição

Crie um plano, uma oferta e uma subscrição para o item do Marketplace de Kubernetes. Também pode utilizar um plano existente e oferecem.

1. Entrar para a [portal de administração.](https://adminportal.local.azurestack.external)

1. Crie um plano de como o plano de base. Para obter instruções, consulte [criar um plano no Azure Stack](azure-stack-create-plan.md).

1. Crie uma oferta. Para obter instruções, consulte [criar uma oferta no Azure Stack](azure-stack-create-offer.md).

1. Selecione **oferece**e localize a oferta que criou.

1. Selecione **descrição geral** no painel da oferta.

1. Selecione **alterar estado**. Selecione **público**.

1. Selecione **+ criar um recurso** > **ofertas e planos** > **subscrição** para criar uma subscrição.

    a. Introduza um **nome a apresentar**.

    b. Introduza um **utilizador**. Utilize a conta do Azure AD associada ao seu inquilino.

    c. **Descrição do fornecedor**

    d. Definir o **inquilino Directory** para o inquilino do Azure AD para o Azure Stack. 

    e. Selecione **oferecem**. Selecione o nome da oferta que criou. Tome nota do ID de subscrição.

## <a name="create-a-service-principle-and-credentials-in-ad-fs"></a>Criar um principal de serviço e as credenciais no AD FS

Se usar o Active Directory Federated Services (AD FS) para o seu serviço de gestão de identidade, terá de criar um principal de serviço para os utilizadores a implementar um cluster de Kubernetes.

1. Criar e exportar um certificado para ser utilizado para criar o principal de serviço. O seguinte trecho de código abaixo mostra como criar um certificado autoassinado. 

    - Terá das seguintes partes de informações:

       | Valor | Descrição |
       | ---   | ---         |
       | Palavra-passe | A palavra-passe do certificado. |
       | Caminho de certificados local | O nome de ficheiro e caminho do certificado. Por exemplo: `path\certfilename.pfx` |
       | Nome do certificado | O nome do certificado. |
       | Localização do arquivo de certificados |  Por exemplo, `Cert:\LocalMachine\My` |

    - Abra o PowerShell com uma linha de comandos elevada. Execute o seguinte script com os parâmetros atualizados para seus valores:

        ```PowerShell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "<certificate name>"
        #certificate store location. Eg. Cert:\LocalMachine\My
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2. Crie principal de serviço com o certificado.

    - Terá das seguintes partes de informações:

       | Valor | Descrição                     |
       | ---   | ---                             |
       | ERCS IP | No ASDK, o ponto final com privilégios é normalmente `AzS-ERCS01`. |
       | Nome da aplicação | Um nome simple para o principal de serviço de aplicações. |
       | Localização do arquivo de certificados | O caminho no computador onde armazenou o certificado. Por exemplo: `Cert:\LocalMachine\My\<someuid>` |

    - Abra o PowerShell com uma linha de comandos elevada. Execute o seguinte script com os parâmetros atualizados para seus valores:

        ```PowerShell  
        #Create service principle using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        #certificate store location. Eg. Cert:\LocalMachine\My
        $certStoreLocation="<certificate store location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service Principle Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

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
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - Os detalhes do princípio de serviço, veja como o fragmento abaixo

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Adicionar uma imagem do Ubuntu server

Adicione a imagem de Ubuntu Server seguinte no Marketplace:

1. Inicie sessão para o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços**e, em seguida, sob o **administração** categoria, selecione **gestão Marketplace**.

1. Selecione **+ adicionar a partir do Azure**.

1. Introduza `UbuntuServer`.

1. Selecione a versão mais recente do servidor. Verifique a versão completa e certifique-se de que tem a versão mais recente:
    - **Publisher**: Canónico
    - **Oferecer**: UbuntuServer
    - **Versão**: 16.04.201806120 (ou versão mais recente)
    - **SKU**: 16.04-LTS

1. Selecione **transferir.**

## <a name="add-a-custom-script-for-linux"></a>Adicionar um script personalizado para Linux

Adicione do Kubernetes a partir do Marketplace:

1. Abra o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços** e, em seguida, sob o **administração** categoria, selecione **Marketplace gestão**.

1. Selecione **+ adicionar a partir do Azure**.

1. Introduza `Custom Script for Linux`.

1. Selecione o script com o seguinte perfil:
    - **Oferecer**: Script personalizado para Linux 2.0
    - **Versão**: 2.0.6 (ou versão mais recente)
    - **Publisher**: Microsoft Corp

    > [!Note]  
    > Mais de uma versão de Script personalizado para Linux pode estar relacionada. Terá de adicionar a última versão do item.

1. Selecione **transferir.**


## <a name="add-kubernetes-to-the-marketplace"></a>Adicionar o Kubernetes no Marketplace

1. Abra o [portal de administração](https://adminportal.local.azurestack.external).

1. Selecione **todos os serviços** e, em seguida, sob o **administração** categoria, selecione **Marketplace gestão**.

1. Selecione **+ adicionar a partir do Azure**.

1. Introduza `Kubernetes`.

1. Selecione `Kubernetes Cluster`.

1. Selecione **transferir.**

    > [!note]  
    > Poderá demorar cinco minutos para o item do marketplace a aparecer no Marketplace.

    ![Utilizar o Kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Atualizar ou remover o Kubernetes 

Ao atualizar o item de Kubernetes, irá remover o item anterior no Marketplace. Siga as instruções neste artigo para adicionar que os Kubernetes atualizar no Marketplace.

Para remover o item de Kubernetes:

1. Ligar ao Azure Stack com o PowerShell como um operador. Para obter instruções, consulte [ligue-se ao Azure Stack com o PowerShell como um operador](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Localize o item atual do Cluster de Kubernetes na galeria.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Anote o nome do item atual, por exemplo `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Utilize o seguinte cmdlet do PowerShell para remover o item:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Passos Seguintes

[Implementar o Kubernetes para o Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Descrição geral da oferta de serviços no Azure Stack](azure-stack-offer-services-overview.md)
