---
title: Azure Service Fabric CLI-sfctl partição
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para gerir divisórias para um serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257092"
---
# <a name="sfctl-partition"></a>sfctl partition
Consultar e gerir divisórias para qualquer serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| perda de dados | Esta API induzirá a perda de dados para a partição especificada. |
| estado de perda de dados | Obtém-se o progresso de uma operação de perda de dados de partição iniciada usando a API StartDataLoss. |
| Saúde | Obtém a saúde da partição de tecido de serviço especificado. |
| informações | Obtém a informação sobre uma divisória de Tecido de Serviço. |
| lista | Obtém a lista de divisórias de um serviço de Tecido de Serviço. |
| carregar | Obtém a informação de carga da partição de tecido de serviço especificado. |
| reset de carga | Reinicia a carga atual de uma divisória de tecido de serviço. |
| quórum-perda | Induz a perda de quórum para uma determinada divisão de serviço. |
| quórum-perda-status | Obtém-se o progresso de uma operação de perda de quórum numa partição iniciada usando a API StartQuorumLoss. |
| recuperar | Indica ao cluster de Tecido de Serviço que deve tentar recuperar uma divisória específica que está atualmente presa na perda de quórum. |
| recuperar tudo | Indica ao cluster de Tecido de Serviço que deve tentar recuperar quaisquer serviços (incluindo serviços de sistema) que estão atualmente presos em perda de quórum. |
| relatório-saúde | Envia um relatório de saúde sobre a partição do Tecido de Serviço. |
| restart | Esta API reiniciará algumas ou todas as réplicas ou instâncias da partição especificada. |
| reinício do estado | Obtém-se o progresso de uma operação PartitionRestart começou a usar startPartitionRestart. |
| svc-nome | Obtém o nome do serviço de tecido de serviço para uma partição. |

## <a name="sfctl-partition-data-loss"></a>perda de dados de partição sfctl
Esta API induzirá a perda de dados para a partição especificada.

Irá desencadear uma chamada para a API OnDataLossAsync da partição.  Esta API induzirá a perda de dados para a partição especificada. Irá desencadear uma chamada para a API OnDataLoss da partição. A perda de dados real dependerá do DataLossMode especificado.
- DataLoss Parciais: Apenas um quórum de réplicas são removidos e o OnDataLoss é ativado para a partição, mas a perda de dados real depende da presença de replicação a bordo.  
- FullDataLoss: Todas as réplicas são removidas, portanto, todos os dados são perdidos e o OnDataLoss é ativado. Esta API só deve ser chamada com um serviço estatal como alvo. Chamar esta API com um serviço de sistema como o alvo não é aconselhável.

> [!NOTE]   
> Uma vez que esta API tenha sido chamada, não pode ser revertida. Chamar cancelOperation só vai parar a execução e limpar o estado interno do sistema. Não irá restaurar os dados se o comando tiver progredido o suficiente para causar perda de dados. Ligue para a API GetDataLossProgress com a mesma OperationId para devolver informações sobre a operação iniciada com esta API.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --modo de perda de dados [Obrigatório] | Este enum é passado para a API StartDataLoss para indicar que tipo de perda de dados induzir. |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partição dados-perda-estado
Obtém-se o progresso de uma operação de perda de dados de partição iniciada usando a API StartDataLoss.

Obtém-se o progresso de uma operação de perda de dados iniciada com o StartDataLoss, utilizando o OperationId.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-health"></a>sfctl saúde de partição
Obtém a saúde da partição de tecido de serviço especificado.

Use EventosHealthStateFilter para filtrar a recolha de eventos de saúde relatados no serviço com base no estado de saúde. Utilize replicasHealthStateFilter para filtrar a coleção de objetos ReplicaHealthState na partição. Se especificar uma divisória que não existe na loja de saúde, este pedido devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --excluir-estatísticas da saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de crianças entidades no estado de saúde Ok, Aviso e Erro. |
| --réplicas-saúde-estado-filtro | Permite filtrar a coleção de objetos ReplicaHealthState na partição. O valor pode ser obtido a partir de membros ou operações mordendo em membros do HealthStateFilter. Apenas réplicas que correspondam ao filtro serão devolvidas. Todas as réplicas serão usadas para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas serão devolvidas. Os valores do Estado são enumerações baseadas em bandeiras, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) serão devolvidos. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-info"></a>sfctl informação de partição
Obtém a informação sobre uma divisória de Tecido de Serviço.

Obtém a informação sobre a partição especificada. A resposta inclui o ID de partição, informações do esquema de partição, chaves apoiadas pela partição, estado, saúde e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-list"></a>lista de divisórias sfctl
Obtém a lista de divisórias de um serviço de Tecido de Serviço.

A resposta inclui o ID de partição, informações do esquema de partição, chaves apoiadas pela partição, estado, saúde e outros detalhes sobre a partição.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-load"></a>sfctl carga de partição
Obtém a informação de carga da partição de tecido de serviço especificado.

