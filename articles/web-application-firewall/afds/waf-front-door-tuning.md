---
title: Firewall de aplicação web de afinação (WAF) para Azure Front Door
description: Neste artigo, aprende-se a sintonizar o WAF para a Porta da Frente.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 21550cc34b21756186ea607c3efd2ebd10cbf979
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214257"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Firewall de aplicação web de afinação (WAF) para Azure Front Door
 
O Conjunto de Regras Padrão gerido pelo Azure baseia-se no [Conjunto de Regras Fundamentais (CRS) da OWASP](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) e foi concebido para ser rigoroso fora da caixa. Espera-se frequentemente que as regras da WAF sejam ajustadas de acordo com as necessidades específicas da aplicação ou organização que utilizam o WAF. Isto é geralmente conseguido definindo exclusões de regras, criando regras personalizadas, e até mesmo desativando regras que podem estar a causar problemas ou falsos positivos. Há algumas coisas que pode fazer se os pedidos que devem passar pela sua Firewall de Aplicação Web (WAF) estiverem bloqueados.

Em primeiro lugar, certifique-se de que leu a [visão geral](afds-overview.md) da Porta frontal e a política da WAF para documentos [da porta da frente.](waf-front-door-create-portal.md) Além disso, certifique-se de que ativou [a monitorização e registo da WAF](waf-front-door-monitor.md). Estes artigos explicam como funciona a WAF, como funciona a regra WAF e como aceder aos registos da WAF.
 
## <a name="understanding-waf-logs"></a>Compreender registos WAF
 
O objetivo dos registos WAF é mostrar todos os pedidos que sejam correspondidos ou bloqueados pela WAF. É uma coleção de todos os pedidos avaliados que são combinados ou bloqueados. Se notar que o WAF bloqueia um pedido que não deve (um falso positivo), pode fazer algumas coisas. Primeiro, reduza e encontre o pedido específico. Se desejar, pode [configurar uma mensagem de resposta personalizada](./waf-front-door-configure-custom-response-code.md) para incluir o campo para identificar `trackingReference` facilmente o evento e realizar uma consulta de registo nesse valor específico. Procure nos registos para encontrar o URI específico, o timetamp ou o computador do cliente IP do pedido. Quando encontrar as entradas de registo relacionadas, pode começar a agir com falsos positivos. 
 
Por exemplo, digamos que tem um tráfego legítimo contendo a corda `1=1` que pretende passar através do seu WAF. Aqui está o que o pedido parece:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Se experimentar o pedido, o WAF bloqueia o tráfego que contém a sua corda *1=1* em qualquer parâmetro ou campo. Esta é uma corda frequentemente associada a um ataque de injeção SQL. Pode consultar os registos e ver o tempo de verificação do pedido e as regras que bloqueiam/correspondem.
 
