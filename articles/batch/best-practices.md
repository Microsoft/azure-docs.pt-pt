---
title: Práticas recomendadas-lote do Azure
description: Conheça as práticas recomendadas e dicas úteis para desenvolver sua solução de lote do Azure.
author: laurenhughes
ms.author: lahugh
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: gwallace
ms.openlocfilehash: 19c5b6acaeddb915af49cf62a884da0678075f15
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535668"
---
# <a name="azure-batch-best-practices"></a>Práticas recomendadas do lote do Azure

Este artigo aborda uma coleção de práticas recomendadas para usar o serviço de lote do Azure com eficiência e eficiência. Essas práticas recomendadas derivam de nossa experiência com o lote e as experiências de clientes do lote. É importante entender este artigo para evitar armadilhas de design, possíveis problemas de desempenho e antipadrões ao desenvolver para e usar o lote.

Neste artigo, você aprenderá:

> [!div class="checklist"]
> - Quais são as práticas recomendadas
> - Por que você deve usar as práticas recomendadas
> - O que pode acontecer se você não seguir as práticas recomendadas
> - Como seguir as práticas recomendadas

## <a name="pools"></a>Conjuntos

Os pools do lote são os recursos de computação para executar trabalhos no serviço de lote. As seções a seguir fornecem orientação sobre as principais práticas recomendadas a serem seguidas ao trabalhar com pools do lote.

### <a name="pool-configuration-and-naming"></a>Configuração e nomenclatura de pool

