---
title: Adicionar relatórios personalizados de estado de funcionamento do Service Fabric
description: Descreve como enviar relatórios de integridade personalizados para entidades do Azure Service Fabric Health. Fornece recomendações para projetar e implementar relatórios de integridade de qualidade.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451592"
---
# <a name="add-custom-service-fabric-health-reports"></a>Adicionar relatórios personalizados de estado de funcionamento do Service Fabric
O Azure Service Fabric introduz um [modelo de integridade](service-fabric-health-introduction.md) projetado para sinalizar condições de cluster e aplicativo não íntegras em entidades específicas. O modelo de integridade usa **relatórios de integridade** (componentes do sistema e Watchdogs). O objetivo é o diagnóstico e o reparo rápidos e fáceis. Os gravadores de serviço precisam pensar antecipadamente sobre a integridade. Qualquer condição que possa afetar a integridade deve ser relatada, especialmente se puder ajudar a sinalizar problemas próximos à raiz. As informações de integridade podem poupar tempo e esforço na depuração e investigação. A utilidade é especialmente clara quando o serviço está em funcionamento em escala na nuvem (particular ou Azure).

Os relatórios de Service Fabric monitoram as condições de interesse identificadas. Eles relatam nessas condições com base em sua exibição local. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega os dados de integridade enviados por todos os relatórios para determinar se as entidades estão íntegras globalmente. O modelo deve ser avançado, flexível e fácil de usar. A qualidade dos relatórios de integridade determina a precisão da exibição de integridade do cluster. Falsos positivos que mostram incorretamente problemas não íntegros podem afetar negativamente as atualizações ou outros serviços que usam dados de integridade. Exemplos desses serviços são os serviços de reparo e os mecanismos de alerta. Portanto, algumas idéias são necessárias para fornecer relatórios que capturam condições de interesse da melhor maneira possível.

Para projetar e implementar relatórios de integridade, os Watchdogs e os componentes do sistema devem:

* Defina a condição em que eles estão interessados, a maneira como ele é monitorado e o impacto na funcionalidade do cluster ou do aplicativo. Com base nessas informações, decida sobre a propriedade do relatório de integridade e o estado de integridade.
* Determine a [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) à qual o relatório se aplica.
* Determine onde o relatório é feito, de dentro do serviço ou de um Watchdog interno ou externo.
* Defina uma fonte usada para identificar o reporter.
* Escolha uma estratégia de relatório, seja periodicamente ou em transições. A maneira recomendada é periodicamente, pois requer um código mais simples e é menos propenso a erros.
* Determine por quanto tempo o relatório para condições não íntegras deve permanecer no repositório de integridade e como ele deve ser limpo. Usando essas informações, decida a vida útil do relatório e o comportamento de remoção na expiração.

Conforme mencionado, os relatórios podem ser feitos em:

* A réplica do serviço Service Fabric monitorado.
* Watchdogs internos implantados como um serviço de Service Fabric (por exemplo, um serviço sem estado Service Fabric que monitora as condições e os relatórios de problemas). Os Watchdogs podem ser implantados em todos os nós ou podem ser relacionadosdos para o serviço monitorado.
* Watchdogs internos que são executados nos nós de Service Fabric, mas *não* são implementados como Service Fabric serviços.
* Watchdogs externos que investigam o recurso de *fora* do cluster de Service Fabric (por exemplo, serviço de monitoramento como Gomez).

