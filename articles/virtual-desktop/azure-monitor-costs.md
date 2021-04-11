---
title: Monitor Windows Virtual Desktop estimativas de preços de preços - Azure
description: Como estimar custos e preços para a utilização do Monitor Azure para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 5bd89a734a20c913bacca1f5531aa76d76418c80
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448224"
---
# <a name="estimate-azure-monitor-costs"></a>Estimativa custos do Monitor Azure

Azure Monitor Logs é um serviço que recolhe, indexa e armazena dados gerados pelo seu ambiente. Por isso, o modelo de preços do Azure Monitor baseia-se na quantidade de dados que são trazidos e processados (ou "ingeridos") pelo seu espaço de trabalho Log Analytics em gigabytes por dia. O custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados recolhidos, mas também no plano de pagamento do Azure que selecionou e quanto tempo escolhe armazenar os dados que o seu ambiente gera.

Este artigo explicará as seguintes coisas para ajudá-lo a entender como funciona o preço no Azure Monitor:

- Como estimar os custos de ingestão e armazenamento de dados antecipadamente antes de ativar esta funcionalidade
- Como medir e controlar a sua ingestão e armazenamento para reduzir custos ao utilizar esta funcionalidade

>[!NOTE]
> Todos os tamanhos e preços listados neste artigo são apenas exemplos para demonstrar como funciona a estimativa. Para uma avaliação mais precisa com base no seu modelo de preços Azure Monitor Log Analytics e na região de Azure, consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Estimativa de dados e custos de armazenamento

Recomendamos que utilize um conjunto de dados predefinidos escrito como registos no seu espaço de trabalho Log Analytics. Nas seguintes estimativas de exemplo, vamos olhar para os dados faturantes na configuração padrão

Os conjuntos de dados predefinidos para O Monitor Azure para o Ambiente de Trabalho Virtual do Windows incluem:

- Contadores de desempenho dos anfitriões da sessão
- Registos de eventos do Windows dos anfitriões da sessão
- Windows Virtual Desktop diagnósticos a partir da infraestrutura de serviço

Os seus custos de ingestão e armazenamento de dados dependem do tamanho, saúde e utilização do seu ambiente. O exemplo estima que usaremos neste artigo para calcular as gamas de custos que pode esperar são baseadas em máquinas virtuais saudáveis que passam a luz para o uso de energia, com base nas nossas [diretrizes de tamanho de máquina virtual,](/remote/remote-desktop-services/virtual-machine-recs)para calcular uma série de custos de ingestão de dados e armazenamento que você poderia esperar.

O VM de utilização de luz que vamos usar no nosso exemplo inclui os seguintes componentes:

- 4 vCPUs, 1 disco
- 16 sessões por dia
- Duração média da sessão de 2 horas (120 minutos)
- 100 processos por sessão

A utilização de energia VM que vamos usar no nosso exemplo inclui os seguintes componentes:

- 6 vCPUs, 1 disco
- 6 sessões por dia
- Duração média da sessão de 4 horas (240 minutos)
- 200 processos por sessão

## <a name="estimating-performance-counter-ingestion"></a>Estimativa da contra ingestão de desempenho

Os contadores de desempenho mostram como os recursos do sistema estão a funcionar. A ingestão de dados de contador de desempenho depende do tamanho e utilização do seu ambiente. Na maioria dos casos, os contadores de desempenho devem comuer 80 a 99% da ingestão de dados para o Monitor Azure para o Windows Virtual Desktop.

Antes de começar a estimar, é importante que compreenda que cada contador de desempenho envia dados numa frequência específica. Definimos uma taxa de amostra padrão por minuto (também pode editar esta taxa nas suas definições), mas essa taxa será aplicada em diferentes fatores multiplicadores dependendo do contador. Os seguintes fatores afetam a taxa:

- Para o fator por máquina virtual (VM), cada contador envia dados por VM no seu ambiente à taxa de amostra padrão por minuto enquanto o VM está em execução. Pode estimar o número de registos que estes contadores enviam por dia multiplicando a taxa de amostra padrão por minuto pelo número de VMs no seu ambiente, multiplicando esse número pelo tempo médio de funcionamento por dia.

   Para resumir:

   Taxa de amostra padrão por minuto × número de núcleos de CPU no VM SKU × número de VMs × tempo médio de funcionamento de VM por dia = número de registos enviados por dia

