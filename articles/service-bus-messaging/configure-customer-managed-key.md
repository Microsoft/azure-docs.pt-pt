---
title: Configure a sua própria chave para encriptar os dados do Azure Service Bus em repouso
description: Este artigo fornece informações sobre como configurar a sua própria chave para encriptar o repouso de dados do Azure Service Bus.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 35680819350582062dd4227c65f9e72ae8b3ee5c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489705"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente para encriptar os dados do Azure Service Bus em repouso utilizando o portal Azure
A Azure Service Bus Premium fornece encriptação de dados em repouso com a Encriptação do Serviço de Armazenamento Azure (Azure SSE). O Service Bus Premium conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados que são armazenados com o Azure Storage são encriptados utilizando as teclas geridas pela Microsoft. 

## <a name="overview"></a>Descrição geral
A Azure Service Bus suporta agora a opção de encriptar dados em repouso com as teclas geridas pela Microsoft ou com teclas geridas pelo cliente (Bring Your Own Key - BYOK). esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às chaves geridas pelo cliente que são usadas para encriptar o Azure Service Bus em repouso.

Ativar a função BYOK é um processo de configuração de uma única vez no seu espaço de nome.

> [!NOTE]
> Existem algumas ressalvas para a chave gerida pelo cliente para encriptação do lado do serviço. 
>   * Esta funcionalidade é suportada pelo nível [Azure Service Bus Premium.](service-bus-premium-messaging.md) Não pode ser ativado para espaços de nome de ônibus de nível padrão.
>   * A encriptação só pode ser ativada para espaços novos ou vazios. Se o espaço de nomes contiver quaisquer filas ou tópicos, então a operação de encriptação falhará.

Pode utilizar o Azure Key Vault para gerir as suas chaves e auditar o uso da chave. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../key-vault/general/overview.md)

