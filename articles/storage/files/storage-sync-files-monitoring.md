---
title: Monitor Azure File Sync / Microsoft Docs
description: Reveja como monitorizar a implementação do Azure File Sync utilizando o Azure Monitor, o Storage Sync Service e o Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 87d0b740ec4f7ffb8966b386c273c023f69c42d8
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008304"
---
# <a name="monitor-azure-file-sync"></a>Monitorizar o Azure File Sync

Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. Podes ter o número de caches que precisares em todo o mundo.

Este artigo descreve como monitorizar a implementação do Azure File Sync utilizando o Monitor Azure, o Serviço de Sincronização de Armazenamento e o Servidor do Windows.

Os seguintes cenários estão abrangidos por este guia: 
- Ver métricas de Sincronização de Ficheiros Azure no Monitor Azure.
- Crie alertas no Azure Monitor para o notificar proativamente das condições críticas.
- Ver saúde da sua implementação Azure File Sync utilizando o portal Azure.
- Como utilizar os registos de eventos e os contadores de desempenho nos seus Servidores windows para monitorizar a saúde da sua implementação de Azure File Sync. 

## <a name="azure-monitor"></a>Azure Monitor

Utilize [o Azure Monitor](../../azure-monitor/overview.md) para visualizar métricas e configurar alertas para sincronização, nivelamento em nuvem e conectividade do servidor.  

### <a name="metrics"></a>Métricas

As métricas do Azure File Sync são ativadas por predefinição e são enviadas para o Azure Monitor a cada 15 minutos.

**Como ver as métricas do Azure File Sync no Azure Monitor**
1. Vá ao seu **Serviço de Sincronização de Armazenamento** no portal **Azure** e clique em **Métricas**.
2. Clique na **métrica** e selecione a métrica que pretende visualizar.

![Screenshot das métricas Azure File Sync](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

As seguintes métricas para Azure File Sync estão disponíveis no Azure File Sync:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (carregar e transferir).<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Recuperação de camadas de nuvem | Tamanho dos dados recordados.<br><br>**Nota:** Esta métrica será removida no futuro. Utilize a métrica do tamanho da recolha de camadas cloud para monitorizar o tamanho dos dados recolhidos.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do servidor |
| Tamanho de recuperação de nível de nuvem | Tamanho dos dados recordados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do servidor, Nome do Grupo Sincronizado |
| Tamanho de recolha de camadas de nuvem por aplicação | Tamanho dos dados recolhidos por aplicação.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome da aplicação, nome do servidor, nome do grupo de sincronização |
| Produção de recolha de camadas de nuvem | Tamanho do número de dados de recolha de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do servidor, Nome do Grupo Sincronizado |
| Ficheiros não sincronizados | Contagem de ficheiros que não estão a sincronizar.<br><br>Unidade: Contagem<br>Tipos de agregação: Média, Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Ficheiros sincronizados | Contagem de ficheiros transferidos (carregar e transferir).<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |
| Estado on-line do servidor | Contagem de batimentos cardíacos recebidos do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1=sessão de sincronização bem sucedida; 0=sessão de sincronização falhada)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto final do servidor, direção de sincronização, nome do grupo de sincronização |

### <a name="alerts"></a>Alertas

Os alertas notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Para saber mais sobre a configuração de alertas no Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

**Como criar alertas para a Azure File Sync**

1. Aceda ao seu **Serviço de Sincronização de Armazenamento** no portal **Azure**. 
2. Clique em **Alertas** na secção de Monitorização e, em seguida, clique **em + Nova regra de alerta**.
3. Clique **em Selecionar a condição** e fornecer as seguintes informações para o alerta: 
    - **Métrica**
    - **Nome de dimensão**
    - **Lógica de alerta**
4. Clique **em Selecionar grupo de ação** e adicione um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
5. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
6. Clique **em Criar regra de alerta** para criar o alerta.  

A tabela a seguir enumera alguns cenários de exemplo para monitorizar e a métrica adequada a utilizar para o alerta:

| Cenário | Métrica para usar para alerta |
|-|-|
| Saúde do ponto final do servidor mostra um erro no portal | Resultado da sessão de sincronização |
| Os ficheiros não estão a sincronizar um servidor ou ponto final na nuvem | Ficheiros não sincronizados |
| O servidor registado não está a comunicar com o Serviço de Sincronização de Armazenamento | Estado on-line do servidor |
| O tamanho da recuperação do nível da nuvem ultrapassou os 500GiB num dia  | Tamanho de recuperação de nível de nuvem |

