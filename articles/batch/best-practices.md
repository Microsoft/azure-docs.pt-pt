---
title: Melhores práticas
description: Aprenda as melhores práticas e dicas úteis para desenvolver a sua solução De Lote Azure.
ms.date: 04/03/2020
ms.topic: article
ms.openlocfilehash: 43a0020953ea44593cf38298a78547194751fc72
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117510"
---
# <a name="azure-batch-best-practices"></a>Boas práticas do Lote Azure

Este artigo discute uma coleção de boas práticas para a utilização do serviço Azure Batch de forma eficaz e eficiente. Estas boas práticas derivam da nossa experiência com o Batch e as experiências dos clientes do Batch. É importante entender este artigo para evitar armadilhas de design, potenciais problemas de desempenho e anti-padrões enquanto desenvolve para, e usando, Batch.

Neste artigo, aprenderá:

> [!div class="checklist"]
> - Quais são as melhores práticas
> - Por que deve usar as melhores práticas
> - O que pode acontecer se não seguir as melhores práticas
> - Como seguir as melhores práticas

## <a name="pools"></a>Conjuntos

Os pools de lotes são os recursos computacionais para a execução de trabalhos no serviço Batch. As seguintes secções fornecem orientações sobre as melhores práticas a seguir ao trabalhar com piscinas de Lote.

### <a name="pool-configuration-and-naming"></a>Configuração e nomeação da piscina

