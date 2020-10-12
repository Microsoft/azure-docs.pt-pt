---
title: Aplicação CLI-sfctl de tecido de serviço Azure
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para a gestão de aplicações.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 880770345eb7d65850db322bd97d64c60b6681ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260867"
---
# <a name="sfctl-application"></a>sfctl application
Criar, excluir e gerir aplicações e tipos de aplicações.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| criar | Cria uma aplicação de Tecido de Serviço utilizando a descrição especificada. |
| delete | Elimina uma aplicação de Tecido de Serviço existente. |
| implantado | Obtém a informação sobre uma aplicação implantada num nó de Tecido de Serviço. |
| saúde implantada | Obtém a informação sobre a saúde de uma aplicação implantada num nó de Tecido de Serviço. |
| lista implantada | Obtém a lista de aplicações implantadas num nó de Tecido de Serviço. |
| Saúde | Obtém a saúde da aplicação de tecido de serviço. |
| informações | Obtém informações sobre uma aplicação de Tecido de Serviço. |
| lista | Obtém a lista de aplicações criadas no cluster de Tecido de Serviço que correspondem aos filtros especificados. |
| carregar | Obtém informações sobre uma aplicação de Tecido de Serviço. |
| manifest | Obtém o manifesto descrevendo um tipo de aplicação. |
| provisão | Provisões ou registos de um tipo de aplicação De Tecido de Serviço com o cluster utilizando o pacote '.sfpkg' na loja externa ou utilizando o pacote de aplicações na loja de imagens. |
| relatório-saúde | Envia um relatório de saúde sobre a aplicação de Tecido de Serviço. |
| tipo | Obtém a lista de tipos de aplicação no cluster de Tecido de Serviço que corresponde exatamente ao nome especificado. |
| lista de tipos | Obtém a lista de tipos de aplicações no cluster de Tecido de Serviço. |
| não provisão | Remove ou não registra um tipo de aplicação de Tecido de Serviço do cluster. |
| atualizar | Começa a atualizar uma aplicação no cluster de Tecido de Serviço. |
| resumo de upgrade | Retoma a atualização de uma aplicação no cluster de Tecido de Serviço. |
| upgrade-rollback | Começa a reverter a atualização em curso de uma aplicação no cluster de Tecido de Serviço. |
| estado de atualização | Obtém detalhes para a mais recente atualização realizada nesta aplicação. |
| carregar | Copie um pacote de aplicações de Tecido de Serviço para a loja de imagens. |

## <a name="sfctl-application-create"></a>aplicação sfctl criar
Cria uma aplicação de Tecido de Serviço utilizando a descrição especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-nome [Obrigatório] | O nome do pedido, incluindo o \: "sistema URI do tecido". |
| --tipo de aplicação [Obrigatório] | O nome do tipo de aplicação encontrado no manifesto de candidatura. |
| --versão de aplicações [Necessária] | A versão do tipo de aplicação tal como definida no manifesto de aplicação. |
| --max-node-contagem | O número máximo de nós onde o Service Fabric reservará capacidade para esta aplicação. Note-se que isto não significa que os serviços desta aplicação serão colocados em todos esses nós. |
| --métricas | Uma lista codificada por JSON das descrições métricas da capacidade da aplicação. Uma métrica é definida como um nome, associado a um conjunto de capacidades para cada nó em que a aplicação existe. |
| --min-node-count | O número mínimo de nós onde o Service Fabric reservará capacidade para esta aplicação. Note-se que isto não significa que os serviços desta aplicação serão colocados em todos esses nós. |
| --parâmetros | Uma lista codificada por JSON de substituições de parâmetros de aplicação a aplicar ao criar a aplicação. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-delete"></a>aplicação sfctl eliminar
Elimina uma aplicação de Tecido de Serviço existente.

