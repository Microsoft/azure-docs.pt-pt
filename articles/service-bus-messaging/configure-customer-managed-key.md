---
title: Configure a sua própria chave para encriptar os dados do Ônibus de Serviço Azure em repouso
description: Este artigo fornece informações sobre como configurar a sua própria chave para encriptar o descanso de dados do Azure Service Bus.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: 82a5fbef8c307d60d82b147f04a2a687b8b0433e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459071"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente para encriptar os dados do Ônibus de Serviço Azure em repouso utilizando o portal Azure
O Azure Service Bus Premium fornece encriptação de dados em repouso com encriptação do Serviço de Armazenamento Azure (Azure SSE). O Service Bus Premium conta com o Armazenamento Azure para armazenar os dados e, por padrão, todos os dados armazenados com o Armazenamento Azure são encriptados utilizando chaves geridas pela Microsoft. 

## <a name="overview"></a>Descrição geral
O Azure Service Bus suporta agora a opção de encriptar dados em repouso com chaves geridas pela Microsoft ou chaves geridas pelo cliente (Bring Your Own Key - BYOK). esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às chaves geridas pelo cliente que são usadas para encriptar o Azure Service Bus em repouso.

Ativar a funcionalidade BYOK é um processo de configuração de uma vez no seu espaço de nome.

> [!NOTE]
> Existem algumas ressalvas na chave gerida pelo cliente para encriptação do lado do serviço. 
>   * Esta funcionalidade é suportada pelo nível [Azure Service Bus Premium.](service-bus-premium-messaging.md) Não pode ser ativado para espaços de nome de ônibus de nível padrão.
>   * A encriptação só pode ser ativada para espaços de nomenovos ou vazios. Se o espaço de nome sintetizar dados, então a operação de encriptação falhará.

Pode utilizar o Cofre de Chaves Azure para gerir as suas chaves e auditar o seu uso da chave. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](../key-vault/general/overview.md)

