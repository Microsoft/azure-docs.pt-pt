---
title: Diretrizes para dados pessoais armazenados no Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como gerenciar dados pessoais armazenados no Azure Log Analytics e os métodos para identificá-los e removê-los.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 05/18/2018
ms.openlocfilehash: 7733b27bb5af01e55cd732c16f6c9cb1e9301819
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932133"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Diretrizes para dados pessoais armazenados em Log Analytics e Application Insights

Log Analytics é um armazenamento de dados onde os dados pessoais provavelmente serão encontrados. Application Insights armazena seus dados em uma partição de Log Analytics. Este artigo discutirá onde em Log Analytics e Application Insights esses dados normalmente são encontrados, bem como os recursos disponíveis para você lidar com esses dados.

> [!NOTE]
> Para os fins deste artigo, os _dados de log_ referem-se aos dados enviados para um espaço de trabalho log Analytics, enquanto _os dados do aplicativo_ referem-se aos dados coletados pelo Application insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Estratégia para a manipulação de dados pessoais

Embora seja por você e sua empresa, por fim, determine a estratégia com a qual você tratará seus dados privados (se não houver), veja a seguir algumas abordagens possíveis. Eles são listados em ordem de preferência de um ponto de vista técnico do mais para o menos preferível:

* Sempre que possível, interrompa a coleta, ofusca, anonime ou ajuste os dados que estão sendo coletados para excluí-lo de ser considerado "privado". Essa é _, de longe_ , a abordagem preferida, poupando a necessidade de criar uma estratégia de manipulação de dados muito dispendiosa e impactante.
* Quando não for possível, tente normalizar os dados para reduzir o impacto na plataforma de dados e no desempenho. Por exemplo, em vez de registrar em log uma ID de usuário explícita, crie um dados de pesquisa que correlacionarão o username e seus detalhes a uma ID interna que pode então ser registrada em outro lugar. Dessa forma, caso um dos seus usuários solicite que você exclua suas informações pessoais, é possível que somente a exclusão da linha na tabela de pesquisa correspondente ao usuário seja suficiente. 
* Por fim, se os dados privados precisarem ser coletados, crie um processo em volta do caminho da API de limpeza e do caminho da API de consulta existente para atender a qualquer obrigação que você possa ter ao exportar e excluir todos os dados privados associados a um usuário. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Onde procurar dados privados em Log Analytics?

Log Analytics é um armazenamento flexível, que, ao assinar um esquema para seus dados, permite que você substitua cada campo por valores personalizados. Além disso, qualquer esquema personalizado pode ser ingerido. Assim, é impossível dizer exatamente onde os dados privados serão encontrados em seu espaço de trabalho específico. No entanto, os locais a seguir são bons pontos de partida no seu inventário:

### <a name="log-data"></a>Dados de registo

* *Endereços IP*: o log Analytics coleta uma variedade de informações de IP em várias tabelas diferentes. Por exemplo, a consulta a seguir mostra todas as tabelas em que os endereços IPv4 foram coletados nas últimas 24 horas:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *IDs de usuário*: as IDs de usuário são encontradas em uma grande variedade de soluções e tabelas. Você pode procurar um nome de usuário específico em todo o seu conjunto de dado usando o comando de pesquisa:
    ```
    search "[username goes here]"
    ```
  Lembre-se de procurar não apenas os nomes de usuário legíveis, mas também os GUIDs que podem ser rastreados diretamente de volta para um usuário específico!
* *IDs de dispositivo*: como IDs de usuário, as IDs de dispositivo são, às vezes, consideradas "privadas". Use a mesma abordagem listada acima para IDs de usuário para identificar tabelas em que isso pode ser uma preocupação. 
* *Dados personalizados*: log Analytics permite a coleção em uma variedade de métodos: logs personalizados e campos personalizados, a [API do coletor de dados http](../../azure-monitor/platform/data-collector-api.md) e os dados personalizados coletados como parte dos logs de eventos do sistema. Todos eles são suscetíveis a conter dados privados e devem ser examinados para verificar se esses dados existem.
* *Dados capturados pela solução*: como o mecanismo de solução é um aberto, é recomendável revisar todas as tabelas geradas por soluções para garantir a conformidade.

