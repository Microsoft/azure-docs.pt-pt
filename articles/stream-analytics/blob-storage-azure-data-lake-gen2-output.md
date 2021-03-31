---
title: Armazenamento de blob e saída do Azure Data Lake Gen2 da Azure Stream Analytics
description: Este artigo descreve o armazenamento de blob e o Azure Data Lake Gen 2 como saída para a Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 3ce22837da2ca30249b399a297b6188d950b1ea4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935145"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Armazenamento de blob e saída do Azure Data Lake Gen2 da Azure Stream Analytics

Data Lake Storage Gen2 faz do Azure Storage a base para a construção de lagos de dados empresariais em Azure. Projetado desde o início para o serviço de múltiplos petabytes de informação enquanto sustenta centenas de gigabits de produção, Data Lake Storage Gen2 permite-lhe gerir facilmente quantidades massivas de dados. Uma parte fundamental do Data Lake Storage Gen2 é a adição de um espaço hierárquico de nomes para o armazenamento blob.

O armazenamento Azure Blob oferece uma solução rentável e escalável para armazenar grandes quantidades de dados não estruturados na nuvem. Para uma introdução no armazenamento blob e sua utilização, consulte [Upload, download e liste blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Configuração de saída

A tabela que se segue lista os nomes da propriedade e as suas descrições para a criação de uma saída blob ou ADLS Gen2.

| Nome da propriedade       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável usado em consultas para direcionar a saída de consulta para este armazenamento de bolhas. |
| Conta de armazenamento     | O nome da conta de armazenamento para onde está a enviar a sua produção.               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Recipiente de armazenamento   | Um agrupamento lógico para bolhas armazenadas no serviço Azure Blob. Quando fizer o upload de uma bolha para o serviço Blob, tem de especificar um recipiente para essa bolha. |
| Padrão do caminho | Opcional. O padrão do caminho do ficheiro que é usado para escrever as suas bolhas dentro do recipiente especificado. <br /><br /> No padrão do caminho, pode optar por utilizar uma ou mais instâncias das variáveis de data e hora para especificar a frequência que as bolhas estão escritas: <br /> {data}, {tempo} <br /><br />Pode utilizar a partição de bolhas personalizadas para especificar um nome personalizado {field} dos dados do evento para as bolhas de partição. O nome de campo é alfanumérico e pode incluir espaços, hífens e sublinhados. As restrições aos campos personalizados incluem: <ul><li>Os nomes de campo não são sensíveis a casos. Por exemplo, o serviço não pode diferenciar entre a coluna "ID" e a coluna "id".</li><li>Campos aninhados não são permitidos. Em vez disso, use um pseudónimo na consulta de trabalho para "achatar" o campo.</li><li>Expressões não podem ser usadas como nome de campo.</li></ul> <br />Esta funcionalidade permite a utilização de configurações de especificadores de data/hora personalizadas no caminho. Os formatos de data e hora personalizados devem ser especificados um de cada vez, incluídos na {data: \<specifier> } palavra-chave. As entradas admissíveis \<specifier> para são yyyy, MM, M, dd, d, HH, H, mm, m, ss, ou s. A {datatime: \<specifier> } a palavra-chave pode ser usada várias vezes no caminho para formar configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs/{date}/{time}</li><li>Exemplo 2: cluster1/logs/{date}</li><li>Exemplo 3: cluster1/{client_id}/{data}/{tempo}</li><li>Exemplo 4: cluster1/{datetime:ss}/{myField} onde a consulta é: SELECT data.myField AS myField FROM Input;</li><li>Exemplo 5: cluster1/ano={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />O carimbo de tempo da estrutura da pasta criada segue a UTC e não a hora local. [System.Timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) é o tempo usado para divisórias baseadas em todos os tempos.<br /><br />O nome de ficheiros utiliza a seguinte convenção: <br /><br />{Padrão do prefixo do caminho}/schemaHashcode_Guid_Number.extensão<br /><br /> Aqui Guid representa o identificador único atribuído a um escritor interno que é criado para escrever para um ficheiro blob. O número representa o índice do bloco blob. <br /><br /> Exemplo de ficheiros de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Para obter mais informações sobre esta funcionalidade, consulte [a partição de saída personalizada do Azure Stream Analytics.](stream-analytics-custom-path-patterns-blob-storage-output.md) |
| Formato de data | Opcional. Se o token de data for utilizado no caminho do prefixo, pode selecionar o formato de data em que os seus ficheiros estão organizados. Exemplo: YYYY/MM/DD |
| Formato de tempo | Opcional. Se o token de tempo for utilizado no caminho do prefixo, especifique o formato de tempo em que os seus ficheiros estão organizados. Atualmente, o único valor suportado é o HH. |
| Formato de serialização de eventos | Formato de serialização para dados de saída. JSON, CSV, Avro e Parquet são apoiados. |
|Linhas mínimas |O número de filas mínimas por lote. Para o Parquet, cada lote criará um novo ficheiro. O valor predefinido atual é de 2.000 linhas e o máximo permitido é de 10.000 linhas.|
|Tempo máximo |O tempo máximo de espera por lote. Após este período, o lote será escrito para a saída mesmo que o requisito de linhas mínimas não seja cumprido. O valor predefinido atual é de 1 minuto e o máximo permitido é de 2 horas. Se a sua saída de bolha tiver frequência de padrão de trajetória, o tempo de espera não pode ser superior ao intervalo de tempo da partição.|
| Encoding    | Se estiver a utilizar o formato CSV ou JSON, deve ser especificado um codificamento. UTF-8 é o único formato de codificação suportado neste momento. |
| Delimitador   | Aplicável apenas para serialização de CSV. Stream Analytics suporta uma série de delimiters comuns para serializar dados de CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, separador e barra vertical. |
| Formato      | Aplicável apenas para serialização JSON. **A linha separada** especifica que a saída é formatada por cada objeto JSON separado por uma nova linha. Se selecionar **linha separada,** o JSON é lido um objeto de cada vez. Todo o conteúdo por si só não seria um JSON válido. **A matriz** especifica que a saída é formatada como uma matriz de objetos JSON. Esta matriz só é fechada quando o trabalho parar ou stream Analytics passou para a janela da próxima vez. Em geral, é preferível usar JSON separados de linha, porque não requer qualquer manuseamento especial enquanto o ficheiro de saída ainda está a ser escrito. |

## <a name="blob-output-files"></a>Ficheiros de saída blob

Quando está a utilizar o armazenamento Blob como saída, é criado um novo ficheiro na bolha nos seguintes casos:

* Se o ficheiro exceder o número máximo de blocos permitidos (atualmente 50.000). Pode atingir o número máximo permitido de blocos sem atingir o tamanho máximo permitido de bolhas. Por exemplo, se a taxa de saída for elevada, pode ver mais bytes por bloco, e o tamanho do ficheiro é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do ficheiro é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer esquema fixo (CSV, Avro, Parquet).
* Se um trabalho for reiniciado, seja externamente por um utilizador que o pare e o inicie, ou internamente para manutenção do sistema ou recuperação de erros.
* Se a consulta estiver totalmente dividida, e for criado um novo ficheiro para cada divisão de saída.
* Se o utilizador eliminar um ficheiro ou um recipiente da conta de armazenamento.
* Se a saída for dividida pelo tempo utilizando o padrão de prefixo do caminho, e uma nova bolha for usada quando a consulta se move para a hora seguinte.
* Se a saída for dividida por um campo personalizado, e uma nova bolha for criada por chave de partição se não existir.
* Se a saída for dividida por um campo personalizado onde a cardinalidade da chave de partição ultrapassa os 8.000, e uma nova bolha é criada por chave de partição.

## <a name="partitioning"></a>Criação de partições

Para a tecla de partição, utilize fichas {date} e {time} dos campos de eventos no padrão do caminho. Escolha o formato de data, como YYYY/MM/DD, DD/MM/YYYY, ou MM-DD-YYYY. HH é usado para o formato de tempo. A saída do blob pode ser dividida por um único atributo de evento personalizado {fieldname} ou {datetime: \<specifier> }. O número de autores de saída segue a partição de entrada para [consultas totalmente paralesivelizáveis.](stream-analytics-scale-jobs.md)

## <a name="output-batch-size"></a>Tamanho do lote de saída

Para obter o tamanho máximo da mensagem, consulte [os limites de armazenamento do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O tamanho máximo do bloco blob é de 4 MB e a contagem máxima de bock blob é de 50.000. |

## <a name="next-steps"></a>Passos seguintes

* [Utilize identidade gerida (pré-visualização) para autenticar o seu trabalho Azure Stream Analytics para o armazenamento de blob Azure](blob-output-managed-identity.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
