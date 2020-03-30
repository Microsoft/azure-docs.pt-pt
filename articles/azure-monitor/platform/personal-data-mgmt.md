---
title: Orientação para dados pessoais armazenados no Azure Log Analytics. Microsoft Docs
description: Este artigo descreve como gerir os dados pessoais armazenados no Azure Log Analytics e os métodos para identificá-lo e removê-lo.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: a720627e1783d2e29ef180b7855132ea59444cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248752"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Orientação para dados pessoais armazenados em Log Analytics e Insights de Aplicação

O Log Analytics é uma loja de dados onde é provável que sejam encontrados dados pessoais. Application Insights armazena os seus dados numa partição de Log Analytics. Este artigo irá discutir onde em Log Analytics e Application Insights esses dados são normalmente encontrados, bem como as capacidades disponíveis para você para lidar com esses dados.

> [!NOTE]
> Para efeitos deste artigo, os dados relativos ao _registo_ referem-se a dados enviados para um espaço de trabalho do Log Analytics, enquanto os dados da _aplicação_ referem-se aos dados recolhidos pela Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Estratégia para o tratamento de dados pessoais

Embora caberá a si e à sua empresa determinar a estratégia com que irá lidar com os seus dados privados (se é que em tudo), as seguintes abordagens são possíveis. Estão enumerados por ordem de preferência do ponto de vista técnico da maioria para o mínimo preferível:

* Sempre que possível, pare a recolha de, obfuscate, anonimizar, ou de outra forma ajustar os dados que estão a ser recolhidos para os excluir de serem considerados "privados". Esta é de _longe_ a abordagem preferida, poupando-lhe a necessidade de criar uma estratégia de tratamento de dados muito dispendiosa e impactante.
* Sempre que não possível, tente normalizar os dados para reduzir o impacto na plataforma de dados e no desempenho. Por exemplo, em vez de registar um ID explícito do utilizador, criar á suplicoso que correlacionará o nome de utilizador e os seus dados a um ID interno que pode ser registado noutro local. Desta forma, caso um dos seus utilizadores lhe peça para apagar as suas informações pessoais, é possível que apenas eliminar a linha na tabela de pesquisa correspondente ao utilizador seja suficiente. 
* Finalmente, se os dados privados tiverem de ser recolhidos, construa um processo em torno do caminho da API de purga e do caminho api de consulta existente para cumprir quaisquer obrigações que possa ter em torno da exportação e eliminação de quaisquer dados privados associados a um utilizador. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Onde procurar dados privados no Log Analytics?

O Log Analytics é uma loja flexível, que ao prescrever um esquema aos seus dados, permite-lhe anular todos os campos com valores personalizados. Além disso, qualquer esquema personalizado pode ser ingerido. Como tal, é impossível dizer exatamente onde os dados privados serão encontrados no seu espaço de trabalho específico. As seguintes localizações, no entanto, são bons pontos de partida no seu inventário:

### <a name="log-data"></a>Dados de registo

* *Endereços IP*: O Log Analytics recolhe uma variedade de informações ip em muitas tabelas diferentes. Por exemplo, a seguinte consulta mostra todas as tabelas onde os endereços IPv4 foram recolhidos nas últimas 24 horas:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *IDs do utilizador*: As iDs do utilizador são encontradas numa grande variedade de soluções e tabelas. Pode procurar um nome de utilizador específico em todo o seu conjunto de dados utilizando o comando de pesquisa:
    ```
    search "[username goes here]"
    ```
  Lembre-se de procurar não só nomes de utilizadores legíveis pelo homem, mas também guiDs que podem ser diretamente rastreados a um determinado utilizador!
