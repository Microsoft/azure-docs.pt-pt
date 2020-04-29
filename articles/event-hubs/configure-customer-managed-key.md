---
title: Configure a sua própria chave para encriptar dados do Azure Event Hubs em repouso
description: Este artigo fornece informações sobre como configurar a sua própria chave para encriptar o descanso de dados do Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459139"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso utilizando o portal Azure
O Azure Event Hubs fornece encriptação de dados em repouso com encriptação do Serviço de Armazenamento Azure (Azure SSE). O Event Hubs conta com o Armazenamento Azure para armazenar os dados e, por padrão, todos os dados armazenados com o Armazenamento Azure são encriptados utilizando chaves geridas pela Microsoft. 

## <a name="overview"></a>Descrição geral
O Azure Event Hubs suporta agora a opção de encriptar dados em repouso com chaves geridas pela Microsoft ou chaves geridas pelo cliente (Bring Your Own Key – BYOK). Esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às chaves geridas pelo cliente que são usadas para encriptar dados do Azure Event Hubs em repouso.

Ativar a funcionalidade BYOK é um processo de configuração de uma vez no seu espaço de nome.

> [!NOTE]
> A capacidade BYOK é apoiada por [Event Hubs dedicados agrupamentos de inquilinos únicos.](event-hubs-dedicated-overview.md) Não pode ser ativado para espaços de nome padrão do Event Hubs.

Pode utilizar o Cofre de Chaves Azure para gerir as suas chaves e auditar o seu uso da chave. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](../key-vault/general/overview.md)

