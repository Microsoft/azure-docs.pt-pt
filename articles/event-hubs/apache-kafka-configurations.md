---
title: Configurações recomendadas para clientes Apache Kafka - Azure Event Hubs
description: Este artigo fornece configurações recomendadas de Apache Kafka para clientes que interagem com a Azure Event Hubs para Apache Kafka.
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: be009aae41b2cb26ab02fdbe14bc4e18311ad235
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042356"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Configurações recomendadas para clientes Apache Kafka
Aqui estão as configurações recomendadas para a utilização de Azure Event Hubs a partir de aplicações de clientes Apache Kafka. 

## <a name="java-client-configuration-properties"></a>Propriedades de configuração do cliente Java

### <a name="producer-and-consumer-configurations"></a>Configurações de produtor e consumidor

Propriedade | Valores recomendados | Alcance permitido | Notas
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (aproximadamente) | < 240000 | Pode ser baixado para recolher mudanças de metadados mais cedo.
`connections.max.idle.ms`   | 180000 | < 240000 | O Azure fecha a entrada da TCP inativa > 240.000 ms, o que pode resultar no envio de ligações mortas (mostradas como lotes expirados devido ao tempo de envio).

### <a name="producer-configurations-only"></a>Apenas configurações de produtores
Os configs do produtor podem ser encontrados [aqui.](https://kafka.apache.org/documentation/#producerconfigs)

Propriedade | Valores Recomendados | Gama Permitida | Notas
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | O serviço fechará as ligações se forem enviados pedidos superiores a 1.046.528 bytes.  *Este valor **deve** ser alterado e irá causar problemas em cenários de produção de alta produção.*
`retries` | > 0 | | Pode exigir um aumento delivery.timeout.ms valor, consulte a documentação.
`request.timeout.ms` | 30000 .. 60000 | > 20000| O EH irá internalizar para um mínimo de 20.000 ms.  *Embora os pedidos com valores de tempo limite mais baixos sejam aceites, o comportamento do cliente não é garantido.*
`metadata.max.idle.ms` | 180000 | > 5000 | Controla quanto tempo o produtor vai cache metadados para um tópico que está inativo. Se o tempo decorrido desde que um tópico foi produzido pela última vez exceder a duração dos metadados inativos, então os metadados do tópico são esquecidos e o próximo acesso a ele forçará um pedido de procura de metadados.
`linger.ms` | > 0 | | Para cenários de produção elevada, o valor de persituos deve ser igual ao valor tolerável mais elevado para tirar partido do loteamento.
`delivery.timeout.ms` | | | Definido de acordo com a fórmula `request.timeout.ms`  +  `linger.ms` ( * `retries` * .
`compression.type` | `none` | | Compressão atualmente não suportada.

### <a name="consumer-configurations-only"></a>Apenas configurações de consumo
Os consumidores podem ser encontrados [aqui.](https://kafka.apache.org/documentation/#consumerconfigs)

Propriedade | Valores Recomendados | Gama Permitida | Notas
---|---:|-----:|---
`heartbeat.interval.ms` | 3.000 | | 3000 é o valor padrão e não deve ser alterado.
`session.timeout.ms` | 30000 |6000 .. 300000| Comece com 30000, aumente se ver reequilíbrio frequente devido a batimentos cardíacos perdidos.


## <a name="librdkafka-configuration-properties"></a>propriedades de configuração librdkafka
O ficheiro de configuração principal `librdkafka` [(link)](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)contém descrições estendidas para as propriedades abaixo.

### <a name="producer-and-consumer-configurations"></a>Configurações de produtor e consumidor

Propriedade | Valores Recomendados | Gama Permitida | Notas
---|---:|-----:|---
`socket.keepalive.enable` | true | | Necessário para que a ligação esteja inativa.  O Azure vai fechar a entrada da TCP > 240.000 ms.
`metadata.max.age.ms` | ~ 180000| < 240000 | Pode ser baixado para recolher mudanças de metadados mais cedo.

### <a name="producer-configurations-only"></a>Apenas configurações de produtores

Propriedade | Valores Recomendados | Gama Permitida | Notas
---|---:|-----:|---
`retries` | > 0 | | O padrão é 2. Recomendamos que mantenha este valor. 
`request.timeout.ms` | 30000 .. 60000 | > 20000| O EH irá internalizar para um mínimo de 20.000 ms.  `librdkafka` o valor padrão é 5000, o que pode ser problemático. *Embora os pedidos com valores de tempo limite mais baixos sejam aceites, o comportamento do cliente não é garantido.*
`partitioner` | `consistent_random` | Ver documentação librdkafka | `consistent_random` é padrão e melhor.  As chaves vazias e nulas são manuseadas idealmente para a maioria dos casos.
`compression.codec` | `none` || A compressão não está suportada atualmente.

### <a name="consumer-configurations-only"></a>Apenas configurações de consumo

Propriedade | Valores Recomendados | Gama Permitida | Notas
---|---:|-----:|---
`heartbeat.interval.ms` | 3.000 || 3000 é o valor padrão e não deve ser alterado.
`session.timeout.ms` | 30000 |6000 .. 300000| Comece com 30000, aumente se ver reequilíbrio frequente devido a batimentos cardíacos perdidos.


## <a name="further-notes"></a>Outras notas

Verifique a seguinte tabela de cenários de erro relacionados com a configuração comum.

Sintomas | Problema | Solução
----|---|-----
Compensação cometer falhas por causa do reequilíbrio | O seu consumidor está à espera demasiado tempo entre as chamadas para a sondagem e o serviço está a expulsar o consumidor do grupo. | Tem várias opções: <ul><li>Aumentar o tempo de processamento da sondagem `max.poll.interval.ms` ()</li><li>Diminua o tamanho do lote de mensagens para acelerar o processamento</li><li>Melhorar a paralelização do processamento para evitar o bloqueio ao consumidor.poll()</li></ul> Aplicar alguma combinação dos três é provavelmente o mais sensato.
Exceções da rede em produção elevada | Está a utilizar o cliente Java + max.request.size?  Os seus pedidos podem ser muito grandes. | Veja Java configs acima.

## <a name="next-steps"></a>Passos seguintes
Consulte [os limites de subscrição e serviços da Azure, quotas e restrições](..//azure-resource-manager/management/azure-subscription-service-limits.md) para quotas e limites de todos os serviços da Azure. 
