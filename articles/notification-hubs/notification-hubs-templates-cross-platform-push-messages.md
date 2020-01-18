---
title: Modelos de hubs de notificação do Azure
description: Saiba mais sobre como usar modelos para os hubs de notificação do Azure.
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
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263749"
---
# <a name="templates"></a>Modelos

Os modelos permitem que um aplicativo cliente especifique o formato exato das notificações que deseja receber. Usando modelos, um aplicativo pode obter vários benefícios diferentes, incluindo o seguinte:

- Um back-end independente de plataforma
- Notificações personalizadas
- Independência de versão do cliente
- Localização fácil

Esta seção fornece dois exemplos detalhados de como usar modelos para enviar notificações independentes de plataforma direcionando todos os seus dispositivos entre plataformas e para personalizar a notificação de difusão para cada dispositivo.

## <a name="using-templates-cross-platform"></a>Usando modelos de plataforma cruzada

A maneira padrão de enviar notificações por push é enviar, para cada notificação a ser enviada, uma carga específica para os serviços de notificação de plataforma (WNS, APNS). Por exemplo, para enviar um alerta para o APNS, a carga é um objeto JSON da seguinte forma:

```json
{"aps": {"alert" : "Hello!" }}
```

Para enviar uma mensagem de notificação semelhante em um aplicativo da Windows Store, a carga XML é a seguinte:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Você pode criar cargas semelhantes para as plataformas MPNS (Windows Phone) e FCM (Android).

Esse requisito força o back-end do aplicativo a produzir cargas diferentes para cada plataforma e torna efetivamente o back-end responsável por parte da camada de apresentação do aplicativo. Algumas preocupações incluem a localização e os layouts gráficos (especialmente para aplicativos da Windows Store que incluem notificações para vários tipos de blocos).

O recurso de modelo de hubs de notificação permite que um aplicativo cliente crie registros especiais, chamados de registros de modelo, que incluem, além do conjunto de marcas, um modelo. O recurso de modelo de hubs de notificação permite que um aplicativo cliente associe dispositivos a modelos se você estiver trabalhando com instalações (preferenciais) ou registros. Considerando os exemplos de carga precedentes, as únicas informações independentes de plataforma são a mensagem de alerta real (Olá!). Um modelo é um conjunto de instruções para o Hub de notificação sobre como formatar uma mensagem independente de plataforma para o registro desse aplicativo cliente específico. No exemplo anterior, a mensagem independente de plataforma é uma única propriedade: `message = Hello!`.

A figura a seguir ilustra o processo:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

O modelo para o registro do aplicativo cliente do iOS é o seguinte:

```json
{"aps": {"alert": "$(message)"}}
```

O modelo correspondente para o aplicativo cliente da Windows Store é:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Observe que a mensagem real é substituída pela expressão $ (Message). Essa expressão instrui o Hub de notificação, sempre que envia uma mensagem para esse registro específico, para criar uma mensagem que segue e alterna o valor comum.

Se você estiver trabalhando com o modelo de instalação, a chave de instalação "modelos" mantém um JSON de vários modelos. Se você estiver trabalhando com o modelo de registro, o aplicativo cliente poderá criar vários registros para usar vários modelos; por exemplo, um modelo para mensagens de alerta e um modelo para atualizações de bloco. Os aplicativos cliente também podem misturar registros nativos (registros sem modelo) e registros de modelo.

O Hub de notificação envia uma notificação para cada modelo sem considerar se eles pertencem ao mesmo aplicativo cliente. Esse comportamento pode ser usado para converter notificações independentes de plataforma em mais notificações. Por exemplo, a mesma mensagem independente de plataforma para o Hub de notificação pode ser convertida diretamente em um alerta do sistema e uma atualização de bloco, sem exigir que o back-end esteja ciente dela. Algumas plataformas (por exemplo, iOS) podem recolher várias notificações para o mesmo dispositivo se elas forem enviadas em um curto período de tempo.