No exemplo seguinte, exploramos um `FrontdoorWebApplicationFirewallLog` registo gerado devido a uma correspondência de regras. A seguinte consulta log analytics pode ser usada para encontrar pedidos que tenham sido bloqueados nas últimas 24 horas:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
No `requestUri` campo, pode ver-se que o pedido foi feito `/api/Feedbacks/` especificamente. Indo mais longe, encontramos a identificação da regra `942110` no `ruleName` campo. Conhecendo a regra ID, você pode ir ao [OWASP ModSecurity Core Rule Definir Repositório Oficial](https://github.com/coreruleset/coreruleset) e pesquisar por essa [regra ID](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) para rever o seu código e entender exatamente o que esta regra corresponde. 
 
Em seguida, verificando o `action` campo, vemos que esta regra está definida para bloquear pedidos após a correspondência, e confirmamos que o pedido foi, de facto, bloqueado pela WAF porque `policyMode` o conjunto está definido para `prevention` . 
 
Agora, vamos verificar a informação no `details` campo. É aqui que se pode ver a `matchVariableName` `matchVariableValue` informação. Aprendemos que esta regra foi desencadeada porque alguém inseriu *1=1* no `comment` campo da aplicação web.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Há também valor na verificação dos registos de acesso para expandir o seu conhecimento sobre um determinado evento WAF. Abaixo revemos o `FrontdoorAccessLog` log que foi gerado como resposta ao evento acima.
 
Pode ver que estes são registos relacionados com base no `trackingReference` valor que é o mesmo. Entre vários campos que fornecem informações gerais, tais como `userAgent` `clientIP` e, chamamos a atenção para os `httpStatusCode` campos e `httpStatusDetails` campos. Aqui, podemos confirmar que o cliente recebeu uma resposta HTTP 403, o que confirma absolutamente que este pedido foi negado e bloqueado. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Resolução de falsos positivos
 
Para tomar uma decisão informada sobre como lidar com um falso positivo, é importante familiarizar-se com as tecnologias que a sua aplicação utiliza. Por exemplo, digamos que não há um servidor SQL na sua pilha de tecnologia, e que está a receber falsos positivos relacionados com essas regras. Desativar essas regras não enfraquece necessariamente a sua segurança. 

Com esta informação, e o conhecimento de que a regra 942110 é a que corresponde à `1=1` corda no nosso exemplo, podemos fazer algumas coisas para impedir que este pedido legítimo seja bloqueado:
 
* Utilizar listas de exclusão
  * Consulte [firewall de aplicação web (WAF) com listas de exclusão do Serviço de Porta Frontal](waf-front-door-exclusion.md) para obter mais informações sobre listas de exclusão. 
* Alterar ações da WAF
  * Consulte [as Ações da WAF](afds-overview.md#waf-actions) para obter mais informações sobre que ações podem ser tomadas quando um pedido corresponde às condições de uma regra.
* Use regras personalizadas
  * Consulte [as regras personalizadas para firewall de aplicação web com porta frontal Azure](waf-front-door-custom-rules.md) para obter mais informações sobre regras personalizadas.
* Desativar regras 

> [!TIP]
> Ao selecionar uma abordagem para permitir pedidos legítimos através da WAF, tente torná-lo o mais estreito possível. Por exemplo, é melhor usar uma lista de exclusão do que desativar uma regra inteiramente.

### <a name="using-exclusion-lists"></a>Utilização de listas de exclusão

Um benefício da utilização de uma lista de exclusão é que apenas a variável de correspondência que seleciona para excluir deixará de ser inspecionada para esse pedido dado. Ou seja, pode escolher entre cabeçalhos de pedido específicos, solicitar cookies, argumentar argumentos de argumento de cartão de corpo para ser excluído se uma determinada condição for satisfeita, em oposição a excluir todo o pedido de inspeção. As outras variáveis não especificadas do pedido continuarão a ser inspecionadas normalmente.
 
É importante considerar que as exclusões são um cenário global. Isto significa que a exclusão configurada se aplicará a todo o tráfego que passa pela sua WAF, e não apenas a uma aplicação web específica ou uri. Por exemplo, isto pode ser uma preocupação se *1=1* é um pedido válido no organismo para uma determinada aplicação web, mas não para outros sob a mesma política da WAF. Se fizer sentido utilizar diferentes listas de exclusão para diferentes aplicações, considere usar diferentes políticas waf para cada aplicação e aplicá-las no frontend de cada aplicação.
 
Ao configurar listas de exclusão para regras geridas, pode optar por excluir todas as regras dentro de um conjunto de regras, todas as regras dentro de um grupo de regras ou uma regra individual. Uma lista de exclusão pode ser configurada usando [PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0) [Azure CLI,](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add) [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)ou o portal Azure.

* Exclusões ao nível de regras
  * A aplicação de exclusões a um nível de regra significa que as exclusões especificadas não serão analisadas apenas contra essa regra individual, enquanto ainda ser analisada por todas as outras regras do conjunto de regras. Este é o nível mais granular para exclusões, e pode ser usado para afinar a regra gerida definida com base nas informações que encontra nos registos da WAF ao resolver problemas num evento.
* Exclusões ao nível do grupo de regras
  * A aplicação de exclusões a um nível de grupo de regras significa que as exclusões especificadas não serão analisadas em comparação com esse conjunto específico de tipos de regras. Por exemplo, a seleção *do SQLI* como um grupo de regras excluído indica que as exclusões de pedidos definidos não seriam inspecionadas por nenhuma das regras específicas do SQLI, mas continuaria a ser inspecionada por regras de outros grupos, como *PHP,* *RFI* ou *XSS*. Este tipo de exclusão pode ser útil quando temos a certeza de que a aplicação não é suscetível a tipos específicos de ataques. Por exemplo, uma aplicação que não tenha bases de dados SQL poderia ter todas as regras *SQLI* excluídas sem ser prejudicial ao seu nível de segurança.
* Exclusões ao nível definido pela regra 
  * A aplicação de exclusões a um nível definido por regras significa que as exclusões especificadas não serão analisadas em nenhuma das regras de segurança disponíveis nessa regra definida. Trata-se de uma exclusão abrangente, pelo que deve ser utilizada com cuidado.

Neste exemplo, vamos realizar uma exclusão ao nível mais granular (aplicando exclusão a uma única regra) e estamos a tentar excluir a variável de **correspondência Pedido de nome args** que contém `comment` . Isto é aparente porque você pode ver os detalhes variáveis de correspondência no registo de firewall: `"matchVariableName": "PostParamValue:comment"` . O atributo é `comment` . Também pode encontrar este nome de atributo de algumas outras formas, ver [Encontrar pedido atribuir nomes](#finding-request-attribute-names).

![Regras de exclusão](../media/waf-front-door-tuning/exclusion-rules.png)

![Exclusão de regras para regra específica](../media/waf-front-door-tuning/exclusion-rule.png)

Ocasionalmente, há casos em que parâmetros específicos são passados para a WAF de uma forma que pode não ser intuitiva. Por exemplo, há um token que é passado ao autenticar usando o Azure Ative Directory. Este token, `__RequestVerificationToken` geralmente é passado como um cookie de pedido. No entanto, em alguns casos em que os cookies são desativados, este token também é passado como um argumento pós-pedido. Por esta razão, para abordar os falsos positivos da AD Azure, é necessário garantir que `__RequestVerificationToken` isso é adicionado à lista de exclusão para ambos e . `RequestCookieNames` `RequestBodyPostArgsNames` .

Exclusões num nome de campo *(seletor)* significam que o valor deixará de ser avaliado pela WAF. No entanto, o próprio nome de campo continua a ser avaliado e, em casos raros, pode corresponder a uma regra da WAF e desencadear uma ação.

![Exclusão de regras para conjunto de regras](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Alteração das ações da WAF

Outra forma de lidar com o comportamento das regras da WAF é escolher a ação que tomará quando um pedido corresponde às condições de uma regra. As ações disponíveis são: [Permitir, Bloquear, Registar e Redirecionar.](afds-overview.md#waf-actions)

Neste exemplo, alteramos o *Bloco* de Ação padrão para a ação *log* na regra 942110. Isto fará com que a WAF regissse o pedido e continue a avaliar o mesmo pedido contra as restantes regras de menor prioridade.

![Ações da WAF](../media/waf-front-door-tuning/actions.png)

Depois de realizar o mesmo pedido, podemos remeter para os registos e veremos que este pedido foi uma correspondência na regra ID 942110, e que o `action_s` campo agora indica *Log* em vez de *Bloco*. Em seguida, expandimos a consulta de registo para incluir a `trackingReference_s` informação e ver o que mais aconteceu com este pedido.

![Log mostrando vários jogos de regras](../media/waf-front-door-tuning/actions-log.png)

Curiosamente, vemos um jogo de regras SQLI diferente ocorre milissegundos após a regra ID 942110 foi processada. O mesmo pedido corresponde à regra ID 942310, e desta vez o *Bloco* de Ação padrão foi desencadeado.

Outra vantagem de usar a ação *Log* durante a sintonização ou resolução de problemas da WAF é que pode identificar se várias regras dentro de um grupo de regras específicas estão a corresponder e a bloquear um determinado pedido. Em seguida, pode criar as suas exclusões ao nível adequado, ou seja, ao nível do grupo regra ou regra. 

### <a name="using-custom-rules"></a>Usando regras personalizadas

Uma vez identificado o que está a causar uma correspondência de regras da WAF, pode usar regras personalizadas para ajustar a forma como a WAF responde ao evento. As regras personalizadas são processadas antes de regras geridas, podem conter mais do que uma condição, e as suas ações podem ser [Permitir, Negar, Registar ou Redirecionar.](afds-overview.md#waf-actions) Quando há uma correspondência de regras, o motor WAF para de processar. Isto significa que outras regras personalizadas com menor prioridade e regras geridas já não são executadas.

No exemplo abaixo, criámos uma regra personalizada com duas condições. A primeira condição é procurar o `comment` valor no corpo de pedido. A segunda condição é procurar o `/api/Feedbacks/` valor no pedido URI.

A utilização de uma regra personalizada permite-lhe ser o mais granular ao afinar as suas regras de WAF e lidar com falsos positivos. Neste caso, não estamos a tomar medidas apenas com base no valor corporal do `comment` pedido, que pode existir em vários sites ou aplicações sob a mesma política da WAF. Ao incluir outra condição para também corresponder a um pedido específico `/api/Feedbacks/` URI, garantimos que esta regra personalizada se aplica verdadeiramente a este caso de uso explícito que nós examinamos. Isto garante que o mesmo ataque, se realizado em diferentes condições, ainda seria inspecionado e impedido pelo motor WAF.

![Registo](../media/waf-front-door-tuning/custom-rule.png)

Ao explorar o registo, pode ver que o `ruleName_s` campo contém o nome dado à regra personalizada que criamos: `redirectcomment` . No `action_s` campo, pode ver que a ação *de redirecionamento* foi tomada para este evento. No `details_matches_s` campo, podemos ver os detalhes de ambas as condições foram combinados.

### <a name="disabling-rules"></a>Regras incapacitantes

Outra forma de contornar um falso positivo é desativar a regra que correspondia na entrada que o WAF pensava ser maliciosa. Uma vez que analisou os registos da WAF e reduziu a regra para 942110, pode desativá-la no portal Azure. Consulte [as regras de Firewall de aplicações web personalizadas utilizando o portal Azure](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Desativar uma regra é um benefício quando tem a certeza de que todos os pedidos que satisfazem essa condição específica são, de facto, pedidos legítimos, ou quando tem a certeza de que a regra simplesmente não se aplica ao seu ambiente (como, por exemplo, desativar uma regra de injeção SQL porque tem backends não-SQL). 
 
No entanto, desativar uma regra é um cenário global que se aplica a todos os anfitriões frontais associados à política da WAF. Quando optar por desativar uma regra, pode estar a deixar as vulnerabilidades expostas sem proteção ou deteção para quaisquer outros anfitriões de frontend associados à política WAF.
 
Se pretender utilizar o Azure PowerShell para desativar uma regra gerida, consulte a documentação do [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0) objeto. Se quiser utilizar o Azure CLI, consulte a [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override) documentação.

![Regras da WAF](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> É uma boa ideia documentar quaisquer alterações que faça na sua política da WAF. Inclua pedidos de exemplo para ilustrar a deteção falsamente positiva, e explique claramente por que adicionou uma regra personalizada, desativou uma regra ou regras ou acrescentou uma exceção. Esta documentação pode ser útil se redesenhar a sua aplicação no futuro e precisar de verificar se as suas alterações ainda são válidas. Também pode ajudar se alguma vez for auditado ou precisar de justificar o porquê de ter reconfigurado a política da WAF a partir das suas definições predefinidas.

## <a name="finding-request-fields"></a>Encontrar campos de pedido

Utilizando um proxy de navegador como [o Fiddler,](https://www.telerik.com/fiddler)pode inspecionar pedidos individuais e determinar que campos específicos de uma página web são chamados. Isto é útil quando precisamos de excluir certos campos da inspeção utilizando listas de exclusão na WAF.

### <a name="finding-request-attribute-names"></a>Encontrar nomes de atributos de pedido
 
Neste exemplo, pode-se ver o campo onde a `1=1` corda foi inserida é chamado `comment` . Estes dados foram transmitidos no corpo de um pedido de CORREIO.

![Pedido de violinista mostrando corpo](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Este é um campo que pode excluir. Para saber mais sobre listas de exclusão, consulte [as listas de exclusão do firewall da aplicação Web](./waf-front-door-exclusion.md). Pode excluir a avaliação neste caso configurando a seguinte exclusão:

![Regra de exclusão](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Também pode examinar os registos de firewall para obter as informações para ver o que precisa de adicionar à lista de exclusão. Para ativar o registo, consulte [métricas de monitorização e registos na Porta frontal Azure](./waf-front-door-monitor.md).

Examine o registo de firewall no `PT1H.json` ficheiro durante a hora em que o pedido que pretende inspecionar ocorreu. `PT1H.json` os ficheiros estão disponíveis nos recipientes da conta de armazenamento onde `FrontDoorWebApplicationFirewallLog` os registos de diagnóstico e os `FrontDoorAccessLog` registos de diagnóstico são armazenados.

Neste exemplo, pode ver a regra que bloqueou o pedido (com a mesma Referência de Transação) e ocorreu exatamente ao mesmo tempo:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Com o seu conhecimento de como funcionam os conjuntos de regras geridos pelo Azure (ver [Firewall de Aplicação Web na Porta frontal Azure),](afds-overview.md)sabe que a regra com a *ação: A* propriedade do bloco está bloqueando com base nos dados combinados no corpo de pedido. Pode ver nos detalhes que corresponde a um padrão `1=1` (), e o campo está `comment` nomeado. Siga os mesmos passos anteriores para excluir o nome do cartão de pedido que contém `comment` .

### <a name="finding-request-header-names"></a>Encontrar nomes de cabeçalho de pedido

O Violinista é uma ferramenta útil mais uma vez para encontrar nomes de cabeçalho de pedido. Na imagem seguinte, pode ver os cabeçalhos para este pedido GET, que inclui o Tipo de Conteúdo, O Agente de Utilizador, e assim por diante. Também pode utilizar cabeçalhos de pedido para criar exclusões e regras personalizadas em WAF.

![Pedido de violino mostrando cabeçalho](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Outra forma de visualizar os cabeçalhos de pedido e resposta é olhar para dentro das ferramentas de desenvolvimento do seu navegador, como o Edge ou o Chrome. Pode premir F12 ou click -> **Inspect**  ->  **Developer Tools**, e selecionar o separador **'Rede'.** Carregue uma página web e clique no pedido que pretende inspecionar.

![Pedido de inspetor de rede](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Encontrar nomes de cookies de pedido

Se o pedido contiver cookies, o separador Cookies pode ser selecionado para os visualizar no Fiddler. As informações sobre cookies também podem ser usadas para criar exclusões ou regras personalizadas em WAF.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a firewall da aplicação web Azure](../overview.md).
- Saiba como [criar um Front Door](../../frontdoor/quickstart-create-front-door.md).