Uma aplicação deve ser criada antes de poder ser eliminada. A eliminação de uma aplicação irá eliminar todos os serviços que fazem parte dessa aplicação. Por predefinição, o Service Fabric tentará fechar as réplicas de serviço de forma graciosa e, em seguida, apagar o serviço. No entanto, se um serviço estiver com problemas em fechar a réplica graciosamente, a operação de eliminação pode demorar muito tempo ou ficar presa. Utilize a bandeira forceRemove opcional para saltar a graciosa sequência de fecho e apagar com força a aplicação e todos os seus serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| ...-força-remoção | Remova uma aplicação ou serviço de tecido de serviço com força sem passar pela sequência graciosa de paragem. Este parâmetro pode ser usado para eliminar com força uma aplicação ou serviço para o qual a eliminação está fora devido a problemas no código de serviço que impede o encerramento gracioso de réplicas. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-deployed"></a>aplicação sfctl implementada
Obtém a informação sobre uma aplicação implantada num nó de Tecido de Serviço.

Esta consulta devolve as informações da aplicação do sistema se o ID da aplicação fornecido for para aplicação do sistema. Os resultados englobam aplicações implementadas em estados ativos, de ativação e de descarregamento. Esta consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falha se o nome do nó fornecido não apontar para nenhum nó de tecido de serviço ativo no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --incluir-estado de saúde | Incluir o estado de saúde de uma entidade. Se este parâmetro for falso ou não especificado, então o estado de saúde devolvido é "Desconhecido". Quando definido como verdadeiro, a consulta vai paralelamente ao nó e ao serviço do sistema de saúde antes da fusão dos resultados. Como resultado, a consulta é mais cara e pode demorar mais tempo. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-deployed-health"></a>aplicação sfctl implementada saúde
Obtém a informação sobre a saúde de uma aplicação implantada num nó de Tecido de Serviço.

Obtém a informação sobre a saúde de uma aplicação implantada num nó de Tecido de Serviço. Utilize eventosHealthStateFilter para filtrar opcionalmente para a recolha de objetos HealthEvent relatados na aplicação implementada com base no estado de saúde. Utilize o Serviço de Serviços DesenvolvidosHealthStateFilter opcionalmente para filtrar opcionalmente para crianças do Serviço de Segurança Implementadas Com base no estado de saúde.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-pacotes-pacotes-saúde-estado-filtro | Permite a filtragem dos objetos do estado de saúde do pacote de serviço implantado devolvidos em resultado de consulta de saúde de aplicação implementada com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. São devolvidos apenas os pacotes de serviço que correspondam ao filtro. Todos os pacotes de serviços implantados são utilizados para avaliar o estado de saúde agregado da aplicação implementada. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeira, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for 6, então os pacotes de estado de saúde de serviço com valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --excluir-estatísticas da saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de crianças entidades no estado de saúde Ok, Aviso e Erro. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-deployed-list"></a>sfctl aplicação implementada lista
Obtém a lista de aplicações implantadas num nó de Tecido de Serviço.

Obtém a lista de aplicações implantadas num nó de Tecido de Serviço. Os resultados não incluem informações sobre aplicações de sistema implementadas, a menos que explicitamente questionados por ID. Os resultados englobam aplicações implementadas em estados ativos, de ativação e de descarregamento. Esta consulta requer que o nome do nó corresponda a um nó no cluster. A consulta falha se o nome do nó fornecido não apontar para nenhum nó de tecido de serviço ativo no cluster.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome de nó [Obrigatório] | O nome do nó. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --incluir-estado de saúde | Incluir o estado de saúde de uma entidade. Se este parâmetro for falso ou não especificado, então o estado de saúde devolvido é "Desconhecido". Quando definido como verdadeiro, a consulta vai paralelamente ao nó e ao serviço do sistema de saúde antes da fusão dos resultados. Como resultado, a consulta é mais cara e pode demorar mais tempo. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-health"></a>sfctl saúde aplicação
Obtém a saúde da aplicação de tecido de serviço.

Devolve o estado de saúde da aplicação de tecido de serviço. A resposta relata ok, erro ou estado de saúde de aviso. Se a entidade não for encontrada na loja de saúde, devolverá o Erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --aplicações implantadas-saúde-estado-filtro | Permite a filtragem das aplicações implementadas objetos do estado de saúde devolvidos em resultado de consulta de saúde da aplicação com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Serão devolvidas apenas aplicações que correspondam ao filtro. Todas as aplicações implementadas são usadas para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumerações baseadas em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for 6, então o estado de saúde das aplicações implementadas com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --excluir-estatísticas da saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de crianças entidades no estado de saúde Ok, Aviso e Erro. |
| --serviços-saúde-estado-filtro | Permite a filtragem dos serviços de saúde objetos do Estado devolvidos em resultado de consulta de saúde de serviços com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os serviços que correspondam ao filtro são devolvidos. Todos os serviços são utilizados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumerações baseadas em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for 6, então o estado de saúde dos serviços com o valor do Estado de Saúde de OK (2) e Aviso (4) será devolvido.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-info"></a>sfctl informação de aplicação
Obtém informações sobre uma aplicação de Tecido de Serviço.

