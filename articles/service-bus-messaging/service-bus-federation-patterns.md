---
title: Padrões de tarefa de replicação de mensagens - Azure Service Bus / Microsoft Docs
description: Este artigo fornece orientações detalhadas para implementar padrões específicos de tarefa de replicação de mensagens
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657505"
---
# <a name="message-replication-tasks-patterns"></a>Padrões de tarefas de replicação de mensagens

A [visão geral da federação](service-bus-federation-overview.md) e as [funções do replicador](service-bus-federation-replicator-functions.md) explicam a lógica e os elementos básicos das tarefas de replicação, e recomenda-se que se familiarize com elas antes de continuar com este artigo.

Neste artigo, detalhamos a orientação de implementação de vários dos padrões destacados na secção de visão geral. 

## <a name="replication"></a>Replicação 

O padrão de replicação copia mensagens de uma fila ou tópico para o outro, ou de uma fila ou tópico para outro destino como um Centro de Eventos. As mensagens são reencaminhadas sem escamar qualquer modificação na carga útil da mensagem. 

A implementação deste padrão é coberta pela [replicação da mensagem de e para a amostra de Azure Service Bus.](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)

### <a name="sequences-and-order-preservation"></a>Sequências e preservação da ordem

O modelo de replicação não visa preservar a ordem absoluta de mensagens de uma fila de origem ou tópico numa fila ou tópico alvo, mas foca-se, sempre que necessário, na preservação da ordem relativa das mensagens onde a aplicação o exija. A aplicação permite-o permitindo o suporte de sessão para a entidade de origem e agrupar mensagens relacionadas com a mesma [tecla de sessão](message-sessions.md).

As funções de replicação pré-construídas conscientes da sessão asseguram que as sequências de mensagens com o mesmo id de sessão recuperado de uma entidade de origem são submetidas na fila ou tópico alvo como um lote na sequência original e com o mesmo id de sessão. 

### <a name="service-assigned-metadata"></a>Metadados atribuídos pelo serviço 

Os metadados atribuídos pelo serviço de uma mensagem obtida a partir da fila de origem ou tópico, o número original de tempo e sequência de encademosos, são substituídos por novos valores atribuídos ao serviço na fila ou tópico alvo, mas com as tarefas de replicação predefinidas que são fornecidas nas nossas amostras, os valores originais são preservados nas propriedades do utilizador: `repl-enqueue-time` (cadeia ISO8601) e `repl-sequence` .

Essas propriedades são de tipo string e contêm o valor stringified das respetivas propriedades originais.  Se a mensagem for reencaminhada várias vezes, os metadados atribuídos pelo serviço da fonte imediata são anexados às propriedades já existentes, com valores separados por pontos-e-vírgula. 

### <a name="failover"></a>Ativação pós-falha

Se estiver a utilizar a replicação para fins de recuperação de desastres, para proteger contra mensagens de disponibilidade regional no serviço Service Bus, ou contra interrupções de rede, qualquer cenário de falha deste tipo exigirá a realização de uma falha de uma fila ou tópico para o próximo, dizendo aos produtores e/ou consumidores para usarem o ponto final secundário.

Para todos os cenários de failover, presume-se que os elementos necessários dos espaços de nome são estruturalmente idênticos, o que significa que as filas e tópicos são nomeados de forma idêntica e que as regras de acesso partilhado e/ou regras de controlo de acesso baseadas em funções são criadas da mesma forma. Pode criar (e atualizar) um espaço de nome secundário seguindo as [orientações para mover espaços de nome](move-across-regions.md) e omitir o passo de limpeza.

Para forçar os produtores e consumidores a mudar, é necessário disponibilizar a informação sobre qual o espaço de identificação a utilizar para a procura num local de fácil acesso e atualização. Se os produtores ou consumidores encontrarem erros frequentes ou persistentes, devem consultar esse local e ajustar a sua configuração. Existem inúmeras formas de partilhar essa configuração, mas apontamos duas no seguinte: DNS e partilhas de ficheiros.

#### <a name="dns-based-failover-configuration"></a>Configuração de failover baseada em DNS

