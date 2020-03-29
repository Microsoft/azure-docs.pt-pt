---
title: Ingerir dados de Logstash para Azure Data Explorer
description: Neste artigo, aprende-se a ingerir (carregar) dados no Azure Data Explorer a partir de Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66494547"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Ingerir dados de Logstash para Azure Data Explorer

[O Logstash](https://www.elastic.co/products/logstash) é um pipeline de processamento de dados de código aberto, do lado do servidor, que ingere dados de muitas fontes simultaneamente, transforma os dados e, em seguida, envia os dados para o seu "esconderijo" favorito. Neste artigo, enviará esses dados para o Azure Data Explorer, que é um serviço de exploração de dados rápido e altamente escalável para dados de registo e telemetria. Inicialmente, criará uma tabela e um mapeamento de dados num cluster de teste e, em seguida, direciona o Logstash para enviar dados para a tabela e validar os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um cluster de teste do Explorador de Dados Azure [e base de dados](create-cluster-database-portal.md)
* Instruções de [instalação](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) da versão logstash 6+

## <a name="create-a-table"></a>Criar uma tabela

Depois de ter um cluster e uma base de dados, é hora de criar uma mesa.

1. Execute o seguinte comando na janela de consulta da sua base de dados para criar uma tabela:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Executar o seguinte comando para `logs` confirmar que a nova mesa foi criada e que está vazia:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Criar um mapeamento

O mapeamento é usado pelo Azure Data Explorer para transformar os dados de entrada no esquema da tabela alvo. O seguinte comando cria `basicmsg` um novo mapeamento chamado que extrai propriedades `path` do json `column`de entrada, como notado pelo e as produz para o .

Executar o seguinte comando na janela de consulta:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Instale o plugin de saída logstash

O plugin de saída Logstash comunica com o Azure Data Explorer e envia os dados para o serviço.
Executar o seguinte comando dentro do diretório raiz logstash para instalar o plugin:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Configure o Logstash para gerar um conjunto de dados de amostras

O logstash pode gerar eventos de amostra que podem ser usados para testar um gasoduto de ponta a ponta.
Se já estiver a utilizar o Logstash e tiver acesso ao seu próprio fluxo de eventos, salte para a secção seguinte. 

> [!NOTE]
> Se estiver a utilizar os seus próprios dados, altere a tabela e mapeie objetos definidos nos passos anteriores.

1. Editar um novo ficheiro de texto que contenha as definições de pipeline necessárias (utilizando vi):

    ```sh
    vi test.conf
    ```

1. Colar as seguintes definições que dirão ao Logstash para gerar 1000 eventos de teste:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Esta configuração `stdin` também inclui o plugin de entrada que lhe permitirá escrever mais mensagens por si mesmo (certifique-se de usar o *Enter* para submetê-las no pipeline).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Configure o Logstash para enviar dados para o Azure Data Explorer

Colá-lo as seguintes definições no mesmo ficheiro de config utilizado no passo anterior. Substitua todos os espaços reservados pelos valores relevantes para a sua configuração. Para mais informações, consulte [Criar uma Aplicação AAD](/azure/kusto/management/access-control/how-to-provision-aad-app). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Nome do Parâmetro | Descrição |
| --- | --- |
| **caminho** | O plugin de Logstash escreve eventos em ficheiros temporários antes de enviá-los para o Azure Data Explorer. Este parâmetro inclui um caminho onde os ficheiros devem ser escritos e uma expressão de tempo para a rotação de ficheiros para desencadear um upload para o serviço Azure Data Explorer.|
| **ingest_url** | O ponto final de Kusto para comunicação relacionada com ingestão.|
| **app_id,** **app_key**e **app_tenant**| Credenciais necessárias para ligar ao Azure Data Explorer. Certifique-se de usar uma aplicação com privilégios ingerir. |
| **base de dados**| Nome da base de dados para colocar eventos. |
| **tabela** | Nome de mesa de destino para colocar eventos. |
| **mapeamento** | O mapeamento é usado para mapear uma corda json evento de entrada no formato de linha correta (define em que propriedade vai em que coluna). |

## <a name="run-logstash"></a>Executar Logstash

Estamos agora prontos para executar logstash e testar as nossas definições.

1. No diretório raiz de Logstash, executar o seguinte comando:

    ```sh
    bin/logstash -f test.conf
    ```

    Deve ver informações impressas no ecrã e, em seguida, as 1000 mensagens geradas pela nossa configuração de amostra. Neste ponto, também pode introduzir mais mensagens manualmente.

1. Após alguns minutos, faça a seguinte consulta do Data Explorer para ver as mensagens na tabela que definiu:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Selecione Ctrl+C para sair do Logstash

## <a name="clean-up-resources"></a>Limpar recursos

Execute o seguinte comando na `logs` sua base de dados para limpar a tabela:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas](write-queries.md)