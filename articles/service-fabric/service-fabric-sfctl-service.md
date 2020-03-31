---
title: Serviço de serviço Azure CLI-serviço sfctl
description: Conheça o sfctl, a interface de linha de comando Azure Service Fabric. Inclui uma lista de comandos para gestão de serviços, tipos de serviçoe pacotes de serviços.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906232"
---
# <a name="sfctl-service"></a>sfctl service
Criar, eliminar e gerir os serviços, os tipos de serviço e os pacotes de serviço.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| nome de aplicativo | Obtém o nome da aplicação Service Fabric para um serviço. |
| código-pacote-lista | Obtém a lista de pacotes de código implantados num nó de Tecido de Serviço. |
| criar | Cria o serviço de tecido de serviço especificado. |
| delete | Elimina um serviço de tecido de serviço existente. |
| tipo implantado | Obtém a informação sobre um tipo de serviço especificado da aplicação implantada num nó num cluster de Tecido de Serviço. |
| lista de tipo implantado | Obtém a lista que contém as informações sobre tipos de serviço das aplicações implantadas num nó num cluster de Tecido de Serviço. |
| descrição | Obtém a descrição de um serviço de tecido de serviço existente. |
| get-contentor-logs | Coloca os troncos do recipiente para o recipiente implantados num nó de tecido de serviço. |
| saúde | Obtém a saúde do serviço de tecido de serviço especificado. |
| informações | Obtém informações sobre o serviço específico pertencente à aplicação Service Fabric. |
| list | Obtém a informação sobre todos os serviços pertencentes à aplicação especificada pelo ID da aplicação. |
| manifest | Obtém o manifesto descrevendo um tipo de serviço. |
| pacote-implantação | Descarrega pacotes associados ao manifesto de serviço especificado para a cache de imagem no nó especificado. |
| package-saúde | Obtém informações sobre a saúde de um pacote de serviço para uma aplicação específica implementada para um nó de Tecido de Serviço e aplicação. |
| pacote-info | Obtém a lista de pacotes de serviço implantados num nó de Tecido de Serviço que corresponda exatamente ao nome especificado. |
| lista de pacotes | Obtém a lista de pacotes de serviço saqueados num nó de Tecido de Serviço. |
| recuperar | Indica ao cluster Service Fabric que deve tentar recuperar o serviço especificado que está atualmente preso na perda de quórum. |
| relatório-saúde | Envia um relatório de saúde sobre o serviço de tecido de serviço. |
| resolver | Resolva uma divisória de tecido de serviço. |
| lista de tipos | Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação provisionado num cluster de Tecido de Serviço. |
| update | Atualiza o serviço especificado utilizando a descrição da atualização dada. |

## <a name="sfctl-service-app-name"></a>sfctl serviço app-nome
Obtém o nome da aplicação Service Fabric para um serviço.

Obtém o nome do pedido para o serviço especificado. Um erro de 404 FABRIC_E_SERVICE_DOES_NOT_EXIST é devolvido se não existir um serviço com o ID de serviço fornecido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl código-pacote-lista
Obtém a lista de pacotes de código implantados num nó de Tecido de Serviço.

