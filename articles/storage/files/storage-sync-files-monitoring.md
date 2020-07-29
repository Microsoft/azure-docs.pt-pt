---
title: Monitor Azure File Sync / Microsoft Docs
description: Como monitorizar o Azure File Sync.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0232a0c6526d6dcdfec86dedec437c71e7e21080
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515195"
---
# <a name="monitor-azure-file-sync"></a>Monitorizar o Azure File Sync

Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. Podes ter o número de caches que precisares em todo o mundo.

Este artigo descreve como monitorizar a implementação do Azure File Sync utilizando o Monitor Azure, o Serviço de Sincronização de Armazenamento e o Servidor do Windows.

As seguintes opções de monitorização estão atualmente disponíveis.

## <a name="azure-monitor"></a>Azure Monitor

Utilize [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para visualizar métricas e configurar alertas para sincronização, nivelamento em nuvem e conectividade do servidor.  

### <a name="metrics"></a>Métricas

As métricas do Azure File Sync são ativadas por predefinição e são enviadas para o Azure Monitor a cada 15 minutos.

Para ver as métricas do Azure File Sync no Azure Monitor, selecione o tipo de recurso **Storage Sync Services.**

As seguintes métricas para Azure File Sync estão disponíveis no Azure File Sync:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (carregar e transferir).<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Recuperação de camadas de nuvem | Tamanho dos dados recordados.<br><br>**Nota:** Esta métrica será removida no futuro. Utilize a métrica do tamanho da recolha de camadas cloud para monitorizar o tamanho dos dados recolhidos.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor |
| Tamanho de recuperação de nível de nuvem | Tamanho dos dados recordados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor, Nome do Grupo Sincronizado |
| Tamanho de recolha de camadas de nuvem por aplicação | Tamanho dos dados recolhidos por aplicação.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome da aplicação, nome do servidor, nome do grupo de sincronização |
| Produção de recolha de camadas de nuvem | Tamanho do número de dados de recolha de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor, Nome do Grupo Sincronizado |
| Ficheiros não sincronizados | Contagem de ficheiros que não estão a sincronizar.<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Ficheiros sincronizados | Contagem de ficheiros transferidos (carregar e transferir).<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Estado on-line do servidor | Contagem de batimentos cardíacos recebidos do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1=sessão de sincronização bem sucedida; 0=sessão de sincronização falhada)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |

### <a name="alerts"></a>Alertas

Para configurar alertas no Azure Monitor, selecione o Serviço de Sincronização de Armazenamento e, em seguida, selecione a [métrica Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) para usar para o alerta.  

A tabela a seguir enumera alguns cenários de exemplo para monitorizar e a métrica adequada a utilizar para o alerta:

| Scenario | Métrica para usar para alerta |
|-|-|
| Saúde do ponto final do servidor no portal = Erro | Resultado da sessão de sincronização |
| Os ficheiros não estão a sincronizar um servidor ou ponto final na nuvem | Ficheiros não sincronizados |
| O servidor registado não está a comunicar com o Serviço de Sincronização de Armazenamento | Estado on-line do servidor |
| O tamanho da recuperação do nível da nuvem ultrapassou os 500GiB num dia  | Tamanho de recuperação de nível de nuvem |

Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para visualizar a saúde do servidor registado, a saúde do ponto final do servidor e as métricas, aceda ao Serviço de Sincronização de Armazenamento no portal Azure. Pode ver a saúde do servidor registado na saúde da lâmina e do ponto final do servidor **registados** na lâmina dos **grupos Sync.**

### <a name="registered-server-health"></a>Saúde do servidor registado

