---
title: Guia de solução de problemas de desempenho de arquivos do Azure
description: Problemas de desempenho conhecidos com compartilhamentos de arquivos do Azure e soluções alternativas associadas.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6b28d004ceacda3cec13e96ceae84d5d1ff1a2e5
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699161"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Solucionar problemas de desempenho de arquivos do Azure

Este artigo lista alguns problemas comuns relacionados aos compartilhamentos de arquivos do Azure. Ele fornece possíveis causas e soluções alternativas quando esses problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Alta latência, baixa taxa de transferência e problemas gerais de desempenho

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Compartilhamento com limitação

A cota padrão em um compartilhamento Premium é 100 GiB, que fornece um IOPS de linha de base 100 (com um potencial de disparo de até 300 por uma hora). Para obter mais informações sobre como provisionar e sua relação com [](storage-files-planning.md#provisioned-shares) IOPS, consulte a seção compartilhamentos provisionados do guia de planejamento.

Para confirmar se o compartilhamento está sendo limitado, você pode aproveitar as métricas do Azure no Portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** e, emseguida, pesquise métricas.

1. Selecione **Métricas**.

1. Selecione sua conta de armazenamento como o recurso.

1. Selecione **arquivo** como o namespace de métrica.

1. Selecione **Transações** como a métrica.

1. Adicione um filtro para **ResponseType** e verifique se alguma solicitação tem um código de resposta de **SUCCESSWITHTHROTTLING** (para SMB) ou **ClientThrottlingError** (para REST).

![Opções de métricas para compartilhamentos de filepremium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Solução

- Aumente a capacidade de compartilhamento provisionada especificando uma cota maior em seu compartilhamento.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Carga de trabalho pesada de metadados/namespace

Se a maioria de suas solicitações for centrada em metadados, (como CreateFile/OpenFile/CloseFile/QueryInfo/querydirectory), a latência será pior quando comparada com as operações de leitura/gravação.

Para confirmar se a maioria das suas solicitações são centradas em metadados, você pode usar as mesmas etapas acima. Exceto em vez de adicionar um filtropara ResponseType, adicione um filtro para o **nome da API**.

![Filtrar o nome da API em suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução

- Verifique se o aplicativo pode ser modificado para reduzir o número de operações de metadados.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicativo de thread único

Se o aplicativo que está sendo usado pelo cliente for de thread único, isso poderá resultar em IOPS/taxa de transferência significativamente menor do que o máximo possível com base no tamanho do compartilhamento provisionado.

### <a name="solution"></a>Solução

- Aumente o paralelismo do aplicativo aumentando o número de threads.
- Alterne para aplicativos onde o paralelismo é possível. Por exemplo, para operações de cópia, os clientes podem usar o AzCopy ou o RoboCopy de clientes Windows ou o comando **paralelo** em clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta para solicitações

### <a name="cause"></a>Causa

A VM do cliente pode estar localizada em uma região diferente do compartilhamento de arquivos.

### <a name="solution"></a>Solução

- Execute o aplicativo de uma VM que está localizada na mesma região que o compartilhamento de arquivos.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>O cliente não consegue obter a taxa de transferência máxima com suporte pela rede

Uma causa potencial disso é a falta de suporte a vários canais SMB. Atualmente, os compartilhamentos de arquivos do Azure dão suporte apenas a um único canal, portanto, há apenas uma conexão da VM do cliente com o servidor. Essa conexão única é vinculada a um único núcleo na VM do cliente, portanto, a taxa de transferência máxima Obtida de uma VM é associada a um único núcleo.

### <a name="workaround"></a>Solução

- A obtenção de uma VM com um núcleo maior pode ajudar a melhorar a taxa de transferência.
- A execução do aplicativo cliente de várias VMs aumentará a taxa de transferência.
- Use as APIs REST sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>A taxa de transferência em clientes Linux é significativamente menor quando comparada aos clientes Windows.

### <a name="cause"></a>Causa

Esse é um problema conhecido com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução

- Distribuir a carga entre várias VMs
- Na mesma VM, use vários pontos de montagem com a opção **nosharesock** e espalhe a carga entre esses pontos de montagem.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Altas latências de metadados cargas de trabalho pesadas envolvendo operações de abertura/fechamento extensivas.

### <a name="cause"></a>Causa

Falta de suporte para concessões de diretório.

### <a name="workaround"></a>Solução

- Se possível, evite um excesso de identificadores de abertura/fechamento no mesmo diretório em um curto período de tempo.
- Para VMs do Linux, aumente o tempo limite do cache de entrada de diretório especificando **actimeo =\<SEC >** como uma opção de montagem. Por padrão, é um segundo, portanto, um valor maior, como três ou cinco, pode ajudar.
- Para VMs do Linux, atualize o kernel para 4,20 ou superior.

## <a name="low-iops-on-centosrhel"></a>IOPS baixo no CentOS/RHEL

### <a name="cause"></a>Causa

A profundidade de e/s maior que uma não tem suporte no CentOS/RHEL.

### <a name="workaround"></a>Solução

- Atualize para o CentOS 8/RHEL 8.
- Altere para Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivo lenta de e para arquivos do Azure no Linux

Se estiver experimentando uma cópia de arquivos lenta de e para arquivos do Azure, confira a seção [cópia de arquivo lento de e para arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) no guia de solução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Tremulação/padrão de serra-Tooth para IOPS

### <a name="cause"></a>Causa

O aplicativo cliente excede consistentemente o IOPS de linha de base. Atualmente, não há nenhuma suavização do lado do serviço da carga da solicitação; portanto, se o cliente exceder o IOPS de linha de base, ele será limitado pelo serviço. Essa limitação pode resultar no cliente com um padrão de IOPS de tremulação/Serra-Tooth. Nesse caso, o IOPS médio obtido pelo cliente pode ser menor do que o IOPS de linha de base.

### <a name="workaround"></a>Solução

- Reduza a carga de solicitação do aplicativo cliente, para que o compartilhamento não seja limitado.
- Aumente a cota do compartilhamento para que o compartilhamento não seja limitado.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas excessivas de DirectoryOpen/DirectoryClose

### <a name="cause"></a>Causa

Se o número de chamadas DirectoryOpen/DirectoryClose estiver entre as principais chamadas de API e você não espera que o cliente esteja fazendo essa quantidade de chamadas, pode ser um problema com o antivírus instalado na VM do cliente do Azure.

### <a name="workaround"></a>Solução

- Uma correção para esse problema está disponível na [atualização da plataforma de abril para Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>A criação do arquivo é mais lenta do que o esperado

### <a name="cause"></a>Causa

As cargas de trabalho que dependem da criação de um grande número de arquivos não verão uma diferença significativa entre o desempenho de compartilhamentos de arquivos Premium e compartilhamentos de arquivos padrão.

### <a name="workaround"></a>Solução

- Nenhum.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Desempenho lento do Windows 8.1 ou do servidor 2012 R2

### <a name="cause"></a>Causa

Maior que a latência esperada Acessando arquivos do Azure para cargas de trabalho com uso intensivo de e

### <a name="workaround"></a>Solução

- Instale o [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponível.
