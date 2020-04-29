---
title: Crie aglomerados De Hadoop Apache usando API Azure REST - Azure
description: Saiba como criar clusters HDInsight submetendo modelos de Gestor de Recursos Azure para a API Azure REST.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 2680304bd73bdbae35b29b89f38ae2665615f5e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239924"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Crie aglomerados De Hadoop Apache usando a API Azure REST

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Aprenda a criar um cluster HDInsight usando um modelo de Gestor de Recursos Azure e a API Azure REST.

A API Azure REST permite-lhe realizar operações de gestão em serviços hospedados na plataforma Azure, incluindo a criação de novos recursos, como clusters HDInsight.

> [!NOTE]  
> Os passos deste documento utilizam o [caracol (utilidadehttps://curl.haxx.se/) ](https://curl.haxx.se/) para comunicar com a API Do REST Azure.

## <a name="create-a-template"></a>Criar um modelo

Os modelos do Gestor de Recursos Azure são documentos JSON que descrevem um grupo de **recursos** e todos os recursos nele (como hDInsight.) Esta abordagem baseada em modelos permite-lhe definir os recursos que precisa para o HDInsight num só modelo.

O seguinte documento JSON é uma fusão do [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)modelo e dos ficheiros de parâmetros de , que cria um cluster baseado em Linux usando uma palavra-passe para proteger a conta de utilizador SSH.

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

Este exemplo é utilizado nos passos deste documento. Substitua os *valores* de exemplo na secção **Parâmetros** pelos valores do seu cluster.

> [!IMPORTANT]  
> O modelo utiliza o número padrão de nós do trabalhador (4) para um cluster HDInsight. Se planeia mais de 32 nós de trabalhador, então deve selecionar um tamanho de nó de cabeça com pelo menos 8 núcleos e ram de 14 GB.
>
> Para obter mais informações sobre tamanhos de nós e custos associados, veja os [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua subscrição Azure

Siga os passos documentados em [Iniciar com o Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) e ligue-se à sua subscrição utilizando o `az login` comando.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]  
> Estes passos são uma versão abreviada do principal de *serviço Create com* secção de senha do Use [Azure CLI para criar um serviço principal para aceder ao](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) documento de recursos. Estes passos criam um diretor de serviço que é usado para autenticar a API Do REST Azure.

1. A partir de uma linha de comando, utilize o seguinte comando para listar as suas assinaturas Azure.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na lista, selecione a subscrição que pretende utilizar e note as colunas **Subscription_ID** e __Tenant_ID.__ Guarde estes valores.

2. Utilize o seguinte comando para criar uma aplicação no Diretório Ativo Azure.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Substitua os `--display-name`valores pelos `--homepage`valores e `--identifier-uris` pelos seus próprios valores. Forneça uma senha para a nova entrada do Diretório Ativo.

   > [!NOTE]  
   > Os `--home-page` `--identifier-uris` valores e valores não precisam de fazer referência a uma página web real alojada na internet. Devem ser uris únicos.

   O valor devolvido deste comando é o ID da __aplicação__ para a nova aplicação. Poupe este valor.

3. Utilize o seguinte comando para criar um diretor de serviço utilizando o ID da **aplicação**.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     O valor devolvido deste comando é o ID do __Objeto__. Poupe este valor.

4. Atribuir a função **Proprietário** ao diretor de serviço utilizando o valor de ID do **objeto.** Utilize o ID de **subscrição** que obteve anteriormente.

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Obtenha um símbolo de autenticação

Utilize o seguinte comando para recuperar um símbolo de autenticação:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

`$TENANTID`Definir, `$APPID`e `$PASSWORD` aos valores obtidos ou utilizados anteriormente.

Se este pedido for bem sucedido, recebe uma resposta de 200 séries e o corpo de resposta contém um documento JSON.

O documento JSON devolvido por este pedido contém um elemento chamado **access_token**. O valor do **access_token** é utilizado para autenticar pedidos à API REST.

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

Utilize o seguinte para criar um grupo de recursos.

* Despleida `$SUBSCRIPTIONID` para o ID de subscrição recebido enquanto cria o diretor de serviço.
* Definido `$ACCESSTOKEN` para o token de acesso recebido no passo anterior.
* Substitua-o `DATACENTERLOCATION` pelo centro de dados em que pretende criar o grupo de recursos e recursos. Por exemplo, "Centro-Sul dos EUA".
* Desem para `$RESOURCEGROUPNAME` o nome que pretende utilizar para este grupo:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Se este pedido for bem sucedido, recebe uma resposta de 200 séries e o corpo de resposta contém um documento JSON contendo informações sobre o grupo. O `"provisioningState"` elemento contém `"Succeeded"`um valor de .

## <a name="create-a-deployment"></a>Criar uma implantação

Utilize o seguinte comando para implantar o modelo no grupo de recursos.

* Desloque-se `$DEPLOYMENTNAME` com o nome que pretende utilizar para esta implementação.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Se guardou o modelo para um ficheiro, pode `-d "{ template and parameters}"`utilizar o seguinte comando em vez de:
>
> `--data-binary "@/path/to/file.json"`

Se este pedido for bem sucedido, recebe uma resposta de 200 séries e o corpo de resposta contém um documento JSON contendo informações sobre a operação de implementação.

> [!IMPORTANT]  
> A implantação foi submetida, mas ainda não está concluída. Pode levar vários minutos, geralmente por volta de 15, para que a implantação esteja concluída.

## <a name="check-the-status-of-a-deployment"></a>Verifique o estado de uma implantação

Para verificar o estado da implantação, utilize o seguinte comando:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Este comando devolve um documento JSON contendo informações sobre a operação de implantação. O `"provisioningState"` elemento contém o estado da implantação. Se este elemento contiver um valor de, `"Succeeded"`então a implantação tiver concluído com sucesso.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passos seguintes

Agora que criou com sucesso um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster.

### <a name="apache-hadoop-clusters"></a>Aglomerados Apache Hadoop

* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar mapeiaReduzir com HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Aglomerados Apache HBase

* [Começa com Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Aglomerados de tempestade apache

* [Desenvolva topoologias java para tempestade Apache no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilize componentes Python na Tempestade Apache no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar topoologias com tempestade Apache no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
