---
title: Azure Stream Analytics o particionamento de saída de blob personalizado
description: Este artigo descreve os padrões de caminho de data e hora personalizados e os recursos de campo ou atributos personalizados para a saída do armazenamento de blobs de trabalhos de Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426386"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics o particionamento de saída de blob personalizado

O Azure Stream Analytics dá suporte ao particionamento de saída de blob personalizado com campos ou atributos personalizados e padrões de caminho de data e hora personalizados. 

## <a name="custom-field-or-attributes"></a>Campo ou atributos personalizados

Os atributos de campo ou de entrada personalizados melhoram os fluxos de trabalho de processamento e relatório de dados downstream, permitindo mais controle sobre a saída.

### <a name="partition-key-options"></a>Opções de chave de partição

A chave de partição, ou o nome da coluna, usada para particionar dados de entrada pode conter caracteres alfanuméricos com hifens, sublinhados e espaços. Não é possível usar campos aninhados como uma chave de partição, a menos que sejam usados em conjunto com aliases. A chave de partição deve ser NVARCHAR (MAX).

### <a name="example"></a>Exemplo

Suponha que um trabalho receba dados de entrada de sessões de usuário ao vivo conectadas a um serviço de jogo de vídeo externo em que os dados ingeridos contenham uma coluna **client_id** para identificar as sessões. Para particionar os dados por **client_id**, defina o campo padrão de caminho de BLOB para incluir um token de partição **{client_id}** nas propriedades de saída de blob ao criar um trabalho. Como os dados com vários valores de **client_id** fluem por meio do trabalho de Stream Analytics, os dados de saída são salvos em pastas separadas com base em um único valor de **client_id** por pasta.

![Padrão de caminho com a ID do cliente](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Da mesma forma, se a entrada do trabalho tiver dados de sensor de milhões de sensores em que cada sensor tinha um **sensor_id**, o padrão de caminho será **{sensor_id}** para particionar cada dado de sensor em pastas diferentes.  


Usando a API REST, a seção de saída de um arquivo JSON usado para essa solicitação pode ser semelhante ao seguinte:  

![Saída da API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Depois que o trabalho começar a ser executado, o contêiner de *clientes* poderá ser semelhante ao seguinte:  

![Contêiner de clientes](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Cada pasta pode conter vários BLOBs em que cada blob contém um ou mais registros. No exemplo acima, há um único blob em uma pasta rotulada "06000000" com o seguinte conteúdo:

![Conteúdo do blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Observe que cada registro no blob tem uma coluna **client_id** correspondente ao nome da pasta, pois a coluna usada para particionar a saída no caminho de saída foi **client_id**.

### <a name="limitations"></a>Limitações

1. Somente uma chave de partição personalizada é permitida na propriedade de saída de blob de padrão de caminho. Todos os seguintes padrões de caminho são válidos:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. As chaves de partição não diferenciam maiúsculas de minúsculas, portanto, chaves de partição como "John" e "João" são equivalentes. Além disso, as expressões não podem ser usadas como chaves de partição. Por exemplo, **{ColumnA + coluna b}** não funciona.  

3. Quando um fluxo de entrada consiste em registros com uma cardinalidade de chave de partição em 8000, os registros serão anexados aos BLOBs existentes e apenas criarão novos BLOBs quando necessário. Se a cardinalidade estiver acima de 8000, não haverá nenhum blob de garantia que será gravado e novos BLOBs não serão criados para um número arbitrário de registros com a mesma chave de partição.

## <a name="custom-datetime-path-patterns"></a>Padrões de caminho de DateTime personalizados

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

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
