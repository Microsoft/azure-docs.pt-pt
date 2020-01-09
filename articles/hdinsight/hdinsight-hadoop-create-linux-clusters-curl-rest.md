---
title: Criar clusters de Apache Hadoop usando a API REST do Azure-Azure
description: Saiba como criar clusters HDInsight enviando Azure Resource Manager modelos para a API REST do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: e2d63626ec548f0107d7af935af32e90d6972849
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435533"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Criar clusters Apache Hadoop usando a API REST do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como criar um cluster HDInsight usando um modelo de Azure Resource Manager e a API REST do Azure.

A API REST do Azure permite que você execute operações de gerenciamento em serviços hospedados na plataforma do Azure, incluindo a criação de novos recursos, como clusters HDInsight.

> [!NOTE]  
> As etapas neste documento usam o utilitário de [rotação (https://curl.haxx.se/)](https://curl.haxx.se/) para se comunicar com a API REST do Azure.

## <a name="create-a-template"></a>Criar um modelo

Os modelos de Azure Resource Manager são documentos JSON que descrevem um **grupo de recursos** e todos os recursos nele (como o HDInsight). Essa abordagem baseada em modelo permite que você defina os recursos necessários para o HDInsight em um modelo.

O documento JSON a seguir é uma fusão dos arquivos de modelo e parâmetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que cria um cluster baseado em Linux usando uma senha para proteger a conta de usuário SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Este exemplo é usado nas etapas deste documento. Substitua os *valores* de exemplo na seção de **parâmetros** pelos valores para o cluster.

> [!IMPORTANT]  
> O modelo usa o número padrão de nós de trabalho (4) para um cluster HDInsight. Se você planejar mais de 32 nós de trabalho, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

Siga as etapas documentadas em introdução [ao CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) e conecte-se à sua assinatura usando o comando `az login`.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]  
> Estas etapas são uma versão resumida da seção *criar entidade de serviço com a senha* do documento [usar CLI do Azure para criar uma entidade de serviço para acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) . Estas etapas criam uma entidade de serviço que é usada para autenticar a API REST do Azure.

1. Em uma linha de comando, use o comando a seguir para listar suas assinaturas do Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na lista, selecione a assinatura que você deseja usar e observe as colunas **Subscription_ID** e __Tenant_ID__ . Salve esses valores.

2. Use o comando a seguir para criar um aplicativo no Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Substitua os valores do `--display-name`, `--homepage`e `--identifier-uris` pelos seus próprios valores. Forneça uma senha para a nova entrada de Active Directory.

   > [!NOTE]  
   > Os valores `--home-page` e `--identifier-uris` não precisam fazer referência a uma página da Web real hospedada na Internet. Eles devem ser URIs exclusivos.

   O valor retornado desse comando é a __ID do aplicativo__ para o novo aplicativo. Salve esse valor.

3. Use o comando a seguir para criar uma entidade de serviço usando a **ID do aplicativo**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     O valor retornado desse comando é a __ID do objeto__. Salve esse valor.

4. Atribua a função de **proprietário** à entidade de serviço usando o valor de **ID de objeto** . Use a **ID de assinatura** obtida anteriormente.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Obter um token de autenticação

Use o seguinte comando para recuperar um token de autenticação:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Defina `$TENANTID`, `$APPID`e `$PASSWORD` para os valores obtidos ou usados anteriormente.

Se essa solicitação for bem-sucedida, você receberá uma resposta da série 200 e o corpo da resposta conterá um documento JSON.

O documento JSON retornado por essa solicitação contém um elemento chamado **access_token**. O valor de **access_token** é usado para autenticar solicitações para a API REST.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Use o seguinte para criar um grupo de recursos.

* Defina `$SUBSCRIPTIONID` para a ID de assinatura recebida ao criar a entidade de serviço.
* Defina `$ACCESSTOKEN` para o token de acesso recebido na etapa anterior.
* Substitua `DATACENTERLOCATION` pelo data center você deseja criar o grupo de recursos e recursos no. Por exemplo, ' South EUA Central '.
* Defina `$RESOURCEGROUPNAME` para o nome que você deseja usar para este grupo:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Se essa solicitação for bem-sucedida, você receberá uma resposta da série 200 e o corpo da resposta conterá um documento JSON que contém informações sobre o grupo. O elemento `"provisioningState"` contém um valor de `"Succeeded"`.

## <a name="create-a-deployment"></a>Criar uma implementação

Use o comando a seguir para implantar o modelo no grupo de recursos.

* Defina `$DEPLOYMENTNAME` para o nome que você deseja usar para essa implantação.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Se você salvou o modelo em um arquivo, poderá usar o seguinte comando em vez de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se essa solicitação for bem-sucedida, você receberá uma resposta da série 200 e o corpo da resposta conterá um documento JSON que contém informações sobre a operação de implantação.

> [!IMPORTANT]  
> A implantação foi enviada, mas não foi concluída. Pode levar vários minutos, geralmente cerca de 15, para que a implantação seja concluída.

## <a name="check-the-status-of-a-deployment"></a>Verificar o status de uma implantação

Para verificar o status da implantação, use o seguinte comando:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Esse comando retorna um documento JSON que contém informações sobre a operação de implantação. O elemento `"provisioningState"` contém o status da implantação. Se esse elemento contiver um valor de `"Succeeded"`, a implantação foi concluída com êxito.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Agora que você criou um cluster HDInsight com êxito, use o seguinte para aprender a trabalhar com o cluster.

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para o Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm

* [Desenvolver topologias Java para Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes do Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