Este artigo mostra como configurar um cofre-chave com chaves geridas pelo cliente utilizando o portal Azure. Para aprender a criar um cofre-chave utilizando o portal Azure, consulte [Quickstart: Crie um Cofre de Chaves Azure utilizando o portal Azure](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> A utilização de chaves geridas pelo cliente com o Azure Service Bus requer que o cofre-chave tenha duas propriedades necessárias configuradas. São:  **Soft Delete** and **Do Not Purpur**. Estas propriedades são ativadas por padrão quando cria um novo cofre chave no portal Azure. No entanto, se necessitar de ativar estas propriedades num cofre de chaves existente, deve utilizar o PowerShell ou o Azure CLI.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente
Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu espaço de nomes Service Bus Premium.
2. Na página Definições do seu espaço de **nomes** de Service Bus, selecione **Encriptação**.
3. Selecione a **encriptação da chave gerida pelo Cliente em repouso,** como mostrado na imagem seguinte.

    ![Ativar chave gerida pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre com chaves

Depois de ativar as chaves geridas pelo cliente, tem de associar a chave gerida pelo cliente ao seu espaço de nomes Azure Service Bus. O Service Bus suporta apenas o Cofre da Chave Azure. Se ativar a **Encriptação com** a opção chave gerida pelo cliente na secção anterior, precisa de ter a chave importada para o Cofre da Chave Azure. Além disso, as teclas devem ter **Soft Delete** e **Não Purgar** configuradas para a chave. Estas definições podem ser configuradas utilizando [PowerShell](../key-vault/general/key-vault-recovery.md) ou [CLI](../key-vault/general/key-vault-recovery.md).

1. Para criar um novo cofre de chaves, siga o Azure Key Vault [Quickstart](../key-vault/general/overview.md). Para obter mais informações sobre a importação de chaves existentes, consulte [sobre chaves, segredos e certificados.](../key-vault/general/about-keys-secrets-certificates.md)
1. Para ligar a proteção de eliminação e purga suave ao criar um cofre, utilize o [comando de criação de chave-teclas az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar proteção de purga a um cofre existente (que já tem exclusão suave ativada), utilize o comando [de atualização az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estes passos:
    1. Para criar uma nova chave, **selecione Gerar/Importar** a partir do menu **Chaves** em **Definições**.
        
        ![Selecione botão gerar/importar](./media/configure-customer-managed-key/select-generate-import.png)

    1. Desa **estacadem opções** para **gerar** e dar à chave um nome.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 

    1. Pode agora selecionar esta chave para associar ao espaço de nomes do Service Bus para encriptar a partir da lista de drop-down. 

        ![Selecione a chave do cofre da chave](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Para redundância, pode somar até 3 chaves. No caso de uma das teclas ter expirado ou não estar acessível, as outras teclas serão utilizadas para encriptação.
        
    1. Preencha os detalhes da chave e clique em **Selecionar**. Isto permitirá a encriptação de dados em repouso no espaço de nomes com uma chave gerida pelo cliente. 


    > [!IMPORTANT]
    > Se procura utilizar a chave gerida pelo Cliente juntamente com a recuperação de desastres geo, por favor reveja abaixo - 
    >
    > Para permitir a encriptação em repouso com a chave gerida pelo cliente, é criada uma política de [acesso](../key-vault/general/secure-your-key-vault.md) para a identidade gerida do Service Bus no Azure KeyVault especificado. Isto garante acesso controlado ao Azure KeyVault a partir do espaço de nomes do Azure Service Bus.
    >
    > Devido a isto:
    > 
    >   * Se [a recuperação de desastres geo](service-bus-geo-dr.md) já estiver ativada para o espaço de nomes do Service Bus e você estiver procurando ativar a chave gerida pelo cliente, então 
    >     * Quebre o emparelhamento
    >     * [Estabeleça a política de acesso](../key-vault/general/assign-access-policy-portal.md) para a identidade gerida tanto para os espaços de nome primário como secundário para o cofre de chaves.
    >     * Configurar encriptação no espaço de nome primário.
    >     * Reassundi os espaços de nome primário e secundário.
    > 
    >   * Se procura ativar o Geo-DR num espaço de nomes de autocarros de serviço onde já está configurada a chave gerida pelo cliente, então -
    >     * [Estabeleça a política de acesso](../key-vault/general/assign-access-policy-portal.md) para a identidade gerida para o espaço de nome secundário para o cofre de chaves.
    >     * Emparelhar os espaços de nome primário e secundário.


## <a name="rotate-your-encryption-keys"></a>Rode as suas chaves de encriptação

Pode rodar a chave no cofre da chave utilizando o mecanismo de rotação Azure Key Vaults. As datas de ativação e de validade também podem ser definidas para automatizar a rotação da chave. O serviço Service Bus detetará novas versões chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves

Revogar o acesso às chaves de encriptação não vai expurgar os dados do Service Bus. No entanto, os dados não podem ser acedidos a partir do espaço de nomes do Service Bus. Pode revogar a chave de encriptação através da política de acesso ou eliminando a chave. Saiba mais sobre as políticas de acesso e a garantia do seu cofre chave do [acesso seguro a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).

Uma vez revogada a chave de encriptação, o serviço Service Bus no espaço de nome encriptado tornar-se-á inoperável. Se o acesso à chave estiver ativado ou a chave eliminada for restaurada, o serviço Service Bus escolherá a chave para que possa aceder aos dados a partir do espaço de nomes do Service Bus encriptado.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Use o modelo de Gestor de Recursos para ativar a encriptação
Esta secção mostra como fazer as seguintes tarefas utilizando **modelos do Gestor de Recursos Azure**. 

1. Crie um espaço de nome de serviço de serviço **premium** com uma identidade de **serviço gerida.**
2. Crie um **cofre chave** e conceda ao serviço acesso à chave cofre. 
3. Atualize o espaço de nomes do Service Bus com as informações do cofre chave (chave/valor). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Criar um espaço de nome de ônibus de serviço premium com identidade de serviço gerido
Esta secção mostra-lhe como criar um espaço de nome do Azure Service Bus com identidade de serviço gerida utilizando um modelo de Gestor de Recursos Azure e PowerShell. 

1. Crie um modelo de Gestor de Recursos Azure para criar um espaço de nome de nível premium de service bus com uma identidade de serviço gerida. Nomeie o ficheiro: **CreateServiceBusPremiumNamespace.jsem**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Crie um ficheiro de parâmetro de modelo nomeado: **CreateServiceBusPremiumNamespaceParams.jsem**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>` - Nome do seu espaço de nomes de ônibus de serviço
    > - `<Location>` - Localização do seu espaço de nomes de ônibus de serviço

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Executar o seguinte comando PowerShell para implementar o modelo para criar um espaço de nome de serviço de serviço premium. Em seguida, recupere a identificação do espaço de nomes do Service Bus para usá-lo mais tarde. `{MyRG}`Substitua-o pelo nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Grant Service Bus namespace acesso à identidade do cofre chave

1. Executar o seguinte comando para criar um cofre de chaves com **proteção de purga** e **eliminação suave** ativada. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OU)
    
    Executar o seguinte comando para atualizar um **cofre de chaves existente**. Especifique os valores para o grupo de recursos e os nomes do cofre antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Desa estade a política de acesso ao cofre de chaves para que a identidade gerida do espaço de nomes do Service Bus possa aceder ao valor da chave no cofre da chave. Utilize o ID do espaço de nomes do Service Bus da secção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Criptografe os dados no espaço de nomes do Service Bus com a chave gerida pelo cliente a partir do cofre de chaves
Até agora, fez os seguintes passos: 

1. Criou um espaço de nome premium com uma identidade gerida.
2. Crie um cofre chave e concedeu o acesso de identidade gerido ao cofre da chave. 

Neste passo, irá atualizar o espaço de nomes do Service Bus com informações sobre o cofre chave. 

1. Crie um ficheiro JSON nomeado **UpdateServiceBusNamespaceWithEncryption.js** com o seguinte conteúdo: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Crie um ficheiro de parâmetro de modelo: **UpdateServiceBusNamespaceWithEncryptionParams.jsligado**.

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>` - Nome do seu espaço de nomes de ônibus de serviço
    > - `<Location>` - Localização do seu espaço de nomes de ônibus de serviço
    > - `<KeyVaultName>` - Nome do seu cofre
    > - `<KeyName>` - Nome da chave no cofre da chave  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Executar o seguinte comando PowerShell para implementar o modelo de Gestor de Recursos. `{MyRG}`Substitua-o pelo nome do seu grupo de recursos antes de executar o comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:
- [Visão geral do ônibus de serviço](service-bus-messaging-overview.md)
- [Visão geral do cofre de chaves](../key-vault/general/overview.md)