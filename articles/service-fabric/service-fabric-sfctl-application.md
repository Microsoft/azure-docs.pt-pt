---
title: Aplicação Azure Service Fabric CLI-sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gestão de aplicações.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b4e1066bba1db387c9dc0600bc55522f0b5fe897
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906206"
---
# <a name="sfctl-application"></a>sfctl application
Criar, eliminar e gerir aplicações e tipos de aplicações.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma aplicação de tecido de serviço utilizando a descrição especificada. |
| delete | Elimina uma aplicação de Tecido de Serviço existente. |
| implantado | Obtém a informação sobre uma aplicação implantada num nó de Tecido de Serviço. |
| saúde implantada | Obtém a informação sobre a saúde de uma aplicação implantada num nó de Tecido de Serviço. |
| lista implantada | Obtém a lista de aplicações implantadas num nó de Tecido de Serviço. |
| saúde | Obtém a saúde da aplicação de tecido de serviço. |
| informações | Obtém informações sobre uma aplicação de Tecido de Serviço. |
| list | Obtém a lista de aplicações criadas no cluster Service Fabric que correspondem aos filtros especificados. |
| carregar | Obtém informações de carga sobre uma aplicação de Tecido de Serviço. |
| manifest | Obtém o manifesto descrevendo um tipo de aplicação. |
| disposição | Provisões ou regista um tipo de aplicação de Tecido de Serviço com o cluster utilizando o pacote '.sfpkg' na loja externa ou utilizando o pacote de aplicações na loja de imagens. |
| relatório-saúde | Envia um relatório de saúde sobre a aplicação Service Fabric. |
| tipo | Obtém a lista de tipos de aplicação no cluster Service Fabric que corresponda exatamente ao nome especificado. |
| lista de tipos | Obtém a lista de tipos de aplicação no cluster Service Fabric. |
| unprovision | Remove ou desregista um tipo de aplicação de tecido de serviço do cluster. |
| atualizar | Começa a atualizar uma aplicação no cluster Service Fabric. |
| upgrade-currículo | Retoma a atualização de uma aplicação no cluster Service Fabric. |
| upgrade-rollback | Começa a reverter a atualização atual de uma aplicação no cluster Service Fabric. |
| estado de atualização | Obtém detalhes sobre a mais recente atualização realizada nesta aplicação. |
| carregar | Copie um pacote de aplicação Service Fabric para a loja de imagens. |

## <a name="sfctl-application-create"></a>sfctl aplicação criar
Cria uma aplicação de tecido de serviço utilizando a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome da aplicação [Obrigatório] | O nome da aplicação,\:incluindo o esquema URI de tecido. |
| --tipo de app [Obrigatório] | O nome do tipo de aplicação encontrado no manifesto de aplicação. |
| --versão de apps [Necessária] | A versão do tipo de aplicação, tal como definida no manifesto de aplicação. |
| -- contagem de nósolotamax | O número máximo de nós onde o Tecido de Serviço reservará capacidade para esta aplicação. Note que isso não significa que os serviços desta aplicação serão colocados em todos esses nós. |
| -métricas | Uma lista codificada da JSON das descrições métricas da capacidade da aplicação. Uma métrica é definida como um nome, associado a um conjunto de capacidades para cada nó em que a aplicação existe. |
| --min-nó-contagem | O número mínimo de nós onde o Tecido de Serviço reservará capacidade para esta aplicação. Note que isso não significa que os serviços desta aplicação serão colocados em todos esses nós. |
| --parâmetros | Uma lista codificada de parâmetros de aplicação a aplicar a aplicar durante a criação da aplicação. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-delete"></a>aplicação sfctl excluir
Elimina uma aplicação de Tecido de Serviço existente.

