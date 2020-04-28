---
title: Partição de saída de blob personalizada Azure Stream Analytics
description: Este artigo descreve os padrões de percurso sinuosos personalizados do DateTime e o campo personalizado ou atributos funcionalidades para a saída de armazenamento de blob a partir de trabalhos do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426386"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Partição de saída de blob personalizada Azure Stream Analytics

O Azure Stream Analytics suporta divisórias personalizadas de saída blob com campos ou atributos personalizados e padrões de percurso sinuosos personalizados. 

## <a name="custom-field-or-attributes"></a>Campo ou atributos personalizados

Os atributos de campo ou entrada personalizados melhoram o processamento de dados a jusante e reportam fluxos de trabalho, permitindo um maior controlo sobre a saída.

### <a name="partition-key-options"></a>Opções-chave de partição

A chave de partição, ou nome da coluna, utilizada para os dados de entrada de divisórias pode conter caracteres alfanuméricos com hífens, sublinhados e espaços. Não é possível utilizar campos aninhados como chave de partição, a menos que seja utilizado em conjunto com pseudónimos. A chave de partição deve ser NVARCHAR (MAX).

### <a name="example"></a>Exemplo

Suponha que um trabalho recorra de dados de entrada de sessões de utilizadores ao vivo ligadas a um serviço externo de videojogos onde os dados ingeridos contêm uma coluna **client_id** para identificar as sessões. Para dividir os dados por **client_id,** detete o campo Blob Path Pattern para incluir um símbolo de partição **{client_id}** nas propriedades de saída blob ao criar um emprego. À medida que os dados com vários **client_id** valores fluem através do trabalho do Stream Analytics, os dados de saída são guardados em pastas separadas com base num único **valor client_id** por pasta.

![Padrão de caminho com id cliente](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Da mesma forma, se a entrada de trabalho fosse dados de sensores de milhões de sensores onde cada sensor tinha um **sensor_id**, o Padrão do Caminho seria **{sensor_id}** para dividir cada dados do sensor para diferentes pastas.  


Utilizando a API REST, a secção de saída de um ficheiro JSON utilizado para esse pedido pode parecer o seguinte:  

![Saída de API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Uma vez que o trabalho começa a funcionar, o recipiente de *clientes* pode parecer o seguinte:  

![Contentor de clientes](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Cada pasta pode conter várias bolhas onde cada bolha contém um ou mais registos. No exemplo acima, existe uma única bolha numa pasta rotulada "06000000" com os seguintes conteúdos:

![Conteúdo blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Note que cada registo na bolha tem uma coluna **client_id** que corresponde ao nome da pasta, uma vez que a coluna utilizada para dividir a saída no caminho de saída foi **client_id**.

### <a name="limitations"></a>Limitações

1. Apenas uma chave de divisória personalizada é permitida na propriedade de saída de bolhas Path Pattern. Todos os seguintes Padrões de Caminho são válidos:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. As chaves da partição são insensíveis, por isso as teclas de partição como "John" e "John" são equivalentes. Além disso, as expressões não podem ser usadas como chaves de partição. Por exemplo, **{columnA + columnB}** não funciona.  

3. Quando um fluxo de entrada é composto por registos com uma cardeal-chave de divisão inferior a 8000, os registos serão anexados às bolhas existentes e só criam novas bolhas quando necessário. Se a cardinalidade for superior a 8000, não há garantias de que as bolhas existentes serão escritas e novas bolhas não serão criadas para um número arbitrário de registos com a mesma chave de partição.

## <a name="custom-datetime-path-patterns"></a>Padrões de caminho sinuoso de data sinuosa

Os padrões de percurso sinuosos do DateTime personalizado permitem especificar um formato de saída que se alinha com as convenções de Streaming de Hive, dando ao Azure Stream Analytics a capacidade de enviar dados para O Azure HDInsight e Azure Databricks para processamento a jusante. Os padrões de percurso de DataTime personalizados são facilmente implementados utilizando a `datetime` palavra-chave no campo Path Prefix da sua saída blob, juntamente com o especificador de formato. Por exemplo, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Fichas suportadas

As seguintes fichas de especificador de formato podem ser utilizadas apenas ou em combinação para obter formatos dateTime personalizados:

|Especificador de formato   |Descrição   |Resultados na hora de exemplo 2018-01-02T10:06:08|
|----------|-----------|------------|
|{data:yyyy}|O ano como um número de quatro dígitos|2018|
|{data:MM}|Mês de 01 a 12|01|
|{data:M}|Mês de 1 a 12|1|
|{data:dd}|Dia de 01 a 31|02|
|{data:d}|Dia de 1 a 12|2|
|{data:HH}|Hora usando o formato 24 horas, de 00 a 23|10|
|{data:mm}|Minutos de 00 a 24|06|
|{data:m}|Minutos de 0 a 24|6|
|{data:ss}|Segundos de 00 a 60|08|

Se não desejar utilizar padrões de DataTime personalizados, pode adicionar o token {date} e/ou {time} ao Prefixo path para gerar uma queda com formatos DateTime incorporados.

![Stream Analytics formatos antigos dateTime](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Extebilidade e restrições

Pode usar o máximo de `{datetime:<specifier>}`fichas, como quiser no padrão do caminho até chegar ao limite de caracteres Path Prefix. Os especificadores de formato não podem ser combinados num único token para além das combinações já listadas pela data e pela hora. 

Para uma divisão `logs/MM/dd`de caminho de:

|Expressão válida   |Expressão inválida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Pode utilizar o mesmo especificador de formato várias vezes no Prefixo do Caminho. O símbolo deve ser repetido cada vez.

### <a name="hive-streaming-conventions"></a>Convenções de streaming de colmeias

Os padrões de percurso personalizados para armazenamento de bolhas podem ser usados `column=` com a convenção hive streaming, que espera que as pastas sejam rotuladas com o nome da pasta.

Por exemplo, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

A saída personalizada elimina o incómodo das tabelas de alteração e adiciona manualmente divisórias aos dados da porta entre o Azure Stream Analytics e a Hive. Em vez disso, muitas pastas podem ser adicionadas automaticamente usando:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemplo

Crie uma conta de armazenamento, um grupo de recursos, um trabalho de Stream Analytics e uma fonte de entrada de acordo com o guia quickstart Do [Portal Azure Stream.](stream-analytics-quick-create-portal.md) Utilize os mesmos dados de amostra utilizados no guia quickstart, também disponíveis no [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Crie um lavatório de saída blob com a seguinte configuração:

![Stream Analytics cria pia de saída de bolha](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

O padrão completo do caminho é o seguinte:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quando inicia o trabalho, é criada uma estrutura de pasta baseada no padrão do caminho no seu recipiente de bolhas. Pode perfurar até ao nível do dia.

![Saída de blob Stream Analytics com padrão de caminho personalizado](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
