---
title: Documentação de orientação para os dados pessoais armazenados no Azure Log Analytics | Documentos da Microsoft
description: Este artigo descreve como gerir dados pessoais armazenados no Azure Log Analytics e os métodos para identificar e removê-lo.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 9112d50384aba288038343ff9a14ed55542fb722
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121354"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Documentação de orientação para os dados pessoais armazenados no Log Analytics e Application Insights

O log Analytics é um arquivo de dados em que são provavelmente encontrará os dados pessoais. O Application Insights armazena os respetivos dados numa partição do Log Analytics. Este artigo discutirá onde no Log Analytics e Application Insights esses dados geralmente se encontra, bem como os recursos disponíveis para lidar com esses dados.

> [!NOTE]
> Para os fins deste artigo _registos de dados_ refere-se a dados enviados para uma área de trabalho do Log Analytics, enquanto _os dados da aplicação_ refere-se aos dados recolhidos pelo Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Estratégia para a manipulação de dados pessoais

Enquanto é até e sua empresa, para determinar a estratégia com a qual irá lidar com a privada dados (se houver), seguem-se algumas abordagens possíveis. Eles estão listados por ordem de preferência de um ponto de vista técnico da maioria menos preferível para:

* Sempre que possível, parar a recolha de, oculte, tornar anónima ou ajustar de outra forma os dados a ser recolhidos para excluí-lo de que está a ser considerado como "private". Isto é _de longe_ a abordagem preferencial, eliminando a necessidade de criar uma estratégia de processamento de dados muito dispendiosos e com impacto.
* Sempre que não é possível, tente normalizar os dados para reduzir o impacto sobre a plataforma de dados e o desempenho. Por exemplo, em vez de iniciar sessão um ID de utilizador explícitas, crie dados de pesquisa que serão correlacionar o nome de utilizador e os respetivos detalhes para uma ID interna que, em seguida, podem ser registadas em outro lugar. Dessa forma, deve um dos seus utilizadores lhe-á pedido para eliminar as suas informações pessoais, é possível que apenas a eliminação da linha na tabela de referência correspondente para o usuário será suficiente. 
* Por fim, se devem ser recolhidos dados privados, crie um processo em todo o caminho de API de remoção e o caminho de API de consulta existente para satisfazer quaisquer obrigações que tiver em torno de exportar e eliminar quaisquer dados privados associados a um utilizador. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Onde procurar dados privados no Log Analytics?

O log Analytics é um armazenamento flexível, que, ao mesmo tempo que prescreve um esquema aos seus dados, permite-lhe substituir todos os campos com valores personalizados. Além disso, pode ser ingerida qualquer esquema personalizada. Como tal, é impossível dizer exatamente onde os dados privados serão encontrados na sua área de trabalho específica. As seguintes localizações, no entanto, são bons pontos de partida em seu inventário:

### <a name="log-data"></a>Dados de registo

* *Endereços IP*: O log Analytics recolhe uma grande variedade de informações de IP em muitas tabelas diferentes. Por exemplo, a seguinte consulta mostra todas as tabelas em que os endereços IPv4 tenham sido recolhidos nas últimas 24 horas:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *IDs de utilizador*: IDs de utilizador encontram-se numa grande variedade de soluções e tabelas. Pode procurar por um nome de utilizador específico em seu conjunto de dados completo com o comando de pesquisa:
    ```
    search "[username goes here]"
    ```
  Lembre-se de ter um aspeto não apenas para os nomes de utilizador legível por humanos, mas também GUIDs que diretamente podem ser rastreados volta a um utilizador específico!
* *Identificações de dispositivo*: Como IDs de usuário, identificações de dispositivo, por vezes, são consideradas "private". Utilize a mesma abordagem, conforme listado acima para IDs de usuário, para identificar tabelas onde isso pode ser uma preocupação. 
* *Dados personalizados*: O log Analytics permite que a coleção numa variedade de métodos: registos personalizados e campos personalizados, o [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) , e dados personalizados recolhido como parte dos logs de eventos do sistema. Todos esses são suscetíveis a que contém dados privados e devem ser examinados para verificar a existem de dados.
* *Dados capturados solução*: Uma vez que o mecanismo de solução é aberta, recomendamos que reveja todas as tabelas geradas pelas soluções para garantir a conformidade.

### <a name="application-data"></a>Dados da aplicação

* *Endereços IP*: Application Insights irá, por predefinição, oculte todos os campos de endereço IP como "0.0.0.0", mas é um padrão bastante comum para substituir este valor com o IP de utilizador reais para manter informações de sessão. A consulta do Analytics abaixo pode ser usada para encontrar qualquer tabela que contém os valores na coluna do endereço IP diferente de "0.0.0.0" nas últimas 24 horas:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *IDs de utilizador*: Por predefinição, o Application Insights irá utilizar IDs gerados aleatoriamente para utilizador e o controlo de sessão. No entanto, é comum ver estes campos substituídos para armazenar um ID mais relevante para a aplicação. Por exemplo: nomes de utilizador, GUIDs do AAD, etc. Estes IDs de muitas vezes, são consideradas como dentro do âmbito como os dados pessoais e, portanto, devem ser manipuladas adequadamente. A nossa recomendação é sempre tentar ofuscar ou tornar anónima a estas identificações. Os campos onde estes valores estão normalmente presentes incluem session_Id, user_Id, user_AuthenticatedId, user_AccountId, bem como a customDimensions.
* *Dados personalizados*: O Application Insights permite-lhe acrescentar um conjunto de dimensões personalizadas para qualquer tipo de dados. Essas dimensões podem ser *qualquer* dados. Utilize a seguinte consulta para identificar todas as dimensões personalizadas recolhidas ao longo das últimas 24 horas:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Os dados na memória e em trânsito*: O Application Insights irá controlar exceções, pedidos, chamadas de dependência e rastreios. Muitas vezes, podem ser recolhidos dados privados no código e no nível de chamada HTTP. Reveja as exceções, pedidos, dependências e rastreios tabelas para identificar os dados. Uso [inicializadores de telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) sempre que possível de ofuscar estes dados.
* *Capturas de depurador de instantâneos*: O [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) recurso do Application Insights permite-lhe recolher instantâneos de depuração, sempre que uma exceção é capturada na instância de produção do seu aplicativo. Instantâneos irão expor o rastreio de pilha completa que leva às exceções, bem como os valores para variáveis locais em cada etapa na pilha. Infelizmente, esta funcionalidade não permite a eliminação seletiva de pontos de ajuste ou acesso programático aos dados dentro do instantâneo. Portanto, se a taxa de retenção de instantâneos padrão não satisfaz os requisitos de conformidade, a recomendação é desativar a funcionalidade.

