---
title: Azure Stream Analytics divisória de saída de bolha personalizada
description: Este artigo descreve os padrões de trajetória datatime personalizados e o campo personalizado ou as funcionalidades de atributos para a saída de armazenamento de blob a partir de trabalhos Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9763a0ac3cba15dcfd66b8fad83230e2b0eb356b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491677"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics divisória de saída de bolha personalizada

O Azure Stream Analytics suporta a divisão de saída de blob personalizada com campos ou atributos personalizados e padrões de caminho datatime personalizados. 

## <a name="custom-field-or-attributes"></a>Campo personalizado ou atributos

Os atributos de campo ou de entrada personalizados melhoram os fluxos de trabalho de processamento e relatório de dados a jusante, permitindo um maior controlo sobre a saída.

### <a name="partition-key-options"></a>Opções-chave de partição

A chave de partição, ou nome da coluna, usado para os dados de entrada de partição pode conter caracteres alfanuméricos com hífens, sublinhados e espaços. Não é possível utilizar campos aninhados como chave de partição, a menos que seja utilizado em conjunto com pseudónimos. A tecla de partição deve ser NVARCHAR(MAX), BIGINT, FLOAT ou BIT (nível de compatibilidade de 1,2 ou superior). Para obter mais informações, consulte os [tipos de dados Azure Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Exemplo

Suponha que um trabalho retira dados de entrada de sessões de utilizadores ao vivo ligadas a um serviço de videojogos externo onde dados ingeridos contêm uma coluna **client_id** para identificar as sessões. Para dividir os dados **client_id,** descreva o campo Blob Path Pattern para incluir um token de partição **{client_id}** nas propriedades de saída de blob ao criar um trabalho. À medida que os dados com vários valores **client_id** fluem através do trabalho stream Analytics, os dados de saída são guardados em pastas separadas com base num único valor **de client_id** por pasta.

![Padrão de caminho com id cliente](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Da mesma forma, se a entrada de trabalho fosse dados de sensores de milhões de sensores onde cada sensor tinha uma **sensor_id**, o Path Pattern seria **{sensor_id}** para dividir cada dado do sensor em diferentes pastas.  


Utilizando a API REST, a secção de saída de um ficheiro JSON utilizado para esse pedido pode parecer o seguinte:  

![Saída DE API de REPOUSO](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Uma vez que o trabalho começa a funcionar, o contentor de *clientes* pode parecer o seguinte:  

![Recipiente de clientes](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Cada pasta pode conter várias bolhas onde cada bolha contém um ou mais registos. No exemplo acima, existe uma única bolha numa pasta com a etiqueta "06000000" com os seguintes conteúdos:

![Conteúdo sonoro](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Note que cada registo na bolha tem uma coluna **client_id** correspondente ao nome da pasta, uma vez que a coluna utilizada para dividir a saída no caminho de saída foi **client_id**.

### <a name="limitations"></a>Limitações

1. Apenas uma chave de partição personalizada é permitida na propriedade de saída de blob Path Pattern. Todos os seguintes padrões de caminho são válidos:

   * cluster1/{data}/{aFieldInMyData}  
   * cluster1/{tempo}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{data}/{time}/{aFieldInMyData} 
   
2. As chaves de partição são insensíveis, por isso as chaves de partição como "John" e "John" são equivalentes. Além disso, as expressões não podem ser usadas como chaves de partição. Por exemplo, **{columnA + columnB}** não funciona.  

3. Quando um fluxo de entrada consiste em registos com uma cardinalidade chave de partição abaixo de 8000, os registos serão anexados a bolhas existentes e só criarão novas bolhas quando necessário. Se o cardinalício for superior a 8000, não há garantias de que as bolhas existentes sejam escritas e não serão criadas novas bolhas para um número arbitrário de registos com a mesma chave de partição.

4. Se a saída do blob for [configurada como imutável,](../storage/blobs/storage-blob-immutable-storage.md)o Stream Analytics criará uma nova bolha cada vez que os dados são enviados.

## <a name="custom-datetime-path-patterns"></a>Padrões de caminho personalizados do tempo de data

Os padrões de trajetória personalizados do DateTime permitem especificar um formato de saída que se alinha com as convenções de Streaming de Hive, dando ao Azure Stream Analytics a capacidade de enviar dados para Azure HDInsight e Azure Databricks para o processamento a jusante. Os padrões de trajetória do Tempo de Data personalizados são facilmente implementados utilizando a `datetime` palavra-chave no campo prefixo do caminho da sua saída blob, juntamente com o especificador de formato. Por exemplo, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Fichas apoiadas

Os seguintes tokens especificadores de formato podem ser usados sozinhos ou em combinação para obter formatos datatime personalizados:

|Especificador de formato   |Descrição   |Resultados no horário de exemplo 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datatime:yyyy}|O ano como um número de quatro dígitos|2018|
|{datatime:MM}|Mês de 01 a 12|01|
|{datatime:M}|Mês de 1 a 12|1|
|{datatime:dd}|Dia das 01 às 31|02|
|{datatime:d}|Dia das 1 a 31|2|
|{hora de data:HH}|Hora usando o formato 24 horas, de 00 a 23|10|
|{datatime:mm}|Minutos das 00 às 60|06|
|{datatime:m}|Minutos das 0 às 60|6|
|{datatime:ss}|Segundos de 00 a 60|08|

Se não pretender utilizar padrões de DateTime personalizados, pode adicionar o token {date} e/ou {time} ao Prefixo do Caminho para gerar um dropdown com formatos DataTime incorporados.

![Stream Analytics velhos formatos DateTime](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Extensibilidade e restrições

Você pode usar quantos tokens, `{datetime:<specifier>}` como você gosta no padrão do caminho até chegar ao limite de caracteres do Prefixo caminho. Os especificadores de formato não podem ser combinados dentro de um único token para além das combinações já listadas pelas datas e quebras de hora. 

Para uma partição de caminho `logs/MM/dd` de:

|Expressão válida   |Expressão inválida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Pode utilizar o mesmo especificador de formato várias vezes no Prefixo do Caminho. O símbolo deve ser repetido cada vez.

### <a name="hive-streaming-conventions"></a>Convenções de streaming da Colmeia

Padrões de caminho personalizados para armazenamento de bolhas podem ser usados com a convenção de Streaming de Colmeia, que espera que as pastas sejam etiquetadas `column=` no nome da pasta.

Por exemplo, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

A saída personalizada elimina o incómodo de alterar tabelas e adicionar manualmente divisórias aos dados portuários entre a Azure Stream Analytics e a Hive. Em vez disso, muitas pastas podem ser adicionadas automaticamente utilizando:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemplo

Crie uma conta de armazenamento, um grupo de recursos, um trabalho stream Analytics e uma fonte de entrada de acordo com o guia de arranque rápido do [portal Azure Stream Analytics.](stream-analytics-quick-create-portal.md) Utilize os mesmos dados de amostra utilizados no guia quickstart, também disponível no [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Criar um lavatório de saída blob com a seguinte configuração:

![Stream Analytics cria pia de saída blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

O padrão de caminho completo é o seguinte:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quando inicia o trabalho, uma estrutura de pasta baseada no padrão do caminho é criada no seu recipiente blob. Podes perfurar até ao nível do dia.

![Saída de blob de stream Analytics com padrão de caminho personalizado](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)