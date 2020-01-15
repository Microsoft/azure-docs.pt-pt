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
ms.openlocfilehash: bab95f6494fad86c9fdfc0b8fb044c22a7c5a628
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945453"
---
# <a name="designing-highly-available-applications-using-read-access-geo-redundant-storage"></a>Criando aplicativos altamente disponíveis usando o armazenamento com redundância geográfica com acesso de leitura

Um recurso comum de infraestruturas baseadas em nuvem como o armazenamento do Azure é que eles fornecem uma plataforma altamente disponível para hospedagem de aplicativos. Os desenvolvedores de aplicativos baseados em nuvem devem considerar cuidadosamente como aproveitar essa plataforma para fornecer aplicativos altamente disponíveis aos seus usuários. Este artigo se concentra em como os desenvolvedores podem usar uma das opções de replicação com redundância geográfica do Azure para garantir que seus aplicativos de armazenamento do Azure estejam altamente disponíveis.

As contas de armazenamento configuradas para replicação com redundância geográfica são replicadas de forma síncrona na região primária e, em seguida, replicadas assincronamente para uma região secundária que está a centenas de quilômetros de distância. O armazenamento do Azure oferece dois tipos de replicação com redundância geográfica:

* O [armazenamento com redundância de zona geográfica (GZRS) (visualização)](storage-redundancy-gzrs.md) fornece replicação para cenários que exigem alta disponibilidade e máxima durabilidade. Os dados são replicados de forma síncrona em três zonas de disponibilidade do Azure na região primária usando o ZRS (armazenamento com redundância de zona) e, em seguida, replicados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS).
* O [grs (armazenamento com redundância geográfica)](storage-redundancy-grs.md) fornece replicação entre regiões para proteger contra interrupções regionais. Os dados são replicados de forma síncrona três vezes na região primária usando o LRS (armazenamento com redundância local) e, em seguida, replicados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS).

Este artigo mostra como projetar seu aplicativo para lidar com uma interrupção na região primária. Se a região primária ficar indisponível, seu aplicativo poderá se adaptar para executar operações de leitura em vez da região secundária. Verifique se sua conta de armazenamento está configurada para RA-GRS ou RA-GZRS antes de começar.

