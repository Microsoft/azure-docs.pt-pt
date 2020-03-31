---
title: Guia de resolução de problemas de desempenho de Azure Files
description: Problemas de desempenho conhecidos com ações de ficheiros Azure e salções de suposições associadas.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598090"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Problemas de resolução de problemas de desempenho de Ficheiros Azure

Este artigo enumera alguns problemas comuns relacionados com as ações de ficheiros do Azure. Proporciona potenciais causas e soluções quando estes problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Alta latência, baixa taxa de rendimento e problemas gerais de desempenho

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Partilhar a pulsação

A quota padrão de uma parte premium é de 100 GiB, que fornece 100 IOPS de base (com potencial para rebentar até 300 durante uma hora). Para obter mais informações sobre o provisionamento e a sua relação com o IOPS, consulte a secção de [ações provisionadas](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) do guia de planeamento.

Para confirmar se a sua parte está a ser estrangulada, pode alavancar a Azure Metrics no portal.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** e, em seguida, procure **Métricas**.

1. Selecione **Métricas**.

1. Selecione a sua conta de armazenamento como recurso.

1. Selecione **File** como o espaço de nomemétrico.

1. Selecione **Transações** como métrica.

1. Adicione um filtro para **ResponseType** e verifique se algum pedido tem um código de resposta de **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST).

