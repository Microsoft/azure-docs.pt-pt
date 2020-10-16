---
title: Serviço Azure Fabric CLI-sfctl
description: Saiba mais sobre o sfctl, a interface de linha de comando do Azure Service Fabric. Inclui uma lista de comandos para a gestão de serviços, tipos de serviços e pacotes de serviços.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 426220f38e6ddfaaf8b24bf4f7d34473d881ae56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245607"
---
# <a name="sfctl-service"></a>sfctl service
Criar, eliminar e gerir serviços, tipos de serviço e pacotes de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| nome de aplicativo | Obtém o nome do pedido de Serviço Fabric para um serviço. |
| lista de pacotes de código | Obtém a lista de pacotes de código implantados num nó de Tecido de Serviço. |
| criar | Cria o serviço de tecido de serviço especificado. |
| delete | Elimina um serviço de tecido de serviço existente. |
| implantado tipo | Obtém a informação sobre um tipo de serviço especificado da aplicação implantada num nó num cluster de Tecido de Serviço. |
| implantada lista de tipos | Obtém a lista que contém as informações sobre tipos de serviço das aplicações implantadas num nó num cluster de Tecido de Serviço. |
| descrição | Obtém a descrição de um serviço de tecido de serviço existente. |
| obter-contentores-logs | Obtém os registos dos contentores para o contentor colocados num nó de tecido de serviço. |
| Saúde | Obtém a saúde do serviço de tecido de serviço especificado. |
| informações | Obtém a informação sobre o serviço específico pertencente à aplicação Service Fabric. |
| lista | Obtém a informação sobre todos os serviços pertencentes à aplicação especificada pelo ID da aplicação. |
| manifest | Obtém o manifesto descrevendo um tipo de serviço. |
| implantação em pacotes | Descarrega pacotes associados a manifesto de serviço especificado para a cache de imagem no nó especificado. |
| saúde em pacote | Obtém a informação sobre a saúde de um pacote de serviço para uma aplicação específica implantada para um nó e aplicação de Tecido de Serviço. |
| package-info | Obtém a lista de pacotes de serviço implantados num nó de Tecido de Serviço que corresponde exatamente ao nome especificado. |
| lista de pacotes | Obtém a lista de pacotes de serviço implantados num nó de Tecido de Serviço. |
| recuperar | Indica ao cluster de Tecido de Serviço que deve tentar recuperar o serviço especificado que está atualmente preso na perda de quórum. |
| relatório-saúde | Envia um relatório de saúde sobre o serviço de tecido de serviço. |
| resolver | Resolva uma divisória de tecido de serviço. |
| lista de tipos | Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação a provisionada num cluster de Tecido de Serviço. |
| update | Atualiza o serviço especificado utilizando a descrição dada da atualização. |

## <a name="sfctl-service-app-name"></a>sfctl serviço app-name
Obtém o nome do pedido de Serviço Fabric para um serviço.

Obtém o nome do pedido para o serviço especificado. Um erro de 404 FABRIC_E_SERVICE_DOES_NOT_EXIST é devolvido se não existir um serviço com o ID de serviço fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-code-package-list"></a>sfctl serviço código-pacote-lista
Obtém a lista de pacotes de código implantados num nó de Tecido de Serviço.