Devolve a informação sobre a aplicação que foi criada ou em processo de criação no cluster de Tecido de Serviço e cujo nome corresponde ao especificado como parâmetro. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --excluir-parâmetros de aplicação | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-list"></a>lista de candidaturas sfctl
Obtém a lista de aplicações criadas no cluster de Tecido de Serviço que correspondem aos filtros especificados.

Obtém a informação sobre as aplicações que foram criadas ou no processo de criação no cluster de Tecido de Serviço e corresponde aos filtros especificados. A resposta inclui o nome, tipo, estado, parâmetros e outros detalhes sobre a aplicação. Se as aplicações não encaixarem numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte. Filtragem AplicaçãoTypeName e ApplicationDefinitionKindFilter não pode ser especificado ao mesmo tempo.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-definição-tipo-filtro | Usado para filtrar na ApplicationDefinitionKind, que é o mecanismo usado para definir uma aplicação de Tecido de Serviço.  <br> - Padrão - Valor predefinido, que executa a mesma função que selecionar "All". O valor é 0.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor DefinitionKind da Aplicação. O valor é 65535.  <br> - ServiceFabricApplicationDescription - Filtro que corresponde à entrada com o valor de AplicaçãoDefinitionKind ServiceFabricApplicationDescription. O valor é 1.  <br> - Compor - Filtro que combine com entrada com Valor DefinitionA Definition Compose. O valor é 2. |
| --denominação-aplicação | O nome do tipo de aplicação usado para filtrar as aplicações para consulta. Este valor não deve conter a versão do tipo de aplicação. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --excluir-parâmetros de aplicação | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-load"></a>carga de aplicação sfctl
Obtém informações sobre uma aplicação de Tecido de Serviço.

Devolve a informação de carga sobre a aplicação que foi criada ou em processo de criação no cluster de Tecido de Serviço e cujo nome corresponde ao especificado como parâmetro. A resposta inclui o nome, nós mínimos, nós máximos, o número de nós que a aplicação ocupa atualmente, e informações métricas de carga de aplicação sobre a aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-manifest"></a>manifesto de aplicação sfctl
Obtém o manifesto descrevendo um tipo de aplicação.

A resposta contém o manifesto de aplicação XML como uma corda.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-tipo-nome [Obrigatório] | O nome do tipo de aplicação. |
| --versão do tipo de aplicação [Necessária] | A versão do tipo de aplicação. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-provision"></a>sfctl provisão de aplicação
Provisões ou registos de um tipo de aplicação De Tecido de Serviço com o cluster utilizando o pacote '.sfpkg' na loja externa ou utilizando o pacote de aplicações na loja de imagens.