## <a name="how-to-export-and-delete-private-data"></a>Como exportar e eliminar dados privados

Conforme mencionado na [estratégia para a manipulação de dados pessoais](#strategy-for-personal-data-handling) secção anteriormente, é __vivamente__ recomendado se todos os possível reestruturar sua política de recolha de dados para desativar a coleção de dados privados, ofuscar ou anonymizing-lo ou, caso contrário, modifique-o para removê-lo de que está a ser considerado como "private". O dados mais avançada resultará em custos para e sua equipe para definir e automatizar uma estratégia, criar uma interface para os seus clientes interagir com os dados, através de custos de manutenção contínua e o tratamento. Além disso, é computacionalmente dispendiosa para o Log Analytics e Application Insights e um grande volume de consultas em simultâneo ou chamadas de API de remoção têm o potencial de afetar negativamente a todas as outras interações com a funcionalidade do Log Analytics. Dito isso, há, de fato, alguns cenários válidos onde devem ser recolhidos dados privados. Nesses casos, os dados devem ser manipulados conforme descrito nesta secção.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Ver e exportar

Para ambos ver e exportar pedidos de dados, o [API de consulta do Log Analytics](https://dev.loganalytics.io/) ou o [API de consulta do Application Insights](https://dev.applicationinsights.io/quickstart) deve ser utilizado. Lógica para converter a forma dos dados a um adequado para fornecer aos seus utilizadores será deve implementar. [As funções do Azure](https://azure.microsoft.com/services/functions/) faz um excelente local para alojar essa lógica.

### <a name="delete"></a>Eliminar

> [!WARNING]
> Exclusões do Log Analytics são destrutivas e não reversível! Tenha muito cuidado em sua execução.

Tornámos disponíveis como parte de um tratamento de privacidade de uma *remover* caminho de API. Este caminho deve ser utilizado com moderação, devido ao risco associado ao fazer isso, o possível impacto de desempenho e o potencial de inclinar completo agregações, medições e outros aspectos dos seus dados do Log Analytics. Consulte a [estratégia para a manipulação de dados pessoais](#strategy-for-personal-data-handling) na secção abordagens alternativas manipular dados privados.

Remoção é uma operação com privilégios bastante elevados que nenhuma aplicação ou o utilizador no Azure (incluindo até mesmo o proprietário do recurso) terão permissão para executar sem ser explicitamente concedido uma função no Azure Resource Manager. Esta função é _Purger dados_ e deve ser delegada com cuidado devido a potencial perda de dados. 

Assim que tiver sido atribuída a função do Azure Resource Manager, dois novos caminhos de API estão disponíveis: 

#### <a name="log-data"></a>Dados de registo

* [Após remover](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – usa um objeto especificar parâmetros de dados para eliminar e retorna uma referência GUID 
* Estado de remoção de GET - a chamada de remoção POST irá devolver um cabeçalho "x-ms-Estado-location", que irá incluir um URL que pode chamar para determinar o estado da sua API de remoção. Por exemplo:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Enquanto esperamos a grande maioria das operações de remoção para concluir a muito mais rápida do que o nosso SLA, devido a seu impacto pesado na plataforma de dados usado pelo Log Analytics, **o formal SLA para a conclusão de operações de remoção está definido em 30 dias**. 

#### <a name="application-data"></a>Dados da aplicação

* [Após remover](https://docs.microsoft.com/rest/api/application-insights/components/purge) – usa um objeto especificar parâmetros de dados para eliminar e retorna uma referência GUID
* Estado de remoção de GET - a chamada de remoção POST irá devolver um cabeçalho "x-ms-Estado-location", que irá incluir um URL que pode chamar para determinar o estado da sua API de remoção. Por exemplo:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Embora a grande maioria das operações de remoção pode ser muito mais rápida do que o SLA, devido a seu impacto pesado na plataforma de dados utilizado pelo Application Insights, concluída **o formal SLA para a conclusão de operações de remoção está definido em 30 dias**.

## <a name="next-steps"></a>Passos Seguintes
- Para saber mais sobre como os dados do Log Analytics são recolhidos, processados e protegidos, veja [segurança de dados do Log Analytics](../../azure-monitor/platform/data-security.md).
- Para saber mais sobre como os dados do Application Insights são recolhidos, processados e protegidos, veja [segurança de dados do Application Insights](../../azure-monitor/app/data-retention-privacy.md).