- Para o fator por CPU, cada contador envia à taxa de amostra padrão por minuto por vCPU em cada VM no seu ambiente enquanto o VM está em execução. Pode estimar o número de registos que os contadores enviarão por dia multiplicando a taxa de amostra padrão por minuto pelo número de núcleos de CPU no SKU VM, multiplicando-o pelo número de minutos que o VM executa e pelo número de VMs no seu ambiente.

   Para resumir:
   
   Taxa de amostra por minuto padrão × número de núcleos de CPU no VM SKU × número de minutos que o VM executa × número de VMs = número de registos enviados por dia

- Para o fator por disco, cada contador envia dados à taxa de amostra padrão para cada disco em cada VM no seu ambiente. O número de registos que estes contadores enviarão por dia equivale à taxa de amostra padrão por minuto multiplicada pelo número de discos no VM SKU, multiplicados por 60 minutos por hora e, finalmente, multiplicados pelas horas ativas médias para um VM.

   Para resumir:

   Taxa de amostra por minuto padrão × número de discos em VM SKU × 60 minutos por hora × número de VMs × tempo médio de funcionamento de VM por dia = número de registos enviados por dia

- Para o fator por sessão, cada contador envia dados à taxa de amostra padrão para cada sessão no seu ambiente enquanto a sessão está ligada. Pode estimar o número de registos que estes contadores enviarão por dia, multiplicando a taxa de amostra padrão por minuto pelo número médio de sessões por dia e pela duração média da sessão.

   Para resumir:

   Taxa de amostra padrão por minuto × sessões por dia × duração média da sessão = número de registos enviados por dia

- Para o fator por processo, cada contador envia dados à taxa padrão para cada processo em cada sessão no seu ambiente. Pode estimar o número de registos que estes contadores enviarão por dia multiplicando a taxa de amostra padrão por minuto pelo número médio de sessões por dia, multiplicando-a pela duração média da sessão e pelo número médio de processos por sessão.
  
   Para resumir:

   A taxa de amostra por minuto × sessões por dia × duração média da sessão × número médio de processos por sessão = número de registos enviados por dia

A tabela que se segue lista os 20 contadores de desempenho Azure Monitor para o Windows Virtual Desktop recolhe e as suas taxas de incumprimento:

| Nome do contador | Taxa de amostra padrão | Fator de frequência |
|--------------|---------------------|------------------|
| Disco Lógico(C:) \\ % de espaço livre | 60 segundos  | Por disco             |
| Disco lógico (C:) \\ Avg. Comprimento da fila do disco   | 30 segundos    | Por disco             |
| Disco lógico(C:) \\ Avg. Disco seg/Transferência  | 60 segundos       | Por disco             |
| Comprimento da fila do disco lógico (C:) \\ Comprimento da fila do disco  | 30 segundos      | Por disco             |
| Memória( \* ) \\ Mbytes disponíveis | 30 segundos    | Por VM  |
| Memória. \* ) Falhas de \\ página/seg | 30 segundos   | Por VM  |
| Memória. \* \\ ) Páginas/seg | 30 segundos   | Por VM  |
| Memory. \* \\ % Bytes comprometidos em uso | 30 segundos    | Por VM  |
| PhysicalDisk( \* ) \\ Avg. Comprimento da fila do disco | 30 segundos      | Por disco             |
| PhysicalDisk. \* \\ ) Avg. Disco sec/Read | 30 segundos  | Por disco             |
| PhysicalDisk( \* ) \\ Avg. Disco sec/Transfer | 30 segundos  | Por disco             |
| PhysicalDisk( \* ) \\ Avg. Disco sec/Write | 30 segundos | Por disco             |
| Informação do processador (_Total) \\ % tempo do processador | 30 segundos | Por núcleo/CPU         |
| Serviços de \* Terminais. ) \\ Sessões Ativas          | 60 segundos | Por VM  |
| Serviços de \* Terminais. ) \\ Sessões Inativas        | 60 segundos | Por VM  |
| Serviços \* terminais. \\ | 60 segundos | Por VM  |
| Atraso de entrada do utilizador por \* processo. \\         | 30 segundos | Por processo          |
| Atraso de entrada do utilizador por \* sessão. \\        | 30 segundos | Por sessão          |
| Rede \* RemoteFX. \\ | 30 segundos | Por VM  |
| Rede \* RemoteFX. \\     | 30 segundos | Por VM  |

