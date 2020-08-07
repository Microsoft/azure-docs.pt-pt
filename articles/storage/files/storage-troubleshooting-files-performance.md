---
title: Guia de resolução de problemas de desempenho dos Ficheiros Azure Files
description: Resolução de problemas conhecidos problemas de desempenho com ações de ficheiros Azure. Descubra potenciais causas e soluções alternativas associadas quando estes problemas são encontrados.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 1c0d7e5c7c021f8cdad8980bd7659d819b85f899
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905019"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Problemas de resolução de problemas Problemas de desempenho dos Ficheiros Azure

Este artigo enumera alguns problemas comuns relacionados com as ações de ficheiros Azure. Fornece potenciais causas e soluções quando estes problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Altas latências, baixo rendimento e problemas gerais de desempenho

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Partilhar a experimentar estrangulamento

A quota padrão numa ação premium é de 100 GiB, que fornece 100 IOPS de base (com um potencial de explosão até 300 durante uma hora). Para obter mais informações sobre o provisionamento e a sua relação com o IOPS, consulte a secção [de ações provisionadas](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) do guia de planeamento.

Para confirmar se a sua parte está a ser acelerada, pode aproveitar a Azure Metrics no portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** e, em seguida, procure **por Métricas**.

1. Selecione **Métricas**.

1. Selecione a sua conta de armazenamento como o recurso.

1. Selecione **File** como o espaço de nome métrico.

1. Selecione **Transações** como métrica.

1. Adicione um filtro para **o ResponseType** e verifique se algum pedido tem um código de resposta de **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST).

