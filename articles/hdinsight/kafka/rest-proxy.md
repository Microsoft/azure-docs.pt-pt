---
title: Apache Kafka REST proxy - Azure HDInsight
description: Aprenda a fazer operações apache kafka usando um proxy Kafka REST no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: has-adal-ref
ms.date: 04/03/2020
ms.openlocfilehash: 9b5771197c3e2de109af1a3b3475ab28fcbd6453
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647753"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interaja com os clusters Apache Kafka em Azure HDInsight usando um proxy REST

Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de um REST API sobre HTTP. Esta ação significa que os seus clientes Kafka podem estar fora da sua rede virtual. Os clientes podem fazer chamadas http simples para o cluster Kafka, em vez de confiar em bibliotecas Kafka. Este artigo irá mostrar-lhe como criar um grupo de kafka ativado por proxy REST. Também fornece um código de amostra que mostra como fazer chamadas para o proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para operações apoiadas pela Kafka REST API, consulte [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Fundo

![Projeto de procuração kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações suportadas pela API, consulte [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto final do Proxy REST

Criar um cluster HDInsight Kafka com proxy REST cria um novo ponto final público para o seu cluster, que pode encontrar no seu cluster HDInsight **Propriedades** no portal Azure.

### <a name="security"></a>Segurança

O acesso ao proxy Kafka REST é gerido com grupos de segurança do Diretório Ativo Azure. Ao criar o cluster Kafka, forneça ao grupo de segurança Azure AD acesso final point. Os clientes kafka que necessitem de acesso ao proxy REST devem ser registados neste grupo pelo proprietário do grupo. O proprietário do grupo pode registar-se através do Portal ou via PowerShell.

Para pedidos de ponto final de procuração REST, os pedidos de cliente devem obter um token OAuth. O símbolo é usado para verificar a adesão ao grupo de segurança. Encontre uma amostra de [aplicação do Cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. A aplicação do cliente passa o token OAuth no pedido http ao proxy REST.

> [!NOTE]
> Consulte gerir aplicativos e acesso a recursos utilizando grupos de [Diretórios Ativos Azure,](../../active-directory/fundamentals/active-directory-manage-groups.md)para saber mais sobre grupos de segurança AAD. Para obter mais informações sobre o funcionamento das fichas da OAuth, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Registar uma aplicação com o Azure AD. As aplicações de clientes que escrever para interagir com o representante do Kafka REST usarão o ID e o segredo desta aplicação para autenticar o Azure.

1. Crie um grupo de segurança Azure AD. Adicione a aplicação que registou com a Azure AD ao grupo de segurança como **membro** do grupo. Este grupo de segurança será utilizado para controlar quais as aplicações autorizadas a interagir com o representante do REST. Para obter mais informações sobre a criação de grupos DeA Azure, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Validar que o grupo é de tipo **Segurança**.
    ![Grupo de Segurança](./media/rest-proxy/rest-proxy-group.png)

    Validar esta aplicação é membro do Grupo.
    ![Verifique a adesão](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster Kafka com proxy REST habilitado

Os passos abaixo utilizam o portal Azure. Por exemplo, utilizando o Azure CLI, consulte [Create Apache Kafka REST proxy cluster utilizando o Azure CLI](tutorial-cli-rest-proxy.md).

1. Durante o fluxo de trabalho de criação de cluster Kafka, no separador **de rede Security + networking,** verifique a opção proxy Enable Kafka **REST.**

     ![Ativar o proxy Kafka REST e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique em **Selecionar grupo de segurança**. A partir da lista de grupos de segurança, selecione o grupo de segurança que pretende ter acesso ao proxy REST. Pode usar a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Select** na parte inferior.

     ![Ativar o proxy Kafka REST e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete os passos restantes para criar o seu cluster como descrito no [cluster Create Apache Kafka no Azure HDInsight utilizando](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)o portal Azure .

1. Uma vez criado o cluster, vá às propriedades do cluster para registar o URL proxy Kafka REST.

     ![ver URL de procuração REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Amostra de pedido do cliente

Pode usar o código python abaixo para interagir com o proxy REST no seu cluster Kafka. Para utilizar a amostra de código, siga estes passos:

1. Guarde o código da amostra numa máquina com python instalada.
1. Instale dependências de pitão necessárias executando `pip3 install msal` .
1. Modifique a secção de **códigoConfigure estas propriedades** e atualize as seguintes propriedades para o seu ambiente:

    |Propriedade |Descrição |
    |---|---|
    |ID do inquilino|O inquilino Azure onde está a sua assinatura.|
    |ID de Cliente|A identificação do pedido que registou no grupo de segurança.|
    |Segredo do Cliente|O segredo para o pedido que registou no grupo de segurança.|
    |Kafkarest_endpoint|Obtenha este valor do separador **Propriedades** na visão geral do cluster, conforme descrito na [secção de implementação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve estar no seguinte formato –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. A partir da linha de comando, execute o ficheiro python executando`sudo python3 <filename.py>`

Este código faz a seguinte ação:

1. Vai buscar um símbolo da OAuth da Azure AD.
1. Mostra como fazer um pedido ao representante do KAFKA REST.

Para obter fichas OAuth em python, consulte a [aula python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Pode ver-se um atraso enquanto `topics` não forem criados ou apagados através do representante do KAFKA REST. Este atraso deve-se à atualização da cache.

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

Encontre abaixo outra amostra sobre como obter um símbolo de Azure para proxy REST usando um comando de caracóis. **Reparem que precisamos do `scope=https://hib.azurehdinsight.net/.default` especificado enquanto recebemos um símbolo.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Passos seguintes

* [Documentos de referência da API proxy de Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