Uma abordagem de candidato é manter a informação nos registos DNS SRV num DNS que controla e apontar para as respetivas filas ou pontos finais tópicos. Tenha em atenção que o Hubs de mensagens não permite que os seus pontos finais sejam diretamente aliasados com registos CNAME, o que significa que utilizará o DNS como um mecanismo de procura resiliente para endereços de ponto final e não resolverá diretamente as informações do endereço IP. 

Assuma que é dono do domínio `example.com` e, para a sua aplicação, de uma `test.example.com` zona. Para dois Service Bus alternativos, irá agora criar mais duas zonas aninhadas e um registo SRV em cada uma. 

Os registos SRV são, seguindo convenção comum, prefixados `_azure_servicebus._amqp` e detêm dois registos de ponto final: um para AMQP-over-TLS na porta 5671 e outro para AMQP-over-WebSockets na porta 443, ambos apontando para o ponto final do serviço de ônibus do espaço de nome correspondente à zona.

| Zona                 | Registo SRV
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

Na zona da sua aplicação, irá então criar uma entrada CNAME que aponta para a zona subordinada correspondente à sua fila primária ou tópico:

| Registo CNAME                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Utilizando um cliente DNS que permite consultar explicitamente os registos CNAME e SRV (os clientes incorporados da Java e .NET apenas permitem uma simples resolução de nomes para endereços IP), pode então resolver o ponto final pretendido. Com [DnsClient.NET,](https://dnsclient.michaco.net/)por exemplo, a função de procuração é:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

A função devolve o nome de anfitrião-alvo registado para a porta 5671 da zona atualmente aliasada com o CNAME, como mostrado acima. 

A realização de uma falha requer a edição do registo CNAME e apontá-lo para a zona alternativa. 

A vantagem de usar DNS, e especificamente [Azure DNS,](../dns/dns-overview.md)é que a informação do Azure DNS é globalmente replicada e, portanto, resiliente contra falhas de uma região única.

Este procedimento é semelhante ao funcionamento do [Service Bus Geo-DR,](service-bus-geo-dr.md) mas totalmente sob o seu próprio controlo e também funciona com cenários ativos/ativos.

#### <a name="file-share-based-failover-configuration"></a>Configuração de falha baseada em ações de ficheiro

A alternativa mais simples de usar DNS para partilhar informações de ponto final é colocar o nome do ponto final primário num ficheiro de texto simples e servir o ficheiro a partir de uma infraestrutura que é robusta contra falhas e ainda permite atualizações. 

Se já executou uma infraestrutura de web site altamente disponível com disponibilidade global e replicação de conteúdos, adicionando tal ficheiro e republicar o ficheiro se for necessário um switch.

## <a name="merge"></a>Intercalar

O padrão de fusão tem uma ou mais tarefas de replicação que apontam para um alvo, possivelmente em simultâneo com os produtores regulares também enviando mensagens para o mesmo alvo. 

As variações deste padrão são:
- Duas ou mais funções de replicação simultaneamente adquirem mensagens de fontes separadas e enviam-nas para o mesmo alvo.
- Mais uma função de replicação que adquire mensagens de uma fonte enquanto o alvo também é usado diretamente pelos produtores. 
- O padrão anterior, mas mensagens espelhadas entre dois ou mais tópicos, resultando nesses tópicos que contêm as mesmas mensagens, independentemente do local onde as mensagens são produzidas.

As duas primeiras variações de padrões são triviais e não diferem das tarefas de replicação simples.

O último cenário requer que as mensagens já replicadas sejam replicadas novamente. A técnica é demonstrada e explicada na amostra ativa/ativa.

## <a name="editor"></a>Editor

O padrão do editor baseia-se no padrão [de replicação,](#replication) mas as mensagens são modificadas antes de serem reencaminhadas. Exemplos para tais modificações são:

- **_Transcoding_* _ - Se o conteúdo da mensagem (também designado por "corpo" ou "carga útil") chegar da fonte codificada utilizando o formato _Apache Avro* ou algum formato de serialização proprietário, mas a expectativa do sistema que possui o alvo é que o conteúdo seja codificado *JSON,* uma tarefa de replicação transcodificante irá primeiro deserializar a carga útil do *Apache Avro* para um gráfico de objeto na memória e, em seguida, serializar esse gráfico no formato *JSON* para a mensagem que está sendo reencaminhada. A transcoding também inclui tarefas de compressão e descompressão de **conteúdos.**
- **_Transformation_* _ - as mensagens que contenham dados estruturados podem exigir a reformulação desses dados para facilitar o consumo por parte dos consumidores a jusante. Isto pode envolver trabalho como achatamento de estruturas aninhadas, poda de elementos de dados extraérs, ou remodelar a carga útil para exatamente encaixar num determinado esquema.
- _*_Lotear_*_ - as mensagens podem ser recebidas em lotes (várias mensagens numa única transferência) de uma fonte, mas têm de ser reencaminhadas para um alvo, ou vice-versa. Uma tarefa pode, portanto, enviar várias mensagens com base numa única transferência de mensagens de entrada ou agregar um conjunto de mensagens que são depois transferidas em conjunto. 
- _*_Validação_*_ - os dados de mensagens provenientes de fontes externas precisam frequentemente de ser verificados para saber se estão em conformidade com um conjunto de regras antes de poderem ser reencaminhados. As regras podem ser expressas com recurso a esquemas ou códigos. as mensagens que não estejam em conformidade podem ser retiradas, com o problema anotado em registos, ou podem ser encaminhadas para um destino-alvo especial para os manusear mais.   
- _*_Enriquecimento_*_ - os dados de mensagens provenientes de algumas fontes podem exigir enriquecimento com mais contexto para que seja utilizável em sistemas-alvo. Isto pode implicar procurar dados de referência e incorporar esses dados com a mensagem, ou adicionar informações sobre a fonte que é conhecida da tarefa de replicação, mas não contida nas mensagens. 
- _*_Filtragem_*_ - Algumas mensagens que chegam de uma fonte podem ter de ser retidas do alvo com base em alguma regra. Um filtro testa a mensagem contra uma regra e deixa cair a mensagem se a mensagem não corresponder à regra. Filtrar mensagens duplicadas observando determinados critérios e deixando cair mensagens subsequentes com os mesmos valores é uma forma de filtragem.
- _*_Encaminhamento e Partição_*_ - Algumas tarefas de replicação podem permitir dois ou mais alvos alternativos e definir regras para as quais o alvo de replicação é escolhido para qualquer mensagem específica com base nos metadados ou conteúdo da mensagem. Uma forma especial de encaminhamento é a partição, onde a tarefa atribui explicitamente divisórias num alvo de replicação baseado em regras.
- _*_Criptografia_*_ - Uma tarefa de replicação pode ter de desencriptar os conteúdos que chegam da fonte e/ou encriptar o conteúdo reencaminhado para um alvo, e/ou pode ter de verificar a integridade do conteúdo e dos metadados em relação a uma assinatura transportada na mensagem, ou anexar tal assinatura. 
- _*_Attestation_*_ - Uma tarefa de replicação pode anexar metadados, potencialmente protegidos por uma assinatura digital, a uma mensagem que ateste que a mensagem foi recebida através de um canal específico ou num momento específico.     
- _ Corrente * - Uma tarefa *_de_* replicação pode aplicar assinaturas em sequências de mensagens de modo a que a integridade da sequência seja protegida e mensagens em falta possam ser detetadas.

Todos estes padrões podem ser implementados usando funções Azure, utilizando a [mensagem Hubs Trigger](../azure-functions/functions-bindings-service-bus-trigger.md) para adquirir mensagens e a [fila ou a ligação](../azure-functions/functions-bindings-service-bus-output.md) de saída de tópicos para a sua entrega. 

## <a name="routing"></a>Encaminhamento

O padrão de encaminhamento baseia-se no padrão de [replicação,](#replication) mas em vez de ter uma fonte e um alvo, a tarefa de replicação tem vários alvos, ilustrados aqui em C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

A função de encaminhamento considerará os metadados de mensagens e/ou a carga útil da mensagem e, em seguida, escolherá um dos destinos disponíveis para enviar.

## <a name="next-steps"></a>Passos seguintes

- [aplicações de replicador de mensagens em Funções Azure][1]
- [Replicação de mensagens entre o Service Bus][2]
- [Replicando mensagens para hubs de eventos Azure][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub