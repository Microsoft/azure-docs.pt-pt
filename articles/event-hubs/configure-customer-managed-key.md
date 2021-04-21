---
title: Configure a sua própria chave para encriptar os dados do Azure Event Hubs em repouso
description: Este artigo fornece informações sobre como configurar a sua própria chave para encriptar o repouso de dados do Azure Event Hubs.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 33587812121051d93aa8b939c3df70530ba65c5e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812449"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso utilizando o portal Azure
O Azure Event Hubs fornece encriptação de dados em repouso com a Encriptação do Serviço de Armazenamento Azure (Azure SSE). O serviço Event Hubs utiliza o Azure Storage para armazenar os dados. Todos os dados armazenados com O Azure Storage são encriptados utilizando as teclas geridas pela Microsoft. Se utilizar a sua própria chave (também designada por Bring Your Own Key (BYOK) ou tecla gerida pelo cliente), os dados ainda são encriptados utilizando a chave gerida pela Microsoft, mas além disso a chave gerida pela Microsoft será encriptada utilizando a chave gerida pelo cliente. Esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às chaves geridas pelo cliente que são utilizadas para encriptar as chaves geridas pela Microsoft. Ativar a função BYOK é um processo de configuração de uma única vez no seu espaço de nome.

> [!NOTE]
> - A capacidade BYOK é suportada por [clusters dedicados ao Evento Hubs.](event-hubs-dedicated-overview.md) Não pode ser ativado para espaços de nomes padrão do Event Hubs.
> - A encriptação só pode ser ativada para espaços novos ou vazios. Se o espaço de nomes contiver centros de eventos, a operação de encriptação falhará.

Pode utilizar o Azure Key Vault para gerir as suas chaves e auditar o uso da chave. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../key-vault/general/overview.md)

