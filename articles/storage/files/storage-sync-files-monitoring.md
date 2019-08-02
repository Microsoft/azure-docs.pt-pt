---
title: Monitorar Sincronização de Arquivos do Azure | Microsoft Docs
description: Como monitorar Sincronização de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699292"
---
# <a name="monitor-azure-file-sync"></a>Monitorizar o Azure File Sync

Use Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. Sincronização de Arquivos do Azure transforma o Windows Server em um cache rápido de seu compartilhamento de arquivos do Azure. Você pode usar qualquer protocolo que esteja disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter quantos caches forem necessários em todo o mundo.

Este artigo descreve como monitorar sua implantação do Sincronização de Arquivos do Azure usando Azure Monitor, o serviço de sincronização de armazenamento e o Windows Server.

As opções de monitoramento a seguir estão disponíveis no momento.

## <a name="azure-monitor"></a>Azure Monitor

Use [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para exibir as métricas e configurar alertas para sincronização, camadas de nuvem e conectividade de servidor.  

### <a name="metrics"></a>Métricas

As métricas para Sincronização de Arquivos do Azure são habilitadas por padrão e enviadas para Azure Monitor a cada 15 minutos.

Para exibir Sincronização de Arquivos do Azure métricas em Azure Monitor, selecione o tipo de recurso **serviços de sincronização de armazenamento** .

As seguintes métricas para Sincronização de Arquivos do Azure estão disponíveis em Azure Monitor:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (carregar e baixar).<br><br>Unidade Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome do grupo de sincronização |
| Recall de camadas de nuvem | Tamanho dos dados recuperados.<br><br>**Nota**: Essa métrica será removida no futuro. Use a métrica de tamanho de recuperação de camadas de nuvem para monitorar o tamanho dos dados recuperados.<br><br>Unidade Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor |
| Tamanho de recall em camadas de nuvem | Tamanho dos dados recuperados.<br><br>Unidade Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor, nome do grupo de sincronização |
| Tamanho de recall de camadas de nuvem por aplicativo | Tamanho dos dados recuperados pelo aplicativo.<br><br>Unidade Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do aplicativo, nome do servidor, nome do grupo de sincronização |
| Taxa de transferência de recall em camadas de nuvem | Tamanho da taxa de transferência de recall de dados.<br><br>Unidade Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor, nome do grupo de sincronização |
| Os ficheiros não estão a sincronizar | Contagem de arquivos que estão falhando na sincronização.<br><br>Unidade Count<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome do grupo de sincronização |
| Arquivos sincronizados | Contagem de arquivos transferidos (carregar e baixar).<br><br>Unidade Count<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome do grupo de sincronização |
| Status online do servidor | Contagem de pulsações recebidas do servidor.<br><br>Unidade Count<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = sessão de sincronização com êxito; 0 = sessão de sincronização com falha)<br><br>Unidade Count<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome do grupo de sincronização |

### <a name="alerts"></a>Alertas

Para configurar alertas no Azure Monitor, selecione o serviço de sincronização de armazenamento e, em seguida, selecione a [métrica de sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) a ser usada para o alerta.  

A tabela a seguir lista alguns cenários de exemplo para monitorar e a métrica apropriada a ser usada para o alerta:

| Cenário | Métrica a ser usada para alerta |
|-|-|
| Integridade do ponto de extremidade do servidor no portal = erro | Resultado da sessão de sincronização |
| Os arquivos estão falhando ao sincronizar com um ponto de extremidade de servidor ou de nuvem | Os ficheiros não estão a sincronizar |
| O servidor registrado não está conseguindo se comunicar com o serviço de sincronização de armazenamento | Status online do servidor |
| O tamanho de recall em camadas de nuvem excedeu 500GiB em um dia  | Tamanho de recall em camadas de nuvem |

Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para exibir a integridade do servidor registrado, a integridade do ponto de extremidade do servidor e as métricas, vá para o serviço de sincronização de armazenamento no portal do Azure. Você pode exibir a integridade do servidor registrado na folha **servidores registrados** e a integridade do ponto de extremidade do servidor na folha **grupos de sincronização** .

### <a name="registered-server-health"></a>Integridade do servidor registrado