Se estimarmos que cada tamanho recorde seja de 200 bytes, um exemplo de VM executando uma carga de trabalho leve na taxa de amostra padrão enviaria cerca de 90 megabytes de dados de contador de desempenho por dia por VM. Enquanto isso, um exemplo VM executando uma carga de trabalho de potência enviaria cerca de 130 megabytes de dados de contador de desempenho por dia por VM. No entanto, o tamanho do registo e o uso do ambiente podem variar, por isso os megabytes por dia que a sua utilização pode ser diferente.

Para saber mais sobre os contadores de desempenho de atraso de entrada, consulte os [contadores de desempenho do Atraso de Entrada do Utilizador](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Estimativa de ingestão de registo de eventos de Windows

Os Registos de Eventos do Windows são fontes de dados recolhidas por agentes do Log Analytics em máquinas virtuais do Windows. Pode recolher eventos a partir de registos padrão como Sistema e Aplicação, bem como registos personalizados criados por aplicações que precisa de monitorizar.

Estes são os eventos padrão do Windows para monitor azure para o Ambiente de Trabalho Virtual do Windows:

- Aplicação
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operacional
- Sistema
- Microsoft-FSLogix-Apps/Operacional
- Microsoft-FSLogix-Apps/Admin

Os Eventos Windows enviam sempre que os termos do evento são cumpridos no ambiente. Máquinas em estados saudáveis enviarão menos eventos do que máquinas em estados pouco saudáveis. Uma vez que a contagem de eventos é imprevisível, usamos um intervalo de 1.000 a 10.000 eventos por vM por dia com base em exemplos de ambientes saudáveis para esta estimativa. Por exemplo, se estimarmos que cada tamanho recorde de evento neste exemplo seja de 1.500 bytes, este sai para cerca de 2 a 15 megabytes de dados de eventos por dia para o ambiente especificado.

Para saber mais sobre eventos windows, consulte [as propriedades dos registos de eventos do Windows.](../azure-monitor/agents/data-sources-windows-events.md)

## <a name="estimating-diagnostics-ingestion"></a>Estimativa da ingestão de diagnósticos

O serviço de diagnóstico cria registos de atividade tanto para as ações do utilizador como para as ações administrativas.

Estes são os nomes dos registos de atividades dos contadores de diagnóstico:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

O serviço envia informações de diagnóstico sempre que o ambiente cumpre os termos necessários para escoar um registo. Uma vez que a contagem de registos de diagnóstico é imprevisível, usamos uma gama de 500 a 1000 eventos por VM por dia com base em exemplos de ambientes saudáveis para esta estimativa.

Por exemplo, se estimarmos que cada tamanho de registo de diagnóstico neste exemplo seja de 200 bytes, então o total de dados ingeridos seria inferior a 1 MB por VM por dia.

Para saber mais sobre as categorias de registo de atividades, consulte os [diagnósticos de desktop virtual do Windows.](diagnostics-log-analytics.md)

## <a name="estimating-total-costs"></a>Estimativa dos custos totais

Finalmente, vamos estimar o custo total. Neste exemplo, digamos que temos os seguintes resultados baseados nos valores de exemplo nas secções anteriores:

| Origem de dados        | Estimativa de tamanho por dia (em megabytes)   |
|-------------------------------------|------------------------------------------|
| Contadores de desempenho   | 90-130 |
| Eventos    | 2-15 |
| Diagnósticos de desktop virtual do Windows | \< 1 |

Neste exemplo, o total de dados ingeridos para O Monitor Azure para o Windows Virtual Desktop é entre 92 e 145 megabytes por VM por dia. Por outras palavras, a cada 31 dias, cada VM ingere cerca de 3 a 5 gigabytes de dados.

Utilizando o modelo pay-as-you-go padrão para [preços de Log Analytics,](https://azure.microsoft.com/pricing/details/monitor/)pode estimar o custo de recolha e armazenamento de dados do Azure Monitor por mês. Dependendo da ingestão de dados, poderá também considerar o modelo de Reserva de Capacidade para preços de Log Analytics.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Gerencie a ingestão de dados para reduzir custos

Esta secção explicará como medir e gerir a ingestão de dados para reduzir custos.

Para saber sobre a gestão de direitos e permissões no livro, consulte o [controlo access](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>A remoção de pontos de dados terá impacto nos seus visuais correspondentes no Monitor Azure para o Windows Virtual Desktop.

### <a name="log-analytics-settings"></a>Registar definições de Analítico

Aqui ficam algumas sugestões para otimizar as definições do Log Analytics para gerir a ingestão de dados:

- Utilize um espaço de trabalho designado para o Log Analytics para os seus recursos de Desktop Virtual do Windows para garantir que o Log Analytics apenas recolhe contadores de desempenho e eventos para as máquinas virtuais na sua implementação virtual do Windows Desktop.
- Ajuste as definições de armazenamento do Log Analytics para gerir os custos. Pode reduzir o período de retenção, avaliar se um nível de preços de armazenamento fixo seria mais rentável ou definir limites sobre quantos dados pode ingerir para limitar o impacto de uma implementação pouco saudável. Para saber mais, consulte [Gerir a utilização e os custos para os Registos do Monitor Azure.](../azure-monitor/platform/manage-cost-storage.md)

### <a name="remove-excess-data"></a>Remover dados em excesso

A nossa configuração padrão é o único conjunto de dados que recomendamos para o Monitor Azure para o Windows Virtual Desktop. Tem sempre a opção de adicionar pontos de dados adicionais e vê-los no Host Diagnostics: Host browser ou construir gráficos personalizados para eles, no entanto os dados adicionados aumentarão o custo do Log Analytics. Estes podem ser removidos para poupança de custos.

### <a name="measure-and-manage-your-performance-counter-data"></a>Meça e gere os seus dados de contador de desempenho

Os seus verdadeiros custos de monitorização dependerão do tamanho, uso e saúde do seu ambiente. Para entender como medir a ingestão de dados no seu espaço de trabalho Log Analytics, consulte [o volume de dados de registo ingerido](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume).

O desempenho contra-ataques que os anfitriões da sessão usam será provavelmente a sua maior fonte de dados ingeridos para o Azure Monitor para o Windows Virtual Desktop. O modelo de consulta personalizado para um espaço de trabalho Log Analytics pode rastrear a frequência e megabytes ingeridos por contador de desempenho durante o último dia:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Certifique-se de que substitui os valores de espaço reservado do modelo pelos valores que o seu ambiente utiliza, caso contrário a consulta não funcionará.

Esta consulta mostrará todos os contadores de desempenho que ativou no ambiente, e não apenas os padrão para O Monitor Azure para o Windows Virtual Desktop. Esta informação pode ajudá-lo a entender quais as áreas a direcionar para reduzir custos, como reduzir a frequência de um contador ou removê-lo completamente.

Também pode reduzir custos removendo contadores de desempenho. Para aprender a remover contadores de desempenho ou editar contadores existentes para reduzir a sua frequência, consulte [balcões de desempenho configurados](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Gerir registos de eventos do Windows

É pouco provável que os Eventos do Windows causem um pico na ingestão de dados quando todos os anfitriões são saudáveis. Um hospedeiro pouco saudável pode aumentar o número de eventos enviados para o registo, mas a informação pode ser fundamental para corrigir os problemas do anfitrião. Recomendamos mantê-los. Para saber mais sobre como gerir os Registos de Eventos do Windows, consulte [os registos de Eventos do Windows configurados.](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

### <a name="manage-diagnostics"></a>Gerir diagnósticos

Os diagnósticos virtuais do Windows Desktop devem comutilar menos de 1% dos custos de armazenamento de dados, pelo que não recomendamos a remoção dos mesmos. Para gerir os diagnósticos de ambiente de trabalho virtual do Windows, [utilize o Log Analytics para a funcionalidade de diagnóstico](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Monitor para Windows Virtual Desktop nestes artigos:

- [Utilize o Monitor Azure para o Windows Virtual Desktop para monitorizar a sua implementação](azure-monitor.md).
- Use o [glossário](azure-monitor-glossary.md) para aprender mais sobre termos e conceitos.
- Se encontrar algum problema, consulte o nosso [guia de resolução de problemas](troubleshoot-azure-monitor.md) para pedir ajuda.
- Consulte [a monitorização do uso e os custos estimados no Azure Monitor](../azure-monitor/usage-estimated-costs.md) para saber mais sobre a gestão dos seus custos de monitorização.