## <a name="using-templates-for-personalization"></a>Usando modelos para personalização

Outra vantagem de usar modelos é a capacidade de usar os hubs de notificação para executar a personalização por registro de notificações. Por exemplo, considere um aplicativo meteorológico que exibe um bloco com as condições meteorológicos em um local específico. Um usuário pode escolher entre graus Celsius ou Fahrenheit e uma previsão única ou de cinco dias. Usando modelos, cada instalação de aplicativo cliente pode se registrar para o formato necessário (1 dia Celsius, 1 dia Fahrenheit, 5 dias Celsius, 5 dias em Fahrenheit) e fazer com que o back-end envie uma única mensagem que contenha todas as informações necessárias para preencher esses modelos (por exemplo, uma previsão de cinco dias com graus Celsius e Fahrenheit).

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

A mensagem enviada para o Hub de notificação contém todas as seguintes propriedades:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Usando esse padrão, o back-end envia apenas uma única mensagem sem precisar armazenar opções de personalização específicas para os usuários do aplicativo. A figura a seguir ilustra esse cenário:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Como registrar modelos

Para registrar com modelos usando o modelo de instalação (preferencial) ou o modelo de registro, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Linguagem de expressão de modelo

Os modelos são limitados a formatos de documento XML ou JSON. Além disso, você só pode colocar expressões em locais específicos; por exemplo, atributos de nó ou valores para XML, valores de propriedade de cadeia de caracteres para JSON.

A tabela a seguir mostra o idioma permitido nos modelos:

| Expressão       | Descrição |
| ---------------- | --- |
| $ (prop)          | Referência a uma propriedade de evento com o nome fornecido. Os nomes de propriedade não diferenciam maiúsculas de minúsculas. Essa expressão é resolvida no valor de texto da propriedade ou em uma cadeia de caracteres vazia se a propriedade não estiver presente. |
| $ (prop, n)       | Como acima, mas o texto é explicitamente cortado em n caracteres, por exemplo, $ (title, 20) corta o conteúdo da propriedade Title com 20 caracteres. |
| . (prop, n)       | Como acima, mas o texto é sufixado com três pontos à medida que é recortado. O tamanho total da cadeia de caracteres recortada e o sufixo não excedem n caracteres. . (título, 20) com uma propriedade de entrada de "esta é a linha de título" resulta neste **é o título...** |
| % (prop)          | Semelhante a $ (Name), exceto que a saída é codificada por URI. |
| # (prop)          | Usado em modelos JSON (por exemplo, para modelos iOS e Android).<br><br>Essa função funciona exatamente da mesma forma que $ (prop) especificada anteriormente, exceto quando usada em modelos JSON (por exemplo, modelos da Apple). Nesse caso, se essa função não estiver entre "{", "}" (por exemplo, "myjsonproperty": "# (Name)") e for avaliada como um número no formato JavaScript,&#124;por exemplo, RegExp: (0 (&#91;1-9&#93;&#91;0-9&#93;*)) (\.&#91;0-9&#93;+)? ((e&#124;) (+&#124;-)? &#91;0-9&#93;+)?, o JSON de saída é um número.<br><br>Por exemplo, ' Emblema: ' # (Name) ' torna-se ' crachá ': 40 (e não ' 40 '). |
| ' texto ' ou "texto" | Um literal. Os literais contêm texto arbitrário entre aspas simples ou duplas. |
| expr1 + expr2    | O operador de concatenação que está unindo duas expressões em uma única cadeia de caracteres. |

As expressões podem ser qualquer um dos formulários anteriores.

Ao usar a concatenação, a expressão inteira deve estar entre `{}`. Por exemplo, `{$(prop) + ‘ - ’ + $(prop2)}`.

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

Conforme explicado anteriormente, ao usar a concatenação, as expressões devem ser encapsuladas entre chaves. Por exemplo:

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

[Saiba mais sobre os hubs de notificação do Azure](notification-hubs-push-notification-overview.md)