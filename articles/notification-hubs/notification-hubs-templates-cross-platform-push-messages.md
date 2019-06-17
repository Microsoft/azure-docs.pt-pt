---
title: Modelos
description: Este tópico explica os modelos para os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60872110"
---
# <a name="templates"></a>Modelos

Os modelos permitem uma aplicação de cliente especificar o formato exato de notificações que deseja receber. Utilizar modelos, uma aplicação pode realizar várias vantagens diferentes, incluindo o seguinte:

- Um back-end de independente de plataforma
- Notificações personalizadas
- Independência de versão do cliente
- Localização fácil

Esta seção fornece dois exemplos detalhados de como utilizar modelos para enviar notificações de independente de plataforma para todos os seus dispositivos em plataformas e para personalizar as notificações de difusão para cada dispositivo.

## <a name="using-templates-cross-platform"></a>Usando modelos para várias plataformas

É a forma padrão para enviar notificações push enviar, cada notificação que estão a ser enviados, um payload específico para serviços de notificação de plataforma (WNS, APNS). Por exemplo, para enviar um alerta para APNS, o payload é um objeto JSON da seguinte forma:

```json
{"aps": {"alert" : "Hello!" }}
```

Para enviar uma mensagem de alerta semelhante num aplicativo da Windows Store, o payload XML é o seguinte:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Pode criar payloads semelhante para MPNS (Windows Phone) e plataformas FCM (Android).

Este requisito força o back-end de aplicação para produzir payloads diferentes para cada plataforma e torna o back-end responsável por parte da camada de apresentação da aplicação. Algumas preocupações incluem a localização e layouts de gráficos (especialmente para aplicativos Windows Store que incluam notificações para vários tipos de mosaicos).

O recurso de modelo de Hubs de notificação permite que uma aplicação de cliente criar registos especiais, chamados de registos de modelos, que incluem, além do conjunto de marcas, um modelo. O recurso de modelo de Hubs de notificação permite que uma aplicação de cliente associar dispositivos com modelos, se estiver a trabalhar com instalações (preferidas) ou de registos. Tendo em conta os exemplos de payload anterior, as informações de apenas independente de plataforma são a mensagem de alerta real (Olá!). Um modelo é um conjunto de instruções para o Hub de notificação sobre como formatar uma mensagem de independente de plataforma para o registo dessa aplicação de cliente específico. No exemplo anterior, a mensagem de independente de plataforma é uma única propriedade: `message = Hello!`.

A imagem seguinte ilustra o processo:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

O modelo para o registo de aplicações de cliente iOS é o seguinte:

```json
{"aps": {"alert": "$(message)"}}
```

O modelo correspondente para a aplicação de cliente do Windows Store é:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Tenha em atenção que a mensagem real é substituída pelos $ a expressão (mensagens). Esta expressão instrui o Hub de notificação, sempre que envia uma mensagem para esse Registro específico, para criar uma mensagem que se segue ele e comutadores no valor do comum.

Se estiver a trabalhar com o modelo de instalação, a chave de "modelos" de instalação contém um JSON de vários modelos. Se estiver a trabalhar com o modelo de registo, a aplicação cliente pode criar vários registos para poder utilizar vários modelos; Por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de mosaico. Aplicativos cliente também podem combinar registos nativos (registos que nenhum modelo) e registos de modelos.

O Hub de notificação envia uma notificação para cada modelo sem considerar se eles pertencem à mesma aplicação de cliente. Esse comportamento pode ser utilizado para efetuar a conversão de notificações de independente de plataforma para obter mais notificações. Por exemplo, a mesma mensagem independente de plataforma para o Hub de notificação pode ser facilmente traduzida num alerta de alerta e uma atualização de mosaico, sem a necessidade do back-end ficar atento ele. Algumas plataformas (por exemplo, iOS) poderão fechar várias notificações ao mesmo dispositivo, se eles forem enviados num curto período de tempo.

## <a name="using-templates-for-personalization"></a>Utilizar modelos para personalização

