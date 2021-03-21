---
title: Azure Stream Analytics JobConfig.jsnos campos
description: Este artigo lista os campos suportados para o Azure Stream Analytics JobConfig.jsem ficheiros usados para criar empregos no Código do Estúdio Visual.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020490"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.jsnos campos

Os seguintes campos são suportados no *JobConfig.jsem* ficheiro utilizado para criar um [trabalho Azure Stream Analytics usando o Código do Estúdio Visual](quick-create-visual-studio-code.md).

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
|DataLocale|cadeia (de carateres)|No|Os dados locais do trabalho de análise de fluxo. Valor deve ser o nome de um suportado. Predefinições para 'en-US' se nenhum especificado.|
|OutputErrorPolicy|cadeia (de carateres)|No|Indica a política a aplicar a eventos que chegam à saída e não podem ser escritos ao armazenamento externo devido a uma má formação (valores de coluna em falta, valores de coluna de tipo ou tamanho errados). - Parar ou Largar|
|EventosLateArrivalMaxDelayInSeconds|número inteiro|No|O atraso tolerável máximo em segundos onde os eventos que chegam tarde poderiam ser incluídos. O intervalo suportado é de -1 a 1814399 (20.23:59:59 dias) e -1 é utilizado para especificar a espera indefinidamente. Se o imóvel estiver ausente, é interpretado como ter um valor de -1.|
|EventosOutOfOrderMaxDelayInSegundos|número inteiro|No|O atraso tolerável máximo em segundos em que os eventos fora de ordem podem ser ajustados para estarem de volta em ordem.|
|EventosOutOfOrderPolicy|cadeia (de carateres)|No|Indica a política a aplicar aos eventos que chegam fora de ordem no fluxo do evento de entrada. - Ajustar ou Largar|
|StreamingUnits|número inteiro|Yes|Especifica o número de unidades de streaming que o trabalho de streaming utiliza.|
|CompatibilidadeLevel|cadeia (de carateres)|No|Controla certos comportamentos de tempo de execução do trabalho de streaming. - Valores aceitáveis são "1.0", "1.1", "1.2"|
|UseSystemAssignedIdentity|boolean|No|Definir-se verdadeiro para permitir que este trabalho comunique com outros serviços Azure como ele próprio usando uma Identidade de Diretório Ativo Azure Gerido.|
|GlobalStorage.AccountName|cadeia (de carateres)|No|A conta de armazenamento global é usada para armazenar conteúdo relacionado com o seu trabalho de análise de fluxo, como instantâneos de dados de referência SQL.|
|GlobalStorage.AccountKey|cadeia (de carateres)|No|Chave correspondente para conta de armazenamento global.|
|DataSourceCredentialDomain|cadeia (de carateres)|No|Propriedade reservada para armazenamento local credencial.|
|ScriptType|string|Yes|Propriedade reservada para indicar o tipo deste ficheiro de origem. O valor aceitável é "JobConfig" para JobConfig.js.|
|Etiquetas|Pares de valor-chave JSON|No|As etiquetas são pares de nome/valor que lhe permitem categorizar recursos e visualizar a faturação consolidada aplicando a mesma etiqueta a múltiplos recursos e grupos de recursos. Os nomes das etiquetas são insensíveis a casos e os valores das etiquetas são sensíveis a casos.|

## <a name="next-steps"></a>Passos seguintes

* [Criar um trabalho de Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)
* [Teste Stream Analytics consultas localmente com dados de amostra usando Código de Estúdio Visual](visual-studio-code-local-run.md)
* [Teste Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md) 
* [Implementar um trabalho Azure Stream Analytics usando pacote de npm CI/CD](./cicd-overview.md)