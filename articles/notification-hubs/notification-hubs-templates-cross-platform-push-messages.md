---
title: Modelos de Hubs de Notificação Azure
description: Saiba usar modelos para Centros de Notificação Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7d88f57fe92b9da62cc9f90d64bdec4c27642fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263749"
---
# <a name="templates"></a>Modelos

Os modelos permitem que uma aplicação do cliente especifique o formato exato das notificações que pretende receber. Usando modelos, uma aplicação pode realizar vários benefícios diferentes, incluindo o seguinte:

- Um backend agnóstico de plataforma
- Notificações personalizadas
- Independência da versão do cliente
- Localização fácil

Esta secção fornece dois exemplos aprofundados de como usar modelos para enviar notificações agnósticas de plataforma direcionadas para todos os seus dispositivos através de plataformas, e personalizar a notificação de transmissão para cada dispositivo.

## <a name="using-templates-cross-platform"></a>Usando modelos cross-platform

A forma padrão de enviar notificações push é enviar, para cada notificação que deve ser enviada, uma carga útil específica para serviços de notificação da plataforma (WNS, APNS). Por exemplo, para enviar um alerta para a APNS, a carga útil é um objeto JSON do seguinte formulário:

```json
{"aps": {"alert" : "Hello!" }}
```

Para enviar uma mensagem de torrada semelhante numa aplicação da Windows Store, a carga útil XML é a seguinte:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Pode criar cargas similares para as plataformas MPNS (Windows Phone) e FCM (Android).

Este requisito obriga o backend da aplicação a produzir diferentes cargas para cada plataforma, e efetivamente torna o backend responsável por parte da camada de apresentação da app. Algumas preocupações incluem localização e layouts gráficos (especialmente para aplicações da Windows Store que incluem notificações para vários tipos de azulejos).

A funcionalidade de modelo De Logir Hubs permite a uma aplicação de cliente criar registos especiais, chamados registos de modelos, que incluem, além do conjunto de tags, um modelo. A função de modelo De Logir Hubs permite que uma aplicação de cliente associe dispositivos com modelos quer esteja a trabalhar com Instalações (preferidas) ou Registos. Tendo em conta os exemplos de carga útil anteriores, a única informação independente da plataforma é a mensagem de alerta real (Olá!). Um modelo é um conjunto de instruções para o Centro de Notificação sobre como formatar uma mensagem independente da plataforma para o registo dessa aplicação específica do cliente. No exemplo anterior, a mensagem independente da plataforma `message = Hello!`é uma única propriedade: .

A imagem seguinte ilustra o processo:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

O modelo para o registo de aplicações para clientes iOS é o seguinte:

```json
{"aps": {"alert": "$(message)"}}
```

O modelo correspondente para a aplicação de cliente da Windows Store é:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Note que a mensagem real é substituída pela expressão $(mensagem). Esta expressão instrui o Centro de Notificação, sempre que envia uma mensagem para este registo específico, para construir uma mensagem que a siga e alterna no valor comum.

Se estiver a trabalhar com o modelo de instalação, a chave de instalação "modelos" contém um JSON de vários modelos. Se estiver a trabalhar com o modelo de registo, a aplicação do cliente pode criar múltiplas inscrições para utilizar vários modelos; por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de azulejos. As aplicações ao cliente também podem misturar registos nativos (registos sem modelo) e registos de modelos.

O Centro de Notificação envia uma notificação para cada modelo sem considerar se pertencem à mesma aplicação de cliente. Este comportamento pode ser usado para traduzir notificações independentes da plataforma em mais notificações. Por exemplo, a mesma mensagem independente da plataforma para o Centro de Notificação pode ser perfeitamente traduzida num alerta de torrada e numa atualização de azulejos, sem exigir que o backend esteja ciente do mesmo. Algumas plataformas (por exemplo, iOS) podem colapsar várias notificações para o mesmo dispositivo se forem enviadas num curto espaço de tempo.

