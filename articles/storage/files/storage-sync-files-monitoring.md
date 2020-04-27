---
title: Monitor Azure File Sync [ Microsoft Docs
description: Como monitorizar o Sync de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68699292"
---
# <a name="monitor-azure-file-sync"></a>Monitorizar o Azure File Sync

Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Podes ter as caches que precisares em todo o mundo.

Este artigo descreve como monitorizar a sua implementação de Sincronização de Ficheiros Azure utilizando o Monitor Azure, o Serviço de Sincronização de Armazenamento e o Servidor Windows.

As seguintes opções de monitorização estão atualmente disponíveis.

## <a name="azure-monitor"></a>Azure Monitor

Utilize o [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para visualizar métricas e configurar alertas para sincronização, tiering em nuvem e conectividade do servidor.  

### <a name="metrics"></a>Métricas

As métricas para o Sync de Ficheiros Azure são ativadas por padrão e são enviadas para o Monitor Azure a cada 15 minutos.

Para ver as métricas de Sincronização de Ficheiros Azure no Monitor Azure, selecione o tipo de recurso de **Serviços de Sincronização** de Armazenamento.

As seguintes métricas para o Azure File Sync estão disponíveis no Monitor Azure:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (upload e download).<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo sincronizado |
| Recolha de tiering de nuvem | Tamanho dos dados recordados.<br><br>**Nota:** Esta métrica será removida no futuro. Utilize a métrica de tamanho de recolha de camadas cloud para monitorizar o tamanho dos dados recolhidos.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do Servidor |
| Tamanho da recuperação do tiering de nuvem | Tamanho dos dados recordados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do Servidor, Nome do Grupo Sync |
| Tamanho de recolha de nível de nuvem por aplicação | Tamanho dos dados recordados pela aplicação.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome da aplicação, nome do servidor, nome do grupo sincronizado |
| Entrada de recolha de tiering de cloud | Tamanho da recolha de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do Servidor, Nome do Grupo Sync |
| Ficheiros que não sincronizam | Contagem de ficheiros que não sincronizam.<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo sincronizado |
| Ficheiros sincronizados | Contagem de ficheiros transferidos (upload e download).<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo sincronizado |
| Estado on-line do servidor | Contagem de batimentos cardíacos recebidos do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do Servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1=sessão de sincronização bem sucedida; 0=sessão de sincronização falhada)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo sincronizado |

### <a name="alerts"></a>Alertas

Para configurar alertas no Monitor Azure, selecione o Serviço de Sincronização de Armazenamento e, em seguida, selecione a métrica De sincronização de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) para utilizar para o alerta.  

A tabela que se segue enumera alguns cenários de exemplo para monitorizar e a métrica adequada para usar para o alerta:

| Cenário | Métrica para usar para alerta |
|-|-|
| Saúde final do servidor no portal = Erro | Resultado da sessão de sincronização |
| Os ficheiros estão a falhar na sincronização de um servidor ou ponto final de nuvem | Ficheiros que não sincronizam |
| O servidor registado não está a comunicar com o Serviço de Sincronização de Armazenamento | Estado on-line do servidor |
| O tamanho da recolha de nível de nuvem ultrapassou os 500GiB num dia  | Tamanho da recuperação do tiering de nuvem |

