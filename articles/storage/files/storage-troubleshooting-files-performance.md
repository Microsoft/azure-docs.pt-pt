---
title: Desempenho de ficheiros do Azure, guia de resolução de problemas
description: Problemas de desempenho com partilhas de ficheiros do Azure premium (pré-visualização) e soluções associadas conhecidos.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190041"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Resolução de problemas de desempenho de ficheiros do Azure

Este artigo lista alguns problemas comuns relacionados com partilhas de ficheiros do Azure premium (pré-visualização). Fornece possíveis causas e soluções alternativas quando esses problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Alta latência, débito de baixo e problemas gerais de desempenho

### <a name="cause-1-share-experiencing-throttling"></a>Fazer com que 1: Partilhar com limitação

A quota padrão num compartilhamento é 100 GiB, que fornece a linha de base de 100 IOPS (com um potencial de expandir até 300 durante uma hora). Para obter mais informações sobre o aprovisionamento e sua relação com IOPS, consulte a [aprovisionado partilhas](storage-files-planning.md#provisioned-shares) secção do guia de planejamento.

Para confirmar se a partilha está a ser limitada, pode aproveitar as métricas do Azure no portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** e, em seguida, procure **métricas**.

1. Selecione **Métricas**.

1. Selecione a sua conta de armazenamento que o recurso.

1. Selecione **ficheiro** como o espaço de nomes de métrica.

1. Selecione **transações** como a métrica.

1. Adicione um filtro para **ResponseType** e verifique se todos os pedidos têm um código de resposta do **SuccessWithThrottling**.

![Opções de métricas para existem fileshares de premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Solução

- Aumento partilhar capacidade aprovisionada, especificando uma quota superior na sua partilha.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Carga de trabalho pesada de metadados/espaço de nomes

Se a maioria dos seus pedidos de metadados centrados em (como o createfile/openfile/closefile/queryinfo/querydirectory), em seguida, a latência será pior quando comparado com as operações de leitura/escrita.

Para confirmar que se a maioria dos seus pedidos estiverem centrados em metadados, pode utilizar os mesmos passos conforme apresentado acima. Exceto em vez de adicionar um filtro para **ResponseType**, adicione um filtro para **nome da API**.

![Filtrar por nome da API em suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução

- Verifique se o aplicativo pode ser modificado para reduzir o número de operações de metadados.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicação de thread único

Se a aplicação que está a ser utilizada pelo cliente é thread único, isso pode resultar em IOPS/débito significativamente menor que o máximo permitido com base no seu tamanho da partilha aprovisionado.

### <a name="solution"></a>Solução

- Aumente o paralelismo de aplicativo do aumento do número de threads.
- Mude para aplicativos em que o paralelismo é possível. Por exemplo, para operações de cópia, os clientes podem utilizar AzCopy ou RoboCopy de clientes do Windows ou o **paralela** comando nos clientes do Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito elevada para pedidos

### <a name="cause"></a>Causa

Foi possível localizar o cliente VM numa região diferente do que a partilha de ficheiros de premium.

### <a name="solution"></a>Solução

- Execute a aplicação a partir de uma VM que está localizada na mesma região que a partilha de ficheiros de premium.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Não é possível alcançar um débito máximo suportado pela rede de cliente

Uma causa potencial disso é a falta fo SMB multicanal suporte. Atualmente, as partilhas de ficheiros do Azure só suportam canal único, portanto, há apenas uma ligação do cliente VM para o servidor. Esta ligação única é estabilizada para um único núcleo na VM cliente, para que o máximo débito alcançável a partir de uma VM está ligado por um único núcleo.

### <a name="workaround"></a>Solução

- Obtenção de uma VM com um núcleo maior pode ajudar a melhorar o débito.
- Executar a aplicação de cliente provenientes de várias VMs irá aumentar o débito.
- Utilize REST APIs, sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Débito em clientes do Linux é significativamente inferior quando comparado com os clientes do Windows.

### <a name="cause"></a>Causa

Este é um problema conhecido com a implementação de cliente do SMB no Linux.

### <a name="workaround"></a>Solução

- Distribuir a carga por várias VMs
- Na mesma VM, utilize vários pontos de montagem com **nosharesock** opção e spread como a carga por estes pontos de montagem.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latências elevadas para metadados cargas de trabalho pesadas envolvendo amplas aberto/close operações.

### <a name="cause"></a>Causa

Falta de suporte para concessões de diretório.

### <a name="workaround"></a>Solução

- Se possível, evite excessiva znak levé nebo pravé identificador no mesmo diretório dentro de um curto período de tempo.
- Para VMs do Linux, aumentar o tempo de limite do cache de entrada do diretório, especificando **actimeo =<sec>**  como uma opção de montagem. Por predefinição, é um segundo, para que um valor maior, como três ou cinco poderá ajudar.
- Para VMs do Linux, atualize o kernel para 4.20 ou superior.

## <a name="low-iops-on-centosrhel"></a>IOPS de baixo no CentOS/RHEL

### <a name="cause"></a>Causa

Profundidade de e/s maior que um não é suportada no CentOS/RHEL.

### <a name="workaround"></a>Solução

- Atualizar para o 8 de CentOS / RHEL 8.
- Altere para o Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Padrão de Trêmulo/saw-tooth para IOPS

### <a name="cause"></a>Causa

Aplicação cliente excede constantemente a linha de base de IOPS. Atualmente, não há nenhum lado do serviço nivelamento de carga de pedidos, portanto, se o cliente exceder a linha de base de IOPS, esta fica bloqueada pelo serviço. Essa limitação pode resultar em cliente que apresenta um padrão IOPS trêmulo/saw-tooth. Neste caso, média IOPS obtida com o cliente pode ser menor do que a linha de base IOPS.

### <a name="workaround"></a>Solução

- Reduza a carga de pedidos da aplicação cliente, para que não é limitada a partilha.
- Aumente a quota da partilha para que não é limitada a partilha.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas de DirectoryOpen/DirectoryClose

### <a name="cause"></a>Causa

Se o número de chamadas de DirectoryOpen/DirectoryClose está entre as chamadas de API superior e não quiser que o cliente a fazer que o número de chamadas, poderá ser um problema com o antivírus instalado na VM do cliente do Azure.

### <a name="workaround"></a>Solução

- Uma solução para este problema está disponível na [Abril plataforma atualização para Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Criação do ficheiro é mais lenta do que o esperado

### <a name="cause"></a>Causa

Cargas de trabalho que se baseiam na criação de um grande número de ficheiros não verá uma diferença substancial entre o desempenho premium de partilhas de ficheiros e partilhas de ficheiros padrão.

### <a name="workaround"></a>Solução

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou Server 2012 R2

### <a name="cause"></a>Causa

Maior do que esperado latência aceder a ficheiros do Azure para cargas de trabalho intensivas de e/s.

### <a name="workaround"></a>Solução

- Instalar o disponíveis [correção](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).