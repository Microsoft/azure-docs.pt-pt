---
title: Problemas de resolução de problemas com a Azure Event Hubs para Apache Kafka
description: Este artigo mostra como resolver problemas com os hubs de eventos da Azure para Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061432"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guia de resolução de problemas de Apache Kafka para centros de eventos
Este artigo fornece dicas de resolução de problemas para problemas que você pode encontrar ao usar Os Centros de Eventos para Apache Kafka. 

## <a name="server-busy-exception"></a>Server Ocupada exceção
Pode receber a exceção Server Busy por causa do estrangulamento de Kafka. Com clientes AMQP, o Event Hubs devolve imediatamente uma exceção **ocupada do servidor** após o estrangulamento do serviço. É equivalente a uma mensagem de "tente de novo mais tarde". Em Kafka, as mensagens são atrasadas antes de serem concluídas. O comprimento do atraso é devolvido em milissegundos como `throttle_time_ms` na resposta de produzir/buscar. Na maioria dos casos, estes pedidos atrasados não são registados como exceções ocupadas do servidor nos dashboards do Event Hubs. Em vez disso, o valor da resposta `throttle_time_ms` deve ser utilizado como um indicador de que a produção excedeu a quota prevista.

Se o tráfego for excessivo, o serviço tem o seguinte comportamento:

- Se o atraso do pedido de produção exceder o tempo limite de pedido, o Event Hubs devolve o código de erro **de Violação de Política.**
- Se o atraso do pedido de procura exceder o tempo limite de pedido, o Event Hubs regista o pedido como estrangulado e responde com um conjunto vazio de registos e nenhum código de erro.

[Aglomerados dedicados](event-hubs-dedicated-overview.md) não têm mecanismos de estrangulamento. És livre de consumir todos os teus recursos de agrupamento.

## <a name="no-records-received"></a>Não recebidos registos
Pode ver os consumidores não obterem quaisquer registos e reequilibrem-se constantemente. Neste cenário, os consumidores não obtêm registos e reequilibram-se constantemente. Não há exceção ou erro quando isso acontece, mas os registos de Kafka mostrarão que os consumidores estão presos a tentar voltar ao grupo e atribuir divisórias. Existem algumas causas possíveis:

- Certifique-se de que o seu `request.timeout.ms` valor é pelo menos o valor recomendado de 60000 e o seu valor é pelo menos o valor recomendado de `session.timeout.ms` 30000. Ter estas configurações demasiado baixas pode causar intervalos de tempo para o consumidor, que depois causam reequilíbrios (que depois causam mais intervalos, que causam mais reequilíbrio, e assim por diante) 
- Se a sua configuração corresponder aos valores recomendados, e ainda estiver a ver um reequilíbrio constante, sinta-se à vontade para abrir um problema (certifique-se de incluir toda a sua configuração no problema para que possamos ajudar a depurar)!

## <a name="compressionmessage-format-version-issue"></a>Emissão de versão de formato de compressão/mensagem
Kafka suporta a compressão, e os Centros de Eventos para Kafka atualmente não. Erros que mencionam uma versão em formato de mensagem (por exemplo, `The message format version on the broker does not support the request.` ) são causados quando um cliente tenta enviar mensagens kafka comprimidos para os nossos corretores.

Se forem necessários dados comprimidos, comprimir os seus dados antes de os enviar aos corretores e descomprimir após receber é uma solução válida. O corpo da mensagem é apenas um byte array para o serviço, por isso a compressão/descompressão do lado do cliente não causará problemas.

## <a name="unknownserverexception"></a>Desconhecidasexcepção
Poderá receber uma UnknownServerException de bibliotecas de clientes kafka semelhante ao seguinte exemplo: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Abra um bilhete com suporte da Microsoft.  Os registos de registo ao nível de depurado e os tempos de exceção na UTC são úteis para depurar o problema. 

## <a name="other-issues"></a>Outros problemas
Verifique os seguintes itens se vê problemas ao utilizar Kafka em Centros de Eventos.

- **Firewall blocking traffic** - Certifique-se de que a porta **9093** não está bloqueada pela sua firewall.
- **TópicoAuthorizationException** - As causas mais comuns desta exceção são:
    - Um erro na cadeia de ligação no seu ficheiro de configuração, ou
    - Tentando usar os Centros de Eventos para Kafka num espaço de nome de nível básico. O evento Hubs para kafka é [suportado apenas para espaços de nome standard e de nível dedicado.](https://azure.microsoft.com/pricing/details/event-hubs/)
- **Desajuste da versão Kafka** - Os Centros de Eventos para Ecossistemas Kafka suportam as versões 1.0 e posteriores da Kafka. Algumas aplicações que usam a versão 0.10 de Kafka e mais tarde podem ocasionalmente funcionar devido à retrocompatibilidade do protocolo kafka, mas recomendamos veementemente contra a utilização de versões antigas da API. As versões Kafka 0.9 e anteriores não suportam os protocolos SASL necessários e não podem ligar-se aos Centros de Eventos.
- **Codificações estranhas em cabeçalhos AMQP ao consumir com Kafka** - ao enviar eventos para um centro de eventos sobre AMQP, quaisquer cabeçalhos de carga amQP são serializados em codificação AMQP. Os consumidores de Kafka não desserizaram os cabeçalhos da AMQP. Para ler os valores dos cabeçalhos, descodifique manualmente os cabeçalhos AMQP. Em alternativa, pode evitar usar cabeçalhos AMQP se souber que vai consumir através do protocolo Kafka. Para mais informações, consulte [esta edição do GitHub.](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)
- **Autenticação SASL** - Conseguir que a sua estrutura coopere com o protocolo de autenticação SASL exigido pelos Event Hubs pode ser mais difícil do que se vê. Veja se consegue resolver problemas na configuração utilizando os recursos da sua estrutura na autenticação SASL. 

## <a name="limits"></a>Limites
Apache Kafka vs. Event Hubs Kafka. Na maior parte das vezes, a interface kafka do Azure Event Hubs tem os mesmos padrão, propriedades, códigos de erro e comportamento geral que a Apache Kafka tem. Os casos que estes dois diferem explicitamente (ou onde os Centros de Eventos impõem um limite que Kafka não) estão listados abaixo:

- O comprimento máximo da propriedade é de `group.id` 256 caracteres
- O tamanho máximo de `offset.metadata.max.bytes` 1024 bytes
- Os compromissos de compensação são acelerados para 4 chamadas/segundo por partição com um tamanho máximo de log interno de 1 MB


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre Os Centros de Eventos e Centros de Eventos para Kafka, consulte os seguintes artigos:  

- [Guia de desenvolvedores apache Kafka para centros de eventos](apache-kafka-developer-guide.md)
- [Guia de migração apache Kafka para centros de eventos](apache-kafka-migration-guide.md)
- [Perguntas frequentes - Centros de Eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Configurações recomendadas](apache-kafka-configurations.md)