Obtém a lista de pacotes de código implantados num nó de Tecido de Serviço para a aplicação dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --código-pacote-nome | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --nome manifesto de serviço | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-create"></a>serviço sfctl criar
Cria o serviço de tecido de serviço especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o personagem ' ' . Por exemplo, se o nome\:da aplicação for 'fabric /myapp/app1', a identidade da aplicação seria 'myapp\~app1' em 6.0+ e 'myapp/app1' em versões anteriores. |
| --nome [Obrigatório] | Nome do serviço. Esta deve ser uma criança do ID da aplicação. Este é o nome `fabric\:` completo, incluindo o URI. Por exemplo, o `fabric\:/A/B` serviço `fabric\:/A`é uma criança de aplicação. |
| -tipo de serviço [Obrigatório] | Nome do tipo de serviço. |
| --modo de ativação | O modo de ativação do pacote de serviço. |
| -constrangimentos | Os constrangimentos de colocação como uma corda. Os constrangimentos de colocação são expressões booleanas nas propriedades do nó e permitem restringir um serviço a determinados nódosos com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós onde o\:NodeType é azul especifique o seguinte "NodeColor == azul". |
| -serviço correlacionado | Nome do serviço alvo para se relacionar. |
| -- correlação | Correlacionar o serviço com um serviço existente utilizando uma afinidade de alinhamento. |
| --dns-name | O nome DNS do serviço a criar. O sistema DNS de tecido de serviço deve ser ativado para esta definição. |
| -contagem de instâncias | A contagem de casos. Isto aplica-se apenas aos serviços apátridas. |
| -int-esquema | Indica que o serviço deve ser uniformemente dividido através de uma gama de inteiros não assinados. |
| --int-esquema-contagem | O número de divisórias dentro da gama de teclas inteiros para criar, se utilizar um esquema de partição inteiro uniforme. |
| --int-esquema-alto | A extremidade da gama de inteiros chave, se utilizar um esquema de partição de inteiros uniforme. |
| --int-esquema-baixo | O início da gama de inteiros chave, se utilizar um esquema de partição de inteiros uniforme. |
| --métricas de carga | JSON codificada lista de métricas usadas quando os serviços de equilíbrio de carga em nódosos. |
| --min-replica-set-size | O tamanho mínimo da réplica como número. Isto aplica-se apenas a serviços estatais. |
| --mover-custo | Especifica o custo de mudança para o serviço. Os valores possíveis são\: 'Zero', 'Low', 'Medium', 'High', 'VeryHigh'. |
| --esquema nomeado | Indica que o serviço deve ter várias divisórias nomeadas. |
| --nomeado-esquema-lista | JSON codificada lista de nomes para dividir o serviço através, se utilizar o esquema de partição nomeado. |
| - -sem-persistir-estado | Se for verdade, isto indica que o serviço não tem estado persistente armazenado no disco local, ou apenas armazena o estado na memória. |
| --lista de políticas de colocação | JSON codificada lista de políticas de colocação para o serviço, e quaisquer nomes de domínio associados. As políticas podem ser\: `NonPartiallyPlaceService` `PreferPrimaryDomain`uma `RequireDomain` `RequireDomainDistribution`ou mais de. |
| -quorum-perda-espera | A duração máxima, em segundos, para a qual uma partição é permitida em estado de perda de quórum. Isto aplica-se apenas a serviços estatais. |
| -- réplica-reinício-espera | A duração, em segundos, entre quando uma réplica desce e quando uma nova réplica é criada. Isto aplica-se apenas a serviços estatais. |
| --políticas de escala | JSON codificada lista de políticas de escala para este serviço. |
| --tempo de colocação de serviço | A duração pela qual as réplicas podem ficar inBuild antes de informar que a construção está presa. Isto aplica-se apenas a serviços estatais. |
| --esquema singleton | Indica que o serviço deve ter uma única divisória ou ser um serviço não dividido. |
| -stand-by-replica-keep | A duração máxima, em segundos, para a qual as réplicas de StandBy serão mantidas antes de serem removidas. Isto aplica-se apenas a serviços estatais. |
| -estado | Indica que o serviço é um serviço imponente. |
| -apátrida | Indica que o serviço é um serviço apátrida. |
| ---target-replica-set-size | A réplica alvo definiu o tamanho como um número. Isto aplica-se apenas a serviços estatais. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-delete"></a>serviço sfctl apagar
Elimina um serviço de tecido de serviço existente.

Deve ser criado um serviço antes de poder ser eliminado. Por padrão, o Service Fabric tentará fechar as réplicas de serviço de forma graciosa e, em seguida, eliminar o serviço. No entanto, se o serviço estiver com problemas de fecho da réplica graciosamente, a operação de eliminação pode demorar muito tempo ou ficar presa. Utilize a bandeira opcional ForceRemove para saltar a graciosa sequência de fecho e apagar vigorosamente o serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
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

## <a name="sfctl-service-deployed-type"></a>serviço sfctl implementado tipo
Obtém a informação sobre um tipo de serviço especificado da aplicação implantada num nó num cluster de Tecido de Serviço.

