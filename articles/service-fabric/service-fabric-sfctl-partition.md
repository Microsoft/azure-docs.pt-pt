---
title: Azure Service Fabric CLI-partição sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gerir divisórias para um serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905852"
---
# <a name="sfctl-partition"></a>sfctl partition
Consultar e gerir divisórias para qualquer serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| perda de dados | Esta API induzirá a perda de dados para a partição especificada. |
| estado de perda de dados | Obtém o progresso de uma operação de perda de dados de partição iniciada usando a API StartDataLoss. |
| saúde | Obtém a saúde da divisória de tecido de serviço especificada. |
| informações | Obtém a informação sobre uma partição de Tecido de Serviço. |
| list | Obtém a lista de divisórias de um serviço de Tecido de Serviço. |
| carregar | Obtém a informação de carga da divisória de tecido de serviço especificada. |
| reset de carga | Repõe a carga atual de uma divisória de tecido de serviço. |
| perda de quórum | Induz a perda de quórum para uma partição de serviço sinuoso. |
| quórum-perda-estado | Obtém o progresso de uma operação de perda de quórum numa partição iniciada com a API StartQuorumLoss. |
| recuperar | Indica ao cluster De Serviço Tecido que deve tentar recuperar uma partição específica que está atualmente presa na perda de quórum. |
| recuperar-tudo | Indica ao cluster Service Fabric que deve tentar recuperar quaisquer serviços (incluindo serviços de sistema) que estejam atualmente presos em perda de quórum. |
| relatório-saúde | Envia um relatório de saúde sobre a partição de Tecido de Serviço. |
| restart | Esta API reiniciará algumas ou todas as réplicas ou instâncias da divisória especificada. |
| reinício do estado | Obtém o progresso de uma operação PartitionRestart iniciada com startPartitionRestart. |
| svc-name | Obtém o nome do serviço de tecido de serviço para uma partição. |

## <a name="sfctl-partition-data-loss"></a>sfctl partição data-perda
Esta API induzirá a perda de dados para a partição especificada.

Irá desencadear uma chamada para a API OnDataLossAsync da partição.  Esta API induzirá a perda de dados para a partição especificada. Irá desencadear uma chamada para a API OnDataLoss da partição. A perda real de dados dependerá do DataLossMode especificado.
- DataLoss parcial: Apenas um quórum de réplicas é removido e o OnDataLoss é desencadeado para a partição, mas a perda real de dados depende da presença de replicação a bordo.  
- FullDataLoss: Todas as réplicas são removidas, pelo que todos os dados são perdidos e o OnDataLoss é desencadeado. Esta API só deve ser chamada com um serviço imponente como alvo. Chamar a esta API um serviço de sistema como o alvo não é aconselhado.

> [!NOTE]   
> Uma vez que esta API tenha sido chamada, não pode ser invertida. Chamar a CancelOperation só vai parar a execução e limpar o estado do sistema interno. Não restaurará os dados se o comando tiver progredido o suficiente para causar a perda de dados. Ligue para a API GetDataLossProgress com o mesmo OperationId para devolver informações sobre a operação iniciada com esta API.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --modo perda de dados [Obrigatório] | Este enum é passado para a API StartDataLoss para indicar que tipo de perda de dados induzir. |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl divisão data-perda-status
Obtém o progresso de uma operação de perda de dados de partição iniciada usando a API StartDataLoss.

O progresso de uma operação de perda de dados começou com o StartDataLoss, utilizando o OperationId.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-health"></a>sfctl saúde de partição
Obtém a saúde da divisória de tecido de serviço especificada.

Use EventsHealthStateFilter para filtrar a recolha de eventos de saúde relatados no serviço com base no estado de saúde. Utilize réplicasHealthStateFilter para filtrar a coleção de objetos ReplicaHealthState na divisória. Se especificar uma partição que não existe na loja de saúde, este pedido devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -excluir-estatísticas de saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de entidades infantis no estado de saúde Ok, Warning e Error. |
| --réplicas-filtro estado-saúde | Permite filtrar a coleção de objetos ReplicaHealthState na partição. O valor pode ser obtido a partir de membros ou operações bitwise em membros do HealthStateFilter. Só serão devolvidas réplicas que correspondam ao filtro. Todas as réplicas serão usadas para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas serão devolvidas. Os valores do Estado são uma enumeração baseada na bandeira, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador "OR" bitwise. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) serão devolvidos. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-info"></a>informação de partição sfctl
Obtém a informação sobre uma partição de Tecido de Serviço.

Obtém a informação sobre a partição especificada. A resposta inclui o ID de partição, informações do esquema de partição, chaves suportadas pela partilha, estado, saúde e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-list"></a>lista de partição sfctl
Obtém a lista de divisórias de um serviço de Tecido de Serviço.