> [!NOTE]
> O cluster é populado com relatórios de integridade enviados pelos componentes do sistema. Leia mais em [usando relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios do usuário devem ser enviados em [entidades de integridade](service-fabric-health-introduction.md#health-entities-and-hierarchy) que já foram criadas pelo sistema.
> 
> 

Quando o design do relatório de integridade estiver claro, os relatórios de integridade poderão ser enviados facilmente. Você pode usar o [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para relatar a integridade se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o cliente de malha tiver privilégios de administrador. Os relatórios podem ser feitos por meio da API usando [FabricClient. healthmanager. ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), por meio do PowerShell ou do REST. Botões de configuração relatórios em lote para melhorar o desempenho.

> [!NOTE]
> A integridade do relatório é síncrona e representa apenas o trabalho de validação no lado do cliente. O fato de que o relatório é aceito pelo cliente de integridade ou os objetos `Partition` ou `CodePackageActivationContext` não significa que ele é aplicado na loja. Ele é enviado de forma assíncrona e possivelmente em lote com outros relatórios. O processamento no servidor ainda pode falhar: o número de sequência pode estar obsoleto, a entidade na qual o relatório deve ser aplicado foi excluída, etc.
> 
> 

## <a name="health-client"></a>Cliente de integridade
Os relatórios de integridade são enviados para o Gerenciador de integridade por meio de um cliente de integridade, que reside dentro do cliente de malha. O Gerenciador de integridade salva os relatórios no repositório de integridade. O cliente de integridade pode ser configurado com as seguintes configurações:

* **HealthReportSendInterval**: o atraso entre a hora em que o relatório é adicionado ao cliente e a hora em que ele é enviado para o Gerenciador de integridade. Usado para gerar relatórios em lote em uma única mensagem, em vez de enviar uma mensagem para cada relatório. O envio em lote melhora o desempenho. Padrão: 30 segundos.
* **HealthReportRetrySendInterval**: o intervalo no qual o cliente de integridade reenvia relatórios de integridade acumulados para o Gerenciador de integridade. Padrão: 30 segundos, mínimo: 1 segundo.
* **HealthOperationTimeout**: o período de tempo limite para uma mensagem de relatório enviada ao Gerenciador de integridade. Se uma mensagem atingir o tempo limite, o cliente de integridade o tentará novamente até que o Gerenciador de integridade confirme que o relatório foi processado. Padrão: dois minutos.

> [!NOTE]
> Quando os relatórios são armazenados em lote, o cliente de malha deve ser mantido ativo por pelo menos o HealthReportSendInterval para garantir que eles sejam enviados. Se a mensagem for perdida ou o Gerenciador de integridade não puder aplicá-las devido a erros transitórios, o cliente de malha deverá ser mantido ativo por mais tempo para dar a oportunidade de tentar novamente.
> 
> 

O armazenamento em buffer no cliente leva a exclusividade dos relatórios em consideração. Por exemplo, se um determinado notificador inadequado estiver relatando relatórios 100 por segundo na mesma propriedade da mesma entidade, os relatórios serão substituídos pela última versão. No máximo um relatório existe na fila do cliente. Se o envio em lote estiver configurado, o número de relatórios enviados para o Gerenciador de integridade será apenas um por intervalo de envio. Este relatório é o último relatório adicionado, que reflete o estado mais atual da entidade.
Especifique parâmetros de configuração quando `FabricClient` for criado passando [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) com os valores desejados para entradas relacionadas à integridade.

O exemplo a seguir cria um cliente de malha e especifica que os relatórios devem ser enviados quando são adicionados. Em tempos limite e erros que podem ser repetidos, as repetições acontecem a cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

É recomendável manter as configurações do cliente de malha padrão, que definem `HealthReportSendInterval` como 30 segundos. Essa configuração garante o desempenho ideal devido ao envio em lote. Para relatórios críticos que devem ser enviados assim que possível, use `HealthReportSendOptions` com `true` imediato na API [FabricClient. HealthClient. ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) . Os relatórios imediatos ignoram o intervalo de envio em lote. Use este sinalizador com cuidado; Queremos aproveitar o envio em lote do cliente de integridade sempre que possível. O envio imediato também é útil quando o cliente de malha está sendo fechado (por exemplo, o processo determinou um estado inválido e precisa ser desligado para evitar efeitos colaterais). Ele garante um envio de melhor esforço dos relatórios acumulados. Quando um relatório é adicionado com o sinalizador imediato, o cliente de integridade faz o lote de todos os relatórios acumulados desde o último envio.

Os mesmos parâmetros podem ser especificados quando uma conexão com um cluster é criada por meio do PowerShell. O exemplo a seguir inicia uma conexão com um cluster local:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Da mesma forma que a API, os relatórios podem ser enviados usando `-Immediate` opção a ser enviada imediatamente, independentemente do valor de `HealthReportSendInterval`.

Para REST, os relatórios são enviados para o gateway de Service Fabric, que tem um cliente de malha interna. Por padrão, esse cliente é configurado para enviar relatórios em lote a cada 30 segundos. Você pode alterar o intervalo de lote com a configuração de cluster `HttpGatewayHealthReportSendInterval` em `HttpGateway`. Como mencionado, uma opção melhor é enviar os relatórios com `Immediate` verdadeiro. 

> [!NOTE]
> Para garantir que os serviços não autorizados não possam relatar a integridade em relação às entidades no cluster, configure o servidor para aceitar solicitações somente de clientes protegidos. O `FabricClient` usado para relatórios deve ter a segurança habilitada para ser capaz de se comunicar com o cluster (por exemplo, com autenticação Kerberos ou de certificado). Leia mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Relatório de dentro dos serviços de baixo privilégio
Se os serviços de Service Fabric não tiverem acesso de administrador ao cluster, você poderá relatar a integridade em entidades do contexto atual por meio de `Partition` ou `CodePackageActivationContext`.

* Para serviços sem estado, use [IStatelessServicePartition. ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) para relatar a instância de serviço atual.
* Para serviços com estado, use [IStatefulServicePartition. ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) para relatar a réplica atual.
* Use [IServicePartition. ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) para relatar a entidade de partição atual.
* Use [CodePackageActivationContext. ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) para relatar o aplicativo atual.
* Use [CodePackageActivationContext. ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) para relatar o aplicativo atual implantado no nó atual.
* Use [CodePackageActivationContext. ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) para relatar um pacote de serviço para o aplicativo implantado no nó atual.

> [!NOTE]
> Internamente, o `Partition` e o `CodePackageActivationContext` mantêm um cliente de integridade configurado com as configurações padrão. Conforme explicado para o [cliente de integridade](service-fabric-report-health.md#health-client), os relatórios são agrupados e enviados em um temporizador. Os objetos devem ser mantidos ativos para ter a oportunidade de enviar o relatório.
> 
> 

Você pode especificar `HealthReportSendOptions` ao enviar relatórios por meio de APIs de `Partition` e `CodePackageActivationContext` integridade. Se você tiver relatórios críticos que devem ser enviados assim que possível, use `HealthReportSendOptions` com `true`imediatos. Os relatórios imediatos ignoram o intervalo de envio em lote do cliente de integridade interno. Como mencionado anteriormente, use este sinalizador com cuidado; Queremos aproveitar o envio em lote do cliente de integridade sempre que possível.

## <a name="design-health-reporting"></a>Relatórios de integridade de design
A primeira etapa na geração de relatórios de alta qualidade é identificar as condições que podem afetar a integridade do serviço. Qualquer condição que possa ajudar a sinalizar problemas no serviço ou cluster quando ele inicia--ou ainda melhor, antes que um problema ocorra, pode potencialmente economizar bilhões de dólares. Os benefícios incluem menos tempo de inatividade, menos horas da noite gasto investigando e reparando problemas e maior satisfação do cliente.

Depois que as condições são identificadas, os gravadores de Watchdog precisam descobrir a melhor maneira de monitorá-las para o equilíbrio entre sobrecarga e utilidade. Por exemplo, considere um serviço que faz cálculos complexos que usam alguns arquivos temporários em um compartilhamento. Um Watchdog pode monitorar o compartilhamento para garantir que haja espaço suficiente disponível. Ele pode escutar notificações de alterações de arquivo ou diretório. Ele poderá relatar um aviso se um limite inicial for atingido e relatar um erro se o compartilhamento estiver cheio. Em um aviso, um sistema de reparo pode começar a limpar arquivos mais antigos no compartilhamento. Em um erro, um sistema de reparo pode mover a réplica de serviço para outro nó. Observe como os Estados de condição são descritos em termos de integridade: o estado da condição que pode ser considerada íntegra (OK) ou não íntegro (aviso ou erro).

Depois que os detalhes de monitoramento são definidos, um gravador de Watchdog precisa descobrir como implementar o Watchdog. Se as condições puderem ser determinadas de dentro do serviço, o Watchdog poderá fazer parte do próprio serviço monitorado. Por exemplo, o código de serviço pode verificar o uso do compartilhamento e, em seguida, relatar toda vez que tentar gravar um arquivo. A vantagem dessa abordagem é que o relatório é simples. Deve-se ter cuidado para impedir que bugs de Watchdog afetem a funcionalidade do serviço.

O relatório de dentro do serviço monitorado nem sempre é uma opção. Um Watchdog dentro do serviço pode não ser capaz de detectar as condições. Pode não ter a lógica ou os dados para fazer a determinação. A sobrecarga de monitorar as condições pode ser alta. As condições também podem não ser específicas de um serviço, mas afetam as interações entre os serviços. Outra opção é ter Watchdogs no cluster como processos separados. Os Watchdogs monitoram as condições e o relatório, sem afetar os principais serviços de forma alguma. Por exemplo, esses Watchdogs podem ser implementados como serviços sem estado no mesmo aplicativo, implantados em todos os nós ou nos mesmos nós que o serviço.

Às vezes, um Watchdog em execução no cluster também não é uma opção. Se a condição monitorada for a disponibilidade ou a funcionalidade do serviço conforme os usuários a veem, é melhor ter os Watchdogs no mesmo local que os clientes do usuário. Lá, eles podem testar as operações da mesma forma que os usuários as chamam. Por exemplo, você pode ter um Watchdog que reside fora do cluster, emite solicitações para o serviço e verifica a latência e a exatidão do resultado. (Para um serviço de calculadora, por exemplo, 2 + 2 retorna 4 em um período de tempo razoável?)

Depois que os detalhes do Watchdog forem finalizados, você deverá decidir em uma ID de origem que o identifique de forma exclusiva. Se vários Watchdogs do mesmo tipo estiverem em vida no cluster, eles deverão relatar entidades diferentes ou, se eles reportarem a mesma entidade, usar ID de origem ou propriedade diferente. Dessa forma, seus relatórios podem coexistir. A propriedade do relatório de integridade deve capturar a condição monitorada. (Para o exemplo acima, a propriedade pode ser **compartilhada**.) Se vários relatórios se aplicarem à mesma condição, a propriedade deverá conter algumas informações dinâmicas que permitem que os relatórios coexistam. Por exemplo, se vários compartilhamentos precisarem ser monitorados, o nome da propriedade poderá ser **compartilhando-ShareName**.

> [!NOTE]
> *Não* use o repositório de integridade para manter informações de status. Somente informações relacionadas à integridade devem ser relatadas como integridade, pois essas informações afetam a avaliação de integridade de uma entidade. O repositório de integridade não foi projetado como um repositório de uso geral. Ele usa a lógica de avaliação de integridade para agregar todos os dados no estado de integridade. O envio de informações não relacionadas à integridade (como relatar o status com um estado de integridade OK) não afeta o estado de integridade agregado, mas pode afetar negativamente o desempenho do repositório de integridade.
> 
> 

O próximo ponto de decisão é a entidade a ser reportada. Na maioria das vezes, a condição identifica claramente a entidade. Escolha a entidade com a melhor granularidade possível. Se uma condição impactar todas as réplicas em uma partição, o relatório na partição, não no serviço. No entanto, há casos de canto em que é necessário mais pensamento. Se a condição afetar uma entidade, como uma réplica, mas o desejo for ter a condição sinalizada por mais do que a duração da vida útil da réplica, ela deverá ser relatada na partição. Caso contrário, quando a réplica for excluída, o repositório de integridade limpará todos os seus relatórios. Os gravadores de Watchdog devem pensar sobre os tempos de vida da entidade e do relatório. Deve ser claro quando um relatório deve ser limpo de uma loja (por exemplo, quando um erro relatado em uma entidade não se aplica).

Vejamos um exemplo que reúne os pontos descritos. Considere um aplicativo Service Fabric composto por um serviço persistente com estado mestre e serviços sem estado secundários implantados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O mestre tem uma fila de processamento que contém comandos a serem executados por secundários. Os secundários executam as solicitações de entrada e enviam sinais de confirmação de retorno. Uma condição que poderia ser monitorada é o comprimento da fila de processamento mestre. Se o comprimento da fila mestre atingir um limite, um aviso será relatado. O aviso indica que os secundários não podem manipular a carga. Se a fila atingir o comprimento máximo e os comandos forem removidos, um erro será relatado, pois o serviço não poderá ser recuperado. Os relatórios podem estar na propriedade **statusfila**. O Watchdog reside dentro do serviço e é enviado periodicamente na réplica primária mestra. A vida útil é de dois minutos e é enviada periodicamente a cada 30 segundos. Se o primário ficar inativo, o relatório será limpo automaticamente do repositório. Se a réplica de serviço estiver ativa, mas estiver bloqueada ou tiver outros problemas, o relatório expirará no repositório de integridade. Nesse caso, a entidade é avaliada com erro.

Outra condição que pode ser monitorada é o tempo de execução da tarefa. O mestre distribui tarefas para os secundários com base no tipo de tarefa. Dependendo do design, o mestre poderia sondar os secundários para o status da tarefa. Ele também pode aguardar que os secundários enviem sinais de confirmação quando forem concluídos. No segundo caso, deve-se ter cuidado para detectar situações em que os secundários ou as mensagens são perdidos. Uma opção é que o mestre envie uma solicitação de ping para o mesmo secundário, o que envia de volta seu status. Se nenhum status for recebido, o mestre considerará uma falha e reagendará a tarefa. Esse comportamento pressupõe que as tarefas sejam idempotentes.

A condição monitorada poderá ser convertida como um aviso se a tarefa não for feita em um determinado tempo (**T1**, por exemplo, 10 minutos). Se a tarefa não for concluída no tempo (**T2**, por exemplo, 20 minutos), a condição monitorada poderá ser traduzida como erro. Esse relatório pode ser feito de várias maneiras:

* A réplica primária principal se reporta periodicamente. Você pode ter uma propriedade para todas as tarefas pendentes na fila. Se pelo menos uma tarefa demorar mais, o status do relatório na propriedade **PendingTasks** será um aviso ou erro, conforme apropriado. Se não houver nenhuma tarefa pendente ou todas as tarefas iniciarem a execução, o status do relatório será OK. As tarefas são persistentes. Se o primário ficar inativo, o primário promovido recentemente poderá continuar a relatar corretamente.
* Outro processo de Watchdog (na nuvem ou externo) verifica as tarefas (de fora, com base no resultado da tarefa desejada) para ver se elas foram concluídas. Se eles não respeitarem os limites, um relatório será enviado no serviço mestre. Um relatório também é enviado em cada tarefa que inclui o identificador da tarefa, como **PendingTask + TaskId**. Os relatórios devem ser enviados somente em Estados não íntegros. Defina a vida útil para alguns minutos e marque os relatórios a serem removidos quando eles expirarem para garantir a limpeza.
* O secundário que está executando uma tarefa relata quando demora mais do que o esperado para executá-lo. Ele relata a instância de serviço na propriedade **PendingTasks**. O relatório indica a instância de serviço que tem problemas, mas não captura a situação em que a instância se apresenta. Os relatórios são limpos. Ele pode relatar o serviço secundário. Se o secundário concluir a tarefa, a instância secundária limpará o relatório da loja. O relatório não captura a situação em que a mensagem de confirmação é perdida e a tarefa não é concluída a partir do ponto de vista do mestre.

No entanto, o relatório é feito nos casos descritos acima, os relatórios são capturados na integridade do aplicativo quando a integridade é avaliada.

## <a name="report-periodically-vs-on-transition"></a>Relatar periodicamente versus transição
Usando o modelo de relatório de integridade, os Watchdogs podem enviar relatórios periodicamente ou em transições. A maneira recomendada para o relatório de Watchdog é periodicamente, pois o código é muito mais simples e menos propenso a erros. Os Watchdogs devem se esforçar para ser o mais simples possível para evitar bugs que disparem relatórios incorretos. Relatórios não *íntegros* incorretos afetam avaliações de integridade e cenários com base na integridade, incluindo atualizações. Relatórios *íntegros* incorretos ocultam problemas no cluster, o que não é desejado.

Para relatórios periódicos, o Watchdog pode ser implementado com um temporizador. Em um retorno de chamada do temporizador, o Watchdog pode verificar o estado e enviar um relatório com base no estado atual. Não é necessário ver qual relatório foi enviado anteriormente ou fazer qualquer otimização em termos de mensagens. O cliente de integridade tem lógica em lote para ajudar com o desempenho. Embora o cliente de integridade seja mantido ativo, ele se repete internamente até que o relatório seja confirmado pelo repositório de integridade ou o Watchdog gera um relatório mais recente com a mesma entidade, propriedade e origem.

A geração de relatórios sobre transições requer tratamento cuidadoso de estado. O Watchdog monitora algumas condições e relata somente quando as condições são alteradas. A vantagem dessa abordagem é que menos relatórios são necessários. A desvantagem é que a lógica do Watchdog é complexa. O Watchdog deve manter as condições ou os relatórios, para que eles possam ser inspecionados para determinar as alterações de estado. No failover, deve-se ter cuidado com os relatórios adicionados, mas ainda não enviados para o repositório de integridade. O número de sequência deve ser cada vez maior. Caso contrário, os relatórios serão rejeitados como obsoletos. Nos casos raros em que a perda de dados é incorrida, a sincronização pode ser necessária entre o estado do Reporter e o estado do repositório de integridade.

A geração de relatórios sobre transições faz sentido para os serviços que se reportam, por meio de `Partition` ou `CodePackageActivationContext`. Quando o objeto local (réplica ou pacote de serviço implantado/aplicativo implantado) é removido, todos os seus relatórios também são removidos. Essa limpeza automática ameniza a necessidade de sincronização entre o Reporter e o Health Store. Se o relatório for para a partição pai ou o aplicativo pai, é necessário ter cuidado com o failover para evitar relatórios obsoletos no repositório de integridade. A lógica deve ser adicionada para manter o estado correto e limpar o relatório da loja quando não for necessário mais.

## <a name="implement-health-reporting"></a>Implementar relatórios de integridade
Depois que os detalhes da entidade e do relatório estiverem claros, o envio de relatórios de integridade poderá ser feito por meio da API, do PowerShell ou do REST.

### <a name="api"></a>API
Para relatar por meio da API, você precisa criar um relatório de integridade específico para o tipo de entidade que deseja relatar. Dê ao relatório um cliente de integridade. Como alternativa, crie uma informação de integridade e passe-a para os métodos de relatório corretos em `Partition` ou `CodePackageActivationContext` para relatar as entidades atuais.

O exemplo a seguir mostra relatórios periódicos de um Watchdog dentro do cluster. O Watchdog verifica se um recurso externo pode ser acessado de dentro de um nó. O recurso é necessário para um manifesto do serviço dentro do aplicativo. Se o recurso não estiver disponível, os outros serviços dentro do aplicativo ainda poderão funcionar corretamente. Portanto, o relatório é enviado na entidade do pacote de serviço implantado a cada 30 segundos.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Envie relatórios de integridade com **Send-Imfabric*EntityType*HealthReport**.

O exemplo a seguir mostra relatórios periódicos sobre valores de CPU em um nó. Os relatórios devem ser enviados a cada 30 segundos e têm um tempo de vida de dois minutos. Se eles expirarem, o reporter terá problemas, portanto, o nó será avaliado com erro. Quando a CPU está acima de um limite, o relatório tem um estado de integridade de aviso. Quando a CPU permanece acima de um limite por mais do que o tempo configurado, ela é relatada como um erro. Caso contrário, o reporter envia um estado de integridade OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

O exemplo a seguir relata um aviso transitório em uma réplica. Ele primeiro obtém a ID da partição e, em seguida, a ID da réplica para o serviço em que está interessado. Em seguida, ele envia um relatório de **PowershellWatcher** na propriedade **ResourceDependency**. O relatório é de interesse por apenas dois minutos e é removido do repositório automaticamente.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Envie relatórios de integridade usando REST com solicitações POST que vão para a entidade desejada e que têm no corpo a descrição do relatório de integridade. Por exemplo, consulte Como enviar relatórios de [integridade do cluster](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) REST ou [relatórios de integridade do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Todas as entidades têm suporte.

## <a name="next-steps"></a>Passos seguintes
Com base nos dados de integridade, os gravadores de serviço e os administradores de cluster/aplicativo podem considerar maneiras de consumir as informações. Por exemplo, eles podem configurar alertas com base no status de integridade para capturar problemas graves antes que eles provoquemm interrupções. Os administradores também podem configurar sistemas de reparo para corrigir problemas automaticamente.

[Introdução ao monitoramento de integridade Service Fabric](service-fabric-health-introduction.md)

[Exibir Service Fabric relatórios de integridade](service-fabric-view-entities-aggregated-health.md)

[Como relatar e verificar a integridade do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Usar relatórios de integridade do sistema para solução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização do aplicativo Service Fabric](service-fabric-application-upgrade.md)