Obtém a lista que contém a informação sobre um tipo de serviço específico a partir das aplicações implantadas num nó num cluster de Tecido de Serviço. A resposta inclui o nome do tipo de serviço, o seu estado de registo, o pacote de código que o registou e a identificação ativada do pacote de serviço. Cada entrada representa uma ativação de um tipo de serviço, diferenciada pelo ID de ativação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome do tipo de serviço [Obrigatório] | Especifica o nome de um tipo de serviço de tecido de serviço. |
| --nome manifesto de serviço | O nome do manifesto de serviço para filtrar a lista de informações do tipo de serviço implantados. Se especificada, a resposta apenas conterá as informações sobre os tipos de serviço que são definidas neste manifesto de serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-deployed-type-list"></a>serviço sfctl implantado-tipo-lista
Obtém a lista que contém as informações sobre tipos de serviço das aplicações implantadas num nó num cluster de Tecido de Serviço.

Obtém a lista que contém as informações sobre tipos de serviço das aplicações implantadas num nó num cluster de Tecido de Serviço. A resposta inclui o nome do tipo de serviço, o seu estado de registo, o pacote de código que o registou e a identificação ativada do pacote de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço | O nome do manifesto de serviço para filtrar a lista de informações do tipo de serviço implantados. Se especificada, a resposta apenas conterá as informações sobre os tipos de serviço que são definidas neste manifesto de serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-description"></a>descrição do serviço sfctl
Obtém a descrição de um serviço de tecido de serviço existente.

Obtém a descrição de um serviço de tecido de serviço existente. Deve ser criado um serviço antes de a sua descrição poder ser obtida.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl serviço get-container-logs
Coloca os troncos do recipiente para o recipiente implantados num nó de tecido de serviço.

Coloca os registos do recipiente para o recipiente implantados num nó de tecido de serviço para a embalagem de código dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --código-pacote-nome [Obrigatório] | O nome do pacote de código especificado no manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| -- anterior | Especifica se obtém registos de contentores de recipientes de contentores de saída/cadáveres da instância do pacote de código. |
| --cauda | Número de linhas para mostrar a partir do fim dos registos. O padrão é 100. 'todos' para mostrar os registos completos. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-health"></a>sfctl saúde de serviço
Obtém a saúde do serviço de tecido de serviço especificado.

Obtém a informação de saúde do serviço especificado. Use EventsHealthStateFilter para filtrar a recolha de eventos de saúde relatados no serviço com base no estado de saúde. Utilize partitionsHealthStateFilter para filtrar a recolha de divisórias devolvidas. Se especificar um serviço que não existe na loja de saúde, este pedido devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| -excluir-estatísticas de saúde | Indica se as estatísticas de saúde devem ser devolvidas como parte do resultado da consulta. Falso por defeito. As estatísticas mostram o número de entidades infantis no estado de saúde Ok, Warning e Error. |
| --divisórias-filtro estado-saúde | Permite a filtragem das divisórias objetos do estado de saúde devolvidos em resultado de consulta de saúde de serviço com base no seu estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Apenas divisórias que correspondam ao filtro são devolvidas. Todas as divisórias são usadas para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada na bandeira, pelo que o valor pode ser uma combinação destes valores obtidos utilizando o operador "OR" bitwise. Por exemplo, se o valor fornecido for de 6, então o estado de saúde das divisórias com o valor do Estado de Saúde de OK (2) e Aviso (4) será devolvido.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-info"></a>informação de serviço sfctl
Obtém informações sobre o serviço específico pertencente à aplicação Service Fabric.

Devolve as informações sobre o serviço especificado pertencente à aplicação especificada Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
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

## <a name="sfctl-service-list"></a>lista de serviço sfctl
Obtém a informação sobre todos os serviços pertencentes à aplicação especificada pelo ID da aplicação.

Devolve as informações sobre todos os serviços pertencentes à aplicação especificada pelo ID da aplicação.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --continuação-token | O parâmetro simbólico de continuação é utilizado para obter o próximo conjunto de resultados. Um símbolo de continuação com um valor não vazio é incluído na resposta da API quando os resultados do sistema não se encaixam numa única resposta. Quando este valor é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Se não houver mais resultados, então o sinal de continuação não contém um valor. O valor deste parâmetro não deve ser codificado. |
| --nome do tipo de serviço | O nome do tipo de serviço usado para filtrar os serviços para consulta. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-manifest"></a>manifesto de serviço sfctl
Obtém o manifesto descrevendo um tipo de serviço.

