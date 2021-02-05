---
title: Verifique se há erros na piscina e node
description: Este artigo abrange as operações de fundo que podem ocorrer, juntamente com erros a verificar e como evitá-las ao criar piscinas e nós.
author: mscurrell
ms.author: markscu
ms.date: 02/03/2020
ms.topic: how-to
ms.openlocfilehash: 2b67eada5dfa89f95e2c9ae045c6bbe3fa0bb1ce
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576317"
---
# <a name="check-for-pool-and-node-errors"></a>Verifique se há erros na piscina e node

Quando você está criando e gerindo piscinas Azure Batch, algumas operações acontecem imediatamente. A deteção de falhas para estas operações é geralmente simples, pois são devolvidas imediatamente pela API, CLI ou UI. No entanto, algumas operações são assíncronos e executadas em segundo plano, demorando vários minutos a ser concluídas.

Verifique se definiu as suas aplicações para implementar uma verificação completa de erros, especialmente para operações assíncronos. Isto pode ajudá-lo a identificar e diagnosticar rapidamente problemas.

Este artigo descreve formas de detetar e evitar falhas nas operações de fundo que podem ocorrer para piscinas e nós de piscina.

## <a name="pool-errors"></a>Erros na piscina

### <a name="resize-timeout-or-failure"></a>Redimensionar o tempo limite ou o insucesso

Ao criar uma nova piscina ou redimensionar uma piscina existente, especifique o número de nós alvo. A operação de criação ou redimensionamento termina imediatamente, mas a atribuição real de novos nós ou a remoção dos nós existentes podem demorar alguns minutos. Pode especificar o tempo limite de redimensionar na [API de criar](/rest/api/batchservice/pool/add) ou [redimensionar.](/rest/api/batchservice/pool/resize) Se o Batch não conseguir obter o número de nós alvo durante o período de tempo de redimensionamento, a piscina entra num estado estável e relata erros de redimensionamento.

