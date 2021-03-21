---
title: Regras da porta dianteira Azure Os motores combinam com as condições
description: Este artigo fornece uma lista das várias condições de jogo disponíveis com o Motor das Regras da Porta Frontal Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: a352624fc802e4224aa1b60768c064c0054cdfd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035879"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Regras da porta dianteira Azure Condições de jogo do motor

No [MOTOR DE Regras AFD,](front-door-rules-engine.md)uma regra consiste em zero ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de jogo que pode utilizar no MOTOR de Regras DA AFD.

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. Uma regra pode consistir em até 10 condições de jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais são feitas ações definidas. Se utilizar várias condições de correspondência, as condições de jogo são agrupadas utilizando a lógica E. Para todas as condições de correspondência que suportam múltiplos valores (notados como "separados pelo espaço"), assume-se o operador "OR".

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos com base num endereço IP específico, país ou região.
- Filtrar os pedidos por informação do cabeçalho.
- Filtrar pedidos de dispositivos móveis ou dispositivos de ambiente de trabalho.

As seguintes condições de jogo estão disponíveis para usar no motor Azure Front Door Rules.  

## <a name="device-type"></a>Tipo de Dispositivo 

Identifica os pedidos feitos a partir de um dispositivo móvel ou de um dispositivo de ambiente de trabalho.  

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | Mobile, Desktop

## <a name="post-argument"></a>Pós-argumento

Identifica os pedidos com base em argumentos definidos para o método de pedido DOM que é utilizado no pedido. 

#### <a name="required-fields"></a>Campos necessários

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

## <a name="query-string"></a>Cadeias de consulta

Identifica pedidos que contêm um parâmetro específico de cadeia de consulta. Este parâmetro é definido para um valor que corresponde a um padrão específico. Os parâmetros de cadeia de consulta (por exemplo, **parâmetro=valor)** no URL de pedido determinam se esta condição é cumprida. Esta condição de correspondência identifica um parâmetro de cadeia de consulta pelo seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos necessários

Operador | Cadeias de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

## <a name="remote-address"></a>Endereço remoto

Identifica os pedidos com base na localização ou endereço IP do solicitador.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|-----------------
Geo Match | Indicativo do país
Jogo IP | Endereço IP (separado pelo espaço)
Não Geo Match | Indicativo do país
Não jogo IP | Endereço IP (separado pelo espaço)

#### <a name="key-information"></a>Informações-chave

- Utilize a notação CIDR.
- Para especificar vários endereços IP e blocos de endereços IP, utilize um espaço único entre os valores:
  - **Exemplo do IPv4:** *1.2.3.4 10.20.30.40* corresponde a quaisquer pedidos que cheguem de qualquer endereço 1.2.3.4 ou 10.20.30.40.
  - **IPv6 exemplo**: *1:2:3:4:5:6:7:8 10:20:30:40:60:70:80* corresponde a qualquer pedido que chegam de qualquer endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP base seguido de um corte dianteiro e o tamanho do prefixo. Por exemplo:
  - **IPv4 exemplo:** *5.5.5.64/26* corresponde a quaisquer pedidos que cheguem dos endereços 5.5.5.64 até 5.5.5.127.
  - **IPv6 exemplo**: *1:2:3:/48* corresponde a qualquer pedido que chegue de endereços 1:2:3:0:0:0:0:0:0:0:0:0:0:0:0:0:0:3: ffff:ff:ff:ff:ff:ff:ff.

## <a name="request-body"></a>Corpo do pedido

Identifica pedidos com base em texto específico que aparece no corpo do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Corpo do pedido | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

## <a name="request-header"></a>Cabeçalho do pedido

Identifica os pedidos que utilizam um cabeçalho específico no pedido.

#### <a name="required-fields"></a>Campos necessários

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

## <a name="request-method"></a>Método de pedido

Identifica os pedidos que utilizam o método de pedido especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | OBTER, POST, COLOCAR, APAGAR, CABEÇA, OPÇÕES, TRAÇO

#### <a name="key-information"></a>Informações-chave

- Apenas o método de pedido GET pode gerar conteúdo em cache na Porta Frontal Azure. Todos os outros métodos de pedido são proxiemente através da rede. 

## <a name="request-protocol"></a>Protocolo de pedido

Identifica os pedidos que utilizam o protocolo especificado utilizado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | HTTP, HTTPS

## <a name="request-url"></a>URL do Pedido

Identifica pedidos que correspondam à URL especificada.

#### <a name="required-fields"></a>Campos necessários

Operador | URL do Pedido | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

#### <a name="key-information"></a>Informações-chave

- Quando utilizar esta condição de regra, certifique-se de incluir informações de protocolo. Por exemplo: `https://www.<yourdomain\>.com`.

## <a name="request-file-extension"></a>Pedido de extensão de ficheiro

Identifica os pedidos que incluem a extensão de ficheiro especificada no nome do ficheiro no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

#### <a name="key-information"></a>Informações-chave

- Para extensão, não inclua um período de liderança; por exemplo, use *html* em vez de *.html*.

## <a name="request-file-name"></a>Solicitar nome de ficheiro

Identifica os pedidos que incluem o nome de ficheiro especificado no URL de pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Nome de ficheiro | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

#### <a name="key-information"></a>Informações-chave

- Para especificar vários nomes de ficheiros, separe cada nome de ficheiro premindo ENTER. 

## <a name="request-path"></a>Caminho do pedido

Identifica os pedidos que incluem o caminho especificado na URL de pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Minúscula, Maiúscula, Guarnição, Remover Whitespace, URL Codificação, DEcode URL

#### <a name="key-information"></a>Informações-chave

- Quando utilizar esta condição de regra, note que a partida começa após o primeiro `/` do caminho. Por exemplo: `https://www.<yourdomain>.com/folder/page` começaria a combinar `folder/page` .

## <a name="standard-operator-list"></a>Lista de operadores padrão

Para as regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- Igual a 
- Contains 
- Começa com 
- Termina com 
- Menor que
- Menos ou igual
- Maior que
- Maior do que ou igual
- Nem qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não menos que
- Não menos do que ou iguais
- Não maior do que
- Não maior do que ou igual

Para operadores numéricos como *Menos do que* e Maior do que ou *iguais,* a comparação utilizada baseia-se no comprimento. O valor na condição de jogo deve ser um inteiro que seja igual ao comprimento que se quer comparar. 


## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o seu primeiro [Motor de Regras.](front-door-tutorial-rules-engine.md) 
- Saiba mais sobre [as ações do Regimento de Máquinas](front-door-rules-engine-actions.md)
- Saiba mais sobre [o motor das regras da porta da frente Azure](front-door-rules-engine.md)