* *IDs*do dispositivo : Como iDs de utilizador, as iDs do dispositivo são por vezes consideradas "privadas". Utilize a mesma abordagem acima listada para identificação de utilizadores para identificar tabelas onde isso possa ser uma preocupação. 
* *Dados personalizados*: O Log Analytics permite a recolha numa variedade de métodos: registos personalizados e campos personalizados, a API de Coleção de [Dados HTTP,](../../azure-monitor/platform/data-collector-api.md) e dados personalizados recolhidos como parte de registos de eventos do sistema. Todas estas questões são suscetíveis de conter dados privados e devem ser examinadas para verificar se existem tais dados.
* *Dados capturados pela solução*: Como o mecanismo de solução é um mecanismo de solução aberto, recomendamos rever todas as tabelas geradas por soluções para garantir o cumprimento.

### <a name="application-data"></a>Dados da aplicação

* *Endereços IP*: Embora os Insights de Aplicação obstarão, por padrão, todos os campos de endereços IP para "0.0.0.0", é um padrão bastante comum sobrepor este valor com o IP do utilizador real para manter a informação da sessão. A consulta de Analytics abaixo pode ser usada para encontrar qualquer tabela que contenha valores na coluna de endereçoIP que não seja "0.0.0.0" nas últimas 24 horas:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *IDs do utilizador*: Por padrão, os Insights de Aplicação utilizarão IDs gerados aleatoriamente para o rastreio do utilizador e da sessão. No entanto, é comum ver estes campos sobressados para armazenar um ID mais relevante para a aplicação. Por exemplo: nomes de utilizador, GUIDs AAD, etc. Estes IDs são muitas vezes considerados no âmbito como dados pessoais, pelo que devem ser tratados adequadamente. A nossa recomendação é sempre tentar obscar ou anonimizar estas identificações. Os campos onde estes valores são geralmente encontrados incluem session_Id, user_Id, user_AuthenticatedId, user_AccountId, bem como dimensões personalizadas.
* *Dados personalizados*: Os Insights de Aplicação permitem-lhe anexar um conjunto de dimensões personalizadas a qualquer tipo de dados. Estas dimensões podem ser *qualquer* dado. Utilize a seguinte consulta para identificar quaisquer dimensões personalizadas recolhidas nas últimas 24 horas:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dados in-memory e in-transit*: Application Insights rastreará exceções, pedidos, chamadas de dependência e vestígios. Os dados privados podem muitas vezes ser recolhidos ao nível de chamada http. Reveja as exceções, pedidos, dependências e traça tabelas para identificar tais dados. Utilize iniciais de [telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) sempre que possível para obstar estes dados.
* *Snapshot Debugger capturas*: A funcionalidade [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) em Application Insights permite-lhe recolher instantâneos de depurados sempre que uma exceção é apanhada na instância de produção da sua aplicação. Os instantâneos exporão o traço completo da pilha que conduz às exceções, bem como os valores para variáveis locais em cada passo da pilha. Infelizmente, esta funcionalidade não permite a eliminação seletiva de pontos de encaixe, nem acesso programático aos dados dentro do instantâneo. Portanto, se a taxa de retenção de instantâneos padrão não satisfaz os seus requisitos de conformidade, a recomendação é desligar a funcionalidade.

## <a name="how-to-export-and-delete-private-data"></a>Como exportar e eliminar dados privados