### <a name="application-data"></a>Dados da aplicação

* *Endereços IP*: embora Application insights, por padrão, ofusca todos os campos de endereço IP para "0.0.0.0", é um padrão bastante comum para substituir esse valor pelo IP do usuário real para manter as informações da sessão. A consulta de análise abaixo pode ser usada para localizar qualquer tabela que contenha valores na coluna de endereço IP diferente de "0.0.0.0" nas últimas 24 horas:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *IDs de usuário*: por padrão, Application insights usarão IDs geradas aleatoriamente para acompanhamento de sessão e de usuário. No entanto, é comum ver esses campos substituídos para armazenar uma ID mais relevante para o aplicativo. Por exemplo: nomes de acessações, GUIDs do AAD, etc. Essas IDs costumam ser consideradas no escopo como dados pessoais e, portanto, devem ser manipuladas adequadamente. Nossa recomendação é sempre tentar ofuscar ou anônimo essas IDs. Os campos em que esses valores geralmente são encontrados incluem session_Id, user_Id, user_AuthenticatedId, user_AccountId, bem como customDimensions.
* *Dados personalizados*: Application insights permite que você acrescente um conjunto de dimensões personalizadas a qualquer tipo de dados. Essas dimensões podem ser *quaisquer* dados. Use a consulta a seguir para identificar as dimensões personalizadas coletadas nas últimas 24 horas:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dados na memória e em trânsito*: Application insights acompanharão exceções, solicitações, chamadas de dependência e rastreamentos. Os dados privados geralmente podem ser coletados no código e no nível de chamada HTTP. Revise as tabelas exceções, solicitações, dependências e rastreamentos para identificar esses dados. Use [inicializadores de telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) onde for possível ofuscar esses dados.
* *Capturas de depurador de instantâneos*: o recurso de [depurador de instantâneos](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) no Application insights permite que você colete instantâneos de depuração sempre que uma exceção é capturada na instância de produção do seu aplicativo. Os instantâneos vão expor o rastreamento de pilha completo que leva às exceções, bem como os valores para variáveis locais em cada etapa da pilha. Infelizmente, esse recurso não permite a exclusão seletiva de pontos de ajuste ou o acesso programático a dados no instantâneo. Portanto, se a taxa de retenção de instantâneo padrão não atender aos seus requisitos de conformidade, a recomendação será desativar o recurso.

## <a name="how-to-export-and-delete-private-data"></a>Como exportar e excluir dados privados