Este artigo mostra como configurar um cofre chave com chaves geridas pelo cliente utilizando o portal Azure. Para aprender a criar um cofre chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o portal Azure](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> A utilização de chaves geridas pelo cliente com o Azure Service Bus requer que o cofre da chave tenha duas propriedades necessárias configuradas. São: **Soft Delete** and **Not Purpur**. Estas propriedades são ativadas por padrão quando cria um novo cofre chave no portal Azure. No entanto, se precisar de ativar estas propriedades num cofre de chaves existente, deve utilizar o PowerShell ou o Azure CLI.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente
Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu espaço de nome Service Bus Premium.
2. Na página **Definições** do seu espaço de nome do Autocarro de Serviço, **selecione Encriptação**.
3. Selecione a encriptação da **chave gerida pelo Cliente em repouso,** como mostrado na imagem seguinte.

    ![Ativar a chave gerida pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre com chaves

Depois de ativar as chaves geridas pelo cliente, precisa de associar a chave gerida pelo cliente ao seu espaço de nome saque de serviço Azure. O ônibus de serviço suporta apenas o Cofre chave Azure. Se ativar a **Encriptação com** a opção chave gerida pelo cliente na secção anterior, precisa de ter a chave importada para o Cofre chave Azure. Além disso, as teclas devem ter **Soft Delete** e **Não Purgar** configuradas para a tecla. Estas definições podem ser configuradas utilizando [powerShell](../key-vault/general/soft-delete-powershell.md) ou [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Para criar um novo cofre chave, siga o Cofre de Chaves Azure [Quickstart](../key-vault/general/overview.md). Para obter mais informações sobre a importação de chaves existentes, consulte [chaves, segredos e certificados.](../key-vault/about-keys-secrets-and-certificates.md)
1. Para ligar a proteção suave de eliminação e purga quando criar um cofre, utilize o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar proteção de purga a um cofre existente (que já tem soft delete ativado), utilize o comando de [atualização az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estes passos:
    1. Para criar uma nova tecla, selecione **Generate/Import** a partir do menu **Chaves** em **Definições**.
        
        ![Selecione botão Geração/Importação](./media/configure-customer-managed-key/select-generate-import.png)

    1. Definir **Opções** para **Gerar** e dar um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 

    1. Agora pode selecionar esta chave para associar o espaço de nome do Ônibus de serviço para encriptar a partir da lista de drop-down. 

        ![Selecione chave do cofre da chave](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Para redundância, pode adicionar até 3 chaves. No caso de uma das teclas ter expirado ou não estiver acessível, as outras chaves serão utilizadas para encriptação.
        
    1. Preencha os detalhes para a chave e clique em **Selecionar**. Isto permitirá a encriptação de dados em repouso no espaço de nome com uma chave gerida pelo cliente. 


    > [!IMPORTANT]
    > Se procura utilizar a chave gerida pelo Cliente juntamente com a recuperação de desastres geo, por favor reveja a seguinte - 
    >
    > Para permitir a encriptação em repouso com a chave gerida pelo cliente, é criada uma política de [acesso](../key-vault/general/secure-your-key-vault.md) para a identidade gerida pelo Bus de Serviço no específico Azure KeyVault. Isto garante o acesso controlado ao Azure KeyVault a partir do espaço de nome do Azure Service Bus.
    >
    > Devido a isto:
    > 
    >   * Se a recuperação de [desastres geo](service-bus-geo-dr.md) já estiver ativada para o espaço de nome do Ônibus de serviço e você estiver procurando ativar a chave gerida pelo cliente, então 
    >     * Quebrar o emparelhamento
    >     * [Estabeleça a política de acesso](../key-vault/general/managed-identity.md) para a identidade gerida para os espaços de nome primário e secundário para o cofre chave.
    >     * Configurar a encriptação no espaço de nome principal.
    >     * Reemparelhe os espaços de nome primário e secundário.
    > 
    >   * Se procura ativar o Geo-DR num espaço de nome de autocarro de serviço onde a chave gerida pelo cliente já está configurada, então -
    >     * [Estabeleça a política de acesso](../key-vault/general/managed-identity.md) para a identidade gerida para o espaço de nome secundário para o cofre chave.
    >     * Emparelhar os espaços de nome primário e secundário.


## <a name="rotate-your-encryption-keys"></a>Rode as suas chaves de encriptação

Pode rodar a chave no cofre da chave utilizando o mecanismo de rotação dos Cofres de Chaves Azure. Para mais informações, consulte [Configurar a rotação da chave e a auditoria](../key-vault/secrets/key-rotation-log-monitoring.md). As datas de ativação e expiração também podem ser definidas para automatizar a rotação da chave. O serviço Service Bus detetará novas versões-chave e começará a utilizá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves

Revogar o acesso às chaves de encriptação não vai expurgar os dados do Service Bus. No entanto, os dados não podem ser acedidos a partir do espaço de nome do Ônibus de serviço. Pode revogar a chave de encriptação através da política de acesso ou apagando a chave. Saiba mais sobre as políticas de acesso e assegurando o seu cofre chave a partir do [acesso seguro a um cofre chave](../key-vault/general/secure-your-key-vault.md).

Uma vez revogada a chave de encriptação, o serviço service Bus no espaço de nome encriptado tornar-se-á inoperável. Se o acesso à chave estiver ativado ou a chave eliminada for restaurada, o serviço Service Bus escolherá a chave para que possa aceder aos dados a partir do espaço de nome supor o Bus de Serviço encriptado.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Use o modelo do Gestor de Recursos para ativar a encriptação
Esta secção mostra como fazer as seguintes tarefas utilizando modelos de **Gestor de Recursos Azure**. 

1. Crie **um** espaço de nome premium Service Bus com uma **identidade de serviço gerida.**
2. Crie um **cofre chave** e conceda o acesso à identidade de serviço ao cofre chave. 
3. Atualize o espaço de nome do Ônibus de serviço com a informação do cofre chave (chave/valor). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Criar um espaço de nome premium Service Bus com identidade de serviço gerida
Esta secção mostra-lhe como criar um espaço de nome de ônibus de serviço Azure com identidade de serviço gerida usando um modelo de Gestor de Recursos Azure e PowerShell. 

1. Crie um modelo de Gestor de Recursos Azure para criar um espaço de nome premium de ônibus de serviço com uma identidade de serviço gerida. Nomeie o ficheiro: **CreateServiceBusPremiumNamespace.json**: 

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
2. Crie um ficheiro de parâmetro de modelo nomeado: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>`- Nome do seu espaço de nome do autocarro de serviço
    > - `<Location>`- Localização do seu espaço de nome do ônibus de serviço

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
3. Execute o seguinte comando PowerShell para implementar o modelo para criar um espaço de nome de ônibus de serviço premium. Em seguida, recupere a identificação do espaço de nome do Ônibus de serviço para usá-lo mais tarde. Substitua-o `{MyRG}` pelo nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Grant Service Bus nomespace acesso ao cofre chave

1. Executar o seguinte comando para criar um cofre chave com **proteção** de purga e **eliminação suave** ativado. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OU)
    
    Executar o seguinte comando para atualizar um **cofre de chaves existente**. Especifique valores para grupo de recursos e nomes de cofre sinuoso antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Desestabeleça a política de acesso ao cofre chave para que a identidade gerida do espaço de nome do Bus de Serviço possa aceder ao valor-chave no cofre chave. Utilize a identificação do espaço de nome do Autocarro de Serviço da secção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Criptografe dados no espaço de nome do Ônibus de serviço com chave gerida pelo cliente a partir do cofre chave
Fez os seguintes passos até agora: 

1. Criou um espaço de nome premium com uma identidade gerida.
2. Crie um cofre chave e conceda o acesso de identidade gerido ao cofre chave. 

Neste passo, irá atualizar o espaço de nome do Ônibus de serviço com informações de cofre chave. 

1. Crie um ficheiro JSON chamado **UpdateServiceBusNamespaceWithEncryption.json** com o seguinte conteúdo: 

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

2. Crie um ficheiro de parâmetro de modelo: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>`- Nome do seu espaço de nome do autocarro de serviço
    > - `<Location>`- Localização do seu espaço de nome do ônibus de serviço
    > - `<KeyVaultName>`- Nome do seu cofre chave
    > - `<KeyName>`- Nome da chave no cofre da chave  

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
3. Executar o seguinte comando PowerShell para implementar o modelo de Gestor de Recursos. Substitua-o `{MyRG}` pelo nome do seu grupo de recursos antes de executar o comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:
- [Visão geral do ônibus de serviço](service-bus-messaging-overview.md)
- [Descrição geral do Key Vault](../key-vault/general/overview.md)