- **Modo de alocação de piscina** Ao criar uma conta Batch, pode escolher entre dois modos de alocação de piscina: **serviço de lote** ou **subscrição do utilizador**. Para a maioria dos casos, deve utilizar o modo de serviço 'Batch' predefinido, no qual os pools são atribuídos nos bastidores em subscrições geridas pelo Batch. No modo de subscrição de utilizador alternativo, as VMs do Batch e outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. As contas de subscrição do utilizador são usadas principalmente para permitir um subconjunto de cenários importante, mas pequeno. Pode ler mais sobre o modo de subscrição do utilizador na [configuração adicional para o modo de subscrição do utilizador](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Considere o tempo de trabalho e de execução de tarefas ao determinar o trabalho para o mapeamento de piscina.**
    Se tiver empregos compostos principalmente por tarefas de curto prazo, e as contagens totais esperadas são pequenas, de modo que o tempo de funcionamento esperado global mente não é longo, não alocar um novo pool para cada trabalho. O tempo de atribuição dos nódosos diminuirá o tempo de execução do trabalho.

- **As piscinas devem ter mais do que um nó de cálculo.**
    Os nódos os nódosos individuais não estão garantidos para estarem sempre disponíveis. Embora incomuns, falhas de hardware, atualizações do sistema operativo e uma série de outros problemas podem fazer com que os nós individuais estejam offline. Se a carga de trabalho do lote requer progressos determinísticos e garantidos, deverá alocar piscinas com vários nós.

- **Não reutilize os nomes dos recursos.**
    Os recursos dos lotes (empregos, piscinas, etc.) muitas vezes vêm e vão ao longo do tempo. Por exemplo, você pode criar uma piscina na segunda-feira, apagá-la na terça-feira, e depois criar outra piscina na quinta-feira. Cada novo recurso que cria deve receber um nome único que nunca usou antes. Isto pode ser feito usando um GUID (como todo o nome do recurso, ou como parte dele) ou incorporando o tempo em que o recurso foi criado no nome do recurso. O lote suporta o [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), que pode ser usado para dar a um recurso um nome legível humano, mesmo que o ID de recurso real seja algo que não seja tão amigável para o ser humano. A utilização de nomes únicos facilita a diferenciação de que recurso específico fez algo em registos e métricas. Também elimina a ambiguidade se alguma vez tiver de apresentar um pedido de apoio para um recurso.

- **Continuidade durante manutenção e falha na piscina.**
    É melhor ter o seu trabalho a usar piscinas dinamicamente. Se os seus empregos usarem a mesma piscina para tudo, há a hipótese de os seus empregos não funcionarem se algo correr mal com a piscina. Isto é especialmente importante para cargas de trabalho sensíveis ao tempo. Para corrigir isto, selecione ou crie uma piscina dinamicamente quando agenda rindo cada trabalho, ou tenha uma maneira de anular o nome da piscina para que possa contornar uma piscina pouco saudável.

- **Continuidade do negócio durante manutenção e falha na piscina** Existem muitas causas possíveis que podem impedir que uma piscina cresça até ao tamanho exigido, tais como erros internos, restrições de capacidade, etc. Por esta razão, deve estar pronto para redirecionar postos de trabalho numa piscina diferente (possivelmente com um tamanho VM diferente - O lote suporta-o através do [UpdateJob),](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)se necessário. Evite utilizar um ID de piscina estático com a expectativa de que nunca será eliminado e nunca mudará.

### <a name="pool-lifetime-and-billing"></a>Vida útil da piscina e faturação

O tempo de vida útil da piscina pode variar dependendo do método de alocação e opções aplicadas à configuração do pool. As piscinas podem ter uma vida arbitrária e um número variado de nós computacionais na piscina a qualquer momento. É da sua responsabilidade gerir os nós computacionais na piscina, quer explicitamente, quer através de funcionalidades fornecidas pelo serviço (escala automática ou autopool).

- **Mantenha as piscinas frescas.**
    Deve redimensionar as suas piscinas a zero a cada poucos meses para garantir que obtém as atualizações mais recentes do agente do nó e as correções de bugs. A sua piscina não receberá atualizações de agente de nó a menos que seja recriada ou redimensionada para 0 nós de cálculo. Antes de recriar ou redimensionar a sua piscina, é aconselhável descarregar quaisquer registos de agentes de nó para fins de depuração, como discutido na secção [Nós.](#nodes)

- **Recriação da piscina** Numa nota semelhante, não é recomendado apagar e recriar as suas piscinas diariamente. Em vez disso, crie uma nova piscina, atualize os seus empregos existentes para apontar para a nova piscina. Uma vez que todas as tarefas tenham sido transferidas para a nova piscina, então apague a piscina antiga.

- Eficiência da **piscina e faturação** O lote em si não incorre em encargos adicionais, mas incorre em encargos para os recursos computacionais utilizados. Você é cobrado por cada nó de computação na piscina, independentemente do estado em que está. Isto inclui quaisquer encargos necessários para que o nó seja executado, tais como custos de armazenamento e networking. Para aprender mais boas práticas, consulte análise de [custos e orçamentos para o Lote Azure.](budget.md)

### <a name="pool-allocation-failures"></a>Falhas na atribuição de piscinas

As falhas de atribuição de piscinas podem ocorrer em qualquer ponto durante a primeira atribuição ou redimensionações subsequentes. Isto pode ser devido à exaustão temporária da capacidade numa região ou falhas noutros serviços Azure em que o Batch depende. A sua quota principal não é uma garantia, mas sim um limite.

### <a name="unplanned-downtime"></a>Período de indisponibilidade não planeado

É possível que as piscinas de Batch experimentem eventos de inatividade em Azure. Isto é importante ter em mente ao planear e desenvolver o seu cenário ou fluxo de trabalho para o Lote.

No caso de um nó falhar, o Batch tenta automaticamente recuperar estes nós computacionais em seu nome. Isto pode desencadear o reagendamento de qualquer tarefa de execução no nó recuperado. Consulte [o Design para retentar](#designing-for-retries-and-re-execution) para saber mais sobre tarefas interrompidas.

- **Dependência da região de Azure** É aconselhável não depender de uma única região azure se tiver uma carga de trabalho sensível ao tempo ou à produção. Embora raros, há questões que podem afetar toda uma região. Por exemplo, se o seu processamento precisar de começar num momento específico, considere escalar a piscina na sua região primária muito antes da hora de *início.* Se essa balança de piscina falhar, pode voltar a escalar uma piscina numa região de reserva (ou regiões). Piscinas em várias contas em diferentes regiões fornecem uma cópia de segurança pronta e facilmente acessível se algo correr mal com outra piscina. Para mais informações, consulte [Design a sua aplicação para alta disponibilidade](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Tarefas

Um trabalho é um recipiente projetado para conter centenas, milhares, ou mesmo milhões de tarefas.

- **Colocar muitas tarefas num trabalho** Usar um trabalho para executar uma única tarefa é ineficiente. Por exemplo, é mais eficiente usar um único trabalho contendo 1000 tarefas em vez de criar 100 postos de trabalho que contêm 10 tarefas cada. Gerir 1000 postos de trabalho, cada um com uma única tarefa, seria a abordagem menos eficiente, lenta e mais cara a tomar.

    Não desenhe uma solução de Lote que exija milhares simultaneamente de empregos ativos. Não existe quota para tarefas, pelo que executar o maior número possível de tarefas no âmbito do menor número de postos de trabalho utiliza eficazmente as quotas de emprego e de horário de [trabalho.](batch-quota-limit.md#resource-quotas)

- **Vida útil** Um trabalho de Lote tem uma vida indefinida até ser apagado do sistema. O estado de um trabalho designa se pode aceitar mais tarefas para agendar ou não. Um trabalho não se move automaticamente para o estado concluído a menos que explicitamente terminado. Isto pode ser automaticamente acionado através da propriedade [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) ou [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Existe uma quota de emprego ativo e de horário de [trabalho.](batch-quota-limit.md#resource-quotas) Os postos de trabalho e os horários de trabalho em estado concluído não contam para esta quota.

## <a name="tasks"></a>Tarefas

As tarefas são unidades individuais de trabalho que compõem um trabalho. As tarefas são submetidas pelo utilizador e programadas pelo Batch para calcular os nódosos. Existem várias considerações de design a fazer ao criar e executar tarefas. As seguintes secções explicam cenários comuns e como projetar as suas tarefas para lidar com problemas e executar de forma eficiente.

- **Guarde os dados de tarefas como parte da tarefa.**
    Os nóóis computacionais são pela sua natureza efémera. Existem muitas funcionalidades em Lote, como autopool e autoescala que facilitam o desaparecimento dos nódosos. Quando os nós saem da piscina (devido a um redimensionado, ou uma eliminação de piscina) todos os ficheiros desses nós também são eliminados. Por isso, recomenda-se que antes de uma tarefa se complete, ele move a sua saída para fora do nó que está a funcionar e para uma loja durável, da mesma forma que se uma tarefa falha, deve mover os registos necessários para diagnosticar a falha numa loja durável. O Batch tem suporte integrado Azure Storage para carregar dados através [do OutputFiles,](batch-task-output-files.md)bem como uma variedade de sistemas de ficheiros partilhados, ou pode realizar o upload nas suas tarefas.

### <a name="task-lifetime"></a>Vida útil da tarefa

- **Elimine as tarefas quando estiverem completas.**
    Eliminar tarefas quando já não forem necessárias ou definir uma restrição de tarefa de [retençãoTempo.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) Se `retentionTime` estiver definido, o Lote limpa automaticamente o espaço do `retentionTime` disco utilizado pela tarefa quando a tarefa expirar.

    Apagar tarefas realiza duas coisas. Garante que não tem uma acumulação de tarefas no trabalho, tornando a consulta/encontrar a tarefa em que está mais interessado (porque terá de filtrar as tarefas concluídas). Também limpa os dados de tarefacorrespondentes no `retentionTime` nó (desde que ainda não tenha sido atingido). Isto garante que os seus nós não se enchem de dados de tarefas e estão sem espaço para o disco.

### <a name="task-submission"></a>Submissão de tarefas

- **Envie um grande número de tarefas numa coleção.**
    As tarefas podem ser submetidas individualmente ou em coleções. Submeta tarefas em [coleções](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) até 100 de cada vez ao fazer a submissão a granel de tarefas para reduzir o tempo de sobrecarga e submissão.

### <a name="task-execution"></a>Execução de tarefas

- **Escolher as suas tarefas max por nó** O lote suporta a subscrição de tarefas sobresubscritas em nós (executando mais tarefas do que um nó tem núcleos). Cabe-lhe a si garantir que as suas tarefas "encaixem" nos nódosos da sua piscina. Por exemplo, pode ter uma experiência degradada se tentar agendar oito tarefas que cada uma consome 25% de uso de CPU num nó (numa piscina com). `maxTasksPerNode = 8`

### <a name="designing-for-retries-and-re-execution"></a>Desenho para retries e reexecução

As tarefas podem ser automaticamente novamente experimentadas pelo Batch. Existem dois tipos de repetições: controladas pelo utilizador e internas. As repetições controladas pelo utilizador são especificadas pelo [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)da tarefa . Quando um programa especificado na tarefa sai com um código de saída não zero, `maxTaskRetryCount`a tarefa é novamente experimentada até ao valor do .

Embora rara, uma tarefa pode ser novamente experimentada internamente devido a falhas no nó de computação, tais como não ser capaz de atualizar o estado interno ou uma falha no nó enquanto a tarefa está em execução. A tarefa será novamente experimentada no mesmo nó de cálculo, se possível, até um limite interno antes de desistir da tarefa e adiar a tarefa a ser reagendada pelo Batch, potencialmente num nó de cálculo diferente.

- **Construir tarefas duráveis** As tarefas devem ser concebidas para resistir à falha e acomodar a retentativa. Isto é especialmente importante para tarefas de longo prazo. Para isso, certifique-se de que as tarefas geram o mesmo resultado único, mesmo que sejam executados mais de uma vez. Uma maneira de o conseguir é fazer com que as suas tarefas "procurem objetivos". Outra forma é garantir que as suas tarefas são idempotentes (as tarefas terão o mesmo resultado, independentemente do número de vezes que são executadas).

    Um exemplo comum é uma tarefa de copiar ficheiros para um nó de cálculo. Uma abordagem simples é uma tarefa que copia todos os ficheiros especificados cada vez que funciona, o que é ineficiente e não é construído para resistir à falha. Em vez disso, crie uma tarefa para garantir que os ficheiros estão no nó computacional; uma tarefa que não recopia ficheiros que já estão presentes. Desta forma, a tarefa retoma onde ficou parada se fosse interrompida.

- **Nódoreos** de baixa prioridade Não existem diferenças de design ao executar as suas tarefas em nódos dedicados ou de baixa prioridade. Se uma tarefa é preempted enquanto corre num nó de baixa prioridade ou interrompida devido a uma falha num nó dedicado, ambas as situações são atenuadas ao conceber a tarefa de resistir ao fracasso.

- **Tempo de execução de tarefas** Evite tarefas com pouco tempo de execução. Tarefas que só duram um a dois segundos não são as ideais. Deve tentar fazer uma quantidade significativa de trabalho numa tarefa individual (10 segundos no mínimo, até horas ou dias). Se cada tarefa estiver a executar por um minuto (ou mais), então o horário de cima como uma fração do tempo total do cálculo é pequeno.

## <a name="nodes"></a>Nós

- **Iniciar tarefas deve ser idempotente** À semelhança de outras tarefas, a tarefa de início do nó deve ser idempotente, uma vez que será reexecutada sempre que as botas do nó. Uma tarefa idempotente é simplesmente uma que produz um resultado consistente quando executada várias vezes.

- **Gerencie serviços de longo prazo através da interface de serviços do sistema operativo.**
    Às vezes, é necessário executar outro agente ao lado do agente Batch no nó, por exemplo, recolher dados do nó e denunciá-lo. Recomendamos que estes agentes sejam implantados como serviços de `systemd` SO, como um serviço Windows ou um serviço Linux.

    Ao executar estes serviços, não devem ter fechaduras de ficheiros em quaisquer ficheiros em diretórios geridos pelo Batch no nó, porque caso contrário o Batch não poderá eliminar esses diretórios devido às fechaduras dos ficheiros. Por exemplo, se instalar um serviço Windows numa tarefa inicial, em vez de lançar o serviço diretamente a partir do diretório de trabalho de tarefa inicial, copie os ficheiros noutrolocal (se os ficheiros existirem apenas saltar a cópia). Instale o serviço a partir desse local. Quando o Batch repetir a sua tarefa inicial, eliminará o diretório de tarefa inicial e irá criá-lo novamente. Isto funciona porque o serviço tem fechaduras de ficheiros no outro diretório e não no diretório de trabalho de tarefa inicial.

- **Evite criar junções de diretório sintetmente no Windows** As junções de diretório, por vezes chamadas de ligações rígidas de diretório, são difíceis de lidar durante a tarefa e limpeza de emprego. Utilize symlinks (soft-links) em vez de ligações duras.

- **Recolher os registos do agente Batch se houver um problema** Se notar um problema que envolva o comportamento de um nó ou tarefas em funcionamento num nó, é aconselhável recolher os registos do agente Batch antes de negociar os nós em questão. Os registos do agente Batch podem ser recolhidos utilizando os registos de serviço upload batch API. Estes registos podem ser fornecidos como parte de um bilhete de suporte para a Microsoft e ajudarão na resolução e resolução de problemas.

## <a name="security"></a>Segurança

### <a name="security-isolation"></a>Isolamento de segurança

Para efeitos de isolamento, se o seu cenário requer isolar empregos uns dos outros, então deve isolar estes empregos colocando-os em piscinas separadas. Uma piscina é o limite de isolamento de segurança em Batch, e por padrão, duas piscinas não são visíveis ou capazes de comunicar entre si. Evite utilizar contas de lote separadas como meio de isolamento.

## <a name="moving"></a>Movendo-se

### <a name="move-batch-account-across-regions"></a>Mover a conta batch através de regiões

Existem vários cenários em que você gostaria de mover a sua conta de Lote existente de uma região para outra. Por exemplo, talvez queira mover-se para outra região como parte do planeamento de recuperação de desastres.

As contas do Lote Azure não podem ser transferidas de uma região para outra. No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente da sua conta Batch.  Em seguida, pode encenar o recurso noutra região exportando a conta do Lote para um modelo, modificando os parâmetros para combinar com a região de destino, e, em seguida, implantar o modelo para a nova região. Depois de fazer o upload do modelo para a nova região, terá de recriar certificados, horários de trabalho e pacotes de aplicações. Para comprometer as alterações e completar o movimento da conta 'Lote', lembre-se de eliminar a conta original do Lote ou o grupo de recursos.

Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Crie e implemente modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)do Gestor de Recursos Azure utilizando o portal Azure .

## <a name="connectivity-to-the-batch-service"></a>Conectividade com o Serviço de Lote

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Grupos de Segurança de Rede (NSGs) e Rotas Definidas pelo Utilizador (UDRs)

Ao fornecer piscinas de [lotes numa rede virtual,](batch-virtual-network.md)certifique-se de que `BatchNodeManagement` está a seguir de perto as orientações relativas à utilização da etiqueta de serviço, portas, protocolos e direção da regra.
A utilização da etiqueta de serviço é altamente recomendada e não os endereços IP do serviço de lote subjacentes, uma vez que estes podem mudar ao longo do tempo. A utilização direta dos endereços IP do serviço de lote pode manifestar-se como instabilidade, interrupções ou interrupções para as suas piscinas de Lote, uma vez que o serviço de lote atualiza os endereços IP utilizados ao longo do tempo. Se está atualmente a utilizar endereços IP do serviço de lote nas suas regras NSG, é aconselhável mudar para a utilização da etiqueta de serviço.

Para as Rotas Definidas pelo Utilizador, certifique-se de que tem um processo em vigor para atualizar periodicamente os endereços IP do serviço de lote na tabela de rotas à medida que estes mudam ao longo do tempo. Para saber como obter a lista de endereços IP do serviço de lote, consulte [as etiquetas](../virtual-network/service-tags-overview.md)de serviço no local . Os endereços IP do serviço de `BatchNodeManagement` lote serão associados com a etiqueta de serviço (ou a variante regional que corresponde à região da sua conta Batch).

### <a name="honoring-dns"></a>Homenagem ao DNS

Certifique-se de que os seus sistemas estão a honrar o DNS Time-to-Live (TTL) para o seu URL de serviço de conta Batch. Além disso, certifique-se de que os seus clientes de serviço sie e outros mecanismos de conectividade para o serviço Batch não dependem de endereços IP.

Se os seus pedidos receberem respostas http de nível 5xx e houver um cabeçalho "Connection: close" na resposta, o seu cliente de serviço Batch deve respeitar a recomendação fechando a ligação existente, reresolvendo dNS para o URL do serviço de conta Batch, e tentar seguir os pedidos sobre uma nova ligação.

### <a name="retrying-requests-automatically"></a>Reexperimentar pedidos automaticamente

Certifique-se de que os seus clientes de serviço saem com as devidas políticas de retry para rejulgar automaticamente os seus pedidos, mesmo durante o funcionamento normal e não exclusivamente durante os períodos de manutenção do serviço. Estas políticas de repetição devem abranger um intervalo de pelo menos 5 minutos. As capacidades automáticas de retry são fornecidas com vários SDKs de lote, tais como a [classe .NET RetryPolicyProvider](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