Conforme mencionado na seção [estratégia para manipulação de dados pessoais](#strategy-for-personal-data-handling) anteriormente, é __altamente__ recomendável se tudo isso for possível, reestruturar sua política de coleta de dados para desabilitar a coleta de dados privados, ofuscar ou anonimá-lo, ou caso contrário, modifique-o para removê-lo de ser considerado "privado". Lidar com os dados resultará em custos para você e sua equipe para definir e automatizar uma estratégia, criar uma interface para seus clientes para interagir com seus dados e os custos de manutenção em andamento. Além disso, é computacionalmente dispendioso para Log Analytics e Application Insights, e um grande volume de consulta simultânea ou chamadas à API de limpeza têm o potencial de afetar negativamente todas as outras interações com a funcionalidade Log Analytics. Dito isso, há, na verdade, alguns cenários válidos em que os dados privados devem ser coletados. Nesses casos, os dados devem ser tratados conforme descrito nesta seção.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Exibir e exportar

Para exibir e exportar solicitações de dados, a [API de consulta log Analytics](https://dev.loganalytics.io/) ou a [API de consulta de Application insights](https://dev.applicationinsights.io/quickstart) deve ser usada. A lógica para converter a forma dos dados em um apropriado para entrega aos seus usuários dependerá da implementação. [Azure Functions](https://azure.microsoft.com/services/functions/) faz um ótimo lugar para hospedar essa lógica.

> [!IMPORTANT]
>  Embora a grande maioria das operações de limpeza possa ser concluída muito mais rapidamente do que o SLA, **o SLA formal para a conclusão de operações de limpeza é definido como 30 dias** devido ao seu impacto pesado na plataforma de dados usada. Este é um processo automatizado; Não é possível solicitar que uma operação seja manipulada mais rapidamente.

### <a name="delete"></a>Eliminar

> [!WARNING]
> As exclusões em Log Analytics são destrutivas e não reversíveis! Tenha muito cuidado em sua execução.

Disponibilizamos como parte de uma privacidade que manipula um caminho de API de *limpeza* . Esse caminho deve ser usado com moderação devido ao risco associado ao fazer isso, o impacto potencial no desempenho e o potencial de distorcer as agregações, as medições e outros aspectos de seus dados Log Analytics. Consulte a seção [estratégia para manipulação de dados pessoais](#strategy-for-personal-data-handling) para obter abordagens alternativas para lidar com dados privados.

Limpar é uma operação altamente privilegiada que nenhum aplicativo ou usuário no Azure (incluindo até mesmo o proprietário do recurso) terá permissões para executar sem explicitamente receber uma função no Azure Resource Manager. Essa função é o _limpador de dados_ e deve ser cuidadosamente delegada devido ao potencial de perda de dados. 

> [!IMPORTANT]
> Para gerenciar recursos do sistema, as solicitações de limpeza são limitadas a 50 solicitações por hora. Você deve enviar em lote a execução de solicitações de limpeza enviando um único comando cujo predicado inclui todas as identidades de usuário que exigem limpeza. Use o [operador in](/azure/kusto/query/inoperator) para especificar várias identidades. Você deve executar a consulta antes de executar a solicitação de limpeza para verificar se os resultados são esperados. 



Depois que a função Azure Resource Manager tiver sido atribuída, dois novos caminhos de API estarão disponíveis: 

#### <a name="log-data"></a>Dados de registo

* [Post Purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – usa um objeto especificando parâmetros de dados a serem excluídos e retorna um GUID de referência 
* OBTER status de limpeza – a chamada após a limpeza retornará um cabeçalho ' x-MS-status-location ' que incluirá uma URL que você pode chamar para determinar o status da sua API de limpeza. Por exemplo:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Embora esperamos que a grande maioria das operações de limpeza seja concluída muito mais rapidamente do que o nosso SLA, devido ao impacto pesado na plataforma de dados usada pelo Log Analytics, **o SLA formal para a conclusão de operações de limpeza é definido como 30 dias**. 

#### <a name="application-data"></a>Dados da aplicação

* [Post Purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) – usa um objeto especificando parâmetros de dados a serem excluídos e retorna um GUID de referência
* OBTER status de limpeza – a chamada após a limpeza retornará um cabeçalho ' x-MS-status-location ' que incluirá uma URL que você pode chamar para determinar o status da sua API de limpeza. Por exemplo:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Embora a grande maioria das operações de limpeza possa ser concluída muito mais rapidamente do que o SLA, devido ao impacto pesado sobre a plataforma de dados usada pelo Application Insights, **o SLA formal para a conclusão de operações de limpeza é definido como 30 dias**.

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre como Log Analytics dados são coletados, processados e protegidos, consulte [log Analytics segurança de dados](../../azure-monitor/platform/data-security.md).
- Para saber mais sobre como Application Insights dados são coletados, processados e protegidos, consulte [Application insights segurança de dados](../../azure-monitor/app/data-retention-privacy.md).
