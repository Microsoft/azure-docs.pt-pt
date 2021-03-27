---
title: Adicionar relatórios de saúde personalizados do Service Fabric
description: Descreve como enviar relatórios de saúde personalizados a entidades de saúde do Azure Service Fabric. Dá recomendações para a conceção e implementação de relatórios de saúde de qualidade.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c87c063ac9d3b4810657f72c46c17725b8899c77
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626174"
---
# <a name="add-custom-service-fabric-health-reports"></a>Adicionar relatórios de saúde personalizados do Service Fabric
A Azure Service Fabric introduz um [modelo de saúde](service-fabric-health-introduction.md) projetado para sinalizar condições de cluster e aplicação pouco saudáveis em entidades específicas. O modelo de saúde utiliza **repórteres de saúde** (componentes do sistema e cães de guarda). O objetivo é um diagnóstico e reparação fáceis e rápidos. Os escritores de serviços precisam pensar frontalmente sobre a saúde. Qualquer condição que possa ter impacto na saúde deve ser reportada, especialmente se puder ajudar a sinalizar problemas próximos da raiz. A informação de saúde pode economizar tempo e esforço na depuragem e investigação. A utilidade é especialmente clara uma vez que o serviço está em funcionamento em escala na nuvem (privada ou Azure).

Os repórteres do Service Fabric monitorizam as condições de interesse identificadas. Relatam essas condições com base na sua visão local. A [loja de saúde](service-fabric-health-introduction.md#health-store) agrega dados de saúde enviados por todos os repórteres para determinar se as entidades são globalmente saudáveis. O modelo destina-se a ser rico, flexível e fácil de usar. A qualidade dos relatórios de saúde determina a precisão da visão de saúde do cluster. Falsos positivos que mostram erradamente problemas insalubres podem impactar negativamente atualizações ou outros serviços que usam dados de saúde. Exemplos destes serviços são serviços de reparação e mecanismos de alerta. Portanto, alguns pensamentos são necessários para fornecer relatórios que captam as condições de interesse da melhor maneira possível.

Para conceber e implementar relatórios de saúde, os cães de guarda e os componentes do sistema devem:

* Defina a condição em que estão interessados, a forma como é monitorizado e o impacto no cluster ou na funcionalidade da aplicação. Com base nesta informação, decida sobre a propriedade do relatório de saúde e o estado de saúde.
* Determine a [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) a que o relatório se aplica.
* Determinar onde o relatório é feito, a partir do serviço ou de um cão de guarda interno ou externo.
* Defina uma fonte usada para identificar o repórter.
* Escolha uma estratégia de reporte, quer periodicamente, quer em transições. O caminho recomendado é periodicamente, pois requer um código mais simples e é menos propenso a erros.
* Determinar quanto tempo o relatório para condições pouco saudáveis deve permanecer na loja de saúde e como deve ser limpo. Usando esta informação, decida o tempo do relatório para viver e remover o comportamento de validade.

Como mencionado, a comunicação pode ser feita a partir de:

* A réplica de serviço de serviço monitorado.
* Cães de guarda internos implantados como um serviço de Tecido de Serviço (por exemplo, um serviço apátrida de Tecido de Serviço que monitoriza as condições e relatórios de problemas). Os cães de guarda podem ser implantados em todos os nós ou podem ser afinados com o serviço monitorizado.
* Cães de guarda internos que funcionam nos nós do Tecido de Serviço mas *não* são implementados como serviços de Service Fabric.
* Cães de guarda externos que sondam o recurso de *fora* do cluster de Tecido de Serviço (por exemplo, serviço de monitorização como o Gomez).

> [!NOTE]
> Fora da caixa, o cluster é preenchido com relatórios de saúde enviados pelos componentes do sistema. Leia mais na [Utilização de relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios dos [utentes](service-fabric-health-introduction.md#health-entities-and-hierarchy) devem ser enviados sobre entidades de saúde que já tenham sido criadas pelo sistema.
> 
> 

Uma vez que o design de relatórios de saúde é claro, relatórios de saúde podem ser enviados facilmente. Pode utilizar [o FabricClient](/dotnet/api/system.fabric.fabricclient) para reportar saúde se o cluster não for [seguro](service-fabric-cluster-security.md) ou se o cliente de tecido tiver privilégios administrativos. A reporte pode ser feita através da API utilizando [FabricClient.HealthManager.ReportHealth,](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth)através da PowerShell, ou através do REST. Os relatórios do lote dos botões de configuração para melhorar o desempenho.

> [!NOTE]
> A saúde do relatório é sincronizada, e representa apenas o trabalho de validação do lado do cliente. O facto de o relatório ser aceite pelo cliente de saúde ou pelos `Partition` `CodePackageActivationContext` ou objetos não significa que seja aplicado na loja. É enviado assíncronosamente e possivelmente em lotado com outros relatórios. O processamento no servidor pode ainda falhar: o número da sequência pode estar estragado, a entidade na qual o relatório deve ser aplicado foi eliminada, etc.
> 
> 

## <a name="health-client"></a>Cliente de saúde
Os relatórios de saúde são enviados ao gestor de saúde através de um cliente de saúde, que vive dentro do cliente do tecido. O gerente de saúde guarda relatórios na loja de saúde. O cliente de saúde pode ser configurado com as seguintes definições:

* **HealthReportSendInterval**: O atraso entre o momento em que o relatório é adicionado ao cliente e o tempo que é enviado ao gestor de saúde. Usado para relatar relatórios numa única mensagem, em vez de enviar uma mensagem para cada relatório. O lote melhora o desempenho. Padrão: 30 segundos.
* **HealthReportRetrySendInterval**: O intervalo em que o cliente de saúde reensifica relatórios de saúde acumulados ao gestor de saúde. Predefinição: 30 segundos, mínimo: 1 segundo.
* **HealthOperationTimeout**: O período de tempo limite para uma mensagem de relatório enviada ao gestor de saúde. Se uma mensagem for divulgada, o cliente de saúde retira-a até que o responsável pela saúde confirme que o relatório foi processado. Padrão: dois minutos.

> [!NOTE]
> Quando os relatórios forem em lotados, o cliente de tecido deve ser mantido vivo pelo menos para o HealthReportSendInterval para garantir que são enviados. Se a mensagem for perdida ou o gestor de saúde não puder aplicá-las devido a erros transitórios, o cliente de tecido deve ser mantido vivo por mais tempo para lhe dar a oportunidade de voltar a tentar.
> 
> 

O tampão sobre o cliente leva em consideração a singularidade dos relatórios. Por exemplo, se um repórter em particular estiver a reportar 100 relatórios por segundo sobre a mesma propriedade da mesma entidade, os relatórios são substituídos pela última versão. No máximo, um desses relatórios existe na fila dos clientes. Se o lote estiver configurado, o número de relatórios enviados ao health manager é apenas um por intervalo de envio. Este relatório é o último relatório adicional, que reflete o estado mais atual da entidade.
Especifique os parâmetros de configuração quando `FabricClient` é criado através da passagem de [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) com os valores desejados para entradas relacionadas com a saúde.

O exemplo a seguir cria um cliente de tecido e especifica que os relatórios devem ser enviados quando são adicionados. Nos intervalos e erros que podem ser novamente julgados, as retrações acontecem a cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Recomendamos manter as definições padrão do cliente do tecido, que se `HealthReportSendInterval` ajustem para 30 segundos. Esta definição garante um ótimo desempenho devido ao loteamento. Para relatórios críticos que devem ser enviados o mais rapidamente possível, utilize `HealthReportSendOptions` imediatamente `true` em [FabricClient.HealthClient.ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Os relatórios imediatos contornam o intervalo de loteamento. Use esta bandeira com cuidado; queremos aproveitar o lote de clientes de saúde sempre que possível. O envio imediato também é útil quando o cliente de tecido está a fechar (por exemplo, o processo determinou o estado inválido e precisa de ser desligado para evitar efeitos secundários). Garante o melhor envio dos relatórios acumulados. Quando um relatório é adicionado com bandeira imediata, o cliente de saúde lota todos os relatórios acumulados desde o último envio.

Os mesmos parâmetros podem ser especificados quando uma ligação a um cluster é criada através do PowerShell. O exemplo a seguir inicia uma ligação a um cluster local:

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

À semelhança da API, os relatórios podem ser enviados utilizando `-Immediate` o switch para serem enviados imediatamente, independentemente do `HealthReportSendInterval` valor.

Para rest, os relatórios são enviados para o gateway de Tecido de Serviço, que tem um cliente de tecido interno. Por predefinição, este cliente está configurado para enviar relatórios lotados a cada 30 segundos. Pode alterar o intervalo de lote com a definição de configuração do cluster `HttpGatewayHealthReportSendInterval` `HttpGateway` em . Como mencionado, uma melhor opção é enviar os relatórios com `Immediate` a verdade. 

> [!NOTE]
> Para garantir que os serviços não autorizados não podem reportar saúde contra as entidades do cluster, configure o servidor para aceitar pedidos apenas de clientes seguros. Os `FabricClient` utilizados para a denúncia devem ter a segurança habilitada a comunicar com o cluster (por exemplo, com Kerberos ou autenticação de certificados). Leia mais sobre [a segurança do cluster.](service-fabric-cluster-security.md)
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Relatório de serviços de baixo privilégio
Se os serviços de Service Fabric não tiverem acesso administrativo ao cluster, pode reportar saúde a entidades a partir do contexto atual através `Partition` ou `CodePackageActivationContext` .

* Para serviços apátridas, utilize [iStatelessServicePartition.ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) para reportar sobre a instância de serviço atual.
* Para serviços estatais, utilize [iStatefulServicePartition.ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) para reportar sobre a réplica atual.
* Use [iServicePartition.ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) para reportar sobre a entidade de partição atual.
* Utilize [codePackageActivationContext.ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) para reportar sobre a aplicação atual.
* Utilize [codePackageActivationContext.ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) para reportar sobre a aplicação atual implementada no nó atual.
* Utilize [codepackageActivationContext.ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) para reportar um pacote de serviço para a aplicação implantada no nó atual.

> [!NOTE]
> Internamente, o `Partition` e o `CodePackageActivationContext` porão um cliente de saúde configurado com definições padrão. Como explicado para o [cliente de saúde,](service-fabric-report-health.md#health-client)os relatórios são lotados e enviados em temporizador. Os objetos devem ser mantidos vivos para poderem enviar o relatório.
> 
> 

Pode especificar `HealthReportSendOptions` ao enviar relatórios `Partition` e `CodePackageActivationContext` APIs de saúde. Se tiver relatórios críticos que devem ser enviados o mais rapidamente possível, `HealthReportSendOptions` utilize-o com imediato `true` . Relatórios imediatos contornam o intervalo de loteamento do cliente de saúde interna. Como mencionado anteriormente, use esta bandeira com cuidado; queremos aproveitar o lote de clientes de saúde sempre que possível.

## <a name="design-health-reporting"></a>Relatório de saúde de design
O primeiro passo para gerar relatórios de alta qualidade é identificar as condições que podem afetar a saúde do serviço. Qualquer condição que possa ajudar a sinalizar problemas no serviço ou aglomerado quando começa- ou ainda melhor, antes que um problema aconteça - pode potencialmente poupar biliões de dólares. Os benefícios incluem menos tempo de inacomprência, menos horas noturnas passadas a investigar e reparar problemas, e maior satisfação do cliente.

Uma vez identificadas as condições, os escritores de cães de guarda precisam descobrir a melhor maneira de monitorizá-los para equilibrar entre a sobrecarga e a utilidade. Por exemplo, considere um serviço que faça cálculos complexos que usam alguns ficheiros temporários numa partilha. Um cão de guarda poderia monitorizar a partilha para garantir que há espaço suficiente disponível. Poderia ouvir notificações de ficheiros ou alterações de diretório. Pode reportar um aviso se for atingido um limiar inicial e denunciar um erro se a parte estiver completa. Com um aviso, um sistema de reparação pode começar a limpar ficheiros mais antigos sobre a partilha. Num erro, um sistema de reparação pode mover a réplica de serviço para outro nó. Note como a condição diz ser descrita em termos de saúde: o estado da condição que pode ser considerado saudável (ok) ou insalubre (aviso ou erro).

Uma vez definidos os detalhes da monitorização, um escritor de cães de guarda precisa descobrir como implementar o cão de guarda. Se as condições puderem ser determinadas a partir do serviço, o cão de guarda pode fazer parte do próprio serviço monitorizado. Por exemplo, o código de serviço pode verificar o uso da partilha e, em seguida, reportar cada vez que tenta escrever um ficheiro. A vantagem desta abordagem é que a comunicação é simples. Deve-se ter cuidado para evitar que os insetos de vigilância impactem a funcionalidade do serviço.

Reportar a partir do serviço monitorizado nem sempre é uma opção. Um cão de guarda dentro do serviço pode não ser capaz de detetar as condições. Pode não ter a lógica ou os dados para fazer a determinação. A sobrecarga de monitorização das condições pode ser elevada. As condições também podem não ser específicas de um serviço, mas sim afetar as interações entre serviços. Outra opção é ter cães de guarda no cluster como processos separados. Os cães de guarda monitorizam as condições e reportam, sem afetar de forma alguma os principais serviços. Por exemplo, estes cães de guarda poderiam ser implementados como serviços apátridas na mesma aplicação, implantados em todos os nós ou nos mesmos nós que o serviço.

Às vezes, um cão de guarda correndo no aglomerado também não é uma opção. Se a condição monitorizada for a disponibilidade ou funcionalidade do serviço como os utilizadores o vêem, o melhor é ter os cães de guarda no mesmo local que os clientes do utilizador. Lá, podem testar as operações da mesma forma que os utilizadores as chamam. Por exemplo, você pode ter um cão de guarda que vive fora do cluster, emite pedidos ao serviço, e verifica a latência e a correção do resultado. (Para um serviço de calculadora, por exemplo, o retorno 2+2 4 num espaço de tempo razoável?)

Uma vez finalizados os detalhes do cão de guarda, você deve decidir sobre uma identificação de fonte que o identifique exclusivamente. Se vários cães de guarda do mesmo tipo estiverem a viver no cluster, devem reportar-se sobre diferentes entidades, ou, se reportarem sobre a mesma entidade, utilizarem diferentes identificações de origem ou propriedade. Desta forma, os seus relatórios podem coexistir. A propriedade do relatório de saúde deve capturar a condição monitorizada. (Para o exemplo acima, a propriedade pode ser **ShareSize**.) Se vários relatórios se aplicarem à mesma condição, a propriedade deve conter alguma informação dinâmica que permita que os relatórios coexistam. Por exemplo, se várias ações precisarem de ser monitorizadas, o nome da propriedade pode ser **ShareSize-sharename**.

> [!NOTE]
> *Não* utilize a loja de saúde para guardar informações sobre o estado. Apenas as informações relacionadas com a saúde devem ser reportadas como saúde, uma vez que esta informação tem impacto na avaliação de saúde de uma entidade. A loja de saúde não foi concebida como uma loja de uso geral. Usa a lógica de avaliação da saúde para agregar todos os dados no estado de saúde. O envio de informações não relacionadas com a saúde (como o estado de reporte com um estado de saúde de OK) não afeta o estado de saúde agregado, mas pode afetar negativamente o desempenho da loja de saúde.
> 
> 

O próximo ponto de decisão é sobre qual entidade deve reportar. Na maior parte do tempo, a condição identifica claramente a entidade. Escolha a entidade com a melhor granularidade possível. Se uma condição impactar todas as réplicas numa partição, informe sobre a partição, não no serviço. Há casos de canto onde mais pensamento é necessário, no entanto. Se a condição impacta uma entidade, como uma réplica, mas o desejo é ter a condição sinalizada por mais do que a duração da vida útil da réplica, então deve ser reportada na partição. Caso contrário, quando a réplica é eliminada, a loja de saúde limpa todos os seus relatórios. Os escritores de cães de guarda devem pensar sobre a vida da entidade e o relatório. Deve ficar claro quando um relatório deve ser limpo de uma loja (por exemplo, quando um erro reportado sobre uma entidade já não se aplica).

Vejamos um exemplo que reúne os pontos que descrevi. Considere uma aplicação de Tecido de Serviço composta por um serviço magistral estatal e serviços apátridas secundários implantados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O mestre tem uma fila de processamento que contém comandos para serem executados por secundários. Os secundários executam os pedidos de entrada e enviam sinais de reconhecimento. Uma condição que poderia ser monitorizada é o comprimento da fila de processamento principal. Se o comprimento da fila principal atingir um limiar, é comunicado um aviso. O aviso indica que os secundários não aguentam a carga. Se a fila atingir o comprimento máximo e os comandos forem retirados, é reportado um erro, uma vez que o serviço não pode recuperar. Os relatórios podem estar na propriedade **QueueStatus.** O cão de guarda vive dentro do serviço, e é enviado periodicamente na réplica primária principal. O tempo de vida é de dois minutos, e é enviado periodicamente a cada 30 segundos. Se a primária cair, o relatório é limpo automaticamente da loja. Se a réplica do serviço estiver em cima, mas estiver num impasse ou se tiver outros problemas, o relatório expira na loja de saúde. Neste caso, a entidade é avaliada por erro.

Outra condição que pode ser monitorizada é o tempo de execução da tarefa. O mestre distribui tarefas para os secundários com base no tipo de tarefa. Dependendo do desenho, o mestre poderia sondar os secundários para o estatuto de tarefa. Também pode esperar que os secundários enviem sinais de reconhecimento quando terminarem. No segundo caso, deve ser tomado cuidado para detetar situações em que os secundários morrem ou as mensagens são perdidas. Uma opção é o mestre enviar um pedido de ping para o mesmo secundário, que devolve o seu estatuto. Se não for recebido nenhum estatuto, o mestre considera-o uma falha e reagenda a tarefa. Este comportamento pressupõe que as tarefas são idempotentes.

A condição monitorizada pode ser traduzida como um aviso se a tarefa não for feita num determinado momento **(t1**, por exemplo 10 minutos). Se a tarefa não estiver concluída a tempo **(t2**, por exemplo 20 minutos), a condição monitorizada pode ser traduzida como Error. Este relatório pode ser feito de várias formas:

* A réplica primária principal principal reporta-se a si mesma periodicamente. Você pode ter uma propriedade para todas as tarefas pendentes na fila. Se pelo menos uma tarefa demorar mais tempo, o estado do relatório sobre a propriedade **Pendentes** é um aviso ou erro, conforme apropriado. Se não houver tarefas pendentes ou todas as tarefas iniciadas, o estado do relatório está ok. As tarefas são persistentes. Se as primárias descerem, as primárias recém-promovidas podem continuar a reportar corretamente.
* Outro processo de cão de guarda (na nuvem ou no exterior) verifica as tarefas (do exterior, com base no resultado da tarefa pretendido) para ver se estão concluídas. Se não respeitarem os limiares, é enviado um relatório sobre o serviço principal. É também enviado um relatório sobre cada tarefa que inclui o identificador de **tarefas, como o PendingTask+taskId**. Os relatórios só devem ser enviados em estados pouco saudáveis. Desagure a hora de viver até alguns minutos e marque os relatórios a serem removidos quando expirarem para garantir a limpeza.
* O secundário que está a executar um relatório de tarefa quando demora mais do que o esperado para executá-lo. Informa sobre a instância de serviço na propriedade **PendenteTasks**. O relatório aponta a instância de serviço que tem problemas, mas não capta a situação em que o caso morre. Os relatórios são limpos então. Pode reportar sobre o serviço secundário. Se o secundário completar a tarefa, a instância secundária limpa o relatório da loja. O relatório não capta a situação em que a mensagem de reconhecimento é perdida e a tarefa não está terminada do ponto de vista do mestre.

No entanto, o relatório é feito nos casos acima descritos, os relatórios são capturados na saúde da aplicação quando a saúde é avaliada.

## <a name="report-periodically-vs-on-transition"></a>Relatório periodicamente vs. em transição
Ao utilizar o modelo de relatórios de saúde, os cães de guarda podem enviar relatórios periodicamente ou em transições. A forma recomendada de reportar cães de guarda é periodicamente, porque o código é muito mais simples e menos propenso a erros. Os cães de guarda devem esforçar-se para ser o mais simples possível para evitar erros que desencadeiem relatórios incorretos. Relatórios *insalubres* incorretos têm impacto nas avaliações e cenários de saúde baseados na saúde, incluindo upgrades. Relatórios *saudáveis* incorretos escondem problemas no cluster, o que não é desejado.

Para relatórios periódicos, o cão de guarda pode ser implementado com um temporizador. Numa chamada de temporizador, o cão de guarda pode verificar o estado e enviar um relatório baseado no estado atual. Não há necessidade de ver que relatório foi enviado anteriormente ou fazer quaisquer otimizações em termos de mensagens. O cliente de saúde tem lógica de loting para ajudar no desempenho. Enquanto o cliente de saúde é mantido vivo, ele retrifica internamente até que o relatório seja reconhecido pela loja de saúde ou o cão de guarda gera um relatório mais recente com a mesma entidade, propriedade e fonte.

Relatar sobre transições requer uma manipulação cuidadosa do Estado. O cão de guarda monitoriza algumas condições e relatórios apenas quando as condições mudam. O lado positivo desta abordagem é que são necessários menos relatórios. A desvantagem é que a lógica do cão de guarda é complexa. O cão de guarda deve manter as condições ou os relatórios, para que possam ser inspecionados para determinar as alterações do estado. No que diz n adiantado, há que ter cuidado com os relatórios adicionados, mas ainda não enviados para a loja de saúde. O número da sequência deve estar a aumentar. Caso contrário, os relatórios são rejeitados como infalimentos. Nos casos raros em que a perda de dados é incorrida, pode ser necessária sincronização entre o estado do repórter e o estado da loja de saúde.

Relatar sobre transições faz sentido para os serviços que reportam sobre si mesmos, através `Partition` ou `CodePackageActivationContext` . Quando o objeto local (pacote de serviço replicado ou implantado / aplicação implantada) é removido, todos os seus relatórios também são removidos. Esta limpeza automática relaxa a necessidade de sincronização entre repórter e loja de saúde. Se o relatório for para a partilha dos pais ou para a aplicação dos pais, há que ter cuidado para evitar relatórios insuíduos na loja de saúde. A lógica deve ser adicionada para manter o estado correto e limpar o relatório da loja quando já não é necessário.

## <a name="implement-health-reporting"></a>Implementar relatórios de saúde
Uma vez que a entidade e os detalhes do relatório são claros, o envio de relatórios de saúde pode ser feito através da API, PowerShell ou REST.

### <a name="api"></a>API
Para reportar através da API, é necessário criar um relatório de saúde específico do tipo de entidade que pretende reportar. Entregue o relatório a um cliente de saúde. Em alternativa, crie uma informação de saúde e passe-a para corrigir métodos de reporte sobre `Partition` ou reportar sobre as `CodePackageActivationContext` entidades atuais.

O exemplo a seguir mostra relatórios periódicos de um cão de guarda dentro do cluster. O cão de guarda verifica se um recurso externo pode ser acedido a partir de um nó. O recurso é necessário por um manifesto de serviço dentro da aplicação. Se o recurso não estiver disponível, os outros serviços dentro da aplicação ainda podem funcionar corretamente. Portanto, o relatório é enviado sobre a entidade do pacote de serviço implantado a cada 30 segundos.

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
Envie relatórios de saúde com **o Send-ServiceFabric *EntityType* HealthReport**.

O exemplo a seguir mostra relatórios periódicos sobre os valores da CPU num nó. Os relatórios devem ser enviados a cada 30 segundos, e eles têm um tempo para viver de dois minutos. Se expirarem, o repórter tem problemas, por isso o nó é avaliado por erro. Quando a CPU está acima de um limiar, o relatório tem um estado de alerta sanitário. Quando o CPU permanece acima de um limiar por mais do que o tempo configurado, é reportado como um erro. Caso contrário, o repórter envia um estado de saúde ok.

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

O exemplo a seguir relata um aviso transitório numa réplica. Primeiro recebe o ID de partição e depois a réplica iD para o serviço em que está interessado. Em seguida, envia um relatório da **PowershellWatcher** sobre a propriedade **ResourceDependency**. O relatório é de interesse por apenas dois minutos, e é removido automaticamente da loja.

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
Envie relatórios de saúde usando REST com pedidos POST que vão para a entidade desejada e têm no organismo a descrição do relatório de saúde. Por exemplo, ver como enviar relatórios de [saúde](/rest/api/servicefabric/report-the-health-of-a-cluster) do cluster REST ou [relatórios de saúde do serviço](/rest/api/servicefabric/report-the-health-of-a-service). Todas as entidades são apoiadas.

## <a name="next-steps"></a>Passos seguintes
Com base nos dados de saúde, os escritores de serviços e os administradores de cluster/aplicação podem pensar em formas de consumir a informação. Por exemplo, podem estabelecer alertas baseados no estado de saúde para apanhar problemas graves antes de provocarem interrupções. Os administradores também podem configurar sistemas de reparação para corrigir problemas automaticamente.

[Introdução à monitorização da saúde do tecido de serviço](service-fabric-health-introduction.md)

[Ver relatórios de saúde do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Como reportar e verificar a saúde do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Utilize relatórios de saúde do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação do Tecido de Serviço](service-fabric-application-upgrade.md)