Obtém o manifesto descrevendo um tipo de serviço. A resposta contém o manifesto de serviço XML como uma corda.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do tipo de aplicação [Obrigatório] | O nome do tipo de candidatura. |
| --aplicação-tipo-versão [Obrigatório] | A versão do tipo de aplicação. |
| --nome manifesto de serviço [Obrigatório] | O nome de um manifesto de serviço registado como parte de um tipo de aplicação num cluster de Tecido de Serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-package-deploy"></a>sfctl serviço pacote-deploy
Descarrega pacotes associados ao manifesto de serviço especificado para a cache de imagem no nó especificado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --app-tipo-nome [Obrigatório] | O nome do manifesto de inscrição para o manifesto de serviço solicitado correspondente. |
| --app-type-versão [Obrigatório] | A versão do manifesto de candidatura para o manifesto de serviço solicitado correspondente. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome manifesto de serviço [Obrigatório] | O manifesto de serviço associado aos pacotes que serão descarregados. |
| --política de partilha | JSON codificada lista de políticas de partilha. Cada elemento político de partilha é composto por um "nome" e um "âmbito". O nome corresponde ao nome do código, configuração ou pacote de dados que deve ser partilhado. O âmbito pode ser "Nenhum", "Tudo", "Código", "Config" ou "Dados". |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-package-health"></a>sfctl serviço pacote-saúde
Obtém informações sobre a saúde de um pacote de serviço para uma aplicação específica implementada para um nó de Tecido de Serviço e aplicação.

Obtém informações sobre a saúde de um pacote de serviço para uma aplicação específica implantada num nó de Tecido de Serviço. Utilize o EventsHealthStateFilter para filtrar opcionalmente para a recolha de objetos HealthEvent relatados no pacote de serviço implantado com base no estado de saúde.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome do pacote de serviço [Obrigatório] | O nome do pacote de serviço. |
| -eventos-saúde-filtro estado-estado | Permite filtrar a coleção de objetos HealthEvent devolvidos com base no estado de saúde. Os valores possíveis para este parâmetro incluem o valor inteiro de um dos seguintes estados de saúde. Só os eventos que combinam com o filtro são devolvidos. Todos os eventos são usados para avaliar o estado de saúde agregado. Se não especificadas, todas as entradas são devolvidas. Os valores do Estado são uma enumeração baseada em bandeiras, pelo que o valor pode ser uma combinação destes valores, obtidos utilizando o operador bitwise 'OR'. Por exemplo, se o valor fornecido for de 6, todos os eventos com o valor do Estado de Saúde de OK (2) e Aviso (4) são devolvidos.  <br> - Predefinição - Valor predefinido. Corresponde a qualquer Estado de Saúde. O valor é zero.  <br> - Nenhum - Filtro que não corresponda a qualquer valor do Estado de Saúde. Usado para não devolver resultados numa determinada coleção de estados. O valor é 1.  <br> - Ok - Filtrar que corresponde à entrada com o valor do Estado de Saúde Ok. O valor é 2.  <br> - Aviso - Filtrar a entrada que corresponde ao valor do Estado de Saúde Aviso. O valor é 4.  <br> - Erro - Filtrar a entrada que corresponde à entrada com o erro de valor do Estado de Saúde. O valor é 8.  <br> - Tudo - Filtrar a entrada que corresponde a qualquer valor do Estado de Saúde. O valor é 65535. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-package-info"></a>sfctl serviço pacote-info
Obtém a lista de pacotes de serviço implantados num nó de Tecido de Serviço que corresponda exatamente ao nome especificado.

Devolve as informações sobre os pacotes de serviço implantados num nó de Tecido de Serviço para a aplicação dada. Estes resultados são de pacotes de serviço cujo nome corresponde exatamente ao nome do pacote de serviço especificado como parâmetro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --nome do pacote de serviço [Obrigatório] | O nome do pacote de serviço. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
Obtém a lista de pacotes de serviço saqueados num nó de Tecido de Serviço.

Devolve as informações sobre os pacotes de serviço implantados num nó de Tecido de Serviço para a aplicação dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do pedido. Este é tipicamente o nome completo da\:aplicação sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:da aplicação for "tecido /myapp/app1", a identidade da aplicação seria "myapp\~app1" em 6.0+ e "myapp/app1" em versões anteriores. |
| --nome do nó [Obrigatório] | O nome do nó. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-recover"></a>serviço sfctl recuperar
Indica ao cluster Service Fabric que deve tentar recuperar o serviço especificado que está atualmente preso na perda de quórum.