Este artigo mostra como configurar um cofre chave com chaves geridas pelo cliente utilizando o portal Azure. Para aprender a criar um cofre chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o portal Azure](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> A utilização de chaves geridas pelo cliente com hubs de eventos Azure requer que o cofre chave tenha duas propriedades necessárias configuradas. São: **Soft Delete** and **Not Purpur**. Estas propriedades são ativadas por padrão quando cria um novo cofre chave no portal Azure. No entanto, se precisar de ativar estas propriedades num cofre de chaves existente, deve utilizar o PowerShell ou o Azure CLI.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente
Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu cluster dedicado ao Event Hubs.
1. Selecione o espaço de nome no qual pretende ativar o BYOK.
1. Na página **Definições** do espaço de nome sinuoso do seu Event Hubs, **selecione Encriptação**. 
1. Selecione a encriptação da **chave gerida pelo Cliente em repouso,** como mostrado na imagem seguinte. 

    ![Ativar a chave gerida pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre com chaves
Depois de ativar as chaves geridas pelo cliente, precisa de associar a chave gerida pelo cliente ao seu espaço de nome Sem Nome Azure Event Hubs. O Event Hubs suporta apenas o Cofre chave Azure. Se ativar a **Encriptação com** a opção chave gerida pelo cliente na secção anterior, precisa de ter a chave importada para o Cofre chave Azure. Além disso, as teclas devem ter **Soft Delete** e **Não Purgar** configuradas para a tecla. Estas definições podem ser configuradas utilizando [powerShell](../key-vault/general/soft-delete-powershell.md) ou [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Para criar um novo cofre chave, siga o Cofre de Chaves Azure [Quickstart](../key-vault/general/overview.md). Para obter mais informações sobre a importação de chaves existentes, consulte [chaves, segredos e certificados.](../key-vault/about-keys-secrets-and-certificates.md)
1. Para ligar a proteção suave de eliminação e purga quando criar um cofre, utilize o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar proteção de purga a um cofre existente (que já tem soft delete ativado), utilize o comando de [atualização az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estes passos:
    1. Para criar uma nova tecla, selecione **Generate/Import** a partir do menu **Chaves** em **Definições**.
        
        ![Selecione botão Geração/Importação](./media/configure-customer-managed-key/select-generate-import.png)
    1. Definir **Opções** para **Gerar** e dar um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 
    1. Agora pode selecionar esta chave para associar o espaço de nome do Event Hubs para encriptar a partir da lista de drop-down. 

        ![Selecione chave do cofre da chave](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Preencha os detalhes para a chave e clique em **Selecionar**. Isto permitirá a encriptação de dados em repouso no espaço de nome com uma chave gerida pelo cliente. 


## <a name="rotate-your-encryption-keys"></a>Rode as suas chaves de encriptação
Pode rodar a chave no cofre da chave utilizando o mecanismo de rotação dos Cofres de Chaves Azure. Para mais informações, consulte [Configurar a rotação da chave e a auditoria](../key-vault/secrets/key-rotation-log-monitoring.md). As datas de ativação e expiração também podem ser definidas para automatizar a rotação da chave. O serviço Event Hubs detetará novas versões-chave e começará a utilizá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves
Revogar o acesso às chaves de encriptação não vai expurgar os dados dos Centros de Eventos. No entanto, os dados não podem ser acedidos a partir do espaço de nome sinuoso do Event Hubs. Pode revogar a chave de encriptação através da política de acesso ou apagando a chave. Saiba mais sobre as políticas de acesso e assegurando o seu cofre chave a partir do [acesso seguro a um cofre chave](../key-vault/general/secure-your-key-vault.md).

Uma vez revogada a chave de encriptação, o serviço De Eventos Hubs no espaço de nome encriptado tornar-se-á inoperável. Se o acesso à chave estiver ativado ou a chave de exclusão for restaurada, o serviço Event Hubs escolherá a chave para que possa aceder aos dados a partir do espaço de nome sinuoso do Event Hubs encriptado.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico 
A definição de registos de diagnóstico para espaços de nome satisonizados BYOK dá-lhe as informações necessárias sobre as operações quando um espaço de nome é encriptado com chaves geridas pelo cliente. Estes registos podem ser ativados e posteriormente transmitidos para um hub de eventos ou analisados através de análise de log ou transmitidos para armazenamento para realizar análises personalizadas. Para saber mais sobre os registos de diagnóstico, consulte a [visão geral dos registos de Diagnóstico Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Ativar os registos dos utilizadores
Siga estes passos para ativar os registos das chaves geridas pelo cliente.

1. No portal Azure, navegue para o espaço de nome que tem BYOK habilitado.
1. Selecione **as definições** de diagnóstico sob **monitorização**.

    ![Selecione definições de diagnóstico](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecione **+Adicione a definição de diagnóstico**. 

    ![Selecione adicionar definição de diagnóstico](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Forneça um **nome** e selecione para onde pretende transmitir os registos.
1. Selecione **CustomerManagedKeyUserLogs** e **Save**. Esta ação permite os registos de BYOK no espaço de nome.

    ![Selecione a opção de registo de registos de utilizadores de chaves gerida pelo cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Esquema de log 
Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que utilizam o formato descrito na tabela seguinte. 

| Nome | Descrição |
| ---- | ----------- | 
| Nome de tarefa | Descrição da tarefa que falhou. |
| Atividadeid | Identificação interna que é usada para rastrear. |
| categoria | Define a classificação da tarefa. Por exemplo, se a chave do seu cofre chave estiver a ser desativada, então seria uma categoria de informação ou se uma chave não pode ser desembrulhada, pode cair por engano. |
| resourceId | ID de recurso do Gestor de Recursos Azure |
| keyVault | Nome completo do cofre da chave. |
| key | O nome chave que é usado para encriptar o espaço de nome do Event Hubs. |
| versão | A versão da chave que está a ser usada. |
| operação | A operação que foi realizada na chave do seu cofre chave. Por exemplo, desativar/ativar a chave, embrulhar ou desembrulhar |
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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Use o modelo do Gestor de Recursos para ativar a encriptação
Esta secção mostra como fazer as seguintes tarefas utilizando modelos de **Gestor de Recursos Azure**. 

1. Crie um espaço de **nome sinuoso** do Event Hubs com uma identidade de serviço gerida.
2. Crie um **cofre chave** e conceda o acesso à identidade de serviço ao cofre chave. 
3. Atualize o espaço de nome sinuoso do Event Hubs com a informação do cofre chave (chave/valor). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Criar um cluster de Hubs de Eventos e espaço de nome com identidade de serviço gerida
Esta secção mostra-lhe como criar um espaço de nome do Azure Event Hubs com identidade de serviço gerida utilizando um modelo de Gestor de Recursos Azure e PowerShell. 

1. Crie um modelo de Gestor de Recursos Azure para criar um espaço de nome de Event Hubs com uma identidade de serviço gerida. Nomeie o ficheiro: **CreateEventHubClusterAndNamespace.json**: 

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
2. Crie um ficheiro de parâmetro de modelo nomeado: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>`- Nome do seu cluster De Centros de Eventos    
    > - `<EventHubsNamespaceName>`- Nome do seu espaço de nome Sem Nome do Evento Hubs
    > - `<Location>`- Localização do seu espaço de nome Sem Nome Do seu Evento Hubs

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
3. Executar o seguinte comando PowerShell para implementar o modelo para criar um espaço de nome de Event Hubs. Em seguida, recupere a identificação do espaço de nome do Event Hubs para usá-lo mais tarde. Substitua-o `{MyRG}` pelo nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Grant Event Hubs nomespace acesso de identidade ao cofre chave

1. Executar o seguinte comando para criar um cofre chave com **proteção** de purga e **eliminação suave** ativado. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OU)    
    
    Executar o seguinte comando para atualizar um **cofre de chaves existente**. Especifique valores para grupo de recursos e nomes de cofre sinuoso antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Desestabeleça a política de acesso ao cofre chave para que a identidade gerida do espaço de nome do Event Hubs possa aceder ao valor-chave no cofre chave. Utilize o espaço de identificação do espaço de nome sinuoso do Event Hubs da secção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Criptografe dados no espaço de nome do Event Hubs com chave gerida pelo cliente a partir do cofre chave
Fez os seguintes passos até agora: 

1. Criou um espaço de nome premium com uma identidade gerida.
2. Crie um cofre chave e conceda o acesso de identidade gerido ao cofre chave. 

Neste passo, irá atualizar o espaço de nome do Event Hubs com informações sobre o cofre chave. 

1. Crie um ficheiro JSON chamado **CreateEventHubClusterAndNamespace.json** com o seguinte conteúdo: 

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

2. Crie um ficheiro de parâmetro de modelo: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>`- Nome do seu cluster De Centros de Eventos.        
    > - `<EventHubsNamespaceName>`- Nome do seu espaço de nome Sem Nome do Evento Hubs
    > - `<Location>`- Localização do seu espaço de nome Sem Nome Do seu Evento Hubs
    > - `<KeyVaultName>`- Nome do seu cofre chave
    > - `<KeyName>`- Nome da chave no cofre da chave

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
3. Executar o seguinte comando PowerShell para implementar o modelo de Gestor de Recursos. Substitua-o `{MyRG}` pelo nome do seu grupo de recursos antes de executar o comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Resolução de problemas
Como uma boa prática, ative sempre os registos como mostrado na secção anterior. Ajuda a rastrear as atividades quando a encriptação BYOK está ativada. Também ajuda a detetar os problemas.

Seguem-se os códigos de erros comuns a procurar quando a encriptação BYOK está ativada.

| Ação | Código de erro | Estado de dados resultante |
| ------ | ---------- | ----------------------- | 
| Remova a permissão de embrulho/desembrulhar de um cofre chave | 403 |    Inacessível |
| Remover a adesão ao papel aAD de um diretor da AAD que concedeu a permissão de embrulho/desembrulhar | 403 |  Inacessível |
| Eliminar uma chave de encriptação do cofre da chave | 404 | Inacessível |
| Apague o cofre da chave | 404 | Inacessível (pressupõe que o soft-delete está ativado, que é uma definição necessária.) |
| Alterar o período de validade na chave de encriptação de tal forma que já expirou | 403 |   Inacessível  |
| Mudar a NBF (não antes) de tal forma que a chave de encriptação não está ativa | 403 | Inacessível  |
| Selecionando a opção **Permitir serviços MSFT** para a firewall do cofre chave ou bloquear o acesso da rede ao cofre chave que tem a chave de encriptação | 403 | Inacessível |
| Movendo o cofre chave para um inquilino diferente | 404 | Inacessível |  
| Problema de rede intermitente ou interrupção dNS/AAD/MSI |  | Acessível usando a chave de encriptação de dados em cache |

> [!IMPORTANT]
> Para ativar o Geo-DR num espaço de nome que esteja a usar a encriptação BYOK, o espaço de nome secundário para emparelhamento deve estar num cluster dedicado e deve ter um sistema de identidade gerida ativado nele. Para saber mais, consulte [Identidades Geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:
- [Descrição geral dos Event Hubs](event-hubs-about.md)
- [Descrição geral do Key Vault](../key-vault/general/overview.md)




