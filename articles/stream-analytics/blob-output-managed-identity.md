---
title: Saída de blob autenticado com Identidade Gerida Azure Stream Analytics
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho Azure Stream Analytics para a saída de armazenamento Azure Blob.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 2e55b1f18648ce6970b81b943cd8a6a0fc3e7e81
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705137"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>Utilize identidade gerida (pré-visualização) para autenticar o seu trabalho Azure Stream Analytics para o armazenamento de blob Azure

[A autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (pré-visualização) para a saída para o armazenamento Azure Blob dá aos trabalhos stream Analytics acesso direto a uma conta de armazenamento em vez de usar uma cadeia de ligação. Além de uma segurança melhorada, esta funcionalidade também permite escrever dados para uma conta de armazenamento numa Rede Virtual (VNET) dentro do Azure.

Este artigo mostra-lhe como ativar a Identidade Gerida para a(s) saída(s) Blob(s) de um trabalho stream Analytics através do portal Azure e através de uma implementação do Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Crie o trabalho stream Analytics usando o portal Azure

1. Crie um novo trabalho stream Analytics ou abra um trabalho existente no portal Azure. A partir da barra de menu localizada no lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**. Certifique-se de que "Use a identidade gerida atribuída ao sistema" é selecionada e, em seguida, clique no botão **Guardar** na parte inferior do ecrã.

   ![Configurar a identidade gerida stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Na janela de propriedades de saída do lavatório de saída Azure Blob, selecione o modo de autenticação drop-down e escolha **Identidade Gerida**. Para obter informações sobre as outras propriedades de saída, consulte [as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md). Quando terminar, clique em **Guardar**.

   ![Configure saída de armazenamento Azure Blob](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Agora que o trabalho foi criado, consulte o [trabalho Do Stream Analytics para a sua secção](#give-the-stream-analytics-job-access-to-your-storage-account) de conta de armazenamento deste artigo.

## <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

A utilização do Azure Resource Manager permite-lhe automatizar totalmente a implementação do seu trabalho stream Analytics. Pode implementar modelos de Gestor de Recursos utilizando a Azure PowerShell ou o [Azure CLI](/cli/azure/). Os exemplos abaixo utilizam o Azure CLI.


1. Pode criar um recurso **Microsoft.StreamAnalytics/streamingjobs** com uma Identidade Gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para o seu trabalho stream Analytics. Abaixo está um modelo de gestor de recursos de exemplo que implementa um trabalho de Stream Analytics com identidade gerida ativada e um lavatório de saída Blob que utiliza identidade gerida:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    O trabalho acima pode ser implementado para o grupo de recursos **ExemploGroup** utilizando o comando Azure CLI abaixo:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Após a criação do trabalho, pode utilizar o Gestor de Recursos Azure para recuperar a definição completa do trabalho.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    O comando acima devolverá uma resposta como a seguinte:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Tome nota do **principalid** a partir da definição do trabalho, que identifica a Identidade Gerida do seu trabalho dentro do Azure Ative Directory e será usado no passo seguinte para conceder ao Stream Analytics acesso ao trabalho de trabalho na conta de armazenamento.

3. Agora que o trabalho foi criado, consulte o [trabalho Do Stream Analytics para a sua secção](#give-the-stream-analytics-job-access-to-your-storage-account) de conta de armazenamento deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Dê ao Stream Analytics acesso ao seu serviço de armazenamento

Existem dois níveis de acesso que pode optar por dar o seu trabalho stream Analytics:

1. **Acesso ao nível do contentor:** esta opção dá acesso ao trabalho a um recipiente específico existente.
2. **Acesso ao nível da conta:** esta opção dá ao trabalho acesso geral à conta de armazenamento, incluindo a capacidade de criar novos contentores.

A menos que precise do trabalho para criar contentores em seu nome, deve escolher o **acesso ao nível do contentor,** uma vez que esta opção irá conceder ao trabalho o nível mínimo de acesso necessário. Ambas as opções são explicadas abaixo para o portal Azure e a linha de comando.

### <a name="grant-access-via-the-azure-portal"></a>Conceder acesso através do portal Azure

#### <a name="container-level-access"></a>Acesso ao nível do contentor

1. Navegue para o painel de configuração do recipiente dentro da sua conta de armazenamento.

2. Selecione **Controlo de Acesso (IAM)** no lado esquerdo.

3. Na secção "Adicionar uma atribuição de função" clique em **Adicionar**.

4. No painel de atribuição de funções:

    1. Definir a **função** para "Storage Blob Data Contributor"
    2. Certifique-se de que o **acesso a dropdown** é definido para "Utilizador AD Azure, grupo ou principal de serviço".
    3. Digite o nome do seu trabalho stream Analytics no campo de pesquisa.
    4. Selecione o seu trabalho stream Analytics e clique em **Guardar**.

   ![Concessão de acesso a contentores](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Acesso ao nível da conta

1. Navegue até à sua conta de armazenamento.

2. Selecione **Controlo de Acesso (IAM)** no lado esquerdo.

3. Na secção "Adicionar uma atribuição de função" clique em **Adicionar**.

4. No painel de atribuição de funções:

    1. Definir a **função** para "Storage Blob Data Contributor"
    2. Certifique-se de que o **acesso a dropdown** é definido para "Utilizador AD Azure, grupo ou principal de serviço".
    3. Digite o nome do seu trabalho stream Analytics no campo de pesquisa.
    4. Selecione o seu trabalho stream Analytics e clique em **Guardar**.

   ![Acesso à conta de subvenção](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Conceder acesso através da linha de comando

#### <a name="container-level-access"></a>Acesso ao nível do contentor

Para dar acesso a um recipiente específico, executar o seguinte comando utilizando o Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Acesso ao nível da conta

Para dar acesso a toda a conta, executar o seguinte comando utilizando o Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Permitir o acesso ao VNET

Ao configurar as Firewalls da sua conta de armazenamento **e redes virtuais,** pode opcionalmente permitir o tráfego de rede a partir de outros serviços confiáveis da Microsoft. Quando o Stream Analytics autentica usando identidade gerida, fornece provas de que o pedido é originário de um serviço de confiança. Abaixo estão as instruções para permitir esta exceção de acesso VNET.

1.    Navegue para o painel "Firewalls e redes virtuais" dentro do painel de configuração da conta de armazenamento.
2.    Certifique-se de que a opção "Permitir que serviços fidedignos da Microsoft acedam a esta conta de armazenamento".
3.    Se o ativou, clique em **Guardar**.

   ![Permitir o acesso ao VNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Remover Identidade Gerida

A Identidade Gerida criada para um trabalho stream Analytics só é eliminada quando o trabalho é eliminado. Não há como apagar a Identidade Gerida sem apagar o trabalho. Se já não pretender utilizar a Identidade Gerida, pode alterar o método de autenticação para a saída. A Identidade Gerida continuará a existir até que o trabalho seja eliminado, e será usado se decidir utilizar novamente a autenticação de Identidade Gerida.

## <a name="limitations"></a>Limitações
Abaixo estão as limitações atuais desta característica:

1. Contas clássicas de armazenamento Azure.

2. Contas Azure sem Diretório Ativo Azure.

3. O acesso a vários inquilinos não é suportado. O diretor de serviço criado para um determinado trabalho stream Analytics deve residir no mesmo inquilino do Azure Ative Directory em que o trabalho foi criado, e não pode ser usado com um recurso que reside num inquilino azure ative inquilina.

4. [A Identidade Atribuída ao Utilizador](../active-directory/managed-identities-azure-resources/overview.md) não é suportada. Isto significa que o utilizador não pode introduzir o seu próprio principal de serviço para ser utilizado pelo seu trabalho stream Analytics. O diretor de serviço deve ser gerado pela Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics divisória de saída de bolha personalizada](./stream-analytics-custom-path-patterns-blob-storage-output.md)
