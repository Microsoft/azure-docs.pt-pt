---
title: Melhores práticas
description: Aprenda as melhores práticas e dicas úteis para desenvolver a sua solução Azure Batch.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 695f213c0683bd158539b97719f2c2d8c0210edf
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849494"
---
# <a name="azure-batch-best-practices"></a>As melhores práticas do Azure Batch

Este artigo discute uma coleção de boas práticas para a utilização do serviço Azure Batch de forma eficaz e eficiente, com base na experiência da vida real com o Batch. Leia este artigo para evitar armadilhas de design, potenciais problemas de desempenho e anti-padrões durante o desenvolvimento e utilização de Batch.

## <a name="pools"></a>Conjuntos

[As piscinas](nodes-and-pools.md#pools) são os recursos de cálculo para a execução de trabalhos no serviço Batch. As seguintes secções fornecem recomendações para trabalhar com piscinas batch.

### <a name="pool-configuration-and-naming"></a>Configuração da piscina e nomeação

- **Modo de atribuição de piscinas** Ao criar uma conta Batch, pode escolher entre dois modos de atribuição de piscinas: **serviço de lote** ou **subscrição do utilizador.** Na maioria dos casos, deve utilizar o modo de serviço Predefinido Batch, no qual os pools são atribuídos nos bastidores em subscrições geridas pelo Batch. No modo de subscrição de utilizador alternativo, as VMs do Batch e outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. As contas de subscrição do utilizador são usadas principalmente para permitir um subconjunto importante, mas pequeno, de cenários. Pode ler mais sobre o modo de subscrição do utilizador na [configuração adicional para o modo de subscrição do utilizador](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Considere o tempo de trabalho e de execução de tarefas ao determinar o trabalho para o mapeamento da piscina.**
    Se tiver empregos constituídos principalmente por tarefas de curto prazo, e as contagens totais de tarefas previstas forem reduzidas, de modo que o tempo de execução global esperado do trabalho não seja longo, não aloque um novo pool para cada trabalho. O tempo de atribuição dos nós diminuirá o tempo de funcionação do trabalho.

- **As piscinas devem ter mais do que um nó computacional.**
    Os nós individuais não estão garantidos para estarem sempre disponíveis. Embora incomuns, falhas de hardware, atualizações do sistema operativo e uma série de outros problemas podem fazer com que os nós individuais estejam offline. Se a sua carga de trabalho do Lote necessitar de progressos determinísticos e garantidos, deverá alocar piscinas com múltiplos nós.

- **Não reutilizá os nomes dos recursos.**
    Os recursos de lote (empregos, piscinas, etc.) muitas vezes vêm e vão ao longo do tempo. Por exemplo, você pode criar uma piscina na segunda-feira, apagá-la na terça-feira e, em seguida, criar outra piscina na quinta-feira. Cada novo recurso que criar deve receber um nome único que nunca usou antes. Isto pode ser feito utilizando um GUID (como todo o nome de recurso, ou como parte dele) ou incorporando o tempo que o recurso foi criado no nome do recurso. O lote suporta [o DisplayName,](/dotnet/api/microsoft.azure.batch.jobspecification.displayname)que pode ser usado para dar a um recurso um nome legível humano, mesmo que o ID de recurso real seja algo que não seja tão amigável para o homem. A utilização de nomes únicos facilita a diferenciação de que recurso particular fez algo em registos e métricas. Também elimina a ambiguidade se tiver de arquivar um caso de suporte para um recurso.

- **Continuidade durante a manutenção e avaria da piscina.**
    É melhor que os seus empregos usem piscinas dinamicamente. Se os seus trabalhos usarem a mesma piscina para tudo, há a hipótese de os seus empregos não correrem se algo correr mal com a piscina. Isto é especialmente importante para cargas de trabalho sensíveis ao tempo. Para corrigir isto, selecione ou crie uma piscina dinamicamente quando agendar cada trabalho, ou tenha uma maneira de anular o nome da piscina para que possa contornar uma piscina pouco saudável.

- **Continuidade do negócio durante manutenção e falha** na piscina Existem muitas causas possíveis que podem impedir que uma piscina cresça ao tamanho exigido, tais como erros internos, restrições de capacidade, etc. Por esta razão, você deve estar pronto para retarget empregos em uma piscina diferente (possivelmente com um tamanho VM diferente - Batch suporta isso via [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)) se necessário. Evite utilizar um ID estático com a expectativa de que nunca será apagado e nunca mudará.

### <a name="pool-lifetime-and-billing"></a>Vida útil da piscina e faturação

A vida útil da piscina pode variar dependendo do método de alocação e das opções aplicadas à configuração da piscina. As piscinas podem ter uma vida útil arbitrária e um número variado de nós computacional na piscina em qualquer momento. É da sua responsabilidade gerir os nódinhos de computação na piscina, quer explicitamente, quer através de funcionalidades fornecidas pelo serviço (autoescala ou autopool).

- **Mantenha as piscinas frescas.**
    Deve redimensionar as suas piscinas para zero a cada poucos meses para garantir que obtém as últimas atualizações de agentes de nó e correções de erros. A sua piscina não receberá atualizações de agentes de nó, a menos que seja recriada ou redimensionada para 0 nós de computação. Antes de recriar ou redimensionar a sua piscina, é aconselhável baixar quaisquer registos de agentes de nó para fins de depuração, conforme discutido na secção [Nodes.](#nodes)

- **Recriação da piscina** Numa nota semelhante, não é aconselhável apagar e recriar as suas piscinas diariamente. Em vez disso, crie uma nova piscina, atualize os seus empregos existentes para apontar para a nova piscina. Uma vez que todas as tarefas tenham sido transferidas para a nova piscina, em seguida, apagar a piscina antiga.

- **Eficiência da piscina e faturação** O lote em si não incorre em custos adicionais, mas incorrem em encargos pelos recursos computacional usados. Estás cobrado por todos os nóns da piscina, independentemente do estado em que esteja. Isto inclui quaisquer encargos necessários para que o nó seja executado, tais como custos de armazenamento e networking. Para obter mais boas práticas, consulte [análise de custos e orçamentos para o Azure Batch.](budget.md)

### <a name="pool-allocation-failures"></a>Falhas na atribuição de piscinas

Falhas de atribuição de piscinas podem ocorrer em qualquer ponto durante a primeira atribuição ou redimensionações subsequentes. Isto pode ser devido ao esgotamento temporário da capacidade numa região ou falhas em outros serviços Azure em que o Batch depende. A sua quota principal não é uma garantia, mas sim um limite.

### <a name="unplanned-downtime"></a>Período de indisponibilidade não planeado

É possível que as piscinas de Batch experimentem eventos de inatividade em Azure. Tenha isto em mente ao planear e desenvolver o seu cenário ou fluxo de trabalho para o Batch.

No caso de um nó falhar, o Batch tenta automaticamente recuperar estes nós de computação em seu nome. Isto pode desencadear o reagendamento de qualquer tarefa de execução no nó que é recuperado. Consulte [Design para recauchutagem](#design-for-retries-and-re-execution) para saber mais sobre tarefas interrompidas.

### <a name="custom-image-pools"></a>Piscinas de imagem personalizadas

Quando cria um pool Azure Batch utilizando a Configuração da Máquina Virtual, especifica uma imagem VM que fornece o sistema operativo para cada nó de computação na piscina. Pode criar a piscina com uma imagem suportada do Azure Marketplace, ou pode [criar uma imagem personalizada com uma imagem da Galeria de Imagens Partilhada.](batch-sig-images.md) Embora também possa utilizar uma [imagem gerida](batch-custom-images.md) para criar um pool de imagens personalizado, recomendamos a criação de imagens personalizadas utilizando a Galeria de Imagens Partilhadas sempre que possível. A utilização da Galeria de Imagens Partilhadas ajuda-o a providenciar piscinas mais rapidamente, escalar maiores quantidades de VMs e melhorar a fiabilidade ao a provisionar VMs.

### <a name="third-party-images"></a>Imagens de terceiros

As piscinas podem ser criadas usando imagens de terceiros publicadas no Azure Marketplace. Com o modo de subscrição do utilizador As contas de lote, poderá ver o erro "A atribuição falhou devido à verificação de elegibilidade da compra de mercado" ao criar um pool com determinadas imagens de terceiros. Para resolver este erro, aceite os termos definidos pelo editor da imagem. Pode fazê-lo utilizando [a Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) ou [a Azure CLI](https://docs.microsoft.com/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Dependência da região de Azure

É aconselhável não depender de uma única região de Azure se tiver uma carga de trabalho sensível ao tempo ou de produção. Embora raras, há problemas que podem afetar toda uma região. Por exemplo, se o seu processamento precisar de começar numa hora específica, considere aumentar a piscina na sua região primária *muito antes da hora de início*. Se a balança da piscina falhar, pode voltar a escalar uma piscina numa região de reserva (ou regiões). Piscinas em várias contas em diferentes regiões fornecem uma cópia de segurança pronta e facilmente acessível se algo correr mal com outra piscina. Para mais informações, consulte [Design a sua aplicação para obter alta disponibilidade.](high-availability-disaster-recovery.md)

## <a name="jobs"></a>Tarefas

Um [trabalho](jobs-and-tasks.md#jobs) é um contentor projetado para conter centenas, milhares, ou mesmo milhões de tarefas. Siga estas orientações ao criar empregos.

### <a name="fewer-jobs-more-tasks"></a>Menos empregos, mais tarefas

Usar um trabalho para executar uma única tarefa é ineficiente. Por exemplo, é mais eficiente usar um único trabalho contendo 1000 tarefas em vez de criar 100 postos de trabalho que contenham 10 tarefas cada. Gerir 1000 postos de trabalho, cada um com uma única tarefa, seria a abordagem menos eficiente, mais lenta e mais cara a tomar.

Por isso, certifique-se de não conceber uma solução batch que requer milhares de empregos simultaneamente ativos. Não existe quota para tarefas, por isso, executar muitas tarefas com o menor número possível de empregos utiliza eficientemente as quotas de [trabalho e agendamento de emprego.](batch-quota-limit.md#resource-quotas)

### <a name="job-lifetime"></a>Vida de trabalho

Um trabalho em Lote tem uma vida indefinida até ser apagado do sistema. O seu estado designa se pode aceitar mais tarefas para agendamento ou não.

Um trabalho não se move automaticamente para estado preenchido a menos que seja explicitamente encerrado. Isto pode ser ativado automaticamente através da propriedade [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) ou [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints).

Existe uma [quota de emprego ativo e de agendamento de emprego.](batch-quota-limit.md#resource-quotas) Os postos de trabalho e os horários de trabalho em estado preenchido não contam para esta quota.

## <a name="tasks"></a>Tarefas

[As tarefas](jobs-and-tasks.md#tasks) são unidades individuais de trabalho que compõem um trabalho. As tarefas são submetidas pelo utilizador e agendadas por Batch para os nós de computação. Existem várias considerações de design a serem tomadas ao criar e executar tarefas. As secções seguintes explicam cenários comuns e como desenhar as suas tarefas para lidar com problemas e executar de forma eficiente.

### <a name="save-task-data"></a>Guardar dados de tarefas

Os nós computacionais são pela sua natureza efémeras. Existem muitas funcionalidades em Batch, como a autopool e a autoescala, que facilitam o desaparecimento dos nós. Quando os nós saem da piscina (devido a um redimensionado ou a uma eliminação de piscina) todos os ficheiros desses nós também são apagados. Por isso, uma tarefa deve retirar a sua saída do nó em que está a funcionar e para uma loja durável antes de terminar. Da mesma forma, se uma tarefa falhar, deve mover os registos necessários para diagnosticar a falha numa loja durável.

O Batch integrou o suporte Azure Storage para carregar dados através [do OutputFiles,](batch-task-output-files.md)bem como uma variedade de sistemas de ficheiros partilhados, ou pode realizar o upload sozinho nas suas tarefas.

### <a name="manage-task-lifetime"></a>Gerir a vida útil da tarefa

Elimine as tarefas quando já não forem necessárias ou desateia uma restrição [de tarefas de retenção.](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) Se um `retentionTime` for definido, o Batch limpa automaticamente o espaço do disco utilizado pela tarefa quando o `retentionTime` expirar.

Apagar tarefas realiza duas coisas. Garante que não tem uma acumulação de tarefas no trabalho, o que pode dificultar a consulta/encontrar a tarefa que lhe interessa (porque terá de filtrar através das tarefas Concluídas). Também limpa os dados de tarefa correspondentes no nó (fornecido `retentionTime` ainda não foi atingido). Isto ajuda a garantir que os seus nós não se enchem com dados de tarefa e se esgotam o espaço do disco.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Enviar um grande número de tarefas na recolha

As tarefas podem ser submetidas individualmente ou em coleções. Submeta tarefas em [coleções](/rest/api/batchservice/task/addcollection) até 100 numa altura em que se fazem a submissão a granel de tarefas para reduzir o tempo de sobrecarga e submissão.

### <a name="set-max-tasks-per-node-appropriately"></a>Definir tarefas máximas por nó adequadamente

O lote suporta tarefas de subscrição excessiva nos nóns (executando mais tarefas do que um nó tem núcleos). Cabe-lhe a si assegurar que as suas tarefas "encaixem" nos nós da sua piscina. Por exemplo, pode ter uma experiência degradada se tentar agendar oito tarefas que cada uma consome 25% de uso de CPU num nó (numa piscina `taskSlotsPerNode = 8` com).

### <a name="design-for-retries-and-re-execution"></a>Desenho para recauchutagem e re-execução

As tarefas podem ser automaticamente novamente experimentadas por Batch. Existem dois tipos de retrós em que se encontram as suas recaídas: controladas pelo utilizador e internas. As retrações controladas pelo utilizador são especificadas pelo [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)da tarefa . Quando um programa especificado na tarefa sai com um código de saída não zero, a tarefa é novamente tentada até ao valor do `maxTaskRetryCount` .

Embora rara, uma tarefa pode ser novamente julgada internamente devido a falhas no nó de computação, como não ser capaz de atualizar o estado interno ou uma falha no nó enquanto a tarefa está em execução. A tarefa será novamente experimentada no mesmo nó de computação, se possível, até um limite interno antes de desistir da tarefa e adiar a tarefa a ser reagendada por Batch, potencialmente num nó de computação diferente.

Não existem diferenças de design ao executar as suas tarefas em nós dedicados ou de baixa prioridade. Se uma tarefa é antecipada enquanto executa um nó de baixa prioridade ou interrompida devido a uma falha num nó dedicado, ambas as situações são atenuadas através da conceção da tarefa para resistir ao insucesso.

### <a name="build-durable-tasks"></a>Construir tarefas duráveis

As tarefas devem ser concebidas para resistir à falha e acomodar a repetição. Isto é especialmente importante para tarefas de longa duração. Para isso, certifique-se de que as tarefas geram o mesmo resultado único, mesmo que sejam executadas mais de uma vez. Uma maneira de conseguir isso é fazer as suas tarefas "procurar objetivos". Outra forma é garantir que as suas tarefas são idempotentes (as tarefas terão o mesmo resultado independentemente de quantas vezes forem executadas).

Um exemplo comum é uma tarefa para copiar ficheiros para um nó de computação. Uma abordagem simples é uma tarefa que copia todos os ficheiros especificados sempre que é executado, o que é ineficiente e não é construído para resistir ao fracasso. Em vez disso, crie uma tarefa para garantir que os ficheiros estão no nó de computação; uma tarefa que não recopia ficheiros que já estão presentes. Desta forma, a tarefa retoma onde ficou se fosse interrompida.

### <a name="avoid-short-execution-time"></a>Evite um curto período de execução

Tarefas que só duram um a dois segundos não são ideais. Deve tentar fazer uma quantidade significativa de trabalho numa tarefa individual (mínimo de 10 segundos, indo até horas ou dias). Se cada tarefa estiver executada por um minuto (ou mais), então a sobrecarga de agendamento como uma fração do tempo total de computação é pequena.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Utilize o âmbito da piscina para tarefas curtas nos nos nosmes do Windows

Ao agendar uma tarefa nos nós do Lote, pode escolher se a executou com âmbito de tarefa ou alcance de piscina. Se a tarefa funcionar apenas por um curto período de tempo, o âmbito de tarefa pode ser ineficiente devido aos recursos necessários para criar a conta de utilizador automático para essa tarefa. Para uma maior eficiência, considere definir estas tarefas para o âmbito de agrupamento. Para obter mais informações, consulte [Executar uma tarefa como utilizador automático com âmbito de piscina](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Nós

Um [nó computativo](nodes-and-pools.md#nodes) é uma máquina virtual Azure (VM) ou vM de serviço de nuvem que se dedica a processar uma parte da carga de trabalho da sua aplicação. Siga estas diretrizes ao trabalhar com nós.

### <a name="idempotent-start-tasks"></a>Tarefas de início idempotente

Tal como acontece com outras tarefas, a [tarefa de arranque](jobs-and-tasks.md#start-task) do nó deve ser idempotente, uma vez que será repetida sempre que o nó de botas. Uma tarefa idempotente é simplesmente uma tarefa que produz um resultado consistente quando executado várias vezes.

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Gerir serviços de longa duração através da interface de serviços do sistema operativo

Às vezes, é necessário correr outro agente ao lado do agente Batch no nó. Por exemplo, pode querer recolher dados do nó e denunciá-lo. Recomendamos que estes agentes sejam implantados como serviços de OS, como um serviço Windows ou um `systemd` serviço Linux.

Ao executar estes serviços, não devem ter bloqueios de ficheiros em ficheiros em diretórios geridos pelo Batch no nó, porque caso contrário o Batch não poderá eliminar esses diretórios devido às fechaduras de ficheiros. Por exemplo, se instalar um serviço Windows numa tarefa inicial, em vez de lançar o serviço diretamente a partir do diretório de trabalho da tarefa inicial, copie os ficheiros em outro lugar (ou se os ficheiros existirem apenas ignore a cópia). Em seguida, instale o serviço a partir daquele local. Quando o Batch reexecutar a sua tarefa inicial, eliminará o diretório de trabalho da tarefa inicial e criará-o novamente. Isto funciona porque o serviço tem bloqueios de ficheiros no outro diretório, não na tarefa inicial de funcionamento do diretório.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Evite criar junções de diretório no Windows

As junções de diretório, por vezes chamadas de ligações rígidas do diretório, são difíceis de lidar durante a tarefa e a limpeza do emprego. Utilize symlinks (soft-links) em vez de ligações rígidas.

### <a name="collect-the-batch-agent-logs"></a>Recolher os registos do agente Batch

Se notar um problema que envolva o comportamento de um nó ou tarefas em execução num nó, recolha os registos do agente Batch antes de negociar os nós em questão. Os registos do agente Batch podem ser recolhidos utilizando os registos de serviço do Upload Batch API. Estes registos podem ser fornecidos como parte de um bilhete de suporte para a Microsoft e ajudarão na resolução e resolução de problemas de problemas.

### <a name="manage-os-upgrades"></a>Gerir atualizações de SO

Para o modo de subscrição do utilizador As contas de lote, as atualizações automatizadas de SO podem interromper o progresso da tarefa, especialmente se as tarefas forem longas. [A construção de tarefas idempotentes](#build-durable-tasks) pode ajudar a reduzir os erros causados por estas interrupções. Recomendamos também [o agendamento de atualizações de imagens de SO para horários em que não se espera que as tarefas sejam executadas](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

## <a name="isolation-security"></a>Segurança de isolamento

Para efeitos de isolamento, se o seu cenário requer isolamento de empregos uns dos outros, faça-o por tê-los em piscinas separadas. Uma piscina é o limite de isolamento de segurança em Batch, e por padrão, duas piscinas não são visíveis ou capazes de comunicar entre si. Evite utilizar contas de Lote separadas como meio de isolamento.

## <a name="moving-batch-accounts-across-regions"></a>Movimentação de contas de Batch em regiões

Há cenários em que pode ser útil mover uma conta batch existente de uma região para outra. Por exemplo, talvez queira mudar-se para outra região como parte do planeamento de recuperação de desastres.

As contas do Azure Batch não podem ser transferidas diretamente de uma região para outra. No entanto, pode utilizar um modelo de Gestor de Recursos Azure para exportar a configuração existente da sua conta Batch. Em seguida, pode encenar o recurso noutra região exportando a conta Batch para um modelo, modificando os parâmetros para combinar com a região de destino e, em seguida, implantando o modelo para a nova região.

Depois de fazer o upload do modelo para a nova região, terá de recriar certificados, horários de trabalho e pacotes de aplicações. Para cometer as alterações e concluir a mudança da conta Batch, lembre-se de eliminar a conta original do Lote ou grupo de recursos.

Para obter mais informações sobre o Gestor de Recursos e modelos, consulte [Quickstart: Criar e implementar modelos de Gestor de Recursos Azure utilizando o portal Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Conectividade

Reveja as seguintes orientações ao considerar a conectividade nas suas soluções Batch.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Grupos de Segurança de Rede (NSGs) e Rotas Definidas pelo Utilizador (UDRs)

Ao a provisionar [piscinas de Lote numa rede virtual,](batch-virtual-network.md)certifique-se de que está a seguir de perto as diretrizes relativas à utilização da etiqueta de `BatchNodeManagement` serviço, portas, protocolos e direção da regra.
A utilização da etiqueta de serviço é altamente recomendada, em vez de utilizar os endereços IP de serviço subjacentes. Isto porque os endereços IP podem ser alterados ao longo do tempo. A utilização de endereços IP de serviço de lote diretamente pode causar instabilidade, interrupções ou interrupções para as suas piscinas de Lote.

Para as Rotas Definidas pelo Utilizador (UDRs), certifique-se de que tem um processo em vigor para atualizar os endereços IP do serviço Batch periodicamente na sua tabela de rotas, uma vez que estes endereços mudam ao longo do tempo. Para saber como obter a lista de endereços IP de serviço de lote, consulte [as etiquetas de serviço no local.](../virtual-network/service-tags-overview.md) Os endereços IP de serviço de lote serão associados com a etiqueta de `BatchNodeManagement` serviço (ou a variante regional que corresponde à região da conta Batch).

### <a name="honoring-dns"></a>Homenageando o DNS

Certifique-se de que os seus sistemas estão a honrar o DNS Time-to-Live (TTL) para o seu URL de serviço de conta Batch. Além disso, certifique-se de que os seus clientes de serviço Batch e outros mecanismos de conectividade para o serviço Batch não dependem de endereços IP (ou [criar um pool com endereços IP públicos estáticos,](create-pool-public-ip.md) conforme descrito abaixo).

Se os seus pedidos receberem respostas HTTP de nível 5xx e houver um cabeçalho "Ligação: fechar" na resposta, o seu cliente de serviço Batch deve observar a recomendação fechando a ligação existente, reen resolução de DNS para o URL de serviço de conta Batch e tentativa de seguir pedidos sobre uma nova ligação.

### <a name="retry-requests-automatically"></a>Redação de pedidos automaticamente

Certifique-se de que os seus clientes de serviço Batch dispõem de políticas de relírimento adequadas para reescamar automaticamente os seus pedidos, mesmo durante o funcionamento normal e não exclusivamente durante quaisquer períodos de manutenção do serviço. Estas políticas de reesão devem abranger um intervalo de, pelo menos, 5 minutos. As capacidades automáticas de retenção são fornecidas com vários SDKs de lote, tais como a [classe .NET RetryPolicyProvider](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Endereços IP públicos estáticos

Normalmente, as máquinas virtuais numa piscina de Lote são acedidas através de endereços IP públicos que podem mudar ao longo da vida útil da piscina. Isto pode dificultar a interação com uma base de dados ou outro serviço externo que limite o acesso a determinados endereços IP. Para garantir que os endereços IP públicos na sua piscina não mudam inesperadamente, pode criar uma piscina usando um conjunto de endereços IP públicos estáticos que controla. Para obter mais informações, consulte [criar um pool Azure Batch com endereços IP públicos especificados.](create-pool-public-ip.md)

## <a name="batch-node-underlying-dependencies"></a>Dependências subjacentes ao nó do lote

Considere as seguintes dependências e restrições ao conceber as suas soluções Batch.

### <a name="system-created-resources"></a>Recursos criados pelo sistema

O Azure Batch cria e gere um conjunto de utilizadores e grupos no VM, que não deve ser alterado. São os seguintes:

#### <a name="windows"></a>Windows

- Um utilizador chamado **PoolNonAdmin**
- Um grupo de utilizadores chamado **WATaskCommon**

#### <a name="linux"></a>Linux

- Um utilizador chamado **_azbatch**

### <a name="file-cleanup"></a>Limpeza de ficheiros

O lote tenta ativamente limpar o diretório de trabalho em que as tarefas são executadas, uma vez que o seu tempo de retenção expira. Quaisquer ficheiros escritos fora deste diretório são [da sua responsabilidade de limpar para](#manage-task-lifetime) evitar preencher o espaço do disco.

A limpeza automatizada do diretório de trabalho será bloqueada se executar um serviço no Windows a partir do diretório de trabalho startTask, devido à pasta ainda em uso. Isto resultará num desempenho degradado. Para corrigir isto, mude o diretório desse serviço para um diretório separado que não seja gerido pelo Batch.