Indica ao cluster Service Fabric que deve tentar recuperar o serviço especificado que está atualmente preso na perda de quórum. Esta operação só deve ser executada se se souber que as réplicas que estão em baixo não podem ser recuperadas. A utilização incorreta desta API pode causar uma potencial perda de dados.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>relatório de serviço sfctl-saúde
Envia um relatório de saúde sobre o serviço de tecido de serviço.

Informa o estado de saúde do serviço de tecido de serviço especificado. O relatório deve conter as informações sobre a origem do relatório de saúde e dos bens em que é comunicado. O relatório é enviado para um Serviço de Gateway Service Fabric, que reencaminha para a loja de saúde. O relatório pode ser aceite pela porta de entrada, mas rejeitado pela loja de saúde após validação extra. Por exemplo, a loja de saúde pode rejeitar o relatório por causa de um parâmetro inválido, como um número de sequência velha. Para ver se o relatório foi aplicado na loja de saúde, verifique se o relatório aparece nos eventos de saúde do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --propriedade de saúde [Necessária] | A propriedade da informação de saúde. <br><br> Uma entidade pode ter relatórios de saúde para diferentes propriedades. A propriedade é uma corda e não uma enumeração fixa para permitir que a flexibilidade do repórter categorize a condição do Estado que desencadeia o relatório. Por exemplo, um repórter com SourceId "LocalWatchdog" pode monitorizar o estado do disco disponível num nó, para que possa reportar a propriedade "AvailableDisk" nesse nó. O mesmo repórter pode monitorizar a conectividade do nó, para que possa reportar uma propriedade "Conectividade" no mesmo nó. Na loja de saúde, estes relatórios são tratados como eventos de saúde separados para o nó especificado. Juntamente com o SourceId, a propriedade identifica exclusivamente a informação de saúde. |
| --estado de saúde [Obrigatório] | Os valores possíveis incluem\: 'Inválido', 'Ok', 'Aviso', 'Erro', 'Desconhecido'. |
| --service-id [Obrigatório] | A identidade do serviço. <br><br> Este é tipicamente o nome completo do\:serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o personagem ' ' . Por exemplo, se o nome\:do serviço for 'fabric /myapp/app1/svc1', a identidade do serviço seria 'myapp\~app1\~svc1' em 6.0+ e 'myapp/app1/svc1' em versões anteriores. |
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

## <a name="sfctl-service-resolve"></a>sfctl resolver serviço
Resolva uma divisória de tecido de serviço.

Resolva uma partição de serviço de serviço de tecido de serviço para obter os pontos finais das réplicas de serviço.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este ID é tipicamente o nome completo\:do serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for "tecido /myapp/app1/svc1", a identidade de serviço seria "myapp\~app1\~svc1" em 6.0+ e "myapp/app1/svc1" em versões anteriores. |
| --tipo de partilha-chave | Tipo chave para a partição. Este parâmetro é necessário se o esquema de partição para o serviço for Int64Range ou Nomeado. Os valores possíveis estão a seguir-se. - Nenhum (1) - Indica que o parâmetro PartitionKeyValue não está especificado. Isto é válido para as divisórias com esquema de partição como Singleton. Este é o valor predefinido. O valor é 1. - Int64Range (2) - Indica que o parâmetro PartitionKeyValue é uma chave de partição int64. Isto é válido para as divisórias com esquema de partição como Int64Range. O valor é 2. - Nomeado (3) - Indica que o parâmetro PartitionKeyValue é um nome da partição. Isto é válido para as divisórias com esquema de partição como Nomeado. O valor é 3. |
| --divisória-chave-valor | Chave de partição. Isto é necessário se o esquema de partição para o serviço for Int64Range ou Nomeado. Este não é o ID da partição, mas sim o valor chave inteiro, ou o nome do ID de partição. Por exemplo, se o seu serviço estiver a utilizar divisórias variadas de 0 a 10, então eles PartitionKeyValue seriam um inteiro nessa gama. Descrição do serviço de consulta para ver o alcance ou nome. |
| --versão anterior-rsp | O valor no campo versão da resposta que foi recebida anteriormente. Isto é necessário se o utilizador souber que o resultado que foi obtido anteriormente é insoado. |
| --timeout -t | O tempo de funcionamento do servidor para a execução da operação em segundos. Este prazo especifica a duração do tempo que o cliente está disposto a esperar que a operação solicitada esteja concluída. O valor padrão para este parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |

