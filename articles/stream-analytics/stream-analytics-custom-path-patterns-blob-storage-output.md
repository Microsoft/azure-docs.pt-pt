---
title: A criação de partições de saída de Blobs do Azure Stream Analytics, personalizado
description: Este artigo descreve os padrões de caminho de DateTime personalizados e os recursos de campo ou atributos personalizados para a saída de armazenamento de BLOBs de tarefas do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e06313cf83768421bedc6c7baddd30c2ef2e4846
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789426"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>A criação de partições de saída de Blobs do Azure Stream Analytics, personalizado

O Azure Stream Analytics suporta a criação de partições com campos personalizados ou atributos e DateTime personalizadas padrões de caminho de saída de blob personalizado. 

## <a name="custom-field-or-attributes"></a>Campo personalizado ou atributos

Campo personalizado ou atributos de entrada melhoram downstream processamento de dados e relatórios de fluxos de trabalho, permitindo mais controle sobre a saída.

### <a name="partition-key-options"></a>Opções de chave de partição

A chave de partição ou o nome de coluna, utilizado para particionar os dados de entrada pode conter carateres alfanuméricos, hífenes, carateres de sublinhado e espaços. Não é possível usar campos aninhados como uma chave de partição, a menos que utilizado em conjunto com aliases. A chave de partição tem de ser nvarchar (Max).

### <a name="example"></a>Exemplo

Suponha que uma tarefa leva dados de entrada de sessões de utilizador em direto ligados a um serviço externo de vídeo de jogo em que os dados ingeridos contém uma coluna **client_id** para identificar as sessões. Para particionar os dados por **client_id**, defina o campo de padrão do caminho de Blob para incluir um token de partição **{client_id}** nas propriedades de saída do blob durante a criação de uma tarefa. Como os dados com vários **client_id** valores fluem através de tarefa do Stream Analytics, os dados de saída são guardados em pastas separadas com base num único **client_id** valor por pasta.

![Padrão do caminho com o id de cliente](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Da mesma forma, se a tarefa de entrada foi dados de sensores de milhões de sensores em que cada sensor tinha uma **sensor_id**, o padrão de caminho seria **{sensor_id}** para particionar cada dados de sensor para pastas diferentes.  


Arquivo usado para que a solicitação com a API REST, a secção de saída de um JSON pode ser semelhante ao seguinte:  

![Saída de REST API](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Assim que a tarefa é iniciada em execução, o *clientes* contentor pode ser semelhante ao seguinte:  

![Contentor de clientes](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Cada pasta pode conter vários blobs em que cada blob contém um ou mais registos. No exemplo acima, há um único blob numa pasta assinaladas como "06000000" com o seguinte conteúdo:

![Conteúdo do blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Tenha em atenção que cada registo no blob tem um **client_id** nome da coluna correspondente na pasta, desde que a coluna utilizada para particionar a saída no caminho de saída foi **client_id**.

### <a name="limitations"></a>Limitações

1. Apenas uma chave de partição personalizada é permitida na propriedade de saída do blob de padrão do caminho. Todos os seguintes padrões de caminho são válidos:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. As chaves de partição diferenciam maiúsculas de minúsculas, pelo que as chaves de partição, como "John" e "João" são equivalentes. Além disso, expressões não podem ser utilizadas como chaves de partição. Por exemplo, **{ferramenta + columnB}** não funciona.  

3. Quando um fluxo de entrada é composta por registos com uma cardinalidade de chave de partição em 8000, os registos serão acrescentados a blobs existentes e apenas criar novos blobs quando necessário. Se a cardinalidade é efetuada através de 8000 não existe nenhuma garantia existente blobs de escrita e novos blobs não serão criados para um número arbitrário de registos com a mesma chave de partição.

## <a name="custom-datetime-path-patterns"></a>Padrões de caminho de DateTime personalizadas

Padrões de caminho de DateTime personalizadas permitem-lhe especificar um formato de saída que se alinha com convenções de ramo de registo de transmissão em fluxo, oferecendo a capacidade de enviar dados para o Azure HDInsight e o Azure Databricks para processar downstream de Azure Stream Analytics. Padrões de caminho de DateTime personalizados facilmente são implementados usando o `datetime` palavra-chave no campo de prefixo do caminho do seu blob de saída, juntamente com o especificador de formato. Por exemplo, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Tokens suportados

Os tokens de especificador de formato seguinte podem ser usados individualmente ou em combinação para alcançar os formatos de DateTime personalizados:

|Especificador de formato   |Descrição   |Os resultados de tempo de exemplo de 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|O ano como um número de quatro dígitos|2018|
|{datetime:MM}|Mês a partir de 01 a 12|01|
|{datetime:M}|Mês a partir de 1 a 12|1|
|{datetime:dd}|Dia a partir de 01 e 31|02|
|{datetime:d}|Dia a partir de 1 a 12|2|
|{datetime:HH}|Usando o formato de 24 horas, de 00 e 23 de hora|10|
|{datetime:mm}|Minutos de 00 a 24|06|
|{datetime:m}|Minutos de 0 a 24|6|
|{datetime:ss}|Segundos a partir de 00 para 60|08|

Se não pretender utilizar padrões de DateTime personalizadas, pode adicionar a {date} e/ou {time} o token para o prefixo do caminho para gerar uma lista suspensa com formatos de DateTime incorporados.

![Formatos de DateTime antigos do Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Extensibilidade e restrições

Pode utilizar os tokens, `{datetime:<specifier>}`, conforme desejar no padrão de caminho, até atingir o limite de carateres de prefixo do caminho. Especificadores de formato não podem ser combinados num único token para além das combinações já listadas pelas listas pendentes de data e hora. 

Para uma partição de caminho de `logs/MM/dd`:

|Expressão válida   |Expressão inválida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Pode utilizar o mesmo especificador de formato várias vezes no prefixo do caminho. O token deve ser repetido a cada vez.

### <a name="hive-streaming-conventions"></a>Convenções de transmissão em fluxo do ramo de registo

Padrões de caminho personalizado para o armazenamento de BLOBs podem ser utilizadas com a Convenção de ramo de registo de transmissão em fluxo, que espera pastas para ser rotulados com `column=` no nome da pasta.

Por exemplo, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Saída personalizada elimina a necessidade de alterar tabelas e a adição manual de partições de dados de porta entre o Azure Stream Analytics e o Hive. Em vez disso, muitos pastas podem ser adicionadas automaticamente ao utilizar:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemplo

Criar uma conta de armazenamento, um grupo de recursos, uma tarefa do Stream Analytics e uma origem de entrada de acordo com o [Portal do Azure Stream Analytics do Azure](stream-analytics-quick-create-portal.md) guia de início rápido. Utilize os mesmos dados de exemplo utilizados no guia de início rápido, também está disponível no [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Crie um sink de saída do blob com a seguinte configuração:

![Stream Analytics criar sink de saída do blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

O padrão de caminho completo é o seguinte:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quando iniciar a tarefa, uma estrutura de pastas com base no padrão de caminho é criada no contentor de blob. Pode desagregar para o nível do dia.

![Saída de blob do Stream Analytics com o padrão do caminho personalizado](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Passos Seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
