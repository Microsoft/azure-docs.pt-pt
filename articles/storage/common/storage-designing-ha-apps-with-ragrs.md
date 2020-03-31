---
title: Design de aplicações altamente disponíveis usando armazenamento geo-redundante
titleSuffix: Azure Storage
description: Aprenda a usar o armazenamento geo-redundante de acesso de leitura para arquiteto uma aplicação altamente disponível que é suficientemente flexível para lidar com interrupções.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77157097"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Conceber aplicações altamente disponíveis utilizando armazenamento geo-redundante de acesso de leitura

Uma característica comum de infraestruturas baseadas na nuvem como o Azure Storage é que eles fornecem uma plataforma altamente disponível para hospedar aplicações. Os desenvolvedores de aplicações baseadas na nuvem devem considerar cuidadosamente como alavancar esta plataforma para fornecer aplicações altamente disponíveis aos seus utilizadores. Este artigo centra-se na forma como os desenvolvedores podem usar uma das opções de replicação georedundantes do Azure para garantir que as suas aplicações de Armazenamento Azure estão altamente disponíveis.

As contas de armazenamento configuradas para a replicação geo-redundante são sincronizadamente replicadas na região primária, e depois assincronicamente replicadas para uma região secundária que fica a centenas de milhas de distância. O Azure Storage oferece dois tipos de replicação georedundant:

* [O armazenamento geo-zona-redundante (GZRS) (pré-visualização)](storage-redundancy.md) fornece replicação para cenários que requerem alta disponibilidade e durabilidade máxima. Os dados são replicados sincronizadamente em três zonas de disponibilidade azure na região primária utilizando armazenamento redundante de zona (ZRS), e depois replicado assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geozona-redundante de acesso à leitura (RA-GZRS).
* [O armazenamento geo-redundante (GRS)](storage-redundancy.md) fornece replicação inter-regional para proteger contra interrupções regionais. Os dados são replicados sincronizadamente três vezes na região primária utilizando armazenamento localmente redundante (LRS), e depois replicados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geo-redundante de acesso de leitura (RA-GRS).

Este artigo mostra como projetar a sua aplicação para lidar com uma falha na região primária. Se a região primária ficar indisponível, a sua aplicação pode adaptar-se para efetuar operações de leitura contra a região secundária. Certifique-se de que a sua conta de armazenamento está configurada para RA-GRS ou RA-GZRS antes de começar.

