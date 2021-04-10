---
title: Sobreposições de amostragem (pré-visualização) - Azure Monitor Application Insights for Java
description: Aprenda a configurar amostras sobrepõe-se em Insights de Aplicação do Monitor Azure para Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 17979bd548ca0d7b704ebdeb4d060bf35973b319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024152"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Sobreposições de amostragem (pré-visualização) - Azure Monitor Application Insights for Java

> [!NOTE]
> A função de sobreposições de amostragem está em pré-visualização, a partir de 3.0.3-BETA.2.

As sobreposições de amostragem permitem-lhe anular a [percentagem de amostragem padrão,](./java-standalone-config.md#sampling)por exemplo:
 * Desafete a percentagem de amostragem a 0 (ou algum pequeno valor) para verificações de saúde ruidosas.
 * Desa estava a percentagem de amostragem a 0 (ou algum pequeno valor) para chamadas de dependência ruidosas.
 * Defina a percentagem de amostragem para 100 para um tipo de pedido importante (por `/login` exemplo), mesmo que tenha a amostra padrão configurada para algo inferior.

## <a name="terminology"></a>Terminologia

Antes de aprender sobre as sobreposições de amostragem, deve entender o termo *período*. Um período de tempo é um termo geral para:

* Um pedido de entrada.
* Uma dependência de saída (por exemplo, uma chamada remota para outro serviço).
* Uma dependência em processo (por exemplo, trabalho a ser feito por subcomponentes do serviço).

Para as sobreposições de amostragem, estes componentes de envergadura são importantes:

* Atributos

Os atributos de envergadura representam propriedades padrão e personalizadas de um dado pedido ou dependência.

## <a name="getting-started"></a>Introdução

Para começar, crie um ficheiro de configuração nomeado *applicationinsights.jsem*. Guarde-o no mesmo diretório que *o \* agente-agente de aplicações .jar*. Utilize o modelo seguinte.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Como funciona

Quando uma extensão é iniciada, os atributos presentes no intervalo nessa altura são usados para verificar se alguma das amostras se sobrepõe.

Se uma das amostras se sobrepor, a sua percentagem de amostragem é utilizada para decidir se deve ou não provar o intervalo.

Apenas é utilizada a primeira amostra de amostragem que corresponde.

Se nenhuma amostragem se sobrepor:

* Se esta for a primeira extensão do vestígio, então a [percentagem de amostragem padrão](./java-standalone-config.md#sampling) é utilizada.
* Se esta não for a primeira extensão do vestígio, então a decisão de amostragem dos pais é utilizada.

> [!IMPORTANT]
> Quando tiver sido tomada a decisão de não recolher um período de tempo, então todos os períodos a jusante também não serão recolhidos, mesmo que existam sobreposições de amostragem que correspondam ao período a jusante.
> Este comportamento é necessário porque, de outra forma, os vestígios partidos resultariam, com os vãos a jusante a serem recolhidos, mas sendo parentados em vãos que não foram recolhidas.

> [!NOTE]
> A decisão de amostragem baseia-se na hashing do traceId (também conhecido como operationId) a um número entre 0 e 100, e esse haxixe é então comparado com a percentagem de amostragem.
> Uma vez que todos os períodos de um determinado vestígio terão o mesmo traceId, eles terão o mesmo haxixe, e assim a decisão de amostragem será consistente em todo o vestígio.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Exemplo: Suprimir a recolha de telemetria para controlos de saúde

Isto suprimirá a recolha de telemetria para todos os pedidos de `/health-checks` .

Isto também suprimirá a recolha de quaisquer períodos a jusante (dependências) que normalmente seriam recolhidos sob `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Exemplo: Suprimir a recolha de telemetria para uma chamada de dependência ruidosa

Isto suprimirá a recolha de telemetria para todas as `GET my-noisy-key` chamadas redis.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Exemplo: Recolher 100% de telemetria para um tipo de pedido importante

Isto irá recolher 100% de telemetria para `/login` .

Uma vez que os períodos a jusante (dependências) respeitam a decisão de amostragem do progenitor (sem qualquer sobreposição de amostragem para esse período a jusante), esses serão igualmente recolhidos para todos os pedidos de "/login".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Atributos de extensão comum

Esta secção lista alguns atributos de envergadura comuns que a amostragem sobrepõe-se.

### <a name="http-spans"></a>Extensões HTTP

| Atributo  | Tipo | Description | 
|---|---|---|
| `http.method` | cadeia (de carateres) | Método de pedido HTTP.|
| `http.url` | string | URL de pedido completo http no formulário `scheme://host[:port]/path?query[#fragment]` . O fragmento não é normalmente transmitido através de HTTP. Mas se o fragmento for conhecido, deve ser incluído.|
| `http.status_code` | número | [Código de estado de resposta HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo de protocolo HTTP. |
| `http.user_agent` | string | Valor do [cabeçalho http-user-agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>Extensões JDBC

| Atributo  | Tipo | Description  |
|---|---|---|
| `db.system` | cadeia (de carateres) | Identificador para o sistema de gestão de bases de dados (DBMS) produto que está a ser utilizado. |
| `db.connection_string` | string | Cadeia de ligação usada para ligar à base de dados. É recomendado para remover credenciais embutidas.|
| `db.user` | string | Nome de utilizador para aceder à base de dados. |
| `db.name` | string | O String costumava reportar o nome da base de dados a ser acedida. Para comandos que comutem a base de dados, esta cadeia deve ser definida para a base de dados alvo, mesmo que o comando falhe.|
| `db.statement` | string | Declaração de base de dados que está a ser executada.|