![Opções métricas para partilhas de ficheiros premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Para receber um alerta se uma parte de ficheiro for acelerada, consulte como criar um alerta se uma parte de [ficheiro for acelerada](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Solução

- Aumente a capacidade aprovisionada por ações, especificando uma quota mais elevada na sua parte.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Metadados/espaço de trabalho pesado

Se a maioria dos seus pedidos forem centrados em metadados (tais como criar ficheiro/openfile/closefile/consultainfo/consulta) então a latência será pior quando comparada com as operações de leitura/escrita.

Para confirmar se a maioria dos seus pedidos são centrados em metadados, pode usar os mesmos passos que acima. Exceto em vez de adicionar um filtro para O Tipo de **Resposta,** adicione um filtro para **nome API**.

![Filtro para nome API nas suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução

- Verifique se a aplicação pode ser modificada para reduzir o número de operações de metadados.
- Adicione um VHD na partilha de ficheiros e monte VHD sobre SMB do cliente para efetuar operações de ficheiros contra os dados. Esta abordagem funciona para cenários de escritores e múltiplos leitores e permite que as operações de metadados sejam locais, oferecendo desempenho semelhante a um armazenamento local ligado diretamente.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicação de roscar

Se a aplicação que está a ser utilizada pelo cliente for de roscar, isto pode resultar em IOPS/saída significativamente mais baixa do que o máximo possível com base no tamanho da sua parte provisionada.

### <a name="solution"></a>Solução

- Aumentar o paralelismo da aplicação aumentando o número de fios.
- Mude para aplicações onde o paralelismo é possível. Por exemplo, para operações de cópia, os clientes poderiam utilizar a AzCopy ou a RoboCopy de clientes Windows ou o comando **paralelo** nos clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para pedidos

### <a name="cause"></a>Causa

O VM cliente pode estar localizado numa região diferente da parte do ficheiro.

### <a name="solution"></a>Solução

- Executar o pedido de um VM que esteja localizado na mesma região que a parte do ficheiro.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Cliente incapaz de obter o máximo de entrada suportado pela rede

Uma das causas potenciais é a falta de suporte multicanal fo SMB. Atualmente, o ficheiro Azure partilha apenas o único canal, pelo que só existe uma ligação entre o VM do cliente e o servidor. Esta única ligação é agrafada a um único núcleo no VM do cliente, pelo que a potência máxima alcançável a partir de um VM é ligada por um único núcleo.

### <a name="workaround"></a>Solução

- Obter um VM com um núcleo maior pode ajudar a melhorar a entrada.
- Executar a aplicação do cliente a partir de vários VMs aumentará a entrada.

- Utilize APIs de REPOUSO sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A entrada nos clientes do Linux é significativamente mais baixa quando comparada com os clientes do Windows.

### <a name="cause"></a>Causa

Esta é uma questão conhecida com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução

- Espalhe a carga por vários VMs.
- No mesmo VM, utilize vários pontos de montagem com opção **nosharesock** e espalhe a carga através destes pontos de montagem.
- Em Linux, tente montar com opção **nostrictsync** para evitar forçar o sMB a descarregar em todas as chamadas **de sincronia.** No caso dos Ficheiros Azure, esta opção não interfere com a consistência dos dados, mas pode resultar em metadados de ficheiros estoiros na listagem de diretórios (comando**ls -l).** A consulta direta dos metadados de ficheiros (comando**stat)** devolverá os metadados de ficheiros mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas tardios para metadados pesados de cargas de trabalho envolvendo extensas operações abertas/próximas.

### <a name="cause"></a>Causa

Falta de apoio para arrendamentos de diretórios.

### <a name="workaround"></a>Solução

- Se possível, evite uma pega excessiva de abertura/fecho no mesmo diretório num curto espaço de tempo.
- Para os VMs Linux, aumente o tempo de entrada de diretório, especificando **actimeo=\<sec>** como uma opção de montagem. Por padrão, é um segundo, então um valor maior como três ou cinco pode ajudar.
- Para VMs Linux, atualize o núcleo para 4,20 ou mais.

## <a name="low-iops-on-centosrhel"></a>IOPS baixos em CentOS/RHEL

### <a name="cause"></a>Causa

A profundidade IO superior a uma não é suportada no CentOS/RHEL.

### <a name="workaround"></a>Solução

- Upgrade para CentOS 8 / RHEL 8.
- Mude para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia lenta de ficheiros de e para Ficheiros Azure em Linux

Se estiver a experimentar cópias lentas de ficheiros de e para ficheiros Azure, dê uma olhada na cópia do [ficheiro Slow de e para o Azure Files na](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) secção Linux no guia de resolução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Padrão nervoso/dente-serra para IOPS

### <a name="cause"></a>Causa

A aplicação do cliente excede consistentemente o IOPS de base. Atualmente, não existe um acompanhamento do lado do serviço da carga de pedido, por isso, se o cliente exceder o IOPS base, será estrangulado pelo serviço. Esta estrangulamento pode resultar em que o cliente experimente um padrão de IOPS nervoso/dente-serra. Neste caso, os IOPS médios alcançados pelo cliente podem ser inferiores aos IOPS de base.

### <a name="workaround"></a>Solução

- Reduza a carga de pedido da aplicação do cliente, para que a parte não seja estrangulada.
- Aumente a quota da parte para que a parte não seja estrangulada.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas de DirectórioOpen/DirectórioClose

### <a name="cause"></a>Causa

Se o número de chamadas Do DirectórioOpen/Diretórios estiver entre as principais chamadas API e não esperar que o cliente esteja a fazer tantas chamadas, pode ser um problema com o antivírus instalado no VM do cliente Azure.

### <a name="workaround"></a>Solução

- Uma correção para este problema está disponível na [Atualização](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)da Plataforma de abril para windows .

## <a name="file-creation-is-slower-than-expected"></a>A criação de ficheiros é mais lenta do que o esperado

### <a name="cause"></a>Causa

As cargas de trabalho que dependem da criação de um grande número de ficheiros não verão uma diferença substancial entre o desempenho das ações de ficheiros premium e as ações de ficheiros padrão.

### <a name="workaround"></a>Solução

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou Servidor 2012 R2

### <a name="cause"></a>Causa

Latência superior ao esperado acedendo a Ficheiros Azure para cargas de trabalho intensivas iO.

### <a name="workaround"></a>Solução

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se uma partilha de ficheiros for estrangulada

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. 

2. Clique em **Alertas** e, em seguida, clique **+ Nova regra de alerta**.

3. Clique **em Selecionar** para selecionar a conta de **armazenamento/recurso** de ficheiro que contém a partilha de ficheiros que pretende alertar e, em seguida, clique em **Done**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/ficheiro.

4. Clique em **Adicionar** para adicionar uma condição.

5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica de **Transações.**

6. Na lâmina lógica do **sinal Configure,** vá para a dimensão do **tipo Resposta,** clique nos **valores de Dimensão** drop-down e selecione **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REPOUSO). 

  > [!NOTE]
  > Se o valor de dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isto significa que o recurso não foi estrangulado.  Para adicionar o valor **+** de dimensão, clique no lado dos **valores dimensionais** drop-down, type **SuccessWithThrottling** ou **ClientThrottlingError,** clique EM **OK** e repita o passo #6.

7. Vá à dimensão da Partilha de **Ficheiros,** clique nos **valores dimensionais** e selecione a(s) partilha de ficheiros que pretende alertar. 

  > [!NOTE]
  > Se a parte do ficheiro for uma parte padrão do ficheiro, os valores de dimensão serão em branco porque as métricas por ação não estão disponíveis para ações de ficheiros padrão. Os alertas de estrangulamento para as ações de ficheiros padrão serão desencadeados se qualquer parte de ficheiro dentro da conta de armazenamento for estrangulada e o alerta não identificar á parte do ficheiro foi estrangulada. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma parte de ficheiro por conta de armazenamento. 

8. Defina os **parâmetros** de alerta (limiar, operador, granularidade de agregação e frequência) que são utilizados para avaliar a regra de alerta métrico e clicar **em Done**.

  > [!TIP]
  > Se estiver a utilizar um limiar estático, o gráfico métrico pode ajudar a determinar um limiar razoável se a parte do ficheiro estiver atualmente a ser estrangulada. Se estiver a utilizar um limiar dinâmico, o gráfico métrico apresentará os limiares calculados com base em dados recentes.

9. Adicione um grupo de **ação** (e-mail, SMS, etc.) ao alerta, quer selecionando um grupo de ação existente, quer criando um novo grupo de ação.

10. Preencha os detalhes do **Alerta** como o nome da regra do **Alerta,** **Descrição** e **Gravidade**.

11. Clique em Criar a regra de **alerta** para criar o alerta.

Para saber mais sobre a configuração de alertas no Monitor Azure, consulte a [visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
