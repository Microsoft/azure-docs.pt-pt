---
title: Descrição geral para programadores - Azure Batch | Documentos da Microsoft
description: Conheça as funcionalidades do serviço Batch e das respetivas APIs de um ponto de vista de programação.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 12/18/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3839a1a457af1549a82b7607310a619911c78591
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761876"
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Desenvolver soluções de computação paralelas em grande escala com o Batch

Nesta descrição geral dos componentes principais do serviço Azure Batch, vamos discutir as funcionalidades e os recursos principais do serviço que os programadores do Batch podem utilizar para criar soluções de computação paralelas em grande escala.

Se está a desenvolver uma aplicação computacional distribuída ou um serviço que emite chamadas à [API REST][batch_rest_api] diretas ou a utilizar os [SDKs do Batch](batch-apis-tools.md#azure-accounts-for-batch-development), vai utilizar muitos dos recursos e funcionalidades abordados neste artigo.

> [!TIP]
> Para obter uma introdução mais detalhada do serviço Batch, veja [Noções básicas do Azure Batch](batch-technical-overview.md). Veja também a versão mais recente das [Atualizações de serviço do Batch](https://azure.microsoft.com/updates/?product=batch).
>
>

## <a name="batch-service-workflow"></a>Fluxo de trabalho do serviço Batch
O fluxo de trabalho detalhado que se segue é típico de quase todos os serviços e aplicações que utilizam o serviço Batch para processar cargas de trabalho paralelas:

1. Carregue os **ficheiros de dados** que quer processar para uma conta do [Armazenamento do Azure][azure_storage]. O Batch inclui suporte incorporado para aceder ao Armazenamento de Blobs do Azure e as suas tarefas podem transferir estes ficheiros para [nós de computação](#compute-node) quando são executadas.
2. Carregue os **ficheiros de aplicação** que as aplicações vão executar. Estes ficheiros podem ser binários ou scripts e as respetivas dependências e são executados pelas tarefas dos seus trabalhos. As tarefas podem transferir estes ficheiros a partir da sua conta de Armazenamento ou pode utilizar a funcionalidade de [pacotes de aplicações](#application-packages) do Batch na gestão e implementação de aplicações.
3. Crie um [conjunto](#pool) de nós de computação. Quando cria um conjunto, especifica o número de nós de computação do mesmo e o tamanho e o sistema operativo desses nós. Quando cada tarefa dos seus trabalhos é executada, é atribuída a um dos nós do conjunto.
4. Crie um [trabalho](#job). Os trabalhos gerem uma coleção de tarefas. Cada trabalho é associado a um conjunto específico no qual as tarefas desses trabalhos vão ser executadas.
5. Adicione [tarefas](#task) ao trabalho. Cada tarefa executa a aplicação ou script que carregou para processar os ficheiros de dados que transfere a partir da sua conta de Armazenamento. À medida que cada tarefa é concluída, pode carregar o respetivo resultado para o Armazenamento do Azure.
6. Monitorize o progresso do trabalho e obtenha o resultado da tarefa no Armazenamento do Azure.

As secções seguintes abordam estes e os outros recursos do Batch permitem o seu cenário computacional distribuído.

> [!NOTE]
> Precisa de uma [conta do Batch](#account) para utilizar o serviço. Quase todas as soluções do Batch utilizam também uma conta de [Armazenamento do Azure][azure_storage] associada para armazenamento e obtenção de ficheiros. 
>
>

## <a name="batch-service-resources"></a>Recursos do serviço Batch
Todas as soluções que utilizam o serviço Batch precisam de alguns dos recursos seguintes, como contas, nós de computação, conjuntos, trabalhos e tarefas. Outros recursos, como as agendas de trabalhos e os pacotes de aplicações, são funcionalidades úteis, mas opcionais.

* [Conta](#account)
* [Nó de computação](#compute-node)
* [Conjunto](#pool)
* [Tarefa](#job)
  * [Agendas de tarefas](#scheduled-jobs)
* [Tarefa](#task)
  * [Tarefa de início](#start-task)
  * [Tarefa do gestor de tarefas](#job-manager-task)
  * [Tarefas de preparação e de lançamento da tarefa](#job-preparation-and-release-tasks)
  * Tarefa de várias instâncias (Multi-instance task, MPI)
  * [Dependências de tarefas](#task-dependencies)
* [Pacotes de aplicações](#application-packages)

## <a name="account"></a>Conta
Uma conta do Batch é uma entidade identificada exclusivamente no âmbito do serviço Batch. Todo o processamento está associado a uma conta do Batch.

Pode criar uma conta do Azure Batch através do [portal do Azure](batch-account-create-portal.md) ou através de programação, como com a [Biblioteca .NET de Gestão de Batch](batch-management-dotnet.md). Ao criar a conta, pode associar uma conta de armazenamento do Azure para armazenar aplicações ou dados de entrada e saída relacionados com tarefas.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

## <a name="azure-storage-account"></a>Conta de armazenamento do Azure

A maioria das soluções do Batch utilizam o Armazenamento do Azure para armazenar ficheiros de recursos e ficheiros de saída. Por exemplo, as suas tarefas do Batch (incluindo tarefas standard, tarefas de início, tarefas de preparação de trabalhos e tarefas de lançamento de trabalhos), normalmente, especificam os ficheiros de recursos que residem numa contas de armazenamento.

O Batch suporta os seguintes tipos de contas de Armazenamento do Azure:

* Contas para Fins gerais v2 (GPv2) 
* Contas para Fins gerais v1 (GPv1)
* Contas de armazenamento de blobs (atualmente suportadas para conjuntos na configuração da Máquina Virtual)

Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

Pode associar uma conta de armazenamento à sua conta do Batch ao criar a conta do Batch, ou posteriormente. Considere os requisitos de desempenho e custo ao escolher uma conta de armazenamento. Por exemplo, as opções de conta de armazenamento GPv2 e BLOBs suportam [limites de escalabilidade e capacidade](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) mais elevados em comparação com a GPv1. (Contacte o Suporte do Azure para pedir um aumento de um limite de armazenamento.) Estas opções de conta podem melhorar o desempenho das soluções do Batch que contêm um grande número de tarefas paralelas que leem ou escrevem para a conta de armazenamento.

## <a name="compute-node"></a>Nó de computação
Um nó de computação é uma máquina virtual (VM) ou uma VM de um serviço cloud do Azure dedicada ao processamento de uma parte da carga de trabalho da sua aplicação. O tamanho de um nó determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de ficheiros local que está alocado ao nó. Pode criar conjuntos de nós do Windows ou Linux com imagens dos Serviços Cloud do Azure, imagens do [Marketplace das Máquinas Virtuais do Azure][vm_marketplace] ou imagens personalizadas preparadas por si. Veja a secção [Conjunto](#pool), abaixo, para obter mais informações sobre estas opções.

Os nós podem executar qualquer executável ou script que seja suportado pelo ambiente dos respetivos sistemas operativos. Incluem-se \*.exe, \*. cmd, \*. bat e scripts do PowerShell para o Windows, e binários, shell e scripts Python para Linux.

Todos os nós de computação do Batch incluem também:

* Uma [estrutura de pastas](#files-and-directories) padrão e [variáveis de ambiente](#environment-settings-for-tasks) associadas que também estão disponíveis para referência por parte das tarefas.
* Definições de **Firewall** que são configuradas para controlar o acesso.
* [Acesso remoto](#connecting-to-compute-nodes) para Windows (protocolo RDP (Remote Desktop Protocol)) e nós Linux (Secure Shell (SSH)).

## <a name="pool"></a>Conjunto
Um conjunto é uma coleção de nós na qual a sua aplicação é executada. O conjunto pode ser criado manualmente por si ou pelo serviço Batch automaticamente quando especifica o trabalho a ser realizado. Pode criar e gerir um conjunto que cumpra os requisitos de recursos da sua aplicação. Os conjuntos só podem ser utilizados pela conta do Batch em que foram criados. Uma conta do Batch pode ter mais do que um conjunto.

Conjuntos do Azure Batch criados com base na plataforma de computação principal do Azure. Eles proporcionam alocação em grande escala, instalação de aplicações, distribuição de dados, monitorização de estado de funcionamento e o ajuste flexível do número de nós de computação dentro de um conjunto ([dimensionamento](#scaling-compute-resources)).

Um nome e um endereço IP exclusivos são atribuídos a cada nó que seja adicionado a um conjunto. Quando um nó é removido de um conjunto, as alterações feitas ao sistema operativo ou aos ficheiros perdem-se e o respetivo nome e endereço IP são libertados para utilização futura. Quando um nó deixa um conjunto, a sua duração termina.

Quando cria um conjunto, pode especificar os seguintes atributos:

- Sistema operativo e versão do nó de computação
- Tipo de nó de comutação e número de nós de destino
- Tamanho dos nós de computação
- Política de dimensionamento
- Política de agendamento de tarefas
- Estado de comunicação dos nós de computação
- Tarefas de início dos nós de computação
- Pacotes de aplicações
- Configuração da rede

Cada uma destas definições está descrita mais detalhadamente nas secções seguintes.

> [!IMPORTANT]
> As contas do Batch têm uma quota predefinida que limita o número de núcleos nas mesmas. O número de núcleos corresponde ao número de nós de computação. Pode encontrar as quotas predefinidas e instruções sobre como [aumentar uma quota](batch-quota-limit.md#increase-a-quota) em [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Quotas e limites para o serviço Azure Batch). Se o seu agrupamento não estiver a obter o número de destino de nós, tal poderá dever-se à quota de núcleos.
>


### <a name="compute-node-operating-system-and-version"></a>Sistema operativo e versão do nó de computação

Quando cria um agrupamento do Batch, pode especificar a configuração da máquina virtual do Azure e o tipo de sistema operativo que quer executar em cada nó de computação do agrupamento. Os dois tipos de configurações disponíveis no Batch são:

- A **Configuração de Máquina Virtual**, que especifica que o agrupamento é composto por máquinas virtuais do Azure. Estas VMs podem ser criadas a partir de imagens do Linux ou do Windows. 

    Quando cria um agrupamento com base na Configuração de Máquina Virtual, tem de especificar não só o tamanho dos nós e a origem das imagens utilizadas para criá-los, como também a **referência da imagem da máquina virtual** e o **SKU do agente de nó** do Batch a instalar nos nós. Para obter mais informações sobre como especificar estas propriedades dos conjuntos, veja [Provision Linux compute nodes in Azure Batch pools (Aprovisionar nós de computação do Linux em conjuntos do Azure Batch)](batch-linux-nodes.md). Opcionalmente, pode anexar um ou mais discos de dados vazio às VMs do agrupamento criadas a partir de imagens do Marketplace ou incluir os discos de dados em imagens personalizadas utilizadas para criar as VMs.

- A **Configuração de Serviços Cloud**, que especifica que o agrupamento é composto por nós de Serviços Cloud do Azure. Os Serviços Cloud *só* disponibilizam nós de computação do Windows.

    Os sistemas operativos disponíveis para os conjuntos de Configuração de Serviços Cloud estão listados em [Azure Guest OS releases and SDK compatibility matrix (Versões de SO Convidado do Azure e matriz de compatibilidade de SDK)](../cloud-services/cloud-services-guestos-update-matrix.md). Quando cria um agrupamento que contém nós de Serviços Cloud, tem de especificar o tamanho do nó e a respetiva *Família de SO*. Os Serviços Cloud são implementados no Azure mais rapidamente do que as máquinas virtuais que executem o Windows. Se quiser agrupamentos de nós de computação do Windows, vai chegar à conclusão de que os Serviços Cloud são mais vantajosos no que diz respeito ao tempo de implementação.

    * A *Família do SO* também determina quais as versões do .NET que estão instaladas no SO.
    * Tal como acontece com as funções de trabalho nos Serviços Cloud, pode especificar uma *Versão de SO* (para obter mais informações sobre as funções de trabalho, consulte a [descrição geral dos Serviços Cloud](../cloud-services/cloud-services-choose-me.md)).
    * Tal como com as funções de trabalho, recomendamos que especifique `*` para a *Versão do SO*, para que os nós sejam atualizados automaticamente e não seja necessário fazer nada para fornecer versões lançadas recentemente. O principal motivo para selecionar uma versão de SO específica é garantir a compatibilidade da aplicação, o que permite fazer testes de retrocompatibilidade antes de permitir a atualização da versão. Após a validação, a *Versão do SO* do conjunto pode ser atualizada e a nova imagem do SO instalada. As tarefas que estejam em execução são interrompidas e colocadas novamente em fila.

Quando cria um agrupamento, tem de selecionar o **nodeAgentSkuId** adequado, consoante o SO da imagem de base do VHD. Pode chamar a operação [List Supported Node Agent SKUs](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus) para ver um mapeamento dos IDs de SKUs de agente de nós disponíveis para as respetivas referências de Imagem do SO.


#### <a name="custom-images-for-virtual-machine-pools"></a>Imagens personalizadas para agrupamentos de Máquinas Virtuais

Para utilizar uma imagem personalizada, terá de generalizar a imagem para prepará-la. Para obter informações sobre como preparar imagens do Linux personalizadas a partir de VMs do Azure, veja [Como criar uma imagem de uma máquina virtual ou VHD](../virtual-machines/linux/capture-image.md). Para obter informações sobre como preparar imagens do Windows personalizadas a partir de VMs do Azure, veja [Criar uma imagem gerida de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md). 

Para obter requisitos e passos detalhados, veja [Utilizar uma imagem personalizada para criar um conjunto de máquinas virtuais](batch-custom-images.md).

#### <a name="container-support-in-virtual-machine-pools"></a>Suporte de contentor em agrupamentos de Máquinas Virtuais

Ao criar um agrupamento de Configuração de Máquinas Virtuais com as APIs do Batch, pode configurar o agrupamento para executar tarefas nos contentores do Docker. Atualmente, tem de criar o agrupamento através de uma imagem que suporte contentores do Docker. Utilize o Windows Server 2016 Datacenter com a imagem de Contentores do Azure Marketplace ou forneça uma imagem da VM personalizada que inclua o Docker Community Edition ou Enterprise Edition e quaisquer controladores necessários. As definições do agrupamento têm de incluir uma [configuração de contentor](/rest/api/batchservice/pool/add) que copie as imagens do contentor para as VMs quando for criado o agrupamento. As tarefas executadas no agrupamento podem, então, referenciar as imagens e as opções de execução do contentor.

Para obter mais informações, veja [Aplicações de contentor do Run Docker no Azure Batch](batch-docker-container-workloads.md).

## <a name="compute-node-type-and-target-number-of-nodes"></a>Tipo de nó de comutação e número de nós de destino

Quando cria um conjunto, pode especificar quais os tipos de nós de computação que pretende e o número de destino de cada um. Os dois tipos de nós de computação são:

- **Nós de computação dedicados.** Os nós de computação dedicados estão reservados para as suas cargas de trabalho. São mais dispendiosos do que os nós de baixa prioridade, mas é garantido que nunca serão substituídos.

- **Nós de computação de baixa prioridade.** Os nós de baixa prioridade tiram partido da capacidade excedente do Azure para executar as cargas de trabalho do Batch. Os nós de baixa prioridade são mais económicos por hora do que os nós dedicados e permitem cargas de trabalho que exigem muito poder de computação. Para obter mais informações, veja [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Utilizar VMs de baixa prioridade com o Batch).

    Se o Azure não tiver capacidade excedente suficiente, os nós de computação de baixa prioridade podem ser substituídos. Se um nó for substituído enquanto está a executar tarefas, estas são colocadas novamente na fila e executadas assim que esteja disponível um nó de computação. Os nós de baixa prioridade são uma boa opção para cargas de trabalho em que o tempo de conclusão do trabalho é flexível e este pode ser distribuído por muitos nós. Antes de decidir se vai utilizar nós de baixa prioridade no seu cenário, confirme que eventuais trabalhos perdidos devido à interrupção são mínimos e fáceis de recriar.

    
Pode ter nós de computação de baixa prioridade e dedicados no mesmo conjunto. Cada tipo e nó &mdash; baixa prioridade e dedicado &mdash; tem a sua própria definição de destino, de acordo com a qual pode especificar o número pretendido de nós. 
    
O número de nós de computação é referido com *destino* porque, em algumas situações, é possível que o seu conjunto não atinja o número de nós pretendido. Por exemplo, um conjunto pode não alcançar o destino se atingir primeiro a [quota de núcleos](batch-quota-limit.md) da sua conta do Batch. Ou o conjunto poderá não alcançar o destino se tiver aplicado uma fórmula de dimensionamento automático ao conjunto que limite o número máximo de nós.

Para obter informações sobre os preços dos nós de computação de baixa prioridade e dedicados, veja [Preços de Batch](https://azure.microsoft.com/pricing/details/batch/).

### <a name="size-of-the-compute-nodes"></a>Tamanho dos nós de computação

Quando cria um conjunto do Azure Batch, pode escolher entre quase todas as famílias e tamanhos de VM disponíveis no Azure. O Azure oferece um leque diversificado de tamanhos de VM para diferentes cargas de trabalho, incluindo tamanhos de VM especializados preparados para [HPC](../virtual-machines/linux/sizes-hpc.md) ou [GPU](../virtual-machines/linux/sizes-gpu.md). 

Para obter mais informações, veja [Escolher um tamanho de VM para nós de computação num conjunto do Azure Batch](batch-pool-vm-sizes.md).

### <a name="scaling-policy"></a>Política de dimensionamento

Para cargas de trabalho dinâmicas, pode escrever e aplicar uma [fórmula de dimensionamento automático](#scaling-compute-resources) num conjunto. O serviço Batch avalia periodicamente a fórmula e ajusta o número de nós dentro do conjunto com base em vários parâmetros de conjuntos, trabalhos e tarefas que pode especificar.

### <a name="task-scheduling-policy"></a>Política de agendamento de tarefas

A opção de configuração [máximo de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó de computação dentro do conjunto.

A configuração predefinida especifica que uma tarefa seja executada num nó de computação de cada vez, mas existem cenários onde é vantajoso ter duas ou mais tarefas executadas num nó em simultâneo. Veja o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo [Tarefas de nó simultâneas](batch-parallel-node-tasks.md) para saber como tirar partido de várias tarefas por nó.

Também pode especificar um *tipo de preenchimento*, que determina se o Batch propaga as tarefas uniformemente em todos os nós de um conjunto ou se preenche cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó.

### <a name="communication-status-for-compute-nodes"></a>Estado de comunicação dos nós de computação

Na maioria dos cenários, as tarefas funcionam de forma independente e não têm de comunicar entre si. Contudo, poderão existir algumas aplicações nas quais as tarefas têm de comunicar, como em [cenários de MPI](batch-mpi.md).

Pode configurar um conjunto para permitir a **comunicação entre os nós**, para que os nós do conjunto possam comunicar no runtime. Se a comunicação internós estiver ativada, os nós nos conjuntos de Configuração de Serviços Cloud podem comunicar entre si em portas maiores do que a 1.100 e os conjuntos de Configuração de Máquina Virtual não restringem o tráfego em nenhuma porta.

Tenha em conta que a comunicação internós também tem impacto na colocação dos nós dentro dos clusters e pode limitar o número máximo de nós num conjunto, devido a restrições de implementação. Se a comunicação entre nós não for necessária para a sua aplicação, o serviço Batch pode alocar um número de nós potencialmente maior ao conjunto a partir de muitos clusters e datacenters diferentes, para proporcionar um poder de processamento paralelo superior.

### <a name="start-tasks-for-compute-nodes"></a>Tarefas de início dos nós de computação

A *tarefa inicial* opcional é executada em cada nó à medida que é adicionado ao conjunto, e sempre que é reiniciado ou sempre que a respetiva imagem for recriada. A tarefa inicial é especialmente útil para preparar nós de computação para a execução de tarefas, como instalar as aplicações que as suas tarefas executam nos nós de computação.

### <a name="application-packages"></a>Pacotes de aplicações

Pode especificar [pacotes de aplicações](#application-packages) para implementar os nós de computação no conjunto. Os pacotes de aplicações fornecem uma implementação simplificada e o controlo de versões das aplicações que as suas tarefas executam. Os pacotes de aplicações que especificou para um conjunto são instalados em cada nó associado a esse conjunto, e sempre que um nó é reiniciado ou sempre que a respetiva imagem for recriada.

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações. Para obter mais informações sobre a utilização de pacotes de aplicações para implementar as aplicações em nós do Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
>
>

### <a name="network-configuration"></a>Configuração da rede

Pode especificar a sub-rede de uma [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) do Azure na qual devem ser criados os nós de computação do agrupamento. Consulte a secção de configuração de rede de conjunto para obter mais informações.


## <a name="job"></a>Tarefa
Um trabalho é uma coleção de tarefas. Gere de que forma é que a computação é realizada pelas respetivas tarefas nos nós de computação de um conjunto.

* O trabalho especifica o **conjunto** no qual o trabalho será executado. Pode criar um novo conjunto para cada trabalho ou utilizar um conjunto para muitos trabalhos. Pode criar um conjunto para cada trabalho associado a uma agenda de trabalho ou para todos os trabalhos associados a uma agenda de trabalho.
* Pode especificar uma **prioridade de trabalho** opcional. Quando um trabalho for submetido com uma prioridade superior à dos trabalhos atualmente em curso, as tarefas do trabalho de prioridade superior são introduzidas na fila à frente das tarefas dos trabalhos de prioridade inferior. As tarefas de trabalhos de prioridade inferior que já estão em execução não serão substituídas.
* Pode utilizar **restrições** de trabalhos para especificar determinados limites para os trabalhos:

    Pode definir um **tempo máximo cronometrado**, para que, se um trabalho for executado durante mais tempo do que o tempo máximo cronometrado especificado, o trabalho e as respetivas tarefas são terminados.

    O Batch pode detetar e, depois, repetir as tarefas falhadas. Pode especificar o **número máximo de repetições de tarefas** como uma restrição, incluindo o facto de uma tarefa ser *sempre* ou *nunca* repetida. Repetir uma tarefa significa que a tarefa é recolocada na fila para ser executada novamente.
* A aplicação cliente pode adicionar tarefas a um trabalho ou pode especificar uma [tarefa de gestão de trabalhos](#job-manager-task). As tarefas de gestão de trabalhos contêm as informações necessárias para criar as tarefas necessárias para um trabalho, sendo a tarefa de gestão de trabalhos executada num dos nós de computação do conjunto. A tarefa do gestor de trabalhos é processada especificamente pelo Batch – é colocada na fila após a criação do trabalho e é reiniciada se falhar. A tarefa de gestão de trabalhos é *obrigatória* para os trabalhos criados por uma [agenda de trabalhos](#scheduled-jobs), já que é a única forma de definir as tarefas antes de o trabalho ser instanciado.
* Por predefinição, os trabalhos permanecem no estado ativo quando todas as tarefas dentro do trabalho estiverem concluídas. Pode alterar este comportamento para que o trabalho seja automaticamente terminado quando todas as tarefas no trabalho estiverem concluídas. Defina a propriedade **onAllTasksComplete** da tarefa ([OnAllTasksComplete][net_onalltaskscomplete] no Batch .NET) para *terminatejob*, para terminar automaticamente o trabalho quando todas as respetivas tarefas estiverem no estado de conclusão.

    Tenha em atenção que o serviço Batch considera um trabalho *sem* tarefas como tendo todas as tarefas concluídas. Por conseguinte, esta opção é frequentemente utilizada com uma [tarefa de gestor de trabalhos](#job-manager-task). Se pretender utilizar a terminação automática do trabalho sem um gestor de trabalhos, deve definir inicialmente uma nova propriedade **onAllTasksComplete** do trabalho como *noaction* e, em seguida, configurá-la para *terminatejob* (terminar o trabalho) apenas depois de terminar de adicionar tarefas ao trabalho.

### <a name="job-priority"></a>Prioridade dos trabalhos
Pode atribuir uma prioridade aos trabalhos que cria no Batch. O serviço Batch utiliza o valor de prioridade do trabalho para determinar a ordem de agendamento dos trabalhos numa conta (não deve ser confundido com [trabalhos agendados](#scheduled-jobs)). Os valores de prioridade variam entre -1000 a 1000, sendo -1000 a prioridade mais baixa e 1000 a prioridade mais alta. Para atualizar a prioridade de uma tarefa, realize a operação [Atualizar as propriedades de uma tarefa][rest_update_job] (REST do Batch) ou ao modifique a propriedade [CloudJob.Priority][net_cloudjob_priority] (NET do Batch).

Dentro da mesma conta, os trabalhos de prioridade mais alta têm precedência de agendamento sobre os de prioridade mais baixa. Um trabalho com um valor de prioridade superior numa conta não tem precedência de agendamento sobre outro trabalho com um valor de prioridade inferior numa conta diferente.

O agendamento de tarefas no âmbito dos conjuntos é independente. Entre conjuntos diferentes, não é garantido que um trabalho de prioridade superior seja agendado primeiro se o respetivo conjunto associado tiver poucos nós inativos. No mesmo conjunto, as tarefas com o mesmo nível de prioridade têm as mesmas hipóteses de serem agendadas.

### <a name="scheduled-jobs"></a>Tarefas agendadas
[Agendas de tarefas][rest_job_schedules] permitem-lhe criar tarefas recorrentes no âmbito do serviço Batch. Uma agenda de tarefas especifica quando executar tarefas e inclui as especificações das tarefas a executar. Pode especificar a duração da agenda – quando e durante quanto tempo está em vigor – e com que frequência os trabalhos são criados durante o período agendado.

## <a name="task"></a>Tarefa
As tarefas são uma unidade de computação que estão associadas a um trabalho. São executadas num nó. As tarefas são atribuídas a um nó para execução ou são colocadas na fila até que um nó fique livre. Resumindo, as tarefas executam um ou mais programas ou scripts num nó de computação para fazer os trabalhos necessários.

Quando cria uma tarefa, pode especificar:

* A **linha de comandos** da tarefa. Esta é a linha de comandos que executa a aplicação ou script no nó de computação.

    É importante ter em conta que a linha de comandos não é, efetivamente, executada sob uma shell. Por conseguinte,-não é possível tirar partido nativamente das funcionalidades de shell, como a expansão da [variável de ambiente](#environment-settings-for-tasks) expansão (inclui `PATH`). Para tirar partido destas funcionalidades, tem de invocar a shell na linha de comandos, por exemplo, ao iniciar `cmd.exe` nos nós do Windows ou `/bin/sh` no Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se as suas tarefas tiverem de executar uma aplicação ou script que não esteja no `PATH` ou de referenciar variáveis de ambiente, invoque a shell explicitamente na linha de comandos das tarefas.
* Os **ficheiros de recursos** que contêm os dados a serem processados. Estes ficheiros são copiados automaticamente para o nó a partir do Armazenamento de blobs numa conta de Armazenamento do Azure antes de a linha de comando da tarefa ser executada. Para obter mais informações, veja as secções [Tarefa de início](#start-task) e [Ficheiros e diretórios](#files-and-directories).
* As **variáveis de ambiente** de que a aplicação precisa. Para obter mais informações, veja a secção [Definições de ambiente das tarefas](#environment-settings-for-tasks).
* As **restrições** sob as quais a computação deve ocorrer. Por exemplo, as restrições incluem o tempo máximo dentro do qual a tarefa pode ser executada, o número máximo de vezes que uma tarefa falhada deve ser repetida e o tempo máximo durante o qual os ficheiros no diretório de trabalho da tarefa são retidos.
* **Pacotes de aplicações** para implementar o nó de computação no qual a tarefa está agendada para ser executada. Os [pacotes de aplicações](#application-packages) fornecem uma implementação simplificada e o controlo de versões das aplicações que as suas tarefas executam. Os pacotes de aplicações ao nível das tarefas são particularmente úteis em ambientes de conjunto partilhado, em que as diferentes tarefas são executadas num conjunto, e o conjunto não é eliminado quando um trabalho estiver concluído. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
* Referência da **imagem de contentor** no Docker Hub ou num registo privado e as definições adicionais para criar um contentor do Docker no qual a tarefa seja executada no nó. Apenas pode especificar estas informações se o agrupamento tiver uma configuração de contentor.

> [!NOTE]
> A duração máxima de uma tarefa, quando é adicionado à tarefa para quando for concluída, é de 180 dias. As tarefas concluídas mantêm durante sete dias; dados de tarefas não concluídas dentro da duração máxima não estão acessíveis.

Além das tarefas que define para realizar a computação num nó, também são fornecidas pelo serviço Batch as seguintes tarefas especiais:

* [Tarefa de início](#start-task)
* [Tarefa do gestor de tarefas](#job-manager-task)
* [Tarefas de preparação e de lançamento da tarefa](#job-preparation-and-release-tasks)
* Tarefas de várias instâncias (MPI)
* [Dependências de tarefas](#task-dependencies)

### <a name="start-task"></a>Tarefa de início
Ao associar uma **tarefa de início** a um conjunto, pode preparar o ambiente de funcionamento dos respetivos nós. Por exemplo, pode realizar ações como instalar as aplicações que as suas tarefas vão executar ou começar processos em segundo plano. A tarefa de início é executada sempre que um nó é iniciado enquanto permanecer no conjunto, incluindo quando o nó for adicionado pela primeira vez ao conjunto e quando é reiniciado ou a respetiva imagem recriada.

Uma das principais vantagens da tarefa de início é o facto de poder conter todas as informações necessárias para configurar um nó de computação e instalar as aplicações necessárias para a execução de tarefas. Por conseguinte, aumentar o número de nós num conjunto é tão simples como especificar a nova contagem de nó de destino. A tarefa de início fornece ao serviço do Batch as informações necessárias para configurar novos nós e prepará-los para aceitar tarefas.

À semelhança de qualquer tarefa do Batch, pode especificar uma lista de **ficheiros de recursos** no [Armazenamento do Azure][azure_storage], além de uma **linha de comandos** para ser executada. O serviço do Batch começa por copiar os ficheiros de recursos para o nó a partir do Armazenamento do Azure e, depois, executa a linha de comandos. Numa tarefa de início de conjunto, a lista de ficheiros contém, geralmente, a aplicação de tarefa e as respetivas dependências.

Contudo, a tarefa de início também pode incluir dados de referência que todas as tarefas em execução no nó de computação podem utilizar. Por exemplo, a linha de comandos de uma tarefa de início pode fazer uma operação `robocopy` para copiar os ficheiros de aplicação (que foram especificados como ficheiros de recursos e transferidos para o nó) do [diretório de trabalho](#files-and-directories) da tarefa de início para a [pasta partilhada](#files-and-directories) e, em seguida, executar um MSI ou `setup.exe`.

Normalmente, é aconselhável que o serviço Batch aguarde pela conclusão da tarefa de início antes de considerar o nó como estando pronto para receber aplicações, mas esta definição pode ser configurada.

Se uma tarefa inicial falhar num nó de computação, o estado do nó é atualizado para refletir a falha e não são atribuídas tarefas ao nó. Uma tarefa de início pode falhar se houver um problema ao copiar os respetivos ficheiros de recursos do armazenamento ou se o processo executado pela respetiva linha de comandos devolver um código de saída diferente de zero.

Se adicionar ou atualizar a tarefa de início para um agrupamento existente, tem de reiniciar os nós de computação para que a tarefa de início seja aplicada aos mesmos.

>[!NOTE]
> O Batch limita o tamanho total de uma tarefa de início, o que inclui os ficheiros de recurso e as variáveis de ambiente. Se precisar de reduzir o tamanho de uma tarefa de início, pode usar uma de duas abordagens:
>
> 1. Pode utilizar pacotes de aplicações para distribuir aplicações ou dados para todos os nós do agrupamento do Batch. Para obter mais informações sobre os pacotes de aplicações, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
> 2. Pode criar manualmente um arquivo zipado que contenha os seus ficheiros de aplicação. Carregue o arquivo zipado para o Armazenamento do Azure como um blob. Especifique este arquivo como um ficheiro de recursos para a tarefa de início. Antes de executar a linha de comandos da tarefa de início, descomprima o arquivo a partir da linha de comandos. 
>
>    Para descomprimir o arquivo, pode utilizar uma ferramenta de arquivos à sua escolha. Tem de incluir a ferramenta utilizada para descomprimir o arquivo como um ficheiro de recursos para a tarefa de início.
>
>

### <a name="job-manager-task"></a>Tarefa do gestor de tarefas
Geralmente, é utilizada uma **tarefa de gestor de trabalhos** para controlar e/ou monitorizar a execução de trabalhos, por exemplo, para criar e submeter as tarefas de um trabalho, determinar tarefas adicionais a executar e determinar quando o trabalho está concluído. No entanto, as tarefas de gestor de trabalhos não estão confinadas a estas atividades. São tarefas completas que podem realizar quaisquer ações necessárias no âmbito do trabalho. Por exemplo, uma tarefa de gestor de trabalhos pode transferir um ficheiro especificado como um parâmetro, analisar o conteúdo desse ficheiro e submeter mais tarefas com base nesse conteúdo.

A tarefa de gestor de trabalhos é iniciada antes de todas as outras tarefas. Proporciona as seguintes funcionalidades:

* É submetida automaticamente como uma tarefa pelo serviço de Batch quando a tarefa é criada.
* É agendada para execução antes das outras tarefas numa tarefa.
* O seu nó associado é o último a ser removido de um conjunto quando este está a ser reduzido.
* A sua terminação pode ser vinculada à terminação de todas as tarefas na tarefa.
* A tarefa do gestor de trabalhos recebe a prioridade mais elevada quando tem de ser reiniciada. Se um nó inativo não estiver disponível, o serviço Batch pode terminar uma das outras tarefas em execução no conjunto, de modo a criar espaço para a tarefa do gestor de trabalhos ser executada.
* Uma tarefa do gestor de tarefas numa tarefa não tem prioridade sobre as tarefas de outras tarefas. No âmbito das tarefas, apenas as prioridades ao nível da tarefa são respeitadas.

### <a name="job-preparation-and-release-tasks"></a>Tarefas de preparação e de lançamento da tarefa
O Batch fornece tarefas de preparação de trabalhos para a configuração de execução de trabalho prévia. As tarefas de libertação de trabalhos destinam-se a manutenção ou limpeza pós-trabalho.

* **Tarefa de preparação**: Uma tarefa de preparação é executado em todos os nós de computação que estão agendados para executar tarefas antes de qualquer uma das outras tarefas de trabalho são executadas. Pode utilizar uma tarefa de preparação de trabalhos para copiar dados partilhados por todas as tarefas, mas que são exclusivos do trabalho, por exemplo.
* **Tarefa de libertação**: Quando uma tarefa for concluída, uma tarefa de lançamento da tarefa é executada em cada nó no conjunto que executou, pelo menos, uma tarefa. Pode utilizar uma tarefa de libertação de trabalhos para eliminar dados copiados pela tarefa de preparação de trabalhos ou para comprimir e carregar dados de registos de diagnósticos, por exemplo.

Quer a tarefa de preparação de trabalhos, quer a de libertação de trabalhos, permitem-lhe especificar uma linha de comandos a ser executada quando a tarefa é invocada. Oferecem funcionalidades como, por exemplo, transferência de ficheiros, execução elevada, variáveis de ambiente personalizadas, duração de execução máxima e tempo de retenção de ficheiros.

Para obter mais informações sobre as tarefas de lançamento e de preparação da tarefa, consulte [Executar tarefas de preparação e de conclusão da tarefa em nós de computação do Azure Batch](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarefa de várias instâncias
Um [tarefa de várias instâncias](batch-mpi.md) é uma tarefa que está configurada para ser executada simultaneamente em mais do que um nó de computação. Com as tarefas de várias instâncias, pode ativar cenários de computação de alto desempenho que precisam de um grupo de nós de computação alocados em conjunto para processar uma carga de trabalho única (como a Interface de Passagem de Mensagens (Message Passing Interface, MPI)).

Para ver um debate detalhado sobre a execução de trabalhos de MPI no Batch com a biblioteca .NET do Batch, veja [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch (Utilizar tarefas de várias instâncias para executar aplicações de Interface de Passagem de Mensagens (MPI) no Azure Batch)](batch-mpi.md).

### <a name="task-dependencies"></a>Dependências de tarefas
As [dependências de tarefas](batch-task-dependencies.md), como o nome indica, permitem-lhe especificar que uma tarefa depende da conclusão de outras tarefas antes de ser executada. Esta funcionalidade proporciona suporte para situações em que uma tarefa “a jusante” consome o resultado de uma tarefa “a montante” ou quando uma tarefa a montante efetua alguma inicialização que seja necessária para uma tarefa a jusante. Para utilizar esta funcionalidade, tem primeiro de ativar as dependências de tarefas na sua tarefa do Batch. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, pode configurar cenários como os seguintes:

* A *tarefaB* depende da *tarefaA* (a execução da *tarefaB* não é iniciada enquanto a *tarefaA* não tiver sido concluída).
* A *tarefaC* depende da *tarefaA* e da *tarefaB*.
* A *tarefaD* depende de um intervalo de tarefas, como as tarefas *1* à *10*, antes de ser executada.

Veja [Task dependencies in Azure Batch (Dependências de tarefas no Azure Batch)](batch-task-dependencies.md) e o exemplo de código [TaskDependencies][github_sample_taskdeps] no repositório do GitHub [azure-batch-samples][github_samples] para obter detalhes mais aprofundados sobre esta funcionalidade.

## <a name="environment-settings-for-tasks"></a>Definições de ambiente para tarefas
Cada tarefa executada pelo serviço Batch tem acesso às variáveis de ambiente que define nos nós de computação. Estas incluem as variáveis de ambiente definidas pelo serviço Batch ([definidas pelo serviço][msdn_env_vars]) e as variáveis de ambiente personalizadas que pode definir para as suas tarefas. As aplicações e os scripts que as tarefas executam têm acesso a estas variáveis de ambiente durante a execução.

Pode definir variáveis de ambiente personalizadas ao nível da tarefa ou do trabalho ao preencher a propriedade *definições de ambiente* dessas entidades. Por exemplo, veja a operação [Add a task to a job (Adicionar uma tarefa a um trabalho)][rest_add_task] (API REST do Batch) ou as propriedades [CloudTask.EnvironmentSettings][net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings][net_job_env] no .NET do Batch.

O serviço ou aplicação cliente pode obter as variáveis de ambiente de uma tarefa, quer definidas pelo serviço, quer personalizadas, através da operação [Get information about a task][rest_get_task_info] (REST do Batch) (Obter informações sobre uma tarefa) ou ao aceder à propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] (.NET do Batch). Os processos em execução num nó de computação podem aceder a estas e outras variáveis de ambiente no nó, por exemplo, com a sintaxe familiar `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` (Linux).

Pode obter uma lista completa de todas as variáveis de ambiente definidas pelo serviço em [Compute node environment variables (Variáveis de ambiente de nó de computação)][msdn_env_vars].

## <a name="files-and-directories"></a>Ficheiros e diretórios
Cada tarefa tem um *diretório de trabalho* no qual cria zero ou mais ficheiros e diretórios. Este diretório de trabalho pode ser utilizado para armazenar o programa que é executado pela tarefa, os dados que processa e o resultado do processamento feito. Todos os ficheiros e diretórios de uma tarefa são propriedade do utilizador da tarefa.

O serviço Batch expõe uma parte do sistema de ficheiros num nó como o *diretório de raiz*. As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_ROOT_DIR` para aceder ao diretório de raiz. Para obter mais informações sobre como utilizar as variáveis de ambiente, veja [Definições de ambiente das tarefas](#environment-settings-for-tasks).

O diretório de raiz contém a seguinte estrutura de diretórios:

![Estrutura de diretórios do nó de computação][1]

* **shared**: Este diretório proporciona acesso de leitura/gravação para *todos os* tarefas executadas num nó. Qualquer tarefa executada no nó pode criar, ler, atualizar e eliminar ficheiros neste diretório. As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_SHARED_DIR` para aceder a este diretório.
* **arranque**: Este diretório é utilizado por uma tarefa de início como o diretório de trabalho. Todos os ficheiros que tenham sido transferidos para o nó pela tarefa de início são armazenados aqui. A tarefa de início pode criar, ler, atualizar e eliminar ficheiros neste diretório. As tarefas podem consultar a variável de ambiente `AZ_BATCH_NODE_STARTUP_DIR` para aceder a este diretório.
* **Tarefas**: É criado um diretório para cada tarefa executada no nó. O diretório é acedido ao consultar a variável de ambiente `AZ_BATCH_TASK_DIR`.

    Dentro do diretório de cada tarefa, o serviço Batch cria um diretório de trabalho (`wd`) cujo caminho exclusivo é especificado pela variável de ambiente `AZ_BATCH_TASK_WORKING_DIR`. Este diretório proporciona acesso de leitura/escrita à tarefa. A tarefa pode criar, ler, atualizar e eliminar ficheiros neste diretório. Este diretório é mantido com base na restrição *RetentionTime* especificada para a tarefa.

    `stdout.txt` e `stderr.txt`: Estes ficheiros são escritos para a pasta de tarefas durante a execução da tarefa.

> [!IMPORTANT]
> Quando um nó é removido do conjunto, *todos* os ficheiros que estão armazenados no mesmo são removidos.
>
>

## <a name="application-packages"></a>Pacotes de aplicações
A funcionalidade [pacotes de aplicações](batch-application-packages.md) facilita a gestão e a implementação de aplicações nos nós de computação nos seus conjuntos. Pode carregar e gerir facilmente várias versões das aplicações executadas pelas suas tarefas, incluindo os respetivos ficheiros binários e de suporte. Depois, pode implementar automaticamente uma ou mais destas aplicações nos nós de computação do seu conjunto.

Pode especificar os pacotes de aplicações ao nível do conjunto e tarefas. Quando especificar os pacotes de aplicações do conjunto, a aplicação é implementada em todos os nós no conjunto. Quando especificar os pacotes de aplicações de tarefas, a aplicação é implementada apenas nos nós que estão agendados para executarem, pelo menos, uma das tarefas do trabalho, imediatamente antes de a linha de comandos da tarefa ser executada.

O Batch processa os detalhes do trabalho com o Armazenamento do Azure para armazenar os seus pacotes de aplicações e implementá-los em nós de computação, para que o seu código e os custos de gestão possam ser simplificados.

Para saber mais sobre a funcionalidade de pacote de aplicação, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

> [!NOTE]
> Se adicionar pacotes de aplicações de conjunto a um conjunto *existente*, terá de reiniciar os nós de computação para os pacotes de aplicações a serem implementados nos nós.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Duração do nó de computação e de conjunto
Quando conceber a sua solução do Azure Batch, tem de tomar uma decisão de design em relação a como e quando são criados os conjuntos e durante quanto tempo os nós de computação são mantidos disponíveis dentro desses conjuntos.

Num extremo do espetro, pode criar um conjunto para cada trabalho que submeter e eliminá-lo assim que a execução das respetivas tarefas terminar. Desta forma, maximiza a utilização, já que os nós só são alocados quando é necessário e são encerrados logo que fiquem inativos. Apesar de isto significar que o trabalho tem de aguardar que os nós sejam alocados, é importante ter em atenção que as tarefas são agendadas para execução assim que os nós estejam individualmente disponíveis e alocados e que a tarefa inicial esteja concluída. O Batch *não* espera que todos os nós de um conjunto estejam disponíveis para lhes atribuir tarefas. Assim, garante-se a máxima utilização de todos os nós disponíveis.

No outro extremo do espetro, se a prioridade mais elevada for dar início aos trabalhos, pode criar um conjunto antecipadamente e disponibilizar os respetivos nós antes da submissão dos trabalhos. Neste cenário, as tarefas podem ser iniciadas de imediato, mas os nós podem manter-se inativos enquanto aguardam que as tarefas sejam atribuídas.

Geralmente, é utilizada uma abordagem combinada para lidar com cargas variáveis, mas contínuas. Pode ter um conjunto para o qual são submetidos vários trabalhos, mas pode aumentar e reduzir verticalmente o número de nós de acordo com a carga dos trabalhos (veja [Dimensionar os recursos de computação](#scaling-compute-resources) na secção seguinte). Pode fazer estes ajustes reativamente, com base na carga atual, ou pró-ativamente, se for possível prever a carga.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configurar a rede virtual (VNet) e a firewall 

Quando aprovisiona um conjunto de nós de computação no Batch, pode associá-lo a uma sub-rede de uma [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) do Azure. Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).  

### <a name="vnet-requirements"></a>Requisitos de VNet
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Para obter mais informações sobre como configurar um agrupamento do Batch numa VNet, veja [Criar um agrupamento de máquinas virtuais com a rede virtual](batch-virtual-network.md).

## <a name="scaling-compute-resources"></a>Dimensionar os recursos de computação
Com o [dimensionamento automático](batch-automatic-scaling.md), pode fazer com que o serviço Batch ajuste dinamicamente o número de nós de computação num conjunto de acordo com a carga de trabalho e a utilização de recursos atual do seu cenário de computação. Esta funcionalidade permite-lhe reduzir o custo global de execução da sua aplicação ao utilizar apenas os recursos de que precisa e libertar aqueles de que não precisa.

A ativação do dimensionamento automático é feita ao escrever uma [fórmula de dimensionamento automático](batch-automatic-scaling.md#automatic-scaling-formulas) e associá-la a um conjunto. O serviço Batch utiliza esta fórmula para determinar o número de destino de nós no conjunto para o próximo intervalo de dimensionamento (um intervalo que pode configurar). Pode especificar as definições de dimensionamento automático de um conjunto quando o criar ou ativar o dimensionamento num conjunto mais tarde. Também pode atualizar as definições de dimensionamento num conjunto preparado para dimensionamento.

Por exemplo, talvez um trabalho que requer que submeta um grande número de tarefas para execução. Pode atribuir uma fórmula de dimensionamento ao conjunto que ajusta o número de nós no conjunto com base no número atual de tarefas na fila, bem como na taxa de conclusão das tarefas desse trabalho. O serviço Batch avalia periodicamente a fórmula e redimensiona o conjunto com base na carga de trabalho e nas outras definições de fórmula. O serviço adiciona nós conforme necessário quando existe um grande número de tarefas em fila e remove nós quando não existem tarefas em execução ou em fila.

Uma fórmula de dimensionamento pode basear-se nas métricas seguintes:

* **Métricas de tempo**: baseadas em estatísticas recolhidas a cada cinco minutos no número de horas especificado.
* **Métricas de recurso**: baseadas na utilização da CPU, da largura de banda, da memória e no número de nós.
* **Métricas de tarefas**: baseadas no estado da tarefa, como *Ativa* (em fila), *Em Execução* ou *Concluída*.

Quando o dimensionamento automático diminuir o número de nós de computação de um conjunto, tem de pensar como vai processar as tarefas que estão a ser executadas no momento da operação de diminuição. Para dar resposta a esta situação, o Batch disponibiliza uma *opção de desalocação de nós*, a qual pode incluir nas suas fórmulas. Por exemplo, pode especificar que as tarefas em execução são paradas imediatamente e recolocadas em fila para execução noutro nó ou que podem ser concluídas antes de o nó ser removido do conjunto.

Para obter mais informações sobre o dimensionamento automático de uma aplicação, consulte [Dimensionar automaticamente nós de computação num conjunto do Azure Batch](batch-automatic-scaling.md).

> [!TIP]
> Para maximizar a utilização de recursos de computação, defina o número de destino de nós para zero no final de uma tarefa, mas permita que as tarefas em execução sejam concluídas.
>
>

## <a name="security-with-certificates"></a>Segurança com certificados
Normalmente, tem de utilizar certificados quando encriptar ou desencriptar informações confidenciais relativas a tarefas, como a chave de uma [conta de Armazenamento do Azure][azure_storage]. Para suportar esta situação, pode instalar certificados em nós. Os segredos encriptados são transmitidos para as tarefas através dos parâmetros da linha de comandos ou incorporados num dos recursos da tarefa, sendo que os certificados instalados podem ser utilizados para desencriptá-los.

Utilize a operação [Adicionar certificado][rest_add_cert] (REST do Batch) ou o método [CertificateOperations.CreateCertificate][net_create_cert] (.NET do Batch) para adicionar um certificado a uma conta do Batch. Em seguida, pode associar o certificado a um conjunto novo ou existente. Quando um certificado é associado a um conjunto, o serviço Batch instala o certificado em cada nó no conjunto. O serviço Batch instala os certificados adequados quando o nó é iniciado, antes de iniciar qualquer tarefa (incluindo a tarefa de início e a tarefa do gestor de trabalhos).

Se adicionar certificados a um conjunto *existente*, terá de reiniciar os nós de computação para os certificados a serem implementados nos nós.

## <a name="error-handling"></a>Processamento de erros
Pode considerar necessário processar falhas de tarefas e de aplicações dentro da sua solução do Batch.

### <a name="task-failure-handling"></a>Processamento de falhas de tarefas
As falhas de tarefas enquadram-se nestas categorias:

* **Falha de pré-processamento**

    Se ocorrer uma falha ao iniciar uma tarefa, é definido um erro de pré-processamento para a mesma.  

    Os erros de pré-processamento podem dever-se ao facto de os ficheiros de recursos da tarefa terem sido movidos, de a conta de Armazenamento já não estar disponível ou de ter ocorrido outro problema que impediu a cópia bem-sucedida dos ficheiros para o nó.

* **Falhas de carregamento de ficheiros**

    Se o carregamento de ficheiros especificados para uma tarefa falhar por algum motivo, é definido um erro de carregamento para a mesma.

    Os erros de carregamento de ficheiros podem ocorrer se a SAS fornecida para aceder ao Armazenamento do Azure for inválida ou não fornecer permissões de escrita, se a conta de utilizador já não estiver disponíveis ou se tiver ocorrido outro problema que impediu a cópia bem-sucedida dos ficheiros para o nós.    

* **Falhas de aplicações**

    O processo especificado pela linha de comandos da tarefa também pode falhar. Considera-se que o processo falhou quando o processo que a tarefa executa devolve um código de saída diferente de zero (veja *Códigos de saída de tarefas* na secção seguinte).

    Relativamente às falhas de aplicações, pode configurar o Batch para repetir automaticamente a tarefa até um número de vezes especificado.

* **Falhas de restrições**

    Pode definir uma restrição que especifica a duração de execução máxima de um trabalho, a *maxWallClockTime*. Isto pode ser útil para terminar tarefas que não progridem.

    Quando o tempo máximo tiver sido excedido, a tarefa é marcada como *concluída*, mas o código de saída é definido como `0xC000013A` e o campo *schedulingError* é marcado como `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Depurar falhas de aplicações
* `stderr` e `stdout`

    Durante a execução, uma aplicação poderá produzir resultados de diagnóstico que pode utilizar para resolver problemas. Como foi mencionado na secção [Ficheiros e diretórios](#files-and-directories) acima, o serviço Batch escreve resultados padrão e resultados de erro padrão nos ficheiros `stdout.txt` e `stderr.txt` no diretório da tarefa no nó de computação. Pode utilizar o portal do Azure ou um dos SDKs do Batch para transferir estes ficheiros. Por exemplo, pode utilizar [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] na biblioteca .NET do Batch, para obter estes e outros ficheiros para fins de resolução de problemas.

* **Códigos de saída de tarefa**

    Conforme mencionado anteriormente, o serviço Batch marca uma tarefa como falhada se o processo executado por essa tarefa devolver um código de saída diferente de zero. Quando uma tarefa executa um processo, o Batch preenche a propriedade do código de saída dessa tarefa com o *código de devolução do processo*. É importante que tenha em atenção que o código de saída de uma tarefa **não** é determinado pelo serviço do Batch. É determinado um código de saída de tarefas pelo próprio processo ou o sistema operativo no qual o processo foi executado.

### <a name="accounting-for-task-failures-or-interruptions"></a>Explicação de falhas ou interrupções de tarefas
Por vezes, as tarefas podem falhar ou ser interrompidas. A própria aplicação da tarefa pode falhar, o nó no qual a tarefa está a ser executada pode ser reiniciado ou o nó pode ser removido do conjunto durante uma operação de redimensionamento se política de desalocação do conjunto estiver definida para remover nós imediatamente sem aguardar que as tarefas estejam concluídas. Em todos os casos, o Batch pode recolocar automaticamente a tarefa na fila para execução noutro nó.

Também é possível que um problema intermitente faça com que uma tarefa fique suspensa ou demore demasiado tempo a ser executada. Pode definir o intervalo de execução máxima de uma tarefa. Se o intervalo de execução máxima for excedido, o serviço de Batch interrompe a aplicação de tarefas.

### <a name="connecting-to-compute-nodes"></a>Ligar a nós de computação
Pode iniciar sessão remotamente num nó de computação para realizar depurações e resolução de problemas adicionais. Pode utilizar o portal do Azure para transferir um ficheiro do protocolo RDP (Remote Desktop Protocol) para nós do Windows e obter informações de ligação Secure Shell (SSH) para nós do Linux. Também o pode fazer com as APIs do Batch, como, por exemplo, [.NET do Batch][net_rdpfile] ou [Python do Batch](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Para ligar a um nó através de DRP ou SSH, tem de criar, primeiro, um utilizador no nó. Para tal, pode utilizar o portal do Azure, [adicionar uma conta de utilizador a um nó][rest_create_user] com a API REST do Batch, chamar o método [ComputeNode.CreateComputeNodeUser][net_create_user] no .NET do Batch ou chamar o método [add_user][py_add_user] no módulo Python do Batch.
>
>

Se precisar de restringir ou desativar o acesso RDP ou SSH para computação de nós, consulte [Configurar ou desativar acesso remoto para computação de nós num conjunto do Azure Batch](pool-endpoint-configuration.md).

### <a name="troubleshooting-problematic-compute-nodes"></a>Resolução de problemas de nós de computação problemáticos
Em situações onde algumas das suas tarefas estejam a falhar, a aplicação cliente ou o serviço Batch podem examinar os metadados das tarefas com falhas para identificar um nó a funcionar incorretamente. Cada nó num conjunto recebe um ID exclusivo e o nó no qual é executada uma tarefa está incluído nos metadados da tarefa. Depois de ter identificado um nó de problema, pode tomar várias medidas no mesmo:

* **Reiniciar o nó** ([REST][rest_reboot] | [.NET][net_reboot])

    Reiniciar o nó pode, por vezes, limpar problemas latentes, como processos bloqueados ou falhados. Tenha em atenção que, se o conjunto utilizar uma tarefa de início ou o seu trabalho utilizar uma tarefa de preparação de trabalho, estas são executados quando o nó é reiniciado.
* **Recriar imagem do nó** ([REST][rest_reimage] | [.NET][net_reimage])

    Isto reinstala o sistema operativo no nó. Tal como acontece com o reinício de um nó, as tarefas de início e as tarefas de preparação da tarefa são novamente executadas depois de ter sido recriada a imagem do nó.
* **Remover o nó do conjunto** ([REST][rest_remove] | [.NET][net_remove])

    Por vezes, é necessário remover completamente o nó do conjunto.
* **Desativar o agendamento de tarefas no nó** ([REST][rest_offline] | [.NET][net_offline])

    Esta ação coloca o nó offline de forma eficaz, para que não lhe sejam atribuídas mais tarefas, mas permite que o nó permaneça em execução e no conjunto. Isto permite-lhe investigar melhor a causa das falhas sem perder os dados da tarefa com falhas e sem que o nó cause mais falhas nas tarefas. Por exemplo, pode desativar o agendamento de tarefas no nó e, em seguida, [iniciar sessão remotamente](#connecting-to-compute-nodes) para examinar os registos de eventos do nó ou realizar outras ações de resolução de problemas. Depois de terminar a investigação, pode colocar novamente o nó online ao ativar o agendamento de tarefas ([REST][rest_online] | [.NET][net_online]) ou realizar uma das outras ações descritas acima.

> [!IMPORTANT]
> Em cada ação descrita nesta secção -- reiniciar, recriar imagem, remover e desativar o agendamento de tarefas --, pode especificar a forma como as tarefas atualmente em execução no nó são processadas quando realizar a ação. Por exemplo, quando desativar o agendamento de tarefas num nó com a biblioteca de cliente .NET do Batch, pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption][net_offline_option] para especificar se pretende **Terminar** as tarefas em execução, **Recolocá-las na fila** para agendamento noutros nós ou permitir que as tarefas em execução sejam concluídas antes de realizar a ação (**TaskCompletion**).
>
>

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [Ferramentas e APIs do Batch](batch-apis-tools.md) disponíveis para criação de soluções para o Batch.
* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](quick-run-dotnet.md) ou [Python](quick-run-python.md). Estes inícios rápidos orientam-no numa aplicação de exemplo que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.
* Transfira e instale o [Batch Explorer][batch_labs] para utilizar enquanto desenvolve as soluções do Batch. Utilize o Batch Explorer para ajudar a criar, depurar e monitorizar aplicações do Azure Batch. 
* Consulte os recursos da comunidade, incluindo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), o [repositório da Comunidade em Lote](https://github.com/Azure/Batch) e o [fórum do Azure Batch][batch_forum] no MSDN. 

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchExplorer/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: https://docs.microsoft.com/python/azure/?view=azure-python

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