Para obter instruções sobre como criar alertas para estes cenários, consulte a secção [Exemplos de Alerta.](#alert-examples)

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para visualizar a saúde da sua implementação de Azure File Sync no **portal Azure,** navegue para o **Serviço de Sincronização de Armazenamento** e as seguintes informações estão disponíveis:

- Saúde do servidor registado
- Saúde do ponto final do servidor
    - Ficheiros não sincronizados
    - Atividade de sincronização
    - Eficiência do tiering da nuvem
    - Ficheiros não tiering
    - Erros de recordação
- Métricas

### <a name="registered-server-health"></a>Saúde do servidor registado

Para ver a **saúde** do servidor registado no portal, navegue para a secção de **servidores registados** do Serviço de **Sincronização de Armazenamento**.

![Screenshot da saúde dos servidores registados](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Se o estado do **servidor registado** estiver **Online,** o servidor comunica-se com sucesso com o serviço.
- Se o estado do **servidor registado** **for "Offline",** o processo de Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) não está em funcionamento ou o servidor não consegue aceder ao serviço Azure File Sync. Consulte a [documentação de resolução de problemas](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) para obter orientação.

### <a name="server-endpoint-health"></a>Saúde do ponto final do servidor

Para visualizar a saúde de um **ponto final** do servidor no portal, navegue para a secção **Sync groups** do Serviço de **Sincronização** de Armazenamento e selecione um **grupo de sincronização**.

![Screenshot da saúde do ponto final do servidor](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- A atividade **de saúde** e **sincronização** do servidor no portal baseia-se nos eventos de sincronização que são registados no registo de eventos de Telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como erro cancelado, o ponto final do servidor continuará a mostrar-se **tão saudável** no portal enquanto a sessão de sincronização atual estiver a progredir (os ficheiros são aplicados). O ID 9302 do evento é o evento de progresso sincronizado e o Event ID 9102 é registado assim que uma sessão de sincronização estiver concluída.  Para obter mais informações, consulte [a saúde sincronizada](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) e [sincronizar o progresso.](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session) Se a saúde do ponto final do servidor mostrar um **Erro** ou **Nenhuma Atividade,** consulte a [documentação de resolução de problemas](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) para obter orientação.
- A contagem **de ficheiros não sincronizados** no portal baseia-se no ID 9121 do evento que está registado no registo de eventos de Telemetria no servidor. Este evento é registado para cada erro por item uma vez que a sessão de sincronização esteja concluída. Para resolver erros por artigo, veja [como vejo se existem ficheiros ou pastas específicas que não estão a sincronizar?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)
- Para visualizar a **eficiência de tiering** da nuvem no portal, aceda às Propriedades do **Ponto Final** do Servidor e navegue para a secção **Cloud Tiering.** Os dados fornecidos para a eficiência do tiering em nuvem baseiam-se no ID 9071 do evento que é registado no registo de eventos de Telemetria no servidor. Para saber mais, consulte [cloud Tiering Overview](./storage-sync-cloud-tiering.md).
- Para ver **ficheiros que não estão a nivelar** e **a recordar erros** no portal, aceda às Propriedades do **Ponto Final** do Servidor e navegue para a secção **Cloud Tiering.** **O tiering de ficheiros não** é baseado no ID 9003 do evento que está registado no registo de eventos de Telemetria no servidor e os **erros de recuperação** são baseados no ID do evento 9006. Para investigar ficheiros que não estão a hierarquizador ou a recordar, consulte [Como resolver ficheiros que não estão em nível](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) e [como resolver ficheiros que não são recolhidos](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Gráficos métricos

- As seguintes tabelas métricas são visualizadas no portal do Serviço de Sincronização de Armazenamento:

  | Nome da métrica | Descrição | Nome da lâmina |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo sincronizado, ponto final do servidor |
  | Recuperação de camadas de nuvem | Tamanho dos dados recordados | Servidores registados |
  | Ficheiros não sincronizados | Contagem de ficheiros que não estão a sincronizar | Ponto final do servidor |
  | Ficheiros sincronizados | Contagem de ficheiros transferidos (upload e download) | Grupo sincronizado, ponto final do servidor |
  | Estado on-line do servidor | Contagem de batimentos cardíacos recebidos do servidor | Servidores registados |

- Para saber mais, consulte [o Azure Monitor.](#azure-monitor)

  > [!Note]  
  > As tabelas no portal Storage Sync Service têm um intervalo de tempo de 24 horas. Para visualizar diferentes intervalos de tempo ou dimensões, utilize o Azure Monitor.

## <a name="windows-server"></a>Windows Server

No **Servidor** do Windows que tem o agente Azure File Sync instalado, pode visualizar a saúde dos pontos finais do servidor nesse servidor utilizando os **registos** de eventos e **os contadores de desempenho**.

### <a name="event-logs"></a>Registos de eventos

Utilize o registo de eventos de Telemetria no servidor para monitorizar a saúde do servidor registado, sincronização e nivelamento da nuvem. O registo de eventos de Telemetria está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

Saúde sincronizada

- O ID 9102 do evento é registado assim que uma sessão de sincronização estiver concluída. Utilize este evento para determinar se as sessões de sincronização são bem sucedidas **(HResult = 0**) e se existem erros de sincronização por item **(PerItemErrorCount).** Para obter mais informações, consulte a documentação de erros de [saúde](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) e [erros por item.](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Por vezes, as sessões de sincronização falham globalmente ou têm um PerItemErrorCount não-zero. No entanto, eles ainda fazem progressos, e alguns ficheiros sincronizam com sucesso. Pode ver isto nos campos Aplicados como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Estes campos dizem-lhe quanto da sessão conseguiu. Se vir várias sessões de sincronização a falhar em linha e tiverem uma contagem de aplicação cada vez maior, dê tempo de sincronização para tentar novamente antes de abrir um bilhete de apoio.

- O ID 9121 do evento é registado para cada erro por item assim que a sessão de sincronização estiver concluída. Utilize este evento para determinar o número de ficheiros que não estão a sincronizar com este erro **(PersistenteCount** e **TransientCount).** Erros persistentes por item devem ser investigados, ver [como vejo se existem ficheiros ou pastas específicas que não estão a sincronizar?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

- O ID 9302 do evento é registado a cada 5 a 10 minutos se houver uma sessão de sincronização ativa. Utilize este evento para determinar quantos itens devem ser sincronizados **(TotalItemCount),** número de itens que sincronizaram até agora (**AppliedItemCount**) e número de itens que não sincronizaram devido a um erro por item **(PerItemErrorCount).** Se a sincronização não estiver a progredir **(AppliedItemCount=0),** a sessão de sincronização acabará por falhar e um ID 9102 do Evento será registado com o erro. Para obter mais informações, consulte a documentação de [progresso sincronizada.](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Saúde do servidor registado

- O ID 9301 do evento é registado a cada 30 segundos quando um servidor consulta o serviço para empregos. Se o GetNextJob terminar com **o estado = 0,** o servidor é capaz de comunicar com o serviço. Se o GetNextJob terminar com um erro, verifique a [documentação de resolução de problemas](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) para obter orientação.

Saúde de nivelamento de nuvens

- Para monitorizar a atividade de tiering num servidor, utilize o Evento ID 9003, 9016 e 9029 no registo de eventos de Telemetria, que está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

  - O ID 9003 do evento fornece distribuição de erros para um ponto final do servidor. Por exemplo: Contagem total de erros e Código de Erro. Um evento é registado por código de erro.
  - O ID 9016 do evento fornece resultados fantasmagóricos para um volume. Por exemplo: Por exemplo: Por cento de espaço livre é, Número de ficheiros fantasmagóricos em sessão, e Número de ficheiros não fantasma.
  - O ID 9029 do evento fornece informações de sessão fantasmagórica para um ponto final do servidor. Por exemplo: Número de ficheiros tentados na sessão, Número de ficheiros hierárquicos na sessão e Número de ficheiros já nivelados.
  
- Para monitorizar a atividade de recolha num servidor, utilize o Evento ID 9005, 9006, 9009, 9059 e 9071 no registo de eventos da Telemetria, que está localizado no Espectador de Eventos em *Aplicações e Serviços\Microsoft\FileSync\Agent*.

  - O ID 9005 do evento proporciona fiabilidade de recuperação para um ponto final do servidor. Por exemplo: Ficheiros únicos totais acedidos e ficheiros exclusivos total com acesso falhado.
  - O ID 9006 do evento fornece uma distribuição de erro de recuperação para um ponto final do servidor. Por exemplo: Total de Pedidos Falhados e Código de Erro. Um evento é registado por código de erro.
  - O ID 9009 do evento fornece informações de sessão de recuperação para um ponto final do servidor. Por exemplo: DuraçõesSegundos, CondeFilesRecallSucceeded e CountFilesRecallFailed.
  - O ID 9059 do evento fornece distribuição de recolha de aplicações para um ponto final do servidor. Por exemplo: ShareId, Nome da Aplicação e TotalEgressNetworkBytes.
  - O ID 9071 do evento proporciona eficiência de nivelamento em nuvem para um ponto final do servidor. Por exemplo: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes e TotalCacheMissBytes.

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

## <a name="alert-examples"></a>Exemplos de alerta
Esta secção fornece alguns alertas de exemplo para Azure File Sync.

  > [!Note]  
  > Se criar um alerta e for muito barulhento, ajuste o valor do limiar e alerte a lógica.
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Como criar um alerta se a saúde do ponto final do servidor mostrar um erro no portal

1. No **portal Azure,** navegue para o respetivo **Serviço de Sincronização de Armazenamento**. 
2. Vá à secção **de Monitorização** e clique em **Alertas**. 
3. Clique em **+ Nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando **em Selecionar a condição**.
5. Dentro da lâmina **lógica de sinal configurar,** clique no **resultado da sessão de Sincronização** com o nome do sinal.  
6. Selecione a seguinte configuração de dimensão: 
    - Nome de dimensão: **Nome do ponto final do servidor**  
    - Operador: **=** 
    - Valores de dimensão: **Todos os valores atuais e futuros**  
7. Navegue para **a Lógica de Alerta** e complete o seguinte: 
    - Limite definido para **Estática** 
    - Operador: **Menos de** 
    - Tipo de agregação: **Máximo**  
    - Valor limiar: **1** 
    - Avaliado com base em: Granularidade agregação = **24 horas** Frequência de avaliação = **A cada hora** 
    - Clique **em 'Fazer'.** 
8. Clique **em Selecionar grupo de ação** para adicionar um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Como criar um alerta se os ficheiros não estiverem a sincronizar um servidor ou ponto final na nuvem

1. No **portal Azure,** navegue para o respetivo **Serviço de Sincronização de Armazenamento**. 
2. Vá à secção **de Monitorização** e clique em **Alertas**. 
3. Clique em **+ Nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando **em Selecionar a condição**.
5. Dentro da lâmina lógica de **sinal configurar,** clique em **Ficheiros não sincronizando** com o nome do sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome de dimensão: **Nome do ponto final do servidor**  
     - Operador: **=** 
     - Valores de dimensão: **Todos os valores atuais e futuros**  
7. Navegue para **a Lógica de Alerta** e complete o seguinte: 
     - Limite definido para **Estática** 
     - Operador: **Maior do que** 
     - Tipo de agregação: **Média**  
     - Valor limiar: **100** 
     - Avaliado com base em: Granularidade agregação = **5 minutos** / Frequência de avaliação = **A cada 5 minutos** 
     - Clique **em 'Fazer'.** 
8. Clique **em Selecionar grupo de ação** para adicionar um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Como criar um alerta se um servidor registado não estiver a comunicar com o Serviço de Sincronização de Armazenamento

1. No **portal Azure,** navegue para o respetivo **Serviço de Sincronização de Armazenamento**. 
2. Vá à secção **de Monitorização** e clique em **Alertas**. 
3. Clique em **+ Nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando **em Selecionar a condição**.
5. Dentro da lâmina lógica de **sinal configurar,** clique no **estado on-line do Servidor** com o nome de sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome de dimensão: **Nome do servidor**  
     - Operador: **=** 
     - Valores de dimensão: **Todos os valores atuais e futuros**  
7. Navegue para **a Lógica de Alerta** e complete o seguinte: 
     - Limite definido para **Estática** 
     - Operador: **Menos de** 
     - Tipo de agregação: **Máximo**  
     - Valor limiar (em bytes): **1** 
     - Avaliado com base em: Granularidade agregação = **1 hora** / Frequência de avaliação = **A cada 30 minutos** 
        - Note que as métricas são enviadas para o Monitor Azure a cada 15 a 20 minutos. Não desacione a **frequência de avaliação** em menos de 30 minutos (gerará falsos alertas).
     - Clique **em 'Fazer'.** 
8. Clique **em Selecionar grupo de ação** para adicionar um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Como criar um alerta se o tamanho de recolha de nível de nuvem excedeu 500GiB num dia

1. No **portal Azure,** navegue para o respetivo **Serviço de Sincronização de Armazenamento**. 
2. Vá à secção **de Monitorização** e clique em **Alertas**. 
3. Clique em **+ Nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando **em Selecionar a condição**.
5. Dentro da lâmina **lógica de sinal configurar,** clique no **tamanho da recuperação do nível de nuvem** sob o nome de sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome de dimensão: **Nome do servidor**  
     - Operador: **=** 
     - Valores de dimensão: **Todos os valores atuais e futuros**  
7. Navegue para **a Lógica de Alerta** e complete o seguinte: 
     - Limite definido para **Estática** 
     - Operador: **Maior do que** 
     - Tipo de agregação: **Total**  
     - Valor limiar (in bytes): **67108864000** 
     - Avaliado com base em: Granularidade agregação = **24 horas** Frequência de avaliação = **A cada hora** 
    - Clique **em 'Fazer'.** 
8. Clique **em Selecionar grupo de ação** para adicionar um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

## <a name="next-steps"></a>Passos seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Considere as definições de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
- [Ficheiros Azure frequentemente fazem perguntas](storage-files-faq.md)
