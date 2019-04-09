---
title: Adicionar relatórios de estado de funcionamento do Service Fabric personalizados | Documentos da Microsoft
description: Descreve como enviar relatórios de estado de funcionamento personalizados para entidades de estado de funcionamento do Azure Service Fabric. Oferece recomendações para estruturar e implementar os relatórios de estado de funcionamento de qualidade.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 49ebf4ab95816a3da2f74a464b12b46de6228456
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058616"
---
# <a name="add-custom-service-fabric-health-reports"></a>Adicionar relatórios personalizados de estado de funcionamento do Service Fabric
O Azure Service Fabric introduz um [modelo de estado de funcionamento](service-fabric-health-introduction.md) projetado para sinalizar cluster mau estado de funcionamento e condições do aplicativo em entidades específicas. O modelo de estado de funcionamento utiliza **Informadores de estado de funcionamento** (componentes do sistema e watchdogs). O objetivo é fácil e rápido de diagnóstico e reparo. Criadores de serviços precisam pensar iniciais sobre o estado de funcionamento. Deve ser relatada qualquer condição que pode afetar o estado de funcionamento, especialmente se ele pode ajudar a problemas de sinalizador próximo da raiz. As informações de estado de funcionamento podem poupar tempo e esforço na depuração e investigação. A utilidade é especialmente clara assim que o serviço está em execução em escala na cloud (privada ou do Azure).

