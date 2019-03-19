---
title: 'Início rápido: Ingestão de dados do Logstash para o Explorador de dados do Azure'
description: Neste início rápido, irá aprender a ingerir dados de (carga) no Explorador de dados do Azure do Logstash
services: data-explorer
author: tamirkamara
ms.author: takamara
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 1/14/2019
ms.openlocfilehash: 01115524ae3890c1d6a0220bcf89e86090d148cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078878"
---
# <a name="quickstart-ingest-data-from-logstash-to-azure-data-explorer"></a>Início rápido: Ingestão de dados do Logstash para o Explorador de dados do Azure

[Logstash](https://www.elastic.co/products/logstash) é uma código-fonte aberto, pipeline de processamento de dados do lado do servidor que ingere dados de várias fontes em simultâneo, transforma os dados e, em seguida, envia os dados para os seus favoritos "stash". Neste início rápido, vai enviar esses dados para o Explorador de dados do Azure, que é um serviço de exploração de dados rápida e altamente escalável para dados de telemetria e de registo. Irá criar inicialmente uma tabela e o mapeamento de dados num cluster de teste e, em seguida, direcionar o Logstash para enviar dados para a tabela e validar os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Um Explorador de dados do Azure [cluster e a base de dados de teste](create-cluster-database-portal.md)
* O Logstash versão 6 + [instruções de instalação](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>Criar uma tabela

Depois de ter um cluster e uma base de dados, está na altura de criar uma tabela.

1. Execute o seguinte comando na sua janela de consulta de base de dados para criar uma tabela:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Execute o seguinte comando para confirmar que a nova tabela `logs` foi criada e que está vazia:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Criar um mapeamento

Mapeamento é utilizado pelo Explorador de dados do Azure para transformar os dados de entrada para o esquema de tabela de destino. O comando seguinte cria um novo mapeamento com o nome `basicmsg` que extrai as propriedades do json de entrada como observado, o `path` e devolve-os para o `column`.

Execute o seguinte comando na janela de consulta:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Instalar o plug-in do Logstash saída

O plug-in do Logstash saída comunica com o Explorador de dados do Azure e envia os dados para o serviço.
Execute o seguinte comando dentro do diretório de raiz do Logstash para instalar o plug-in:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Configurar o Logstash para gerar um conjunto de dados de exemplo

Logstash pode gerar eventos de exemplo que podem ser utilizados para testar um pipeline ponto a ponto.
Se já estiver a utilizar o Logstash e ter acesso ao seu próprio fluxo de eventos, avance para a secção seguinte. 

> [!NOTE]
> Se estiver a utilizar os seus dados, altere os objetos de mapeamento e de tabela definidos nos passos anteriores.

1. Edite um novo ficheiro de texto que irá conter as definições do pipeline necessários (através de vi):

    ```sh
    vi test.conf
    ```

1. Cole as seguintes definições informará o Logstash para gerar eventos de teste de 1000:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Esta configuração também inclui a `stdin` entrada Plug-in que irá permitir-lhe escrever mais mensagens sozinho (Certifique-se de que utiliza *Enter* para enviá-los no pipeline).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Configurar o Logstash para enviar dados para o Explorador de dados do Azure

Cole as seguintes definições no mesmo ficheiro de configuração utilizado no passo anterior. Substitua todos os marcadores de posição pelos valores relevantes para a sua configuração. Para obter mais informações, consulte [criar uma aplicação do AAD](/azure/kusto/management/access-control/how-to-provision-aad-app). 

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
| **path** | O plug-in do Logstash grava eventos em ficheiros temporários antes de os enviar para o Explorador de dados do Azure. Este parâmetro inclui um caminho onde os ficheiros devem ser escritos e uma expressão de tempo para a rotação de ficheiro acionar um carregamento para o serviço do Explorador de dados do Azure.|
| **ingest_url** | O ponto final de Kusto para relacionados com a ingestão de comunicação.|
| **app_id**,  **app_key**, and **app_tenant**| Credenciais necessárias para ligar ao Explorador de dados do Azure. Certifique-se de que usar um aplicativo com privilégios de ingestão. |
| **database**| Nome de base de dados a colocar eventos. |
| **table** | Nome da tabela de destino para colocar os eventos. |
| **mapping** | Mapeamento é usado para mapear uma cadeia de json de eventos recebida para o formato de linha correto (define qual propriedade entra em qual coluna). |

## <a name="run-logstash"></a>Execute o Logstash

Agora estamos prontos para executar o Logstash e nossas configurações de teste.

1. No diretório de raiz do Logstash, execute o seguinte comando:

    ```sh
    bin/logstash -f test.conf
    ```

    Deverá ver informações impresso na tela e, em seguida, as 1 000 mensagens geradas pela nossa configuração de exemplo. Neste ponto, também pode introduzir mais mensagens manualmente.

1. Após alguns minutos, execute a seguinte consulta de Data Explorer para ver as mensagens na tabela que definiu:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Selecione Ctrl + C para sair do Logstash

## <a name="clean-up-resources"></a>Limpar recursos

Execute o seguinte comando na sua base de dados para limpar o `logs` tabela:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escrever consultas](write-queries.md)