A resposta inclui o ID de partição, informações do esquema de partição, chaves suportadas pela partilha, estado, saúde e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-load"></a>carga de partição sfctl
Obtém a informação de carga da divisória de tecido de serviço especificada.

Devolve informações sobre a carga de uma divisória especificada. A resposta inclui uma lista de relatórios de carga para uma partição de tecido de serviço. Cada relatório inclui o nome métrico de carga, o valor e a última vez reportada na UTC.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-load-reset"></a>sfctl divisória load-reset
Repõe a carga atual de uma divisória de tecido de serviço.

Repõe a carga atual de uma divisória de tecido de serviço na carga predefinida para o serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partição quórum-perda
Induz a perda de quórum para uma partição de serviço sinuoso.

Esta API é útil para uma situação temporária de perda de quórum no seu serviço. Ligue para a API GetQuorumLossProgress com o mesmo OperationId para devolver informações sobre a operação iniciada com esta API. Isto só pode ser chamado em serviços de persistência audac (HasPersistedState==true).  Não utilize esta API em serviços apátridas ou apenas em memória.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --quórum-perda-duração [Obrigatório] | O tempo para o qual a partição será mantida em perda de quórum.  Isto deve ser especificado em segundos. |
| --quórum-perda-modo [Obrigatório] | Este enum é passado para a API StartQuorumLoss para indicar que tipo de perda de quórum induzir. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl divisória quorum-loss-status
Obtém o progresso de uma operação de perda de quórum numa partição iniciada com a API StartQuorumLoss.

O progresso de uma operação de perda de quórum começou com o StartQuorumLoss, utilizando o OperationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-recover"></a>partição sfctl recuperar
Indica ao cluster De Serviço Tecido que deve tentar recuperar uma partição específica que está atualmente presa na perda de quórum.

Esta operação só deve ser executada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-recover-all"></a>partição sfctl recuperar-tudo
Indica ao cluster Service Fabric que deve tentar recuperar quaisquer serviços (incluindo serviços de sistema) que estejam atualmente presos em perda de quórum.

Esta operação só deve ser executada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-report-health"></a>sfctl partição relatório-saúde
Envia um relatório de saúde sobre a partição de Tecido de Serviço.

Informa o estado de saúde da divisória especificada do Tecido de Serviço. O relatório deve conter as informações sobre a origem do relatório de saúde e dos bens em que é comunicado. O relatório é enviado para uma Partição de Gateway de Tecido de Serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, verifique se o relatório aparece na secção de eventos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir que a flexibilidade do repórter categorize a condição do Estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncado]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que ocorreu a truncação. Note que quando truncado, a descrição tem menos de 4096 caracteres da cadeia original. |
| -imediatamente | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Immediate for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o Gateway HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se imediatamente for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do HTTP Gateway. Portanto, será loteizado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar mensagens de relatórios de saúde para a loja de saúde, bem como processamento de relatórios de saúde. Por defeito, os relatórios não são enviados imediatamente. |
| -remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdade, o relatório é removido da loja de saúde após o seu termo. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoverQuando Expirado falso (predefinido). Desta forma, é que o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por engano quando o relatório de saúde expira. Isto sinaliza a entidade como estando em estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --timeout -t | Padrão\: 60. |
| -ttl | A duração da qual este relatório de saúde é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportam periodicamente, devem enviar relatórios com maior frequência do que o tempo de vida. Se os clientes reportarem sobre a transição, podem definir o tempo para viver em infinito. Quando expira a hora de viver, o evento de saúde que contém as informações de saúde é removido da loja de saúde, se removeWhenExpired for verdadeiro, ou avaliado por erro, se removerQuando Expirado falso. Se não for especificado, o tempo para viver não corresponde ao valor infinito. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-restart"></a>reinício da partição sfctl
Esta API reiniciará algumas ou todas as réplicas ou instâncias da divisória especificada.

Esta API é útil para testar o failover. Se utilizado para direcionar uma divisória de serviço apátrida, o RestartPartitionMode deve ser AllReplicasOrInstances. Ligue para a API GetPartitionRestartProgress utilizando o mesmo OperationId para obter o progresso.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --reiniciar o modo de partição [Obrigatório] | Descreva quais divisórias para reiniciar. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart status
Obtém o progresso de uma operação PartitionRestart iniciada com startPartitionRestart.

Obtém o progresso de um PartitionRestart iniciado com startPartitionRestart utilizando o OperationId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -operação-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
Obtém o nome do serviço de tecido de serviço para uma partição.

Obtém o nome do serviço para a partição especificada. Um erro de 404 é devolvido se o ID da partição não existir no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
