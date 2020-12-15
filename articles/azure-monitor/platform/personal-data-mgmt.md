---
title: Orientação para dados pessoais armazenados no Azure Log Analytics Microsoft Docs
description: Este artigo descreve como gerir os dados pessoais armazenados no Azure Log Analytics e os métodos para identificá-lo e removê-lo.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 2bb1e667758a1430e34d222b9a5c537381c07624
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505278"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Orientações para dados pessoais armazenados no Log Analytics e no Application Insights

O Log Analytics é uma loja de dados onde é provável que os dados pessoais sejam encontrados. A Application Insights armazena os seus dados numa partição do Log Analytics. Este artigo irá discutir onde em Log Analytics e Application Insights esses dados são normalmente encontrados, bem como as capacidades disponíveis para lidar com esses dados.

> [!NOTE]
> Para efeitos deste _artigo,_ os dados de registo referem-se a dados enviados para um espaço de trabalho do Log Analytics, enquanto _os dados_ da aplicação referem-se aos dados recolhidos pela Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Estratégia para o tratamento de dados pessoais

Embora seja sua a sua operação determinar a estratégia com que irá lidar com os seus dados privados (se é que é verdade), as seguintes são algumas abordagens possíveis. São listados por ordem de preferência de um ponto de vista técnico da maioria para menos preferível:

* Sempre que possível, parar a recolha de, obfuscate, anonimize, ou de outra forma ajustar os dados recolhidos para excluir que sejam considerados "privados". Esta é _de longe_ a abordagem preferida, poupando-lhe a necessidade de criar uma estratégia de tratamento de dados muito dispendiosa e impactante.
* Sempre que possível, tente normalizar os dados para reduzir o impacto na plataforma de dados e desempenho. Por exemplo, em vez de registar um ID de utilizador explícito, crie um dado de pesquisa que correlacionará o nome de utilizador e os seus dados a um ID interno que pode ser registado em outro lugar. Desta forma, caso um dos seus utilizadores lhe peça para apagar as suas informações pessoais, é possível que apenas a eliminação da linha na tabela de procuração correspondente ao utilizador seja suficiente. 
* Finalmente, se os dados privados tiverem de ser recolhidos, construa um processo em torno do caminho da expurgação da API e do caminho da API de consulta existente para cumprir quaisquer obrigações que possa ter em torno da exportação e eliminação de quaisquer dados privados associados a um utilizador. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Onde procurar dados privados no Log Analytics?

O Log Analytics é uma loja flexível, que ao prescrever um esquema aos seus dados, permite-lhe sobrepor todos os campos com valores personalizados. Além disso, qualquer esquema personalizado pode ser ingerido. Como tal, é impossível dizer exatamente onde os dados privados serão encontrados no seu espaço de trabalho específico. Os seguintes locais, no entanto, são bons pontos de partida no seu inventário:

### <a name="log-data"></a>Dados de registo

* *Endereços IP*: Log Analytics recolhe uma variedade de informações IP em várias tabelas diferentes. Por exemplo, a seguinte consulta mostra todas as tabelas onde os endereços IPv4 foram recolhidos nas últimas 24 horas:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *IDs do utilizador*: Os IDs do utilizador encontram-se numa grande variedade de soluções e tabelas. Pode procurar um nome de utilizador em todo o seu conjunto de dados utilizando o comando de pesquisa:
    ```
    search "[username goes here]"
    ```
  Lembre-se de procurar não só nomes de utilizadores legíveis pelo homem, mas também GUIDs que podem ser diretamente rastreados até um determinado utilizador!
* *IDs do dispositivo*: Tal como os IDs do utilizador, os IDs do dispositivo são por vezes considerados "privados". Utilize a mesma abordagem listada acima para identificação de iDs de utilizador para identificar tabelas onde isso possa ser uma preocupação. 
* *Dados personalizados*: O Log Analytics permite a recolha numa variedade de métodos: registos personalizados e campos personalizados, [a API do Recolhão de Dados HTTP](./data-collector-api.md) e dados personalizados recolhidos como parte dos registos de eventos do sistema. Todos estes são suscetíveis a conter dados privados, e devem ser examinados para verificar se esses dados existem.
* *Dados capturados* por solução : Como o mecanismo de solução é um mecanismo de solução aberto, recomendamos a revisão de todas as tabelas geradas por soluções para garantir o cumprimento.