Uma aplicação deve ser criada antes de poder ser eliminada. A eliminação de uma aplicação irá eliminar todos os serviços que fazem parte dessa aplicação. Por padrão, o Service Fabric tentará fechar as réplicas de serviço de forma graciosa e, em seguida, eliminar o serviço. No entanto, se um serviço estiver com problemas de fecho da réplica graciosamente, a operação de eliminação pode demorar muito tempo ou ficar presa. Utilize a bandeira opcional ForceRemove para saltar a graciosa sequência de fecho e apagar com força a aplicação e todos os seus serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --remoção da força | Remova uma aplicação ou serviço de tecido de serviço com força sem passar pela sequência de paragem graciosa. Este parâmetro pode ser utilizado para eliminar com força uma aplicação ou serviço para o qual a eliminação é cronometragem devido a problemas no código de serviço que impedem o encerramento gracioso das réplicas. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-deployed"></a>aplicação sfctl implantada
Obtém a informação sobre uma aplicação implantada num nó de Tecido de Serviço.

Esta consulta devolve informações de aplicação do sistema se o ID da aplicação fornecido for para aplicação do sistema. Os resultados englobam aplicações implementadas em estados ativos, ativadores e descarregamentos. Esta consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falha se o nome do nó fornecido não apontar para quaisquer nós ativos de tecido de serviço no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --incluir-estado-saúde-estado | Incluir o estado de saúde de uma entidade. Se este parâmetro for falso ou não especificado, então o estado de saúde devolvido é "Desconhecido". Quando é verdade, a consulta vai paralelamente ao nó e ao serviço do sistema de saúde antes da fusão dos resultados. Como resultado, a consulta é mais cara e pode demorar mais tempo. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-deployed-health"></a>aplicação sfctl implantada-saúde
Obtém a informação sobre a saúde de uma aplicação implantada num nó de Tecido de Serviço.

Obtém a informação sobre a saúde de uma aplicação implantada num nó de Tecido de Serviço. Utilize o EventsHealthStateFilter para filtrar opcionalmente para a recolha de objetos HealthEvent relatados na aplicação implementada com base no estado de saúde. Utilize o DeployServicePackagesHealthStateFilter para filtrar opcionalmente as crianças implantadas ServicePackageHealth com base no estado de saúde.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --implementado-service-packages-health-state-filter | Permite a filtragem do pacote de serviço implantado objetos do estado de saúde devolvidos em resultado de consulta de saúde de aplicação implementada com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas são devolvidos pacotes de serviço implantados que correspondam ao filtro. Todos os pacotes de serviços implantados são utilizados para avaliar o estado de saúde agregado da aplicação implantada. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, então os pacotes de estado de saúde de serviço com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -excluir-estatísticas de saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de entidades infantis no estado de saúde Ok, Warning e Error. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-deployed-list"></a>aplicação sfctl implantada lista
Obtém a lista de aplicações implantadas num nó de Tecido de Serviço.

Obtém a lista de aplicações implantadas num nó de Tecido de Serviço. Os resultados não incluem informações sobre aplicações do sistema implementadas, a menos que seja explicitamente consultado por ID. Os resultados englobam aplicações implementadas em estados ativos, ativadores e descarregamentos. Esta consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falha se o nome do nó fornecido não apontar para quaisquer nós ativos de tecido de serviço no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [Obrigatório] | O nome do nó. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| --incluir-estado-saúde-estado | Incluir o estado de saúde de uma entidade. Se este parâmetro for falso ou não especificado, então o estado de saúde devolvido é "Desconhecido". Quando é verdade, a consulta vai paralelamente ao nó e ao serviço do sistema de saúde antes da fusão dos resultados. Como resultado, a consulta é mais cara e pode demorar mais tempo. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-health"></a>saúde da aplicação sfctl
Obtém a saúde da aplicação de tecido de serviço.

Devolve o estado de calor da aplicação de tecido de serviço. A resposta reporta o estado de saúde Ok, Error ou Warning. Se a entidade não for encontrada na loja de saúde, devolverá o Erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --aplicações implantadas-filtro de estado-saúde | Permite a filtragem das aplicações implementadas objetos do estado de saúde devolvidos em resultado de consulta de saúde de aplicação com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas as aplicações implantadas que correspondam ao filtro serão devolvidas. Todas as aplicações implementadas são usadas para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada na bandeira, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando um operador de 'OR' bitwise. Por exemplo, se o valor fornecido for de 6, então o estado de saúde das aplicações implementadas com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -excluir-estatísticas de saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de entidades infantis no estado de saúde Ok, Warning e Error. |
| --serviços-filtro estado-saúde | Permite a filtragem dos serviços de estado de saúde objetos devolvidos em resultado de consultas de saúde de serviços com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os serviços que combinam com o filtro são devolvidos. Todos os serviços são utilizados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada na bandeira, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando um operador de 'OR' bitwise. Por exemplo, se o valor fornecido for de 6, então o estado de saúde dos serviços com o valor do Estado de Saúde de OK (2) e Aviso (4) será devolvido.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-info"></a>informação de aplicação sfctl
Obtém informações sobre uma aplicação de Tecido de Serviço.

