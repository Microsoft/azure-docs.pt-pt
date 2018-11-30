---
title: Tempo de ingestão de dados no Azure Log Analytics | Documentos da Microsoft
description: Explica os diferentes fatores que afetam a latência na coleta de dados no Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: bwren
ms.openlocfilehash: 4f7b0f7c1cd08168db3f0f0ffd6cf6c4fa2c604e
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334555"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Tempo de ingestão de dados no Log Analytics
O Azure Log Analytics é um serviço de dados de grande escala no Azure Monitor, que serve a milhares de envio de cada mês de terabytes de dados a um ritmo cada vez maior de clientes. Muitas vezes, há perguntas sobre o tempo que demora para os dados fiquem disponíveis no Log Analytics depois de serem recolhido. Este artigo explica os diferentes fatores que afetam esta latência.

## <a name="typical-latency"></a>Latência típica
Latência refere-se para o tempo que dados são criados no sistema monitorizado e a hora em que se trata de disponível para análise no Log Analytics. A latência típica de ingerir dados no Log Analytics é entre 2 e 5 minutos. A latência específica para quaisquer dados específicos irá variar dependendo de vários fatores explicado abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo de ingestão total para um determinado conjunto de dados pode ser dividido nas seguintes áreas de alto nível. 

- Hora do agente - tempo para detetar um evento, reuni-los e, em seguida, enviar para o ponto de ingestão do Log Analytics como um registro de log. Na maioria dos casos, esse processo é manipulado por um agente.
- Hora de pipeline - o tempo para o pipeline de ingestão processar o registo de registo. Isto inclui as propriedades do evento de análise e potencialmente adicionar informações calculadas.
- Tempo de indexação – o tempo gasto para ingerir um registro de log no arquivo de grandes volumes de dados no Log Analytics.

Obter detalhes sobre a latência de diferente introduzida neste processo estão descritos abaixo.

### <a name="agent-collection-latency"></a>Latência de coleção de agente
Soluções de gestão e agentes use estratégias diferentes para recolher dados de uma máquina virtual, que pode afetar a latência. Seguem-se alguns exemplos específicos:

- Eventos do Windows, eventos do syslog e métricas de desempenho são recolhidas imediatamente. Contadores de desempenho do Linux são consultados a intervalos de 30 segundos.
- Registos do IIS e dos registos personalizados são recolhidos quando seus timestamp é alterado. Para os registos de IIS, isso depende de [agenda de rollover configurada no IIS](../azure-monitor/platform/data-sources-iis-logs.md). 
- Solução de replicação de diretório Active Directory realiza sua avaliação a cada cinco dias, enquanto a solução de avaliação do Active Directory realiza uma avaliação semanal da infra-estrutura do Active Directory. O agente irá recolher estes registos, apenas quando a avaliação estiver concluída.

### <a name="agent-upload-frequency"></a>Frequência de carregamento do agente
Para garantir que o agente do Log Analytics é simples, o agente coloca na memória intermédia registos e carrega periodicamente para o Log Analytics. Carregar frequência varia entre 30 segundos e dois minutos consoante o tipo de dados. A maioria dos dados são carregados em menos de 1 minuto. Condições de rede podem afetar negativamente a latência destes dados para alcançar o ponto de ingestão do Log Analytics.

### <a name="azure-logs-and-metrics"></a>Métricas e registos do Azure 
Dados de registo de atividade irão demorar cerca de 5 minutos fique disponível no Log Analytics. Dados de registos de diagnóstico e métricas podem demorar 1 a 5 minutos para se tornarem disponíveis, dependendo do serviço do Azure. Em seguida, irá demorar um adicionais 30 a 60 segundos para os registos e 3 minutos, para as métricas para os dados sejam enviados para o ponto de ingestão do Log Analytics.