## <a name="using-templates-for-personalization"></a>Usando modelos para personalização

Outra vantagem na utilização de modelos é a capacidade de utilizar centros de notificação para realizar personalização por registo de notificações. Por exemplo, considere uma aplicação meteorológica que exibe um azulejo com as condições meteorológicas num local específico. Um utilizador pode escolher entre graus Celsius ou Fahrenheit, e uma previsão de um ou cinco dias. Utilizando modelos, cada instalação de aplicação de cliente pode registar-se para o formato necessário (1 dia Celsius, 1 dia Fahrenheit, 5 dias Celsius, 5 dias Fahrenheit), e fazer com que o backend envie uma única mensagem que contenha todas as informações necessárias para preencher esses modelos (por exemplo, uma previsão de cinco dias com graus Celsius e Fahrenheit).

O modelo para a previsão de um dia com temperaturas Celsius é o seguinte:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

A mensagem enviada para o Centro de Notificação contém todas as seguintes propriedades:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Ao utilizar este padrão, o backend apenas envia uma única mensagem sem ter de armazenar opções específicas de personalização para os utilizadores da aplicação. A imagem seguinte ilustra este cenário:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registar modelos

Para se registar com modelos utilizando o modelo de instalação (preferido), ou o modelo de registo, consulte [gestão de registos](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguagem de expressão do modelo

Os modelos estão limitados aos formatos de documentos XML ou JSON. Além disso, você só pode colocar expressões em lugares particulares; por exemplo, atributos de nó ou valores para XML, valores de propriedade de cordas para JSON.

A tabela seguinte mostra a linguagem permitida em modelos:

| Expressão       | Descrição |
| ---------------- | --- |
| $(adereço)          | Referência a uma propriedade de evento com o nome dado. Os nomes de propriedade não são sensíveis a casos. Esta expressão resolve-se no valor de texto da propriedade ou numa cadeia vazia se a propriedade não estiver presente. |
| $(prop, n)       | Como acima, mas o texto é explicitamente recortado em caracteres n, por exemplo $(título, 20) clips o conteúdo da propriedade do título em 20 caracteres. |
| . (adereço, n)       | Como acima, mas o texto é sufixo com três pontos à medida que é cortado. O tamanho total da corda cortada e do sufixo não excede os caracteres n. . (título, 20) com uma propriedade de entrada de "Esta é a linha do título" resulta **neste é o título...** |
| %(prop)          | Semelhante a $(nome), exceto que a saída é codificada por URI. |
| #(adereço)          | Usado em modelos JSON (por exemplo, para modelos iOS e Android).<br><br>Esta função funciona exatamente da mesma forma que $(prop) previamente especificado, exceto quando utilizada em modelos JSON (por exemplo, modelos apple). Neste caso, se esta função não estiver rodeada por "{'",}"" (por exemplo, 'myJsonProperty' : '#(nome)'), e avalia um número em formato Javascript, por exemplo,\. regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93;*),&#91;0-9&#93;+)? (e&#124;E)(&#124;-)?&#91;0-9&#93;+)?, então a saída JSON é um número.<br><br>Por exemplo, 'crachá: '#(nome)' torna-se 'crachá': 40 (e não '40'). |
| 'texto' ou "texto" | Um literal. Os literais contêm texto arbitrário incluído em cotações individuais ou duplas. |
| expr1 + expr2    | O operador de constcatenação juntando duas expressões numa única corda. |

As expressões podem ser qualquer uma das formas anteriores.

Quando se utiliza a concatenação, toda a expressão deve estar rodeada de `{}`. Por exemplo, `{$(prop) + ‘ - ’ + $(prop2)}`.

Por exemplo, o seguinte modelo não é um modelo XML válido:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Como explicado anteriormente, quando se utiliza a concatenação, as expressões devem ser embrulhadas em suportes encaracolados. Por exemplo:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Passos seguintes

[Conheça os Centros de Notificação Azure](notification-hubs-push-notification-overview.md)