Devolve as informações sobre a aplicação que foi criada ou em processo de criação no cluster Service Fabric e cujo nome corresponde ao especificado como parâmetro. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| -excluir-aplicação-parâmetros | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-list"></a>lista de candidaturas sfctl
Obtém a lista de aplicações criadas no cluster Service Fabric que correspondem aos filtros especificados.

Obtém a informação sobre as aplicações que foram criadas ou em processo de criação no cluster Service Fabric e corresponde aos filtros especificados. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação. Se as aplicações não encaixarem numa página, uma página de resultados é devolvida, bem como um sinal de continuação, que pode ser usado para obter a página seguinte. Filtrar AplicaçõesTypeName e ApplicationDefinitionKindFilter não pode ser especificado ao mesmo tempo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-definição-tipo-filtro | Usado para filtrar em ApplicationDefinitionKind, que é o mecanismo usado para definir uma aplicação de Tecido de Serviço.  <br> - Predefinição - Valor predefinido, que desempenha a mesma função que selecionar "All". O valor é 0.  <br> - Tudo - Filtrar a entrada que corresponde à entrada com qualquer valor applicationDefinitionKind. O valor é 65535.  <br> - ServiceFabricApplicationDescription - Filtro que corresponde à entrada com aplicaçãoDefiniçãoValor Value ServiceFabricApplicationDescription. O valor é 1.  <br> - Compor - Filtrar que corresponde à entrada com o valor ApplicationDefinitionKind Compor. O valor é 2. |
| --nome do tipo aplicação | O nome do tipo de aplicação usado para filtrar as aplicações para consulta. Este valor não deve conter a versão do tipo de aplicação. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| -excluir-aplicação-parâmetros | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-load"></a>carga de aplicação sfctl
Obtém informações de carga sobre uma aplicação de Tecido de Serviço.

Devolve as informações de carga sobre a aplicação que foi criada ou em processo de criação no cluster Service Fabric e cujo nome corresponde ao especificado como parâmetro. A resposta inclui o nome, nós mínimos, nós máximos, o número de nós que a aplicação está a ocupar atualmente, e informações métricas de carga de aplicação sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-manifest"></a>manifesto de aplicação sfctl
Obtém o manifesto descrevendo um tipo de aplicação.

A resposta contém o manifesto de aplicação XML como uma corda.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do tipo de aplicação [Obrigatório] | O nome do tipo de candidatura. |
| --aplicação-tipo-versão [Obrigatório] | A versão do tipo de aplicação. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-provision"></a>provisão de aplicação sfctl
Provisões ou regista um tipo de aplicação de Tecido de Serviço com o cluster utilizando o pacote '.sfpkg' na loja externa ou utilizando o pacote de aplicações na loja de imagens.