Para obter informações sobre quais regiões primárias são emparelhadas com quais regiões secundárias, consulte [continuidade de negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

Há trechos de código incluídos neste artigo e um link para um exemplo completo no final que você pode baixar e executar.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Considerações de design de aplicativo ao ler do secundário

A finalidade deste artigo é mostrar como projetar um aplicativo que continuará a funcionar (embora em uma capacidade limitada), mesmo no caso de um grande desastre no data center primário. Você pode projetar seu aplicativo para lidar com problemas transitórios ou de longa execução lendo a partir da região secundária quando houver um problema que interfira na leitura da região primária. Quando a região primária estiver disponível novamente, seu aplicativo poderá retornar à leitura da região primária.

Tenha em mente estes pontos-chave ao projetar seu aplicativo para RA-GRS ou RA-GZRS:

* O armazenamento do Azure mantém uma cópia somente leitura dos dados que você armazena em sua região primária em uma região secundária. Conforme observado acima, o serviço de armazenamento determina o local da região secundária.

* A cópia somente leitura é [eventualmente consistente](https://en.wikipedia.org/wiki/Eventual_consistency) com os dados na região primária.

* Para BLOBs, tabelas e filas, você pode consultar a região secundária para obter um valor de *hora da última sincronização* que informa quando a última replicação da região primária para a secundária ocorreu. (Não há suporte para isso em arquivos do Azure, que não tem redundância de RA-GRS no momento.)

* Você pode usar a biblioteca de cliente de armazenamento para ler e gravar dados na região primária ou secundária. Você também pode redirecionar solicitações de leitura automaticamente para a região secundária se uma solicitação de leitura para a região primária atingir o tempo limite.

* Se a região primária ficar indisponível, você poderá iniciar um failover de conta. Quando você faz failover para a região secundária, as entradas DNS que apontam para a região primária são alteradas para apontar para a região secundária. Após a conclusão do failover, o acesso de gravação é restaurado para contas GRS e RA-GRS. Para obter mais informações, consulte [recuperação de desastre e failover da conta de armazenamento (versão prévia) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

> [!NOTE]
> O failover de conta gerenciada pelo cliente (versão prévia) ainda não está disponível em regiões com suporte para GZRS/RA-GZRS, para que os clientes não possam gerenciar eventos de failover de conta no momento com contas GZRS e RA-GZRS. Durante a versão prévia, a Microsoft gerenciará qualquer evento de failover que afete contas GZRS/RA-GZRS.

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

* Testes

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

A biblioteca de cliente de armazenamento do Azure ajuda a determinar quais erros podem ser repetidos. Por exemplo, um erro 404 (recurso não encontrado) não seria repetido porque tentar novamente provavelmente não resultará em sucesso. Por outro lado, um erro 500 pode ser repetido porque é um erro de servidor e o problema pode ser simplesmente um problema transitório. Para obter mais detalhes, confira o [código-fonte aberto para a classe ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) na biblioteca de cliente de armazenamento do .net. (Procure o método ShouldRetry.)

### <a name="read-requests"></a>Solicitações de leitura

As solicitações de leitura podem ser redirecionadas para o armazenamento secundário se houver um problema com o armazenamento primário. Conforme observado acima no [uso de dados eventualmente consistentes](#using-eventually-consistent-data), deve ser aceitável que seu aplicativo leia dados obsoletos potencialmente. Se você estiver usando a biblioteca de cliente de armazenamento para acessar dados do secundário, poderá especificar o comportamento de repetição de uma solicitação de leitura definindo um valor para a propriedade **locationmode** como um dos seguintes:

* **PrimaryOnly** (o padrão)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

Quando você define o **locationmode** como **PrimaryThenSecondary**, se a solicitação de leitura inicial para o ponto de extremidade primário falhar com um erro que pode ser repetido, o cliente fará automaticamente outra solicitação de leitura para o ponto de extremidade secundário. Se o erro for um tempo limite do servidor, o cliente precisará aguardar até que o tempo limite expire antes de receber um erro com nova tentativa do serviço.

Há basicamente dois cenários a serem considerados quando você está decidindo como responder a um erro com nova tentativa:

* Esse é um problema isolado e as solicitações subsequentes para o ponto de extremidade primário não retornarão um erro com nova tentativa. Um exemplo de onde isso pode acontecer é quando há um erro de rede transitório.

    Nesse cenário, não há nenhuma penalidade de desempenho significativa em ter **locationmode** definido como **PrimaryThenSecondary** , pois isso ocorre apenas com pouca frequência.

* Esse é um problema com pelo menos um dos serviços de armazenamento na região primária e todas as solicitações subsequentes para esse serviço na região primária provavelmente retornarão erros com nova tentativa por um período de tempo. Um exemplo disso é se a região primária está totalmente inacessível.

    Nesse cenário, há uma penalidade de desempenho, pois todas as suas solicitações de leitura tentarão primeiro o ponto de extremidade primário, aguardarão o tempo limite expirar e alternarão para o ponto de extremidade secundário.

Para esses cenários, você deve identificar que há um problema em andamento com o ponto de extremidade primário e enviar todas as solicitações de leitura diretamente para o ponto de extremidade secundário, definindo a propriedade **locationmode** como **SecondaryOnly**. Neste momento, você também deve alterar o aplicativo para ser executado no modo somente leitura. Essa abordagem é conhecida como [padrão de disjuntor](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Solicitações de atualização

O padrão de disjuntor também pode ser aplicado a solicitações de atualização. No entanto, as solicitações de atualização não podem ser redirecionadas para o armazenamento secundário, que é somente leitura. Para essas solicitações, você deve deixar a propriedade **locationmode** definida como **PrimaryOnly** (o padrão). Para lidar com esses erros, você pode aplicar uma métrica a essas solicitações – como 10 falhas em uma linha – e quando o limite for atingido, alterne o aplicativo para o modo somente leitura. Você pode usar os mesmos métodos para retornar ao modo de atualização como os descritos abaixo na próxima seção sobre o padrão de disjuntor.

## <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor Automático

Usar o padrão de disjuntor em seu aplicativo pode impedi-lo de repetir uma operação que provavelmente falhará repetidamente. Ele permite que o aplicativo continue a ser executado em vez de levar tempo enquanto a operação é repetida exponencialmente. Ele também detecta quando a falha foi corrigida e, nesse momento, o aplicativo pode tentar a operação novamente.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Como implementar o padrão de disjuntor

Para identificar se há um problema contínuo com um ponto de extremidade primário, você pode monitorar a frequência com que o cliente encontra erros com nova tentativa. Como cada caso é diferente, você precisa decidir o limite que deseja usar para a decisão de alternar para o ponto de extremidade secundário e executar o aplicativo no modo somente leitura. Por exemplo, você pode optar por executar a opção se houver 10 falhas em uma linha sem sucessos. Outro exemplo é mudar se 90% das solicitações em um período de 2 minutos falharem.

Para o primeiro cenário, você pode simplesmente manter uma contagem das falhas e, se houver um êxito, antes de atingir o máximo, defina a contagem de volta como zero. Para o segundo cenário, uma maneira de implementá-lo é usar o objeto MemoryCache (no .NET). Para cada solicitação, adicione um CacheItem ao cache, defina o valor como Success (1) ou Fail (0) e defina o tempo de expiração como 2 minutos a partir de Now (ou qualquer que seja a sua restrição de tempo). Quando o tempo de expiração de uma entrada é atingido, a entrada é automaticamente removida. Isso dará a você uma janela de 2 minutos sem interrupção. Cada vez que você faz uma solicitação para o serviço de armazenamento, primeiro usa uma consulta LINQ no objeto MemoryCache para calcular o percentual de sucesso somando os valores e dividindo pela contagem. Quando o percentual de sucesso cai abaixo de um limite (como 10%), defina a propriedade **locationmode** para solicitações de leitura como **SecondaryOnly** e alterne o aplicativo para o modo somente leitura antes de continuar.

O limite de erros usados para determinar quando fazer a comutação pode variar de serviço para serviço em seu aplicativo, portanto, você deve considerar torná-los parâmetros configuráveis. Isso também é onde você decide tratar erros com nova tentativa de cada serviço separadamente ou como um, conforme discutido anteriormente.

Outra consideração é como lidar com várias instâncias de um aplicativo e o que fazer quando você detecta erros com nova tentativa em cada instância. Por exemplo, você pode ter 20 VMs em execução com o mesmo aplicativo carregado. Você manipula cada instância separadamente? Se uma instância começar a ter problemas, você deseja limitar a resposta a apenas uma instância ou deseja tentar fazer com que todas as instâncias respondam da mesma maneira quando uma instância tem um problema? Manipular as instâncias separadamente é muito mais simples do que tentar coordenar a resposta entre elas, mas como fazer isso depende da arquitetura do seu aplicativo.

### <a name="options-for-monitoring-the-error-frequency"></a>Opções para monitorar a frequência de erro

Você tem três opções principais para monitorar a frequência de repetições na região primária a fim de determinar quando alternar para a região secundária e alterar o aplicativo para execução no modo somente leitura.

* Adicione um manipulador para o evento de [**repetição**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) no objeto [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) que você passa para suas solicitações de armazenamento – esse é o método exibido neste artigo e usado no exemplo que o acompanha. Esses eventos são acionados sempre que o cliente tenta uma solicitação novamente, permitindo que você acompanhe a frequência com que o cliente encontra erros com nova tentativa em um ponto de extremidade primário.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* No método [**Evaluate**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) em uma política de repetição personalizada, você pode executar o código personalizado sempre que ocorrer uma nova tentativa. Além de gravar quando ocorre uma nova tentativa, isso também lhe dá a oportunidade de modificar seu comportamento de repetição.

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

* A terceira abordagem é implementar um componente de monitoramento personalizado em seu aplicativo que execute ping continuamente no ponto de extremidade de armazenamento primário com solicitações de leitura fictícias (como a leitura de um blob pequeno) para determinar sua integridade. Isso ocuparia alguns recursos, mas não um valor significativo. Quando for descoberto um problema que atinge seu limite, você executaria a mudança para **SecondaryOnly** e para o modo somente leitura.

Em algum momento, convém voltar a usar o ponto de extremidade primário e permitir atualizações. Se estiver usando um dos dois primeiros métodos listados acima, você poderá simplesmente alternar de volta para o ponto de extremidade primário e habilitar o modo de atualização após a execução de um período de tempo ou o número de operações selecionado arbitrariamente. Em seguida, você pode deixar que ele passe pela lógica de repetição novamente. Se o problema tiver sido corrigido, ele continuará a usar o ponto de extremidade primário e permitirá atualizações. Se ainda houver um problema, ele será mais alternado para o ponto de extremidade secundário e o modo somente leitura após a falha dos critérios que você definiu.

Para o terceiro cenário, ao executar o ping no ponto de extremidade de armazenamento primário se tornar bem-sucedido novamente, você poderá disparar o comutador de volta para **PrimaryOnly** e continuar a permitir atualizações.

## <a name="handling-eventually-consistent-data"></a>Manipulando dados eventualmente consistentes

O armazenamento com redundância geográfica funciona replicando as transações da região primária para a secundária. Esse processo de replicação garante que os dados na região secundária sejam *eventualmente consistentes*. Isso significa que todas as transações na região primária serão eventualmente exibidas na região secundária, mas que pode haver um atraso antes que elas apareçam e que não há nenhuma garantia de que as transações cheguem na região secundária na mesma ordem em que elas foram originalmente aplicadas na região primária. Se suas transações chegarem na região secundária fora de ordem, você *poderá* considerar que os dados na região secundária estarão em um estado inconsistente até que o serviço seja atualizado.

A tabela a seguir mostra um exemplo do que pode acontecer quando você atualiza os detalhes de um funcionário para torná-los membros da função *Administradores* . Para fins deste exemplo, isso exige que você atualize a entidade **Employee** e atualize uma entidade de **função de administrador** com uma contagem do número total de administradores. Observe como as atualizações são aplicadas fora de ordem na região secundária.

| **Hora** | **Aciona**                                            | **Replicação**                       | **Hora da última sincronização** | **Resultado** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transação A: <br> Inserir funcionário <br> entidade no primário |                                   |                    | Transação A inserida no primário,<br> Ainda não replicado. |
| T1       |                                                            | Transação A <br> replicado para<br> secundário | T1 | Transação A replicada para secundária. <br>Hora da última sincronização atualizada.    |
| T2       | Transação B:<br>Atualizar<br> entidade de funcionário<br> no primário  |                                | T1                 | Transação B gravada no primário,<br> Ainda não replicado.  |
| T3       | Transação C:<br> Atualizar <br>administrador<br>entidade de função em<br>Primary |                    | T1                 | Transação C gravada no primário,<br> Ainda não replicado.  |
| *T4*     |                                                       | Transação C <br>replicado para<br> secundário | T1         | Transação C replicada para secundário.<br>LastSyncTime não atualizado porque <br>a transação B ainda não foi replicada.|
| *T5*     | Ler entidades <br>do secundário                           |                                  | T1                 | Você Obtém o valor obsoleto para o funcionário <br> entidade porque a transação B não <br> replicado ainda. Você Obtém o novo valor para<br> entidade de função de administrador porque C tem<br> replicados. A hora da última sincronização ainda não<br> atualizado porque a transação B<br> Não foi replicado. Você pode informar ao<br>a entidade da função de administrador está inconsistente <br>Porque a data/hora da entidade é posterior <br>a hora da última sincronização. |
| *T6*     |                                                      | Transação B<br> replicado para<br> secundário | T6                 | *T6* – todas as transações por meio de C têm <br>replicado, hora da última sincronização<br> é atualizado. |

Neste exemplo, suponha que o cliente alterne para a leitura da região secundária em T5. Ele pode ler com êxito a entidade de **função de administrador** neste momento, mas a entidade contém um valor para a contagem de administradores que não são consistentes com o número de entidades de **funcionário** marcadas como administradores na região secundária neste momento. O cliente poderia simplesmente exibir esse valor, com o risco de que ele seja informações inconsistentes. Como alternativa, o cliente pode tentar determinar se a **função de administrador** está em um estado potencialmente inconsistente porque as atualizações ocorreram fora de ordem e, em seguida, informar o usuário desse fato.

Para reconhecer que ele tem dados potencialmente inconsistentes, o cliente pode usar o valor da *hora da última sincronização* que você pode obter a qualquer momento consultando um serviço de armazenamento. Isso informa a hora em que os dados na região secundária eram consistentes pela última vez e quando o serviço aplicou todas as transações antes desse ponto no tempo. No exemplo mostrado acima, depois que o serviço insere a entidade **Employee** na região secundária, a hora da última sincronização é definida como *T1*. Ele permanece em *T1* até que o serviço atualize a entidade **Employee** na região secundária quando ela estiver definida como *T6*. Se o cliente recuperar a hora da última sincronização quando ler a entidade em *T5*, ela poderá compará-la com o carimbo de data/hora na entidade. Se o carimbo de data/hora na entidade for posterior à hora da última sincronização, a entidade estará em um estado potencialmente inconsistente e você poderá tomar a ação apropriada para seu aplicativo. O uso deste campo exige que você saiba quando a última atualização para a primária foi concluída.

## <a name="getting-the-last-sync-time"></a>Obtendo a hora da última sincronização

Você pode usar o PowerShell ou CLI do Azure para recuperar a hora da última sincronização para determinar quando os dados foram gravados pela última vez no secundário.

### <a name="powershell"></a>PowerShell

Para obter a hora da última sincronização para a conta de armazenamento usando o PowerShell, instale um módulo de visualização do armazenamento do Azure que dá suporte à obtenção de estatísticas de replicação geográfica. Por exemplo:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Em seguida, verifique a propriedade **GeoReplicationStats. LastSyncTime** da conta de armazenamento. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

### <a name="azure-cli"></a>CLI do Azure

Para obter a hora da última sincronização para a conta de armazenamento usando CLI do Azure, verifique a propriedade **geoReplicationStats. lastSyncTime** da conta de armazenamento. Use o parâmetro `--expand` para retornar valores para as propriedades aninhadas em **geoReplicationStats**. Lembre-se de substituir os valores de espaço reservado pelos seus próprios valores:

```azurecli
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

## <a name="testing"></a>Testes

É importante testar se o aplicativo se comporta conforme o esperado quando encontra erros com nova tentativa. Por exemplo, você precisa testar se o aplicativo alterna para o secundário e para o modo somente leitura quando detecta um problema e alterna de volta quando a região primária fica disponível novamente. Para fazer isso, você precisa de uma maneira de simular erros com nova tentativa e controlar com que frequência eles ocorrem.

Você pode usar o [Fiddler](https://www.telerik.com/fiddler) para interceptar e modificar respostas HTTP em um script. Esse script pode identificar as respostas que vêm de seu ponto de extremidade primário e alterar o código de status HTTP para um que a biblioteca de cliente de armazenamento reconhece como um erro com nova tentativa. Este trecho de código mostra um exemplo simples de um script Fiddler que intercepta respostas para solicitações de leitura na tabela **employeeData** para retornar um status 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Você pode estender esse exemplo para interceptar um intervalo maior de solicitações e apenas alterar o **responseCode** em alguns deles para simular melhor um cenário do mundo real. Para obter mais informações sobre como personalizar scripts Fiddler, consulte [modificando uma solicitação ou resposta](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) na documentação do Fiddler.

Se você tiver tornado os limites para alternar o aplicativo para o modo somente leitura configurável, será mais fácil testar o comportamento com volumes de transações de não produção.

## <a name="next-steps"></a>Próximos Passos

* Para obter mais informações sobre como ler a partir da região secundária, incluindo outro exemplo de como a última propriedade de hora de sincronização está definida, consulte [Opções de redundância de armazenamento do Azure e armazenamento com redundância geográfica com acesso de leitura](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

* Para obter um exemplo completo mostrando como fazer a alternância entre os pontos de extremidade primário e secundário, consulte [exemplos do Azure – usando o padrão de disjuntor com o armazenamento ra-grs](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