Para saber mais sobre a configuração de alertas no Monitor Azure, consulte a [visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para ver a saúde do servidor registado, a saúde do ponto final do servidor e as métricas, vá ao Serviço de Sincronização de Armazenamento no portal Azure. Pode visualizar a saúde do servidor registado na lâmina dos **servidores registados** e na saúde final do servidor na lâmina dos **grupos Sync.**

### <a name="registered-server-health"></a>Saúde do servidor registado

- Se o estado do **servidor Registado** estiver **Online,** o servidor está a comunicar com sucesso com o serviço.
- Se o estado do **servidor registado** estiver **offline,** verifique se o processo do Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) no servidor está a funcionar. Se o servidor estiver por detrás de uma firewall ou procuração, consulte [este artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar a firewall e o proxy.

### <a name="server-endpoint-health"></a>Saúde do ponto final do servidor

- A saúde do ponto final do servidor no portal baseia-se nos eventos de sincronização que estão registados no registo do evento Telemettry no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como o erro cancelado, a sincronização pode ainda parecer saudável no portal enquanto a sessão de sincronização atual estiver a progredir. O ID do evento 9302 é utilizado para determinar se os ficheiros estão a ser aplicados. Para mais informações, consulte [sincronizar a saúde](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizar o progresso.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)
- Se o portal mostrar um erro de sincronização porque a sincronização não está a progredir, consulte a documentação de resolução de [problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para orientação.

### <a name="metric-charts"></a>Gráficos métricos

- Os seguintes gráficos métricos são visualizados no portal do Serviço de Sincronização de Armazenamento:

  | Nome da métrica | Descrição | Nome da lâmina |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo Sync, Ponto final do Servidor |
  | Recolha de tiering de nuvem | Tamanho dos dados recordados | Servidores registados |
  | Ficheiros que não sincronizam | Contagem de ficheiros que não estão a sincronizar | Ponto final do servidor |
  | Ficheiros sincronizados | Contagem de ficheiros transferidos (upload e download) | Grupo Sync, Ponto final do Servidor |
  | Estado on-line do servidor | Contagem de batimentos cardíacos recebidos do servidor | Servidores registados |

- Para saber mais, consulte [o Monitor Azure.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)

  > [!Note]  
  > As tabelas no portal storage Sync Service têm um intervalo de tempo de 24 horas. Para ver diferentes intervalos de tempo ou dimensões, utilize o Monitor Azure.

## <a name="windows-server"></a>Windows Server

No Windows Server, pode ver o tiering em nuvem, o servidor registado e a saúde sincronizada.

### <a name="event-logs"></a>Registos de eventos

Utilize o registo de eventos da Telemetria no servidor para monitorizar a saúde do servidor registado, sincronização e nível de nuvem. O registo de eventos da Telemetria está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

Saúde sincronizada:

- O ID do evento 9102 é registado após o final de uma sessão de sincronização. Utilize este evento para determinar se as sessões de sincronização são bem sucedidas **(HResult = 0**) e se existem erros de sincronização por item. Para obter mais informações, consulte a documentação de erros de [saúde sincronizada](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [por item.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Por vezes, as sessões de sincronização falham no geral ou têm um PerItemErrorCount não-zero. No entanto, continuam a progredir e alguns ficheiros sincronizam-se com sucesso. Pode ver isto nos campos Aplicados, tais como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Estes campos dizem-lhe quanto da sessão foi bem sucedida. Se vir várias sessões de sincronização falharem seguidamente, e tiverem uma contagem de aplicação cada vez maior, dê tempo de sincronização para tentar novamente antes de abrir um bilhete de apoio.

- O ID do evento 9302 é registado a cada 5 a 10 minutos se houver uma sessão de sincronização ativa. Utilize este evento para determinar se a sessão de sincronização atual está a progredir **(AppliedItemCount > 0**). Se a sincronização não estiver a progredir, a sessão de sincronização deve eventualmente falhar, e um Id de evento 9102 será registado com o erro. Para mais informações, consulte a documentação de [progresso sincronizado.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Saúde do servidor registado:

- O ID do evento 9301 é registado a cada 30 segundos quando um servidor questiona o serviço para trabalhos. Se o GetNextJob terminar com o **estado = 0,** o servidor é capaz de comunicar com o serviço. Se o GetNextJob terminar com um erro, verifique a documentação de resolução de [problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientação.

Saúde de tiering de nuvem:

- Para monitorizar a atividade de tiering num servidor, utilize o Event ID 9003, 9016 e 9029 no registo de eventos da Telemettry, que está localizado no Event Viewer ao abrigo de *Aplicações e Serviços\Microsoft\FileSync\Agent*.

  - O ID do evento 9003 fornece a distribuição de erros para um ponto final do servidor. Por exemplo: Contagem total de erros e Código de Erro. Um evento é registado por código de erro.
  - O ID do evento 9016 fornece resultados fantasmagóricas para um volume. Por exemplo: O espaço livre por cento é, número de ficheiros fantasmagórico em sessão, e número de ficheiros não se fantasma.
  - O ID do evento 9029 fornece informações de sessão fantasmagórica para um ponto final do servidor. Por exemplo: Número de ficheiros tentados na sessão, Número de ficheiros hierarquizados na sessão e Número de ficheiros já nividamente.
  
- Para monitorizar a atividade de recolha num servidor, utilize o Event ID 9005, 9006, 9009 e 9059 no registo de eventos da Telemettry, que está localizado no Event Viewer ao abrigo de *Aplicações e Serviços\Microsoft\FileSync\Agent*.

  - O ID do evento 9005 proporciona a fiabilidade da recuperação de um ponto final do servidor. Por exemplo: Total de ficheiros únicos acedidos e Total ficheiros únicos com acesso falhado.
  - O ID do evento 9006 fornece a distribuição de erros de recolha para um ponto final do servidor. Por exemplo: Total de Pedidos Falhados e ErrorCode. Um evento é registado por código de erro.
  - O ID do evento 9009 fornece informações de sessão de recolha para um ponto final do servidor. Por exemplo: DuraçõesSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - O ID do evento 9059 fornece a distribuição de recolha de aplicações para um ponto final do servidor. Por exemplo: ShareId, Nome da Aplicação e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Utilize os contadores de desempenho do Ficheiro Azure Sync no servidor para monitorizar a atividade de sincronização.

Para visualizar os contadores de desempenho do Web File Sync no servidor, abra o Monitor de Desempenho (Perfmon.exe). Pode encontrar os contadores sob os **bytes AFS Transferidos** e **Objetos de Operações sincronizadas AFS.**

Os seguintes contadores de desempenho para O Sincronizado de Ficheiros Azure estão disponíveis no Monitor de Desempenho:

| Objeto de desempenho\Contra Nome | Descrição |
|-|-|
| Bytes AFS transferidos\Bytes/seg descarregados | Número de bytes descarregados por segundo. |
| Bytes AFS transferidos\Bytes/seg | Número de bytes carregados por segundo. |
| Bytes AFS transferidos\Total Bytes/seg | Total de bytes por segundo (upload e download). |
| Operações sincronizadas AFS\Ficheiros de Sincronização descarregados/seg | Número de ficheiros descarregados por segundo. |
| Operações sincronizadas AFS\Uploaded Sync Files/seg | Número de ficheiros enviados por segundo. |
| Operações de sincronização AFS\Total Sync File Operations/seg | Número total de ficheiros sincronizados (upload e download). |

## <a name="next-steps"></a>Passos seguintes
- [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
- [Considere configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Resolver problemas da Sincronização de Ficheiros do Azure](storage-sync-files-troubleshoot.md)
- [Ficheiros Azure frequentemente fazem perguntas](storage-files-faq.md)
