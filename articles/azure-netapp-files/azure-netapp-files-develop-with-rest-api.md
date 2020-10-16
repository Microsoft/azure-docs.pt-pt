---
title: Desenvolver para Azure NetApp Files com REST API / Microsoft Docs
description: A API REST para o serviço Azure NetApp Files define operações HTTP para recursos como a conta NetApp, o pool de capacidade, os volumes e instantâneos.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 4599f4e3ca291c312c99e938b237d1eb9cd9d407
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929285"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Desenvolver para o Azure NetApp Files com a API REST 

A API REST para o serviço Azure NetApp Files define operações HTTP contra recursos como a conta NetApp, o pool de capacidade, os volumes e instantâneos. Este artigo ajuda-o a começar a utilizar a API dos Ficheiros Azure NetApp.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API especificação

A especificação REST API para ficheiros Azure NetApp é publicada através do [GitHub:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Aceda aos ficheiros Azure NetApp REST API  

1. [Instale o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.
2. Crie um principal de serviço no seu Diretório Ativo Azure (Azure AD):
   1. Verifique se tem [permissões suficientes.](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

   2. Introduza o seguinte comando no Azure CLI: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      A saída do comando é semelhante ao seguinte exemplo:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Mantenha a saída do comando.  Vai precisar dos `appId` `password` valores e `tenant` valores. 

3. Solicite um token de acesso OAuth:

    Os exemplos deste artigo utilizam o cURL. Também pode utilizar várias ferramentas API, tais como [Carteiro,](https://www.getpostman.com/) [Insónia](https://insomnia.rest/)e [Pata.](https://paw.cloud/)  

    Substitua as variáveis no exemplo seguinte com a saída de comando do passo 2 acima. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    A saída fornece um token de acesso semelhante ao seguinte exemplo:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    O token visualizado é válido por 3600 segundos. Depois disso, tens de pedir um novo símbolo. 
    Guarde o símbolo para um editor de texto.  Vai precisar para o próximo passo.

4. Envie uma chamada de teste e inclua o token para validar o seu acesso à API REST:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Exemplos de utilização da API  

Este artigo utiliza o seguinte URL para a linha de base dos pedidos. Este URL aponta para a raiz do espaço de nomes Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

Deve substituir os `SUBIDGOESHERE` valores e `RESOURCEGROUPGOESHERE` valores nos seguintes exemplos pelos seus próprios valores. 

### <a name="get-request-examples"></a>OBTER exemplos de pedido

Utiliza um pedido GET para consultar objetos de Ficheiros Azure NetApp numa subscrição, como mostram os seguintes exemplos: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>PUT solicitar exemplos

Utiliza um pedido PUT para criar novos objetos nos Ficheiros Azure NetApp, como mostram os seguintes exemplos. O corpo do pedido PUT pode incluir os dados formatados JSON para as alterações. Deve ser incluído no comando do caracho como texto ou referências como ficheiro. Para referir o corpo como um ficheiro, guarde o exemplo json para um ficheiro e adicione `-d @<filename>` ao comando do caracol.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>Exemplos JSON

O exemplo a seguir mostra como criar uma conta NetApp:

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

O exemplo a seguir mostra como criar um pool de capacidade: 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

O exemplo a seguir mostra como criar um novo volume. (O protocolo predefinido para o volume é NFSV3.) 

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

O exemplo a seguir mostra como criar uma imagem instantânea de um volume: 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> Tem de especificar `fileSystemId` para criar uma imagem instantânea.  Pode obter o `fileSystemId` valor com um pedido GET para um volume. 

## <a name="next-steps"></a>Passos seguintes

[Consulte a referência AZure NetApp API](/rest/api/netapp/)