A propriedade [ResizeError](/rest/api/batchservice/pool/get#resizeerror) para a avaliação mais recente lista os erros ocorridos.

As causas comuns para erros de redimensionar incluem:

- Redimensionar o tempo é muito curto
  - Na maioria das circunstâncias, o tempo limite de 15 minutos é suficiente para que os nós de piscina sejam alocados ou removidos.
  - Se estiver a alocar um grande número de nós, recomendamos que se ajuste o tempo limite de redimensionamento para 30 minutos. Por exemplo, quando você está redimensionando para mais de 1.000 nós de uma imagem Azure Marketplace, ou para mais de 300 nós de uma imagem VM personalizada.
- Quota-base insuficiente
  - Uma conta Batch é limitada no número de núcleos que pode alocar em todas as piscinas. O lote deixa de atribuir nós uma vez que essa quota tenha sido atingida. Pode [aumentar](./batch-quota-limit.md) a quota principal para que o Batch possa alocar mais nós.
- IPs de sub-redes insuficientes quando uma [piscina está em uma rede virtual](./batch-virtual-network.md)
  - Uma sub-rede de rede virtual deve ter endereços IP não atribuídos suficientes para alocar a todos os nódos de piscina solicitados. Caso contrário, os nós não podem ser criados.
- Recursos insuficientes quando uma [piscina está numa rede virtual](./batch-virtual-network.md)
  - Pode criar recursos como equilibradores de carga, IPs públicos e grupos de segurança de rede na mesma subscrição que a conta Batch. Verifique se as quotas de subscrição são suficientes para estes recursos.
- Grandes piscinas com imagens VM personalizadas
  - Grandes piscinas que usam imagens VM personalizadas podem demorar mais tempo a alocar e redimensionar os intervalos de tempo podem ocorrer.  Consulte [Criar uma piscina com a Galeria de Imagens Partilhadas](batch-sig-images.md) para obter recomendações sobre limites e configuração.

### <a name="automatic-scaling-failures"></a>Falhas automáticas de escala

Pode definir O Lote Azure para escalar automaticamente o número de nós numa piscina. Você define os parâmetros para a [fórmula de escala automática para uma piscina](./batch-automatic-scaling.md). O serviço Batch utilizará então a fórmula para avaliar periodicamente o número de nós na piscina e definirá um novo número-alvo.

Os seguintes tipos de problemas podem ocorrer quando se utilizam a escala automática:

- A avaliação automática de escalonamento falha.
- A operação de redimensione resultante falha e os tempos esgotados.
- Um problema com a fórmula de escala automática leva a valores de alvo de nó incorretos. O redimensionar funciona ou acaba.

Para obter informações sobre a última avaliação automática de escala, utilize a propriedade [autoScaleRun.](/rest/api/batchservice/pool/get#autoscalerun) Esta propriedade reporta o tempo de avaliação, os valores e o resultado, e quaisquer erros de desempenho.

O [evento completo de redimensionar](./batch-pool-resize-complete-event.md) a piscina capta informações sobre todas as avaliações.

### <a name="pool-deletion-failures"></a>Falhas de eliminação de piscinas

Quando apaga uma piscina que contém nós, primeiro o Lote elimina os nós. Esta ação pode demorar vários minutos a concluir. Depois disso, o Batch elimina o objeto da piscina em si.

O lote define o estado da [piscina](/rest/api/batchservice/pool/get#poolstate) para **apagar** durante o processo de eliminação. A aplicação de chamada pode detetar se a eliminação da piscina está a demorar muito tempo usando as propriedades **state** e **stateTransitionTime.**

## <a name="node-errors"></a>Erros no nó

Mesmo quando o Batch atribui com sucesso os nós numa piscina, vários problemas podem fazer com que alguns dos nós não sejam saudáveis e incapazes de executar tarefas. Estes nós ainda incorrem em acusações, por isso é importante detetar problemas para evitar pagar por nós que não podem ser usados. Além de erros comuns no nó, conhecer o estado de [trabalho](/rest/api/batchservice/job/get#jobstate) atual é útil para a resolução de problemas.

### <a name="start-task-failures"></a>Iniciar falhas de tarefa

É melhor especificar uma tarefa de [partida](/rest/api/batchservice/pool/add#starttask) opcional para uma piscina. Como em qualquer tarefa, pode utilizar uma linha de comando e ficheiros de recursos para descarregar a partir do armazenamento. A tarefa inicial é executada para cada nó depois de ter sido iniciado. A propriedade **waitForSuccess** especifica se o Batch aguarda até que a tarefa inicial termine com sucesso antes de agendar quaisquer tarefas para um nó.

E se configurar o nó para esperar pela conclusão da tarefa inicial com sucesso, mas a tarefa inicial falha? Nesse caso, o nó não será utilizável, mas continuará a incorrer em acusações.

Pode detetar falhas de tarefa de início utilizando o [resultado](/rest/api/batchservice/computenode/get#taskexecutionresult) e [falhasInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) propriedades da propriedade do nó [inicial de nível inicial.](/rest/api/batchservice/computenode/get#starttaskinformation)

Uma tarefa inicial falhada também faz com que Batch desembarque o [estado](/rest/api/batchservice/computenode/get#computenodestate) do nó para **começar** se  **a espera ForSuccess** foi definido como **verdadeiro**.

Como em qualquer tarefa, pode haver muitas causas para uma falha de tarefa inicial. Para resolver problemas, verifique o stdout, stderr e quaisquer outros ficheiros de registo específicos de tarefas.

As tarefas de arranque devem ser re-participantes, pois é possível que a tarefa inicial seja executada várias vezes no mesmo nó; a tarefa inicial é executada quando um nó é remimagemed ou reiniciado. Em casos raros, uma tarefa inicial será executada após um evento ter causado um reboot de nó, onde um dos discos operativos ou disquetes efémeros foi reimagemed enquanto o outro não estava. Uma vez que as tarefas de arranque do Batch (como todas as tarefas do Lote) são executadas a partir do disco efémero, este não é normalmente um problema, mas em alguns casos em que a tarefa inicial é instalar uma aplicação no disco do sistema operativo e manter outros dados no disco efémero, isso pode causar problemas porque as coisas estão dessincronizadas. Proteja a sua aplicação em conformidade se estiver a utilizar ambos os discos.

### <a name="application-package-download-failure"></a>Falha no descarregamento do pacote de aplicações

Você pode especificar um ou mais pacotes de aplicação para uma piscina. O lote descarrega os ficheiros de pacotes especificados para cada nó e descomprimia os ficheiros após o início do nó, mas antes de as tarefas serem agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicação. Por exemplo, para copiar ficheiros para um local diferente ou para executar a configuração.

A propriedade de [erros](/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha no descarregamento e descomprimir um pacote de aplicações; o estado do nó está definido para **inutilizável**.

### <a name="container-download-failure"></a>Falha no download do contentor

Pode especificar uma ou mais referências de contentores numa piscina. O lote descarrega os recipientes especificados para cada nó. A propriedade de [erros](/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha no descarregamento de um recipiente e define o estado do nó para **inutilizável**.

### <a name="node-os-updates"></a>Atualizações de nó noss

Para piscinas windows, `enableAutomaticUpdates` está definido `true` por padrão. É aconselhável permitir atualizações automáticas, mas podem interromper o progresso da tarefa, especialmente se as tarefas forem longas. Pode definir este valor `false` se precisar de garantir que uma atualização de SO não acontece inesperadamente.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

Azure Batch pode definir o [estado do nó](/rest/api/batchservice/computenode/get#computenodestate) **inutilizável** por muitas razões. Com o estado do nó definido para **inutilizável,** as tarefas não podem ser agendadas para o nó, mas ainda assim incorre em acusações.

Nós num estado **inutilizável,** mas sem [erros](/rest/api/batchservice/computenode/get#computenodeerror) significa que Batch é incapaz de comunicar com o VM. Neste caso, o Batch tenta sempre recuperar o VM. O lote não tentará automaticamente recuperar VMs que não tenham instalado pacotes de aplicações ou contentores, mesmo que o seu estado seja **inutilizável**.

Se o Batch conseguir determinar a causa, a propriedade de [erros](/rest/api/batchservice/computenode/get#computenodeerror) de nó reporta-a.

Exemplos adicionais de causas para os **nóns inutilizáveis** incluem:

- Uma imagem VM personalizada é inválida. Por exemplo, uma imagem que não está devidamente preparada.

- Um VM é movido devido a uma falha de infraestrutura ou a uma atualização de baixo nível. O lote recupera o nó.

- Foi implementada uma imagem VM em hardware que não a suporta. Por exemplo, tentar executar uma imagem CentOS HPC numa [VM Standard_D1_v2.](../virtual-machines/dv2-dsv2-series.md)

- Os VMs estão numa [rede virtual Azure,](batch-virtual-network.md)e o tráfego foi bloqueado para portas-chave.

- Os VMs estão numa rede virtual, mas o tráfego de saída para o armazenamento do Azure está bloqueado.

- Os VMs estão numa rede virtual com uma configuração DE DNS do cliente e o servidor DNS não consegue resolver o armazenamento do Azure.

### <a name="node-agent-log-files"></a>Ficheiros de registo de agente de nó

O processo de agente Batch que funciona em cada nó de piscina pode fornecer ficheiros de registo que podem ser úteis se precisar de contactar suporte sobre um problema de nó de piscina. Os ficheiros de registo de um nó podem ser carregados através do portal Azure, Do Batch Explorer ou de uma [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e guardar os ficheiros de registo. Depois, pode apagar o nó ou a piscina para economizar o custo dos nós em funcionamento.

### <a name="node-disk-full"></a>Disco de nó cheio

A unidade temporária para um VM de nó de piscina é usada pelo Batch para ficheiros de trabalho, ficheiros de tarefas e ficheiros partilhados, tais como:

- Ficheiros de pacotes de aplicações
- Ficheiros de recursos de tarefa
- Ficheiros específicos para aplicações descarregados para uma das pastas Batch
- Stdout e stderr arquivos para cada execução de aplicação de tarefa
- Ficheiros de saída específicos para aplicações

Alguns destes ficheiros só são escritos uma vez quando os nós de piscina são criados, tais como pacotes de aplicação de piscina ou ficheiros de recursos de tarefa de início de piscina. Mesmo que apenas escrito uma vez quando o nó é criado, se estes ficheiros forem demasiado grandes, podem preencher a unidade temporária.

Outros ficheiros são escritos para cada tarefa que é executada num nó, como stdout e stderr. Se um grande número de tarefas executadas no mesmo nó e/ou os ficheiros de tarefas forem demasiado grandes, podem preencher a unidade temporária.

O tamanho da unidade temporária depende do tamanho do VM. Uma consideração ao escolher um tamanho VM é garantir que a unidade temporária tem espaço suficiente.

- No portal Azure ao adicionar uma piscina, a lista completa de tamanhos VM pode ser exibida e existe uma coluna 'Tamanho do Disco de Recurso'.
- Os artigos que descrevem todos os tamanhos de VM têm tabelas com uma coluna 'Armazenamento Temporário'; por [exemplo, tamanhos de VM otimizados compute](../virtual-machines/sizes-compute.md)

Para ficheiros escritos por cada tarefa, pode ser especificado um tempo de retenção para cada tarefa que determina quanto tempo os ficheiros de tarefa são mantidos antes de serem automaticamente limpos. O tempo de retenção pode ser reduzido para reduzir os requisitos de armazenamento.

Se o disco temporário ficar sem espaço (ou estiver muito perto de ficar sem espaço), o nó deslocar-se-á para estado [inutilizável](/rest/api/batchservice/computenode/get#computenodestate) e será reportado um erro de nó dizendo que o disco está cheio.

Se não tem certeza do que está ocupando espaço no nó, tente remoer para o nó e investiga manualmente para onde o espaço foi. Também pode utilizar a API dos [Ficheiros de Lista](/rest/api/batchservice/file/listfromcomputenode) de Lote para examinar ficheiros em pastas geridas por Batch (por exemplo, saídas de tarefas). Note que esta API apenas lista ficheiros nos diretórios geridos do Batch. Se as suas tarefas criaram ficheiros noutro lado, não os verá.

Certifique-se de que todos os dados necessários foram recuperados do nó ou enviados para uma loja durável e, em seguida, elimine os dados necessários para libertar o espaço.

Pode eliminar trabalhos antigos ou tarefas antigas concluídas cujos dados de tarefa ainda estão nos nós. Veja na [coleção RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) no nó, ou nos [ficheiros do nó.](/rest/api/batchservice/file/listfromcomputenode) A supressão de um emprego eliminará todas as tarefas no trabalho; a eliminação das tarefas no trabalho irá desencadear dados nos diretórios de tarefas do nó a serem eliminados, libertando assim espaço. Uma vez libertado espaço suficiente, reinicie o nó e deverá sair do estado "Inutilizável" e voltar a "Ocio".

Para recuperar um nó inutilizável nas piscinas [VirtualMachineConfiguration,](/rest/api/batchservice/pool/add#virtualmachineconfiguration) pode remover um nó da piscina utilizando os [nós de remoção API](/rest/api/batchservice/pool/removenodes). Em seguida, você pode cultivar a piscina novamente para substituir o nó mau por um fresco. Para piscinas [CloudServiceConfiguration,](/rest/api/batchservice/pool/add#cloudserviceconfiguration) pode re-imagem do nó através da [imagem de re-imagem do Lote API](/rest/api/batchservice/computenode/reimage). Isto vai limpar todo o disco. A re-imagem não é suportada atualmente para piscinas [virtualMachineConfiguration.](/rest/api/batchservice/pool/add#virtualmachineconfiguration)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [verificação de erros de trabalho e de tarefa.](batch-job-task-error-checking.md)
- Saiba mais [sobre as melhores práticas](best-practices.md) para trabalhar com a Azure Batch.
