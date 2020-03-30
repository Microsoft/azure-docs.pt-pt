---
title: Adicione relatórios de saúde personalizados de tecido de serviço
description: Descreve como enviar relatórios de saúde personalizados para entidades de saúde azure service fabric. Dá recomendações para a conceção e implementação de relatórios de saúde de qualidade.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451592"
---
# <a name="add-custom-service-fabric-health-reports"></a>Adicione relatórios de saúde personalizados de tecido de serviço
A Azure Service Fabric introduz um modelo de [saúde](service-fabric-health-introduction.md) concebido para sinalizar condições de cluster e aplicação pouco saudáveis em entidades específicas. O modelo de saúde utiliza repórteres de **saúde** (componentes do sistema e cães de guarda). O objetivo é fácil e rápido de diagnóstico e reparação. Os escritores de serviços precisam pensar bem na saúde. Qualquer condição que possa ter impacto na saúde deve ser reportada, especialmente se puder ajudar problemas de sinalização perto da raiz. A informação de saúde pode economizar tempo e esforço na depuração e investigação. A utilidade é especialmente clara quando o serviço está em funcionamento em escala na nuvem (privada ou Azure).

Os repórteres do Serviço Fabric monitorizam as condições de interesse identificadas. Relatam essas condições com base na sua visão local. A loja de [saúde](service-fabric-health-introduction.md#health-store) agrega dados de saúde enviados por todos os repórteres para determinar se as entidades são globalmente saudáveis. O modelo destina-se a ser rico, flexível e fácil de usar. A qualidade dos relatórios de saúde determina a exatidão da visão de saúde do cluster. Falsos positivos que mostram erradamente problemas insalubres podem impactar negativamente upgrades ou outros serviços que usam dados de saúde. Exemplos desses serviços são serviços de reparação e mecanismos de alerta. Por conseguinte, é necessário pensar em fornecer relatórios que captem as condições de interesse da melhor forma possível.

Para conceber e implementar relatórios de saúde, cães de guarda e componentes do sistema devem:

* Defina a condição em que estão interessados, a forma como é monitorizado e o impacto no cluster ou na funcionalidade da aplicação. Com base nesta informação, decida sobre a propriedade do relatório de saúde e o estado de saúde.
* Determine a [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) a que o relatório se aplica.
* Determine onde o relatório é feito, a partir do interior do serviço ou de um cão de guarda interno ou externo.
* Defina uma fonte usada para identificar o repórter.
* Escolha uma estratégia de reporte, periodicamente ou em transições. A forma recomendada é periodicamente, pois requer código mais simples e é menos propensa a erros.
* Determine quanto tempo o relatório para condições pouco saudáveis deve permanecer no armazém de saúde e como deve ser apurado. Utilizando esta informação, decida o tempo do relatório para viver e remover o comportamento de expiração.

Como mencionado, a comunicação pode ser feita a partir de:

* A réplica de serviço de serviço de serviço monitorado.
* Cães de guarda internos implantados como um serviço de tecido de serviço (por exemplo, um serviço apátrida service Fabric que monitoriza as condições e os relatórios de problemas). Os cães de guarda podem ser implantados com todos os nós ou podem ser afinados com o serviço monitorizado.
* Cães de guarda internos que funcionam nos nódosos de Tecido de Serviço mas *não* são implementados como serviços de Fabricação de Serviço.
* Cães de guarda externos que sondam o recurso de *fora* do cluster Service Fabric (por exemplo, serviço de monitorização como o Gomez).

> [!NOTE]
> Fora da caixa, o aglomerado é povoado com relatórios de saúde enviados pelos componentes do sistema. Leia mais na [Utilização de relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios dos utilizadores devem ser enviados a entidades de [saúde](service-fabric-health-introduction.md#health-entities-and-hierarchy) que já tenham sido criadas pelo sistema.
> 
> 

Uma vez que o design de relatórios de saúde é claro, relatórios de saúde podem ser enviados facilmente. Pode utilizar o [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para reportar saúde se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o cliente do tecido tiver privilégios de administração. O reporte pode ser feito através da API utilizando [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), através da PowerShell, ou através do REST. Relatórios de lote de botões de configuração para um melhor desempenho.

> [!NOTE]
> A saúde do relatório é sincronizada, e representa apenas o trabalho de validação do lado do cliente. O facto de o relatório ser aceite `Partition` `CodePackageActivationContext` pelo cliente de saúde ou pelos ou objetos não significa que seja aplicado na loja. É enviado assincronicamente e possivelmente repleto de outros relatórios. O processamento no servidor pode ainda falhar: o número da sequência pode estar estagnado, a entidade em que o relatório deve ser aplicado foi eliminada, etc.
> 
> 

## <a name="health-client"></a>Cliente de saúde
Os relatórios de saúde são enviados ao gerente de saúde através de um cliente de saúde, que vive dentro do cliente de tecido. O gerente da saúde guarda relatórios na loja de saúde. O cliente de saúde pode ser configurado com as seguintes definições:

* **HealthReportSendInterval**: O atraso entre o momento em que o relatório é adicionado ao cliente e o tempo que é enviado ao gestor de saúde. Usado para enviar relatórios numa única mensagem, em vez de enviar uma mensagem para cada relatório. O lote melhora o desempenho. Predefinição: 30 segundos.
* **HealthReportRetrySendInterval**: O intervalo em que o cliente de saúde reenvia relatórios de saúde acumulados ao gestor de saúde. Predefinição: 30 segundos, mínimo: 1 segundo.
* **SaúdeOperationTimeout**: O período de tempo de paragem para uma mensagem de relatório enviada ao gestor de saúde. Se uma mensagem for para fora, o cliente de saúde a retenta até que o gestor de saúde confirme que o relatório foi processado. Padrão: dois minutos.

> [!NOTE]
> Quando os relatórios são loteados, o cliente de tecido deve ser mantido vivo pelo menos para o HealthReportSendInterval para garantir que são enviados. Se a mensagem for perdida ou o gestor de saúde não puder aplicá-los devido a erros transitórios, o cliente do tecido deve ser mantido vivo por mais tempo para lhe dar a oportunidade de se recandidatar.
> 
> 

O tampão no cliente leva em consideração a singularidade dos relatórios. Por exemplo, se um repórter em particular está a reportar 100 relatórios por segundo sobre a mesma propriedade da mesma entidade, os relatórios são substituídos pela última versão. No máximo, existe um relatório deste tipo na fila do cliente. Se o lote estiver configurado, o número de relatórios enviados ao gestor de saúde é apenas um por intervalo de envio. Este relatório é o último relatório acrescentado, que reflete o estado mais atual da entidade.
Especifique `FabricClient` os parâmetros de configuração quando for criado através do [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) com os valores desejados para entradas relacionadas com a saúde.

O exemplo seguinte cria um cliente de tecido e especifica que os relatórios devem ser enviados quando são adicionados. Nos intervalos e erros que podem ser retentados, as tentativas acontecem a cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Recomendamos manter as definições de `HealthReportSendInterval` cliente de tecido predefinido, que se fixam em 30 segundos. Esta definição garante um desempenho ótimo devido ao loteamento. Para relatórios críticos que devem ser `HealthReportSendOptions` enviados o mais rapidamente possível, utilize com immediate `true` in [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Relatórios imediatos contornam o intervalo de loteamento. Use esta bandeira com cuidado; queremos aproveitar o lote do cliente de saúde sempre que possível. O envio imediato também é útil quando o cliente do tecido está a fechar (por exemplo, o processo determinou o estado inválido e precisa de ser encerrado para evitar efeitos secundários). Garante um melhor envio dos relatórios acumulados. Quando um relatório é adicionado com bandeira imediata, o cliente de saúde emlota todos os relatórios acumulados desde o último envio.

Os mesmos parâmetros podem ser especificados quando uma ligação a um cluster é criada através do PowerShell. O exemplo seguinte inicia uma ligação a um cluster local:

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

Da mesma forma que a API, os relatórios podem ser enviados utilizando `-Immediate` o interruptor para serem enviados imediatamente, independentemente do `HealthReportSendInterval` valor.

Para o REST, os relatórios são enviados para o gateway service Fabric, que tem um cliente de tecido interno. Por padrão, este cliente está configurado para enviar relatórios loteados a cada 30 segundos. Pode alterar o intervalo do lote `HttpGatewayHealthReportSendInterval` `HttpGateway`com a definição de configuração do cluster . Como mencionado, uma melhor opção `Immediate` é enviar os relatórios com verdade. 

> [!NOTE]
> Para garantir que os serviços não autorizados não podem reportar saúde contra as entidades do cluster, configure o servidor para aceitar pedidos apenas de clientes seguros. O `FabricClient` utilizado para reportar deve ter segurança habilitada para poder comunicar com o cluster (por exemplo, com Kerberos ou autenticação de certificado). Leia mais sobre segurança de [clusters](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Relatório de dentro de serviços de baixo privilégio
Se os serviços de Serviço Fabric não tiverem acesso ao cluster, pode `Partition` reportar `CodePackageActivationContext`saúde em entidades do contexto atual através ou .

* Para serviços apátridas, utilize [iStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) para reportar sobre a atual instância de serviço.
* Para serviços estatais, utilize [iStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) para reportar sobre a réplica atual.
* Utilize [iServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) para reportar sobre a entidade partição atual.
* Utilize [codePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) para reportar sobre a aplicação atual.
* Utilize [codePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) para reportar sobre a aplicação atual implementada no nó atual.
* Utilize [codePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) para reportar um pacote de serviço para a aplicação implementada no nó atual.

> [!NOTE]
> Internamente, `Partition` o `CodePackageActivationContext` e o porão de um cliente de saúde configurado com configurações predefinidas. Como explicado para o cliente de [saúde,](service-fabric-report-health.md#health-client)os relatórios são lotados e enviados num temporizador. Os objetos devem ser mantidos vivos para poderem enviar o relatório.
> 
> 

Pode especificar `HealthReportSendOptions` ao enviar `Partition` `CodePackageActivationContext` relatórios através e APIs de saúde. Se tiver relatórios críticos que devem ser `HealthReportSendOptions` enviados o mais rapidamente possível, utilize com Imediatamente `true`. Relatórios imediatos contornam o intervalo de loteamento do cliente de saúde interna. Como mencionado anteriormente, use esta bandeira com cuidado; queremos aproveitar o lote do cliente de saúde sempre que possível.

## <a name="design-health-reporting"></a>Relatórios de saúde de conceção
O primeiro passo para gerar relatórios de alta qualidade é identificar as condições que podem afetar a saúde do serviço. Qualquer condição que possa ajudar a sinalizar problemas no serviço ou no cluster quando começa - ou melhor, antes que um problema aconteça - pode potencialmente poupar biliões de dólares. Os benefícios incluem menos tempo de descanso, menos horas noturnas passadas a investigar e reparar problemas, e uma maior satisfação do cliente.

Uma vez identificadas as condições, os escritores de cães de guarda precisam descobrir a melhor maneira de monitorizá-los para o equilíbrio entre a sobrecarga e a utilidade. Por exemplo, considere um serviço que faça cálculos complexos que usam alguns ficheiros temporários numa ação. Um cão de guarda poderia monitorizar a partilha para garantir que há espaço suficiente disponível. Pode ouvir notificações de ficheiros ou alterações de diretório. Pode denunciar um aviso se for atingido um limiar inicial e denunciar um erro se a parte estiver cheia. Num aviso, um sistema de reparação pode começar a limpar ficheiros mais antigos da parte. Num erro, um sistema de reparação pode mover a réplica do serviço para outro nó. Note como a condição é descrita em termos de saúde: o estado da condição que pode ser considerada saudável (ok) ou insalubre (aviso ou erro).

Uma vez definidos os detalhes de monitorização, um escritor de cães de guarda precisa descobrir como implementar o cão de guarda. Se as condições puderem ser determinadas dentro do serviço, o cão de guarda pode fazer parte do próprio serviço monitorizado. Por exemplo, o código de serviço pode verificar o uso da partilha e, em seguida, reportar cada vez que tenta escrever um ficheiro. A vantagem desta abordagem é que a comunicação é simples. Deve ser tomado cuidado para evitar que os bugs de cão de guarda afetem a funcionalidade do serviço.

O reporte de dentro do serviço monitorizado nem sempre é uma opção. Um cão de guarda dentro do serviço pode não ser capaz de detetar as condições. Pode não ter a lógica ou os dados para fazer a determinação. A sobrecarga de monitorização das condições pode ser elevada. As condições também podem não ser específicas de um serviço, mas sim afetar as interações entre serviços. Outra opção é ter cães de guarda no cluster como processos separados. Os cães de guarda monitorizam as condições e reportam, sem afetar de forma alguma os principais serviços. Por exemplo, estes cães de guarda poderiam ser implementados como serviços apátridas na mesma aplicação, implantados em todos os nós ou nos mesmos nós que o serviço.

Às vezes, um cão de guarda correndo no aglomerado também não é uma opção. Se a condição monitorizada for a disponibilidade ou funcionalidade do serviço como os utilizadores o vêem, o melhor é ter os cães de guarda no mesmo local que os clientes do utilizador. Lá, podem testar as operações da mesma forma que os utilizadores as chamam. Por exemplo, pode ter um cão de guarda que vive fora do cluster, emite pedidos ao serviço, e verifica a latência e correção do resultado. (Para um serviço de calculadora, por exemplo, 2+2 devolve 4 num período de tempo razoável?)

Uma vez finalizados os detalhes do cão de guarda, deve decidir sobre um ID de origem que o identifique de forma única. Se vários cães de guarda do mesmo tipo estiverem a viver no cluster, devem reportar sobre diferentes entidades ou, se reportarem sobre a mesma entidade, utilizar diferentes identidades ou propriedades de origem. Desta forma, os seus relatórios podem coexistir. A propriedade do relatório de saúde deve capturar a condição monitorizada. (Para o exemplo acima, a propriedade pode ser **ShareSize**.) Se vários relatórios se aplicarem à mesma condição, a propriedade deve conter alguma informação dinâmica que permita que os relatórios coexistam. Por exemplo, se várias ações precisarem de ser monitorizadas, o nome da propriedade pode ser **ShareSize-sharename**.

> [!NOTE]
> *Não* utilize a loja de saúde para manter as informações sobre o estado. Apenas informações relacionadas com a saúde devem ser comunicadas como saúde, uma vez que esta informação tem impacto na avaliação da saúde de uma entidade. A loja de saúde não foi concebida como uma loja de fins gerais. Usa a lógica de avaliação da saúde para agregar todos os dados no estado de saúde. O envio de informações não relacionadas com a saúde (como o estado de reporte com um estado de saúde de OK) não afeta o estado de saúde agregado, mas pode afetar negativamente o desempenho da loja de saúde.
> 
> 

O próximo ponto de decisão é qual a entidade a informar. Na maior parte do tempo, a condição identifica claramente a entidade. Escolha a entidade com a melhor granularidade possível. Se uma condição impactar todas as réplicas numa divisória, informe sobre a partição, não no serviço. Há casos de canto onde mais pensamento é necessário, no entanto. Se a condição impactar uma entidade, como uma réplica, mas o desejo é ter a condição sinalizada por mais do que a duração da vida da réplica, então deve ser reportada na partição. Caso contrário, quando a réplica é apagada, a loja de saúde limpa todos os seus relatórios. Os escritores de cães de guarda devem pensar nas vidas da entidade e no relatório. Deve ficar claro quando um relatório deve ser limpo de uma loja (por exemplo, quando um erro reportado numa entidade já não se aplica).

Vejamos um exemplo que reúne os pontos que descrevi. Considere uma aplicação de Tecido de Serviço composta por um serviço principal e persistente e serviços apátridas secundários implantados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O mestre tem uma fila de processamento que contém comandos para ser executado por secundários. Os secundários executam os pedidos de entrada e enviam de volta sinais de reconhecimento. Uma condição que poderia ser monitorizada é o comprimento da fila de processamento principal. Se o comprimento da fila principal atingir um limiar, é comunicado um aviso. O aviso indica que os secundários não aguentam a carga. Se a fila atingir o comprimento máximo e os comandos forem retirados, é reportado um erro, uma vez que o serviço não pode recuperar. Os relatórios podem estar na propriedade **QueueStatus**. O cão de guarda vive dentro do serviço, e é enviado periodicamente na réplica primária principal. A hora de viver é de dois minutos, e é enviada periodicamente a cada 30 segundos. Se a primária descer, o relatório é limpo automaticamente da loja. Se a réplica do serviço estiver em cima, mas estiver num impasse ou com outros problemas, o relatório expira na loja de saúde. Neste caso, a entidade é avaliada por engano.

Outra condição que pode ser monitorizada é o tempo de execução da tarefa. O mestre distribui tarefas aos secundários com base no tipo de tarefa. Dependendo do design, o mestre pode sondar os secundários para o estatuto de tarefa. Também poderia esperar que os secundários enviassem de volta sinais de reconhecimento quando terminarem. No segundo caso, é necessário ter cuidado para detetar situações em que os secundários morrem ou se perdem mensagens. Uma opção é que o mestre envie um pedido de ping para o mesmo secundário, que devolve o seu estatuto. Se nenhum estatuto for recebido, o mestre considera-o uma falha e remarca a tarefa. Este comportamento pressupõe que as tarefas são idempotentes.

A condição monitorizada pode ser traduzida como um aviso se a tarefa não for feita num determinado tempo **(t1**, por exemplo, 10 minutos). Se a tarefa não estiver concluída a tempo **(t2**, por exemplo, 20 minutos), a condição monitorizada pode ser traduzida como Erro. Este relatório pode ser feito de várias maneiras:

* A réplica primária principal principal relata sobre si mesma periodicamente. Você pode ter uma propriedade para todas as tarefas pendentes na fila. Se pelo menos uma tarefa demorar mais tempo, o estado do relatório na propriedade **Pendentes Tarefas** é um aviso ou erro, conforme apropriado. Se não houver tarefas pendentes ou todas as tarefas iniciadas, o estado do relatório está bem. As tarefas são persistentes. Se as primárias descerem, as primárias recém-promovidas podem continuar a reportar corretamente.
* Outro processo de vigilância (na nuvem ou externa) verifica as tarefas (de fora, com base no resultado de tarefa pretendido) para ver se estão concluídas. Se não respeitarem os limiares, é enviado um relatório sobre o serviço principal. É também enviado um relatório em cada tarefa que inclui o identificador de tarefas, como **o PendenteTask+taskId**. Os relatórios só devem ser enviados em estados pouco saudáveis. Marque o tempo para viver até alguns minutos e marque os relatórios a remover quando expirarem para garantir a limpeza.
* O secundário que está a executar um relatório de tarefas quando demora mais do que o esperado a executá-lo. Informa sobre a instância de serviço na propriedade **Pendentes Tarefas**. O relatório aponta a instância de serviço que tem problemas, mas não capta a situação em que a ocorrência morre. Os relatórios estão limpos. Pode reportar sobre o serviço secundário. Se o secundário completar a tarefa, a instância secundária retira o relatório da loja. O relatório não capta a situação em que a mensagem de reconhecimento se perde e a tarefa não está terminada do ponto de vista do mestre.

No entanto, o relatório é feito nos casos acima descritos, os relatórios são capturados na saúde da aplicação quando a saúde é avaliada.

## <a name="report-periodically-vs-on-transition"></a>Relatório periódico vs. sobre transição
Utilizando o modelo de relatóriode saúde, os cães de guarda podem enviar relatórios periodicamente ou sobre transições. A forma recomendada de reportagem de cães de guarda é periodicamente, porque o código é muito mais simples e menos propenso a erros. Os cães de guarda devem esforçar-se para serem o mais simples possível para evitar bugs que desencadeiem relatórios incorretos. Relatórios *incorretos e insalubres* têm impacto em avaliações e cenários de saúde com base na saúde, incluindo upgrades. Relatórios *saudáveis* incorretos escondem problemas no cluster, o que não é desejado.

Para reportagens periódicas, o cão de guarda pode ser implementado com um temporizador. Numa chamada de temporizador, o cão de guarda pode verificar o estado e enviar um relatório baseado no estado atual. Não há necessidade de ver que relatório foi enviado anteriormente ou fazer quaisquer otimizações em termos de mensagens. O cliente de saúde tem lógica de loteamento para ajudar com o desempenho. Enquanto o cliente de saúde é mantido vivo, ele se retenta internamente até que o relatório seja reconhecido pela loja de saúde ou o cão de guarda gera um relatório mais recente com a mesma entidade, propriedade e fonte.

A comunicação sobre as transições requer um manuseamento cuidadoso do Estado. O cão de guarda monitoriza algumas condições e reporta apenas quando as condições mudam. O lado positivo desta abordagem é que são necessários menos relatórios. A desvantagem é que a lógica do cão de guarda é complexa. O cão de guarda deve manter as condições ou os relatórios, de modo a que possam ser inspecionados para determinar as alterações do Estado. No caso do fracasso, há que ter cuidado com relatórios adicionados, mas ainda não enviados para a loja de saúde. O número da sequência deve estar cada vez maior. Caso contrário, os relatórios são rejeitados como estando velhos. Nos raros casos em que a perda de dados é incorrida, pode ser necessária sincronização entre o estado do repórter e o estado da loja de saúde.

A comunicação sobre as transições faz `Partition` `CodePackageActivationContext`sentido para os serviços que se reportam sobre si mesmos, através ou . Quando o objeto local (réplica ou pacote de serviço implantado/aplicação implantada) é removido, todos os seus relatórios também são removidos. Esta limpeza automática relaxa a necessidade de sincronização entre repórter e loja de saúde. Se o relatório for para a partição dos pais ou para a aplicação dos pais, deve ser tomado cuidado para evitar relatórios antigos na loja de saúde. Há que acrescentar a lógica para manter o estado correto e limpar o relatório da loja quando já não é necessário.

## <a name="implement-health-reporting"></a>Implementar relatórios de saúde
Uma vez que a entidade e os detalhes do relatório sejam claros, o envio de relatórios de saúde pode ser feito através da API, PowerShell ou REST.

### <a name="api"></a>API
Para informar através da API, é necessário criar um relatório de saúde específico do tipo de entidade que pretende reportar. Dê o relatório a um cliente de saúde. Em alternativa, crie uma informação de `Partition` saúde e aprove-a para corrigir métodos de reporte sobre ou `CodePackageActivationContext` para reportar sobre as entidades atuais.

O exemplo seguinte mostra relatórios periódicos de um cão de guarda dentro do cluster. O cão de guarda verifica se um recurso externo pode ser acedido dentro de um nó. O recurso é necessário por um manifesto de serviço dentro da aplicação. Se o recurso não estiver disponível, os outros serviços dentro da aplicação ainda podem funcionar corretamente. Por conseguinte, o relatório é enviado sobre a entidade do pacote de serviços implantado a cada 30 segundos.

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
Envie relatórios de saúde com O Relatório de Saúde da Entidade de **Fabricação de Envio*EntityType*** de Serviços De Saúde .

O exemplo seguinte mostra relatórios periódicos sobre os valores da CPU num nó. Os relatórios devem ser enviados a cada 30 segundos, e têm tempo para viver dois minutos. Se expirarem, o repórter tem problemas, por isso o nó é avaliado por engano. Quando a CPU está acima de um limiar, o relatório tem um estado de alerta sanitário. Quando o CPU permanece acima de um limiar por mais do que o tempo configurado, é reportado como um erro. Caso contrário, o repórter envia um estado de saúde de OK.

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

O exemplo que se segue relata um aviso transitório sobre uma réplica. Primeiro obtém a identificação da divisória e, em seguida, a réplica de identificação para o serviço em que está interessado. Em seguida, envia um relatório do **PowershellWatcher** sobre a **propriedade ResourceDependency**. O relatório é de interesse por apenas dois minutos, e é removido automaticamente da loja.

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
Envie relatórios de saúde usando o REST com pedidos POST que vão para a entidade desejada e têm no organismo a descrição do relatório de saúde. Por exemplo, consulte como enviar relatórios de [saúde](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) do cluster REST ou relatórios de [saúde](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)de serviço . Todas as entidades são apoiadas.

## <a name="next-steps"></a>Passos seguintes
Com base nos dados de saúde, os escritores de serviços e administradores de cluster/aplicação podem pensar em formas de consumir a informação. Por exemplo, podem criar alertas baseados no estado de saúde para apanhar problemas graves antes de provocarem interrupções. Os administradores também podem criar sistemas de reparação para corrigir problemas automaticamente.

[Introdução à monitorização da saúde do tecido de serviço](service-fabric-health-introduction.md)

[Ver relatórios de saúde de tecido de serviço](service-fabric-view-entities-aggregated-health.md)

[Como reportar e verificar a saúde do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Use relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade de aplicação de tecido de serviço](service-fabric-application-upgrade.md)

