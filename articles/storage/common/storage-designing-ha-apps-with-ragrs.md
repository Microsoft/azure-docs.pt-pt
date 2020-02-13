---
title: Criar aplicativos altamente disponíveis usando o armazenamento com redundância geográfica
titleSuffix: Azure Storage
description: Saiba como usar o armazenamento com redundância geográfica com acesso de leitura para arquitetar um aplicativo altamente disponível que seja flexível o suficiente para lidar com interrupções.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 592be1710893791e80dfe4b20e1323e789b33e69
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157097"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Criando aplicativos altamente disponíveis usando o armazenamento com redundância geográfica com acesso de leitura

Um recurso comum de infraestruturas baseadas em nuvem como o armazenamento do Azure é que eles fornecem uma plataforma altamente disponível para hospedagem de aplicativos. Os desenvolvedores de aplicativos baseados em nuvem devem considerar cuidadosamente como aproveitar essa plataforma para fornecer aplicativos altamente disponíveis aos seus usuários. Este artigo se concentra em como os desenvolvedores podem usar uma das opções de replicação com redundância geográfica do Azure para garantir que seus aplicativos de armazenamento do Azure estejam altamente disponíveis.

As contas de armazenamento configuradas para replicação com redundância geográfica são replicadas de forma síncrona na região primária e, em seguida, replicadas assincronamente para uma região secundária que está a centenas de quilômetros de distância. O armazenamento do Azure oferece dois tipos de replicação com redundância geográfica:

* [O armazenamento geo-zona-redundante (GZRS) (pré-visualização)](storage-redundancy.md) fornece replicação para cenários que requerem alta disponibilidade e durabilidade máxima. Os dados são replicados de forma síncrona em três zonas de disponibilidade do Azure na região primária usando o ZRS (armazenamento com redundância de zona) e, em seguida, replicados assincronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geozona-redundante de acesso à leitura (RA-GZRS).
* [O armazenamento geo-redundante (GRS)](storage-redundancy.md) fornece replicação inter-regional para proteger contra interrupções regionais. Os dados são replicados de forma síncrona três vezes na região primária usando o LRS (armazenamento com redundância local) e, em seguida, replicados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS).

Este artigo mostra como projetar seu aplicativo para lidar com uma interrupção na região primária. Se a região primária ficar indisponível, seu aplicativo poderá se adaptar para executar operações de leitura em vez da região secundária. Verifique se sua conta de armazenamento está configurada para RA-GRS ou RA-GZRS antes de começar.

