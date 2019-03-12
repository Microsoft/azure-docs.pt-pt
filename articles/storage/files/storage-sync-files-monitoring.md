---
title: Monitorizar a sincronização de ficheiros do Azure | Documentos da Microsoft
description: Como monitorizar a sincronização de ficheiros do Azure.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 4ae17249903f317e7a75a3e6bc7c03292021c96a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534638"
---
# <a name="monitor-azure-file-sync"></a>Monitorizar o Azure File Sync

Utilize o Azure File Sync para centralizar as partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode usar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Pode ter o número de caches que precisar em todo o mundo.

Este artigo descreve como monitorizar a implementação do Azure File Sync com o portal do Azure e o Windows Server.

As seguintes opções de monitorização estão atualmente disponíveis.

## <a name="azure-portal"></a>Portal do Azure

No portal do Azure, pode ver o estado de funcionamento do servidor registado e métricas de estado de funcionamento do ponto final de servidor (estado de funcionamento de sincronização).

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para ver o estado de funcionamento do servidor registado e métricas de estado de funcionamento de ponto final de servidor, vá para o serviço de sincronização de armazenamento no portal do Azure. Pode ver o estado de funcionamento do servidor registado no **servidores registados** painel e servidor de ponto final estado de funcionamento no **sincronizar grupos** painel.

Estado de funcionamento do servidor registado:

- Se o **servidor registado** estado é **Online**, o servidor que está a comunicar corretamente com o serviço.
- Se o **servidor registado** estado é **aparece Offline**, certifique-se de que o processo de Monitor de sincronização de armazenamento (AzureStorageSyncMonitor.exe) no servidor está em execução. Se o servidor estiver protegido por uma firewall ou proxy, consulte [este artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar a firewall e proxy.

Estado de funcionamento do servidor ponto final:

- O estado de funcionamento do ponto final do servidor no portal do baseia-se sobre os eventos de sincronização que são registados no registo de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como o erro foi cancelada, sincronização poderá ainda aparecer bom estado de funcionamento no portal, desde que a sessão de sincronização atual está a tornar o progresso. ID de evento 9302 é utilizado para determinar se os ficheiros estão a ser aplicados. Para obter mais informações, consulte [sincronizar o estado de funcionamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizar progresso](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se o portal mostra um erro de sincronização porque a sincronização não está a tornar o progresso, consulte a [documentação de resolução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientações.

Métricas:

- As seguintes métricas estão visíveis no portal do serviço de sincronização de armazenamento:

  | Nome da métrica | Descrição | Nome do painel |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (carregamento e transferência) | Grupo de sincronização, o ponto final do servidor |
  | Lembre-se camadas da cloud | Tamanho dos dados recuperados | Servidores Registados |
  | Os ficheiros não estão a sincronizar | Contagem de ficheiros que estão a falhar para sincronizar | Ponto final do servidor |
  | Ficheiros sincronizados | Contagem de ficheiros transferidos (carregamento e transferência) | Grupo de sincronização, o ponto final do servidor |
  | Estado online do servidor | Contagem de heartbeats recebido do servidor | Servidores Registados |

- Para obter mais informações, consulte [do Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Os gráficos no portal do serviço de sincronização de armazenamento tem um intervalo de tempo de 24 horas. Para ver os intervalos de tempo diferente ou dimensões, utilize o Azure Monitor.

### <a name="azure-monitor"></a>Azure Monitor

Uso [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para monitorizar a sincronização de camada de cloud e conetividade do servidor. Métricas para o Azure File Sync estão ativadas por predefinição e são enviadas para o Azure Monitor a cada 15 minutos.

Para ver métricas de sincronização de ficheiros do Azure no Azure Monitor, selecione o **serviços de sincronização de armazenamento** tipo de recurso.

As métricas seguintes para o Azure File Sync estão disponíveis no Azure Monitor:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (carregamento e transferência).<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicável: Nome de servidor Endpoint nome, sincronização direção, grupo de sincronização |
| Lembre-se camadas da cloud | Tamanho dos dados recuperados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do Servidor |
| Os ficheiros não estão a sincronizar | Contagem de ficheiros que estão a falhar para sincronizar.<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicável: Nome de servidor Endpoint nome, sincronização direção, grupo de sincronização |
| Ficheiros sincronizados | Contagem de ficheiros transferidos (carregamento e transferência).<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicável: Nome de servidor Endpoint nome, sincronização direção, grupo de sincronização |
| Estado online do servidor | Número de heartbeats recebido do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do Servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = a sincronização com êxito sessão; 0 = a sessão de sincronização com falha)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicável: Nome de servidor Endpoint nome, sincronização direção, grupo de sincronização |

## <a name="windows-server"></a>Windows Server

No Windows Server, pode ver na cloud em camadas, servidor registado e sincronizar o estado de funcionamento.

### <a name="event-logs"></a>Registos de eventos

Utilize o registo de eventos de telemetria no servidor para monitorizar o servidor registado, sincronização e estado de funcionamento de camada de cloud. O registo de eventos de telemetria está localizado no Visualizador de eventos sob *aplicativos e Services\Microsoft\FileSync\Agent*.

Estado de funcionamento de sincronização:

- ID de evento 9102 é iniciado após a conclusão de uma sessão de sincronização. Utilize este evento para determinar se a sessões de sincronização estão bem sucedidas (**HResult = 0**) e se existem por item erros de sincronização. Para obter mais informações, consulte a [sincronizar o estado de funcionamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [erros por item](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) documentação.

  > [!Note]  
  > Por vezes, as sessões de sincronização falharem em geral ou tem um PerItemErrorCount diferente de zero. No entanto, ainda manter o andamento e alguns arquivos de sincronização com êxito. Pode ver isso nos campos aplicados como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Estes campos informá-lo quanto a sessão foi concluída com êxito. Se vir várias sessões de sincronização falhar numa linha e têm uma contagem de aplicados de cada vez maior, dar tempo de sincronização para tentar novamente antes de abrir um pedido de suporte.

- ID de evento 9302 é registado a cada 5 a 10 minutos, se existe uma sessão de sincronização do Active Directory. Utilize este evento para determinar se a sessão de sincronização atual está fazendo progresso (**AppliedItemCount > 0**). Se a sincronização não está fazendo progressos, a sessão de sincronização deve, eventualmente, falhar e uma 9102 de ID de evento será registado com o erro. Para obter mais informações, consulte a [documentação de progresso de sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Estado de funcionamento do servidor registado:

- ID de evento 9301 é registado quando um servidor de consulta o serviço para as tarefas a cada 30 segundos. Se GetNextJob termina com **estado = 0**, o servidor é capaz de comunicar com o serviço. Se GetNextJob terminar com um erro, consulte a [documentação de resolução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientações.

Estado de funcionamento de camadas da cloud:

- Para monitorizar a atividade de disposição em camadas num servidor, utilize 9003 de ID de evento, 9016 e 9029 no registo de eventos de telemetria, que está localizado no Visualizador de eventos em *aplicativos e Services\Microsoft\FileSync\Agent*.

  - ID de evento 9003 fornece distribuição de erros para um ponto final do servidor. Por exemplo: Contagem total de erros e código de erro. Um evento é registado por código de erro.
  - ID de evento 9016 fornece resultados à conversão em fantasma para um volume. Por exemplo: Percentagem de espaço livre é, número de ficheiros fantasma na sessão, e o número de ficheiros falhadas para fantasma.
  - ID de evento 9029 fornece informações de sessão à conversão em fantasma para um ponto final do servidor. Por exemplo: Número de ficheiros tentada a sessão, o número de ficheiros em camadas na sessão, e o número de ficheiros já em camadas.
  
- Para monitorizar a atividade de recolhimento num servidor, utilize 9005 de ID de evento, 9006, 9009 e 9059 no registo de eventos de telemetria, que está localizado no Visualizador de eventos em *aplicativos e Services\Microsoft\FileSync\Agent*.

  - ID de evento 9005 fornece confiabilidade de recolhimento de um ponto de final do servidor. Por exemplo: Total de ficheiros exclusivos acedidos e Total de ficheiros exclusivos com acesso falhadas.
  - ID de evento 9006 fornece distribuição de erros de recolhimento de um ponto de final do servidor. Por exemplo: Total de pedidos falhados e código de erro. Um evento é registado por código de erro.
  - ID de evento 9009 fornece informações de sessão de recolhimento de um ponto de final do servidor. Por exemplo: DurationSeconds CountFilesRecallSucceeded e CountFilesRecallFailed.
  - ID de evento 9059 fornece distribuição de solicitação de recolhimento de aplicativo para um ponto final do servidor. Por exemplo: ShareId, nome da aplicação e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Utilize os contadores de desempenho de sincronização de ficheiros do Azure no servidor para monitorizar a atividade de sincronização.

Para ver contadores de desempenho de sincronização de ficheiros do Azure no servidor, abra o Monitor de desempenho (Perfmon.exe). Pode encontrar os contadores no **transferidos Bytes a AFS** e **operações de sincronização de AFS** objetos.

Os seguintes contadores de desempenho para o Azure File Sync estão disponíveis no Monitor de desempenho:

| Nome de Object\Counter de desempenho | Descrição |
|-|-|
| AFS Bytes Transferred\Downloaded Bytes/seg | Número de bytes transferidos por segundo. |
| AFS Bytes Transferred\Uploaded Bytes/seg | Número de bytes carregados por segundo. |
| AFS Bytes Transferred\Total Bytes/seg | Total de bytes por segundo (carregamento e transferência). |
| AFS sincronização Operations\Downloaded sincronização ficheiros/seg | Número de ficheiros transferidos por segundo. |
| AFS sincronização Operations\Uploaded sincronização ficheiros/seg | Número de ficheiros carregados por segundo. |
| AFS sincronização Operations\Total sincronização ficheiro operações/seg | Número total de ficheiros sincronizados (carregamento e transferência). |

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Considere as definições de proxy e firewall](storage-sync-files-firewall-and-proxy.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Resolver problemas da sincronização de ficheiros do Azure](storage-sync-files-troubleshoot.md)
- [Perguntas mais frequentes sobre os ficheiros do Azure](storage-files-faq.md)