Este artigo mostra como configurar um cofre-chave com chaves geridas pelo cliente utilizando o portal Azure. Para aprender a criar um cofre-chave utilizando o portal Azure, consulte [Quickstart: Crie um Cofre de Chaves Azure utilizando o portal Azure](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> A utilização de chaves geridas pelo cliente com a Azure Event Hubs requer que o cofre-chave tenha duas propriedades necessárias configuradas. São:  **Soft Delete** and **Do Not Purpur**. Estas propriedades são ativadas por padrão quando cria um novo cofre chave no portal Azure. No entanto, se necessitar de ativar estas propriedades num cofre de chaves existente, deve utilizar o PowerShell ou o Azure CLI.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente
Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu cluster dedicado ao Event Hubs.
1. Selecione o espaço de nomes no qual deseja ativar BYOK.
1. Na página **de Definições** do espaço de nomes dos Centros de Eventos, selecione **Encriptação**. 
1. Selecione a **encriptação da chave gerida pelo Cliente em repouso,** como mostrado na imagem seguinte. 

    ![Ativar chave gerida pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre com chaves
Depois de ativar as chaves geridas pelo cliente, tem de associar a chave gerida pelo cliente ao seu espaço de nomes Azure Event Hubs. O Event Hubs suporta apenas o Cofre da Chave Azure. Se ativar a **Encriptação com** a opção chave gerida pelo cliente na secção anterior, precisa de ter a chave importada para o Cofre da Chave Azure. Além disso, as teclas devem ter **Soft Delete** e **Não Purgar** configuradas para a chave. Estas definições podem ser configuradas utilizando [PowerShell](../key-vault/general/key-vault-recovery.md) ou [CLI](../key-vault/general/key-vault-recovery.md).

1. Para criar um novo cofre de chaves, siga o Azure Key Vault [Quickstart](../key-vault/general/overview.md). Para obter mais informações sobre a importação de chaves existentes, consulte [sobre chaves, segredos e certificados.](../key-vault/general/about-keys-secrets-certificates.md)
1. Para ligar a proteção de eliminação e purga suave ao criar um cofre, utilize o [comando de criação de chave-teclas az.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar proteção de purga a um cofre existente (que já tem exclusão suave ativada), utilize o comando [de atualização az keyvault.](/cli/azure/keyvault#az_keyvault_update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estes passos:
    1. Para criar uma nova chave, **selecione Gerar/Importar** a partir do menu **Chaves** em **Definições**.
        
        ![Selecione botão gerar/importar](./media/configure-customer-managed-key/select-generate-import.png)
    1. Desa **estacadem opções** para **gerar** e dar à chave um nome.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 
    1. Agora pode selecionar esta chave para associar ao espaço de nomes do Event Hubs para encriptar a partir da lista de drop-down. 

        ![Selecione a chave do cofre da chave](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Preencha os detalhes da chave e clique em **Selecionar**. Isto permitirá a encriptação da chave gerida pela Microsoft com a sua chave (chave gerida pelo cliente). 


## <a name="rotate-your-encryption-keys"></a>Rode as suas chaves de encriptação
Pode rodar a chave no cofre da chave utilizando o mecanismo de rotação Azure Key Vaults. As datas de ativação e de validade também podem ser definidas para automatizar a rotação da chave. O serviço Event Hubs detetará novas versões chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves
Revogar o acesso às chaves de encriptação não vai expurgar os dados dos Centros de Eventos. No entanto, os dados não podem ser acedidos a partir do espaço de nomes do Event Hubs. Pode revogar a chave de encriptação através da política de acesso ou eliminando a chave. Saiba mais sobre as políticas de acesso e a garantia do seu cofre chave do [acesso seguro a um cofre de chaves](../key-vault/general/security-features.md).

Uma vez revogada a chave de encriptação, o serviço Desempaços de Eventos no espaço de nome encriptado tornar-se-á inoperável. Se o acesso à tecla estiver ativado ou a tecla eliminar for restaurada, o serviço Event Hubs escolherá a chave para que possa aceder aos dados a partir do espaço de nomes do Event Hubs encriptado.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico 
A definição de registos de diagnóstico para espaços de nomes ativados byok dá-lhe as informações necessárias sobre as operações. Estes registos podem ser ativados e posteriormente transmitidos para um centro de eventos ou analisados através de análise de registos ou transmitidos para armazenamento para realizar análises personalizadas. Para saber mais sobre os registos de diagnóstico, consulte [a visão geral dos registos de diagnóstico do Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Ativar registos de utilizadores
Siga estes passos para ativar os registos para chaves geridas pelo cliente.

1. No portal Azure, navegue para o espaço de nome que tem BYOK ativado.
1. Selecione **as definições de diagnóstico** em **Monitorização**.

    ![Selecione definições de diagnóstico](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecione **+Adicionar a definição de diagnóstico**. 

    ![Selecione adicionar a definição de diagnóstico](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Forneça um **nome** e selecione para onde pretende transmitir os registos.
1. **Selecione CustomerManagedKeyUserLogs** e **Guarde**. Esta ação permite os registos de BYOK no espaço de nomes.

    ![Selecione a opção de registos de utilizadores de chaves geridos pelo cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Esquema de registo 
Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que usam o formato descrito na tabela seguinte. 

| Nome | Descrição |
| ---- | ----------- | 
| Nome de tarefa | Descrição da tarefa que falhou. |
| ActivityId | Identificação interna que é usada para rastrear. |
| categoria | Define a classificação da tarefa. Por exemplo, se a chave do cofre está a ser desativada, então seria uma categoria de informação ou se uma chave não puder ser desembrulhada, pode cair sob erro. |
| resourceId | Azure Resource Manager resource ID |
| keyVault | Nome completo do cofre da chave. |
| key | O nome-chave que é usado para encriptar o espaço de nomes do Event Hubs. |
| versão | A versão da chave a ser usada. |
| operation | A operação que é feita na chave do cofre. Por exemplo, desativar/ativar a chave, embrulhar ou desembrulhar |
| code | O código que está associado à operação. Exemplo: Código de erro, 404 significa que a chave não foi encontrada. |
| message | Qualquer mensagem de erro associada à operação |

Aqui está um exemplo do registo para uma chave gerida pelo cliente:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Use o modelo de Gestor de Recursos para ativar a encriptação
Esta secção mostra como fazer as seguintes tarefas utilizando **modelos do Gestor de Recursos Azure**. 

1. Crie um **espaço de nomes de Event Hubs** com uma identidade de serviço gerida.
2. Crie um **cofre chave** e conceda ao serviço acesso à chave cofre. 
3. Atualize o espaço de nomes dos Centros de Eventos com as informações do cofre chave (chave/valor). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Criar um cluster de Centros de Eventos e espaço de nome com identidade de serviço gerida
Esta secção mostra-lhe como criar um espaço de nome Azure Event Hubs com identidade de serviço gerida utilizando um modelo de Gestor de Recursos Azure e PowerShell. 

1. Crie um modelo de Gestor de Recursos Azure para criar um espaço de nomes de Centros de Eventos com uma identidade de serviço gerida. Nomeie o ficheiro: **CreateEventHubClusterAndNamespace.jsem**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Crie um ficheiro de parâmetro de modelo nomeado: **CreateEventHubClusterAndNamespaceParams.jsem**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>` - Nome do seu cluster De Centros de Eventos    
    > - `<EventHubsNamespaceName>` - Nome do seu espaço de nomes de Centros de Eventos
    > - `<Location>` - Localização do seu espaço de nomes de Centros de Eventos

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Executar o seguinte comando PowerShell para implementar o modelo para criar um espaço de nomes de Centros de Evento. Em seguida, recupere a identificação do espaço de nomes do Event Hubs para usá-lo mais tarde. `{MyRG}`Substitua-o pelo nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Grant Event Hubs acesso à identidade do espaço de nome ao cofre chave

1. Executar o seguinte comando para criar um cofre de chaves com **proteção de purga** e **eliminação suave** ativada. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OU)    
    
    Executar o seguinte comando para atualizar um **cofre de chaves existente**. Especifique os valores para o grupo de recursos e os nomes do cofre antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Desa estade a política de acesso ao cofre de chaves para que a identidade gerida do espaço de nomes Do Event Hubs possa aceder ao valor da chave no cofre da chave. Utilize o ID do espaço de nomes 'Event Hubs' da secção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Criptografe os dados no espaço de nomes do Event Hubs com a chave gerida pelo cliente a partir do cofre de chaves
Até agora, fez os seguintes passos: 

1. Criou um espaço de nome premium com uma identidade gerida.
2. Crie um cofre chave e concedeu o acesso de identidade gerido ao cofre da chave. 

Neste passo, irá atualizar o espaço de nomes do Event Hubs com informações de cofre chave. 

1. Crie um ficheiro JSON nomeado **CreateEventHubClusterAndNamespace.js** com o seguinte conteúdo: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Crie um ficheiro de parâmetro de modelo: **UpdateEventHubClusterAndNamespaceParams.jsligado**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>` - Nome do seu cluster De Eventos Hubs.        
    > - `<EventHubsNamespaceName>` - Nome do seu espaço de nomes de Centros de Eventos
    > - `<Location>` - Localização do seu espaço de nomes de Centros de Eventos
    > - `<KeyVaultName>` - Nome do seu cofre
    > - `<KeyName>` - Nome da chave no cofre da chave

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Resolução de problemas
Como uma boa prática, ative sempre os registos como mostrados na secção anterior. Ajuda a rastrear as atividades quando a encriptação BYOK está ativada. Também ajuda a descodi o problema.

Seguem-se os códigos de erros comuns a procurar quando a encriptação BYOK estiver ativada.

| Ação | Código de erro | Estado de dados resultante |
| ------ | ---------- | ----------------------- | 
| Remova a permissão de embrulho/desembrulhar de um cofre de chaves | 403 |    Inacessível |
| Remova a filiação de papel da AAD a um diretor da AAD que concedeu a permissão de embrulho/desembrulhar | 403 |  Inacessível |
| Elimine uma chave de encriptação do cofre da chave | 404 | Inacessível |
| Apagar o cofre da chave | 404 | Inacessível (pressupõe-se que a eliminação suave esteja ativada, o que é uma definição necessária.) |
| Alterar o período de validade na chave de encriptação de tal forma que já expirou | 403 |   Inacessível  |
| Mudar o NBF (não antes) de modo que a chave de encriptação não está ativa | 403 | Inacessível  |
| Selecionando a opção **Permitir serviços MSFT** para a firewall do cofre de chaves ou bloquear o acesso à rede ao cofre de chaves que tem a chave de encriptação | 403 | Inacessível |
| Mover o cofre chave para um inquilino diferente | 404 | Inacessível |  
| Problema de rede intermitente ou interrupção de DNS/AAD/MSI |  | Acessível usando chave de encriptação de dados em cache |

> [!IMPORTANT]
> Para ativar o Geo-DR num espaço de nome que está a usar a encriptação BYOK, o espaço de nome secundário para emparelhamento deve estar num cluster dedicado e deve ter um sistema atribuído à identidade gerida ativada nele. Para saber mais, consulte [Identidades Geridas para Recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:
- [Descrição geral dos Event Hubs](event-hubs-about.md)
- [Visão geral do cofre de chaves](../key-vault/general/overview.md)