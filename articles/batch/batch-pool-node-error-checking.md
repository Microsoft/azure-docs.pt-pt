---
title: Verificar se há erros de pool e de nó-lote do Azure
description: Erros a serem verificados e como evitá-los ao criar pools e nós
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 3c8e189e84e0a467125995b3e2d633c285eb7367
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350061"
---
# <a name="check-for-pool-and-node-errors"></a>Verificar se há erros de pool e de nó

Quando você estiver criando e gerenciando pools do lote do Azure, algumas operações acontecem imediatamente. No entanto, algumas operações são assíncronas e são executadas em segundo plano, levando vários minutos para serem concluídas.

Detectar falhas de operações que ocorrem imediatamente é simples, pois todas as falhas são retornadas imediatamente pela API, CLI ou interface do usuário.

Este artigo aborda as operações em segundo plano que podem ocorrer para pools e nós de pool. Ele especifica como você pode detectar e evitar falhas.

## <a name="pool-errors"></a>Erros de pool

### <a name="resize-timeout-or-failure"></a>Redimensionar tempo limite ou falha

Ao criar um novo pool ou redimensionar um pool existente, você especifica o número de destino de nós.  A operação de criação ou redimensionamento é concluída imediatamente, mas a alocação real de novos nós ou a remoção de nós existentes pode levar vários minutos.  Especifique o tempo limite de redimensionamento na API de [criação](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionamento](https://docs.microsoft.com/rest/api/batchservice/pool/resize) . Se o lote não puder obter o número de destino de nós durante o período de tempo limite de redimensionamento, o pool entrará em um estado estável e redimensionará os erros.

A propriedade [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) para a avaliação mais recente lista os erros que ocorreram.

As causas comuns para erros de redimensionamento incluem:

- O tempo limite de redimensionamento é muito curto
  - Na maioria das circunstâncias, o tempo limite padrão de 15 minutos é longo o suficiente para que os nós do pool sejam alocados ou removidos.
  - Se você estiver alocando um grande número de nós, é recomendável definir o tempo limite de redimensionamento como 30 minutos. Por exemplo, quando você está redimensionando para mais de 1.000 nós de uma imagem do Azure Marketplace ou para mais de 300 nós de uma imagem de VM personalizada.
- Cota de núcleo insuficiente
  - Uma conta do lote é limitada no número de núcleos que ele pode alocar em todos os pools. O lote para de alocar nós depois que a cota é atingida. Você [pode aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) a cota de núcleos para que o lote possa alocar mais nós.
- IPs de sub-rede insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma sub-rede de rede virtual deve ter endereços IP não atribuídos suficientes para alocar a cada nó de pool solicitado. Caso contrário, os nós não poderão ser criados.
- Recursos insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Você pode criar recursos como balanceadores de carga, IPs públicos e grupos de segurança de rede na mesma assinatura que a conta do lote. Verifique se as cotas de assinatura são suficientes para esses recursos.
- Grandes pools com imagens de VM personalizadas
  - Os pools grandes que usam imagens de VM personalizadas podem levar mais tempo para alocar e redimensionar os tempos limite podem ocorrer.  Consulte [criar um pool com a Galeria de imagens compartilhadas](batch-sig-images.md) para obter recomendações sobre limites e configurações.

### <a name="automatic-scaling-failures"></a>Falhas de dimensionamento automático

Você também pode definir o lote do Azure para dimensionar automaticamente o número de nós em um pool. Você define os parâmetros para a [fórmula de dimensionamento automático para um pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). O serviço de lote usa a fórmula para avaliar periodicamente o número de nós no pool e definir um novo número de destino. Os seguintes tipos de problemas podem ocorrer:

- A avaliação de dimensionamento automático falha.
- A operação de redimensionamento resultante falha e atinge o tempo limite.
- Um problema com a fórmula de dimensionamento automático leva a valores de destino de nó incorretos. O redimensionamento funciona ou atinge o tempo limite.

Você pode obter informações sobre a última avaliação de dimensionamento automático usando a propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) . Essa propriedade relata o tempo de avaliação, os valores e o resultado e quaisquer erros de desempenho.