- Se o estado do **servidor registrado** estiver **online**, o servidor estará se comunicando com êxito com o serviço.
- Se o estado do **servidor registrado** for **exibido offline**, verifique se o processo do monitor de sincronização de armazenamento (AzureStorageSyncMonitor. exe) no servidor está em execução. Se o servidor estiver protegido por um firewall ou proxy, consulte [Este artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar o firewall e o proxy.

### <a name="server-endpoint-health"></a>Integridade do ponto de extremidade do servidor

- A integridade do ponto de extremidade do servidor no portal é baseada nos eventos de sincronização que são registrados no log de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como erro cancelado, a sincronização ainda poderá aparecer íntegra no portal, desde que a sessão de sincronização atual esteja progredindo. A ID de evento 9302 é usada para determinar se os arquivos estão sendo aplicados. Para obter mais informações, consulte [sincronizar integridade](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizar andamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se o portal mostrar um erro de sincronização porque a sincronização não está progredindo, consulte a [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter diretrizes.

### <a name="metric-charts"></a>Gráficos de métricas

- Os gráficos de métrica a seguir são visíveis no portal do serviço de sincronização de armazenamento:

  | Nome da métrica | Descrição | Nome da folha |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (carregar e baixar) | Grupo de sincronização, ponto de extremidade do servidor |
  | Recall de camadas de nuvem | Tamanho dos dados recuperados | Servidores Registados |
  | Os ficheiros não estão a sincronizar | Contagem de arquivos que estão falhando na sincronização | Ponto de extremidade do servidor |
  | Arquivos sincronizados | Contagem de arquivos transferidos (carregar e baixar) | Grupo de sincronização, ponto de extremidade do servidor |
  | Status online do servidor | Contagem de pulsações recebidas do servidor | Servidores Registados |

- Para saber mais, consulte [Azure monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Os gráficos no portal do serviço de sincronização de armazenamento têm um intervalo de tempo de 24 horas. Para exibir diferentes intervalos de tempo ou dimensões, use Azure Monitor.

## <a name="windows-server"></a>Windows Server

No Windows Server, você pode exibir a camada de nuvem, o servidor registrado e a integridade da sincronização.

### <a name="event-logs"></a>Registos de eventos

Use o log de eventos de telemetria no servidor para monitorar a integridade do servidor registrado, sincronização e camadas de nuvem. O log de eventos de telemetria está localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

Integridade da sincronização:

- A ID de evento 9102 é registrada após a conclusão de uma sessão de sincronização. Use esse evento para determinar se as sessões de sincronização são bem-sucedidas (**HRESULT = 0**) e se há erros de sincronização por item. Para obter mais informações, consulte a documentação [sincronizar integridade](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [erros por item](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > Às vezes, as sessões de sincronização falham em geral ou têm um PerItemErrorCount diferente de zero. No entanto, eles ainda encaminham o progresso e alguns arquivos são sincronizados com êxito. Você pode ver isso nos campos aplicados, como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Esses campos informam quanto da sessão foi bem-sucedida. Se você vir a falha de várias sessões de sincronização em uma linha e elas tiverem uma contagem aplicada crescente, forneça o tempo de sincronização para tentar novamente antes de abrir um tíquete de suporte.

- A ID de evento 9302 é registrada a cada 5 a 10 minutos se houver uma sessão de sincronização ativa. Use esse evento para determinar se a sessão de sincronização atual está fazendo o andamento (**AppliedItemCount > 0**). Se a sincronização não estiver progredindo, a sessão de sincronização deverá eventualmente falhar e uma ID de evento 9102 será registrada com o erro. Para obter mais informações, consulte a [documentação progresso da sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integridade do servidor registrado:

- A ID de evento 9301 é registrada a cada 30 segundos quando um servidor consulta o serviço para trabalhos. Se GetNextJob for concluído com **status = 0**, o servidor será capaz de se comunicar com o serviço. Se GetNextJob for concluído com um erro, consulte a [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter diretrizes.

Integridade de camadas de nuvem:

- Para monitorar a atividade de camadas em um servidor, use a ID de evento 9003, 9016 e 9029 no log de eventos de telemetria, que está localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

  - A ID do evento 9003 fornece a distribuição de erros para um ponto de extremidade do servidor. Por exemplo: Contagem total de erros e ErrorCode. Um evento é registrado por código de erro.
  - A ID de evento 9016 fornece resultados fantasmas para um volume. Por exemplo: O percentual de espaço livre é, o número de arquivos fantasmas na sessão e o número de arquivos com falha no fantasma.
  - A ID de evento 9029 fornece informações de sessão de fantasma para um ponto de extremidade do servidor. Por exemplo: Número de arquivos tentados na sessão, número de arquivos em camadas na sessão e número de arquivos já em camadas.
  
- Para monitorar a atividade de recuperação em um servidor, use a ID de evento 9005, 9006, 9009 e 9059 no log de eventos de telemetria, localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

  - A ID do evento 9005 fornece a confiabilidade de recuperação para um ponto de extremidade do servidor. Por exemplo: Total de arquivos exclusivos acessados e total de arquivos exclusivos com acesso com falha.
  - A ID do evento 9006 fornece distribuição de erro de recuperação para um ponto de extremidade do servidor. Por exemplo: Total de solicitações com falha e ErrorCode. Um evento é registrado por código de erro.
  - A ID do evento 9009 fornece informações da sessão de recuperação para um ponto de extremidade do servidor. Por exemplo: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - A ID de evento 9059 fornece a distribuição de recuperação de aplicativo para um ponto de extremidade do servidor. Por exemplo: Shareid, nome do aplicativo e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Use os contadores de desempenho Sincronização de Arquivos do Azure no servidor para monitorar a atividade de sincronização.

Para exibir Sincronização de Arquivos do Azure contadores de desempenho no servidor, abra o monitor de desempenho (Perfmon. exe). Você pode encontrar os contadores nos objetos de **operações de sincronização** de **AFS bytes transferidos** e AFS.

Os contadores de desempenho a seguir para Sincronização de Arquivos do Azure estão disponíveis no monitor de desempenho:

| Nome do Objeto\contador de desempenho | Descrição |
|-|-|
| Transferred\Downloaded de bytes AFS bytes/s | Número de bytes baixados por segundo. |
| Transferred\Uploaded de bytes AFS bytes/s | Número de bytes carregados por segundo. |
| Transferred\Total de bytes AFS bytes/s | Total de bytes por segundo (carregar e baixar). |
| Arquivos de sincronização de Operations\Downloaded de sincronização AFS/s | Número de arquivos baixados por segundo. |
| Arquivos de sincronização de Operations\Uploaded de sincronização AFS/s | Número de arquivos carregados por segundo. |
| Operações de arquivo de sincronização Operations\Total de sincronização AFS/s | Número total de arquivos sincronizados (carregar e baixar). |

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Considerar as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
- [Perguntas frequentes sobre arquivos do Azure](storage-files-faq.md)
