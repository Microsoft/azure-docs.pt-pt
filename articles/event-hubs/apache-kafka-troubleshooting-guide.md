---
title: Problemas com Hubs de Eventos Azure para Apache Kafka
description: Este artigo mostra como resolver problemas com o Azure Event Hubs para Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606733"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guia de resolução de problemas apache Kafka para Centros de Eventos
Este artigo fornece dicas de resolução de problemas para problemas que você pode encontrar ao usar Hubs de evento para Apache Kafka. 

## <a name="server-busy-exception"></a>Exceção do Servidor Ocupado
Pode receber a exceção do Server Busy devido à estrangulamento de Kafka. Com os clientes AMQP, o Event Hubs devolve imediatamente uma exceção **do Server Busy** após a aceleração do serviço. É equivalente a uma mensagem de "tentar de novo mais tarde". Em Kafka, as mensagens são adiadas antes de serem concluídas. O comprimento do atraso é `throttle_time_ms` devolvido em milissegundos como na resposta de produção/busca. Na maioria dos casos, estes pedidos atrasados não são registados como exceções serverBusy em dashboards De Event Hubs. Em vez disso, `throttle_time_ms` o valor da resposta deve ser utilizado como indicador de que a produção excedeu o contingente provisionado.

Se o tráfego for excessivo, o serviço tem o seguinte comportamento:

- Se o atraso do pedido de produto exceder o tempo limite de pedido, o Event Hubs devolve o código de erro **de violação** de políticas.
- Se o atraso do pedido exceder o tempo limite de pedido, o Event Hubs regista o pedido como estrangulado e responde com um conjunto vazio de registos e sem código de erro.

[Aglomerados dedicados](event-hubs-dedicated-overview.md) não têm mecanismos de estrangulamento. É livre de consumir todos os seus recursos de agrupamento.

## <a name="no-records-received"></a>Não são recebidos registos.
Pode ver que os consumidores não recebem registos e estão constantemente a reequilibrar-se. Neste cenário, os consumidores não obtêm registos e reequilibram constantemente. Não há exceção ou erro quando isso acontece, mas os registos de Kafka mostrarão que os consumidores estão presos a tentar juntar-se ao grupo e a atribuir divisórias. Existem algumas causas possíveis:

- Certifique-se `request.timeout.ms` de que o seu tem pelo menos `session.timeout.ms` o valor recomendado de 60000 e o seu valor pelo menos 30000. Ter estas configurações demasiado baixas pode causar intervalos de tempo para os consumidores, que depois causam reequilíbrios (que depois causam mais intervalos, que causam mais reequilíbrio, e assim por diante) 
- Se a sua configuração corresponder aos valores recomendados, e ainda estiver a ver um reequilíbrio constante, sinta-se à vontade para abrir um problema (certifique-se de incluir toda a sua configuração no problema para que possamos ajudar a depurar)!

## <a name="compressionmessage-format-version-issue"></a>Problema da versão do formato compressão/mensagem
Kafka apoia a compressão, e os Centros de Eventos para Kafka atualmente não. Erros que mencionam uma versão de `The message format version on the broker does not support the request.`formato de mensagem (por exemplo, ) são causados quando um cliente tenta enviar mensagens Kafka comprimidos para os nossos corretores.

Se forem necessários dados comprimidos, comprimir os seus dados antes de enviá-los aos corretores e descomprimir depois de receber é uma supressão válida. O corpo da mensagem é apenas um matriz byte para o serviço, para que a compressão/descompressão do lado do cliente não cause problemas.

## <a name="unknownserverexception"></a>Exceção do Servidor Desconhecido
Pode receber uma Exceção UnknownServer das bibliotecas de clientes kafka semelhante ao seguinte exemplo: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Abra um bilhete com suporte da Microsoft.  A exploração madeireira de nível de depuração e os carimbos de tempo de exceção na UTC são úteis para depurar a questão. 

## <a name="other-issues"></a>Outros problemas
Verifique os seguintes itens se vir problemas ao utilizar Kafka em Centros de Eventos.

- **Firewall bloqueando o tráfego** - Certifique-se de que a porta **9093** não está bloqueada pela sua firewall.
- **Exceção tópicoS -** As causas mais comuns desta exceção são:
    - Um erro na cadeia de ligação no seu ficheiro de configuração, ou
    - Tentando usar Os Centros de Eventos para Kafka num espaço de nome básico. Os Hubs de Eventos para Kafka só são [suportados para espaços](https://azure.microsoft.com/pricing/details/event-hubs/)de nome standard e dedicados.
- **Desajuste da versão Kafka** - Os Hubs de Eventos para Os Ecossistemas Kafka suportam as versões Kafka 1.0 e mais tarde. Algumas aplicações que usam a versão 0.10 de Kafka e mais tarde podem ocasionalmente funcionar devido à retrocompatibilidade do protocolo kafka, mas recomendamos veementemente não usar versões API antigas. As versões Kafka 0.9 e anteriores não suportam os protocolos SASL necessários e não conseguem ligar-se aos Centros de Eventos.
- **Estranhas codificações em cabeçalhos AMQP ao consumir com Kafka** - ao enviar eventos para um centro de eventos sobre amqp, quaisquer cabeçalhos de carga útil AMQP são serializados na codificação AMQP. Os consumidores de Kafka não desserializam os cabeçalhos da AMQP. Para ler os valores do cabeçalho, descodifique manualmente os cabeçalhos AMQP. Em alternativa, pode evitar usar cabeçalhos AMQP se souber que estará a consumir através do protocolo Kafka. Para mais informações, consulte [esta edição do GitHub.](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)
- **Autenticação SASL** - Conseguir que o seu quadro coopere com o protocolo de autenticação SASL exigido pelos Centros de Eventos pode ser mais difícil do que se vê. Veja se consegue resolver a configuração utilizando os recursos do seu quadro na autenticação SASL. 

## <a name="limits"></a>Limites
Apache Kafka vs. Event Hubs Kafka. Na maior parte dos casos, os Hubs de Eventos dos Ecossistemas Kafka têm os mesmos incumprimentos, propriedades, códigos de erro e comportamento geral que apache Kafka tem. Os casos em que estes dois diferem explicitamente (ou onde os Hubs de Eventos impõem um limite que Kafka não) são listados abaixo:

- O comprimento máximo `group.id` da propriedade é de 256 caracteres
- O tamanho `offset.metadata.max.bytes` máximo é de 1024 bytes
- Os compromissos compensados são estrangulados a 4 chamadas/segundo por partição com um tamanho máximo de registo interno de 1 MB


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Guia de desenvolvimento apache Kafka para Centros de Eventos](apache-kafka-developer-guide.md)
- [Guia de migração Apache Kafka para Centros de Eventos](apache-kafka-migration-guide.md)
- [Perguntas frequentes - Centros de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
