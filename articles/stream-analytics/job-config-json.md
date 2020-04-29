---
title: Campos Azure Stream Analytics JobConfig.json
description: Este artigo lista os campos suportados para o ficheiro Azure Stream Analytics JobConfig.json usado para criar empregos no Código do Estúdio Visual.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617960"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Campos Azure Stream Analytics JobConfig.json

Os seguintes campos são suportados no ficheiro *JobConfig.json* usado para [criar um trabalho azure Stream Analytics usando o Código do Estúdio Visual](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Nome|Tipo|Necessário|Valor|
|----|----|--------|-----|
|DataLocale|string|Não|O local de dados do trabalho de análise de fluxo. O valor deve ser o nome de um suporte. Incumprimentos em "en-US" se nenhum especificado.|
|Política de Erro de Saída|string|Não|Indica a política a aplicar aos eventos que chegam à saída e não podem ser escritas no armazenamento externo devido a mal formação (valores de coluna em falta, valores de coluna de tipo ou tamanho errados). - Parar ou Largar|
|EventsLateArrivalMaxDelayInSeconds|número inteiro|Não|O atraso tolerável máximo em segundos onde os eventos que chegam atrasados podem ser incluídos. O intervalo suportado é de -1 a 1814399 (20.23:59:59 dias) e -1 é usado para especificar esperar indefinidamente. Se a propriedade estiver ausente, é interpretado como tem um valor de -1.|
|EventosOutofOrdermaxDelayInseconds|número inteiro|Não|O atraso tolerável máximo em segundos onde os eventos fora de ordem podem ser ajustados para voltar em ordem.|
|EventsOutofOrderPolicy|string|Não|Indica a política a aplicar a eventos que chegam fora de ordem no fluxo de eventos de entrada. - Ajustar ou Largar|
|Unidades de Streaming|número inteiro|Sim|Especifica o número de unidades de streaming que o trabalho de streaming utiliza.|
|CompatibilidadeN|string|Não|Controla certos comportamentos de tempo de execução do trabalho de streaming. - Os valores aceitáveis são "1.0", "1.1", "1.2"|
|UtilizaçãoSystemAssignedIdentity|boolean|Não|Deacordo para permitir que este trabalho se comunique com outros serviços Azure como ele próprio usando uma Identidade de Diretório Ativo Azure Gerido.|
|GlobalStorage.AccountName|string|Não|A conta de armazenamento global é usada para armazenar conteúdo relacionado com o seu trabalho de análise de fluxo, como instantâneos de dados de referência SQL.|
|GlobalStorage.AccountKey|string|Não|Chave correspondente para a conta de armazenamento global.|
|DataSourceCredentialDomain|string|Não|Propriedade reservada para armazenamento local credencial.|
|ScriptTypeType|string|Sim|Propriedade reservada para indicar o tipo deste ficheiro fonte. O valor aceitável é "JobConfig" para JobConfig.json.|
|Etiquetas|Pares de valor-chave JSON|Não|As etiquetas são pares de nome/valor que lhe permitem categorizar recursos e visualizar a faturação consolidada aplicando a mesma etiqueta a múltiplos recursos e grupos de recursos. Os nomes das etiquetas são insensíveis aos casos e os valores das etiquetas são sensíveis aos casos.|

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho azure Stream Analytics no Código do Estúdio Visual](quick-create-vs-code.md)
* [Teste Stream Analytics consulta localmente com dados de amostra usando código de estúdio visual](visual-studio-code-local-run.md)
* [Test Stream Analytics consulta localmente contra entrada](visual-studio-code-local-run-live-input.md)
*de fluxo ao vivo usando código de estúdio visual[Implementar um trabalho Azure Stream Analytics usando pacote de npm CI/CD](setup-cicd-vs-code.md)