---
title: Use geo-redundância para projetar aplicações altamente disponíveis
titleSuffix: Azure Storage
description: Aprenda a usar o armazenamento geo-redundante para conceber uma aplicação altamente disponível que seja suficientemente flexível para lidar com interrupções.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: a6aed0630acf6ee6624c72831a2cdc88e6c0a91d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013066"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>Use geo-redundância para projetar aplicações altamente disponíveis

Uma característica comum de infraestruturas baseadas em nuvem como o Azure Storage é que eles fornecem uma plataforma altamente disponível e durável para hospedar dados e aplicações. Os desenvolvedores de aplicações baseadas na nuvem devem considerar cuidadosamente como aproveitar esta plataforma para maximizar essas vantagens para os seus utilizadores. O Azure Storage oferece armazenamento geodu redundante para garantir uma elevada disponibilidade mesmo em caso de paragem regional. As contas de armazenamento configuradas para a replicação geo-redundante são sincronizadamente replicadas na região primária, e depois assíncronamente replicadas para uma região secundária que fica a centenas de milhas de distância.

O Azure Storage oferece duas opções para a replicação geo-redundante. A única diferença entre estas duas opções é como os dados são replicados na região primária:

* [Armazenamento de zonas-redundantes (GZRS)](storage-redundancy.md): Os dados são replicados sincronizadamente em três zonas de disponibilidade de Azure na região primária utilizando *armazenamento redundante de zona (ZRS)* e, em seguida, replicado assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento de zonas de acesso à leitura redundantes (RA-GZRS).

    A Microsoft recomenda a utilização de GZRS/RA-GZRS para cenários que exijam a máxima disponibilidade e durabilidade.

* [Armazenamento geo-redundante (GRS)](storage-redundancy.md): Os dados são replicados sincronizadamente três vezes na região primária utilizando *armazenamento localmente redundante (LRS)* e, em seguida, replicado assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geo-redundante de acesso à leitura (RA-GRS).

Este artigo mostra como desenhar a sua aplicação para lidar com uma paragem na região primária. Se a região primária ficar indisponível, a sua aplicação pode adaptar-se para realizar operações de leitura contra a região secundária. Certifique-se de que a sua conta de armazenamento está configurada para RA-GRS ou RA-GZRS antes de começar.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Considerações de design de aplicações ao ler do secundário

O objetivo deste artigo é mostrar-lhe como desenhar uma aplicação que continuará a funcionar (ainda que em capacidade limitada) mesmo em caso de grande desastre no centro de dados primário. Pode desenhar a sua aplicação para lidar com problemas transitórios ou de longa duração, lendo a partir da região secundária quando há um problema que interfere na leitura da região primária. Quando a região primária estiver novamente disponível, a sua aplicação pode voltar a ler a partir da região primária.

Tenha em mente estes pontos-chave ao conceber a sua aplicação para RA-GRS ou RA-GZRS:

* O Azure Storage mantém uma cópia apenas de leitura dos dados que armazena na sua região primária numa região secundária. Como referido acima, o serviço de armazenamento determina a localização da região secundária.