## <a name="sfctl-service-type-list"></a>sfctl serviço lista tipo
Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação provisionado num cluster de Tecido de Serviço.

Obtém a lista que contém as informações sobre tipos de serviço que são suportados por um tipo de aplicação provisionado num cluster de Tecido de Serviço. O tipo de aplicação previsto deve existir. Caso contrário, um estatuto de 404 é devolvido.

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

## <a name="sfctl-service-update"></a>atualização de serviço sfctl
Atualiza o serviço especificado utilizando a descrição da atualização dada.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --service-id [Obrigatório] | A identidade do serviço. Este é tipicamente o nome completo do\:serviço sem o esquema URI 'fabric ' URI. A partir da versão 6.0, os nomes\~hierárquicos são delimitados com o " personagem ". Por exemplo, se o nome\:do serviço for 'fabric /myapp/app1/svc1', a identidade do serviço seria 'myapp\~app1\~svc1' em 6.0+ e 'myapp/app1/svc1' em versões anteriores. |
| -constrangimentos | Os constrangimentos de colocação como uma corda. Os constrangimentos de colocação são expressões booleanas nas propriedades do nó e permitem restringir um serviço a determinados nódosos com base nos requisitos de serviço. Por exemplo, para colocar um serviço em nós onde o\: NodeType é azul especifique o seguinte "NodeColor == azul". |
| -serviço correlacionado | Nome do serviço alvo para se relacionar. |
| -- correlação | Correlacionar o serviço com um serviço existente utilizando uma afinidade de alinhamento. |
| -contagem de instâncias | A contagem de casos. Isto aplica-se apenas aos serviços apátridas. |
| --métricas de carga | JSON codificada lista de métricas usadas quando a carga equilibra os nódosos. |
| --min-replica-set-size | O tamanho mínimo da réplica como número. Isto aplica-se apenas a serviços estatais. |
| --mover-custo | Especifica o custo de mudança para o serviço. Os valores possíveis são\: 'Zero', 'Low', 'Medium', 'High', 'VeryHigh'. |
| --lista de políticas de colocação | JSON codificada lista de políticas de colocação para o serviço, e quaisquer nomes de domínio associados. As políticas podem ser\: `NonPartiallyPlaceService` `PreferPrimaryDomain`uma `RequireDomain` `RequireDomainDistribution`ou mais de. |
| -quorum-perda-espera | A duração máxima, em segundos, para a qual uma partição é permitida em estado de perda de quórum. Isto aplica-se apenas a serviços estatais. |
| -- réplica-reinício-espera | A duração, em segundos, entre quando uma réplica desce e quando uma nova réplica é criada. Isto aplica-se apenas a serviços estatais. |
| --políticas de escala | JSON codificada lista de políticas de escala para este serviço. |
| --tempo de colocação de serviço | A duração pela qual as réplicas podem ficar inBuild antes de informar que a construção está presa. Isto aplica-se apenas a serviços estatais. |
| -stand-by-replica-keep | A duração máxima, em segundos, para a qual as réplicas de StandBy serão mantidas antes de serem removidas. Isto aplica-se apenas a serviços estatais. |
| -estado | Indica que o serviço alvo é um serviço imponente. |
| -apátrida | Indica que o serviço alvo é um serviço apátrida. |
| ---target-replica-set-size | A réplica alvo definiu o tamanho como um número. Isto aplica-se apenas a serviços estatais. |
| --timeout -t | Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos Globais

|Argumento|Descrição|
| --- | --- |
| --depuração | Aumente a verbosidade da exploração madeireira para mostrar todos os registos de depuração. |
| --ajuda -h | Mostre esta mensagem de ajuda e saia. |
| --saída -o | Formato de saída.  Valores\: permitidos json, jsonc, mesa, tsv.  Json padrão.\: |
| -- consulta | Corda de consulta JMESPath. Consulte\:http //jmespath.org/ para obter mais informações e exemplos. |
| -verbosa | Aumente a verbosidade da exploração madeireira. Utilize -depurar os registos completos de depuração. |


## <a name="next-steps"></a>Passos seguintes
- [Instale](service-fabric-cli.md) o CLI de tecido de serviço.
- Aprenda a utilizar o CLI de tecido de serviço utilizando as [scripts de amostra](/azure/service-fabric/scripts/sfctl-upgrade-application).