Devolve informações sobre a carga de uma partição especificada. A resposta inclui uma lista de relatórios de carga para uma partição de Tecido de Serviço. Cada relatório inclui o nome, valor e última vez reportado na UTC.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-load-reset"></a>sfctl partição reset carga
Reinicia a carga atual de uma divisória de tecido de serviço.

Reinicia a carga atual de uma divisória de tecido de serviço à carga predefinida para o serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partição quórum-perda
Induz a perda de quórum para uma determinada divisão de serviço.

Esta API é útil para uma situação temporária de perda de quórum no seu serviço. Ligue para a API GetQuorumLossProgress com a mesma OperationId para devolver informações sobre a operação iniciada com esta API. Isto só pode ser chamado em serviços de persistência estatal (HasPersistedState=true).  Não utilize esta API em serviços apátridas ou serviços de memória só.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --duração do quórum-perda [Necessário] | O tempo para o qual a partição será mantida em perda de quórum.  Isto deve ser especificado em segundos. |
| --modo de perda de quórum [Obrigatório] | Este enum é passado para a API startQuorumLoss para indicar que tipo de perda de quórum induzir. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partição quórum-perda-status
Obtém-se o progresso de uma operação de perda de quórum numa partição iniciada usando a API StartQuorumLoss.

Inicia-se o progresso de uma operação de perda de quórum com startQuorumLoss, utilizando a OperaçãoId fornecida.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-recover"></a>recuperação de partição sfctl
Indica ao cluster de Tecido de Serviço que deve tentar recuperar uma divisória específica que está atualmente presa na perda de quórum.

Esta operação só deve ser efetuada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-recover-all"></a>sfctl partição recuperar-todos
Indica ao cluster de Tecido de Serviço que deve tentar recuperar quaisquer serviços (incluindo serviços de sistema) que estão atualmente presos em perda de quórum.

Esta operação só deve ser efetuada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-report-health"></a>relatório de partição sfctl-saúde
Envia um relatório de saúde sobre a partição do Tecido de Serviço.

Relatórios de estado de saúde da partição de tecido de serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e sobre os bens em que é comunicado. O relatório é enviado para uma partilha de porta de entrada de tecido de serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para verificar se o relatório foi aplicado na loja de saúde, verifique se o relatório aparece na secção de eventos.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --saúde-propriedade [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir ao repórter flexibilidade para categorizar a condição do estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem \: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa um texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que a truncação ocorreu. Note que, quando truncado, a descrição tem menos de 4096 caracteres da corda original. |
| --imediata | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Imediato for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o GATEWAY HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se o Immediate for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do GATEWAY HTTP. Portanto, será loteado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar as mensagens de relato de saúde para a loja de saúde, bem como o processamento de relatórios de saúde. Por predefinição, os relatórios não são enviados imediatamente. |
| --remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdadeiro, o relatório é removido da loja de saúde depois de expirar. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoveWhenExpired falso (padrão). Desta forma, o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por erro quando o relatório de saúde expira. Isto sinaliza a entidade como estando no estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --timeout -t | Padrão \: 60. |
| --ttl | A duração para a qual este relatório de saúde é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportam periodicamente, devem enviar relatórios com maior frequência do que o tempo de vida. Se os clientes reportarem sobre a transição, podem definir o tempo para viver em infinito. Quando o tempo de vida expirar, o evento de saúde que contém as informações de saúde é removido da loja de saúde, se removeWhenExpired for verdadeiro, ou avaliado por erro, se RemoveWhenExpired falso. Se não for especificado, o tempo de viver não tem valor infinito. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-restart"></a>reinício da partição sfctl
Esta API reiniciará algumas ou todas as réplicas ou instâncias da partição especificada.

Esta API é útil para testar o failover. Se for utilizado para direcionar uma divisória de serviço apátrida, restartPartitionMode deve ser AllReplicasOrInstances. Ligue para a API GetPartitionRestartProgress usando a mesma OperationId para obter o progresso.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --reiniciar-modo de partição [Obrigatório] | Descreva quais divisórias reiniciar. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-restart-status"></a>sfctl partição reiniciar estado
Obtém-se o progresso de uma operação PartitionRestart começou a usar startPartitionRestart.

Obtém-se o progresso de uma PartitionRestart iniciada com startPartitionRestart usando o OperaçãoId fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --operation-id [Obrigatório] | Um GUID que identifica uma chamada desta API.  Isto é passado para a API GetProgress correspondente. |
| --partição-id [Obrigatório] | A identidade da partição. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-partition-svc-name"></a>sfctl partição svc-nome
Obtém o nome do serviço de tecido de serviço para uma partição.

Obtém o nome do serviço para a partição especificada. Um erro de 404 é devolvido se o ID de partição não existir no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Description|
| --- | --- |
| --partição-id [Obrigatório] | A identidade da partição. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Description|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI do Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
