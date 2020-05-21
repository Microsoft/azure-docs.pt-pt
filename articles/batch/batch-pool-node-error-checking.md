---
title: Verifique se há erros na piscina e nonóio
description: Este artigo abrange as operações de fundo que podem ocorrer, juntamente com erros para verificar e como evitá-las ao criar piscinas e nós.
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: how-to
ms.openlocfilehash: 5ac3991a52ab75dccd0033160d6e972d155a882b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723923"
---
# <a name="check-for-pool-and-node-errors"></a>Verifique se há erros na piscina e nonóio

Quando se está a criar e a gerir piscinas do Lote Azure, algumas operações acontecem imediatamente. No entanto, algumas operações são assíncronas e funcionam em segundo plano, demorando vários minutos a ser concluídas.

A deteção de falhas nas operações que ocorrem imediatamente é simples porque quaisquer falhas são devolvidas imediatamente pela API, CLI ou UI.

Este artigo cobre as operações de fundo que podem ocorrer para piscinas e nós de piscina. Especifica como pode detetar e evitar falhas.

## <a name="pool-errors"></a>Erros na piscina

### <a name="resize-timeout-or-failure"></a>Redimensionar o tempo ou a falha

Ao criar uma piscina nova ou redimensionar uma piscina existente, especifice o número de nós alvo.  A operação de criação ou redimensionamento completa-se imediatamente, mas a atribuição real de novos nós ou a remoção dos nós existentes pode demorar vários minutos.  Especifica o tempo de redimensionar a API [de criar](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionar.](https://docs.microsoft.com/rest/api/batchservice/pool/resize) Se o Batch não conseguir obter o número de nós alvo durante o período de tempo de redimensionamento, a piscina entra em estado constante e relata erros de redimensionamento.

A propriedade [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) para a mais recente avaliação lista os erros que ocorreram.

As causas comuns para erros de redimensionação incluem:

- O tempo de descanso de redimensionar é muito curto
  - Na maioria das circunstâncias, o prazo de incumprimento de 15 minutos é tempo suficiente para que os nós da piscina sejam alocados ou removidos.
  - Se estiver a alocar um grande número de nós, recomendamos que o tempo de redimensionamento seja de 30 minutos. Por exemplo, quando se está a redimensionar para mais de 1.000 nós a partir de uma imagem do Azure Marketplace, ou a mais de 300 nós de uma imagem VM personalizada.
- Quota central insuficiente
  - Uma conta Batch é limitada no número de núcleos que pode alocar em todas as piscinas. O lote deixa de alocar os nódosos uma vez atingido esta quota. [Pode aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) a quota central para que o Lote possa alocar mais nós.
- IPs de sub-rede insuficientes quando uma [piscina está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma subnet de rede virtual deve ter endereços IP não atribuídos suficientes para alocar a cada nó de piscina solicitado. Caso contrário, os nós não podem ser criados.
- Recursos insuficientes quando uma [piscina está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Pode criar recursos como equilibradores de carga, iPs públicos e grupos de segurança de rede na mesma subscrição que a conta Batch. Verifique se as quotas de subscrição são suficientes para estes recursos.
- Grandes piscinas com imagens VM personalizadas
  - Grandes piscinas que usam imagens VM personalizadas podem demorar mais tempo a alocar e redimensionar os intervalos de tempo.  Consulte [criar uma piscina com a Galeria de Imagem Partilhada](batch-sig-images.md) para obter recomendações sobre limites e configuração.

### <a name="automatic-scaling-failures"></a>Falhas automáticas de escalação

Também pode definir o Lote Azure para escalar automaticamente o número de nós numa piscina. Define os parâmetros para a [fórmula de escala automática para uma piscina.](https://docs.microsoft.com/azure/batch/batch-automatic-scaling) O serviço Batch utiliza a fórmula para avaliar periodicamente o número de nós na piscina e definir um novo número-alvo. Podem ocorrer os seguintes tipos de problemas:

- A avaliação automática de escalafalha.
- A operação de redimensionação resultante falha e sai.
- Um problema com a fórmula de escala automática leva a valores-alvo incorretos do nó. O redimensionado funciona ou vezes fora.

Pode obter informações sobre a última avaliação automática de escala utilizando a propriedade [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) Esta propriedade reporta o tempo de avaliação, os valores e resultados, e quaisquer erros de desempenho.

O [evento completo de redimensionar](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) a piscina capta informações sobre todas as avaliações.

### <a name="delete"></a>Eliminar

Quando elimina uma piscina que contém nós, primeiro o Batch elimina os nós. Em seguida, elimina o objeto da piscina em si. Pode levar alguns minutos para os nódosos da piscina serem apagados.

O lote define o estado da [piscina](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) para **apagar** durante o processo de eliminação. A aplicação de chamada pode detetar se a eliminação da piscina está a demorar muito tempo utilizando as propriedades **estatais** e **estataisTransitionTime.**

## <a name="pool-compute-node-errors"></a>Erros no nó da computação de piscina

Mesmo quando o Batch aloca com sucesso nós numa piscina, várias questões podem fazer com que alguns dos nós não sejam saudáveis e incapazes de executar tarefas. Estes nós ainda incorrem em encargos, por isso é importante detetar problemas para evitar pagar por nós que não podem ser usados. Além de erros comuns do nó, saber que o [estado de trabalho](/rest/api/batchservice/job/get#jobstate) atual é útil para resolução de problemas.

### <a name="start-task-failures"></a>Iniciar falhas de tarefa

Você deve especificar uma tarefa de [início](/rest/api/batchservice/pool/add#starttask) opcional para uma piscina. Como em qualquer tarefa, pode utilizar uma linha de comando e ficheiros de recursos para descarregar a partir do armazenamento. A tarefa inicial é executada para cada nó depois de ter sido iniciada. A propriedade **waitForSuccess** especifica se o Lote aguarda até que a tarefa de início complete com sucesso antes de agendar quaisquer tarefas para um nó.

E se configurasse o nó para esperar pela conclusão da tarefa de início, mas a tarefa inicial falhar? Nesse caso, o nó não será utilizável, mas continuará a incorrer em acusações.

Pode detetar falhas de tarefa inicial utilizando as propriedades [de resultados](/rest/api/batchservice/computenode/get#taskexecutionresult) e [falhasInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade do nó [de início taskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) de alto nível.

Uma tarefa de arranque falhada também faz com que o Batch desloque o [estado](/rest/api/batchservice/computenode/get#computenodestate) do nó para **iniciar as tarefas falhadas** se a **esperaForSuccess** for definida como **verdadeira**.

Como em qualquer tarefa, pode haver muitas causas para a falha na tarefa inicial.  Para resolver problemas, verifique o stdout, o stderr e quaisquer ficheiros de registo específicos da tarefa.

As tarefas de arranque devem ser recandidatadas, pois é possível que a tarefa inicial seja executada várias vezes no mesmo nó; a tarefa inicial é executada quando um nó é reimageed ou reiniciado. Em casos raros, uma tarefa inicial será executada após um evento que causou um reboot do nó, onde um dos discos operativos ou efémeros foi reimagemdo enquanto o outro não. Uma vez que as tarefas de início do Lote (como todas as tarefas do Lote) funcionam a partir do disco efémero, isso não é normalmente um problema, mas em alguns casos em que a tarefa inicial está a instalar uma aplicação no disco do sistema operativo e a manter outros dados no disco efémero, isso pode causar problemas porque as coisas estão dessincronizadas. Proteja a sua aplicação em conformidade se estiver a utilizar ambos os discos.

### <a name="application-package-download-failure"></a>Falha no descarregamento de pacotes de aplicações

Pode especificar um ou mais pacotes de aplicação para uma piscina. O lote descarrega os ficheiros de pacote especificados para cada nó e descomprime os ficheiros após o início do nó, mas antes de as tarefas estarem agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicação. Por exemplo, copiar ficheiros para um local diferente ou executar configuração.

A propriedade [de erros](/rest/api/batchservice/computenode/get#computenodeerror) do nó relata uma falha no download e descomprime um pacote de aplicação; o estado do nó está definido para **inutilizável**.

### <a name="container-download-failure"></a>Falha no descarregamento de contentores

Pode especificar uma ou mais referências de contentores numa piscina. O lote transfere os recipientes especificados para cada nó. O nó [falha](/rest/api/batchservice/computenode/get#computenodeerror) a propriedade relata uma falha no descarregamento de um recipiente e define o estado do nó como **inutilizável**.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O Lote Azure pode definir o estado do [nó](/rest/api/batchservice/computenode/get#computenodestate) **inutilizável** por muitas razões. Com o estado do nó definido para **inutilizável,** as tarefas não podem ser agendadas para o nó, mas ainda incorre em acusações.

Nós em estado **inutilizável,** mas sem [erros](/rest/api/batchservice/computenode/get#computenodeerror) significa que o Batch é incapaz de comunicar com o VM. Neste caso, Batch tenta sempre recuperar o VM. O lote não tentará automaticamente recuperar VMs que não tenham instalado pacotes de aplicação ou contentores, mesmo que o seu estado não **seja utilizável**.

Se o Batch conseguir determinar a causa, o nó [falha](/rest/api/batchservice/computenode/get#computenodeerror) a propriedade.

Exemplos adicionais de causas para nós **inutilizáveis** incluem:

- Uma imagem VM personalizada é inválida. Por exemplo, uma imagem que não está devidamente preparada.

- Um VM é movido devido a uma falha de infraestrutura ou a uma atualização de baixo nível. O lote recupera o nó.

- Foi implementada uma imagem VM em hardware que não a suporta. Por exemplo, tentar executar uma imagem CentOS HPC numa [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM.

- Os VMs estão numa [rede virtual Azure,](batch-virtual-network.md)e o tráfego foi bloqueado em portas-chave.

- Os VMs estão numa rede virtual, mas o tráfego de saída para o armazenamento do Azure está bloqueado.

- Os VMs estão numa rede virtual com uma configuração DNS do cliente e o servidor DNS não consegue resolver o armazenamento do Azure.

### <a name="node-agent-log-files"></a>Ficheiros de registo do agente do nó

O processo do agente Batch que funciona em cada nó de piscina pode fornecer ficheiros de registo que podem ser úteis se precisar de contactar suporte sobre um problema no nó da piscina. Os ficheiros de registo de um nó podem ser enviados através do portal Azure, do Batch Explorer ou de um [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e guardar os ficheiros de registo. Depois, pode eliminar o nó ou a piscina para poupar o custo dos nós de corrida.

### <a name="node-disk-full"></a>Disco de nó cheio

A unidade temporária para um VM do nó de piscina é usada pelo Batch para ficheiros de trabalho, ficheiros de tarefas e ficheiros partilhados.

- Ficheiros de pacotes de aplicação
- Ficheiros de recursos de tarefas
- Ficheiros específicos da aplicação descarregados para uma das pastas do Lote
- Ficheiros Stdout e Stderr para cada execução de aplicação de tarefas
- Ficheiros de saída específicos da aplicação

Alguns destes ficheiros só são escritos uma vez que os nós da piscina são criados, tais como pacotes de aplicação de pool ou ficheiros de recursos de tarefa sinuosos. Mesmo que apenas escrito uma vez quando o nó é criado, se estes ficheiros forem muito grandes, podem preencher a unidade temporária.

Outros ficheiros são escritos para cada tarefa que é executada num nó, como stdout e stderr. Se um grande número de tarefas forem executadas no mesmo nó e/ou os ficheiros de tarefas forem demasiado grandes, podem preencher a unidade temporária.

O tamanho da unidade temporária depende do tamanho vm. Uma consideração ao escolher um tamanho VM é garantir que a unidade temporária tem espaço suficiente.

- No portal Azure ao adicionar uma piscina, a lista completa de tamanhos vm pode ser exibida e há uma coluna 'Tamanho do Disco de Recursos'.
- Os artigos que descrevem todos os tamanhos vm têm tabelas com uma coluna 'Armazenamento Temporário'; por exemplo [Compute Tamanhos VM otimizados](/azure/virtual-machines/windows/sizes-compute)

Para ficheiros escritos por cada tarefa, pode ser especificado um tempo de retenção para cada tarefa que determina quanto tempo os ficheiros de tarefas são mantidos antes de serem automaticamente limpos. O tempo de retenção pode ser reduzido para reduzir os requisitos de armazenamento.


Se o disco temporário ficar sem espaço (ou estiver muito perto de ficar sem espaço), o nó deslocar-se-á para o estado [inutilizável](/rest/api/batchservice/computenode/get#computenodestate) e será relatado um erro no nó dizendo que o disco está cheio.

### <a name="what-to-do-when-a-disk-is-full"></a>O que fazer quando um disco está cheio

Determine por que o disco está cheio: Se não tem certeza do que está ocupando espaço no nó, é aconselhável remotamente ao nó e investigue manualmente para onde o espaço foi. Também pode utilizar a API de [Ficheiros](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) da Lista de Lote para examinar ficheiros em pastas geridas pelo Batch (por exemplo, saídas de tarefas). Note que esta API apenas lista ficheiros nos diretórios geridos pelo Batch e se as suas tarefas criaram ficheiros noutros locais, não os verá.

Certifique-se de que todos os dados necessários foram recuperados do nó ou enviados para uma loja durável. Toda a mitigação da questão completa do disco envolve a libertação de dados para libertar espaço.

### <a name="recovering-the-node"></a>Recuperando o nó

1. Se a sua piscina for uma piscina [C.loudServiceConfiguration,](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) pode re-imagem do nó através da [API de re-imagem do Lote](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage). Isto vai limpar todo o disco. A re-imagem não é suportada atualmente para piscinas [de Configuração VirtualMachine.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)

2. Se a sua piscina for uma [Configuração VirtualMachine,](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)pode remover o nó da piscina utilizando os [nós de remoção API](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes). Depois, pode voltar a cultivar a piscina para substituir o nó ruim por um fresco.

3.  Eliminar trabalhos antigos concluídos ou tarefas antigas concluídas cujos dados de tarefa ainda se encontra nos nódosos. Para obter uma dica sobre quais os dados de emprego/tarefas nos nós pode procurar na [recolha de Tarefas Recentes](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) no nó, ou nos [ficheiros no nó](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode). A eliminação do trabalho eliminará todas as tarefas no trabalho, e a eliminação das tarefas no trabalho irá desencadear dados nos diretórios de tarefas no nó a serem eliminados, libertando assim espaço. Uma vez libertado espaço suficiente, reinicie o nó e deve sair do estado "Inutilizável" e voltar a entrar em "Idle".

## <a name="next-steps"></a>Próximos passos

Verifique se definiu a sua aplicação para implementar uma verificação abrangente de erros, especialmente para operações assíncronas. Pode ser fundamental detetar e diagnosticar rapidamente problemas.
