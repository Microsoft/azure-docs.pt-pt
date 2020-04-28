---
title: Desenvolver ficheiros Azure NetApp com REST API [ Rest API] Microsoft Docs
description: Descreve como começar com a utilização do Azure NetApp Files REST API.
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
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65957035"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Desenvolver ficheiros Azure NetApp com REST API 

O REST API para o serviço Azure NetApp Files define operações http contra recursos como a conta NetApp, o pool de capacidade, os volumes e instantâneos. Este artigo ajuda-o a começar a usar a API REST API do Azure NetApp Files.

## <a name="azure-netapp-files-rest-api-specification"></a>Especificação da API do Azure NetApp Files REST

A especificação REST API para Ficheiros Azure NetApp é publicada através do [GitHub:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Aceda aos ficheiros Azure NetApp REST API  

1. [Instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.
2. Crie um diretor de serviço no seu Diretório Ativo Azure (Azure AD):
   1. Verifique se tem [permissões suficientes.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)

   1. Insira o seguinte comando no Azure CLI:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      A saída de comando é semelhante ao seguinte exemplo:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Mantenha a saída de comando.  Vai precisar `appId`dos `password`valores e `tenant` valores. 

3. Solicite um sinal de acesso OAuth:

    Os exemplos deste artigo usam cURL.  Também pode utilizar várias ferramentas API, tais como [Carteiro,](https://www.getpostman.com/) [Insónia,](https://insomnia.rest/)e [Pata.](https://paw.cloud/)  

    Substitua as variáveis no exemplo seguinte com a saída de comando do passo 2 acima. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    A saída fornece um sinal de acesso semelhante ao seguinte exemplo:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    O token apresentado é válido por 3600 segundos. Depois disso, tens de pedir um novo símbolo. 
    Guarde o símbolo para um editor de texto.  Vai precisar para o próximo passo.

4. Envie uma chamada de teste e inclua o símbolo para validar o seu acesso à API REST:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Exemplos que utilizam a API  

Este artigo utiliza o seguinte URL para a linha de base dos pedidos. Este URL aponta para a raiz do espaço de nome sinuoso dos Ficheiros Azure NetApp. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Deve substituir `subID` os `resourceGroups` valores e valores nos seguintes exemplos com os seus próprios valores. 

### <a name="get-request-examples"></a>Obtenha exemplos de pedidos

Você usa um pedido GET para consultar objetos de Ficheiros Azure NetApp numa subscrição, como mostram os seguintes exemplos: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Put exemplos de pedido

Utiliza um pedido PUT para criar novos objetos em Ficheiros Azure NetApp, como mostram os seguintes exemplos. O corpo do pedido PUT pode incluir os dados formatados JSON para as alterações, ou pode especificar um ficheiro para ler. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Exemplos jSON

O exemplo que se segue mostra como criar uma conta NetApp:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

O exemplo que se segue mostra como criar um conjunto de capacidades: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

O exemplo que se segue mostra como criar um novo volume: 

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

O exemplo que se segue mostra como criar uma imagem instantânea de um volume: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Tens de `fileSystemId` especificar para criar uma fotografia.  Pode obter `fileSystemId` o valor com um pedido GET a um volume. 

## <a name="next-steps"></a>Passos seguintes

[Consulte a referência rest API dos Ficheiros Azurapp](https://docs.microsoft.com/rest/api/netapp/)