- Se o estado do **servidor registado** estiver **Online,** o servidor comunica-se com sucesso com o serviço.
- Se o estado **do servidor registado** estiver **offline,** verifique se o processo do Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) no servidor está em execução. Se o servidor estiver por detrás de uma firewall ou procuração, consulte [este artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar a firewall e o proxy.

### <a name="server-endpoint-health"></a>Saúde do ponto final do servidor

- A saúde do ponto final do servidor no portal baseia-se nos eventos de sincronização que são registados no registo de eventos de Telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como erro cancelado, a sincronização pode ainda parecer saudável no portal enquanto a sessão de sincronização atual estiver a progredir. O ID 9302 do evento é utilizado para determinar se os ficheiros estão a ser aplicados. Para obter mais informações, consulte [a saúde sincronizada](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizar o progresso.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)
- Se o portal mostrar um erro de sincronização porque a sincronização não está a progredir, consulte [a documentação de resolução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientação.

### <a name="metric-charts"></a>Gráficos métricos

- As seguintes tabelas métricas são visualizadas no portal do Serviço de Sincronização de Armazenamento:

  | Nome da métrica | Descrição | Nome da lâmina |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo sincronizado, ponto final do servidor |
  | Recuperação de camadas de nuvem | Tamanho dos dados recordados | Servidores registados |
  | Ficheiros não sincronizados | Contagem de ficheiros que não estão a sincronizar | Ponto final do servidor |
  | Ficheiros sincronizados | Contagem de ficheiros transferidos (upload e download) | Grupo sincronizado, ponto final do servidor |
  | Estado on-line do servidor | Contagem de batimentos cardíacos recebidos do servidor | Servidores registados |

- Para saber mais, consulte [o Azure Monitor.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)

  > [!Note]  
  > As tabelas no portal Storage Sync Service têm um intervalo de tempo de 24 horas. Para visualizar diferentes intervalos de tempo ou dimensões, utilize o Azure Monitor.

## <a name="windows-server"></a>Windows Server

No Windows Server, pode visualizar o tiering da nuvem, o servidor registado e a saúde sincronizada.

### <a name="event-logs"></a>Registos de eventos

Utilize o registo de eventos de Telemetria no servidor para monitorizar a saúde do servidor registado, sincronização e nivelamento da nuvem. O registo de eventos de Telemetria está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

Saúde sincronizada:

- O ID 9102 do evento é registado após o final de uma sessão de sincronização. Utilize este evento para determinar se as sessões de sincronização são bem sucedidas **(HResult = 0**) e se existem erros de sincronização por item. Para obter mais informações, consulte a documentação de erros de [saúde](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [erros por item.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Por vezes, as sessões de sincronização falham globalmente ou têm um PerItemErrorCount não-zero. No entanto, eles ainda fazem progressos, e alguns ficheiros sincronizam com sucesso. Pode ver isto nos campos Aplicados como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Estes campos dizem-lhe quanto da sessão conseguiu. Se vir várias sessões de sincronização a falhar em linha e tiverem uma contagem de aplicação cada vez maior, dê tempo de sincronização para tentar novamente antes de abrir um bilhete de apoio.

- O ID 9302 do evento é registado a cada 5 a 10 minutos se houver uma sessão de sincronização ativa. Utilize este evento para determinar se a sessão de sincronização atual está a progredir **(AppliedItemCount > 0**). Se a sincronização não estiver a progredir, a sessão de sincronização deverá eventualmente falhar e um ID 9102 do Evento será registado com o erro. Para obter mais informações, consulte a documentação de [progresso sincronizada.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Saúde do servidor registado:

- O ID 9301 do evento é registado a cada 30 segundos quando um servidor consulta o serviço para empregos. Se o GetNextJob terminar com **o estado = 0,** o servidor é capaz de comunicar com o serviço. Se o GetNextJob terminar com um erro, verifique a [documentação de resolução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientação.

Saúde de nivelamento em nuvem:

- Para monitorizar a atividade de tiering num servidor, utilize o Evento ID 9003, 9016 e 9029 no registo de eventos de Telemetria, que está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

  - O ID 9003 do evento fornece distribuição de erros para um ponto final do servidor. Por exemplo: Contagem total de erros e Código de Erro. Um evento é registado por código de erro.
  - O ID 9016 do evento fornece resultados fantasmagóricos para um volume. Por exemplo: Por exemplo: Por cento de espaço livre é, Número de ficheiros fantasmagóricos em sessão, e Número de ficheiros não fantasma.
  - O ID 9029 do evento fornece informações de sessão fantasmagórica para um ponto final do servidor. Por exemplo: Número de ficheiros tentados na sessão, Número de ficheiros hierárquicos na sessão e Número de ficheiros já nivelados.
  
- Para monitorizar a atividade de recolha num servidor, utilize o Evento ID 9005, 9006, 9009 e 9059 no registo de eventos da Telemetria, que está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

  - O ID 9005 do evento proporciona fiabilidade de recuperação para um ponto final do servidor. Por exemplo: Ficheiros únicos totais acedidos e ficheiros exclusivos total com acesso falhado.
  - O ID 9006 do evento fornece uma distribuição de erro de recuperação para um ponto final do servidor. Por exemplo: Total de Pedidos Falhados e Código de Erro. Um evento é registado por código de erro.
  - O ID 9009 do evento fornece informações de sessão de recuperação para um ponto final do servidor. Por exemplo: DuraçõesSegundos, CondeFilesRecallSucceeded e CountFilesRecallFailed.
  - O ID 9059 do evento fornece distribuição de recolha de aplicações para um ponto final do servidor. Por exemplo: ShareId, Nome da Aplicação e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Utilize os contadores de desempenho Azure File Sync no servidor para monitorizar a atividade de sincronização.

Para ver os contadores de desempenho do Azure File Sync no servidor, abra o Monitor de Desempenho (Perfmon.exe). Pode encontrar os contadores sob os objetos DE Operações **de Sincronização de Bytes AFS e** **AFS.**

Os seguintes contadores de desempenho para Azure File Sync estão disponíveis no Monitor de Desempenho:

| Objeto de desempenho\Nome do contador | Descrição |
|-|-|
| AFS Bytes Transferido\Downloaded Bytes/sec | Número de bytes descarregados por segundo. |
| AFS Bytes transferido\Uploaded Bytes/sec | Número de bytes carregados por segundo. |
| AFS Bytes transferido\Total Bytes/seg | Total de bytes por segundo (carregar e baixar). |
| Operações de sincronização DA AFS\Ficheiros de sincronização descarregados/seg | Número de ficheiros descarregados por segundo. |
| Operações de sincronização AFS\Ficheiros de sincronização carregados/seg | Número de ficheiros enviados por segundo. |
| OPERAÇÕES DE SINCRONIZAÇÃO AFS\Total sync operações/seg | Número total de ficheiros sincronizados (carregar e transferir). |

## <a name="next-steps"></a>Próximos passos
- [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
- [Considere as definições de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Resolver problemas da Sincronização de Ficheiros do Azure](storage-sync-files-troubleshoot.md)
- [Ficheiros Azure frequentemente fazem perguntas](storage-files-faq.md)