Obtém a lista de pacotes de código implantados num nó de Tecido de Serviço para a aplicação dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --código-pacote-nome | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --serviço-manifesto-nome | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-create"></a>serviço sfctl criar
Cria o serviço de tecido de serviço especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o \~ ' ' caráter. Por exemplo, se o nome da aplicação for 'tecido \: /myapp/app1', a identidade da aplicação seria 'myapp \~ app1' em 6.0+ e 'myapp/app1' em versões anteriores. |
| --nome [Obrigatório] | O nome do serviço. Esta deve ser uma criança da identificação da aplicação. Este é o nome completo, incluindo o `fabric\:` URI. Por exemplo, o serviço `fabric\:/A/B` é uma criança de aplicação `fabric\:/A` . |
| --tipo de serviço [Obrigatório] | Nome do tipo de serviço. |
| --modo de ativação | O modo de ativação do pacote de serviço. |
| ...restrições | Os constrangimentos de colocação como uma corda. As restrições de colocação são expressões booleanas nas propriedades dos nónó sociais e permitem restringir um serviço a nós específicos com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós onde o NodeType é azul especifique o seguinte \: "NodeColor == azul". |
| ...-serviço correlacionado | Nome do serviço-alvo para correlacionar. |
| --correlação | Correlacione o serviço com um serviço existente utilizando uma afinidade de alinhamento. |
| --dns-nome | O nome DNS do serviço a criar. O serviço de sistema DNS do Tecido de Serviço deve ser ativado para esta definição. |
| --contagem de instâncias | A contagem de exemplos. Isto aplica-se apenas aos serviços apátridas. |
| --int-esquema | Indica que o serviço deve ser dividido uniformemente através de uma série de inteiros não assinados. |
| --int-esquema-contagem | O número de divisórias dentro da gama de chave inteiro para criar, se utilizar um esquema de partição inteiro uniforme. |
| --int-esquema-alto | O fim da gama de inteiros chave, se utilizar um esquema de partição inteiro uniforme. |
| --int-esquema-baixo | O início da gama de inteiros chave, se utilizar um esquema de partição inteiro uniforme. |
| --métricas de carga | JSON codificou lista de métricas usadas quando serviços de equilíbrio de carga em nós. |
| --min-réplica-tamanho | O tamanho mínimo de réplica definida como número. Isto aplica-se apenas aos serviços estatais. |
| ...-movimento-custo | Especifica o custo de deslocação para o serviço. Os valores possíveis são \: 'Zero', 'Baixo', 'Médio', 'Alto', 'VeryHigh'. |
| --esquema de nome | Indica que o serviço deve ter várias divisórias nomeadas. |
| --lista de esquemas nomeados | A JSON codificou a lista de nomes para dividir o serviço, se utilizar o esquema de partição nomeado. |
| --sem-persistência | Se for verdade, isto indica que o serviço não tem nenhum estado persistente armazenado no disco local, ou apenas armazena o estado na memória. |
| --lista de políticas de colocação | JSON codificou a lista de políticas de colocação para o serviço, e quaisquer nomes de domínio associados. As políticas podem ser um ou mais \: `NonPartiallyPlaceService` `PreferPrimaryDomain` de, `RequireDomain` . . `RequireDomainDistribution` . |
| --quórum-perda-espera | A duração máxima, em segundos, para a qual uma divisória pode estar num estado de perda de quórum. Isto aplica-se apenas aos serviços estatais. |
| --réplica-reiniciar-esperar | A duração, em segundos, entre quando uma réplica cai e quando uma nova réplica é criada. Isto aplica-se apenas aos serviços estatais. |
| ---escala-políticas | JSON codificou lista de políticas de escala para este serviço. |
| --serviço-colocação-tempo | A duração para a qual as réplicas podem permanecer inBuild antes de reportar que a construção está presa. Isto aplica-se apenas aos serviços estatais. |
| --singleton-esquema | Indica que o serviço deve ter uma única divisória ou ser um serviço não dividido. |
| --stand-by-replica-keep | A duração máxima, em segundos, para a qual as réplicas do StandBy serão mantidas antes de serem removidas. Isto aplica-se apenas aos serviços estatais. |
| --estado | Indica que o serviço é um serviço estatal. |
| ...apátrida | Indica que o serviço é um serviço apátrida. |
| --target-replica-set-size | A réplica do alvo definiu o tamanho como um número. Isto aplica-se apenas aos serviços estatais. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-delete"></a>serviço sfctl apagar
Elimina um serviço de tecido de serviço existente.

Um serviço deve ser criado antes de poder ser eliminado. Por predefinição, o Service Fabric tentará fechar as réplicas de serviço de forma graciosa e, em seguida, apagar o serviço. No entanto, se o serviço estiver com problemas em fechar a réplica graciosamente, a operação de eliminação pode demorar muito tempo ou ficar presa. Utilize a bandeira ForceRemove opcional para saltar a graciosa sequência de fecho e eliminar o serviço com força.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
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

## <a name="sfctl-service-deployed-type"></a>serviço sfctl implantado tipo
Obtém a informação sobre um tipo de serviço especificado da aplicação implantada num nó num cluster de Tecido de Serviço.

Obtém a lista que contém a informação sobre um tipo de serviço específico a partir das aplicações implantadas num nó num cluster de Tecido de Serviço. A resposta inclui o nome do tipo de serviço, o seu estado de registo, o pacote de código que o registou e o ID de ativação do pacote de serviços. Cada entrada representa uma ativação de um tipo de serviço, diferenciada pelo ID de ativação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --tipo de serviço [Obrigatório] | Especifica o nome de um tipo de serviço de tecido de serviço. |
| --serviço-manifesto-nome | O nome do manifesto de serviço para filtrar a lista de informações do tipo de serviço implementadas. Se especificado, a resposta apenas conterá as informações sobre tipos de serviço definidos neste manifesto de serviço. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl serviço implantado-tipo-lista
Obtém a lista que contém as informações sobre tipos de serviço das aplicações implantadas num nó num cluster de Tecido de Serviço.