Para obter informações sobre quais as regiões primárias emparelhadas com as regiões secundárias, consulte a continuidade do negócio e a recuperação de [desastres (BCDR): Regiões Emparelhadas de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Existem códigos incluídos neste artigo, e um link para uma amostra completa no final que você pode descarregar e executar.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Considerações de design de aplicação ao ler a partir do secundário

O objetivo deste artigo é mostrar-lhe como projetar uma aplicação que continuará a funcionar (ainda que de forma limitada) mesmo em caso de um grande desastre no centro de dados primário. Pode projetar a sua aplicação para lidar com problemas transitórios ou de longa duração lendo a partir da região secundária quando há um problema que interfere com a leitura da região primária. Quando a região primária estiver novamente disponível, a sua candidatura pode voltar à leitura da região primária.

Tenha em mente estes pontos-chave ao conceber a sua aplicação para RA-GRS ou RA-GZRS:

* O Azure Storage mantém uma cópia apenas de leitura dos dados que armazena na sua região primária numa região secundária. Como referido acima, o serviço de armazenamento determina a localização da região secundária.

* A cópia apenas para leitura é [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para bolhas, mesas e filas, você pode consultar a região secundária para um valor do *Último Tempo sincronizado* que lhe diz quando ocorreu a última replicação do primário para a região secundária. (Isto não é suportado para ficheiros Azure, que não tem redundância RA-GRS neste momento.)

* Pode utilizar a Biblioteca do Cliente de Armazenamento para ler e escrever dados na região primária ou secundária. Também pode redirecionar os pedidos de leitura para a região secundária se um pedido de leitura para a região primária se esgotar.

* Se a região primária ficar indisponível, pode iniciar uma falha de conta. Quando se falha na região secundária, as entradas de DNS que apontam para a região primária são alteradas para apontar para a região secundária. Após a falha estar completa, o acesso por escrito é restaurado para contas GRS e RA-GRS. Para mais informações, consulte a falha da conta de recuperação e armazenamento de [desastres (pré-visualização) no Armazenamento Do Azure](storage-disaster-recovery-guidance.md).

> [!NOTE]
> O failover da conta gerida pelo cliente (pré-visualização) ainda não está disponível em regiões que suportam gZRS/RA-GZRS, pelo que os clientes não podem atualmente gerir eventos de failover de conta com contas GZRS e RA-GZRS. Durante a pré-visualização, a Microsoft irá gerir quaisquer eventos de failover que afetem as contas GZRS/RA-GZRS.

### <a name="using-eventually-consistent-data"></a>Usando dados eventualmente consistentes

A solução proposta pressupõe que é aceitável devolver dados potencialmente fracos ao pedido de chamada. Uma vez que os dados na região secundária são eventualmente consistentes, é possível que a região primária possa tornar-se inacessível antes de uma atualização para a região secundária ter terminado a replicação.

Por exemplo, suponha que o seu cliente envie uma atualização com sucesso, mas a região primária falha antes de a atualização ser propagada para a região secundária. Quando o cliente pede para ler os dados de volta, eles recebem os dados antigos da região secundária em vez dos dados atualizados. Ao conceber a sua candidatura, deve decidir se isso é aceitável e, em caso afirmativo, como enviará uma mensagem ao cliente. 

Mais tarde neste artigo, mostramos como verificar o Último Tempo de Sincronização para os dados secundários para verificar se o secundário está atualizado.

### <a name="handling-services-separately-or-all-together"></a>Manuseamento de serviços separadamente ou todos juntos

Embora improvável, é possível que um serviço fique indisponível enquanto os outros serviços ainda estão totalmente funcionais. Pode manusear as tentativas e o modo de leitura apenas para cada serviço separadamente (bolhas, filas, mesas), ou pode lidar com repetições genericamente para todos os serviços de armazenamento em conjunto.

Por exemplo, se utilizar filas e bolhas na sua aplicação, poderá decidir colocar em código separado para lidar com erros retisíveis para cada um destes. Depois, se receber uma nova tentativa do serviço de blob, mas o serviço de fila ainda está a funcionar, apenas a parte da sua aplicação que trata de bolhas será impactada. Se decidir manusear todos os retenções de serviço de armazenamento genericamente e uma chamada para o serviço de blob devolver um erro retríptil, então os pedidos para o serviço de blob e o serviço de fila serão impactados.

Em última análise, isto depende da complexidade da sua aplicação. Pode decidir não lidar com as falhas por serviço, mas sim redirecionar os pedidos de leitura de todos os serviços de armazenamento para a região secundária e executar a aplicação em modo de leitura apenas quando detetar um problema com qualquer serviço de armazenamento na região primária.

### <a name="other-considerations"></a>Outras considerações

Estas são as outras considerações que iremos discutir no resto deste artigo.

* Manipulação de repetições de pedidos de leitura utilizando o padrão de disjuntor

* Dados eventualmente consistentes e o Último Tempo de Sincronização

* Testar

## <a name="running-your-application-in-read-only-mode"></a>Executar a sua aplicação em modo de leitura

Para se preparar eficazmente para uma paragem na região primária, deve ser capaz de lidar com os pedidos de leitura falhados e pedidos de atualização falhados (com a atualização neste caso, ou seja, inserções, atualizações e eliminações). Se a região primária falhar, os pedidos de leitura podem ser redirecionados para a região secundária. No entanto, os pedidos de atualização não podem ser redirecionados para o secundário porque o secundário é apenas de leitura. Por esta razão, precisa de desenhar a sua aplicação para funcionar em modo de leitura.

Por exemplo, pode definir uma bandeira que é verificada antes de qualquer pedido de atualização ser submetido ao Armazenamento Azure. Quando um dos pedidos de atualização chegar, pode ignorá-lo e devolver uma resposta adequada ao cliente. Pode até querer desativar completamente determinadas funcionalidades até que o problema seja resolvido e notificar os utilizadores de que essas funcionalidades estão temporariamente indisponíveis.

Se decidir lidar com erros para cada serviço separadamente, também terá de lidar com a capacidade de executar a sua aplicação no modo de leitura apenas por serviço. Por exemplo, pode ter bandeiras de leitura apenas para cada serviço que possam ser ativados e desativados. Depois pode manusear a bandeira nos locais apropriados do seu código.

Ser capaz de executar a sua aplicação em modo de leitura tem outro benefício secundário – dá-lhe a capacidade de garantir uma funcionalidade limitada durante uma grande atualização de aplicações. Pode acionar a sua aplicação para funcionar em modo de leitura e apontar para o centro de dados secundário, garantindo que ninguém acede aos dados na região primária enquanto está a fazer upgrades.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Manusear atualizações ao funcionar em modo de leitura

Existem muitas formas de lidar com os pedidos de atualização quando se executa no modo de leitura. Não vamos cobrir isto de forma abrangente, mas geralmente, há alguns padrões que considera.

1. Pode responder ao seu utilizador e dizer-lhes que não está a aceitar atualizações. Por exemplo, um sistema de gestão de contactos poderia permitir aos clientes aceder em informações de contacto, mas não fazer atualizações.

2. Pode fazer fila nas suas atualizações noutra região. Neste caso, escreveria os seus pedidos de atualização pendentes para uma fila numa região diferente e, em seguida, teria uma maneira de processar esses pedidos após o centro de dados primário voltar a estar online. Neste cenário, deve informar o cliente de que a atualização solicitada está na fila para posterior processamento.

3. Pode escrever as suas atualizações numa conta de armazenamento noutra região. Depois, quando o centro de dados primário voltar a funcionar, pode ter uma forma de fundir essas atualizações nos dados primários, dependendo da estrutura dos dados. Por exemplo, se estiver a criar ficheiros separados com um carimbo de data/hora no nome, pode copiar esses ficheiros de volta para a região primária. Isto funciona para algumas cargas de trabalho, tais como registo e dados de iOT.

## <a name="handling-retries"></a>Manipulação de tentativas

A biblioteca de clientes Azure Storage ajuda-o a determinar quais os erros que podem ser novamente julgados. Por exemplo, um erro 404 (recurso não encontrado) não seria novamente julgado porque tentar não é suscetível de resultar em sucesso. Por outro lado, um erro de 500 pode ser novamente julgado porque se trata de um erro do servidor, e o problema pode simplesmente ser um problema transitório. Para mais detalhes, consulte o [código de código aberto para a classe ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca de clientes de armazenamento .NET. (Procure o método ShouldRetry.)

### <a name="read-requests"></a>Ler pedidos

Os pedidos de leitura podem ser redirecionados para armazenamento secundário se houver algum problema com o armazenamento primário. Como referido acima na utilização de [dados eventualmente consistentes,](#using-eventually-consistent-data)deve ser aceitável que a sua aplicação possa ler dados sobre os quais stão. Se estiver a utilizar a biblioteca do cliente de armazenamento para aceder a dados do secundário, pode especificar o comportamento de retry de um pedido de leitura, definindo um valor para a propriedade **LocationMode** para um dos seguintes:

* **PrimaryOnly** (o padrão)

* **PrimáriaThenSecondary**

* **Secundário**

* **SegundaPrimária**

Quando define o Modo de **Localização** para **o PrimaryThenSecondary,** se o pedido de leitura inicial para o ponto final primário falhar com um erro que pode ser novamente experimentado, o cliente faz automaticamente outro pedido de leitura para o ponto final secundário. Se o erro for um tempo de tempo de servidor, o cliente terá de esperar que o tempo de validade expire antes de receber um erro retível do serviço.

Há basicamente dois cenários a considerar quando está a decidir como responder a um erro retível:

* Trata-se de um problema isolado e os pedidos subsequentes ao ponto final primário não retornarão a um erro retível. Um exemplo de onde isto pode acontecer é quando há um erro transitório de rede.

    Neste cenário, não existe uma penalização significativa de desempenho em ter **o Modo de Localização** definido para o **PrimaryThenSecondary,** uma vez que isso só acontece com pouca frequência.

* Trata-se de um problema com pelo menos um dos serviços de armazenamento da região primária e todos os pedidos subsequentes a esse serviço na região primária são suscetíveis de devolver erros retríptil por um período de tempo. Um exemplo disso é se a região primária é completamente inacessível.

    Neste cenário, há uma penalização de desempenho porque todos os seus pedidos de leitura vão tentar primeiro o ponto final primário, esperar que o tempo expire, em seguida, mudar para o ponto final secundário.

Para estes cenários, deve identificar que existe um problema contínuo com o ponto final primário e enviar todos os pedidos de leitura diretamente para o ponto final secundário, definindo a propriedade **LocationMode** para **SecondaryOnly**. Neste momento, deve também alterar a aplicação para ser executada em modo de leitura. Esta abordagem é conhecida como o [Padrão de Disjuntor.](/azure/architecture/patterns/circuit-breaker)

### <a name="update-requests"></a>Atualizar pedidos

O padrão de Disjuntor também pode ser aplicado aos pedidos de atualização. No entanto, os pedidos de atualização não podem ser redirecionados para o armazenamento secundário, que é apenas de leitura. Para estes pedidos, deverá deixar a propriedade **LocationMode** definida para **PrimaryOnly** (o predefinido). Para lidar com estes erros, pode aplicar uma métrica a estes pedidos – como 10 falhas seguidas – e quando o seu limiar for cumprido, mude a aplicação para modo de leitura. Pode utilizar os mesmos métodos para voltar ao modo de atualização como os descritos abaixo na secção seguinte sobre o padrão de Disjuntor.

## <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor Automático

A utilização do padrão de disjuntor na sua aplicação pode impedi-lo de voltar a experimentar uma operação que é suscetível de falhar repetidamente. Permite que a aplicação continue a funcionar em vez de demorar algum tempo enquanto a operação é retentada exponencialmente. Também deteta quando a falha foi corrigida, altura em que a aplicação pode voltar a tentar a operação.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor

Para identificar que existe um problema contínuo com um ponto final primário, pode monitorizar a frequência com que o cliente encontra erros retisíveis. Como cada caso é diferente, tem de decidir sobre o limiar que pretende utilizar para a decisão de mudar para o ponto final secundário e executar a aplicação no modo apenas de leitura. Por exemplo, pode decidir executar a troca se houver 10 falhas seguidas sem sucessos. Outro exemplo é mudar se 90% dos pedidos num período de 2 minutos falharem.

Para o primeiro cenário, pode simplesmente manter uma contagem das falhas, e se houver um sucesso antes de atingir o máximo, recue a contagem para zero. Para o segundo cenário, uma maneira de implementá-lo é usar o objeto MemoryCache (em .NET). Para cada pedido, adicione um CacheItem à cache, detete o valor para o sucesso (1) ou falha (0), e detetete o tempo de validade para 2 minutos a partir de agora (ou qualquer que seja o seu tempo de restrição de tempo). Quando o tempo de validade de uma entrada é atingido, a entrada é automaticamente removida. Isto vai dar-lhe uma janela rolante de 2 minutos. Cada vez que faz um pedido ao serviço de armazenamento, você primeiro usa uma consulta Linq através do objeto MemoryCache para calcular o sucesso por cento, resumindo os valores e dividindo-se pela contagem. Quando o sucesso por cento descer abaixo de algum limiar (como 10%), detete a propriedade **LocationMode** para pedidos de leitura para **SecondaryOnly** e mude a aplicação para modo de leitura apenas antes de continuar.

O limiar de erros utilizados para determinar quando fazer o interruptor pode variar de serviço para serviço na sua aplicação, pelo que deve considerar torná-los parâmetros configuráveis. É também aqui que decide lidar com erros retisíveis de cada serviço separadamente ou como um, como discutido anteriormente.

Outra consideração é como lidar com vários casos de uma aplicação, e o que fazer quando você detetar erros retisíveis em cada caso. Por exemplo, pode ter 20 VMs em execução com a mesma aplicação carregada. Lida com cada instância separadamente? Se um caso começar a ter problemas, quer limitar a resposta a apenas esse caso, ou se quer tentar que todos os casos respondam da mesma forma quando um caso tem um problema? Lidar com os casos separadamente é muito mais simples do que tentar coordenar a resposta através deles, mas como faz isso depende da arquitetura da sua aplicação.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções para monitorizar a frequência de erro

Tem três opções principais para monitorizar a frequência de repetições na região primária, a fim de determinar quando mudar para a região secundária e alterar a aplicação para funcionar em modo de leitura.

* Adicione um manipulador para o evento [**Retry**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) no objeto [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) que passa para os seus pedidos de armazenamento – este é o método apresentado neste artigo e utilizado na amostra que o acompanha. Estes eventos disparam sempre que o cliente tenta um pedido, permitindo-lhe rastrear com que frequência o cliente encontra erros retisíveis num ponto final primário.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* No método [**de Avaliação**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) numa política de retry personalizada, pode executar código personalizado sempre que ocorre uma nova tentativa. Além de gravar quando uma nova tentativa acontece, isto também lhe dá a oportunidade de modificar o seu comportamento de retry.

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

* A terceira abordagem é implementar um componente de monitorização personalizado na sua aplicação que continuamente pings o seu ponto final de armazenamento primário com pedidos de leitura manequim (como ler uma pequena bolha) para determinar a sua saúde. Isto iria ressumir alguns recursos, mas não uma quantidade significativa. Quando se descobrir um problema que atinge o seu limiar, então **executa** o interruptor para modo Secundário e apenas para leitura.

A dada altura, deverá voltar a utilizar o ponto final primário e permitir atualizações. Se utilizar um dos dois primeiros métodos acima listados, pode simplesmente voltar ao ponto final primário e ativar o modo de atualização depois de ter sido realizado um período de tempo ou número arbitrariamente selecionado. Pode então deixá-lo passar pela lógica de retry novamente. Se o problema tiver sido corrigido, continuará a utilizar o ponto final primário e permitirá atualizações. Se ainda houver um problema, voltará a mudar para o ponto final secundário e apenas para ler depois de falhar os critérios que definiu.

Para o terceiro cenário, ao bater o ponto final de armazenamento primário torna-se novamente bem sucedido, pode acionar o interruptor de volta para **PrimaryOnly** e continuar a permitir atualizações.

## <a name="handling-eventually-consistent-data"></a>Manuseamento de dados eventualmente consistentes

O armazenamento geo-redundante funciona replicando transações do primário para a região secundária. Este processo de replicação garante que os dados na região secundária são *eventualmente consistentes.* Isto significa que todas as transações na região primária acabarão por aparecer na região secundária, mas que pode haver um atraso antes de aparecerem, e que não há garantias de que as transações cheguem à região secundária na mesma ordem em que foram originalmente aplicados na região primária. Se as suas transações chegarem à região secundária fora de ordem, *poderá* considerar que os seus dados na região secundária estão num estado inconsistente até que o serviço recupere.

A tabela que se segue mostra um exemplo do que pode acontecer quando atualizar os detalhes de um empregado para torná-los membros do papel de *administradores.* Para o bem deste exemplo, isto requer que atualize a entidade **colaboradora** e atualize uma entidade **de função de administrador** com uma contagem do número total de administradores. Note como as atualizações são aplicadas fora de ordem na região secundária.

| **Tempo** | **Transação**                                            | **Replicação**                       | **Última Sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação A: <br> Inserir empregado <br> entidade em primário |                                   |                    | Transação A inserida nas primárias,<br> não replicado ainda. |
| T1       |                                                            | Transação A <br> replicado para<br> secundário | T1 | Transação A replicada para secundária. <br>Último Tempo de Sincronização atualizado.    |
| T2       | Transação B:<br>Atualizar<br> entidade funcionária<br> nas primárias  |                                | T1                 | Transação B escrita para primária,<br> não replicado ainda.  |
| T3       | Transação C:<br> Atualizar <br>administrador<br>entidade de papel em<br>primária |                    | T1                 | Transação C escrita para primária,<br> não replicado ainda.  |
| *T4*     |                                                       | Transação C <br>replicado para<br> secundário | T1         | Transação C replicada para secundária.<br>LastSyncTime não atualizado porque <br>transação B ainda não foi replicada.|
| *T5*     | Ler entidades <br>de secundário                           |                                  | T1                 | Obtém-se o valor velho para o empregado <br> entidade porque transação B não <br> replicado ainda. Obtém-se o novo valor para<br> entidade de papel administrador porque C tem<br> replicado. Última Sincronização ainda não<br> sido atualizado porque transação B<br> não se replicou. Pode dizer-se que<br>entidade de papel administrador é inconsistente <br>porque a data/hora da entidade é depois <br>o Último Tempo de Sincronização. |
| *T6*     |                                                      | Transação B<br> replicado para<br> secundário | T6                 | *T6* – Todas as transações através de C têm <br>sido replicado, Último Tempo sincronizado<br> é atualizado. |

Neste exemplo, assuma que o cliente muda para a leitura da região secundária em T5. Pode ler com sucesso **a** entidade gestora neste momento, mas a entidade contém um valor para a contagem de administradores que não é consistente com o número de entidades **colaboradoras** que são marcadas como administradores na região secundária neste momento. O seu cliente pode simplesmente mostrar este valor, com o risco de ser uma informação inconsistente. Em alternativa, o cliente pode tentar determinar que a **função** do administrador está num estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem e, em seguida, informar o utilizador desse facto.

Para reconhecer que tem dados potencialmente inconsistentes, o cliente pode usar o valor do Último Tempo de *Sincronização* que pode obter a qualquer momento consultando um serviço de armazenamento. Isto diz-lhe o momento em que os dados na região secundária foram consistentes pela última vez e quando o serviço tinha aplicado todas as transações antes desse momento. No exemplo acima indicado, após a inserção do serviço a entidade **colaboradora** na região secundária, o último tempo de sincronização é fixado em *T1*. Permanece no *T1* até que o serviço atualize a entidade **colaboradora** da região secundária quando esta se fixar em *T6*. Se o cliente recuperar o último tempo de sincronização quando ler a entidade no *T5,* pode compará-la com a marca de tempo na entidade. Se o carimbo de tempo na entidade for mais tarde do que o último tempo de sincronização, então a entidade está num estado potencialmente inconsistente, e pode tomar qualquer medida adequada para a sua aplicação. A utilização deste campo requer que saiba quando a última atualização para a primária foi concluída.

Para saber como verificar o último tempo de sincronização, consulte [a propriedade Last Sync Time para obter uma conta](last-sync-time-get.md)de armazenamento .

## <a name="testing"></a>Testar

É importante testar que a sua aplicação se comporta como esperado quando encontra erros retisíveis. Por exemplo, é necessário testar que a aplicação muda para o modo secundário e apenas para leitura quando deteta um problema, e volta a mudar quando a região primária volta a estar disponível. Para isso, é necessário uma forma de simular erros retisíveis e controlar a frequência com que ocorrem.

Pode utilizar [o Fiddler](https://www.telerik.com/fiddler) para intercetar e modificar as respostas httpnum script. Este script pode identificar respostas que vêm do seu ponto final primário e alterar o código de estado HTTP para um que a Biblioteca de Clientes de Armazenamento reconhece como um erro retríptil. Este código de corte mostra um exemplo simples de um script fiddler que interceta respostas para ler pedidos contra a tabela de **dados dos funcionários** para devolver um estatuto de 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Poderia estender este exemplo para intercetar um leque mais alargado de pedidos e apenas alterar o Código de **resposta** em alguns deles para simular melhor um cenário real. Para obter mais informações sobre a personalização dos scripts do Violinista, consulte [modificar um pedido ou resposta](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) na documentação do Violinista.

Se tiver tornado os limiares para mudar a sua aplicação para modo de leitura configurável, será mais fácil testar o comportamento com volumes de transações não produtivas.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre como ler a partir da região secundária, incluindo outro exemplo de como a propriedade do Último Tempo de Sincronização é definida, consulte opções de [redundância de armazenamento de armazenamento azure e leia o acesso ao armazenamento geo-redundante.](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)

* Para obter uma amostra completa que mostre como fazer o interruptor para trás e para a frente entre os pontos finais primários e secundários, consulte [amostras Azure – Utilizando o padrão de disjuntor com armazenamento RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
