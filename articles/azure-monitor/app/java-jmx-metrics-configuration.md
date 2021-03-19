---
title: Como configurar métricas JMX - Insights de aplicação do Azure Monitor para Java
description: Configure a recolha adicional de métricas JMX para insights de aplicação do Azure Monitor Java agente
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609613"
---
# <a name="configuring-jmx-metrics"></a>Configurar métricas JMX

Insights de aplicação Java 3.0 agente recolhe algumas das métricas JMX por padrão, mas em muitos casos isso não é suficiente. Este documento descreve a opção de configuração JMX em detalhes.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Como posso recolher métricas JMX adicionais?

A recolha de métricas JMX pode ser configurada adicionando uma ```"jmxMetrics"``` secção à applicationinsights.jsem arquivo. Pode especificar o nome da métrica da forma como pretende que apareça no portal Azure no recurso de insights de aplicação. Tem de definir o nome e o atributo do objeto para cada uma das métricas que pretende ser recolhidas.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Como sei quais as métricas disponíveis para configurar?

Você pregou -você deve saber os nomes dos objetos e os atributos, essas propriedades são diferentes para várias bibliotecas, quadros e servidores de aplicações, e muitas vezes não estão bem documentados. Para obter os nomes e atributos dos objetos, você precisa ver a árvore MBean. Um MBean é um objeto Java gerido, que pode representar um dispositivo, uma aplicação ou um recurso, e tem um conjunto de atributos. 

Para ver as métricas disponíveis e navegar pelas métricas disponíveis, recomendamos a utilização do [Controlo de Missão de Java.](https://www.oracle.com/java/technologies/jdk-mission-control.html)

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Como navegar no Controlo da Missão Java para chegar às métricas certas?

Quando executar a ferramenta Java Mission Control, terá uma seleção de JVMs disponíveis no lado esquerdo, clique no processo relevante no separador 'JVM Browser'. Aguarde até que o JMC carregue o painel de instrumentos para o processo, selecione o separador 'MBean Browser' na parte inferior (ver abaixo). O JMC deve estar localizado na mesma pasta que o JVM e o seu processo/app deve estar em funcionamento.

![Screenshot do navegador JMC MBean](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Como chegar às métricas que quero, e os atributos necessários?

O navegador MBean abre a árvore MBean com a lista de categorias que podem ser expandidas. Selecionar uma categoria à esquerda abre a lista de atributos à direita. Abaixo está um exemplo de uma métrica, o seu nome de objeto, e os atributos. Os atributos podem estar aninhados, como no exemplo abaixo.

![Screenshot da árvore JMC MBean](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Exemplo de configuração

A partir da seleção, como mostrado na imagem acima, permite configurar algumas métricas. O primeiro é um exemplo de uma métrica aninhada - `LastGcInfo` que tem várias propriedades, e queremos capturar o `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Tipos de métricas recolhidas e opções de configuração disponíveis?

Apoiamos métricas JMX numéricas e booleanas, enquanto outros tipos não são suportados e serão ignorados. 

Atualmente, os wildcards e atributos agregados não são suportados, é por isso que todos os pares de atributos 'object name'/'attribute' devem ser configurados separadamente. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Onde encontro as Métricas JMX em insights de aplicação?

À medida que a sua aplicação está em execução e as métricas JMX são recolhidas, pode vê-las indo ao portal Azure e navegar para o recurso de insights da sua aplicação. No separador Métricas, selecione o dropdown como mostrado abaixo para ver as métricas.

![Screenshot de métricas no portal](media/java-ipa/jmx/jmx-portal.png)
