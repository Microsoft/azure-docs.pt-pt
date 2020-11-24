---
title: Apache Kafka REST proxy - Azure HDInsight
description: Aprenda a fazer operações apache kafka usando um representante kafka REST em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: d6c45a5c8062c3b6441309361037f8755a552074
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791889"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interaja com os clusters Apache Kafka em Azure HDInsight usando um representante rest

O Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de uma API REST sobre HTTPS. Esta ação significa que os seus clientes Kafka podem estar fora da sua rede virtual. Os clientes podem fazer chamadas HTTPS simples e seguras para o cluster Kafka, em vez de confiarem nas bibliotecas kafka. Este artigo irá mostrar-lhe como criar um cluster de proxy REST habilitado para Kafka. Também fornece um código de amostra que mostra como fazer chamadas para o proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para operações apoiadas pela API Kafka REST, consulte [hdInsight Kafka REST Proxy API Reference](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Fundo

![Design de procuração kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações suportadas pela API, consulte [Apache Kafka REST Proxy API](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>PONTO final de Proxy REST Proxy

A criação de um cluster HDInsight Kafka com proxy REST cria um novo ponto final público para o seu cluster, que pode encontrar no seu cluster HDInsight **Properties** no portal Azure.

### <a name="security"></a>Segurança

O acesso ao representante kafka REST é gerido com grupos de segurança do Azure Ative Directory. Ao criar o cluster Kafka, forneça ao grupo de segurança Azure AD acesso ao ponto final REST. Os clientes kafka que precisam de acesso ao representante rest deve ser registado neste grupo pelo proprietário do grupo. O proprietário do grupo pode registar-se através do Portal ou via PowerShell.

Para pedidos de ponto final de procuração REST, as aplicações do cliente devem obter um token OAuth. O símbolo é usado para verificar a adesão ao grupo de segurança. Encontre uma [amostra de aplicação do Cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. A aplicação do cliente transmite o token OAuth no pedido HTTPS ao representante DO REST.

> [!NOTE]
> Consulte [a aplicação Manage e o acesso a recursos utilizando grupos Azure Ative Directory,](../../active-directory/fundamentals/active-directory-manage-groups.md)para saber mais sobre grupos de segurança AAD. Para obter mais informações sobre o funcionamento dos tokens da OAuth, consulte [o acesso autorizado às aplicações web do Azure Ative Directory utilizando o fluxo de concessão de código OAuth 2.0](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Representante kafka REST com grupos de segurança de rede
Se você trouxer o seu próprio VNet e controlar o tráfego da rede com grupos de segurança de rede, permita o tráfego **de entrada no** porto **9400,** além da porta 443. Isto garantirá que o servidor proxy Kafka REST está acessível.

## <a name="prerequisites"></a>Pré-requisitos

1. Registar uma aplicação com o Azure AD. As aplicações do cliente que escreve para interagir com o representante kafka REST usarão o ID e o segredo desta aplicação para autenticar a Azure.

1. Crie um grupo de segurança do AAD. Adicione a aplicação que registou com a Azure AD ao grupo de segurança como **membro** do grupo. Este grupo de segurança será utilizado para controlar quais aplicações podem interagir com o representante DO REST. Para obter mais informações sobre a criação de grupos AD Azure, consulte [criar um grupo básico e adicionar membros usando o Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Validar o grupo é do tipo **Segurança.**
    ![Grupo de Segurança](./media/rest-proxy/rest-proxy-group.png)

    Validar esta candidatura é membro do Grupo.
    ![Ver Adesão](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster Kafka com proxy REST habilitado

Os passos abaixo utilizam o portal Azure. Para um exemplo utilizando o Azure CLI, consulte [o cluster de procuração Apache Kafka REST utilizando o Azure CLI](tutorial-cli-rest-proxy.md).

1. Durante o fluxo de trabalho de criação de cluster Kafka, no **separador Segurança + rede,** verifique a opção **de procuração Enable Kafka REST.**

     ![A screenshot mostra a página de cluster Create H D Insight com segurança + rede selecionada.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique **em Select Security Group**. Na lista de grupos de segurança, selecione o grupo de segurança que pretende ter acesso ao representante DO REST. Pode utilizar a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Selecione** na parte inferior.

     ![A screenshot mostra a página de cluster Create H D Insight com a opção de selecionar um grupo de segurança.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete os passos restantes para criar o seu cluster como descrito no [cluster Create Apache Kafka em Azure HDInsight utilizando o portal Azure](./apache-kafka-get-started.md).

1. Uma vez criado o cluster, vá às propriedades do cluster para gravar o URL de procuração kafka REST.

     ![ver REST proxy URL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Amostra de aplicação de cliente

Pode utilizar o código python abaixo para interagir com o representante REST no seu cluster Kafka. Para utilizar a amostra de código, siga estes passos:

1. Guarde o código de amostra numa máquina com python instalado.
1. Instale as dependências de python necessárias executando `pip3 install msal` .
1. Modifique a secção de código **Configurar estas propriedades** e atualizar as seguintes propriedades para o seu ambiente:

    |Propriedade |Descrição |
    |---|---|
    |ID do inquilino|O inquilino Azure onde está a sua assinatura.|
    |ID de Cliente|A identificação para o pedido que registou no grupo de segurança.|
    |Segredo do Cliente|O segredo para o pedido que registou no grupo de segurança.|
    |Kafkarest_endpoint|Obtenha este valor a partir do separador **Propriedades** na visão geral do cluster, conforme descrito na [secção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve estar no seguinte formato – `https://<clustername>-kafkarest.azurehdinsight.net`|

1. A partir da linha de comando, execute o ficheiro python executando `sudo python3 <filename.py>`

Este código faz a seguinte ação:

1. Vai buscar um token OAuth da Azure AD.
1. Mostra como fazer um pedido ao representante kafka REST.

Para obter mais informações sobre a obtenção de fichas OAuth em Python, consulte [a classe Python AuthenticationContext](/python/api/adal/adal.authentication_context.authenticationcontext). Você pode ver um atraso enquanto `topics` que não são criados ou apagados através do representante kafka REST são refletidos lá. Este atraso deve-se à atualização da cache. O campo de **valor** da API do produtor foi reforçado. Agora, aceita objetos JSON e qualquer forma serializada.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Encontre abaixo outra amostra sobre como obter um token de Azure para o proxy REST usando um comando de caracóis. **Note que precisamos do `scope=https://hib.azurehdinsight.net/.default` especificado enquanto recebemos um símbolo.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passos seguintes

* [Documentos de referência da API de procuração kafka REST](/rest/api/hdinsight-kafka-rest-proxy/)
