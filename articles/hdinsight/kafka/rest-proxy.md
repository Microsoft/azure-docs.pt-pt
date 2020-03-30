---
title: Apache Kafka REST proxy - Azure HDInsight
description: Aprenda a executar operações apache kafka usando um proxy Kafka REST no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198910"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interaja com os clusters Apache Kafka em Azure HDInsight usando um proxy REST

Kafka REST Proxy permite-lhe interagir com o seu cluster Kafka através de um REST API sobre HTTP. Isto significa que os seus clientes Kafka podem estar fora da sua rede virtual. Além disso, os clientes podem fazer chamadas http simples para enviar e receber mensagens para o cluster Kafka, em vez de confiar em bibliotecas Kafka. Este tutorial irá mostrar-lhe como criar um grupo de kafka ativado por REST e fornecer um código de amostra que mostre como fazer chamadas para o proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para obter a especificação completa das operações suportadas pela API Kafka REST, consulte [hDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Segundo plano

![Kafka REST proxy arquitetura](./media/rest-proxy/rest-proxy-architecture.png)

Para obter a especificação completa das operações suportadas pela API, consulte [a Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto final do Proxy REST

Criar um cluster HDInsight Kafka com proxy REST cria um novo ponto final público para o seu cluster, que pode encontrar no seu cluster HDInsight "Propriedades" no portal Azure.

### <a name="security"></a>Segurança

O acesso ao proxy Kafka REST é gerido com grupos de segurança do Diretório Ativo Azure. Ao criar o cluster Kafka com o proxy REST ativado, você fornecerá o grupo de segurança Azure Ative Diretório que deve ter acesso ao ponto final REST. Os clientes Kafka (aplicações) que necessitem de acesso ao proxy REST devem ser registados neste grupo pelo proprietário do grupo. O proprietário do grupo pode fazê-lo através do Portal ou via Powershell.

Antes de fazer pedidos ao ponto final do proxy REST, o pedido do cliente deve obter um token OAuth para verificar a adesão ao grupo de segurança certo. Por favor, encontre uma amostra de [aplicação do Cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. Uma vez que o pedido do cliente tenha o símbolo OAuth, eles devem passar esse símbolo no pedido HTTP feito ao representante REST.

> [!NOTE]  
> Consulte gerir aplicativos e acesso a recursos utilizando grupos de [Diretórios Ativos Azure,](../../active-directory/fundamentals/active-directory-manage-groups.md)para saber mais sobre grupos de segurança AAD. Para obter mais informações sobre o funcionamento das fichas da OAuth, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Registar uma aplicação com o Azure AD. As aplicações de clientes que escrever para interagir com o representante do Kafka REST usarão o ID e o segredo desta aplicação para autenticar o Azure.
1. Crie um grupo de segurança Azure AD e adicione a aplicação que registou com a Azure AD ao grupo de segurança. Este grupo de segurança será utilizado para controlar quais as aplicações autorizadas a interagir com o representante do REST. Para obter mais informações sobre a criação de grupos DeA Azure, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster Kafka com proxy REST habilitado

1. Durante o fluxo de trabalho de criação de cluster kafka, no separador "Security + networking", verifique a opção "Enable Kafka REST proxy".

     ![Ativar o proxy Kafka REST e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique em **Selecionar grupo de segurança**. A partir da lista de grupos de segurança, selecione o grupo de segurança que pretende ter acesso ao proxy REST. Pode usar a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Select** na parte inferior.

     ![Ativar o proxy Kafka REST e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete os passos restantes para criar o seu cluster como descrito no [cluster Create Apache Kafka no Azure HDInsight utilizando](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)o portal Azure .

1. Uma vez criado o cluster, vá às propriedades do cluster para registar o URL proxy Kafka REST.

     ![ver URL de procuração REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Amostra de pedido do cliente

Pode usar o código python abaixo para interagir com o proxy REST no seu cluster Kafka. Para utilizar a amostra de código, siga estes passos:

1. Guarde o código da amostra numa máquina com python instalada.
1. Instale dependências de pitão necessárias executando `pip3 install adal` e `pip install msrestazure`.
1. Modifique a secção de *códigoConfigure estas propriedades* e atualize as seguintes propriedades para o seu ambiente:
    1.  *ID do inquilino* – O inquilino Azure onde está a sua subscrição.
    1.  *ID do cliente* – O ID para a aplicação que registou no grupo de segurança.
    1.  *Segredo do Cliente* – O segredo para a aplicação que registou no grupo de segurança
    1.  *Kafkarest_endpoint* – obtenha este valor a partir do separador "propriedades" na visão geral do cluster, conforme descrito na [secção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve estar no seguinte formato –`https://<clustername>-kafkarest.azurehdinsight.net`
3. A partir da linha de comando, execute o ficheiro python executando`python <filename.py>`

Este código faz o seguinte:

1. Fetches a OAuth token from Azure AD
1. Mostra como fazer um pedido ao representante do KAFKA REST

Para obter fichas OAuth em python, consulte a [aula python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Pode ver um atraso enquanto os tópicos que não são criados ou apagados através do representante kafka REST são refletidos lá. Este atraso deve-se à atualização da cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

## <a name="next-steps"></a>Passos seguintes

* [Documentos de referência da API proxy de Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