Fornece um tipo de aplicação de Tecido de Serviço com o cluster. A disposição é necessária antes de qualquer nova aplicação poder ser instantânea. A operação de provisão pode ser realizada quer no pacote de aplicações especificado pela relativaPathInImageStore, quer utilizando o URI do '.sfpkg' externo. A menos que a provisão externa seja definida, este comando espera a disponibilização da loja de imagens.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-package-download-uri | O caminho para o pacote de aplicações 'sfpkg' de onde o pacote de aplicações pode ser descarregado usando protocolos HTTP ou HTTPS. <br><br> Apenas para a loja externa do tipo de provisão. O pacote de aplicações pode ser armazenado numa loja externa que fornece operação GET para descarregar o ficheiro. Os protocolos suportados são HTTP e HTTPS, e o caminho deve permitir o acesso ao READ. |
| --application-type-build-path | Apenas para a loja de imagem tipo provisão. O caminho relativo para o pacote de aplicações na loja de imagens especificado durante a operação de upload prévio. |
| --denominação-aplicação | Apenas para a loja externa do tipo de provisão. O nome do tipo de aplicação representa o nome do tipo de aplicação encontrado no manifesto de aplicação. |
| --versão tipo aplicação | Apenas para a loja externa do tipo de provisão. A versão tipo de aplicação representa a versão do tipo de aplicação encontrada no manifesto de aplicação. |
| --disposição externa | O local de onde o pacote de pedidos pode ser registado ou a provisionado. Indica que a provisão é para um pacote de aplicações que foi previamente enviado para uma loja externa. O pacote de aplicação termina com a extensão *.sfpkg. |
| -- sem espera | Indica se o provisionamento deve ou não ocorrer de forma assíncronea. <br><br> Quando definido como verdadeiro, a operação de provisão retorna quando o pedido é aceite pelo sistema, e a operação de provisão continua sem qualquer limite de tempo. O valor predefinido é false. Para pacotes de aplicações grandes, recomendamos definir o valor verdadeiro. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-report-health"></a>relatório de aplicação sfctl-saúde
Envia um relatório de saúde sobre a aplicação de Tecido de Serviço.