### <a name="management-solutions-collection"></a>Coleção de soluções de gestão
Algumas soluções não recolher os dados de um agente e podem utilizar um método de coleção que cria latência adicional. Algumas soluções de recolhem dados em intervalos regulares, sem tentar coleção quase em tempo real. Exemplos específicos incluem o seguinte:

- Solução do Office 365 consulta os registos de atividades usando a API de atividade do gerenciamento do Office 365, a que atualmente não fornece que garantias de qualquer latência quase em tempo real.
- Dados de soluções (conformidade de atualização, por exemplo) de análise do Windows são recolhidos pela solução a uma frequência diária.

Consulte a documentação para cada solução determinar a frequência de recolha.

### <a name="pipeline-process-time"></a>Tempo do processo de pipeline
Depois de registros de log são ingeridos no pipeline do Log Analytics, foram escritas para armazenamento temporário para garantir o isolamento de inquilino e certifique-se de que os dados não são perdidos. Este processo adiciona normalmente 5 a 15 segundos. Algumas soluções de gestão implementam algoritmos mais pesados agregar dados e derivam informações como dados de transmissão em fluxo no. Por exemplo, a monitorização de desempenho de rede agrega os dados recebidos em intervalos de 3 minutos, com eficiência, adicionando latência de 3 minutos. Outro processo que adiciona latência é o processo que lida com registos personalizados. Em alguns casos, esse processo pode adicionar alguns minutos de latência para registos que são recolhidos a partir de arquivos pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Novos tipos de dados personalizados de aprovisionamento
Quando é criado um novo tipo de dados personalizados a partir um [log personalizado](../log-analytics/../azure-monitor/platform/data-sources-custom-logs.md) ou o [API de Recoletor de dados](../log-analytics/log-analytics-data-collector-api.md), o sistema cria um contentor de armazenamento dedicado. Esta é uma sobrecarga de uso individual que ocorre somente no primeiro aspeto deste tipo de dados.

### <a name="surge-protection"></a>Proteção de surto
A prioridade do Log Analytics é garantir que não existem dados de cliente são perdidos, para que o sistema tenha proteção incorporada para surtos de dados. Isto inclui buffers para garantir que até mesmo sob carga grande, o sistema irá manter a funcionar. Sob uma carga normal, esses controles adicionar menos de um minuto, mas em condições extremas e pode adicionar a um tempo significativo, garantindo dados de falhas é seguros.

### <a name="indexing-time"></a>Tempo de indexação
Existe um equilíbrio incorporado para cada plataforma de grandes volumes de dados entre o fornecimento de análise e capacidades de pesquisa avançada em vez de fornecer acesso imediato aos dados. O Azure Log Analytics permite-lhe executar consultas poderosas em milhares de milhões de registos e obter resultados dentro de alguns segundos. Isso se tornou possível uma vez que a infraestrutura transforma os dados significativamente durante a ingestão e armazena-os em estruturas compactas exclusivas. O sistema coloca na memória intermédia os dados até que o suficiente de está disponível para criar estas estruturas. Isso deve ser concluído antes do Registro de log é apresentada nos resultados da pesquisa.

Este processo atualmente demora cerca de 5 minutos quando existe um volume baixo de dados, mas menos tempo taxas de dados mais altas. Isso parece não intuitivo, mas esse processo permite que a otimização de latência para cargas de trabalho de produção de grande volume.



## <a name="checking-ingestion-time"></a>A verificar o tempo de ingestão
Pode utilizar o **Heartbeat** tabela para obter uma estimativa da latência de dados de agentes. Uma vez que o Heartbeat é enviado uma vez um minuto, a diferença entre a hora atual e o último registo de pulsação ideal é que será mais próximo de um minuto possível.

Utilize a seguinte consulta para listar os computadores com a latência mais alta.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Utilize a seguinte consulta em grandes ambientes resumem a latência de diferentes porcentagens de total de computadores.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>Passos Seguintes
* Leitura a [(SLA) de contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) para o Log Analytics.