O [evento redimensionamento de pool concluído](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) captura informações sobre todas as avaliações.

### <a name="delete"></a>Eliminar

Quando você exclui um pool que contém nós, o primeiro lote exclui os nós. Em seguida, ele exclui o objeto de pool em si. Pode levar alguns minutos para que os nós do pool sejam excluídos.

Lote define o [estado do pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) a ser **excluído** durante o processo de exclusão. O aplicativo de chamada pode detectar se a exclusão do pool está demorando muito tempo usando as propriedades **State** e **stateTransitionTime** .

## <a name="pool-compute-node-errors"></a>Erros de nó de computação do pool

Mesmo quando o lote aloca nós em um pool com êxito, vários problemas podem fazer com que alguns nós não estejam íntegros e não possam executar tarefas. Esses nós ainda incorrem em encargos, portanto, é importante detectar problemas para evitar o pagamento de nós que não podem ser usados. Além dos erros de nó comuns, saber se o [estado](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate) atual do trabalho é útil para solução de problemas.

### <a name="start-task-failures"></a>Falhas na tarefa inicial

Talvez você queira especificar uma tarefa de [início](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) opcional para um pool. Assim como em qualquer tarefa, você pode usar uma linha de comando e arquivos de recurso para baixar do armazenamento. A tarefa inicial é executada para cada nó depois de ser iniciada. A propriedade **waitForSuccess** especifica se o lote aguarda até que a tarefa inicial seja concluída com êxito antes de agendar todas as tarefas para um nó.

E se você tiver configurado o nó para aguardar a conclusão bem-sucedida da tarefa inicial, mas a tarefa inicial falhar? Nesse caso, o nó não será utilizável, mas ainda incorrerá em encargos.