Provisões um tipo de aplicação de tecido de serviço com o cluster. A disposição é necessária antes de quaisquer novos pedidos poderem ser instantaneamente instantâneos. A operação de fornecimento pode ser executada quer no pacote de aplicações especificado pela empresa relativaPathInImageStore, quer através da utilização do URI do '.sfpkg' externo. A menos que a provisão externa esteja definida, este comando espera a provisão da loja de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-pacote-download-uri | O caminho para o pacote de aplicações '.sfpkg' de onde o pacote de aplicações pode ser descarregado usando protocolos HTTP ou HTTPS. <br><br> Para provisões, apenas para uma loja externa tipo. O pacote de aplicações pode ser armazenado numa loja externa que fornece a operação GET para descarregar o ficheiro. Os protocolos suportados são HTTP e HTTPS, e o caminho deve permitir o acesso da READ. |
| --aplicação-tipo-construção-caminho | Para a oferta apenas loja de imagem tipo. O caminho relativo para o pacote de aplicação na loja de imagens especificado durante o funcionamento de upload prévio. |
| --nome do tipo aplicação | Para provisões, apenas para uma loja externa tipo. O nome do tipo de aplicação representa o nome do tipo de aplicação encontrado no manifesto de aplicação. |
| --aplicação-tipo-versão | Para provisões, apenas para uma loja externa tipo. A versão do tipo de aplicação representa a versão do tipo de aplicação encontrada no manifesto de aplicação. |
| --oferta externa | A localização de onde o pacote de candidatura pode ser registado ou provisionado. Indica que a disposição é para um pacote de aplicação que foi previamente enviado para uma loja externa. O pacote de aplicação termina com a extensão *.sfpkg. |
| - não esperar | Indica se o provisionamento deve ou não ocorrer assincronicamente. <br><br> Quando é verdadeira, a operação de fornecimento regressa quando o pedido é aceite pelo sistema, e a operação de provisão continua sem limite de tempo. O valor predefinido é false. Para grandes pacotes de aplicações, recomendamos que o valor seja verdadeiro. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-report-health"></a>relatório de aplicação sfctl-saúde
Envia um relatório de saúde sobre a aplicação Service Fabric.