Tal como mencionado na secção de tratamento de [dados pessoais](#strategy-for-personal-data-handling) __anteriormente,__ recomenda-se fortemente que, se possível, reestruture a sua política de recolha de dados para desativar a recolha de dados privados, obstinação ou anos, ou modificando-os de outra forma para removê-lo de ser considerado "privado". O manuseamento dos dados resultará acima de tudo em custos para si e para a sua equipa para definir e automatizar uma estratégia, construir uma interface para os seus clientes interagirem com os seus dados através e custos de manutenção em curso. Além disso, é computacionalmente dispendioso para log analytics e Insights de aplicação, e um grande volume de consultas ou chamadas API de purga simultânea têm o potencial de impactar negativamente todas as outras interações com a funcionalidade Log Analytics. Dito isto, existem, de facto, alguns cenários válidos em que os dados privados devem ser recolhidos. Para estes casos, os dados devem ser tratados como descrito nesta secção.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Vista e exportação

Para ver e para os pedidos de dados de exportação, a [Consulta Log Analytics API](https://dev.loganalytics.io/) ou a Consulta de Insights de [Aplicação API](https://dev.applicationinsights.io/quickstart) devem ser utilizadas. A lógica de converter a forma dos dados para uma adequada para entregar aos seus utilizadores será da sua parte a sua implementação. [A Azure Functions](https://azure.microsoft.com/services/functions/) é um ótimo lugar para acolher tal lógica.

> [!IMPORTANT]
>  Embora a grande maioria das operações de purga possa ser concluída muito mais rapidamente do que a **SLA, o SLA formal para a conclusão das operações de purga é fixado em 30 dias** devido ao seu forte impacto na plataforma de dados utilizada. Este é um processo automatizado; não há como solicitar que uma operação seja tratada mais rapidamente.

### <a name="delete"></a>Eliminar

> [!WARNING]
> Os desisques em Log Analytics são destrutivos e não reversíveis! Por favor, tenha muito cuidado na execução deles.

Disponibilizámos como parte de uma privacidade que trata de um caminho de *Purga* API. Este caminho deve ser utilizado com moderação devido ao risco associado ao seu desempenho, ao potencial impacto de desempenho e ao potencial de distorcer agregações, medições e outros aspetos dos seus dados de Log Analytics. Consulte a secção de manipulação de [dados pessoais para](#strategy-for-personal-data-handling) obter abordagens alternativas para lidar com dados privados.

A purga é uma operação altamente privilegiada que nenhuma app ou utilizador em Azure (incluindo mesmo o proprietário do recurso) terá permissões para executar sem ser explicitamente concedido um papel no Gestor de Recursos Azure. Esta função é _Data Purger_ e deve ser delegada cautelosamente devido ao potencial de perda de dados. 

> [!IMPORTANT]
> Para gerir os recursos do sistema, os pedidos de purga são acelerados a 50 pedidos por hora. Deve remar a execução dos pedidos de purga enviando um único comando cujo predicado inclui todas as identidades do utilizador que requerem purga. Utilize o [operador in](/azure/kusto/query/inoperator) para especificar múltiplas identidades. Deve executar a consulta antes de executar o pedido de purga para verificar se os resultados são esperados. 



Uma vez atribuído o papel de Gestor de Recursos Azure, estão disponíveis dois novos caminhos da API: 

#### <a name="log-data"></a>Dados de registo

* [POST purga](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - toma um objeto especificando parâmetros de dados para eliminar e devolve um GUID de referência 
* Get estado de purga - a chamada de purga POST devolverá um cabeçalho de 'x-ms-status-location' que incluirá um URL que pode ligar para determinar o estado da sua API de purga. Por exemplo:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Embora esperemos que a grande maioria das operações de purga completem muito mais rapidamente do que o nosso SLA, devido ao seu forte impacto na plataforma de dados utilizada pelo Log Analytics, **o SLA formal para a conclusão das operações de purga é definido em 30 dias.** 

#### <a name="application-data"></a>Dados da aplicação

* [POST purga](https://docs.microsoft.com/rest/api/application-insights/components/purge) - toma um objeto especificando parâmetros de dados para eliminar e devolve um GUID de referência
* Get estado de purga - a chamada de purga POST devolverá um cabeçalho de 'x-ms-status-location' que incluirá um URL que pode ligar para determinar o estado da sua API de purga. Por exemplo:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Embora a grande maioria das operações de purga possam ser concluídas muito mais rapidamente do que o SLA, devido ao seu forte impacto na plataforma de dados utilizada pela Application Insights, **o SLA formal para a conclusão das operações de purga é fixado em 30 dias.**

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre como os dados do Log Analytics são recolhidos, processados e protegidos, consulte a [segurança dos dados do Log Analytics](../../azure-monitor/platform/data-security.md).
- Para saber mais sobre como os dados do Application Insights são recolhidos, processados e protegidos, consulte a [segurança de dados do Application Insights](../../azure-monitor/app/data-retention-privacy.md).
