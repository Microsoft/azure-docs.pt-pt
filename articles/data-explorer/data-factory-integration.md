---
title: Integração do Azure Data Explorer com a Azure Data Factory
description: Neste tópico, integre o Azure Data Explorer com a Azure Data Factory para utilizar as atividades de cópia, procura e comando
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293628"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrar o Explorador de Dados Azure com a Azure Data Factory

[A Azure Data Factory](/azure/data-factory/) (ADF) é um serviço de integração de dados baseado na nuvem que lhe permite integrar diferentes lojas de dados e realizar atividades nos dados. A ADF permite-lhe criar fluxos de trabalho orientados para dados para orquestrar e automatizar o movimento de dados e a transformação de dados. O Azure Data Explorer é uma das lojas de [dados suportadas](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) na Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Atividades da Azure Data Factory para o Azure Data Explorer

Várias integrações com a Azure Data Factory estão disponíveis para utilizadores do Azure Data Explorer:

### <a name="copy-activity"></a>Atividade Copiar
 
A atividade da Azure Data Factory Copy é utilizada para transferir dados entre lojas de dados. O Azure Data Explorer é suportado como fonte, onde os dados são copiados do Azure Data Explorer para qualquer loja de dados suportada, e um lavatório, onde os dados são copiados de qualquer loja de dados suportada para o Azure Data Explorer. Para mais informações, consulte [os dados de cópia de ou para o Azure Data Explorer utilizando](/azure/data-factory/connector-azure-data-explorer)a Azure Data Factory . e para um walk-through detalhado ver [dados de carga da Azure Data Factory para o Azure Data Explorer](data-factory-load-data.md).
O Azure Data Explorer é suportado pelo Azure IR (Integration Runtime), utilizado quando os dados são copiados dentro do Azure, e o IR auto-hospedado, utilizado na cópia de dados de/para lojas de dados localizadas no local ou numa rede com controlo de acesso, como uma Rede Virtual Azure. Para mais informações, consulte [qual ir a utilizar](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Ao utilizar a atividade de cópia e criar um **Serviço Linked** ou um **Dataset,** selecione a loja de dados **Azure Data Explorer (Kusto)** e não a antiga loja de dados **Kusto**.  

### <a name="lookup-activity"></a>Atividade de procura
 
A atividade de lookup é usada para executar consultas no Azure Data Explorer. O resultado da consulta será devolvido como a saída da atividade de Lookup, e pode ser utilizado na próxima atividade no oleoduto, conforme descrito na [documentação de ADF Lookup](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Além do limite de tamanho da resposta de 5.000 linhas e 2 MB, a atividade também tem um limite de tempo limite de consulta de 1 hora.

### <a name="command-activity"></a>Atividade de comando

A atividade do Comando permite a execução de [comandos](/azure/kusto/concepts/#control-commands)de controlo do Explorador de Dados Azure . Ao contrário das consultas, os comandos de controlo podem potencialmente modificar dados ou metadados. Alguns dos comandos de controlo são direcionados para ingerir dados `.ingest`no `.set-or-append`Azure Data Explorer, utilizando comandos como ou `.export`) ou copiar dados do Azure Data Explorer para lojas de dados externas utilizando comandos como .
Para um walk-through detalhado da atividade do comando, consulte a utilização da atividade de comando da [Azure Data Factory para executar comandos](data-factory-command-activity.md)de controlo do Azure Data Explorer .  Usar um comando de controlo para copiar dados pode, por vezes, ser uma opção mais rápida e barata do que a atividade copy. Para determinar quando utilizar a atividade do Comando contra a atividade da Cópia, consulte [a selecione entre as atividades de Cópia e Comando ao copiar dados](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Copiar a granel a partir de um modelo de base de dados

A [Cópia a granel de uma base de dados para o Azure Data Explorer utilizando o modelo Azure Data Factory](data-factory-template.md) é um pipeline pré-definido da Fábrica de Dados Azure. O modelo é usado para criar muitos oleodutos por base de dados ou por tabela para uma cópia de dados mais rápida. 

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento 

Os fluxos de dados de [mapeamento da Azure Data Factory](/azure/data-factory/concepts-data-flow-overview) são transformações de dados projetadas visualmente que permitem aos engenheiros de dados desenvolver lógica de transformação de dados gráficos sem escrever código. Para criar um fluxo de dados e ingerir dados ao Azure Data Explorer, utilize o seguinte método:

1. Criar o fluxo de dados de [mapeamento.](/azure/data-factory/data-flow-create)
1. [Exportar os dados para Azure Blob.](/azure/data-factory/data-flow-sink) 
1. Defina a atividade de cópia [da Rede de Eventos](/azure/data-explorer/ingest-data-event-grid) ou [ADF](/azure/data-explorer/data-factory-load-data) para ingerir os dados ao Azure Data Explorer.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Selecione entre as atividades do Comando copy e Azure Data Explorer quando copiar dados 

Esta secção irá ajudá-lo a selecionar a atividade correta para as suas necessidades de cópia de dados.

Ao copiar dados de ou para o Azure Data Explorer, existem duas opções disponíveis na Azure Data Factory:
* Atividade de cópia.
* Atividade do Comando Azure Data Explorer, que executa um dos comandos de controlo que transferem dados no Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Copiar dados do Azure Data Explorer
  
Pode copiar dados do Azure Data Explorer [`.export`](/azure/kusto/management/data-export/) utilizando a atividade de cópia ou o comando. O `.export` comando executa uma consulta, e depois exporta os resultados da consulta. 

Consulte a tabela seguinte para uma `.export` comparação da atividade da Cópia e do comando para copiar dados do Azure Data Explorer.

| | Atividade Copiar | .comando de exportação |
|---|---|---|
| **Descrição do fluxo** | A ADF executa uma consulta sobre Kusto, processa o resultado e envia-o para a loja de dados alvo. <br>**(ADX > ADF > loja de dados do lavatório)** | A ADF `.export` envia um comando de controlo para o Azure Data Explorer, que executa o comando, e envia os dados diretamente para a loja de dados alvo. <br>**(ADX > loja de dados afundada)** |
| **Lojas de dados-alvo suportadas** | Uma grande variedade de lojas de [dados suportadas](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Blob Azure, Base de Dados SQL |
| **Desempenho** | Centralizado | <ul><li>Distribuídos (predefinidos), dados de exportação de vários nódosos simultaneamente</li><li>Mais rápido e eficiente COGS.</li></ul> |
| **Limites do servidor** | [Os limites](/azure/kusto/concepts/querylimits) de consulta podem ser prolongados/desativados. Por predefinição, as consultas da ADF contêm: <ul><li>Limite de tamanho de 500.000 registos ou 64 MB.</li><li>Limite de tempo de 10 minutos.</li><li>`noTruncation`definido para falso.</li></ul> | Por predefinição, alarga ou desativa os limites de consulta: <ul><li>Os limites de tamanho estão desativados.</li><li>O tempo de tempo do servidor é prolongado para 1 hora.</li><li>`MaxMemoryConsumptionPerIterator`e `MaxMemoryConsumptionPerQueryPerNode` são estendidos ao máximo (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Se o seu destino de cópia for `.export` uma das lojas de dados suportadas pelo comando, `.export` e se nenhuma das funcionalidades de atividade da Cópia for crucial para as suas necessidades, selecione o comando.

### <a name="copying-data-to-azure-data-explorer"></a>Copiar dados para o Azure Data Explorer

Pode copiar dados para o Azure Data Explorer utilizando a atividade de cópia ou `.set-or-replace` `.set`comandos de ingestão, tais como ingerir a partir [de consulta](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, , `.replace)`, e [ingerir a partir do armazenamento](/azure/kusto/management/data-ingestion/ingest-from-storage) ).`.ingest` 

Consulte a tabela seguinte para uma comparação da atividade da Cópia e comandos de ingestão para copiar dados ao Azure Data Explorer.

| | Atividade Copiar | Ingest de consulta<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Ingestão a partir do armazenamento <br> `.ingest` |
|---|---|---|---|
| **Descrição do fluxo** | A ADF obtém os dados da loja de dados de origem, converte-os num formato tabular, e faz as alterações necessárias ao mapeamento do esquema. Em seguida, a ADF envia os dados para as bolhas Azure, divide-os em pedaços e, em seguida, descarrega as bolhas para ingerir na tabela ADX. <br> (Loja de**dados de origem > ADF > blobs Azure > ADX**) | Estes comandos podem executar uma `.show` consulta ou um comando, e ingerir os resultados da consulta numa tabela **(ADX > ADX**). | Este comando ingere dados numa tabela "puxando" os dados de um ou mais artefactos de armazenamento em nuvem. |
| **Lojas de dados de origem suportada** |  [variedade de opções](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (utilizando o plugin sql_request), cosmos (utilizando o plugin cosmosdb_sql_request) e qualquer outra loja de dados que forneça APIs HTTP ou Python. | Sistema de ficheiros, Armazenamento De Blob Azure, ADLS Gen 1, ADLS Gen 2 |
| **Desempenho** | As ingestão são em fila e geridas, o que garante ingestão de pequenadimensão e assegura uma elevada disponibilidade, proporcionando equilíbrio de carga, tentativas e manuseamento de erros. | <ul><li>Esses comandos não foram concebidos para importar dados de grande volume.</li><li>Funciona como esperado e mais barato. Mas para cenários de produção e quando as taxas de tráfego e os tamanhos de dados são grandes, use a atividade Copy.</li></ul> |
| **Limites do servidor** | <ul><li>Sem limite de tamanho.</li><li>Limite máximo: 1 hora por bolha ingerida. |<ul><li>Há apenas um limite de tamanho na parte da consulta, `noTruncation=true`que pode ser ignorado especificando .</li><li>Limite máximo: 1 hora.</li></ul> | <ul><li>Sem limite de tamanho.</li><li>Limite máximo: 1 hora.</li></ul>|

> [!TIP]
> * Ao copiar dados da ADF para o `ingest from query` Azure Data Explorer, utilize os comandos.  
> * Para grandes conjuntos de dados (>1GB), utilize a atividade Copy.  

## <a name="required-permissions"></a>Permissões obrigatórias

A tabela seguinte enumera as permissões necessárias para várias etapas na integração com a Azure Data Factory.

| Passo | Operação | Nível mínimo de permissões | Notas |
|---|---|---|---|
| **Criar um Serviço Linked** | Navegação na base de dados | *visualizador de bases de dados* <br>O utilizador de entrada em sessão que utilize a ADF deve ser autorizado a ler metadados de base de dados. | O utilizador pode fornecer o nome da base de dados manualmente. |
| | Testar Ligação | *monitor de base de dados* ou *ingestor de tabela* <br>O diretor de serviço deve `.show` ser autorizado a executar comandos de nível de base de dados ou ingestão de nível de tabela. | <ul><li>TestConnection verifica a ligação ao cluster e não à base de dados. Pode ter sucesso mesmo que a base de dados não exista.</li><li>Permissões de administração de mesa não são suficientes.</li></ul>|
| **Criação de um Conjunto de Dados** | Navegação à mesa | *monitor de base de dados* <br>O utilizador registado no utilizador que utilize ADF `.show` deve ser autorizado a executar comandos de nível de base de dados. | O utilizador pode fornecer o nome da mesa manualmente.|
| **Criação de um Conjunto de Dados** ou Atividade de **Cópia** | Dados de pré-visualização | *visualizador de bases de dados* <br>O diretor de serviço deve ser autorizado a ler metadados de base de dados. | | 
|   | Esquema de importação | *visualizador de bases de dados* <br>O diretor de serviço deve ser autorizado a ler metadados de base de dados. | Quando a ADX é a fonte de uma cópia tabular-a-tabular, a ADF importará o esquema automaticamente, mesmo que o utilizador não tenha importado explicitamente o esquema. |
| **ADX como Pia** | Criar um mapeamento de coluna de nome próprio | *monitor de base de dados* <br>O diretor de serviço deve `.show` ser autorizado a executar comandos de nível de base de dados. | <ul><li>Todas as operações obrigatórias funcionarão com *a ingestorde tabela.*</li><li> Algumas operações opcionais podem falhar.</li></ul> |
|   | <ul><li>Crie um mapeamento CSV na mesa</li><li>Largue o mapeamento</li></ul>| *ingestor de tabela* ou *administração* de base de dados <br>O diretor de serviço deve ser autorizado a fazer alterações numa tabela. | |
|   | Ingerir dados | *ingestor de tabela* ou *administração* de base de dados <br>O diretor de serviço deve ser autorizado a fazer alterações numa tabela. | | 
| **ADX como fonte** | Executar consulta | *visualizador de bases de dados* <br>O diretor de serviço deve ser autorizado a ler metadados de base de dados. | |
| **Comando Kusto** | | De acordo com o nível de permissões de cada comando. |

## <a name="performance"></a>Desempenho 

Se o Azure Data Explorer for a fonte e utilizar a atividade de Lookup, copy ou command que contenha uma consulta onde, consulte [as melhores práticas](/azure/kusto/query/best-practices) de consulta para informações de desempenho e [documentação ADF para a atividade](/azure/data-factory/copy-activity-performance)de cópia .
  
Esta secção aborda a utilização da atividade de cópia onde o Azure Data Explorer é o lavatório. O resultado estimado para o afundatório Azure Data Explorer é de 11-13 MBps. A tabela que se segue detalha os parâmetros que influenciam o desempenho da pia do Azure Data Explorer.

| Parâmetro | Notas |
|---|---|
| **Componentes proximidade geográfica** | Coloque todos os componentes na mesma região:<ul><li>fonte e lojas de dados afundadas.</li><li>Tempo de execução de integração ADF.</li><li>O seu cluster ADX.</li></ul>Certifique-se de que, pelo menos, o seu tempo de integração está na mesma região que o seu cluster ADX. |
| **Número de DIUs** | 1 VM por cada 4 DIUs utilizados pela ADF. <br>Aumentar os DIUs só ajudará se a sua fonte for uma loja baseada em ficheiros com vários ficheiros. Cada VM processará então um ficheiro diferente em paralelo. Portanto, copiar um único ficheiro grande terá uma latência maior do que copiar vários ficheiros menores.|
|**Quantidade e SKU do seu cluster ADX** | Um elevado número de nós ADX aumentará o tempo de processamento da ingestão.|
| **Paralelismo** | Para copiar uma quantidade muito grande de dados de uma base de dados, dividir os seus dados e, em seguida, usar um loop ForEach que copia cada partição paralelamente ou use a cópia a granel da Base de Dados para o Modelo de Explorador de [Dados Azure](data-factory-template.md). **Nota: Definições** > **O grau de paralelismo** na atividade da Cópia não é relevante para o ADX. |
| **Complexidade do processamento de dados** | A latência varia de acordo com o formato de ficheiro de origem, o mapeamento da coluna e a compressão.|
| **O VM executando o seu tempo de funcionamento de integração** | <ul><li>Para cópia azure, VMs ADF e skus da máquina não podem ser alterados.</li><li> Para a cópia do Azure, determine que o VM que acolhe o seu IR auto-hospedado é suficientemente forte.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Dicas e armadilhas comuns

### <a name="monitor-activity-progress"></a>Monitorizar o progresso da atividade

* Ao monitorizar o progresso da atividade, a propriedade *escrita* data pode ser muito maior do que a propriedade *de leitura* de Dados porque a leitura de *Dados* é calculada de acordo com o tamanho do ficheiro binário, enquanto os *Dados escritos* são calculados de acordo com o tamanho da memória, depois de os dados serem desserializados e descomprimidos.

* Ao monitorizar o progresso da atividade, pode ver que os dados são escritos para o lavatório Azure Data Explorer. Ao consultar a tabela Azure Data Explorer, vê que os dados ainda não chegaram. Isto porque há duas fases para copiar para o Azure Data Explorer. 
    * A primeira fase lê os dados de origem, divide-os em pedaços de 900 MB, e envia cada pedaço para um Azure Blob. A primeira fase é vista pela visão de progresso da atividade da ADF. 
    * A segunda fase começa assim que todos os dados são enviados para O Blobs Azure. Os nódos os nódosos do motor Azure Data Explorer descarregam as bolhas e ingestão os dados na mesa de sumidouro. Os dados são então vistos na sua tabela Azure Data Explorer.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Falha na intenção de ficheiros CSV devido a fuga imprópria

O Azure Data Explorer espera que os ficheiros CSV se alinhem com o [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Espera:
* Os campos que contenham caracteres que exijam escapar (como " e novas linhas) devem começar e terminar com um **"** personagem, sem espaço branco. Todos **os "** caracteres *dentro* do campo são escapados usando um personagem duplo **"** **(").** Por exemplo, _"Hello, "World""_ é um ficheiro CSV válido com um único disco com uma única coluna ou campo com o conteúdo _Hello, "World"._
* Todos os registos do ficheiro devem ter o mesmo número de colunas e campos.

A Azure Data Factory permite o caráter backslash (escape). Se gerar um ficheiro CSV com um personagem backslash utilizando a Azure Data Factory, a ingestão do ficheiro ao Azure Data Explorer falhará.

#### <a name="example"></a>Exemplo

Os seguintes valores de texto: Olá, "Mundo"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

Deve aparecer num ficheiro CSV adequado da seguinte forma: "Olá, "Mundo""<br/>
"ABC DEF"<br/>
""ABC DEF"<br/>
""ABC\D""EF"<br/>

Ao usar o personagem de fuga padrão (backslash), o seguinte CSV \"não\"funcionará com o Azure Data Explorer: "Olá, Mundo"<br/>
"ABC DEF"<br/>
"ABC\"DEF"<br/>
\""ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Objetos JSON aninhados

Ao copiar um ficheiro JSON para o Azure Data Explorer, note que:
* As matrizes não são apoiadas.
* Se a sua estrutura JSON contiver tipos de dados de objetos, a Azure Data Factory irá aplainar os itens infantis do objeto e tentar mapear cada item de criança para uma coluna diferente na tabela Azure Data Explorer. Se quiser que todo o objeto seja mapeado para uma única coluna no Azure Data Explorer:
    * Insere toda a linha JSON numa única coluna dinâmica no Azure Data Explorer.
    * Editar manualmente a definição do pipeline utilizando o editor JSON da Azure Data Factory. Em **Mapeamentos**
       * Remova os mapeamentos múltiplos que foram criados para cada item infantil e adicione um único mapeamento que mapeie o seu tipo de objeto à sua coluna de mesa.
       * Depois do suporte quadrado de fecho, adicione uma vírnia seguida de:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Especificar Propriedades Adicionais ao copiar para o Azure Data Explorer

> [!NOTE]
> Esta funcionalidade encontra-se atualmente disponível através da edição manual da carga útil JSON. 

Adicione uma única linha sob a secção "pia" da atividade da cópia da seguinte forma:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Escapar do valor pode ser complicado. Utilize o seguinte código de corte como referência:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

O valor impresso:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Passos seguintes

* Saiba como [copiar dados para o Azure Data Explorer utilizando](data-factory-load-data.md)a Azure Data Factory .
* Saiba mais sobre a utilização do [modelo azure data factory para cópia a granel da base de dados para o Azure Data Explorer](data-factory-template.md).
* Saiba utilizar a atividade de comando da [Azure Data Factory para executar comandos](data-factory-command-activity.md)de controlo do Azure Data Explorer .
* Saiba mais sobre as consultas do [Azure Data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.