O monitor do Service Fabric Informadores identificados condições de interesse. Que reportam nessas condições com base na respetiva vista local. O [arquivo de estado de funcionamento](service-fabric-health-introduction.md#health-store) agrega dados de estado de funcionamento enviados por todos os jornalistas para determinar se as entidades são globalmente em bom estadas. O modelo se destina a ser avançado, flexível e fácil de usar. A qualidade dos relatórios de estado de funcionamento determina a precisão da vista de estado de funcionamento do cluster. Falsos positivos que rostos mostram problemas de mau estado de funcionamento podem afetar negativamente as atualizações ou outros serviços que utilizam dados de estado de funcionamento. Exemplos de tais serviços são serviços de reparação e mecanismos de alerta. Por conseguinte, um pouco de reflexão é necessária para fornecer relatórios que capturam as condições de interesse da melhor forma possível.

Conceber e implementar a geração de relatórios de estado de funcionamento, watchdogs e tem de componentes do sistema:

* Defina a condição que está interessado, a maneira que é monitorizado e o impacto sobre a funcionalidade de cluster ou aplicação. Com base nessas informações, decidir qual a propriedade de relatório de estado de funcionamento e o estado de funcionamento.
* Determinar os [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) que o relatório se aplica a.
* Determinar onde o relatório é concluído, de dentro do serviço ou a partir de um watchdog interno ou externo.
* Defina uma fonte de utilizado para identificar o gerador de relatórios.
* Escolha uma estratégia de geração de relatórios, periodicamente ou em transições. A forma recomendada é periodicamente, pois requer mais simples de código e é menos propenso a erros.
* Determine o intervalo de tempo que o relatório para condições de mau estado de funcionamento deve permanecer no arquivo de estado de funcionamento e como devem ser limpos. Usando essas informações, decida a hora do relatório ao vivo e o comportamento de remove-no-expiração.

Conforme mencionado, a geração de relatórios pode ser feita em:

* A réplica de serviço do Service Fabric monitorizada.
* Watchdogs internos implementados como um serviço do Service Fabric (por exemplo, um serviço Service Fabric sem monitoração de estado que monitoriza as condições e relatórios de problemas). Os watchdogs podem ser implementada um todos os nós ou pode ser com afinidade com o serviço monitorizado.
* Watchdogs internos que executam em nós de Service Fabric, mas são *não* implementados como serviços do Service Fabric.
* Watchdogs externos que o recurso de sonda *fora* cluster do Service Fabric (por exemplo, serviço de monitoramento, como Gomez).

> [!NOTE]
> Uso imediato, o cluster é preenchido com os relatórios de estado de funcionamento enviados pelos componentes de sistema. Leia mais sobre [usar o estado de funcionamento do sistema de relatórios para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios de utilizador têm de ser enviados [entidades de estado de funcionamento](service-fabric-health-introduction.md#health-entities-and-hierarchy) que já foram criadas pelo sistema.
> 
> 

Uma vez o estado de funcionamento reporting design é claro, relatórios de estado de funcionamento podem ser enviados facilmente. Pode usar [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) estado de funcionamento do relatório, se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o cliente de recursos de infraestrutura tem privilégios de administrador. Relatórios podem ser feito por meio da API pelo usando [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), através do PowerShell ou através de REST. Botões de configuração do batch relatórios para um melhor desempenho.

> [!NOTE]
> Estado de funcionamento do relatório é síncrono e representa o trabalho de validação do lado do cliente. O fato de que o relatório é aceite pelo cliente do Estado de funcionamento ou o `Partition` ou `CodePackageActivationContext` objetos não significa que é aplicada no arquivo. É enviada de forma assíncrona e possivelmente em lotes com outros relatórios. O processamento no servidor ainda poderão falhar: o número de sequência pode ser obsoleto, a entidade na qual o relatório tem de ser aplicado tiver sido eliminado, etc.
> 
> 

## <a name="health-client"></a>Cliente de estado de funcionamento
Os relatórios de estado de funcionamento são enviados para o Gestor de estado de funcionamento através de um cliente do Estado de funcionamento, o que se encontrem dentro do cliente de recursos de infraestrutura. O Gestor de estado de funcionamento guarda relatórios no arquivo de estado de funcionamento. O cliente do Estado de funcionamento pode ser configurado com as seguintes definições:

* **HealthReportSendInterval**: O atraso entre a hora que do relatório é adicionado ao cliente e a hora será enviado para o Gestor de estado de funcionamento. Utilizado para relatórios do batch numa única mensagem, em vez de enviar uma mensagem para cada relatório. A criação de batches melhora o desempenho. Predefinição: 30 segundos.
* **HealthReportRetrySendInterval**: O intervalo no qual o cliente do Estado de funcionamento reenvia o estado de funcionamento acumulado relatórios para o Gestor de estado de funcionamento. Predefinição: 30 segundos, mínimo: 1 segundo.
* **HealthOperationTimeout**: O período de tempo limite de uma mensagem de relatório enviada para o Gestor de estado de funcionamento. Se uma mensagem exceder o tempo limite, o cliente do Estado de funcionamento repete-lo até que o Gestor de estado de funcionamento confirma que o relatório tiver sido processado. Predefinição: dois minutos.

> [!NOTE]
> Quando os relatórios são limitativas, o cliente de recursos de infraestrutura deve ser mantido ativo para, pelo menos, o HealthReportSendInterval para garantir que são enviadas. Se a mensagem é perdida ou o Gestor de estado de funcionamento não é possível aplicá-las devido a erros transitórios, o cliente de recursos de infraestrutura deve ser mantido ativo mais para lhe dar uma chance para tentar novamente.
> 
> 

A colocação em memória intermédia no cliente demora a exclusividade dos relatórios em consideração. Por exemplo, se um gerador de relatórios de ruim específico está a comunicar 100 relatórios por segundo na mesma propriedade da mesma entidade, os relatórios são substituídos pela última versão. No máximo um esses relatórios existe na fila de cliente. Se a criação de batches estiver configurada, o número de relatórios enviados para o Gestor de estado de funcionamento é apenas um por intervalo de envio. Este relatório é o último relatório foi adicionado, que reflete o estado mais recente da entidade.
Especificar parâmetros de configuração quando `FabricClient` é criado pela transmissão [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) com os valores pretendidos para entradas relacionadas com o estado de funcionamento.

O exemplo seguinte cria um cliente de recursos de infraestrutura e especifica que os relatórios devem ser enviados quando são adicionados. Em tempos limite e erros que podem ser repetidos, as repetições ocorrem cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Recomendamos que mantenha a predefinição do cliente de recursos de infraestrutura as definições, que definir `HealthReportSendInterval` para 30 segundos. Esta definição garante um desempenho ideal devido a criação de batches. Para os relatórios críticos que têm de ser enviados logo que possível, utilize `HealthReportSendOptions` com Immediate `true` na [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Relatórios de imediato, ignorar o intervalo de criação de batches. Utilize este sinalizador com cuidado; Queremos aproveitar o cliente de estado de funcionamento a criação de batches sempre que possível. Enviar de imediato, também é útil quando está a fechar o cliente de recursos de infraestrutura (por exemplo, o processo determinou estado inválido e precisa de ser encerrada para impedir que os efeitos colaterais). Ele garante o envio dos relatórios acumulados melhor esforço. Quando um relatório é adicionado com o sinalizador de imediato, o cliente do Estado de funcionamento dos lotes todos os relatórios acumulados desde o último envio.

Mesmos parâmetros podem ser especificados quando é criada uma ligação a um cluster através do PowerShell. O exemplo seguinte inicia uma conexão com um cluster local:

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

Da mesma forma para a API, relatórios podem ser enviados usando `-Immediate` comutador para ser enviada imediatamente, independentemente do `HealthReportSendInterval` valor.

Para o resto, os relatórios são enviados para o gateway do Service Fabric, que tem um cliente de recursos de infraestrutura interna. Por predefinição, este cliente está configurado para enviar relatórios em lote a cada 30 segundos. Pode alterar o intervalo de batch com a definição de configuração de cluster `HttpGatewayHealthReportSendInterval` no `HttpGateway`. Como mencionado, a melhor opção é enviar os relatórios com `Immediate` verdadeiro. 

> [!NOTE]
> Para garantir que os serviços não autorizados não é possível reportar o estado de funcionamento nas entidades do cluster, configure o servidor para aceitar pedidos apenas a partir de clientes protegidos. O `FabricClient` utilizada para os relatórios tem de ter segurança ativado para conseguir comunicar com o cluster (por exemplo, com o Kerberos ou autenticação de certificados). Leia mais sobre [segurança de cluster](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Relatório nos serviços de baixo privilégio
Se os serviços do Service Fabric não tem acesso de administrador para o cluster, pode comunicar o estado de funcionamento nas entidades do contexto atual através de `Partition` ou `CodePackageActivationContext`.

* Para serviços sem estado, utilize [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) para gerar relatórios sobre a instância de serviço atual.
* Para serviços com estado, utilize [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) para comunicar na réplica atual.
* Uso [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) para gerar relatórios sobre a entidade de partição atual.
* Uso [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) para gerar relatórios sobre o aplicativo atual.
* Uso [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) para gerar relatórios sobre a aplicação atual implementada no nó atual.
* Uso [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) para um relatório sobre um pacote de serviço para a aplicação implementada no nó atual.

> [!NOTE]
> Internamente, o `Partition` e o `CodePackageActivationContext` manter um cliente de estado de funcionamento configurado com predefinições. Conforme explicado para o [cliente do Estado de funcionamento](service-fabric-report-health.md#health-client), os relatórios são em lotes e enviados num temporizador. Os objetos devem ser mantidos ativos ter a chance de enviar o relatório.
> 
> 

Pode especificar `HealthReportSendOptions` ao enviar relatórios por meio `Partition` e `CodePackageActivationContext` APIs de estado de funcionamento. Se tiver relatórios críticos que têm de ser enviados logo que possível, utilize `HealthReportSendOptions` com Immediate `true`. Relatórios de imediato, ignorar o intervalo de criação de batches do cliente do Estado de funcionamento interno. Como mencionado anteriormente, utilize este sinalizador com cuidado; Queremos aproveitar o cliente de estado de funcionamento a criação de batches sempre que possível.

## <a name="design-health-reporting"></a>Criar relatórios de estado de funcionamento
A primeira etapa na geração de relatórios de alta qualidade é identificar as condições que podem afetar o estado de funcionamento do serviço. Qualquer condição que pode ajudar a problemas de sinalizador no serviço ou do cluster quando for iniciado, ou melhor ainda, antes de um problema acontece – pode potencialmente guardar milhares de milhões de dólares. Os benefícios incluem menor período de inatividade, menos horas da noite gasto a investigar e reparar problemas e maior satisfação do cliente.

Depois das condições são identificadas, escritores de watchdog precisam descobrir a melhor forma de monitorizá-las para o equilíbrio entre a sobrecarga e utilidade. Por exemplo, considere um serviço que faz os cálculos complexos que usam alguns ficheiros temporários numa partilha. Um watchdog pode monitorar a partilha para se certificar de que existe espaço suficiente está disponível. Ele pode escutar notificações de alterações de ficheiro ou diretório. Esse foi possível comunicar um aviso se um limiar inicial for atingido e relatar um erro se a partilha está cheio. Num aviso, um sistema de reparação foi possível iniciar a limpeza dos arquivos antigos na partilha. Num erro, um sistema de reparação foi possível mover a réplica de serviço para outro nó. Observe como os Estados de condição são descritos em termos de estado de funcionamento: o estado da condição que pode ser considerada em bom estado (ok) ou mau estado de funcionamento (aviso ou erro).

Depois dos detalhes de monitorização são configurados, um gravador de watchdog precisa descobrir como implementar o watchdog. Se as condições podem ser determinadas a partir do serviço, o watchdog pode fazer parte do próprio serviço monitorizado. Por exemplo, o código de serviço pode verificar a utilização de partilha e, em seguida, comunicar sempre que tentar escrever um arquivo. A vantagem dessa abordagem é que a geração de relatórios é simples. Deve ter cuidado para impedir que os bugs de watchdog afetar a funcionalidade de serviço.

Relatórios no serviço monitorizado nem sempre é uma opção. Não pode ser capaz de detetar as condições um watchdog no serviço. Pode não ter a lógica ou a dados para tornar a determinação. A sobrecarga de monitorização as condições pode ser elevada. As condições também podem não ser específicas a um serviço, mas em vez disso, afetar as interações entre os serviços. Outra opção consiste em ter watchdogs no cluster como processos separados. Os watchdogs monitorizar as condições e o relatório, sem afetar os serviços principais de qualquer forma. Por exemplo, estes watchdogs poderiam ser implementados como serviços sem monitoração de estado no mesmo aplicativo, implementado em todos os nós ou em nós do mesmo que o serviço.

Às vezes, um watchdog em execução no cluster não é uma opção. Se a condição monitorizada for a disponibilidade ou a funcionalidade do serviço, como os utilizadores veem-lo, é recomendável ter os watchdogs no mesmo local que os clientes de utilizador. Lá, eles podem testar as operações da mesma forma que os utilizadores chamá-los. Por exemplo, pode ter um watchdog que reside fora do cluster, emite solicitações para o serviço e verifica a latência e a correção do resultado. (Para um serviço da Calculadora, por exemplo, 2 + 2 devolver 4 num período de tempo razoável?)

Assim que os detalhes de watchdog tem sido finalizados, deve escolher um ID de origem que identifica de forma exclusiva. Se watchdogs várias do mesmo tipo são vivendo no cluster, tem de comunicar em entidades diferentes, ou, se eles um relatório sobre a mesma entidade, utilize o ID de origem diferente ou propriedade. Dessa forma, os relatórios podem coexistir. A propriedade do relatório de estado de funcionamento, deve capturar a condição monitorizada. (No exemplo acima, a propriedade pode ser **ShareSize**.) Se aplicam vários relatórios para a mesma condição, a propriedade deve conter algumas informações dinâmicas que permite que os relatórios para coexistir. Por exemplo, se precisam de múltiplas partilhas a serem monitorados, o nome da propriedade pode ser **ShareSize sharename**.

> [!NOTE]
> Fazer *não* utilizar o arquivo de estado de funcionamento para manter as informações de estado. Apenas as informações relacionadas com o estado de funcionamento devem ser comunicadas como estado de funcionamento, como essas informações tem impacto sobre a avaliação de estado de funcionamento de uma entidade. O arquivo de estado de funcionamento não foi projetado como um armazenamento para fins gerais. Ele usa a lógica de avaliação do Estado de funcionamento para agregar todos os dados para o estado de funcionamento. Enviar informações não relacionadas com o estado de funcionamento (como comunicar o estado com um Estado de funcionamento de OK) não afeta o estado de funcionamento agregado, mas ele pode afetar negativamente o desempenho do armazenamento de estado de funcionamento.
> 
> 

O próximo ponto de decisão é qual entidade ao relatório no. A maioria das vezes, a condição claramente identifica a entidade. Escolha a entidade com melhor granularidade possível. Se uma condição afeta todas as réplicas numa partição, um relatório sobre a partição, não no serviço. Existem casos em que pensar mais é necessária, porém. Se a condição tem impacto sobre a uma entidade, como uma réplica, mas o desejo é fazer com que a condição sinalizados para mais do que o ciclo de vida de réplica, em seguida, deve ser informado na partição. Caso contrário, quando a réplica é eliminada, o arquivo de estado de funcionamento limpa todos os seus relatórios. Gravadores de Watchdog tem de pensar sobre os tempos de vida da entidade e o relatório. Tem de ser clara quando um relatório deve ser limpa a partir de um arquivo (por exemplo, quando já não se aplica um erro comunicado numa entidade).

Vamos examinar um exemplo que reúna os pontos que descrevi. Considere que uma aplicação do Service Fabric é composto por um serviço persistente com monitoração de Estado principal e secundários serviços sem estado implementados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O mestre tem uma fila de processamento que contém comandos a serem executados por bases de dados secundárias. As bases de dados secundárias executar pedidos recebidos e enviar sinais de back-confirmação. Uma condição que poderia ser monitorizada é o comprimento da fila de processamento mestre. Se o comprimento da fila mestre atinge um limiar, é comunicado um aviso. O aviso indica que as bases de dados secundárias não é possível processar a carga. Se a fila atinge o comprimento máximo e comandos são ignorados, é comunicado um erro, como o serviço não é possível recuperar. Os relatórios podem ter na propriedade **QueueStatus**. O watchdog reside dentro do serviço e é enviado periodicamente na réplica primária mestre. O tempo de duração é dois minutos, e são enviado periodicamente a cada 30 segundos. Se o primário ficar inativo, o relatório é automaticamente limpo do arquivo. Se a réplica de serviço está ativo, mas está travado ou ter outros problemas, o relatório expira no arquivo de estado de funcionamento. Neste caso, a entidade é avaliada em erro.

Outra condição de que pode ser monitorizada é o tempo de execução de tarefa. O mestre distribui tarefas para as bases de dados secundárias com base no tipo de tarefa. Dependendo da estrutura, o mestre foi a consultar as bases de dados secundárias para o estado da tarefa. Ele também poderia esperar para bases de dados secundárias enviar sinais de back-confirmação quando elas são feitas. No segundo caso, deve ter cuidado para situações em que são perdidas die de bases de dados secundárias ou mensagens de detetar. Uma opção é para o mestre Enviar um pedido de ping para o mesmo secundário, que retorna o respetivo estado. Se for recebido sem estado, o modelo global considera-o uma falha e reagenda a tarefa. Este comportamento parte do princípio de que as tarefas sejam idempotentes.

A condição monitorizada pode ser traduzida como um aviso se a tarefa não é feita num determinado período de tempo (**t1**, por exemplo, 10 minutos). Se a tarefa não é concluída no tempo (**t2**, por exemplo, 20 minutos), a condição monitorizada pode ser traduzida como erro. Este relatório pode ser feito de várias formas:

* A réplica primária mestre relatórios em si mesmo periodicamente. Pode ter uma propriedade para todas as tarefas pendentes na fila. Se, pelo menos, uma tarefa leva mais tempo, o estado do relatório na propriedade **PendingTasks** é um aviso ou erro, conforme apropriado. Se não existem tarefas pendentes ou todas as tarefas iniciou a execução, o estado do relatório está OK. As tarefas são persistentes. Se o primário ficar inativo, pode continuar primária recentemente promovida comunicar corretamente.
* Outro processo de watchdog (na cloud ou externa) verifica as tarefas (de fora, com base no resultado da tarefa pretendida) para ver se elas são concluídas. Se eles não respeitam os limites, é enviado um relatório sobre o serviço principal. Um relatório também é enviado em cada tarefa que inclui o identificador de tarefas, como **PendingTask + taskId**. Relatórios devem ser enviados apenas em mau estado de funcionamento dos Estados. Definir a hora em direto para alguns minutos e marcar os relatórios a serem removidos quando expiram para garantir que a limpeza.
* Secundário que está a executar uma tarefa de relatórios quando demora mais tempo do que o esperado para executá-lo. Comunica na instância do serviço na propriedade **PendingTasks**. O relatório aponta a instância de serviço que tem problemas, mas ele não captura a situação em que a instância se tornar inativo. Os relatórios são, em seguida, limpos. Ele foi um relatório sobre o serviço secundário. Se o elemento secundário for concluída a tarefa, a instância secundária limpa o relatório a partir da loja. O relatório não captura a situação em que a mensagem de confirmação é perdida e a tarefa estiver concluída não do ponto de vista do mestre.

No entanto, a comunicação é efetuada em casos descritos acima, os relatórios são capturados no estado de funcionamento do aplicativo quando o estado de funcionamento é avaliado.

## <a name="report-periodically-vs-on-transition"></a>Relatório periodicamente versus na transição
Ao utilizar o modelo de relatório de estado de funcionamento, watchdogs pode enviar relatórios periodicamente ou em transições. A forma recomendada para os relatórios de watchdog é periodicamente, uma vez que o código é muito mais simples e menos propensa a erros. Os watchdogs devem sempre ser tão simples quanto possível para evitar bugs que acionam relatórios incorretos. Incorreta *mau estado de funcionamento* relatórios afetam avaliações de estado de funcionamento e cenários com base no estado de funcionamento, incluindo atualizações. Incorreta *bom estado de funcionamento* ocultar de relatórios de problemas do cluster, o que não for o pretendido.

Para relatórios periódica, o watchdog pode ser implementada com um temporizador. No retorno de chamada do timer, o watchdog pode verificar o estado e enviar um relatório com base no estado atual. Não é necessário para ver o relatório foi enviado anteriormente ou fazer qualquer otimizações em termos de mensagens. O cliente do Estado de funcionamento tem lógica para o ajudar a com desempenho de criação de batches. Embora o cliente do Estado de funcionamento é mantido ativo, internamente repete até que o relatório é reconhecido pelo arquivo de estado de funcionamento ou a watchdog gera um relatório mais recente com a mesma entidade, propriedade e origem.

Relatórios em transições de requer cuidadosa manipulação do Estado. O watchdog monitoriza algumas condições e reporta apenas quando as condições forem alteradas. A vantagem dessa abordagem é que menos relatórios são necessárias. A desvantagem é que a lógica do watchdog é complexa. O watchdog tem de manter as condições ou os relatórios, para que pode ser inspecionadas para determinar as alterações de estado. Na ativação pós-falha, deve ter cuidado com os relatórios incluídos, mas ainda não foi enviada para o arquivo de estado de funcionamento. O número de sequência tem de ser cada vez. Caso contrário, os relatórios são rejeitados como obsoleto. Nos casos raros em que é incorrida perda de dados, a sincronização poderá ser necessária entre o estado do gerador de relatórios e o estado do armazenamento de estado de funcionamento.

Relatórios em transições faz sentido para serviços de relatórios em si, através da `Partition` ou `CodePackageActivationContext`. Quando o objeto local (réplica ou pacote de serviço implementado / aplicação implementada) é removido, todos os seus relatórios também são removidos. A limpeza automática relaxes a necessidade de sincronização entre o gerador de relatórios e o arquivo de estado de funcionamento. Se o relatório destina-se a partição pai ou aplicação principal, deve ter cuidado na ativação pós-falha para evitar relatórios obsoletos no arquivo de estado de funcionamento. Tem de adicionar lógica para manter o estado correto e desmarque o relatório a partir da loja não são necessários mais.

## <a name="implement-health-reporting"></a>Implementar o relatório de estado de funcionamento
Assim que os detalhes da entidade e relatório são claros, enviar relatórios de estado de funcionamento pode ser feito por meio da API, PowerShell ou REST.

### <a name="api"></a>API
Para comunicar através da API, terá de criar um relatório de estado de funcionamento específico para o tipo de entidade que pretende um relatório sobre. Dê o relatório a um cliente do Estado de funcionamento. Em alternativa, crie uma informações de estado de funcionamento e passá-lo para corrigir os métodos de criação de relatórios no `Partition` ou `CodePackageActivationContext` para gerar relatórios sobre entidades atuais.

O exemplo seguinte mostra periódico de relatórios a partir de um watchdog dentro do cluster. O watchdog verifica se um recurso externo pode ser acessado num nó. O recurso é necessário um manifesto de serviço dentro do aplicativo. Se o recurso estiver indisponível, os outros serviços dentro do aplicativo podem continuará a funcionar corretamente. Por conseguinte, o relatório é enviado na entidade de pacote de serviço implementado a cada 30 segundos.

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
Enviar relatórios de estado de funcionamento com **ServiceFabric de envio*EntityType*HealthReport**.

O exemplo seguinte mostra periódico de relatórios sobre os valores de CPU num nó. Os relatórios devem ser enviados a cada 30 segundos e têm um tempo de duração de dois minutos. Se esta expira, o gerador de relatórios tem problemas, para que o nó é avaliado em erro. Quando a CPU for superior a um limiar, o relatório tem um Estado de funcionamento de aviso. Quando a CPU permanece acima um limiar para mais do que o tempo configurado, será comunicado como um erro. Caso contrário, o gerador de relatórios envia um Estado de funcionamento de OK.

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

O exemplo seguinte reporta um aviso transitório numa réplica. Ele primeiro obtém o ID de partição e, em seguida, o ID de réplica para o serviço que está interessada em. Em seguida, envia um relatório a partir **PowershellWatcher** na propriedade **ResourceDependency**. O relatório é de interesse para somente dois minutos e este é removido da loja automaticamente.

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
Envie relatórios de estado de funcionamento através de REST com solicitações POST que vão para a entidade pretendida e tem, no corpo, a descrição do relatório de estado de funcionamento. Por exemplo, veja como enviar REST [relatórios de estado de funcionamento do cluster](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) ou [relatórios de estado de funcionamento do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Todas as entidades são suportadas.

## <a name="next-steps"></a>Passos Seguintes
Com base nos dados de estado de funcionamento, escritores de serviço e os administradores de cluster/aplicação podem pensar em formas de usar as informações. Por exemplo, eles podem configurar alertas com base no estado de funcionamento para capturar problemas graves, antes que provoque interrupções. Os administradores podem também configurar sistemas de reparo para corrigir problemas automaticamente.

[Introdução ao estado de funcionamento do Service Fabric monitorização](service-fabric-health-introduction.md)

[Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Como comunicar e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização de aplicação do Service Fabric](service-fabric-application-upgrade.md)