Para obter informações sobre quais as regiões primárias emparelhadas com as regiões secundárias, consulte a continuidade do negócio e a recuperação de [desastres (BCDR): Regiões Emparelhadas de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Há trechos de código incluídos neste artigo e um link para um exemplo completo no final que você pode baixar e executar.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Considerações de design de aplicativo ao ler do secundário

A finalidade deste artigo é mostrar como projetar um aplicativo que continuará a funcionar (embora em uma capacidade limitada), mesmo no caso de um grande desastre no data center primário. Você pode projetar seu aplicativo para lidar com problemas transitórios ou de longa execução lendo a partir da região secundária quando houver um problema que interfira na leitura da região primária. Quando a região primária estiver disponível novamente, seu aplicativo poderá retornar à leitura da região primária.

Tenha em mente estes pontos-chave ao projetar seu aplicativo para RA-GRS ou RA-GZRS:

* O armazenamento do Azure mantém uma cópia somente leitura dos dados que você armazena em sua região primária em uma região secundária. Conforme observado acima, o serviço de armazenamento determina o local da região secundária.

* A cópia apenas para leitura é [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para bolhas, mesas e filas, você pode consultar a região secundária para um valor do *Último Tempo sincronizado* que lhe diz quando ocorreu a última replicação do primário para a região secundária. (Não há suporte para isso em arquivos do Azure, que não tem redundância de RA-GRS no momento.)

* Você pode usar a biblioteca de cliente de armazenamento para ler e gravar dados na região primária ou secundária. Você também pode redirecionar solicitações de leitura automaticamente para a região secundária se uma solicitação de leitura para a região primária atingir o tempo limite.

* Se a região primária ficar indisponível, você poderá iniciar um failover de conta. Quando você faz failover para a região secundária, as entradas DNS que apontam para a região primária são alteradas para apontar para a região secundária. Após a conclusão do failover, o acesso de gravação é restaurado para contas GRS e RA-GRS. Para mais informações, consulte a falha da conta de recuperação e armazenamento de [desastres (pré-visualização) no Armazenamento Do Azure](storage-disaster-recovery-guidance.md).

> [!NOTE]
> O failover de conta gerenciada pelo cliente (versão prévia) ainda não está disponível em regiões com suporte para GZRS/RA-GZRS, para que os clientes não possam gerenciar eventos de failover de conta no momento com contas GZRS e RA-GZRS. Durante a pré-visualização, a Microsoft irá gerir quaisquer eventos de failover que afetem as contas GZRS/RA-GZRS.

### <a name="using-eventually-consistent-data"></a>Usando dados eventualmente consistentes

A solução proposta pressupõe que é aceitável retornar dados potencialmente obsoletos para o aplicativo de chamada. Como os dados na região secundária são eventualmente consistentes, é possível que a região primária possa se tornar inacessível antes que uma atualização para a região secundária tenha terminado a replicação.

Por exemplo, suponha que o cliente envie uma atualização com êxito, mas a região primária falhe antes que a atualização seja propagada para a região secundária. Quando o cliente pede para ler os dados de volta, eles recebem os dados obsoletos da região secundária em vez dos dados atualizados. Ao criar seu aplicativo, você deve decidir se isso é aceitável e, em caso afirmativo, como você enviará uma mensagem ao cliente. 

Posteriormente neste artigo, mostraremos como verificar a hora da última sincronização dos dados secundários para verificar se o secundário está atualizado.

### <a name="handling-services-separately-or-all-together"></a>Tratamento de serviços separadamente ou todos juntos

Embora seja improvável, é possível que um serviço fique indisponível enquanto os outros serviços ainda estão totalmente funcionais. Você pode manipular as repetições e o modo somente leitura para cada serviço separadamente (BLOBs, filas, tabelas) ou pode lidar com as novas tentativas genericamente para todos os serviços de armazenamento juntos.

Por exemplo, se você usar filas e blobs em seu aplicativo, poderá decidir colocar um código separado para manipular erros com nova tentativa para cada um deles. Em seguida, se você receber uma nova tentativa do serviço BLOB, mas o serviço fila ainda estiver funcionando, somente a parte do aplicativo que manipula os BLOBs será afetada. Se você decidir manipular todas as repetições de serviço de armazenamento genericamente e uma chamada para o serviço blob retornar um erro com nova tentativa, as solicitações para o serviço BLOB e o serviço fila serão afetadas.

Por fim, isso depende da complexidade do seu aplicativo. Você pode decidir não lidar com as falhas por serviço, mas em vez de redirecionar solicitações de leitura para todos os serviços de armazenamento para a região secundária e executar o aplicativo no modo somente leitura ao detectar um problema com qualquer serviço de armazenamento na região primária.

### <a name="other-considerations"></a>Outras considerações

Essas são as outras considerações que discutiremos no restante deste artigo.

* Tratamento de novas tentativas de solicitações de leitura usando o padrão de disjuntor

* Dados consistentes eventualmente e a hora da última sincronização

* Testar

## <a name="running-your-application-in-read-only-mode"></a>Executando seu aplicativo no modo somente leitura

Para se preparar efetivamente para uma interrupção na região primária, você deve ser capaz de lidar com solicitações de leitura com falha e solicitações de atualização com falha (com Update neste caso, significa inserções, atualizações e exclusões). Se a região primária falhar, as solicitações de leitura poderão ser redirecionadas para a região secundária. No entanto, as solicitações de atualização não podem ser redirecionadas para o secundário porque o secundário é somente leitura. Por esse motivo, você precisa projetar seu aplicativo para ser executado no modo somente leitura.

Por exemplo, você pode definir um sinalizador que é verificado antes que qualquer solicitação de atualização seja enviada ao armazenamento do Azure. Quando uma das solicitações de atualização chega, você pode ignorá-la e retornar uma resposta apropriada para o cliente. Talvez você queira até mesmo desabilitar determinados recursos até que o problema seja resolvido e notificar os usuários de que esses recursos estão temporariamente indisponíveis.

Se você decidir tratar erros para cada serviço separadamente, também precisará lidar com a capacidade de executar seu aplicativo no modo somente leitura por serviço. Por exemplo, você pode ter sinalizadores somente leitura para cada serviço que pode ser habilitado e desabilitado. Em seguida, você pode manipular o sinalizador nos locais apropriados em seu código.

Ser capaz de executar seu aplicativo no modo somente leitura tem outro benefício adicional – ele oferece a capacidade de garantir uma funcionalidade limitada durante uma atualização de aplicativo importante. Você pode disparar seu aplicativo para ser executado no modo somente leitura e apontar para o data center secundário, garantindo que ninguém esteja acessando os dados na região primária enquanto você estiver fazendo atualizações.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Tratamento de atualizações durante a execução no modo somente leitura

Há várias maneiras de lidar com solicitações de atualização durante a execução no modo somente leitura. Não abordaremos isso de forma abrangente, mas, em geral, há alguns padrões que você considera.

1. Você pode responder ao seu usuário e informar a eles que você não está aceitando atualizações no momento. Por exemplo, um sistema de gerenciamento de contatos pode permitir que os clientes acessem informações de contato, mas não façam atualizações.

2. Você pode enfileirar suas atualizações em outra região. Nesse caso, você escreveria suas solicitações de atualização pendentes para uma fila em uma região diferente e, em seguida, terá uma maneira de processar essas solicitações depois que o data center primário ficar online novamente. Nesse cenário, você deve permitir que o cliente saiba que a atualização solicitada está na fila para processamento posterior.

3. Você pode gravar suas atualizações em uma conta de armazenamento em outra região. Em seguida, quando o data center primário voltar a ficar online, você poderá ter uma maneira de mesclar essas atualizações nos dados primários, dependendo da estrutura dos dados. Por exemplo, se estiver criando arquivos separados com um carimbo de data/hora no nome, você poderá copiar esses arquivos de volta para a região primária. Isso funciona para algumas cargas de trabalho, como registro em log e dados de iOT.

## <a name="handling-retries"></a>Manipulando repetições

A biblioteca de cliente de armazenamento do Azure ajuda a determinar quais erros podem ser repetidos. Por exemplo, um erro 404 (recurso não encontrado) não seria repetido porque tentar novamente provavelmente não resultará em sucesso. Por outro lado, um erro 500 pode ser repetido porque é um erro de servidor e o problema pode ser simplesmente um problema transitório. Para mais detalhes, consulte o [código de código aberto para a classe ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca de clientes de armazenamento .NET. (Procure o método ShouldRetry.)

### <a name="read-requests"></a>Solicitações de leitura

As solicitações de leitura podem ser redirecionadas para o armazenamento secundário se houver um problema com o armazenamento primário. Como referido acima na utilização de [dados eventualmente consistentes,](#using-eventually-consistent-data)deve ser aceitável que a sua aplicação possa ler dados sobre os quais stão. Se estiver a utilizar a biblioteca do cliente de armazenamento para aceder a dados do secundário, pode especificar o comportamento de retry de um pedido de leitura, definindo um valor para a propriedade **LocationMode** para um dos seguintes:

* **PrimaryOnly** (o padrão)

* **PrimáriaThenSecondary**

* **Secundário**

* **SegundaPrimária**

Quando define o Modo de **Localização** para **o PrimaryThenSecondary,** se o pedido de leitura inicial para o ponto final primário falhar com um erro que pode ser novamente experimentado, o cliente faz automaticamente outro pedido de leitura para o ponto final secundário. Se o erro for um tempo limite do servidor, o cliente precisará aguardar até que o tempo limite expire antes de receber um erro com nova tentativa do serviço.

Há basicamente dois cenários a serem considerados quando você está decidindo como responder a um erro com nova tentativa:

* Esse é um problema isolado e as solicitações subsequentes para o ponto de extremidade primário não retornarão um erro com nova tentativa. Um exemplo de onde isso pode acontecer é quando há um erro de rede transitório.

    Neste cenário, não existe uma penalização significativa de desempenho em ter **o Modo de Localização** definido para o **PrimaryThenSecondary,** uma vez que isso só acontece com pouca frequência.

* Esse é um problema com pelo menos um dos serviços de armazenamento na região primária e todas as solicitações subsequentes para esse serviço na região primária provavelmente retornarão erros com nova tentativa por um período de tempo. Um exemplo disso é se a região primária está totalmente inacessível.

    Nesse cenário, há uma penalidade de desempenho, pois todas as suas solicitações de leitura tentarão primeiro o ponto de extremidade primário, aguardarão o tempo limite expirar e alternarão para o ponto de extremidade secundário.

Para estes cenários, deve identificar que existe um problema contínuo com o ponto final primário e enviar todos os pedidos de leitura diretamente para o ponto final secundário, definindo a propriedade **LocationMode** para **SecondaryOnly**. Neste momento, você também deve alterar o aplicativo para ser executado no modo somente leitura. Esta abordagem é conhecida como o [Padrão de Disjuntor.](/azure/architecture/patterns/circuit-breaker)

### <a name="update-requests"></a>Solicitações de atualização

O padrão de disjuntor também pode ser aplicado a solicitações de atualização. No entanto, as solicitações de atualização não podem ser redirecionadas para o armazenamento secundário, que é somente leitura. Para estes pedidos, deverá deixar a propriedade **LocationMode** definida para **PrimaryOnly** (o predefinido). Para lidar com esses erros, você pode aplicar uma métrica a essas solicitações – como 10 falhas em uma linha – e quando o limite for atingido, alterne o aplicativo para o modo somente leitura. Você pode usar os mesmos métodos para retornar ao modo de atualização como os descritos abaixo na próxima seção sobre o padrão de disjuntor.

## <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor Automático

Usar o padrão de disjuntor em seu aplicativo pode impedi-lo de repetir uma operação que provavelmente falhará repetidamente. Ele permite que o aplicativo continue a ser executado em vez de levar tempo enquanto a operação é repetida exponencialmente. Ele também detecta quando a falha foi corrigida e, nesse momento, o aplicativo pode tentar a operação novamente.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor

Para identificar se há um problema contínuo com um ponto de extremidade primário, você pode monitorar a frequência com que o cliente encontra erros com nova tentativa. Como cada caso é diferente, você precisa decidir o limite que deseja usar para a decisão de alternar para o ponto de extremidade secundário e executar o aplicativo no modo somente leitura. Por exemplo, você pode optar por executar a opção se houver 10 falhas em uma linha sem sucessos. Outro exemplo é mudar se 90% dos pedidos num período de 2 minutos falharem.

Para o primeiro cenário, você pode simplesmente manter uma contagem das falhas e, se houver um êxito, antes de atingir o máximo, defina a contagem de volta como zero. Para o segundo cenário, uma maneira de implementá-lo é usar o objeto MemoryCache (no .NET). Para cada solicitação, adicione um CacheItem ao cache, defina o valor como Success (1) ou Fail (0) e defina o tempo de expiração como 2 minutos a partir de Now (ou qualquer que seja a sua restrição de tempo). Quando o tempo de expiração de uma entrada é atingido, a entrada é automaticamente removida. Isso dará a você uma janela de 2 minutos sem interrupção. Cada vez que você faz uma solicitação para o serviço de armazenamento, primeiro usa uma consulta LINQ no objeto MemoryCache para calcular o percentual de sucesso somando os valores e dividindo pela contagem. Quando o sucesso por cento descer abaixo de algum limiar (como 10%), detete a propriedade **LocationMode** para pedidos de leitura para **SecondaryOnly** e mude a aplicação para modo de leitura apenas antes de continuar.

O limite de erros usados para determinar quando fazer a comutação pode variar de serviço para serviço em seu aplicativo, portanto, você deve considerar torná-los parâmetros configuráveis. Isso também é onde você decide tratar erros com nova tentativa de cada serviço separadamente ou como um, conforme discutido anteriormente.

Outra consideração é como lidar com várias instâncias de um aplicativo e o que fazer quando você detecta erros com nova tentativa em cada instância. Por exemplo, você pode ter 20 VMs em execução com o mesmo aplicativo carregado. Você manipula cada instância separadamente? Se uma instância começar a ter problemas, você deseja limitar a resposta a apenas uma instância ou deseja tentar fazer com que todas as instâncias respondam da mesma maneira quando uma instância tem um problema? Manipular as instâncias separadamente é muito mais simples do que tentar coordenar a resposta entre elas, mas como fazer isso depende da arquitetura do seu aplicativo.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções para monitorar a frequência de erro

Você tem três opções principais para monitorar a frequência de repetições na região primária a fim de determinar quando alternar para a região secundária e alterar o aplicativo para execução no modo somente leitura.

* Adicione um manipulador para o evento [**Retry**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) no objeto [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) que passa para os seus pedidos de armazenamento – este é o método apresentado neste artigo e utilizado na amostra que o acompanha. Esses eventos são acionados sempre que o cliente tenta uma solicitação novamente, permitindo que você acompanhe a frequência com que o cliente encontra erros com nova tentativa em um ponto de extremidade primário.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* No método [**de Avaliação**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) numa política de retry personalizada, pode executar código personalizado sempre que ocorre uma nova tentativa. Além de gravar quando ocorre uma nova tentativa, isso também lhe dá a oportunidade de modificar seu comportamento de repetição.

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

* A terceira abordagem é implementar um componente de monitoramento personalizado em seu aplicativo que execute ping continuamente no ponto de extremidade de armazenamento primário com solicitações de leitura fictícias (como a leitura de um blob pequeno) para determinar sua integridade. Isso ocuparia alguns recursos, mas não um valor significativo. Quando se descobrir um problema que atinge o seu limiar, então **executa** o interruptor para modo Secundário e apenas para leitura.

Em algum momento, convém voltar a usar o ponto de extremidade primário e permitir atualizações. Se estiver usando um dos dois primeiros métodos listados acima, você poderá simplesmente alternar de volta para o ponto de extremidade primário e habilitar o modo de atualização após a execução de um período de tempo ou o número de operações selecionado arbitrariamente. Em seguida, você pode deixar que ele passe pela lógica de repetição novamente. Se o problema tiver sido corrigido, ele continuará a usar o ponto de extremidade primário e permitirá atualizações. Se ainda houver um problema, ele será mais alternado para o ponto de extremidade secundário e o modo somente leitura após a falha dos critérios que você definiu.

Para o terceiro cenário, ao bater o ponto final de armazenamento primário torna-se novamente bem sucedido, pode acionar o interruptor de volta para **PrimaryOnly** e continuar a permitir atualizações.

## <a name="handling-eventually-consistent-data"></a>Manipulando dados eventualmente consistentes

O armazenamento com redundância geográfica funciona replicando as transações da região primária para a secundária. Este processo de replicação garante que os dados na região secundária são *eventualmente consistentes.* Isso significa que todas as transações na região primária serão eventualmente exibidas na região secundária, mas que pode haver um atraso antes que elas apareçam e que não há nenhuma garantia de que as transações cheguem na região secundária na mesma ordem em que elas foram originalmente aplicadas na região primária. Se as suas transações chegarem à região secundária fora de ordem, *poderá* considerar que os seus dados na região secundária estão num estado inconsistente até que o serviço recupere.

A tabela que se segue mostra um exemplo do que pode acontecer quando atualizar os detalhes de um empregado para torná-los membros do papel de *administradores.* Para o bem deste exemplo, isto requer que atualize a entidade **colaboradora** e atualize uma entidade **de função de administrador** com uma contagem do número total de administradores. Observe como as atualizações são aplicadas fora de ordem na região secundária.

| **Tempo** | **Transação**                                            | **Replicação**                       | **Última Sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação A: <br> Inserir funcionário <br> entidade no primário |                                   |                    | Transação A inserida no primário,<br> Ainda não replicado. |
| T1       |                                                            | Transação A <br> replicado para<br> secundário | T1 | Transação A replicada para secundária. <br>Hora da última sincronização atualizada.    |
| T2       | Transação B:<br>Atualizar<br> entidade de funcionário<br> no primário  |                                | T1                 | Transação B gravada no primário,<br> Ainda não replicado.  |
| T3       | Transação C:<br> Atualizar <br>administrador<br>entidade de função em<br>Primary |                    | T1                 | Transação C gravada no primário,<br> Ainda não replicado.  |
| *T4*     |                                                       | Transação C <br>replicado para<br> secundário | T1         | Transação C replicada para secundário.<br>LastSyncTime não atualizado porque <br>a transação B ainda não foi replicada.|
| *T5*     | Ler entidades <br>do secundário                           |                                  | T1                 | Você Obtém o valor obsoleto para o funcionário <br> entidade porque a transação B não <br> replicado ainda. Você Obtém o novo valor para<br> entidade de função de administrador porque C tem<br> replicados. A hora da última sincronização ainda não<br> atualizado porque a transação B<br> Não foi replicado. Você pode informar ao<br>a entidade da função de administrador está inconsistente <br>Porque a data/hora da entidade é posterior <br>a hora da última sincronização. |
| *T6*     |                                                      | Transação B<br> replicado para<br> secundário | T6                 | *T6* – Todas as transações através de C têm <br>replicado, hora da última sincronização<br> é atualizado. |

Neste exemplo, suponha que o cliente alterne para a leitura da região secundária em T5. Pode ler com sucesso **a** entidade gestora neste momento, mas a entidade contém um valor para a contagem de administradores que não é consistente com o número de entidades **colaboradoras** que são marcadas como administradores na região secundária neste momento. O cliente poderia simplesmente exibir esse valor, com o risco de que ele seja informações inconsistentes. Em alternativa, o cliente pode tentar determinar que a **função** do administrador está num estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem e, em seguida, informar o utilizador desse facto.

Para reconhecer que tem dados potencialmente inconsistentes, o cliente pode usar o valor do Último Tempo de *Sincronização* que pode obter a qualquer momento consultando um serviço de armazenamento. Isso informa a hora em que os dados na região secundária eram consistentes pela última vez e quando o serviço aplicou todas as transações antes desse ponto no tempo. No exemplo acima indicado, após a inserção do serviço a entidade **colaboradora** na região secundária, o último tempo de sincronização é fixado em *T1*. Permanece no *T1* até que o serviço atualize a entidade **colaboradora** da região secundária quando esta se fixar em *T6*. Se o cliente recuperar o último tempo de sincronização quando ler a entidade no *T5,* pode compará-la com a marca de tempo na entidade. Se o carimbo de data/hora na entidade for posterior à hora da última sincronização, a entidade estará em um estado potencialmente inconsistente e você poderá tomar a ação apropriada para seu aplicativo. O uso deste campo exige que você saiba quando a última atualização para a primária foi concluída.

Para saber como verificar o último tempo de sincronização, consulte [a propriedade Last Sync Time para obter uma conta](last-sync-time-get.md)de armazenamento .

## <a name="testing"></a>Testar

É importante testar se o aplicativo se comporta conforme o esperado quando encontra erros com nova tentativa. Por exemplo, você precisa testar se o aplicativo alterna para o secundário e para o modo somente leitura quando detecta um problema e alterna de volta quando a região primária fica disponível novamente. Para fazer isso, você precisa de uma maneira de simular erros com nova tentativa e controlar com que frequência eles ocorrem.

Pode utilizar [o Fiddler](https://www.telerik.com/fiddler) para intercetar e modificar as respostas httpnum script. Esse script pode identificar as respostas que vêm de seu ponto de extremidade primário e alterar o código de status HTTP para um que a biblioteca de cliente de armazenamento reconhece como um erro com nova tentativa. Este código de corte mostra um exemplo simples de um script fiddler que interceta respostas para ler pedidos contra a tabela de **dados dos funcionários** para devolver um estatuto de 502:

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

Se você tiver tornado os limites para alternar o aplicativo para o modo somente leitura configurável, será mais fácil testar o comportamento com volumes de transações de não produção.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre como ler a partir da região secundária, incluindo outro exemplo de como a propriedade do Último Tempo de Sincronização é definida, consulte opções de [redundância de armazenamento de armazenamento azure e leia o acesso ao armazenamento geo-redundante.](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)

* Para obter uma amostra completa que mostre como fazer o interruptor para trás e para a frente entre os pontos finais primários e secundários, consulte [amostras Azure – Utilizando o padrão de disjuntor com armazenamento RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
