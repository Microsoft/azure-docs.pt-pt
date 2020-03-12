---
title: Produção de bolha autenticada com Identidade Gerida Azure Stream Analytics
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho de Azure Stream Analytics à saída de armazenamento Do Blob Azure.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129967"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Use identidade gerida para autenticar o seu trabalho azure stream analytics para a saída de Armazenamento Azure Blob

[A autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para saída para o armazenamento De Blob Azure dá aos trabalhos stream Analytics acesso direto a uma conta de armazenamento em vez de usar uma cadeia de ligação. Além de uma maior segurança, esta funcionalidade também permite escrever dados numa conta de armazenamento numa Rede Virtual (VNET) dentro do Azure.

Este artigo mostra-lhe como ativar a Identidade Gerida para a saída blob(s) de um trabalho de Stream Analytics através do portal Azure e através de uma implementação do Gestor de Recursos Azure.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Crie o trabalho stream analytics usando o portal Azure

1. Crie um novo trabalho stream analytics ou abra um emprego existente no portal Azure. A partir da barra de menu seletiva do lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**. Certifique-se de que "Use a Identidade Gerida atribuída pelo sistema" e, em seguida, clique no botão **Guardar** na parte inferior do ecrã.

   ![Identidade gerida por Configure Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Na janela de propriedades de saída do depósito do Azure Blob, selecione o modo de autenticação drop-down e escolha **Identidade Gerida**. Para obter informações sobre as outras propriedades de saída, consulte [as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md). Quando terminar, clique em **Guardar**.

   ![Configure saída de armazenamento De Blob Azure](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Agora que o trabalho é criado, consulte o acesso ao [trabalho do Give the Stream Analytics à sua](#give-the-stream-analytics-job-access-to-your-storage-account) secção de conta de armazenamento deste artigo.

## <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

A utilização do Gestor de Recursos Azure permite-lhe automatizar totalmente a implementação do seu trabalho em Stream Analytics. Pode implementar modelos de Gestor de Recursos utilizando o Azure PowerShell ou o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Os exemplos abaixo utilizam o Azure CLI.


1. Pode criar um recurso **Microsoft.StreamAnalytics/streamingjobs** com uma Identidade Gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para o seu trabalho de Stream Analytics. Abaixo está um modelo de Gestor de Recursos de exemplo que implementa um trabalho de Stream Analytics com identidade gerida ativada e um lavatório de saída Blob que usa identidade gerida:

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

    O trabalho acima pode ser implantado para o grupo de recursos **ExemploGroup** utilizando o comando CLI do Azure abaixo:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
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

   Tome nota do **principado da** definição do trabalho, que identifica a Identidade Gerida do seu trabalho dentro do Diretório Ativo Azure e será utilizado no próximo passo para conceder o acesso ao emprego da Stream Analytics na conta de armazenamento.

3. Agora que o trabalho é criado, consulte o acesso ao [trabalho do Give the Stream Analytics à sua](#give-the-stream-analytics-job-access-to-your-storage-account) secção de conta de armazenamento deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Dê ao stream Analytics acesso ao emprego na sua conta de armazenamento

Existem dois níveis de acesso que pode optar por dar o seu trabalho de Streaming Analytics:

1. Acesso ao **nível do contentor:** esta opção dá ao trabalho acesso a um recipiente específico existente.
2. Acesso ao **nível da conta:** esta opção dá ao trabalho acesso geral à conta de armazenamento, incluindo a capacidade de criar novos contentores.

A menos que necessite do trabalho para criar contentores em seu nome, deve escolher o acesso ao **nível do Contentor,** uma vez que esta opção concederá ao trabalho o nível mínimo de acesso necessário. Ambas as opções são explicadas abaixo para o portal Azure e a linha de comando.

### <a name="grant-access-via-the-azure-portal"></a>Conceder acesso através do portal Azure

#### <a name="container-level-access"></a>Acesso ao nível do contentor

1. Navegue para o painel de configuração do recipiente dentro da sua conta de armazenamento.

2. Selecione **Control de Acesso (IAM)** no lado esquerdo.

3. Na secção "Adicionar uma atribuição de funções", clique em **adicionar**.

4. No painel de atribuição de papéis:

    1. Definir a **função** de "Contribuinte de Dados blob de armazenamento"
    2. Certifique-se de que o **acesso atribuído ao** dropdown está definido para "Utilizador, grupo ou diretor de serviço Azure AD".
    3. Digite o nome do seu trabalho de Stream Analytics no campo de pesquisa.
    4. Selecione o seu trabalho de Analytics stream e clique em **Guardar**.

   ![Acesso ao contentor de concessão](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Acesso ao nível da conta

1. Navegue até à sua conta de armazenamento.

2. Selecione **Control de Acesso (IAM)** no lado esquerdo.

3. Na secção "Adicionar uma atribuição de funções", clique em **adicionar**.

4. No painel de atribuição de papéis:

    1. Definir a **função** de "Contribuinte de Dados blob de armazenamento"
    2. Certifique-se de que o **acesso atribuído ao** dropdown está definido para "Utilizador, grupo ou diretor de serviço Azure AD".
    3. Digite o nome do seu trabalho de Stream Analytics no campo de pesquisa.
    4. Selecione o seu trabalho de Analytics stream e clique em **Guardar**.

   ![Acesso à conta de concessão](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Conceder acesso através da linha de comando

#### <a name="container-level-access"></a>Acesso ao nível do contentor

Para dar acesso a um recipiente específico, execute o seguinte comando utilizando o Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Acesso ao nível da conta

Para dar acesso a toda a conta, execute o seguinte comando utilizando o Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Ativar o acesso vNET

Ao configurar as **Firewalls e redes virtuais**da sua conta de armazenamento, pode opcionalmente permitir o tráfego de rede a partir de outros serviços confiáveis da Microsoft. Quando o Stream Analytics autentica usando a Identidade Gerida, fornece a prova de que o pedido é originário de um serviço de confiança. Abaixo estão as instruções para ativar esta exceção de acesso VNET.

1.  Navegue para o painel "Firewalls e redes virtuais" dentro do painel de configuração da conta de armazenamento.
2.  Certifique-se de que a opção "Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento".
3.  Se o ativar, clique em **Guardar**.

   ![Ativar o acesso vNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Limitações
Abaixo estão as limitações atuais desta funcionalidade:

1. Contas clássicas de Armazenamento Azure.

2. Contas Azure sem Diretório Ativo Azure.

3. O acesso a vários inquilinos não é suportado. O diretor de serviço criado para um determinado trabalho stream analytics deve residir no mesmo inquilino azure Ative Directory em que o trabalho foi criado, e não pode ser usado com um recurso que reside em um inquilino azure Ative Directory diferente.

4. [A Identidade Atribuída](../active-directory/managed-identities-azure-resources/overview.md) ao Utilizador não é suportada. Isto significa que o utilizador não pode entrar no seu próprio serviço principal para ser utilizado pelo seu trabalho stream analytics. O diretor de serviço deve ser gerado pelo Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Partição de saída de blob personalizada Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