Informa o estado de saúde da especificada aplicação Service Fabric. O relatório deve conter as informações sobre a origem do relatório de saúde e dos bens em que é comunicado. O relatório é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, obtenha saúde de aplicação e verifique se o relatório aparece.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o personagem ' ' . Por exemplo, se o nome\:da aplicação for 'fabric /myapp/app1', a identidade da aplicação seria 'myapp\~app1' em 6.0+ e 'myapp/app1' em versões anteriores. |
| --propriedade de saúde [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir que a flexibilidade do repórter categorize a condição do Estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
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

## <a name="sfctl-application-type"></a>tipo de aplicação sfctl
Obtém a lista de tipos de aplicação no cluster Service Fabric que corresponda exatamente ao nome especificado.

Devolve as informações sobre os tipos de aplicação que são provisionados ou em processo de aprovisionamento no cluster Service Fabric. Estes resultados são de tipos de aplicação cujo nome corresponde exatamente ao especificado como parâmetro, e que cumprem os parâmetros de consulta dado. Todas as versões do tipo de aplicação que correspondam ao nome do tipo de aplicação são devolvidas, com cada versão devolvida como um tipo de aplicação. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta de página, o que significa que se não todos os tipos de aplicação se encaixarem numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte. Por exemplo, se houver 10 tipos de aplicação, mas uma página apenas se adequa aos três primeiros tipos de aplicação, ou se os resultados máximos forem definidos para 3, então três são devolvidos. Para aceder ao resto dos resultados, recupere as páginas seguintes utilizando o token de continuação devolvido na consulta seguinte. Um sinal de continuação vazio é devolvido se não houver páginas posteriores.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do tipo de aplicação [Obrigatório] | O nome do tipo de candidatura. |
| --aplicação-tipo-versão | A versão do tipo de aplicação. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| -excluir-aplicação-parâmetros | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-type-list"></a>sfctl aplicação lista de tipo
Obtém a lista de tipos de aplicação no cluster Service Fabric.

Devolve as informações sobre os tipos de aplicação que são provisionados ou em processo de aprovisionamento no cluster Service Fabric. Cada versão de um tipo de aplicação é devolvida como um tipo de aplicação. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta de página, o que significa que se não todos os tipos de aplicação se encaixarem numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte. Por exemplo, se houver 10 tipos de aplicação, mas uma página apenas se adequa aos três primeiros tipos de aplicação, ou se os resultados máximos forem definidos para 3, então três são devolvidos. Para aceder ao resto dos resultados, recupere as páginas seguintes utilizando o token de continuação devolvido na consulta seguinte. Um sinal de continuação vazio é devolvido se não houver páginas posteriores.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-type-definition-kind-filter | Utilizado para filtrar no ApplicationTypeDefinitionKind, que é o mecanismo utilizado para definir um tipo de aplicação de tecido de serviço.  <br> - Predefinição - Valor predefinido, que desempenha a mesma função que selecionar "All". O valor é 0.  <br> - Tudo - Filtrar a entrada que corresponde à entrada com qualquer valor applicationTypeDefinitionKind. O valor é 65535.  <br> - ServiceFabricApplicationPackage - Filtro que corresponde à entrada com applicationTypeDefinitionValor valor ServiceFabricApplicationPackage. O valor é 1.  <br> - Compor - Filtrar que corresponde à entrada com o valor ApplicationTypeDefinitionKind Compõe. O valor é 2. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| -excluir-aplicação-parâmetros | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| -resultados máximos | O número máximo de resultados a devolver como parte das consultas páginadas. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta páginada inclui o maior número possível de resultados que se encaixem na mensagem de devolução. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-unprovision"></a>não provisão aplicação sfctl
Remove ou desregista um tipo de aplicação de tecido de serviço do cluster.

Esta operação só pode ser executada se todas as instâncias de aplicação do tipo de aplicação tiverem sido eliminadas. Uma vez que o tipo de aplicação não esteja registado, não podem ser criadas novas instâncias de aplicação para este tipo de aplicação em particular.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do tipo de aplicação [Obrigatório] | O nome do tipo de candidatura. |
| --aplicação-tipo-versão [Obrigatório] | A versão do tipo de aplicação, tal como definida no manifesto de aplicação. |
| --parâmetro asincrono | A bandeira indicando se a não disposição deve ou não ocorrer assíncronamente. Quando é verdadeira, a operação de não provisão regressa quando o pedido é aceite pelo sistema, e a operação de não fornecimento continua sem limite de tempo. O valor predefinido é false. No entanto, recomendamos que seja fiel aos grandes pacotes de aplicações que foram provisionados. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-upgrade"></a>upgrade de aplicação sfctl
Começa a atualizar uma aplicação no cluster Service Fabric.

Valida os parâmetros de atualização da aplicação fornecidos e começa a atualizar a aplicação se os parâmetros forem válidos. Note que a descrição da atualização substitui a descrição da aplicação existente. Isto significa que, se os parâmetros não forem especificados, os parâmetros existentes nas aplicações serão substituídos com a lista de parâmetros vazios. Isto resultaria na aplicação utilizando o valor padrão dos parâmetros do manifesto de aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --versão de aplicação [Necessária] | A versão do tipo de aplicação alvo (encontrada no manifesto de aplicação) para a atualização da aplicação. |
| --parâmetros [obrigatórios] | Uma lista codificada de parâmetros de aplicação a aplicar a aplicar durante a atualização da aplicação. |
| --padrão-serviço-política de saúde | JSON codifica especificação da política de saúde utilizada por padrão para avaliar a saúde de um tipo de serviço. |
| --falha-acção | A ação a realizar quando uma atualização monitorizada encontra a política de monitorização ou as violações da política de saúde. |
| --reinício da força | Reiniciar vigorosamente os processos durante a atualização mesmo quando a versão do código não mudou. |
| --saúde-check-retry-timeout | O período de tempo entre as tentativas de realização de verificações de saúde se a aplicação ou o cluster não forem saudáveis.  Padrão\: PT0H10m0s. |
| --saúde-check-estável-duração | O tempo que a aplicação ou o cluster devem manter-se saudáveis antes da atualização passar para o próximo domínio de atualização.  Padrão\: PT0H2M0s. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-wait-duração | O tempo de espera após completar um domínio de atualização antes de iniciar o processo de verificação de saúde.  Padrão\: 0. |
| -- aplicações max-insalubres | A percentagem máxima permitida de aplicações implantadas não saudáveis. Representado como um número entre 0 e 100. |
| --modo | O modo utilizado para monitorizar a saúde durante uma atualização de rolamento.  Padrão\: Não monitorizadoAuto. |
| --replica-set-check-out | O máximo de tempo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. Medido em segundos. |
| --serviço-política de saúde | Mapa codificado jSON com política de saúde do tipo de serviço por nome do tipo de serviço. O mapa está vazio. |
| --timeout -t | Padrão\: 60. |
| --upgrade-tempo de domínio | O tempo que cada domínio de atualização tem de completar antes da execução da Ação de Falha.  Padrão\: P10675199DT02H48m05.4775807s. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-timeout | O tempo que a atualização global tem de completar antes da execução da FailAction.  Padrão\: P10675199DT02H48m05.4775807s. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl aplicação upgrade-currículo
Retoma a atualização de uma aplicação no cluster Service Fabric.

Retoma uma atualização manual de aplicações do Tecido de Serviço não monitorizada. Serviço Tecido atualiza um domínio de upgrade de cada vez. Para atualizações manuais não monitorizadas, depois de o Tecido de Serviço terminar um domínio de atualização, aguarda-lhe que chame a este API antes de seguir para o próximo domínio de atualização.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --upgrade-nome de domínio [Obrigatório] | O nome do domínio de atualização para retomar a atualização. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl aplicação upgrade-rollback
Começa a reverter a atualização atual de uma aplicação no cluster Service Fabric.

Começa a reverter a atualização atual da aplicação para a versão anterior. Esta API só pode ser usada para reverter a atual atualização em curso que está a avançar para uma nova versão. Se a aplicação não estiver atualmente a ser atualizada, utilize a StartApplicationUpgrade API para atualizá-la para a versão desejada, incluindo voltar a ser uma versão anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-upgrade-status"></a>sfctl aplicação upgrade-status
Obtém detalhes sobre a mais recente atualização realizada nesta aplicação.

Devolve informações sobre o estado da última atualização de aplicações, juntamente com detalhes para ajudar a depurar problemas de saúde de aplicações.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-application-upload"></a>upload de aplicação sfctl
Copie um pacote de aplicação Service Fabric para a loja de imagens.

Exiba opcionalmente o progresso de upload de cada ficheiro da embalagem. O upload do `stderr`progresso é enviado para .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caminho [Obrigatório] | Caminho para o pacote de candidatura local. |
| -compressa | Aplicável apenas aos pacotes de aplicações service Fabric. Crie uma nova pasta contendo o pacote de aplicação comprimido para a localização predefinida, ou para a localização especificada pelo parâmetro de localização comprimido, e, em seguida, carregue a pasta recém-criada. <br><br> Se já existir um ficheiro comprimido gerado por sfctl, será substituído se esta bandeira estiver definida. Um erro será devolvido se o diretório não for um pacote de aplicação. Se já for um pacote de aplicação comprimido, a pasta será copiada como está. Por predefinição, o pacote de aplicação comprimido recém-criado será eliminado após um upload bem sucedido. Se o upload não for bem sucedido, por favor limpe manualmente a embalagem comprimido conforme necessário. A eliminação não remove quaisquer dirs vazios que possam ter sido criados se o parâmetro de localização comprimido refere-se a diretórios inexistentes. |
| --localização comprimido | A localização para colocar o pacote de aplicação comprimido. <br><br> Se não for fornecida qualquer localização, o pacote comprimido será colocado sob uma pasta recém-criada chamada sfctl_compressed_temp sob o directório-mãe especificado no argumento do caminho. Por exemplo, se o argumento\:do caminho tiver o valor C/FolderA/AppPkg, então o pacote comprimido será adicionado a C/FolderA/sfctl_compressed_temp/AppPkg.\: |
| --imagestore-string | Loja de imagem de destino para fazer o upload do pacote de aplicações para.  Imagem\: \:De tecido padrão. <br><br> Para fazer o upload para um local\:de arquivo, inicie este parâmetro com 'file'. Caso contrário, o valor deve ser a cadeia de ligação da loja de imagens, como o valor predefinido. |
| -manter-se comprimido | Quer mantenha ou não o pacote comprimido gerado na conclusão do upload bem-sucedido. <br><br> Se não estiver definido, então, após a conclusão bem sucedida, os pacotes de aplicações comprimidos serão eliminados. Se o upload não tiver sido bem sucedido, então o pacote de aplicação será sempre mantido no diretório de saída para recarregar. |
| -show-progress | Mostre o progresso do upload do ficheiro para grandes pacotes. |
| --timeout -t | O tempo total em segundos. O upload falhará e devolverá o erro após o tempo de saída ter passado. Este prazo aplica-se a todo o pacote de aplicação, e os prazos de entrega de ficheiros individuais serão iguais à duração do tempo de paragem. O timeout não inclui o tempo necessário para comprimir o pacote de aplicações.  Padrão\: 300. |

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