* A cópia só de leitura é [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para bolhas, mesas e filas, pode consultar a região secundária para obter um valor *de Última Hora de Sincronização* que lhe diga quando ocorreu a última replicação da primária para a região secundária. (Isto não é suportado para ficheiros Azure, que não tem redundância RA-GRS neste momento.)

* Pode utilizar a Biblioteca do Cliente de Armazenamento para ler e escrever dados na região primária ou secundária. Também pode redirecionar os pedidos de leitura automaticamente para a região secundária se um pedido de leitura para a região primária se esgotar.

* Se a região primária ficar indisponível, pode iniciar uma falha de conta. Quando se falha na região secundária, as entradas de DNS que apontam para a região primária são alteradas para apontar para a região secundária. Após a conclusão do failover, o acesso à escrita é restaurado para as contas GRS e RA-GRS. Para obter mais informações, consulte [a recuperação de desastres e a falha da conta de armazenamento.](storage-disaster-recovery-guidance.md)

### <a name="using-eventually-consistent-data"></a>Usando dados eventualmente consistentes

A solução proposta pressupõe que é aceitável devolver dados potencialmente velhos ao pedido de chamada. Como os dados na região secundária são eventualmente consistentes, é possível que a região primária possa tornar-se inacessível antes de uma atualização para a região secundária ter terminado a replicação.

Por exemplo, suponha que o seu cliente submeta uma atualização com sucesso, mas a região primária falha antes que a atualização seja propagada para a região secundária. Quando o cliente pede para ler os dados de volta, eles recebem os dados obsoletos da região secundária em vez dos dados atualizados. Ao conceber a sua aplicação, deve decidir se isso é aceitável e, em caso afirmativo, como irá enviar uma mensagem ao cliente. 

Mais tarde neste artigo, mostramos como verificar a Última Hora de Sincronização para os dados secundários para verificar se o secundário está atualizado.

### <a name="handling-services-separately-or-all-together"></a>Serviços de manuseamento separados ou todos juntos

Embora improvável, é possível que um serviço fique indisponível enquanto os outros serviços ainda estão totalmente funcionais. Pode manusear as retrações e o modo apenas de leitura para cada serviço separadamente (bolhas, filas, mesas), ou pode lidar com as recaídas genericamente para todos os serviços de armazenamento em conjunto.

Por exemplo, se utilizar filas e bolhas na sua aplicação, poderá decidir colocar um código separado para lidar com erros retripáveis para cada um deles. Depois, se receber uma nova volta do serviço blob, mas o serviço de fila ainda está a funcionar, apenas a parte da sua aplicação que lida com bolhas será impactada. Se decidir lidar com todas as retrações do serviço de armazenamento genericamente e uma chamada para o serviço blob retorna um erro retripvel, então os pedidos para o serviço blob e o serviço de fila serão impactados.

Em última análise, isso depende da complexidade da sua aplicação. Pode decidir não lidar com as falhas por serviço, mas em vez disso, redirecionar os pedidos de leitura de todos os serviços de armazenamento para a região secundária e executar a aplicação apenas em modo de leitura quando detetar um problema com qualquer serviço de armazenamento na região primária.

### <a name="other-considerations"></a>Outras considerações

Estas são as outras considerações que iremos discutir no resto deste artigo.

* Manipulação de retréis de pedidos de leitura usando o padrão de disjuntor de circuito

* Dados eventualmente consistentes e o último tempo de sincronização

* Testar

## <a name="running-your-application-in-read-only-mode"></a>Executar a sua aplicação no modo apenas de leitura

Para se preparar eficazmente para uma paragem na região primária, deve ser capaz de lidar com os pedidos de leitura falhados e os pedidos de atualização falhados (com atualizações neste caso que significam inserções, atualizações e supressões). Se a região primária falhar, os pedidos de leitura podem ser redirecionados para a região secundária. No entanto, os pedidos de atualização não podem ser redirecionados para o secundário porque o secundário é apenas lido. Por esta razão, precisa de desenhar a sua aplicação para funcionar apenas em modo de leitura.

Por exemplo, pode definir uma bandeira que é verificada antes de quaisquer pedidos de atualização serem submetidos ao Azure Storage. Quando um dos pedidos de atualização for apresentado, pode ignorá-lo e devolver uma resposta adequada ao cliente. Pode até querer desativar todas as funcionalidades até que o problema seja resolvido e notificar os utilizadores de que essas funcionalidades estão temporariamente indisponíveis.

Se decidir lidar separadamente com os erros de cada serviço, também terá de lidar com a capacidade de executar a sua aplicação apenas em modo de leitura por serviço. Por exemplo, pode ter bandeiras de leitura apenas para cada serviço que possa ser ativado e desativado. Em seguida, pode manusear a bandeira nos locais apropriados do seu código.

Ser capaz de executar a sua aplicação apenas em modo de leitura tem outro benefício secundário – dá-lhe a capacidade de garantir uma funcionalidade limitada durante uma grande atualização de aplicação. Pode ativar a sua aplicação para funcionar apenas no modo de leitura e apontar para o centro de dados secundário, garantindo que ninguém está a aceder aos dados na região primária enquanto faz upgrades.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Manipulação de atualizações ao executar no modo apenas de leitura

Existem muitas formas de lidar com pedidos de atualização quando são executando apenas o modo de leitura. Não vamos cobrir isto de forma abrangente, mas geralmente, há alguns padrões que considera.

1. Pode responder ao seu utilizador e dizer-lhe que não está a aceitar atualizações. Por exemplo, um sistema de gestão de contactos poderia permitir que os clientes acedessem a informações de contacto, mas não estois.

2. Pode encostar as suas atualizações noutra região. Neste caso, escreveria os seus pedidos de atualização pendentes para uma fila numa região diferente, e depois teria uma maneira de processar esses pedidos após o centro de dados primário voltar a estar online. Neste cenário, deverá informar o cliente de que a atualização solicitada está na fila para posterior processamento.

3. Pode escrever as suas atualizações para uma conta de armazenamento noutra região. Depois, quando o centro de dados primário voltar a funcionar, pode-se ter uma forma de fundir essas atualizações nos dados primários, dependendo da estrutura dos dados. Por exemplo, se estiver a criar ficheiros separados com um carimbo de data/hora no nome, pode copiar esses ficheiros de volta para a região primária. Isto funciona para algumas cargas de trabalho, tais como dados de registo e iOT.

## <a name="handling-retries"></a>Manipulação de retrós em que lidar

A biblioteca do cliente Azure Storage ajuda-o a determinar quais os erros que podem ser novamente julgados. Por exemplo, um erro de 404 (recurso não encontrado) não seria novamente julgado porque a sua nova tentativa não é suscetível de resultar em sucesso. Por outro lado, um erro de 500 pode ser novamente julgado porque se trata de um erro do servidor, e o problema pode ser simplesmente um problema transitório. Para mais detalhes, consulte o [código de código aberto para a classe ExponenciaialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca do cliente de armazenamento .NET. (Procure o método ShouldRetry.)

### <a name="read-requests"></a>Ler pedidos

Os pedidos de leitura podem ser redirecionados para armazenamento secundário se houver um problema com o armazenamento primário. Como referido acima na [utilização de dados eventualmente consistentes,](#using-eventually-consistent-data)deve ser aceitável que a sua aplicação leia potencialmente dados velhos. Se estiver a utilizar a biblioteca do cliente de armazenamento para aceder aos dados do secundário, pode especificar o comportamento de uma reluto de um pedido de leitura, definindo um valor para a propriedade **LocationMode** para um dos seguintes:

* **PrimaryOnly** (o padrão)

* **Ensino Primário**

* **SecundárioOnly**

* **SecundárioThenPrimary**

Quando define o **LocationMode** para **PrimaryThenSecondary,** se o pedido de leitura inicial para o ponto final primário falhar com um erro que pode ser novamente julgado, o cliente faz automaticamente outro pedido de leitura para o ponto final secundário. Se o erro for um tempo limite do servidor, então o cliente terá de esperar que o tempo limite expire antes de receber um erro retripvel do serviço.

Existem basicamente dois cenários a ter em conta quando se está a decidir como responder a um erro retripvel:

* Trata-se de um problema isolado e os pedidos subsequentes ao ponto final primário não retornarão a um erro retripável. Um exemplo de onde isto pode acontecer é quando há um erro transitório de rede.

    Neste cenário, não há nenhuma penalização significativa no desempenho em ter **LocationMode** definido para **PrimaryThenSecondary,** uma vez que isso só acontece raramente.

* Trata-se de um problema com pelo menos um dos serviços de armazenamento na região primária e todos os pedidos subsequentes a esse serviço na região primária são suscetíveis de devolver erros retripáveis por um período de tempo. Exemplo disso é se a região primária é completamente inacessível.

    Neste cenário, há uma penalização de desempenho porque todos os seus pedidos de leitura vão tentar primeiro o ponto final primário, esperar que o tempo limite expire e, em seguida, mudar para o ponto final secundário.

Para estes cenários, deverá identificar que existe um problema em curso com o principal ponto final e enviar todos os pedidos de leitura diretamente para o ponto final secundário, definindo a propriedade **LocationMode** para **a SecondaryOnly.** Neste momento, também deverá alterar a aplicação para ser executada apenas no modo de leitura. Esta abordagem é conhecida como o [Padrão do Disjuntor.](/azure/architecture/patterns/circuit-breaker)

### <a name="update-requests"></a>Atualizar pedidos

O padrão de disjuntor também pode ser aplicado para atualizar pedidos. No entanto, os pedidos de atualização não podem ser redirecionados para o armazenamento secundário, que é apenas lido. Para estes pedidos, deverá deixar a propriedade **LocationMode** definida para **PrimaryOnly** (o padrão). Para lidar com estes erros, pode aplicar uma métrica a estes pedidos – como 10 falhas seguidas – e quando o seu limiar for cumprido, mude a aplicação para o modo apenas de leitura. Pode utilizar os mesmos métodos para voltar ao modo de atualização que os descritos abaixo na secção seguinte sobre o padrão de Disjuntor.

## <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor Automático

A utilização do padrão de disjuntor na sua aplicação pode impedir que ele retordamente uma operação que é suscetível de falhar repetidamente. Permite que a aplicação continue a funcionar em vez de ocupar tempo enquanto a operação é revistada exponencialmente. Também deteta quando a falha foi corrigida, altura em que a aplicação pode tentar novamente a operação.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor

Para identificar que existe um problema contínuo com um ponto final primário, é possível monitorizar com que frequência o cliente encontra erros retripáveis. Como cada caso é diferente, tem de decidir sobre o limiar que pretende utilizar para a decisão de mudar para o ponto final secundário e executar a aplicação apenas no modo de leitura. Por exemplo, pode decidir executar o interruptor se houver 10 falhas seguidas sem sucesso. Outro exemplo é mudar se 90% dos pedidos num período de 2 minutos falharem.

Para o primeiro cenário, pode simplesmente manter uma contagem das falhas, e se houver um sucesso antes de atingir o máximo, faça a contagem voltar a zero. Para o segundo cenário, uma forma de implementá-lo é utilizar o objeto MemoryCache (em .NET). Para cada pedido, adicione um CacheItem à cache, deite o valor ao sucesso (1) ou falha (0), e desfaça o tempo de validade para 2 minutos a partir de agora (ou qualquer que seja a sua restrição de tempo). Quando o tempo de validade de uma entrada é atingido, a entrada é automaticamente removida. Isto vai dar-lhe uma janela de 2 minutos. Cada vez que faz um pedido ao serviço de armazenamento, primeiro usa uma consulta Linq através do objeto MemoryCache para calcular o sucesso por cento, resumindo os valores e dividindo-se pela contagem. Quando o sucesso por cento cair abaixo de algum limiar (como 10%), desajuste a propriedade **LocationMode** para pedidos de leitura para **SecondaryOnly** e mude a aplicação para o modo apenas de leitura antes de continuar.

O limiar de erros utilizados para determinar quando fazer o comutador pode variar de serviço para serviço na sua aplicação, pelo que deve considerar torná-los parâmetros configuráveis. É também aqui que decide lidar com erros retripáveis de cada serviço separadamente ou como um, como discutido anteriormente.

Outra consideração é como lidar com vários casos de uma aplicação, e o que fazer quando detetar erros retáveis em cada instância. Por exemplo, pode ter 20 VMs em execução com a mesma aplicação carregada. Lida com cada caso separadamente? Se uma das instâncias começar a ter problemas, quer limitar a resposta a apenas um caso, ou quer tentar que todas as instâncias respondam da mesma forma quando um caso tem um problema? Lidar com os casos separadamente é muito mais simples do que tentar coordenar a resposta através deles, mas como o fazes depende da arquitetura da tua aplicação.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções para monitorizar a frequência de erro

Tem três opções principais para monitorizar a frequência de retração na região primária, a fim de determinar quando mudar para a região secundária e alterar a aplicação para funcionar apenas em modo de leitura.

* Adicione um manipulador para o evento [**de Retripação**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) no objeto [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) que transmite aos seus pedidos de armazenamento – este é o método apresentado neste artigo e utilizado na amostra que o acompanha. Estes eventos disparam sempre que o cliente re-tenta um pedido, permitindo-lhe rastrear a frequência com que o cliente encontra erros retripáveis num ponto final primário.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* No método [**Avaliar**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) numa política de relícola personalizada, pode executar código personalizado sempre que ocorrer uma nova volta. Além de gravar quando uma nova tentou acontecer, isso também lhe dá a oportunidade de modificar o seu comportamento de relemgar.

    ```csharp
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* A terceira abordagem é implementar um componente de monitorização personalizado na sua aplicação que continuamente pinga o seu ponto final de armazenamento primário com pedidos de leitura falsa (como ler uma pequena bolha) para determinar a sua saúde. Isto ocuparia alguns recursos, mas não uma quantidade significativa. Quando for descoberto um problema que atinja o seu limiar, efetuaria o interruptor para **o modo SecondaryOnly** e apenas de leitura.

Em algum momento, irá querer voltar a utilizar o ponto final primário e permitir atualizações. Se utilizar um dos dois primeiros métodos acima indicados, pode simplesmente voltar ao ponto final primário e ativar o modo de atualização depois de ter sido realizada uma quantidade ou número de operações arbitrariamente selecionados. Pode então deixá-lo passar pela lógica de relemgar novamente. Se o problema tiver sido corrigido, continuará a utilizar o ponto final primário e permitirá atualizações. Se ainda houver um problema, voltará a mudar para o ponto final secundário e apenas para o modo de leitura depois de ter falhado os critérios que definiu.

Para o terceiro cenário, ao pingar o ponto final de armazenamento primário torna-se novamente bem sucedido, pode ativar o interruptor de volta para **PrimaryOnly** e continuar a permitir atualizações.

## <a name="handling-eventually-consistent-data"></a>Manipulação de dados eventualmente consistentes

Obras de armazenamento geo-redundantes replicando transações do primário para a região secundária. Este processo de replicação garante que os dados na região secundária *são eventualmente consistentes.* Isto significa que todas as transações na região primária acabarão por aparecer na região secundária, mas que pode haver um atraso antes de aparecerem, e que não há garantias de que as transações cheguem à região secundária da mesma ordem em que foram originalmente aplicadas na região primária. Se as suas transações chegarem à região secundária fora de encomenda, *poderá* considerar os seus dados na região secundária como um estado inconsistente até que o serviço se recupere.

A tabela que se segue mostra um exemplo do que pode acontecer quando atualiza os detalhes de um funcionário para torná-los membros da função de *administrador.* Por uma questão deste exemplo, isto requer que atualize a entidade **do colaborador** e atualize uma entidade **de função de administrador** com uma contagem do número total de administradores. Note como as atualizações são aplicadas fora de ordem na região secundária.

| **Time** | **Transação**                                            | **Replicação**                       | **Última hora de sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação A: <br> Inserir funcionário <br> entidade em primária |                                   |                    | Transação A inserida na primária,<br> ainda não replicado. |
| T1       |                                                            | Transação A <br> replicado para<br> secundário | T1 | Transação A replicada para secundária. <br>Última hora do sincronização atualizada.    |
| T2       | Transação B:<br>Atualizar<br> entidade dos trabalhadores<br> nas primárias  |                                | T1                 | Transação B escrita para a primária,<br> ainda não replicado.  |
| T3       | Transação C:<br> Atualizar <br>administrador<br>entidade de função em<br>primária |                    | T1                 | Transação C escrita para a primária,<br> ainda não replicado.  |
| *T4*     |                                                       | Transação C <br>replicado para<br> secundário | T1         | Transação C replicada para secundária.<br>LastSyncTime não atualizado porque <br>a transação B ainda não foi replicada.|
| *T5*     | Ler entidades <br>de secundário                           |                                  | T1                 | Obtém-se o valor velho para o empregado. <br> entidade, porque a transação B não tem <br> replicado ainda. Obtém-se o novo valor para<br> entidade de função administrador porque C tem<br> replicado. A última hora do sincronização ainda não<br> foi atualizado porque a transação B<br> não se replicou. Pode dizer-se que<br>entidade de função administrador é inconsistente <br>porque a data/hora da entidade é depois <br>a última hora do sincronização. |
| *T6*     |                                                      | Transação B<br> replicado para<br> secundário | T6                 | *T6* – Todas as transações através de C têm <br>foi replicado, Última Hora de Sincronização<br> é atualizado. |

Neste exemplo, assuma que o cliente passa a ler da região secundária em T5. Pode ler com sucesso a entidade **de função administradora** neste momento, mas a entidade contém um valor para a contagem de administradores que não é consistente com o número de entidades **de colaboradores** que são marcadas como administradores na região secundária neste momento. O seu cliente poderia simplesmente mostrar este valor, correndo o risco de ser uma informação inconsistente. Em alternativa, o cliente poderia tentar determinar que a **função de administrador** está num estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem, e depois informar o utilizador deste facto.

Para reconhecer que tem dados potencialmente inconsistentes, o cliente pode usar o valor da Última Hora de *Sincronização* que pode obter a qualquer momento consultando um serviço de armazenamento. Isto diz-lhe a época em que os dados na região secundária foram consistentes pela última vez e quando o serviço tinha aplicado todas as transações antes desse momento. No exemplo acima indicado, após o serviço inserir a entidade **do trabalhador** na região secundária, o último tempo de sincronização é definido para *T1*. Permanece em *T1* até que o serviço atualize a entidade **de colaboradores** da região secundária quando estiver definido para *T6*. Se o cliente recuperar a última sincronização quando ler a entidade em *T5,* pode compará-la com o relógio de tempo na entidade. Se a marca de tempo na entidade for mais tarde do que a última hora de sincronização, então a entidade está num estado potencialmente inconsistente, e pode tomar qualquer que seja a ação apropriada para a sua aplicação. A utilização deste campo requer que saiba quando a última atualização para a primária foi concluída.

Para saber como verificar a última hora de sincronização, consulte [a propriedade Da Última Hora de Sincronização para obter uma conta de armazenamento.](last-sync-time-get.md)

## <a name="testing"></a>Testar

É importante testar que a sua aplicação se comporta como esperado quando encontra erros retripáveis. Por exemplo, é necessário testar que a aplicação muda para o modo secundário e apenas de leitura quando deteta um problema e volta a ligar quando a região primária volta a estar disponível. Para isso, precisa de uma forma de simular erros retripáveis e controlar a frequência com que ocorrem.

Pode utilizar [o Violinista](https://www.telerik.com/fiddler) para intercetar e modificar respostas HTTP num script. Este script pode identificar respostas que vêm do seu ponto final primário e alterar o código de estado HTTP para um que a Biblioteca do Cliente de Armazenamento reconhece como um erro retripável. Este código mostra um exemplo simples de um script do Fiddler que interceta respostas para ler pedidos contra a tabela **de dados dos empregados** para devolver um estado 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Pode estender este exemplo para intercetar um leque mais alargado de pedidos e apenas alterar a **respostaCode** em alguns deles para simular melhor um cenário real. Para obter mais informações sobre a personalização dos scripts do Fiddler, consulte [modificar um Pedido ou Resposta](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) na documentação do Fiddler.

Se tiver tornado os limiares para mudar a sua aplicação para modo de leitura configurável, será mais fácil testar o comportamento com volumes de transações não produtivos.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma amostra completa que mostre como fazer o interruptor para trás e para a frente entre os pontos finais primários e secundários, consulte [amostras de Azure – Utilizando o Padrão do Disjuntor com armazenamento RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
