---
title: Apache Kafka proxy REST-Azure HDInsight
description: Saiba como executar operações de Apache Kafka usando um proxy REST do Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b53fc3af71ce872c9ca9f513139c8179fd4165ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031415"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir com clusters Apache Kafka no Azure HDInsight usando um proxy REST

O proxy REST do Kafka permite que você interaja com o cluster Kafka por meio de uma API REST sobre HTTP. Isso significa que os clientes do Kafka podem estar fora da rede virtual. Além disso, os clientes podem fazer chamadas HTTP simples para enviar e receber mensagens para o cluster Kafka, em vez de depender de bibliotecas Kafka.  

## <a name="background"></a>Segundo plano

### <a name="architecture"></a>Arquitetura

Sem um proxy REST, os clientes Kafka precisam estar na mesma VNet que o cluster Kafka ou uma VNet emparelhada. O proxy REST permite que você conecte produtores de dados ou consumidores localizados em qualquer lugar. A implantação do proxy REST cria um novo ponto de extremidade público para o cluster, que pode ser encontrado nas configurações do Portal.

Para obter a especificação completa das operações suportadas pela API, consulte [a Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Segurança

O acesso ao proxy REST do Kafka é gerenciado com Azure Active Directory grupos de segurança. Consulte gerir aplicativos e acesso a recursos utilizando grupos de [Diretórios Ativos Azure,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)para obter mais informações.

Ao criar o cluster Kafka com o proxy REST habilitado, você fornecerá o grupo de segurança do AAD que deve ter acesso ao ponto de extremidade REST. Os clientes do Kafka (aplicativos) que precisam de acesso ao proxy REST devem ser registrados nesse grupo pelo proprietário do grupo. O proprietário do grupo pode fazer isso por meio do portal ou por meio do PowerShell.

Antes de fazer solicitações para o ponto de extremidade do proxy REST, o aplicativo cliente deve obter um token OAuth para verificar a associação do grupo de segurança certo. Para obter mais informações sobre o funcionamento das fichas da OAuth, consulte [Autorizar o acesso às aplicações web do Azure Ative Directory utilizando o fluxo de subvenção de código OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md). Para um exemplo de pegar um token OAuth em python, consulte a amostra de [aplicação do Cliente](#client-application-sample)

Depois que o aplicativo cliente tiver o token OAuth, ele deverá passar esse token na solicitação HTTP feita para o proxy REST.

## <a name="prerequisites"></a>Pré-requisitos

1. Registar uma aplicação com o Azure AD. Os aplicativos cliente que você escreve para interagir com o proxy REST do Kafka usarão a ID e o segredo do aplicativo para autenticar no Azure.
1. Crie um grupo de segurança do Azure AD e adicione o aplicativo que você registrou com o Azure AD ao grupo de segurança. Esse grupo de segurança será usado para controlar quais aplicativos têm permissão para interagir com o proxy REST. Para obter mais informações sobre a criação de grupos DeA Azure, consulte [Criar um grupo básico e adicionar membros usando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster Kafka com o proxy REST habilitado

1. Durante o fluxo de trabalho de criação do cluster Kafka, na guia "segurança + rede", marque a opção "Habilitar proxy REST do Kafka".

     ![Habilitar o proxy REST do Kafka e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Clique em **Selecionar grupo de segurança**. Na lista de grupos de segurança, selecione o grupo de segurança que você deseja ter acesso ao proxy REST. Você pode usar a caixa de pesquisa para localizar o grupo de segurança apropriado. Clique no botão **Select** na parte inferior.

     ![Habilitar o proxy REST do Kafka e selecionar grupo de segurança](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Complete os passos restantes para criar o seu cluster como descrito no [cluster Create Apache Kafka no Azure HDInsight utilizando](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)o portal Azure .

1. Depois que o cluster for criado, vá para as propriedades do cluster para registrar a URL do proxy REST do Kafka.

     ![exibir URL do proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exemplo de aplicativo cliente

Você pode usar o código Python abaixo para interagir com o proxy REST no cluster Kafka. Esse código faz o seguinte:

1. Busca um token OAuth do Azure AD
1. Cria o tópico especificado
1. Envia mensagens para esse tópico
1. Consome mensagens desse tópico

Para obter fichas OAuth em python, consulte a [aula python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Você pode ver um atraso enquanto os tópicos que não são criados ou excluídos por meio do proxy REST Kafka são refletidos lá. Esse atraso é devido à atualização do cache.

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

Para usar o exemplo de código, siga estas etapas:

1. Salve o código de exemplo em um computador com o Python instalado.
1. Instale dependências de pitão necessárias executando `pip3 install adal` e `pip install msrestazure`.
1. Modifique o código e atualize as seguintes propriedades para o seu ambiente:
    1.  *ID do inquilino* – O inquilino Azure onde está a sua subscrição.
    1.  *ID do cliente* – O ID para a aplicação que registou no grupo de segurança.
    1.  *Segredo do Cliente* – O segredo para a aplicação que registou no grupo de segurança
    1.  *Kafkarest_endpoint* – obtenha este valor a partir do separador "propriedades" na visão geral do cluster, conforme descrito na [secção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Deve estar no seguinte formato – `https://<clustername>-kafkarest.azurehdinsight.net`
3. A partir da linha de comando, execute o ficheiro python executando `python <filename.py>`

## <a name="next-steps"></a>Passos seguintes

* [Documentos de referência da API proxy de Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Tutorial: Use o Produtor Apache Kafka e APIs de consumo](apache-kafka-producer-consumer-api.md)