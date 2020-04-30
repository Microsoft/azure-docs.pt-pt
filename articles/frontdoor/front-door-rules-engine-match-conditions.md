---
title: Porta da frente azure [ Microsoft Docs
description: Este artigo apresenta uma descrição geral do Azure Front Door. Descubra se é a escolha certa para equilibrar o tráfego de utilizadores para a sua aplicação.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 77c0d68f507e09b315c912d1d91fdf9cf63db6fa
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515532"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Regras da porta da frente azure regras condições de correspondência do motor

No [Motor de Regras Da AFD,](front-door-rules-engine.md) uma regra consiste em zero ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que pode utilizar no Motor de Regras Da AFD. 

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. Uma regra pode consistir em até 10 condições de jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais são executadas ações definidas. Se utilizar várias condições de jogo, as condições de jogo são agrunadas utilizando e lógica. Para todas as condições de correspondência que suportam múltiplos valores (anotados abaixo como "separados do espaço"), assume-se o operador "OR". 

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos com base num endereço IP específico, país ou região.
- Filtrar pedidos por informação do cabeçalho.
- Filtrar pedidos de dispositivos móveis ou dispositivos de secretária.

As seguintes condições de jogo estão disponíveis para utilização no motor Regras da Porta Frontal Azure.  

## <a name="device-type"></a>Tipo de Dispositivo 

Identifica pedidos feitos a partir de um dispositivo móvel ou de um dispositivo de secretária.  

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | Mobile, Desktop

## <a name="post-argument"></a>Argumento pós

Identifica pedidos com base em argumentos definidos para o método de pedido do POST que é usado no pedido. 

#### <a name="required-fields"></a>Campos necessários

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

## <a name="query-string"></a>Cadeias de consulta

Identifica pedidos que contêm um parâmetro específico de corda de consulta. Este parâmetro está definido para um valor que corresponde a um padrão específico. Os parâmetros de cadeia de consulta (por exemplo, **parâmetro=valor**) no URL de pedido determinam se esta condição é satisfeita. Esta condição de correspondência identifica um parâmetro de corda de consulta pelo seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos necessários

Operador | Cadeias de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

## <a name="remote-address"></a>Endereço remoto

Identifica pedidos com base na localização do solicitado ou endereço IP.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|-----------------
Geo Match | Código de país
Jogo IP | Endereço IP (separado sem espaço)
Não Geo Match | Código de país
Não jogo IP | Endereço IP (separado sem espaço)

#### <a name="key-information"></a>Informação chave

- Utilize notação CIDR.
- Para especificar vários endereços IP e blocos de endereços IP, utilize um único espaço entre os valores:
  - **Exemplo iPv4**: *1.2.3.4 10.20.30.40* corresponde a quaisquer pedidos que cheguem de ambos os endereços 1.2.3.4 ou 10.20.30.40.
  - **Exemplo do IPv6:** *1:2:3:4:5:6:7:8 10:20:40:50:60:70:80* corresponde a quaisquer pedidos que cheguem de ambos os endereços 1:2:3:4:5:7:8 ou 10:20:40:50:60:70:70:80.
- A sintaxe para um bloco de endereçoIP é o endereço IP base seguido de uma barra para a frente e o tamanho do prefixo. Por exemplo:
  - **Exemplo iPv4**: *5.5.5.64/26* corresponde a quaisquer pedidos que cheguem dos endereços 5.5.5.64 a 5.5.5.127.
  - **Exemplo iPv6**: *1:2:3:/48* corresponde a qualquer pedido que chegue dos endereços 1:2:3:0:0:0:0:0:0:0 a 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ff

## <a name="request-body"></a>Corpo do pedido

Identifica pedidos com base em texto específico que aparece no corpo do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Corpo do pedido | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

## <a name="request-header"></a>Cabeçalho do pedido

Identifica pedidos que usam um cabeçalho específico no pedido.

#### <a name="required-fields"></a>Campos necessários

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

## <a name="request-method"></a>Método de pedido

Identifica pedidos que utilizam o método de pedido especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | OBTER, POST, COLOCAR, APAGAR, CABEÇA, OPÇÕES, RASTREAR

#### <a name="key-information"></a>Informação chave

- Apenas o método de pedido GET pode gerar conteúdo em cache na Porta Frontal Azure. Todos os outros métodos de pedido são proxid através da rede. 

## <a name="request-protocol"></a>Protocolo de pedido

Identifica pedidos que utilizam o protocolo especificado utilizado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | HTTP, HTTPS

## <a name="request-url"></a>URL do Pedido

Identifica pedidos que correspondam ao URL especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | URL do Pedido | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

#### <a name="key-information"></a>Informação chave

- Quando utilizar esta condição de regra, certifique-se de incluir informações protocolares. Por exemplo: *https://www.\<yourdomain\>.com*.

## <a name="request-file-extension"></a>Solicitar extensão de ficheiro

Identifica pedidos que incluem a extensão de ficheiro especificada no nome do ficheiro no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

#### <a name="key-information"></a>Informação chave

- Para extensão, não inclua um período de liderança; por exemplo, use *html* em vez de *.html*.

## <a name="request-file-name"></a>Solicitar nome de ficheiro

Identifica pedidos que incluem o nome de ficheiro especificado no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Nome de ficheiro | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

#### <a name="key-information"></a>Informação chave

- Para especificar vários nomes de ficheiros, separe cada nome de ficheiro premindo o ENTER. 

## <a name="request-path"></a>Caminho do pedido

Identifica pedidos que incluem o caminho especificado no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, maiúscula, guarnição, remoção do espaço branco, url encode, url descodificar

## <a name="standard-operator-list"></a>Lista de operadores padrão

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- É igual a 
- Contains 
- Começa com 
- Termina com 
- Menor do que
- Menos ou iguais
- Maior que
- Maior ou igual
- Não é qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não menos do que
- Não menos do que ou igual
- Não maior do que
- Não maior do que ou igual

Para operadores numéricos como *Menos do que* e Maior *esquecê-lo ou iguais,* a comparação utilizada baseia-se no comprimento. Neste caso, o valor na condição de jogo deve ser um inteiro que seja igual ao comprimento que quer comparar. 


## <a name="next-steps"></a>Passos seguintes

- Aprenda a configurar a sua primeira [configuração do Motor de Regras](front-door-tutorial-rules-engine.md). 
- Saiba mais sobre [as ações do Motor de Regras](front-door-rules-engine-actions.md)
- Saiba mais sobre o motor de [regras da porta da frente azure](front-door-rules-engine.md)