Outra vantagem de usar modelos é a capacidade de utilizar os Hubs de notificação para realizar a personalização por registo de notificações. Por exemplo, considere um aplicativo meteorológico que apresenta um mosaico com as condições de Meteorologia numa localização específica. Um utilizador pode escolher entre graus Celsius ou Fahrenheit e uma previsão única ou de cinco dias. Utilizar modelos, cada instalação da aplicação cliente pode se registrar para o formato necessário (1 dia Celsius, 1 dia Fahrenheit, 5 dias Celsius, 5 dias Fahrenheit), e ter o back-end enviar uma única mensagem que contém todas as informações necessárias para preencher esses modelos (por exemplo, uma previsão com graus Celsius e Fahrenheit de cinco dias).

O modelo para a previsão de um dia com Celsius temperaturas é o seguinte:

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

A mensagem enviada para o Hub de notificação contém as seguintes propriedades:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Ao utilizar este padrão, o back-end envia apenas uma única mensagem sem ter de armazenar as opções de personalização específicos para os utilizadores da aplicação. A imagem seguinte ilustra esse cenário:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registar modelos

Para registar com modelos usando o modelo de instalação (preferencial) ou o modelo de registo, consulte [gestão de registo](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguagem de expressão de modelo

Os modelos são limitados a formatos de documento XML ou JSON. Além disso, só pode colocar expressões em locais específicos; Por exemplo, atributos de nó ou valores para XML, cadeia de valores de propriedade para JSON.

A tabela seguinte mostra o idioma permitido em modelos:

| expressão       | Descrição |
| ---------------- | --- |
| $(prop)          | Referência a uma propriedade de evento com o nome fornecido. Nomes de propriedade não diferenciam maiúsculas de minúsculas. Esta expressão é resolvido para o valor da propriedade text ou para uma cadeia vazia se a propriedade não estiver presente. |
| $(prop, n)       | Conforme apresentado acima, mas o texto é explicitamente recortado na n caracteres, por exemplo $(title, 20) corta o conteúdo da propriedade title em 20 carateres. |
| . (prop, n)       | Conforme apresentado acima, mas o texto é o sufixo com três pontos é recortada. O tamanho total da cadeia de caracteres abreviada e o sufixo não exceda n caracteres. . (título, 20) com uma propriedade de entrada de "Esta é a linha de título" resulta numa **esse é o título...** |
| %(prop)          | Semelhante à $(name), exceto que a saída é codificada do URI. |
| #(prop)          | Utilizado em modelos JSON (por exemplo, para iOS e Android modelos).<br><br>Esta função funciona exatamente da mesma forma $(prop) anteriormente especificado, exceto quando utilizado em modelos JSON (por exemplo, modelos de Apple). Neste caso, se esta função não está rodeada por "{','}" (por exemplo, "myJsonProperty": "#(nome)"), e ela é avaliada como um número no formato de Javascript, por exemplo, regexp: (0&#124;(&#91;1 a 9&#93;&#91;0 a 9&#93;*)) (\.&#91;0 a 9&#93;+)? ((e&#124;E) (+&#124;-)? &#91;0 a 9&#93;+)?, em seguida, a saída JSON é um número.<br><br>Por exemplo, "destaque:"#(nome)"torna-se"colocar um distintivo": 40 (e não '40"). |
| "texto" ou "texto" | Um literal. Literais contêm texto arbitrário entre aspas simples ou duplas. |
| expr1 + expr2    | O operador de concatenação ingressar em duas expressões numa única cadeia de caracteres. |

As expressões podem ser qualquer um dos formulários anteriores.

Ao usar a concatenação, toda a expressão tem rodeada pela `{}`. Por exemplo, `{$(prop) + ‘ - ’ + $(prop2)}`.

Por exemplo, o modelo seguinte não é um modelo XML válido:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Tal como explicado anteriormente, ao usar a concatenação, expressões tem ser encapsuladas entre chaves. Por exemplo:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```