### <a name="application-data"></a>Dados da aplicação

* *Endereços IP*: Embora os Insights de Aplicação obfundem por defeito todos os campos de endereço IP para "0.0.0.0", é um padrão bastante comum para anular este valor com o IP do utilizador real para manter a informação da sessão. A consulta abaixo pode ser usada para encontrar qualquer tabela que contenha valores na coluna de endereço IP que não seja "0.0.0.0" nas últimas 24 horas:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *IDs do utilizador*: Por padrão, os Application Insights utilizarão IDs gerados aleatoriamente para o utilizador e rastreio de sessão. No entanto, é comum ver estes campos ultrapassados para armazenar um ID mais relevante para a aplicação. Por exemplo: nomes de utilizador, GUIDs AAD, etc. Estes IDs são muitas vezes considerados como in-scope como dados pessoais, pelo que devem ser tratados adequadamente. A nossa recomendação é sempre tentar obsetinar ou anonimizar estas identificações. Os campos onde estes valores são geralmente encontrados incluem session_Id, user_Id, user_AuthenticatedId, user_AccountId, bem como as dimensões personalizadas.
* *Dados personalizados*: O Application Insights permite-lhe anexar um conjunto de dimensões personalizadas a qualquer tipo de dado. Estas dimensões podem ser *qualquer* dado. Utilize a seguinte consulta para identificar quaisquer dimensões personalizadas recolhidas nas últimas 24 horas:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dados em memória e em trânsito*: Os Insights de Aplicação acompanharão exceções, pedidos, chamadas de dependência e vestígios. Os dados privados podem ser recolhidos frequentemente ao nível do código e da chamada HTTP. Reveja as exceções, pedidos, dependências e traços de tabelas para identificar tais dados. Utilize [inicializadores de telemetria](../app/api-filtering-sampling.md) sempre que possível para ocultar estes dados.
* *Snapshot Debugger captura*: A funcionalidade [Snapshot Debugger](../app/snapshot-debugger.md) em Application Insights permite-lhe recolher instantâneos de depurar sempre que uma exceção é apanhada na instância de produção da sua aplicação. Os instantâneos exporão o traço completo da pilha, conduzindo às exceções, bem como os valores para variáveis locais em cada passo da pilha. Infelizmente, esta funcionalidade não permite a eliminação seletiva de pontos de encaixe ou acesso programático aos dados dentro do instantâneo. Portanto, se a taxa de retenção de instantâneo padrão não satisfaz os seus requisitos de conformidade, a recomendação é desligar a funcionalidade.

## <a name="how-to-export-and-delete-private-data"></a>Como exportar e eliminar dados privados