![Opções de métricas para partilhas de ficheiros premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Para receber um alerta se uma partilha de ficheiros for acelerada, consulte [Como criar um alerta se uma partilha de ficheiros for acelerada](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Solução

- Aumente a capacidade a percentagem apresentando especificando uma quota mais elevada na sua parte.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Metadata/espaço de nome carga de trabalho pesada

Se a maioria dos seus pedidos forem centrados em metadados (tais como criarfile/openfile/closefile/queryinfo/querydirectory) então a latência será pior quando comparada com operações de leitura/escrita.

Para confirmar se a maioria dos seus pedidos são centrados em metadados, pode usar os mesmos passos acima. Exceto que em vez de adicionar um filtro para **ResponseType,** adicione um filtro para **o nome API**.

![Filtro para nome API nas suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução

- Verifique se a aplicação pode ser modificada para reduzir o número de operações de metadados.
- Adicione um VHD na partilha de ficheiros e monte VHD sobre SMB do cliente para realizar operações de ficheiros contra os dados. Esta abordagem funciona para cenários individuais de escritores e vários leitores e permite que as operações de metadados sejam locais, oferecendo um desempenho semelhante a um armazenamento direto local.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicação de rosca única

Se a aplicação utilizada pelo cliente for de rosca única, isso pode resultar numa produção IOPS/produção significativamente mais baixa do que o máximo possível com base no tamanho da sua ação aprovisionada.

### <a name="solution"></a>Solução

- Aumente o paralelismo de aplicação aumentando o número de fios.
- Mude para aplicações onde o paralelismo é possível. Por exemplo, para operações de cópia, os clientes poderiam utilizar a AzCopy ou RoboCopy a partir de clientes Windows ou o comando **paralelo** nos clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para pedidos

### <a name="cause"></a>Causa

O VM cliente poderia estar localizado numa região diferente da parte do ficheiro.

### <a name="solution"></a>Solução

- Executar o pedido a partir de um VM que está localizado na mesma região que a partilha de ficheiros.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Cliente incapaz de atingir a produção máxima suportada pela rede

Uma das causas potenciais é a falta de suporte a vários canais SMB. Atualmente, as ações de ficheiroS Azure suportam apenas um canal único, pelo que existe apenas uma ligação do VM do cliente ao servidor. Esta ligação única é marcada para um único núcleo no VM do cliente, de modo que a produção máxima alcançável a partir de um VM é ligada por um único núcleo.

### <a name="workaround"></a>Solução

- Obter um VM com um núcleo maior pode ajudar a melhorar a produção.
- Executar a aplicação do cliente a partir de vários VMs aumentará a produção.

- Utilize APIs DE REPOUSO sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A produção de clientes Linux é significativamente menor quando comparada com os clientes windows.

### <a name="cause"></a>Causa

Esta é uma questão conhecida com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução

- Espalhe a carga por vários VMs.
- No mesmo VM, utilize vários pontos de montagem com opção **nosharesock** e espalhe a carga por estes pontos de montagem.
- Em Linux, tente montar com a opção **nostrictsync** para evitar forçar o SMB a descarregar em cada chamada **fsync.** Para os Ficheiros Azure, esta opção não interfere com a consistência dos dados, mas pode resultar em metadados de ficheiros em atraso na listagem de diretórios (comando**l -l).** Consulta direta de metadados de ficheiro (comando**stat)** retornará os metadados de ficheiro mais atualizados.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas latências para metadados pesados cargas de trabalho envolvendo extensas operações abertas/próximas.

### <a name="cause"></a>Causa

Falta de apoio para arrendamentos de diretórios.

### <a name="workaround"></a>Solução

- Se possível, evite uma abertura/manípulo de fecho excessivo no mesmo diretório num curto espaço de tempo.
- Para os VMs Linux, aumente o tempo limite de cache de entrada de diretório especificando **actimeo= \<sec> ** como uma opção de montagem. Por padrão, é um segundo, então um valor maior como três ou cinco pode ajudar.
- Para os VMs Linux, atualize o núcleo para 4,20 ou mais.

## <a name="low-iops-on-centosrhel"></a>IOPS baixos no CentOS/RHEL

### <a name="cause"></a>Causa

A profundidade IO superior a uma não é suportada no CentOS/RHEL.

### <a name="workaround"></a>Solução

- Upgrade para CentOS 8 / RHEL 8.
- Mudar para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia lenta de ficheiros de e para ficheiros Azure em Linux

Se estiver a experimentar cópias lentas de ficheiros de e para ficheiros Azure, dê uma olhada na [cópia do ficheiro Slow de e para ficheiros Azure na](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) secção Linux no guia de resolução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Padrão nervoso/dente-de-serra para IOPS

### <a name="cause"></a>Causa

A aplicação do cliente excede consistentemente o IOPS de base. Atualmente, não existe um alisamento lateral de serviço da carga de pedido, por isso, se o cliente exceder o IOPS de base, será estrangulado pelo serviço. Esse estrangulamento pode resultar em que o cliente experimente um padrão de IOPS nervoso/dente-serra. Neste caso, o IOPS médio alcançado pelo cliente pode ser inferior ao IOPS de base.

### <a name="workaround"></a>Solução

- Reduza a carga de pedido da aplicação do cliente, para que a parte não seja estrangulada.
- Aumente a quota da parte para que a parte não seja estrangulada.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas do DirectórioO/Diretório

### <a name="cause"></a>Causa

Se o número de chamadas Do DirectyOpen/DirectoryClose estiver entre as principais chamadas da API e não espera que o cliente esteja a fazer tantas chamadas, pode ser um problema com o antivírus instalado no VM do cliente Azure.

### <a name="workaround"></a>Solução

- Uma correção para este problema está disponível na [Atualização](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)da Plataforma de abril para o Windows .

## <a name="file-creation-is-slower-than-expected"></a>A criação de ficheiros é mais lenta do que o esperado

### <a name="cause"></a>Causa

Cargas de trabalho que dependem da criação de um grande número de ficheiros não verão uma diferença substancial entre o desempenho das ações de ficheiros premium e as ações de ficheiros padrão.

### <a name="workaround"></a>Solução

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou Do Servidor 2012 R2

### <a name="cause"></a>Causa

Maior do que o esperado, a latência acede aos Ficheiros Azure para cargas de trabalho intensivas de IO.

### <a name="workaround"></a>Solução

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se uma partilha de ficheiros for acelerada

1. No [portal Azure](https://portal.azure.com), clique no **Monitor**. 

2. Clique **em Alertas** e, em seguida, clique **em + Nova regra de alerta**.

3. Clique **em Selecionar** para selecionar a conta de **armazenamento/recurso de ficheiro** que contém a partilha de ficheiros que pretende alertar e, em seguida, clicar em **'Fazer'.** Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/ficheiro.

4. Clique **em Adicionar** para adicionar uma condição.

5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **de Transações.**

6. Na lâmina lógica de **sinal configurar,** vá para a dimensão do **tipo Resposta,** clique nos **valores** de Dimension para baixo e selecione **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST). 

  > [!NOTE]
  > Se o valor da dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isto significa que o recurso não foi estrangulado.  Para adicionar o valor de dimensão, clique **+** ao lado dos **valores** de Dimension drop-down, **digite SuccessWithThrottling** ou **ClientThrottlingError,** clique em **OK** e repita o passo #6.

7. Vá para a dimensão **'Partilhar** ficheiros', clique nos **valores** de Dimension drop-down e selecione a(s) partilha de ficheiros que pretende alertar. 

  > [!NOTE]
  > Se a partilha de ficheiros for uma partilha de ficheiros padrão, os valores de dimensão descerão em branco porque as métricas por ação não estão disponíveis para ações de ficheiros padrão. Os alertas de estrangulamento para as ações de ficheiros padrão serão desencadeados se alguma parte do ficheiro dentro da conta de armazenamento for acelerada e o alerta não identificar qual a partilha de ficheiros que foi acelerada. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento. 

8. Defina os **parâmetros** de alerta (limiar, operador, granularidade de agregação e frequência) que são utilizados para avaliar a regra de alerta métrico e clicar em **Fazer**.

  > [!TIP]
  > Se estiver a utilizar um limiar estático, o gráfico métrico pode ajudar a determinar um limiar razoável se a parte do ficheiro estiver atualmente a ser estrangulada. Se estiver a utilizar um limiar dinâmico, o gráfico métrico apresentará os limiares calculados com base em dados recentes.

9. Adicione um **grupo de ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.

10. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.

11. Clique **em Criar regra de alerta** para criar o alerta.

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