Relatórios de estado de saúde da aplicação de Tecido de Serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e sobre os bens em que é comunicado. O relatório é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, obtenha saúde da aplicação e verifique se o relatório aparece.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o \~ ' ' caráter. Por exemplo, se o nome da aplicação for 'tecido \: /myapp/app1', a identidade da aplicação seria 'myapp \~ app1' em 6.0+ e 'myapp/app1' em versões anteriores. |
| --saúde-propriedade [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir ao repórter flexibilidade para categorizar a condição do estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem \: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --source-id [Obrigatório] | O nome de origem que identifica o componente cliente/cão de guarda/sistema que gerou a informação de saúde. |
| --descrição | A descrição da informação de saúde. <br><br> Representa um texto gratuito utilizado para adicionar informações legíveis humanas sobre o relatório. O comprimento máximo da corda para a descrição é de 4096 caracteres. Se a corda fornecida for mais comprida, será automaticamente truncada. Quando truncados, os últimos caracteres da descrição contêm um marcador "[Truncated]", e o tamanho total da corda é de 4096 caracteres. A presença do marcador indica aos utilizadores que a truncação ocorreu. Note que, quando truncado, a descrição tem menos de 4096 caracteres da corda original. |
| --imediata | Uma bandeira que indica se o relatório deve ser enviado imediatamente. <br><br> Um relatório de saúde é enviado para uma aplicação de gateway de tecido de serviço, que reencaminha para a loja de saúde. Se o Imediato for definido como verdadeiro, o relatório é enviado imediatamente de HTTP Gateway para a loja de saúde, independentemente das definições do cliente de tecido que a Aplicação HTTP Gateway está a usar. Isto é útil para relatórios críticos que devem ser enviados o mais rapidamente possível. Dependendo do tempo e de outras condições, o envio do relatório pode ainda falhar, por exemplo, se o GATEWAY HTTP estiver fechado ou a mensagem não chegar ao Gateway. Se o Immediate for definido como falso, o relatório é enviado com base nas definições do cliente de saúde a partir do GATEWAY HTTP. Portanto, será loteado de acordo com a configuração HealthReportSendInterval. Esta é a configuração recomendada porque permite ao cliente de saúde otimizar as mensagens de relato de saúde para a loja de saúde, bem como o processamento de relatórios de saúde. Por predefinição, os relatórios não são enviados imediatamente. |
| --remover-quando expirado | Valor que indica se o relatório é removido da loja de saúde quando expira. <br><br> Se for verdadeiro, o relatório é removido da loja de saúde depois de expirar. Se for definido como falso, o relatório é tratado como um erro quando expirado. O valor desta propriedade é falso por defeito. Quando os clientes reportam periodicamente, devem definir RemoveWhenExpired falso (padrão). Desta forma, o repórter tem problemas (por exemplo, impasse) e não pode reportar, a entidade é avaliada por erro quando o relatório de saúde expira. Isto sinaliza a entidade como estando no estado de saúde error. |
| --número de sequência | O número da sequência deste relatório de saúde como uma corda numérica. <br><br> O número da sequência do relatório é usado pela loja de saúde para detetar relatórios antigos. Se não for especificado, um número de sequência é gerado automaticamente pelo cliente de saúde quando um relatório é adicionado. |
| --timeout -t | Padrão \: 60. |
| --ttl | A duração para a qual este relatório de saúde é válido. Este campo utiliza o formato ISO8601 para especificar a duração. <br><br> Quando os clientes reportam periodicamente, devem enviar relatórios com maior frequência do que o tempo de vida. Se os clientes reportarem sobre a transição, podem definir o tempo para viver em infinito. Quando o tempo de vida expirar, o evento de saúde que contém as informações de saúde é removido da loja de saúde, se removeWhenExpired for verdadeiro, ou avaliado por erro, se RemoveWhenExpired falso. Se não for especificado, o tempo de viver não tem valor infinito. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-type"></a>tipo de aplicação sfctl
Obtém a lista de tipos de aplicação no cluster de Tecido de Serviço que corresponde exatamente ao nome especificado.

Devolve a informação sobre os tipos de aplicação que são a provisionados ou em processo de a provisionamento no cluster de Tecidos de Serviço. Estes resultados são de tipos de aplicação cujo nome corresponde exatamente ao especificado como parâmetro, e que cumprem os parâmetros de consulta determinados. Todas as versões do tipo de aplicação correspondentes ao nome do tipo de aplicação são devolvidas, sendo cada versão devolvida como um tipo de aplicação. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta paged, o que significa que se não todos os tipos de aplicação se encaixarem numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte. Por exemplo, se existem 10 tipos de aplicação mas uma página só se adequa aos três primeiros tipos de aplicação, ou se os resultados máximos forem definidos para 3, então três são devolvidos. Para aceder aos restantes resultados, recupere as páginas seguintes utilizando o token de continuação devolvido na consulta seguinte. Um sinal de continuação vazio é devolvido se não houver páginas posteriores.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-tipo-nome [Obrigatório] | O nome do tipo de aplicação. |
| --versão tipo aplicação | A versão do tipo de aplicação. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --excluir-parâmetros de aplicação | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-type-list"></a>lista de tipos de aplicação sfctl
Obtém a lista de tipos de aplicações no cluster de Tecido de Serviço.

Devolve a informação sobre os tipos de aplicação que são a provisionados ou em processo de a provisionamento no cluster de Tecidos de Serviço. Cada versão de um tipo de aplicação é devolvida como um tipo de aplicação. A resposta inclui o nome, versão, estado e outros detalhes sobre o tipo de aplicação. Esta é uma consulta paged, o que significa que se não todos os tipos de aplicação se encaixarem numa página, uma página de resultados é devolvida, bem como um token de continuação, que pode ser usado para obter a página seguinte. Por exemplo, se existem 10 tipos de aplicação mas uma página só se adequa aos três primeiros tipos de aplicação, ou se os resultados máximos forem definidos para 3, então três são devolvidos. Para aceder aos restantes resultados, recupere as páginas seguintes utilizando o token de continuação devolvido na consulta seguinte. Um sinal de continuação vazio é devolvido se não houver páginas posteriores.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-tipo-definição-filtro-tipo | Usado para filtrar no ApplicationTypeDefinitionKind, que é o mecanismo utilizado para definir um tipo de aplicação de Tecido de Serviço.  <br> - Padrão - Valor predefinido, que executa a mesma função que selecionar "All". O valor é 0.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor ApplicationTypeDefinitionKind. O valor é 65535.  <br> - ServiceFabricApplicationPackage - Filtro que corresponde à entrada com o valor de AplicaçãoTypeDefinitionKind ServiceFabricApplicationPackage. O valor é 1.  <br> - Compor - Filtro que combine com entrada com Valor DefinitionKind de Aplicação. O valor é 2. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --excluir-parâmetros de aplicação | A bandeira que especifica se os parâmetros de aplicação serão excluídos do resultado. |
| --resultados máximos | O número máximo de resultados a devolver como parte das consultas de página. Este parâmetro define o limite superior no número de resultados devolvidos. Os resultados devolvidos podem ser inferiores aos resultados máximos especificados se não encaixarem na mensagem de acordo com as restrições máximas do tamanho da mensagem definidas na configuração. Se este parâmetro for zero ou não especificado, a consulta paged inclui o maior número possível de resultados que se encaixam na mensagem de retorno. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-unprovision"></a>sfctl aplicação não provisão
Remove ou não registra um tipo de aplicação de Tecido de Serviço do cluster.

Esta operação só pode ser executada se todas as instâncias de aplicação do tipo de aplicação tiverem sido eliminadas. Uma vez que o tipo de aplicação não esteja registado, não podem ser criados novos casos de aplicação para este tipo específico de aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-tipo-nome [Obrigatório] | O nome do tipo de aplicação. |
| --versão do tipo de aplicação [Necessária] | A versão do tipo de aplicação tal como definida no manifesto de aplicação. |
| --async-parâmetro | A bandeira indicando se a não revisão deve ocorrer de forma assíncronea. Quando definido como verdadeiro, a operação não provisão retorna quando o pedido é aceite pelo sistema, e a operação de não revisão continua sem qualquer limite de tempo. O valor predefinido é false. No entanto, recomendamos que seja verdadeira para os grandes pacotes de aplicações que foram a provisionados. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-upgrade"></a>atualização de aplicações sfctl
Começa a atualizar uma aplicação no cluster de Tecido de Serviço.

Valida os parâmetros de atualização da aplicação fornecida e começa a atualizar a aplicação se os parâmetros forem válidos. Note que a descrição da atualização substitui a descrição da aplicação existente. Isto significa que, se os parâmetros não forem especificados, os parâmetros existentes nas aplicações serão substituídos com a lista de parâmetros vazios. Isto resultaria na aplicação utilizando o valor predefinido dos parâmetros do manifesto de aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. <br><br> Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --versão da aplicação [Necessária] | A versão do tipo de aplicação alvo (encontrada no manifesto de aplicação) para a atualização da aplicação. |
| --parâmetros [Obrigatórios] | Uma lista codificada por JSON de sobreposições de parâmetros de aplicação a aplicar ao atualizar a aplicação. |
| --política de saúde de serviço padrão | A JSON codificou a especificação da política de saúde utilizada por defeito para avaliar a saúde de um tipo de serviço. |
| ...-falha de ação | A ação a realizar quando uma atualização monitorizada encontra políticas de monitorização ou violações da política de saúde. |
| ...-força-reiniciar | Reinicie vigorosamente os processos durante a atualização, mesmo quando a versão código não tenha sido alterada. |
| --verificação da saúde-re-tentar-tempo limite | O período de tempo entre tentativas de efetuar controlos de saúde se a aplicação ou o agrupamento não forem saudáveis.  \:PT0H10M0s predefinidos. |
| --saúde-verificação-duração estável | O tempo que a aplicação ou cluster deve permanecer saudável antes da atualização avançar para o próximo domínio de atualização.  \:PT0H2M0s predefinidos. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --saúde-check-wait-duração | O tempo de espera após completar um domínio de upgrade antes de iniciar o processo de verificação de saúde.  Predefinido \: 0. |
| --max-insalubre-apps | A percentagem máxima permitida de aplicações não saudáveis implementadas. Representado como um número entre 0 e 100. |
| --modo | O modo utilizado para monitorizar a saúde durante uma atualização de rolamento.  Padrão \: não monitorizado. |
| --replica-set-check-timeout | O tempo máximo para bloquear o processamento de um domínio de upgrade e evitar a perda de disponibilidade quando há problemas inesperados. Medido em segundos. |
| --prestação de serviços-política de saúde | Mapa codificado JSON com política de saúde tipo de serviço por nome do tipo de serviço. O mapa está vazio. |
| --timeout -t | Padrão \: 60. |
| --upgrade-tempo limite de domínio | O tempo que cada domínio de atualização tem de completar antes da execução do FailureAction.  Default \: P10675199DT02H48M05.4775807S. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| --upgrade-tempo limite | O tempo que a atualização global tem de ser concluída antes da execução do FailureAction.  Default \: P10675199DT02H48M05.4775807S. <br><br> É interpretado pela primeira vez como uma corda que representa uma duração ISO 8601. Se isso falhar, então é interpretado como um número que representa o número total de milissegundos. |
| ...-aviso-como-erro | Indica se os avisos são tratados com a mesma gravidade que os erros. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl aplicação upgrade-resume
Retoma a atualização de uma aplicação no cluster de Tecido de Serviço.

Retoma uma atualização manual da aplicação do Tecido de Serviço não monitorizada. O Service Fabric atualiza um domínio de upgrade de cada vez. Para atualizações manuais não monitorizadas, depois de o Service Fabric terminar um domínio de atualização, espera que ligue para esta API antes de avançar para o domínio de atualização seguinte.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --upgrade-nome de domínio [Obrigatório] | O nome do domínio de atualização no qual retomará a atualização. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl aplicação upgrade-rollback
Começa a reverter a atualização em curso de uma aplicação no cluster de Tecido de Serviço.

Começa a reverter a atual atualização da aplicação para a versão anterior. Esta API só pode ser usada para reverter a atual atualização em curso que está a avançar para a nova versão. Se a aplicação não estiver atualmente a ser atualizada, utilize a API startApplicationUpgrade para atualizá-la para a versão desejada, incluindo o regresso a uma versão anterior.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-upgrade-status"></a>sfctl aplicação upgrade-status
Obtém detalhes para a mais recente atualização realizada nesta aplicação.

Devolve informações sobre o estado da última atualização de aplicações, juntamente com detalhes para ajudar a depurar problemas de saúde dos pedidos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-application-upload"></a>upload de aplicações sfctl
Copie um pacote de aplicações de Tecido de Serviço para a loja de imagens.

Exibir opcionalmente o progresso do upload para cada ficheiro na embalagem. O upload do progresso é enviado para `stderr` .

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --caminho [Obrigatório] | Caminho para o pacote de aplicações locais. |
| --compressa | Aplicável apenas aos pacotes de aplicações do Service Fabric. Crie uma nova pasta que contenha o pacote de aplicações comprimidos para a localização predefinida ou para a localização especificada pelo parâmetro de localização comprimido e, em seguida, carrefique a pasta recém-criada. <br><br> Se já existir um ficheiro comprimido gerado pelo sfctl, será substituído se esta bandeira for definida. Um erro será devolvido se o diretório não for um pacote de candidatura. Se já se trata de um pacote de aplicações comprimidos, a pasta será copiada como está. Por predefinição, o pacote de aplicação comprimido recém-criado será eliminado após um upload bem sucedido. Se o upload não for bem sucedido, por favor limpe manualmente o pacote comprimido conforme necessário. A supressão não remove quaisquer dirs vazios que possam ter sido criados se o parâmetro de localização comprimido referenciar diretórios inexistentes. |
| --localização comprimido | O local para colocar o pacote de aplicação comprimido. <br><br> Se não for fornecida nenhuma localização, o pacote comprimido será colocado sob uma pasta recém-criada chamada sfctl_compressed_temp sob o directório-mãe especificado no argumento do caminho. Por exemplo, se o argumento do caminho tiver valor \: C/PastaA/AppPkg, então o pacote comprimido será adicionado a \: C/PastaA/sfctl_compressed_temp/AppPkg. |
| --imagestore-string | Loja de imagens de destino para carregar o pacote de aplicações para.  Imagem \: de tecido padrãoS. \: <br><br> Para fazer o upload para uma localização de ficheiro, inicie este parâmetro com \: 'ficheiro'. Caso contrário, o valor deve ser a cadeia de ligação da loja de imagens, como o valor predefinido. |
| --manter-comprimido | Manter ou não o pacote comprimido gerado na conclusão do upload com sucesso. <br><br> Se não estiver definido, então, após a conclusão com sucesso, os pacotes de aplicações comprimidos serão eliminados. Se o upload não tiver sido bem sucedido, então o pacote de aplicações será sempre mantido no diretório de saída para re upload. |
| --show-progresso | Mostrar o progresso do upload de ficheiros para grandes pacotes. |
| --timeout -t | O tempo limite total em segundos. O upload falhará e devolveu o erro após o tempo limite de carregamento ter passado. Este tempo limite aplica-se a todo o pacote de aplicações, e os intervalos individuais de ficheiros serão iguais à duração do tempo limite restante. O timeout não inclui o tempo necessário para comprimir o pacote de aplicações.  Padrão \: 300. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI do Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
