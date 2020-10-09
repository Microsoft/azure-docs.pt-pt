---
title: Migrar centenas de terabytes de dados para o Azure Cosmos DB
description: Este médico descreve como pode migrar 100 s de terabytes de dados para cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: 1e48b2ff6e469a5f792b64c20631e4bd64fb9fd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85263549"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrar centenas de terabytes de dados para o Azure Cosmos DB 

O Azure Cosmos DB pode armazenar terabytes de dados. Pode realizar uma migração de dados de grande escala para mover a carga de trabalho de produção para o Azure Cosmos DB. Este artigo descreve os desafios envolvidos na migração de dados de grande escala para o Azure Cosmos DB e apresenta-lhe a ferramenta que o ajuda com os desafios e que migra os dados para o Azure Cosmos DB. Neste caso prático, o cliente utilizou a API SQL do Cosmos DB.  

Antes de migrar toda a carga de trabalho para Azure Cosmos DB, pode migrar um subconjunto de dados para validar alguns dos aspetos como a escolha chave de partição, desempenho de consulta e modelação de dados. Depois de validar a prova de conceito, pode mover toda a carga de trabalho para Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Ferramentas para a migração de dados 

As estratégias de migração da Azure Cosmos DB diferem atualmente com base na escolha da API e na dimensão dos dados. Para migrar conjuntos de dados mais pequenos – para validar modelação de dados, desempenho de consulta, escolha de chaves de partição, etc. – pode escolher [o](import-data.md) [conector Azure Cosmos DB da Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Se você está familiarizado com Spark, você também pode optar por usar o [conector Azure Cosmos DB Spark](spark-connector.md) para migrar dados.

## <a name="challenges-for-large-scale-migrations"></a>Desafios para migrações em larga escala 

As ferramentas existentes para a migração de dados para a Azure Cosmos DB têm algumas limitações que se tornam especialmente evidentes em larga escala:

 * **Capacidades limitadas de escala**: Para migrar terabytes de dados para a Azure Cosmos DB o mais rapidamente possível, e para consumir eficazmente toda a produção a fortalhada, os clientes da migração devem ter a capacidade de escalar indefinidamente.  

* **Falta de rastreio e de verificação de progressos**: É importante acompanhar o progresso da migração e ter um ponto de verificação enquanto migra grandes conjuntos de dados. Caso contrário, qualquer erro que ocorra durante a migração irá parar a migração, e você tem que iniciar o processo do zero. Não seria produtivo reiniciar todo o processo de migração quando 99% do processo já está concluído.  

* **Falta de fila de letras mortas**: Dentro de grandes conjuntos de dados, em alguns casos pode haver problemas com partes dos dados de origem. Além disso, pode haver problemas transitórios com o cliente ou a rede. Qualquer um destes casos não deve fazer com que toda a migração falhe. Embora a maioria das ferramentas de migração tenha capacidades robustas de retenção que se protegem contra questões intermitentes, nem sempre é suficiente. Por exemplo, se menos de 0,01% dos documentos de dados de origem forem superiores a 2 MB de tamanho, fará com que o documento escreva para falhar no Azure Cosmos DB. Idealmente, é útil que a ferramenta de migração persista estes documentos "falhados" para outra fila de letras mortas, que pode ser processada após a migração. 

Muitas destas limitações estão a ser corrigidas para ferramentas como a fábrica de Dados Azure, serviços de migração de dados Azure. 

## <a name="custom-tool-with-bulk-executor-library"></a>Ferramenta personalizada com biblioteca de executor a granel 

Os desafios descritos na secção acima, podem ser resolvidos usando uma ferramenta personalizada que pode ser facilmente dimensionada em vários casos e é resistente a falhas transitórias. Além disso, a ferramenta personalizada pode parar e retomar a migração em vários pontos de verificação. A Azure Cosmos DB já fornece a biblioteca de [executor a granel](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) que incorpora algumas destas funcionalidades. Por exemplo, a biblioteca de executor a granel já tem a funcionalidade de lidar com erros transitórios e pode escalar fios num único nó para consumir cerca de 500 K RUs por nó. A biblioteca de executor a granel também divide o conjunto de dados de origem em micro-lotes que são operados independentemente como uma forma de checkpoint.  

A ferramenta personalizada utiliza a biblioteca de executor a granel e suporta a escala em vários clientes e para rastrear erros durante o processo de ingestão. Para utilizar esta ferramenta, os dados de origem devem ser divididos em ficheiros distintos no Azure Data Lake Storage (ADLS) para que diferentes trabalhadores da migração possam pegar em cada ficheiro e ingerê-los em Azure Cosmos DB. A ferramenta personalizada faz uso de uma recolha separada, que armazena metadados sobre o progresso da migração para cada ficheiro de origem individual em ADLS e rastreia quaisquer erros associados a eles.  

A imagem a seguir descreve o processo de migração utilizando esta ferramenta personalizada. A ferramenta está a funcionar num conjunto de máquinas virtuais, e cada máquina virtual consulta a recolha de rastreio em Azure Cosmos DB para adquirir um arrendamento numa das divisórias de dados de origem. Uma vez feito isto, a partição de dados de origem é lida pela ferramenta e ingerida no Azure Cosmos DB utilizando a biblioteca do executor a granel. Em seguida, a recolha de rastreio é atualizada para registar o progresso da ingestão de dados e quaisquer erros encontrados. Depois de uma partição de dados ser processada, a ferramenta tenta consultar a próxima partição de origem disponível. Continua a processar a próxima partição de origem até que todos os dados são migrados. O código fonte da ferramenta está disponível [aqui.](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Configuração da ferramenta de migração" border="false":::
 

 

A recolha de rastreio contém documentos como mostrado no exemplo seguinte. Verá tais documentos um para cada partição nos dados de origem.  Cada documento contém os metadados para a partição de dados de origem, tais como a sua localização, estado de migração e erros (se houver):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Pré-requisitos para a migração de dados 

Antes do início da migração de dados, existem alguns pré-requisitos a considerar:  

#### <a name="estimate-the-data-size"></a>Estimar o tamanho dos dados:  

O tamanho dos dados de origem pode não ser exatamente o mapa para o tamanho dos dados em Azure Cosmos DB. Alguns documentos de amostra da fonte podem ser inseridos para verificar o seu tamanho de dados em Azure Cosmos DB. Dependendo do tamanho do documento da amostra, o tamanho total dos dados em Azure Cosmos DB pós-migração, pode ser estimado. 

Por exemplo, se cada documento após a migração em Azure Cosmos DB for de cerca de 1 KB e se houver cerca de 60 mil milhões de documentos no conjunto de dados de origem, isso significaria que o tamanho estimado em Azure Cosmos DB seria de cerca de 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Recipientes pré-criados com RUs suficientes: 

Embora a Azure Cosmos DB escasseia o armazenamento automaticamente, não é aconselhável partir do menor tamanho do recipiente. Os contentores mais pequenos têm uma menor disponibilidade de produção, o que significa que a migração demoraria muito mais tempo a ser concluída. Em vez disso, é útil criar os recipientes com a dimensão final dos dados (tal como estimado no passo anterior) e certificar-se de que a carga de trabalho de migração está a consumir plenamente a produção a fortalida.  

No passo anterior. uma vez que o tamanho dos dados foi estimado em cerca de 60 TB, é necessário um recipiente de pelo menos 2,4 M RUs para acomodar todo o conjunto de dados.  

 

#### <a name="estimate-the-migration-speed"></a>Estimar a velocidade de migração: 

Partindo do princípio de que a carga de trabalho de migração pode consumir toda a produção a provisionada, a provisão em toda a região proporcionaria uma estimativa da velocidade de migração. Continuando o exemplo anterior, são necessários 5 RUs para escrever um documento de 1-KB à conta Azure Cosmos DB SQL API.  2,4 milhões de RUs permitiriam uma transferência de 480.000 documentos por segundo (ou 480 MB/s). Isto significa que a migração completa de 60 TB levará 125.000 segundos ou cerca de 34 horas.  

Caso pretenda que a migração esteja concluída dentro de um dia, deverá aumentar a produção prevista para 5 milhões de RUs. 

 

#### <a name="turn-off-the-indexing"></a>Desligue a indexação:  

Uma vez que a migração deve ser concluída o mais rapidamente possível, é aconselhável minimizar o tempo e as RUs gastas na criação de índices para cada um dos documentos ingeridos.  A Azure Cosmos DB indexa automaticamente todas as propriedades, vale a pena minimizar a indexação a alguns termos selecionados ou desligá-lo completamente para o curso da migração. Pode desligar a política de indexação do contentor alterando o IndexingMode para nenhum, como mostrado abaixo:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Depois de concluída a migração, pode atualizar a indexação.  

## <a name="migration-process"></a>Processo de migração 

Após a conclusão dos pré-requisitos, pode migrar dados com os seguintes passos:  

1. Primeiro importe os dados de origem para Azure Blob Storage. Para aumentar a velocidade da migração, é útil paralelizar entre divisórias de origem distintas. Antes de iniciar a migração, o conjunto de dados de origem deve ser dividido em ficheiros com tamanho de cerca de 200 MB.   

2. A biblioteca de executor a granel pode aumentar, para consumir 500.000 RUs num único cliente VM. Uma vez que a produção disponível é de 5 milhões de RUs, 10 Ubuntu 16.04 VMs (Standard_D32_v3) devem ser a provisionados na mesma região onde está localizada a sua base de dados Azure Cosmos. Deve preparar estes VMs com a ferramenta de migração e o ficheiro de definições.  

3. Faça o passo da fila numa das máquinas virtuais do cliente. Este passo cria a recolha de rastreio, que digitaliza o contentor ADLS e cria um documento de rastreio de progresso para cada um dos ficheiros de partição do conjunto de dados de origem.  

4. Em seguida, executar o passo de importação em todos os VMs cliente. Cada um dos clientes pode apropriar-se de uma partição de origem e ingerir os seus dados na Azure Cosmos DB. Uma vez concluída e o seu estado atualizado na coleção de rastreio, os clientes podem então consultar a próxima partição de origem disponível na coleção de rastreio.  

5. Este processo continua até que todo o conjunto de divisórias de origem foram ingeridas. Uma vez processadas todas as partições de origem, a ferramenta deve ser novamente repesccionada no modo de correção de erros na mesma recolha de rastreio. Este passo é necessário para identificar as divisórias de origem que devem ser re-processadas devido a erros.  

6. Alguns destes erros podem dever-se a documentos incorretos nos dados de origem. Estes devem ser identificados e corrigidos. Em seguida, deve repetir o passo de importação das divisórias falhadas para reingressá-las. 

Uma vez concluída a migração, pode validar que a contagem de documentos em Azure Cosmos DB é a mesma que a contagem de documentos na base de dados de origem. Neste exemplo, o tamanho total em Azure Cosmos DB acabou por ser de 65 terabytes. Após a migração, a indexação pode ser ligada seletivamente e as RUs podem ser reduzidas para o nível exigido pelas operações da carga de trabalho.

## <a name="contact-the-azure-cosmos-db-team"></a>Contacte a equipa DB da Azure Cosmos
Embora possa seguir este guia para migrar com sucesso grandes conjuntos de dados para Azure Cosmos DB, para migrações em larga escala, recomenda-se que contacte a equipa de produtos DB da Azure Cosmos para validar a modelação de dados e uma revisão geral da arquitetura. Com base no seu conjunto de dados e carga de trabalho, a equipa do produto também pode sugerir outras otimizações de desempenho e custos que possam ser aplicáveis a si. Para contactar a equipa DB da Azure Cosmos para obter assistência com migrações em larga escala, pode abrir um bilhete de apoio ao abrigo do tipo de problema "General Advisory" e do subtipo de problemas de "Grandes migrações (TB+)" como mostrado abaixo.

:::image type="content" source="./media/migrate-cosmosdb-data/supporttopic.png" alt-text="Configuração da ferramenta de migração":::


## <a name="next-steps"></a>Passos seguintes

* Saiba mais experimentando as aplicações de amostra que consomem a biblioteca de executor a granel em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* A biblioteca de executor a granel está integrada no conector Cosmos DB Spark, para saber mais, ver artigo [do conector Azure Cosmos DB Spark.](spark-connector.md)  
* Contacte a equipa de produtos da Azure Cosmos DB abrindo um bilhete de apoio ao abrigo do tipo de problema "General Advisory" e do subtipo de problemas de migração "Grandes (TB+)" para ajuda adicional com migrações em larga escala. 
