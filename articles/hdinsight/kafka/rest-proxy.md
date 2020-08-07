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
ms.openlocfilehash: 660e200b673da53af1ee00e4de1e2ce3298e861d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876449"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interaja com os clusters Apache Kafka em Azure HDInsight usando um representante rest

Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de uma API REST sobre HTTP. Esta ação significa que os seus clientes Kafka podem estar fora da sua rede virtual. Os clientes podem fazer chamadas HTTP simples para o cluster Kafka, em vez de confiar em bibliotecas kafka. Este artigo irá mostrar-lhe como criar um cluster de proxy REST habilitado para Kafka. Também fornece um código de amostra que mostra como fazer chamadas para o proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para operações apoiadas pela API Kafka REST, consulte [hdInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Fundo

![Design de procuração kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações suportadas pela API, consulte [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>PONTO final de Proxy REST Proxy

A criação de um cluster HDInsight Kafka com proxy REST cria um novo ponto final público para o seu cluster, que pode encontrar no seu cluster HDInsight **Properties** no portal Azure.

### <a name="security"></a>Segurança

O acesso ao representante kafka REST é gerido com grupos de segurança do Azure Ative Directory. Ao criar o cluster Kafka, forneça ao grupo de segurança Azure AD acesso ao ponto final REST. Os clientes kafka que precisam de acesso ao representante rest deve ser registado neste grupo pelo proprietário do grupo. O proprietário do grupo pode registar-se através do Portal ou via PowerShell.

Para pedidos de ponto final de procuração REST, as aplicações do cliente devem obter um token OAuth. O símbolo é usado para verificar a adesão ao grupo de segurança. Encontre uma [amostra de aplicação do Cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. O pedido de cliente passa o token OAuth no pedido HTTP ao representante DO REST.

> [!NOTE]
> Consulte [a aplicação Manage e o acesso a recursos utilizando grupos Azure Ative Directory,](../../active-directory/fundamentals/active-directory-manage-groups.md)para saber mais sobre grupos de segurança AAD. Para obter mais informações sobre o funcionamento dos tokens da OAuth, consulte [o acesso autorizado às aplicações web do Azure Ative Directory utilizando o fluxo de concessão de código OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

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

     ![Ativar o proxy do Kafka REST e selecionar o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique **em Select Security Group**. Na lista de grupos de segurança, selecione o grupo de segurança que pretende ter acesso ao representante DO REST. Pode utilizar a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Selecione** na parte inferior.

     ![Ativar o proxy do Kafka REST e selecionar o grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete os passos restantes para criar o seu cluster como descrito no [cluster Create Apache Kafka em Azure HDInsight utilizando o portal Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

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
    |Kafkarest_endpoint|Obtenha este valor a partir do separador **Propriedades** na visão geral do cluster, conforme descrito na [secção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve estar no seguinte formato –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. A partir da linha de comando, execute o ficheiro python executando`sudo python3 <filename.py>`

Este código faz a seguinte ação:

1. Vai buscar um token OAuth da Azure AD.
1. Mostra como fazer um pedido ao representante kafka REST.

Para obter mais informações sobre a obtenção de fichas OAuth em python, consulte [a classe Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Você pode ver um atraso enquanto `topics` que não são criados ou apagados através do representante kafka REST são refletidos lá. Este atraso deve-se à atualização da cache.

```python
#Required python packages
#pip3 install msal

import msal

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

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Encontre abaixo outra amostra sobre como obter um token de Azure para o proxy REST usando um comando de caracóis. **Note que precisamos do `scope=https://hib.azurehdinsight.net/.default` especificado enquanto recebemos um símbolo.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passos seguintes

* [Documentos de referência da API de procuração kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
