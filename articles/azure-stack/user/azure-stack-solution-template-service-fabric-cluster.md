---
title: Implementar um cluster do Service Fabric seguro no Azure Stack | Documentos da Microsoft
description: Saiba como implementar um cluster do Service Fabric seguro no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: d0d725a57c27fe30215d77a596f6fb3b8c8720d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098013"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Implementar um cluster do Service Fabric no Azure Stack

Utilize o **Cluster do Service Fabric** item no Azure Marketplace para implementar um cluster do Service Fabric seguro no Azure Stack. 

Para obter mais informações sobre como trabalhar com o Service Fabric, veja [descrição geral do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) e [cenários de segurança de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), na documentação do Azure.

O cluster do Service Fabric no Azure Stack não está a utilizar o fornecedor de recursos Microsoft.ServiceFabric. Em vez disso, no Azure Stack, o cluster do Service Fabric é um conjunto de dimensionamento com o conjunto de software pré-instalados com a Desired State Configuration (DSC).

## <a name="prerequisites"></a>Pré-requisitos

O seguinte é necessário para implementar o cluster do Service Fabric:
1. **Certificado de cluster**  
   Este é o certificado de servidor X.509 que adiciona ao Cofre de chaves ao implementar o Service Fabric. 
   - O **CN** sobre este certificado deve coincidir com o nome de domínio completamente qualificado (FQDN) do cluster do Service Fabric é criar. 
   - O formato de certificado tem de estar PFX, que as chaves públicas e privadas são necessárias. 
     Ver [requisitos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para criar este certificado de servidor.

     > [!NOTE]  
     > Pode usar inplace um certificado autoassinado do certificado de servidor x.509 para fins de teste. Certificados autoassinados não é necessário fazer corresponder o FQDN do cluster.

1. **Certificado de cliente administrativo** este é o certificado que o cliente irá utilizar para autenticar para o cluster do Service Fabric, o que pode ser autoassinado. Ver [requisitos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para criar este certificado de cliente.

1. **Os itens seguintes tem de estar disponíveis no mercado de pilha do Azure:**
    - **Windows Server 2016** – o modelo utiliza a imagem do Windows Server 2016 para criar o cluster.  
    - **Extensão de Script de cliente** -extensão da Máquina Virtual da Microsoft.  
    - **Fase de configuração pretendido do PowerShell** -extensão da Máquina Virtual da Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault
Para implementar um cluster do Service Fabric, tem de especificar o KeyVault correto *identificador de segredo* ou o URL para o cluster do Service Fabric. O modelo Azure Resource Manager usa um cofre de chaves, como entrada. Em seguida, o modelo obtém o certificado de Cluster, ao instalar o cluster do Service Fabric.

> [!IMPORTANT]  
> Tem de utilizar o PowerShell para adicionar um segredo ao Cofre de chaves para uso com o Service Fabric. Não utilize o portal.  

Utilize o seguinte script para criar o Cofre de chaves e adicionar as *certificado de cluster* a ele. (Consulte a [pré-requisitos](#prerequisites).) Antes de executar o script, reveja o script de exemplo e atualize os parâmetros indicados para corresponder ao seu ambiente. Este script também irá transmitir os valores que precisa de fornecer para o modelo Azure Resource Manager. 

> [!TIP]  
> Antes do script pode ter êxito, deve haver uma oferta pública, que inclui os serviços de computação, rede, armazenamento e o Cofre de chaves. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Para obter mais informações, consulte [gerir o Cofre de chaves no Azure Stack com o PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-key-vault-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Implementar o item do Marketplace

1. No portal de utilizador, aceda a **+ criar um recurso** > **computação** > **Cluster do Service Fabric**. 

   ![Selecione o Cluster do Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. Para cada página, como *Noções básicas*, preencha o formulário de implementação. Se não tiver a certeza de um valor, utilize as predefinições. Selecione **OK** para avançar para a página seguinte:

   ![Noções básicas](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. Sobre o *definições de rede* página, pode especificar portas específicas para abrir para as suas aplicações:

   ![Definições de Rede](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. Sobre o *segurança* página, adicione os valores que obteve da [criar o Cofre de chaves do Azure](#add-a-secret-to-key-vault) e carregar o segredo.

   Para o *Thumbprint do certificado de cliente de administrador*, introduza o thumbprint do *certificado de cliente administrativo*. (Consulte a [pré-requisitos](#prerequisites).)
   
   - Cofre de chaves de origem:  Especificar toda *id de Cofre de chaves* cadeia de caracteres dos resultados do script. 
   - URL de certificado de cluster: Especifique o URL completo do *Id de segredo* dos resultados do script. 
   - Thumbprint do certificado de cluster: Especifique a *Thumbprint do certificado de Cluster* dos resultados do script.
   - Thumbprints de certificado de cliente administrativo: Especifique a *Thumbprint do certificado de cliente de administrador* criados nos pré-requisitos. 

   ![Saída de scripts](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Segurança](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Conclua o assistente e, em seguida, selecione **criar** para implementar o Cluster do Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Aceder ao Cluster do Service Fabric
Pode acessar o cluster do Service Fabric com o Service Fabric Explorer ou o PowerShell do Service Fabric.


### <a name="use-service-fabric-explorer"></a>Utilizar o Service Fabric Explorer
1.  Confirme que o navegador da Web tem acesso ao seu certificado de cliente administrativo e possa ser autenticado no cluster do Service Fabric.  

    a. Abra o Internet Explorer e aceda a **opções da Internet** > **conteúdo** > **certificados**.
  
    b. Relativas a certificados, selecione **importação** para iniciar o *Assistente de importação de certificado*e, em seguida, clique em **seguinte**. Na *ficheiro a importar* página clique **procurar**e selecione o **certificado de cliente administrativo** que indicou para o modelo Azure Resource Manager.
        
       > [!NOTE]  
       > Este certificado não é o certificado de Cluster que foi adicionado anteriormente para o Cofre de chaves.  

    c. Certifique-se de que tem "Personal Information Exchange" selecionado na lista pendente de extensão da janela do Explorador de ficheiros.  

       ![Troca de informações pessoais](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Sobre o *Store de certificado* página, selecione **pessoais**e, em seguida, conclua o assistente.  
       ![Arquivo de certificados](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Para localizar o FQDN do cluster do Service Fabric:  

    a. Vá para o grupo de recursos que estão associado com o Service Fabric cluster e localize a *endereço IP público* recursos. Selecione o objeto associado com o endereço IP público para abrir o *endereço IP público* painel.  

      ![Endereço IP público](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. No painel de endereço IP público, o FQDN indicará *nome DNS*.  

      ![Nome DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Para localizar o URL para o Service Fabric Explorer e o ponto final de ligação de cliente, reveja os resultados da implementação do modelo.

1. No seu browser, aceda a https://*FQDN*: 19080. Substitua *FQDN* com o FQDN do cluster do Service Fabric do passo 2.   
   Se já usou um certificado autoassinado, obterá um aviso de que a ligação não é segura. Para continuar para o web site, selecione **mais informações**e, em seguida **avance para a página Web**. 

1. Para autenticar para o site, tem de selecionar um certificado a utilizar. Selecione **mais escolhas**, escolha o certificado apropriado e, em seguida, clique em **OK** para ligar ao Service Fabric Explorer. 

   ![Autenticar](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Utilizar o PowerShell do Service Fabric

1. Instalar o *SDK do Microsoft Azure Service Fabric* partir [preparar o ambiente de desenvolvimento no Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) na documentação do Azure Service Fabric.  

1. Depois de concluída a instalação, configure as variáveis de ambiente de sistema para garantir que os cmdlets do Service Fabric estão acessíveis a partir do PowerShell.  
    
    a. Aceda a **painel de controlo** > **sistema e segurança** > **sistema**e, em seguida, selecione **configurações avançadas do sistema**.  
    
      ![Painel de controlo](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Sobre o **avançadas** separador de *propriedades do sistema*, selecione **variáveis de ambiente**.  

    c. Para *variáveis do sistema*, edite **caminho** e certifique-se de que **c:\\arquivos de programas\\Microsoft Service Fabric\\bin\\recursos de infraestrutura \\Fabric.Code** na parte superior da lista de variáveis de ambiente.  

      ![Lista de variáveis de ambiente](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Depois de alterar a ordem das variáveis de ambiente, reinicie o PowerShell e, em seguida, execute o seguinte script de PowerShell para obter acesso ao cluster do Service Fabric:

   ```PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > Não existe nenhuma *https://* antes do nome do cluster no script. É necessária a porta 19000.

## <a name="next-steps"></a>Passos Seguintes

[Implementar o Kubernetes para o Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
