---
title: Implementar o Kubernetes no Azure Stack com o Active Directory Federated Services (AD FS) | Documentos da Microsoft
description: Saiba como implementar o Kubernetes no Azure Stack com o Active Directory Federated Services (AD FS).
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
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: f054cf101f24d7cc571e9f90943122e42beb9dc6
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983494"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Implementar o Kubernetes no Azure Stack com o Active Directory Federated Services

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em pré-visualização. Cenário de desligado de pilha do Azure não é atualmente suportado pela pré-visualização.

Pode seguir os passos neste artigo para implementar e configurar os recursos do Kubernetes. Utilize estes passos ao Active Directory Federated Services (AD FS) é o seu serviço de gestão de identidade.

## <a name="prerequisites"></a>Pré-requisitos 

Para começar, certifique-se de que tem as permissões corretas e que o Azure Stack está pronto.

1. Gere um público e privado par de chaves SSH para iniciar sessão na VM do Linux no Azure Stack. Terá da chave pública ao criar o cluster.

    Para obter instruções sobre como gerar uma chave, consulte [geração de chave SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Verifique se tem uma subscrição válida no seu portal de inquilino do Azure Stack, e que tem suficiente IP público de endereços disponíveis para adicionar novos aplicativos.

    O cluster não é possível implementar um Azure Stack **administrador** subscrição. Tem de utilizar um **utilizador** subscrição. 

1. Terá do serviço do Cofre de chaves na sua subscrição do Azure Stack.

1. Terá do Cluster de Kubernetes no seu mercado. 

Se perder o serviço do Cofre de chaves e item do mercado de Cluster do Kubernetes, fale com seu administrador do Azure Stack.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Terá de contactar o administrador do Azure Stack para configurar o seu principal de serviço ao utilizar o AD FS como a sua solução de identidade. O principal de serviço fornece o acesso de aplicação aos recursos do Azure Stack.

1. O administrador do Azure Stack fornece um certificado e as informações para o principal de serviço.

    - As informações do principal de serviço devem ser semelhante:

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

    - O certificado será um ficheiro com a extensão `.pfx`. Irá armazenar o certificado num cofre de chaves, como um segredo.

2. Atribua uma função como um contribuinte de seu novo principal de serviço à sua subscrição. Para obter instruções, consulte [atribuir uma função](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3. Crie um cofre de chaves para armazenar o certificado para a implementação. Utilize os seguintes scripts do PowerShell, em vez do Portal.

    - Terá das seguintes partes de informações:

        | Value | Descrição |
        | ---   | ---         |
        | Ponto final do Gestor de recursos do Azure | O Gestor de recursos do Microsoft Azure é uma estrutura de gestão que permite aos administradores implementar, gerir e monitorizar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, numa única operação.<br>O ponto final no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/`<br>O ponto de extremidade em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | O ID de subscrição | O [ID de subscrição](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como acessa ofertas no Azure Stack. |
        | O nome de utilizador | Utilizar apenas o nome de utilizador em vez de seu nome de domínio e nome de utilizador, como `username` em vez de `azurestack\username`. |
        | O nome do grupo de recursos  | O nome de um novo grupo de recursos ou selecione um grupo de recursos existente. O nome do recurso tem de ser de alfanuméricos e minúsculas. |
        | Nome do Cofre de chaves | Nome do cofre.<br> Padrão RegEx: `^[a-zA-Z0-9-]{3,24}$` |
        | Localização do grupo de recursos | A localização do grupo de recursos. Esta é a região que escolher para a sua instalação do Azure Stack. |

    - Abra o PowerShell com uma linha de comandos elevada, e [ligar ao Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Execute o seguinte script com os parâmetros atualizados para seus valores:

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. Carregue o certificado para o Cofre de chaves.

    - Terá das seguintes partes de informações:

        | Value | Descrição |
        | ---   | ---         |
        | Caminho do certificado | O caminho de ficheiro ou o FQDN para o certificado. |
        | Palavra-passe de certificado | A palavra-passe do certificado. |
        | Nome do segredo | O nome secreto utilizado para referenciar o certificado armazenado no cofre. |
        | Nome do Cofre de chaves | O nome do Cofre de chaves que criou no passo anterior. |
        | Ponto final do Gestor de recursos do Azure | O ponto final no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/`<br>O ponto de extremidade em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | O ID de subscrição | O [ID de subscrição](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como acessa ofertas no Azure Stack. |

    - Abra o PowerShell com uma linha de comandos elevada, e [ligar ao Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Execute o seguinte script com os parâmetros atualizados para seus valores:

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<key vault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>Implementar o Kubernetes

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).

1. Selecione **+ criar um recurso** > **computação** > **Cluster do Kubernetes**. Clique em **Criar**.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Noções básicas

1. Selecione **Noções básicas** criar Cluster do Kubernetes.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Selecione seu **subscrição** ID.

1. Introduza o nome de um novo grupo de recursos ou selecione um grupo de recursos existente. O nome do recurso tem de ser de alfanuméricos e minúsculas.

1. Selecione o **localização** do grupo de recursos. Esta é a região que escolher para a sua instalação do Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Definições do Cluster de Kubernetes

1. Selecione **definições do Cluster de Kubernetes** criar Cluster do Kubernetes.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Introduza o **nome de utilizador de administrador VM de Linux**. Nome de utilizador para as máquinas de virtuais de Linux que fazem parte do cluster de Kubernetes e DVM.

1. Introduza o **chave pública SSH** utilizado na autorização de todas as máquinas de Linux criados como parte do cluster de Kubernetes e DVM.

1. Introduza o **prefixo de DNS de perfil do mestre** que seja exclusivo para a região. Tem de ser um nome exclusivo para a região, tal como `k8s-12345`. Tente optou por ele mesmo que o grupo de recursos dê um nome como melhor prática.

    > [!Note]  
    > Para cada cluster, utilize um prefixo DNS de perfil de mestre de novos e únicos.

1. Selecione o **contagem de perfil do Kubernetes principal conjunto**. A contagem de contém o número de nós no conjunto mestre. Aí pode ter entre 1 e 7. Este valor deve ser um número ímpar.

1. Selecione **o VMSize das VMs mestres Kubernetes**.

1. Selecione o **contagem de perfil do conjunto de nós de Kubernetes**. A contagem de contém o número de agentes no cluster. 

1. Selecione o **perfil de armazenamento**. Pode escolher **Blob de disco** ou **disco gerido**. Esta ação Especifica as VMs de nó de tamanho de VM de Kubernetes. 

1. Selecione **ADFS** para o **sistema de identidade do Azure Stack** para a sua instalação do Azure Stack.

1. Introduza o **ID de cliente do Principal de serviço** é utilizada pelo fornecedor de cloud do Azure do Kubernetes. O ID de cliente identificado como o ID da aplicação quando o administrador do Azure Stack criou o principal de serviço.

1. Introduza o **grupo de recursos do Cofre de chaves** origens que o Cofre de chaves que contém o certificado.

1. Introduza o **nome do Key Vault** o nome do Cofre de chaves que contém o certificado como um segredo. 

1. Introduza o **segredo do Key Vault**. O nome secreto faz referência a seu certificado.

1. Introduza o **versão de fornecedor de Cloud do Azure do Kubernetes**. Esta é a versão para o fornecedor do Azure do Kubernetes. O Azure Stack lança uma compilação do Kubernetes personalizada para cada versão do Azure Stack.

### <a name="3-summary"></a>3. Resumo

1. Selecione o resumo. O painel apresenta uma mensagem de validação para as definições de configurações de Cluster de Kubernetes.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Reveja as definições.

3. Selecione **OK** para implementar o seu cluster.

> [!TIP]  
>  Se tiver dúvidas sobre a implementação, pode publicar a sua pergunta ou ver se alguém tem já respondeu à pergunta na [fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Passos Seguintes

[Ligar ao cluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Ativar o Dashboard do Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)