Obtém a lista que contém as informações sobre tipos de serviço das aplicações implantadas num nó num cluster de Tecido de Serviço. A resposta inclui o nome do tipo de serviço, o seu estado de registo, o pacote de código que o registou e o ID de ativação do pacote de serviços.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-manifesto-nome | O nome do manifesto de serviço para filtrar a lista de informações do tipo de serviço implementadas. Se especificado, a resposta apenas conterá as informações sobre tipos de serviço definidos neste manifesto de serviço. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-description"></a>descrição do serviço sfctl
Obtém a descrição de um serviço de tecido de serviço existente.

Obtém a descrição de um serviço de tecido de serviço existente. Um serviço deve ser criado antes de obter a sua descrição.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-get-container-logs"></a>serviço sfctl get-container-logs
Obtém os registos dos contentores para o contentor colocados num nó de tecido de serviço.

Obtém os registos dos contentores para o contentor colocados num nó de tecido de serviço para o pacote de código dado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-manifesto-nome [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --anterior | Especifica se deve obter registos de contentores de recipientes de saída/desaudo da instância do pacote de código. |
| --cauda | Número de linhas para mostrar a partir do final dos registos. O padrão é 100. 'todos' para mostrar os registos completos. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-health"></a>sfctl saúde serviço
Obtém a saúde do serviço de tecido de serviço especificado.

Obtém a informação de saúde do serviço especificado. Use EventosHealthStateFilter para filtrar a recolha de eventos de saúde relatados no serviço com base no estado de saúde. Utilize o PartitionsHealthStateFilter para filtrar a recolha de divisórias devolvidas. Se especificar um serviço que não existe na loja de saúde, este pedido devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --excluir-estatísticas da saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de crianças entidades no estado de saúde Ok, Aviso e Erro. |
| --partições-saúde-estado-filtro | Permite a filtragem das divisórias objetos do estado de saúde devolvidos em resultado de consulta de saúde de serviço com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas as divisórias que correspondem ao filtro são devolvidas. Todas as divisórias são usadas para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumerações baseadas em bandeiras, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for 6, então o estado de saúde das divisórias com o valor do Estado de Saúde de OK (2) e Aviso (4) será devolvido.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-info"></a>sfctl informações de serviço
Obtém a informação sobre o serviço específico pertencente à aplicação Service Fabric.

Devolve as informações sobre o serviço especificado pertencente à aplicação de Tecido de Serviço especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-list"></a>lista de serviço sfctl
Obtém a informação sobre todos os serviços pertencentes à aplicação especificada pelo ID da aplicação.

Devolve a informação sobre todos os serviços pertencentes à aplicação especificada pelo ID da aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --continuação-token | O parâmetro de token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se enquadram numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o token de continuação não contém um valor. O valor deste parâmetro não deve ser codificado por URL. |
| --tipo de serviço-tipo | O nome do tipo de serviço usado para filtrar os serviços para consulta. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-manifest"></a>sfctl manifesto de serviço
Obtém o manifesto descrevendo um tipo de serviço.

Obtém o manifesto descrevendo um tipo de serviço. A resposta contém o manifesto de serviço XML como uma corda.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --aplicação-tipo-nome [Obrigatório] | O nome do tipo de aplicação. |
| --versão do tipo de aplicação [Necessária] | A versão do tipo de aplicação. |
| --serviço-manifesto-nome [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-package-deploy"></a>sfctl serviço de serviço de implantação
Descarrega pacotes associados a manifesto de serviço especificado para a cache de imagem no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-tipo-nome [Obrigatório] | O nome do manifesto de candidatura para o manifesto de serviço solicitado correspondente. |
| --versão tipo app [Necessária] | A versão do manifesto de candidatura para o manifesto de serviço solicitado correspondente. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-manifesto-nome [Obrigatório] | O nome do manifesto de serviço associado aos pacotes que serão descarregados. |
| --política de partilha | JSON codificou lista de políticas de partilha. Cada elemento político de partilha é composto por um 'nome' e 'âmbito'. O nome corresponde ao nome do código, configuração ou pacote de dados que deve ser partilhado. O âmbito pode ser 'Nenhum', 'All', 'Code', 'Config' ou 'Data'. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-package-health"></a>sfctl serviço pacote-saúde
Obtém a informação sobre a saúde de um pacote de serviço para uma aplicação específica implantada para um nó e aplicação de Tecido de Serviço.

Obtém a informação sobre a saúde de um pacote de serviço para uma aplicação específica implantada num nó de Tecido de Serviço. Utilize o EventsHealthStateFilter para filtrar opcionalmente a recolha de objetos HealthEvent relatados no pacote de serviço implantado com base no estado de saúde.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-pacote-nome [Obrigatório] | O nome do pacote de serviço. |
| --eventos-saúde-estado-filtro | Permite filtrar a recolha de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não for especificado, todas as entradas são devolvidas. Os valores do Estado são enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, Se o valor fornecido for 6, todos os eventos com o valor healthState de OK (2) e Aviso (4) são devolvidos.  <br> - Padrão - Valor predefinido. Corresponde a qualquer Estado da Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do HealthState. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtro que combine com a entrada com o valor HealthState Ok. O valor é 2.  <br> - Aviso - Filtro que corresponda à entrada com o valor healthState Warning. O valor é 4.  <br> - Erro - Filtrar a entrada com o Erro de Valor HealthState. O valor é 8.  <br> - Tudo - Filtrar que corresponda à entrada com qualquer valor HealthState. O valor é 65535. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-package-info"></a>sfctl serviço pacote-info
Obtém a lista de pacotes de serviço implantados num nó de Tecido de Serviço que corresponde exatamente ao nome especificado.

Devolve as informações sobre os pacotes de serviço implantados num nó de Tecido de Serviço para a aplicação dada. Estes resultados são de pacotes de serviço cujo nome corresponde exatamente ao nome do pacote de serviço especificado como parâmetro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --serviço-pacote-nome [Obrigatório] | O nome do pacote de serviço. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-package-list"></a>sfctl serviço pacote lista
Obtém a lista de pacotes de serviço implantados num nó de Tecido de Serviço.

Devolve as informações sobre os pacotes de serviço implantados num nó de Tecido de Serviço para a aplicação dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --id de aplicação [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da aplicação sem o \: esquema URI de tecido. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome da aplicação for "tecido \: /myapp/app1", a identidade da aplicação seria "myapp \~ app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome de nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-recover"></a>sfctl serviço recuperar
Indica ao cluster de Tecido de Serviço que deve tentar recuperar o serviço especificado que está atualmente preso na perda de quórum.

Indica ao cluster de Tecido de Serviço que deve tentar recuperar o serviço especificado que está atualmente preso na perda de quórum. Esta operação só deve ser efetuada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-report-health"></a>sfctl relatório de serviço-saúde
Envia um relatório de saúde sobre o serviço de tecido de serviço.

Relatórios de estado de saúde do serviço de tecido de serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e sobre os bens em que é comunicado. O relatório é enviado para um Serviço de Gateway de Tecidos de Serviço, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, verifique se o relatório aparece nos eventos de saúde do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --saúde-propriedade [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir ao repórter flexibilidade para categorizar a condição do estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem \: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --service-id [Obrigatório] | A identidade do serviço. <br><br> Este é tipicamente o nome completo do serviço sem o \: esquema URI 'fabric'. A partir da versão 6.0, os nomes hierárquicos são delimitados com o \~ ' ' caráter. Por exemplo, se o nome de serviço for 'tecido \: /myapp/app1/svc1', a identidade do serviço seria 'myapp \~ app1 \~ svc1' em 6.0+ e 'myapp/app1/svc1' em versões anteriores. |
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

## <a name="sfctl-service-resolve"></a>sfctl resolução de serviço
Resolva uma divisória de tecido de serviço.

Resolva uma divisória de serviço de tecido de serviço para obter os pontos finais das réplicas de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo do serviço sem o esquema URI de \: tecido' . A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for "tecido \: /myapp/app1/svc1", a identidade do serviço seria "myapp \~ app1 \~ svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --partição-tipo-chave | Tipo chave para a partição. Este parâmetro é necessário se o esquema de partição do serviço for Int64Range ou Nomeado. Os valores possíveis estão a seguir-se. - Nenhum (1) - Indica que o parâmetro PartitionKeyValue não está especificado. Isto é válido para as divisórias com esquema de partição como Singleton. Este é o valor predefinido. O valor é 1. - Int64Range (2) - Indica que o parâmetro PartitionKeyValue é uma chave de partição int64. Isto é válido para as divisórias com esquema de partição como Int64Range. O valor é 2. - Nome (3) - Indica que o parâmetro PartitionKeyValue é um nome da partição. Isto é válido para as divisórias com esquema de partição como Nomeado. O valor é 3. |
| --valor-chave partição | Chave de partição. Isto é necessário se o esquema de partição para o serviço for Int64Range ou Nomeado. Este não é o ID da partição, mas sim, ou o valor-chave inteiro, ou o nome do ID da partição. Por exemplo, se o seu serviço estiver a utilizar divisórias de 0 a 10, então a PartitionKeyValue seria um inteiro nessa gama. Descrição do serviço de consulta para ver o alcance ou o nome. |
| --versão anterior-rsp- | O valor no campo versão da resposta que foi recebida anteriormente. Isto é necessário se o utilizador souber que o resultado que foi obtido anteriormente é velho. |
| --timeout -t | O tempo limite do servidor para a realização da operação em segundos. Este prazo estipula a duração que o cliente está disposto a esperar pela conclusão da operação solicitada. O valor predefinido para este parâmetro é de 60 segundos.  Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |

## <a name="sfctl-service-type-list"></a>lista de tipos de serviço sfctl
Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação a provisionada num cluster de Tecido de Serviço.

Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação a provisionada num cluster de Tecido de Serviço. O tipo de aplicação fornecido deve existir. Caso contrário, um estatuto 404 é devolvido.

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

## <a name="sfctl-service-update"></a>atualização de serviço sfctl
Atualiza o serviço especificado utilizando a descrição dada da atualização.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este é tipicamente o nome completo do serviço sem o \: esquema URI 'fabric'. A partir da versão 6.0, os nomes hierárquicos são delimitados com o " \~ " " personagem. Por exemplo, se o nome de serviço for 'tecido \: /myapp/app1/svc1', a identidade do serviço seria 'myapp \~ app1 \~ svc1' em 6.0+ e 'myapp/app1/svc1' em versões anteriores. |
| ...restrições | Os constrangimentos de colocação como uma corda. As restrições de colocação são expressões booleanas nas propriedades dos nónó sociais e permitem restringir um serviço a nós específicos com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós onde o NodeType é azul especifique o seguinte \: "NodeColor == azul". |
| ...-serviço correlacionado | Nome do serviço-alvo para correlacionar. |
| --correlação | Correlacione o serviço com um serviço existente utilizando uma afinidade de alinhamento. |
| --contagem de instâncias | A contagem de exemplos. Isto aplica-se apenas aos serviços apátridas. |
| --métricas de carga | JSON codificou a lista de métricas utilizadas quando a carga equilibrando os nós. |
| --min-réplica-tamanho | O tamanho mínimo de réplica definida como número. Isto aplica-se apenas aos serviços estatais. |
| ...-movimento-custo | Especifica o custo de deslocação para o serviço. Os valores possíveis são \: 'Zero', 'Baixo', 'Médio', 'Alto', 'VeryHigh'. |
| --lista de políticas de colocação | JSON codificou a lista de políticas de colocação para o serviço, e quaisquer nomes de domínio associados. As políticas podem ser um ou mais \: `NonPartiallyPlaceService` `PreferPrimaryDomain` de, `RequireDomain` . . `RequireDomainDistribution` . |
| --quórum-perda-espera | A duração máxima, em segundos, para a qual uma divisória pode estar num estado de perda de quórum. Isto aplica-se apenas aos serviços estatais. |
| --réplica-reiniciar-esperar | A duração, em segundos, entre quando uma réplica cai e quando uma nova réplica é criada. Isto aplica-se apenas aos serviços estatais. |
| ---escala-políticas | JSON codificou lista de políticas de escala para este serviço. |
| --serviço-colocação-tempo | A duração para a qual as réplicas podem permanecer inBuild antes de reportar que a construção está presa. Isto aplica-se apenas aos serviços estatais. |
| --stand-by-replica-keep | A duração máxima, em segundos, para a qual as réplicas do StandBy serão mantidas antes de serem removidas. Isto aplica-se apenas aos serviços estatais. |
| --estado | Indica que o serviço de alvo é um serviço estatal. |
| ...apátrida | Indica que o serviço de alvo é um serviço apátrida. |
| --target-replica-set-size | A réplica do alvo definiu o tamanho como um número. Isto aplica-se apenas aos serviços estatais. |
| --timeout -t | Padrão \: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depurar | Aumente a verbosidade do registo para mostrar todos os registos de depurg. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --output -o | Formato de saída.  Valores permitidos \: json, jsonc, mesa, tsv.  \:Json padrão. |
| -consulta | Cadeia de consulta JMESPath. Consulte http \: //jmespath.org/ para obter mais informações e exemplos. |
| -verbose | Aumentar a verbosidade do registo. Use -depurg para registos completos de depurg. |


## <a name="next-steps"></a>Passos seguintes
- [Configurar](service-fabric-cli.md) o CLI de Tecido de Serviço.
- Saiba como utilizar o CLI do tecido de serviço utilizando os [scripts](./scripts/sfctl-upgrade-application.md)da amostra .