- **Modo de alocação de conjuntos**  
    Ao criar uma conta do lote, você pode escolher entre dois modos de alocação de pool: **serviço de lote** ou **assinatura de usuário**. Na maioria dos casos, você deve usar o modo de serviço de lote padrão, no qual os pools são alocados em segundo plano em assinaturas gerenciadas em lote. No modo de subscrição de utilizador alternativo, as VMs do Batch e outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. As contas de assinatura de usuário são usadas principalmente para habilitar um subconjunto importante, mas pequeno, de cenários. Você pode ler mais sobre o modo de assinatura [do usuário em configuração adicional para o modo de assinatura do usuário](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Considere o tempo de execução do trabalho e da tarefa ao determinar o mapeamento do trabalho para o pool.**  
    Se você tiver trabalhos compostos principalmente de tarefas de execução curta, e as contagens de tarefas totais esperadas forem pequenas, para que o tempo de execução esperado geral do trabalho não seja longo, não aloque um novo pool para cada trabalho. O tempo de alocação dos nós reduzirá o tempo de execução do trabalho.

- **Os pools devem ter mais de um nó de computação.**  
    Não há garantia de que os nós individuais estejam sempre disponíveis. Embora não sejam comuns, falhas de hardware, atualizações de sistema operacional e um host de outros problemas podem fazer com que nós individuais estejam offline. Se a carga de trabalho do lote exigir um progresso determinístico e garantido, você deverá alocar pools com vários nós.

- **Não reutilize nomes de recursos.**  
    Os recursos do lote (trabalhos, pools, etc.) geralmente são fornecidos e passam ao longo do tempo. Por exemplo, você pode criar um pool na segunda-feira, excluí-lo na terça-feira e, em seguida, criar outro pool na quinta-feira. Cada novo recurso que você criar deve receber um nome exclusivo que você não usou antes. Isso pode ser feito usando um GUID (como o nome do recurso inteiro ou como parte dele) ou inserindo a hora em que o recurso foi criado no nome do recurso. O lote dá suporte a [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), que pode ser usado para dar a um recurso um nome legível por humanos, mesmo que a ID de recurso real seja algo que seja amigável para o humano. Usar nomes exclusivos facilita a diferenciação de qual recurso em particular fez algo em logs e métricas. Ela também removerá a ambiguidade se você precisar arquivar um caso de suporte para um recurso.

- **Continuidade durante a manutenção e a falha do pool.**  
    É melhor fazer com que seus trabalhos usem pools dinamicamente. Se seus trabalhos usarem o mesmo pool para tudo, haverá a chance de que seus trabalhos não sejam executados se algo der errado com o pool. Isso é especialmente importante para cargas de trabalho com detecção de hora. Para corrigir isso, selecione ou crie um pool dinamicamente ao agendar cada trabalho ou tenha uma maneira de substituir o nome do pool para que você possa ignorar um pool não íntegro.

- **Continuidade dos negócios durante a manutenção e a falha do pool**  
    Há muitas causas possíveis que podem impedir que um pool aumente para o tamanho necessário que você deseja, como erros internos, restrições de capacidade, etc. Por esse motivo, você deve estar pronto para redirecionar trabalhos em um pool diferente (possivelmente com um tamanho de VM diferente-o lote dá suporte a isso via [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)), se necessário. Evite usar uma ID de pool estático com a expectativa de que ela nunca seja excluída e nunca seja alterada.

### <a name="pool-lifetime-and-billing"></a>Tempo de vida e cobrança do pool

O tempo de vida do pool pode variar dependendo do método de alocação e das opções aplicadas à configuração do pool. Os pools podem ter um tempo de vida arbitrário e um número variável de nós de computação no pool a qualquer momento. É sua responsabilidade gerenciar os nós de computação no pool explicitamente ou por meio de recursos fornecidos pelo serviço (autoescala ou autopool).

- **Mantenha os pools atualizados.**  
    Você deve redimensionar seus pools para zero a cada poucos meses para garantir que você obtenha as atualizações e correções de bug mais recentes do agente de nó. Seu pool não receberá atualizações do agente de nó a menos que seja recriado ou redimensionado para 0 nós de computação. Antes de recriar ou redimensionar o pool, é recomendável baixar todos os logs do agente de nó para fins de depuração, conforme discutido na seção [nós](#nodes) .

- **Recriação de pool**  
    Em uma observação semelhante, não é recomendável excluir e recriar seus pools diariamente. Em vez disso, crie um novo pool, atualize seus trabalhos existentes para apontar para o novo pool. Depois que todas as tarefas tiverem sido movidas para o novo pool, exclua o pool antigo.

- **Eficiência e cobrança do pool**  
    O próprio lote incorre em encargos extras, mas você não tem cobranças para os recursos de computação usados. Você será cobrado por cada nó de computação no pool, independentemente do estado em que ele está. Isso inclui quaisquer encargos necessários para que o nó seja executado, como os custos de armazenamento e rede. Para saber mais sobre as práticas recomendadas, consulte [análise de custo e orçamentos para o lote do Azure](budget.md).

### <a name="pool-allocation-failures"></a>Falhas de alocação de pool

As falhas de alocação de pool podem ocorrer a qualquer momento durante a primeira alocação ou redimensionamentos subsequentes. Isso pode ser devido à esgotamento de capacidade temporária em uma região ou falhas em outros serviços do Azure nos quais o lote depende. Sua cota principal não é uma garantia, mas sim um limite.

### <a name="unplanned-downtime"></a>Tempo de inatividade não planejado

É possível que os pools do lote tenham eventos de tempo de inatividade no Azure. Isso é importante para ter em mente ao planejar e desenvolver seu cenário ou fluxo de trabalho para o lote.

No caso de um nó falhar, o lote tentará recuperar automaticamente esses nós de computação em seu nome. Isso pode disparar a reagendamento de qualquer tarefa em execução no nó recuperado. Consulte [projetando para novas tentativas](#designing-for-retries-and-re-execution) para saber mais sobre as tarefas interrompidas.

- **Dependência da região do Azure**  
    É aconselhável não depender de uma única região do Azure se você tiver uma carga de trabalho de produção ou de tempo. Embora seja raro, há problemas que podem afetar toda a região. Por exemplo, se o processamento precisar ser iniciado em um momento específico, considere escalar verticalmente o pool em sua região primária *bem antes da hora de início*. Se a escala do pool falhar, você poderá fazer fallback para escalar verticalmente um pool em uma região (ou regiões) de backup. Os pools em várias contas em regiões diferentes fornecem um backup pronto e facilmente acessível se algo der errado com outro pool. Para obter mais informações, consulte [criar seu aplicativo para alta disponibilidade](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Tarefas

Um trabalho é um contêiner projetado para conter centenas, milhares ou até mesmo milhões de tarefas.

- **Colocar muitas tarefas em um trabalho**  
    O uso de um trabalho para executar uma única tarefa é ineficiente. Por exemplo, é mais eficiente usar um único trabalho contendo 1000 tarefas em vez de criar trabalhos 100 que contenham 10 tarefas cada. A execução de 1000 trabalhos, cada um com uma única tarefa, seria a abordagem menos eficiente, mais lenta e mais cara a ser tomada.  

    Não projete uma solução em lote que exija milhares de trabalhos ativos simultaneamente. Não há nenhuma cota para tarefas, portanto, executar tantas tarefas no menor número de trabalhos possível usa com eficiência suas [cotas de trabalho e agenda de trabalho](batch-quota-limit.md#resource-quotas).

- **Vida útil do trabalho**  
    Um trabalho em lotes tem um tempo de vida indefinido até que seja excluído do sistema. O estado de um trabalho designa se ele pode aceitar mais tarefas para agendamento ou não. Um trabalho não é movido automaticamente para o estado concluído, a menos que seja explicitamente encerrado. Isso pode ser disparado automaticamente por meio da propriedade [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) ou [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Há um [trabalho ativo padrão e uma cota de agendamento de trabalho](batch-quota-limit.md#resource-quotas). Trabalhos e agendas de trabalho no estado concluído não contam para esta cota.

## <a name="tasks"></a>Tarefas

As tarefas são unidades individuais de trabalho que compõem um trabalho. As tarefas são enviadas pelo usuário e agendadas pelo lote para nós de computação. Há várias considerações de design a serem feitas ao criar e executar tarefas. As seções a seguir explicam cenários comuns e como projetar suas tarefas para lidar com problemas e executar com eficiência.

- **Salve os dados da tarefa como parte da tarefa.**  
    Nós de computação são por sua natureza efêmera. Há muitos recursos no lote, como o autopool e o dimensionamento automático, que facilitam o desdesaparecimento dos nós. Quando os nós deixam o pool (devido a um redimensionamento ou a uma exclusão de pool), todos os arquivos nesses nós também são excluídos. Por isso, é recomendável que, antes de uma tarefa ser concluída, ele mova a saída do nó em que está sendo executado e para um repositório durável, da mesma forma, se uma tarefa falhar, deverá mover os logs necessários para diagnosticar a falha em um repositório durável. O lote integrou suporte ao armazenamento do Azure para carregar dados via [OutputFiles](batch-task-output-files.md), bem como uma variedade de sistemas de arquivos compartilhados, ou você pode executar o upload por conta própria em suas tarefas.

### <a name="task-lifetime"></a>Tempo de vida da tarefa

- **Exclua as tarefas quando elas estiverem concluídas.**  
    Exclua tarefas quando elas não forem mais necessárias ou defina uma restrição de tarefa de [retençãotime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) . Se um `retentionTime` estiver definido, o lote limpará automaticamente o espaço em disco usado pela tarefa quando o `retentionTime` expirar.  

    A exclusão de tarefas realiza duas coisas. Ele garante que você não tenha uma compilação de tarefas no trabalho, fazendo consultas/encontrando a tarefa de que você está interessado com mais dificuldade (porque você terá que filtrar as tarefas concluídas). Ele também limpa os dados de tarefa correspondentes no nó (desde que o `retentionTime` ainda não tenha sido atingido). Isso garante que os nós não sejam preenchidos com os dados da tarefa e fique sem espaço em disco.

### <a name="task-submission"></a>Envio de tarefa

- **Envie um grande número de tarefas em uma coleção.**  
    As tarefas podem ser enviadas em uma base individual ou em coleções. Envie tarefas em [coleções](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) de até 100 de uma vez ao realizar o envio em massa de tarefas para reduzir a sobrecarga e o tempo de envio.

### <a name="task-execution"></a>Execução da tarefa

- **Escolhendo o máximo de tarefas por nó**  
    O lote dá suporte a tarefas de sobrecarregar em nós (executando mais tarefas do que um nó tem núcleos). Cabe a você garantir que suas tarefas se ajustem aos nós no pool. Por exemplo, você pode ter uma experiência degradada se tentar agendar oito tarefas, cada uma consumindo 25% de uso da CPU em um nó (em um pool com `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Design para repetições e reexecução

As tarefas podem ser repetidas automaticamente pelo lote. Há dois tipos de tentativas: controlado pelo usuário e interno. As repetições controladas pelo usuário são especificadas pelo [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)da tarefa. Quando um programa especificado na tarefa é encerrado com um código de saída diferente de zero, a tarefa é repetida até o valor da `maxTaskRetryCount`.

Embora seja raro, uma tarefa pode ser repetida internamente devido a falhas no nó de computação, como não ser capaz de atualizar o estado interno ou uma falha no nó enquanto a tarefa está em execução. A tarefa será repetida no mesmo nó de computação, se possível, até um limite interno antes de desistir da tarefa e adiar a tarefa a ser reagendada pelo lote, potencialmente em um nó de computação diferente.

- **Tarefas duráveis de compilação**  
    As tarefas devem ser projetadas para resistir à falha e acomodar novas tentativas. Isso é especialmente importante para tarefas de longa execução. Para fazer isso, verifique se as tarefas geram o mesmo resultado, mesmo que elas sejam executadas mais de uma vez. Uma maneira de conseguir isso é fazer suas tarefas "atingir metas". Outra maneira é garantir que suas tarefas sejam idempotentes (as tarefas terão o mesmo resultado, independentemente de quantas vezes elas forem executadas).

    Um exemplo comum é uma tarefa para copiar arquivos para um nó de computação. Uma abordagem simples é uma tarefa que copia todos os arquivos especificados toda vez que ele é executado, o que é ineficiente e não é criado para resistir a falhas. Em vez disso, crie uma tarefa para garantir que os arquivos estejam no nó de computação; uma tarefa que não copia arquivos que já estão presentes. Dessa forma, a tarefa é escolhida de onde parou se foi interrompida.

- **Nós de baixa prioridade**  
    Não há diferenças de design ao executar suas tarefas em nós dedicados ou de baixa prioridade. Se uma tarefa é antecipada durante a execução em um nó de baixa prioridade ou interrompida devido a uma falha em um nó dedicado, ambas as situações são atenuadas com a criação da tarefa para resistir à falha.

- **Tempo de execução da tarefa**  
    Evite tarefas com tempo de execução curto. As tarefas que só são executadas por um ou dois segundos não são ideais. Você deve tentar fazer uma quantidade significativa de trabalho em uma tarefa individual (no mínimo 10 segundos, indo até horas ou dias). Se cada tarefa estiver em execução por um minuto (ou mais), a sobrecarga de agendamento como uma fração do tempo de computação geral será pequena.

## <a name="nodes"></a>Nós

- **As tarefas iniciais devem ser idempotentes**  
    Semelhante a outras tarefas, a tarefa de início do nó deve ser idempotente, pois ela será executada novamente sempre que o nó for inicializado. Uma tarefa idempotente é simplesmente uma que produz um resultado consistente quando executada várias vezes.

- **Gerencie serviços de longa execução por meio da interface de serviços do sistema operacional.**  
    Às vezes, há a necessidade de executar outro agente junto com o agente do lote no nó, por exemplo, para coletar dados do nó e relatá-los. Recomendamos que esses agentes sejam implantados como serviços do sistema operacional, como um serviço do Windows ou um serviço de `systemd` do Linux.  

    Ao executar esses serviços, eles não devem fazer bloqueios de arquivo em todos os arquivos em diretórios gerenciados em lote no nó, pois, caso contrário, o lote não poderá excluir esses diretórios devido aos bloqueios de arquivo. Por exemplo, se estiver instalando um serviço do Windows em uma tarefa inicial, em vez de iniciar o serviço diretamente do diretório de trabalho Iniciar tarefa, copie os arquivos em outro lugar (se os arquivos existirem apenas ignorar a cópia). Instale o serviço a partir desse local. Quando o lote executa novamente a tarefa inicial, ele excluirá o diretório de trabalho Iniciar tarefa e o criará novamente. Isso funciona porque o serviço tem bloqueios de arquivo no outro diretório, não o diretório de trabalho Iniciar tarefa.

- **Evite criar junções de diretório no Windows**  
    As junções de diretório, às vezes chamadas de links físicos de diretório, são difíceis de lidar durante a limpeza da tarefa e do trabalho. Use symlinks (links Soft) em vez de links físicos.

- **Coletar os logs do agente do lote se houver um problema**  
    Se você notar um problema envolvendo o comportamento de um nó ou de tarefas em execução em um nó, é recomendável coletar os logs do agente do lote antes de desalocar os nós em questão. Os logs do agente do lote podem ser coletados usando a API carregar logs de serviço do lote. Esses logs podem ser fornecidos como parte de um tíquete de suporte à Microsoft e ajudarão no problema de resolução e solução de problemas.

## <a name="security"></a>Segurança

### <a name="security-isolation"></a>Isolamento de segurança

Para fins de isolamento, se o seu cenário exigir o isolamento de trabalhos uns dos outros, você deverá isolar esses trabalhos fazendo com que eles estejam em pools separados. Um pool é o limite de isolamento de segurança no lote e, por padrão, dois pools não são visíveis ou são capazes de se comunicar entre si. Evite usar contas do lote separadas como meio de isolamento.