Como mencionado na [estratégia de tratamento de dados pessoais](#strategy-for-personal-data-handling) __anteriormente,__ é fortemente recomendado para, se tudo possível, reestruturar a sua política de recolha de dados para desativar a recolha de dados privados, obfuscando ou anonimizando-os, ou de outra forma modificá-lo para removê-lo de ser considerado "privado". O tratamento dos dados resultará, acima de tudo, em custos para si e para a sua equipa para definir e automatizar uma estratégia, construir uma interface para que os seus clientes interajam com os seus dados através dos seus dados, e custos de manutenção contínuos. Além disso, é computacionalmente dispendioso para Log Analytics e Application Insights, e um grande volume de consultas simultâneas ou chamadas de expurgação API têm o potencial de impactar negativamente todas as outras interação com a funcionalidade Log Analytics. Dito isto, existem, de facto, alguns cenários válidos em que os dados privados devem ser recolhidos. Para estes casos, os dados devem ser tratados como descrito nesta secção.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Ver e exportar

Para pedidos de visualização e exportação de dados, deve ser utilizada a [API de consulta de informação de log analytics](https://dev.loganalytics.io/) ou a [consulta de Insights de Aplicação API.](https://dev.applicationinsights.io/quickstart) A lógica de converter a forma dos dados para um adequado para entregar aos seus utilizadores cabe-lhe a si implementar. [As Funções Azure](https://azure.microsoft.com/services/functions/) são um ótimo lugar para acolher tal lógica.

> [!IMPORTANT]
>  Embora a grande maioria das operações de purga possa completar muito mais rapidamente do que o SLA, **o SLA formal para a conclusão das operações de purga é fixado em 30 dias** devido ao seu forte impacto na plataforma de dados utilizada. Este SLA satisfaz os requisitos do RGPD. É um processo automatizado, por isso não há como pedir que uma operação seja tratada mais rapidamente. 

### <a name="delete"></a>Eliminar

> [!WARNING]
> As eliminações no Log Analytics são destrutivas e não reversíveis! Por favor, tenha muito cuidado na execução deles.

Disponibilizamos como parte de uma privacidade que lida com um caminho *de expurgação* da API. Este caminho deve ser utilizado com moderação devido ao risco associado a fazê-lo, ao impacto potencial do desempenho e ao potencial de distorcer agregações, medições e outros aspetos dos seus dados do Log Analytics. Consulte a [secção de tratamento de dados pessoais](#strategy-for-personal-data-handling) para obter abordagens alternativas para lidar com dados privados.

> [!NOTE]
> Uma vez realizada a operação de purga, os dados não podem ser acedidos enquanto o estado *de operação* de [purga](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/getpurgestatus) estiver pendente . 

A purga é uma operação altamente privilegiada que nenhuma app ou utilizador em Azure (incluindo mesmo o proprietário do recurso) terá permissões para executar sem que seja expressamente concedido um papel no Azure Resource Manager. Esta função é _Data Purger_ e deve ser delegada cautelosamente devido ao potencial de perda de dados. 

> [!IMPORTANT]
> Para gerir os recursos do sistema, os pedidos de purga são acelerados a 50 pedidos por hora. Deve emarnar a execução de pedidos de purga enviando um único comando cujo predicado inclui todas as identidades do utilizador que requerem purga. Utilize o [operador in](/azure/kusto/query/inoperator) para especificar várias identidades. Deve executar a consulta antes de executar o pedido de purga para verificar se os resultados são esperados. 



Uma vez atribuída a função de Gestor de Recursos Azure, estão disponíveis dois novos caminhos da API: 

#### <a name="log-data"></a>Dados de registo

* [Purga POST](/rest/api/loganalytics/workspacepurge/purge) - toma um objeto especificando parâmetros de dados para apagar e devolve uma referência GUID 
* Obtenha o estado de purga - a chamada de purga POST devolverá um cabeçalho 'x-ms-status-location' que incluirá um URL que pode ligar para determinar o estado da sua expurgação API. Por exemplo:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Embora esperemos que a grande maioria das operações de purga completem muito mais rapidamente do que o nosso SLA, devido ao seu forte impacto na plataforma de dados utilizada pela Log Analytics, **o SLA formal para a conclusão das operações de purga é fixado em 30 dias.** 

#### <a name="application-data"></a>Dados da aplicação

* [Purga POST](/rest/api/application-insights/components/purge) - toma um objeto especificando parâmetros de dados para apagar e devolve uma referência GUID
* Obtenha o estado de purga - a chamada de purga POST devolverá um cabeçalho 'x-ms-status-location' que incluirá um URL que pode ligar para determinar o estado da sua expurgação API. Por exemplo:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Embora a grande maioria das operações de purga possa completar muito mais rapidamente do que o SLA, devido ao seu forte impacto na plataforma de dados utilizada pela Application Insights, **o SLA formal para a conclusão das operações de purga é fixado em 30 dias.**

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre como os dados do Log Analytics são recolhidos, processados e protegidos, consulte [a segurança de dados do Log Analytics](./data-security.md).
- Para saber mais sobre como os dados do Application Insights são recolhidos, processados e protegidos, consulte [a segurança dos dados do Application Insights](../app/data-retention-privacy.md).

