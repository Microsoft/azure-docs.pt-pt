---
title: Modelos de hubs de notificação Azure
description: Saiba como usar modelos para Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635647"
---
# <a name="notification-hubs-templates"></a>Modelos de Centros de Notificação

Os modelos permitem que uma aplicação do cliente especifique o formato exato das notificações que pretende receber. Utilizando modelos, uma aplicação pode realizar vários benefícios diferentes, incluindo os seguintes:

- Um backend agnóstico de plataforma
- Notificações personalizadas
- Independência da versão do cliente
- Localização fácil

Esta secção fornece dois exemplos aprofundados de como usar modelos para enviar notificações agnósticas da plataforma direcionadas a todos os seus dispositivos através de plataformas e para personalizar notificações de transmissão para cada dispositivo.

## <a name="using-templates-cross-platform"></a>Usando modelos cross-platform

A forma padrão de enviar notificações push é enviar, para cada notificação que deve ser enviada, uma carga útil específica para os serviços de notificação da plataforma (WNS, APNS). Por exemplo, para enviar um alerta para a APNS, a carga útil é um objeto JSON do seguinte formulário:

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

Pode criar cargas similares para plataformas MPNS (Windows Phone) e FCM (Android).

Esta exigência obriga o backend da app a produzir cargas diferentes para cada plataforma, e efetivamente torna o backend responsável por parte da camada de apresentação da app. Algumas preocupações incluem localização e layouts gráficos (especialmente para aplicações Windows Store que incluem notificações para vários tipos de azulejos).

A funcionalidade de modelo 'Hubs' de notificação permite a uma aplicação do cliente criar registos especiais, chamados registos de modelos, que incluem, além do conjunto de tags, um modelo. A funcionalidade de modelo 'Hubs de Notificação' permite que uma aplicação do cliente associe dispositivos com modelos quer esteja a trabalhar com instalações (preferenciais) ou registos. Tendo em conta os exemplos anteriores de carga útil, a única informação independente da plataforma é a mensagem de alerta real **(Olá!** Um modelo é um conjunto de instruções para o centro de notificação sobre como formatar uma mensagem independente da plataforma para o registo dessa aplicação específica do cliente. No exemplo anterior, a mensagem independente da plataforma é uma única propriedade: `message = Hello!` .

A seguinte figura ilustra o processo:

![Diagrama mostrando o processo de utilização de modelos cross-platform](./media/notification-hubs-templates/notification-hubs-hello.png)

O modelo para o registo de aplicações do cliente iOS é o seguinte:

```json
{"aps": {"alert": "$(message)"}}
```

O modelo correspondente para a aplicação cliente da Windows Store é:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Note que a mensagem real é substituída pela expressão `$(message)` . Esta expressão instrui o centro de notificação, sempre que envia uma mensagem a este registo em particular, para construir uma mensagem que a segue e insira o valor comum.

Se estiver a trabalhar com o modelo de instalação, a tecla "modelos" de instalação contém um JSON de vários modelos. Se estiver a trabalhar com o modelo de registo, a aplicação do cliente pode criar vários registos de forma a utilizar vários modelos; por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de azulejos. As aplicações do cliente também podem misturar registos nativos (registos sem modelo) e registos de modelos.

O centro de notificações envia uma notificação para cada modelo sem considerar se pertencem à mesma aplicação do cliente. Este comportamento pode ser usado para traduzir notificações independentes da plataforma em mais notificações. Por exemplo, a mesma mensagem independente da plataforma para o centro de notificação pode ser traduzida perfeitamente num alerta de torradas e numa atualização de azulejos, sem que seja necessário que o backend esteja ciente do mesmo. Algumas plataformas (por exemplo, iOS) podem colapsar várias notificações para o mesmo dispositivo se forem enviadas num curto espaço de tempo.

## <a name="using-templates-for-personalization"></a>Usando modelos para personalização

Outra vantagem na utilização de modelos é a capacidade de usar Os Centros de Notificação para realizar a personalização por registo de notificações. Por exemplo, considere uma aplicação meteorológica que exibe um azulejo com as condições meteorológicas num local específico. Um utilizador pode escolher entre graus Celsius ou Fahrenheit, e uma previsão de um ou cinco dias. Utilizando modelos, cada instalação de aplicativos do cliente pode registar-se para o formato necessário (1 dia Celsius, 1 dia Fahrenheit, 5 dias Celsius, 5 dias Fahrenheit), e ter o backend enviar uma única mensagem que contenha todas as informações necessárias para preencher esses modelos (por exemplo, uma previsão de cinco dias com graus Celsius e Fahrenheit).

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

A mensagem enviada para o centro de notificação contém todas as seguintes propriedades:

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Ao utilizar este padrão, o backend apenas envia uma única mensagem sem ter de armazenar opções específicas de personalização para os utilizadores da aplicação. A seguinte figura ilustra este cenário:

![Diagrama mostrando como o backend apenas envia uma única mensagem para cada plataforma.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registar modelos

Para registar-se com os modelos utilizando o modelo de instalação (preferencial), ou o modelo de registo, consulte [a gestão do Registo](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguagem de expressão de modelo

Os modelos estão limitados aos formatos de documentos XML ou JSON. Além disso, só é possível colocar expressões em locais específicos; por exemplo, atributos ou valores de nó para XML, valores de propriedade de cadeia para JSON.

A tabela a seguir mostra o idioma permitido nos modelos:

| Expressão       | Descrição |
| ---------------- | --- |
| $(adereço)          | Referência a uma propriedade do evento com o nome próprio. Os nomes das propriedades não são sensíveis a casos. Esta expressão resolve-se no valor de texto da propriedade ou numa cadeia vazia se a propriedade não estiver presente. |
|$(prop, n)       | Como acima, mas o texto é explicitamente cortado em n caracteres, por exemplo $(título, 20) clips o conteúdo da propriedade título em 20 caracteres. |
| . (adereço, n)      | Como acima, mas o texto é sufixado com três pontos à medida que é cortado. O tamanho total da corda cortada e do sufixo não excede os caracteres n. (título, 20) com uma propriedade de entrada de "This is the title line" resulta em **This is the title...** |
| %(prop)          | Semelhante a $(nome), exceto que a saída é codificada URI. |
| #(prop)          | Usado em modelos JSON (por exemplo, para modelos iOS e Android).<br><br>Esta função funciona exatamente da mesma forma que '$(prop)' previamente especificado, exceto quando usado em modelos JSON (por exemplo, modelos apple). Neste caso, se esta função não estiver rodeada de "{'}" (por exemplo, 'myJsonProperty' : '#(nome)'), e avaliar um número em formato JavaScript, por exemplo, regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(&#91;\. 0-9&#93;+).) e&#124;E) (+&#124;-)?&#91;0-9&#93;+)?, então a saída JSON é um número.<br><br>Por exemplo, 'badge: '#(nome)' torna-se 'badge' : 40 (e não '40'). |
| 'texto' ou "texto" | Um literal. Os literais contêm texto arbitrário incluído em cotações simples ou duplas. |
| expr1 + expr2    | O operador de concatenação junta duas expressões numa única corda. |

As expressões podem ser qualquer uma das formas anteriores.

Ao utilizar a concatenação, toda a expressão deve estar rodeada de `{}` . Por exemplo, `{$(prop) + ‘ - ’ + $(prop2)}`.

Por exemplo, o modelo a seguir não é um modelo XML válido:

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

[Saiba mais sobre os Hubs de Notificação Azure](notification-hubs-push-notification-overview.md)