Você pode detectar falhas de tarefa inicial usando as propriedades [Result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade de nó [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) de nível superior.

Uma tarefa de inicialização com falha também faz com que o lote defina o [estado](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) do nó como **starttaskfailed** se **waitForSuccess** foi definido como **true**.

Assim como ocorre com qualquer tarefa, pode haver muitas causas para a falha da tarefa inicial.  Para solucionar problemas, verifique stdout, stderr e outros arquivos de log específicos da tarefa.

As tarefas iniciais devem ser reentrante novamente, pois é possível que a tarefa inicial seja executada várias vezes no mesmo nó; a tarefa inicial é executada quando um nó é recriado ou reinicializado. Em casos raros, uma tarefa inicial será executada Depois que um evento tiver causado uma reinicialização de nó, onde um dos discos de sistema operacional ou efêmero foi refeita a imagem enquanto o outro não estava. Como as tarefas de início do lote (como todas as tarefas do lote) são executadas a partir do disco efêmero, isso normalmente não é um problema, mas em alguns casos em que a tarefa inicial está instalando um aplicativo no disco do sistema operacional e mantendo outros dados no disco efêmero, isso pode causar problemas porque as coisas estão fora de sincronia. Proteja seu aplicativo adequadamente se você estiver usando ambos os discos.

### <a name="application-package-download-failure"></a>Falha no download do pacote de aplicativos

Você pode especificar um ou mais pacotes de aplicativos para um pool. O lote baixa os arquivos de pacote especificados para cada nó e descompacta os arquivos após o nó ser iniciado, mas antes que as tarefas sejam agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicativos. Por exemplo, para copiar arquivos para um local diferente ou para executar a instalação.

A propriedade de [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha ao baixar e cancelar a compactação de um pacote de aplicativos; o estado do nó é definido como **inutilizável**.

### <a name="container-download-failure"></a>Falha no download do contêiner

Você pode especificar uma ou mais referências de contêiner em um pool. O lote baixa os contêineres especificados para cada nó. A propriedade de [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) de nó relata uma falha ao baixar um contêiner e define o estado do nó como **inutilizável**.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O lote do Azure pode definir o [estado do nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) como **inutilizável** por vários motivos. Com o estado do nó definido como **inutilizável**, as tarefas não podem ser agendadas para o nó, mas ainda incorrem em encargos.

Nós em um estado **inutilizável** , mas sem [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) significa que o lote não pode se comunicar com a VM. Nesse caso, o lote sempre tenta recuperar a VM. O lote não tentará automaticamente recuperar as VMs que falharam ao instalar pacotes de aplicativos ou contêineres, mesmo que seu estado seja **inutilizável**.

Se o lote puder determinar a causa, a propriedade de [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) de nó o relatará.

Exemplos adicionais de causas para nós **inutilizáveis** incluem:

- Uma imagem de VM personalizada é inválida. Por exemplo, uma imagem que não está preparada corretamente.

- Uma VM é movida devido a uma falha de infraestrutura ou uma atualização de nível baixo. O lote recupera o nó.

- Uma imagem de VM foi implantada em hardware que não oferece suporte a ela. Por exemplo, tentar executar uma imagem CentOS HPC em uma VM [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) .

- As VMs estão em uma [rede virtual do Azure](batch-virtual-network.md)e o tráfego foi bloqueado para as principais portas.

- As VMs estão em uma rede virtual, mas o tráfego de saída para o armazenamento do Azure é bloqueado.

- As VMs estão em uma rede virtual com uma configuração de DNS do cliente e o servidor DNS não pode resolver o armazenamento do Azure.

### <a name="node-agent-log-files"></a>Arquivos de log do agente de nó

O processo do agente do lote que é executado em cada nó de pool pode fornecer arquivos de log que podem ser úteis se você precisar entrar em contato com o suporte sobre um problema de nó de pool. Os arquivos de log de um nó podem ser carregados por meio do portal do Azure, Batch Explorer ou uma [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e salvar os arquivos de log. Depois disso, você pode excluir o nó ou pool para economizar o custo dos nós em execução.

### <a name="node-disk-full"></a>Nó cheio do disco

A unidade temporária para uma VM de nó de pool é usada pelo lote para arquivos de trabalho, arquivos de tarefas e arquivos compartilhados.

- Arquivos de pacotes de aplicativos
- Ficheiros de recursos de tarefas
- Arquivos específicos do aplicativo baixados para uma das pastas do lote
- Arquivos stdout e stderr para cada execução de aplicativo de tarefa
- Arquivos de saída específicos do aplicativo

Alguns desses arquivos são gravados apenas uma vez quando nós de pool são criados, como pacotes de aplicativos de pool ou arquivos de recurso de tarefa de início de pool. Mesmo que seja gravado apenas uma vez quando o nó for criado, se esses arquivos forem muito grandes, eles poderão preencher a unidade temporária.

Outros arquivos são gravados para cada tarefa que é executada em um nó, como stdout e stderr. Se um grande número de tarefas for executado no mesmo nó e/ou os arquivos de tarefa forem muito grandes, eles poderão preencher a unidade temporária.

O tamanho da unidade temporária depende do tamanho da VM. Uma consideração ao escolher um tamanho de VM é garantir que a unidade temporária tenha espaço suficiente.

- Na portal do Azure ao adicionar um pool, a lista completa de tamanhos de VM pode ser exibida e há uma coluna "tamanho do disco do recurso".
- Os artigos que descrevem todos os tamanhos de VM têm tabelas com uma coluna ' armazenamento temporário '; por exemplo, [tamanhos de VM otimizados para computação](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

Para arquivos gravados por cada tarefa, um tempo de retenção pode ser especificado para cada tarefa que determina por quanto tempo os arquivos de tarefa são mantidos antes de serem limpos automaticamente. O tempo de retenção pode ser reduzido para reduzir os requisitos de armazenamento.

Se o espaço em disco temporário for preenchido, no momento, o nó interromperá a execução das tarefas. No futuro, um [erro de nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) será relatado.


## <a name="next-steps"></a>Passos seguintes

Verifique se você definiu seu aplicativo para implementar a verificação de erros abrangente, especialmente para operações assíncronas. Pode ser essencial detectar e diagnosticar problemas imediatamente.
