---
title: Apache Kafka proxy REST-Azure HDInsight
description: Saiba como executar operações de Apache Kafka usando um proxy REST do Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: a64d03ebe7c8bbb4cfa9c7bd63a678892250373d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482871"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir com clusters Apache Kafka no Azure HDInsight usando um proxy REST

O proxy REST do Kafka permite que você interaja com o cluster Kafka por meio de uma API REST sobre HTTP. Isso significa que os clientes do Kafka podem estar fora da rede virtual. Além disso, os clientes podem fazer chamadas HTTP simples para enviar e receber mensagens para o cluster Kafka, em vez de depender de bibliotecas Kafka.  

## <a name="background"></a>Segundo plano

### <a name="architecture"></a>Arquitetura

Sem um proxy REST, os clientes Kafka precisam estar na mesma VNet que o cluster Kafka ou uma VNet emparelhada. O proxy REST permite que você conecte produtores de dados ou consumidores localizados em qualquer lugar. A implantação do proxy REST cria um novo ponto de extremidade público para o cluster, que pode ser encontrado nas configurações do Portal.

Para obter a especificação completa das operações com suporte pela API, consulte [Apache Kafka API de proxy REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Segurança

O acesso ao proxy REST do Kafka é gerenciado com Azure Active Directory grupos de segurança. Consulte [gerenciar o acesso a aplicativos e recursos usando grupos de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)para obter mais informações.

Ao criar o cluster Kafka com o proxy REST habilitado, você fornecerá o grupo de segurança do AAD que deve ter acesso ao ponto de extremidade REST. Os clientes do Kafka (aplicativos) que precisam de acesso ao proxy REST devem ser registrados nesse grupo pelo proprietário do grupo. O proprietário do grupo pode fazer isso por meio do portal ou por meio do PowerShell.

Antes de fazer solicitações para o ponto de extremidade do proxy REST, o aplicativo cliente deve obter um token OAuth para verificar a associação do grupo de segurança certo. Para obter mais informações sobre como os tokens OAuth funcionam, consulte [autorizar o acesso a Azure Active Directory aplicativos Web usando o fluxo de concessão de código oauth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md). Para obter um exemplo de como buscar um token OAuth em Python, consulte [exemplo de aplicativo cliente](#client-application-sample)

Depois que o aplicativo cliente tiver o token OAuth, ele deverá passar esse token na solicitação HTTP feita para o proxy REST.

## <a name="prerequisites"></a>Pré-requisitos

1. Registar uma aplicação com o Azure AD. Os aplicativos cliente que você escreve para interagir com o proxy REST do Kafka usarão a ID e o segredo do aplicativo para autenticar no Azure.
1. Crie um grupo de segurança do Azure AD e adicione o aplicativo que você registrou com o Azure AD ao grupo de segurança. Esse grupo de segurança será usado para controlar quais aplicativos têm permissão para interagir com o proxy REST. Para obter mais informações sobre como criar grupos do Azure AD, consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster Kafka com o proxy REST habilitado

1. Durante o fluxo de trabalho de criação do cluster Kafka, na guia "segurança + rede", marque a opção "Habilitar proxy REST do Kafka".

     ![Habilitar o proxy REST do Kafka e selecionar grupo de segurança](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-enable.png)

1. Clique em **selecionar grupo de segurança**. Na lista de grupos de segurança, selecione o grupo de segurança que você deseja ter acesso ao proxy REST. Você pode usar a caixa de pesquisa para localizar o grupo de segurança apropriado. Clique no botão **selecionar** na parte inferior.

     ![Habilitar o proxy REST do Kafka e selecionar grupo de segurança](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-select-security-group.png)

1. Conclua as etapas restantes para criar o cluster, conforme descrito em [criar Apache Kafka cluster no Azure HDInsight usando portal do Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Depois que o cluster for criado, vá para as propriedades do cluster para registrar a URL do proxy REST do Kafka.

     ![exibir URL do proxy REST](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exemplo de aplicativo cliente

Você pode usar o código Python abaixo para interagir com o proxy REST no cluster Kafka. Esse código faz o seguinte:

1. Busca um token OAuth do Azure AD
1. Cria o tópico especificado
1. Envia mensagens para esse tópico
1. Consome mensagens desse tópico

Para obter mais informações sobre como obter tokens OAuth no Python, consulte [Python AuthenticationContext Class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Você pode ver um atraso enquanto os tópicos que não são criados ou excluídos por meio do proxy REST Kafka são refletidos lá. Esse atraso é devido à atualização do cache.

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
1. Instale as dependências do Python necessárias executando `pip3 install adal` e `pip install msrestazure`.
1. Modifique o código e atualize as seguintes propriedades para o seu ambiente:
    1.  *ID do locatário* – o locatário do Azure onde está sua assinatura.
    1.  *ID do cliente* – a ID do aplicativo que você registrou no grupo de segurança.
    1.  *Segredo do cliente* – o segredo para o aplicativo que você registrou no grupo de segurança
    1.  *Kafkarest_endpoint* – obtenha esse valor na guia "Propriedades" na visão geral do cluster, conforme descrito na [seção implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Ele deve estar no seguinte formato – `https://<clustername>-kafkarest.azurehdinsight.net`
3. Na linha de comando, execute o arquivo Python executando `python <filename.py>`

## <a name="next-steps"></a>Passos seguintes

* [Documentos de referência da API de proxy REST do Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Tutorial: usar as APIs de produtor e consumidor do Apache Kafka](apache-kafka-producer-consumer-api.md)