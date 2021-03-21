---
title: Atividade de cópia na Azure Data Factory
description: Conheça a atividade copy na Azure Data Factory. Pode usá-lo para copiar dados de uma loja de dados de origem suportada para uma loja de dados de sumidouro suportado.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387908"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia na Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Data Factory que está a usar:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Na Azure Data Factory, pode utilizar a atividade Copy para copiar dados entre lojas de dados localizadas no local e na nuvem. Depois de copiar os dados, pode utilizar outras atividades para os transformar e analisar ainda mais. Também pode utilizar a atividade Copy para publicar resultados de transformação e análise para inteligência empresarial (BI) e consumo de aplicações.

![O papel da atividade copy](media/copy-activity-overview/copy-activity.png)

A atividade Copy é executada num [período de execução de integração](concepts-integration-runtime.md). Pode utilizar diferentes tipos de tempos de integração para diferentes cenários de cópia de dados:

* Ao copiar dados entre duas lojas de dados que são acessíveis ao público através da internet a partir de qualquer IP, pode utilizar o tempo de execução da integração do Azure para a atividade da cópia. Este tempo de integração é seguro, fiável, escalável e [globalmente disponível.](concepts-integration-runtime.md#integration-runtime-location)
* Quando está a copiar dados de e para lojas de dados que estão localizados no local ou numa rede com controlo de acesso (por exemplo, uma rede virtual Azure), precisa de configurar um tempo de integração auto-hospedado.

Um tempo de integração deve ser associado a cada fonte e afundar a loja de dados. Para obter informações sobre como a atividade Copy determina qual o tempo de execução da integração a utilizar, consulte [determinar qual o IR a utilizar](concepts-integration-runtime.md#determining-which-ir-to-use).

Para copiar dados de uma fonte para um lavatório, o serviço que executa a atividade Copy executa estes passos:

1. Lê dados de uma loja de dados de origem.
2. Executa serialização/deserialização, compressão/descompressão, mapeamento de colunas, e assim por diante. Executa estas operações com base na configuração do conjunto de dados de entrada, conjunto de dados de saída e atividade de Cópia.
3. Escreve dados para a loja de dados da pia/destino.

![Descrição geral da atividade de cópia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Lojas e formatos de dados suportados

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de ficheiro suportados

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Pode utilizar a atividade Copy para copiar ficheiros em vez de duas lojas de dados baseadas em ficheiros, caso em que os dados são copiados de forma eficiente sem qualquer serialização ou deserialização. Além disso, também pode analisar ou gerar ficheiros de um determinado formato, por exemplo, pode executar o seguinte:

* Copie os dados de uma base de dados do SQL Server e escreva para Azure Data Lake Storage Gen2 em formato Parquet.
* Copie ficheiros em formato de texto (CSV) a partir de um sistema de ficheiros no local e escreva para o armazenamento de Azure Blob em formato Avro.
* Copie ficheiros fechados de um sistema de ficheiros no local, descomprimir-os no voo e escreva ficheiros extraídos para a Azure Data Lake Storage Gen2.
* Copie os dados no formato Gzip comprimed text (CSV) a partir do armazenamento Azure Blob e escreva-os para a Base de Dados Azure SQL.
* Muitas mais atividades que requerem serialização/deserialização ou compressão/descompressão.

## <a name="supported-regions"></a>Regiões suportadas

O serviço que permite a atividade Copy está disponível globalmente nas regiões e geografias listadas em [locais de execução de integração Azure.](concepts-integration-runtime.md#integration-runtime-location) A topologia globalmente disponível garante um movimento de dados eficiente que geralmente evita o lúpulo entre regiões. Consulte [produtos por região](https://azure.microsoft.com/regions/#services) para verificar a disponibilidade de Data Factory e o movimento de dados numa região específica.

## <a name="configuration"></a>Configuração

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Em geral, para utilizar a atividade copy na Azure Data Factory, é necessário:

1. **Crie serviços ligados para a loja de dados de origem e para a loja de dados da pia.** Pode encontrar a lista de conectores suportados nas [lojas de dados suportadas e](#supported-data-stores-and-formats) na secção de formatos deste artigo. Consulte a secção "Propriedades de serviço ligadas" do artigo do conector para obter informações de configuração e propriedades suportadas. 
2. **Crie conjuntos de dados para a fonte e afunde.** Consulte as secções "Propriedades do Conjunto de Dados" dos artigos de conector de origem e de sumidouro para obter informações de configuração e propriedades suportadas.
3. **Criar um oleoduto com a atividade Copy.** A secção seguinte dá um exemplo.

### <a name="syntax"></a>Syntax

O modelo seguinte de uma atividade copy contém uma lista completa de propriedades suportadas. Especifique os que se encaixam no seu cenário.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Necessário? |
|:--- |:--- |:--- |
| tipo | Para uma atividade copy, definir para `Copy` | Yes |
| entradas | Especifique o conjunto de dados que criou que aponta para os dados de origem. A atividade Copy suporta apenas uma única entrada. | Yes |
| saídas | Especifique o conjunto de dados que criou que aponta para os dados do lavatório. A atividade Copy suporta apenas uma saída. | Yes |
| typeProperties | Especifique propriedades para configurar a atividade Copy. | Yes |
| source | Especifique o tipo de fonte de cópia e as propriedades correspondentes para a recuperação de dados.<br/>Para obter mais informações, consulte a secção "Copiar propriedades da atividade" no artigo de conector listado nas [lojas e formatos de dados suportados.](#supported-data-stores-and-formats) | Yes |
| pia | Especifique o tipo de pia de cópia e as propriedades correspondentes para a escrita de dados.<br/>Para obter mais informações, consulte a secção "Copiar propriedades da atividade" no artigo de conector listado nas [lojas e formatos de dados suportados.](#supported-data-stores-and-formats) | Yes |
| tradutor | Especifique mapeamentos de colunas explícitas de fonte para afundar. Esta propriedade aplica-se quando o comportamento padrão da cópia não satisfaz as suas necessidades.<br/>Para obter mais informações, consulte [o mapeamento de Schema na atividade de cópia.](copy-activity-schema-and-type-mapping.md) | No |
| dataIntegrationUnnits | Especifique uma medida que represente a quantidade de energia que o tempo de execução da integração do [Azure](concepts-integration-runtime.md) utiliza para a cópia de dados. Estas unidades eram anteriormente conhecidas como Unidades de Movimento de Dados em Nuvem (DMU). <br/>Para mais informações, consulte [unidades de integração de dados.](copy-activity-performance-features.md#data-integration-units) | No |
| paralelosCopias | Especifique o paralelismo que pretende que a atividade Copy utilize ao ler dados da fonte e escrever dados para a pia.<br/>Para obter mais informações, consulte [a cópia paralela.](copy-activity-performance-features.md#parallel-copy) | No |
| preservar | Especificar se deve conservar metadados/ACLs durante a cópia de dados. <br/>Para obter mais informações, consulte [Preserve metadados.](copy-activity-preserve-metadata.md) |No |
| permitir A marcação<br/>stagingSettings | Especificar se deve encenar os dados provisórios no armazenamento blob em vez de copiar diretamente os dados da fonte para o afundamento.<br/>Para obter informações sobre cenários úteis e detalhes de configuração, consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redireccionamentosIncompatíveis| Escolha como lidar com linhas incompatíveis quando copiar dados da fonte para afundar.<br/>Para obter mais informações, consulte [a tolerância à falha.](copy-activity-fault-tolerance.md) | No |

## <a name="monitoring"></a>Monitorização

Pode monitorizar a atividade copy executada na Azure Data Factory tanto visual como programáticamente. Para mais informações, consulte [a atividade de cópia do Monitor](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Cópia incremental

A Data Factory permite-lhe copiar gradualmente dados delta de uma loja de dados de origem para uma loja de dados de sumidouros. Para mais informações, consulte [Tutorial: Copiar gradualmente os dados.](tutorial-incremental-copy-overview.md)

## <a name="performance-and-tuning"></a>Desempenho e otimização

A experiência [de monitorização da atividade](copy-activity-monitoring.md) de cópia mostra-lhe as estatísticas de desempenho de cópia para cada uma das suas atividades executadas. O [guia de desempenho e escalabilidade](copy-activity-performance.md) da atividade copy descreve factores-chave que afetam o desempenho do movimento de dados através da atividade Copy na Azure Data Factory. Também enumera os valores de desempenho observados durante os testes e discute como otimizar o desempenho da atividade Copy.

## <a name="resume-from-last-failed-run"></a>Resumo da última corrida falhada

Os suportes de atividade de cópia retomam a partir da última execução falhada quando copia grande dimensão de ficheiros como está com o formato binário entre lojas baseadas em ficheiros e opta por preservar a hierarquia da pasta/ficheiro de fonte para afundar, por exemplo, para migrar dados do Amazon S3 para a Azure Data Lake Storage Gen2. Aplica-se aos seguintes conectores baseados em ficheiros: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md)Gen2 , Azure File Storage , File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md)e [SFTP](connector-sftp.md).

Pode alavancar o currículo da atividade da cópia de duas formas:

- **Redação do nível de atividade:** Pode definir a contagem de repetição na atividade de cópia. Durante a execução do gasoduto, se esta atividade de cópia falhar, a próxima repetição automática começará a partir do ponto de falha do último julgamento.
- **Repetição da atividade falhada:** Após a conclusão da execução do gasoduto, também pode desencadear uma repetição da atividade falhada na visão de monitorização do UI ADF ou programáticamente. Se a atividade falhada for uma atividade de cópia, o gasoduto não só se repetirá desta atividade, como também retomará do ponto de insucesso da execução anterior.

    ![Resumo de cópia](media/copy-activity-overview/resume-copy.png)

Alguns pontos a notar:

- O currículo acontece ao nível dos ficheiros. Se a atividade de cópia falhar ao copiar um ficheiro, na próxima execução, este ficheiro específico será re-copiado.
- Para que o currículo funcione corretamente, não altere as definições de atividade da cópia entre as repetições.
- Ao copiar dados do Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 e Google Cloud Storage, a atividade de cópia pode ser retomada a partir de um número arbitrário de ficheiros copiados. Enquanto para o resto dos conectores baseados em ficheiros como fonte, atualmente a atividade de cópias suporta retomar a partir de um número limitado de ficheiros, geralmente na gama de dezenas de milhares e varia dependendo do comprimento dos caminhos de arquivo; ficheiros para além deste número serão re-copiados durante as repetições.

Para outros cenários que não a cópia de ficheiro binário, a repetição da atividade da cópia começa desde o início.

## <a name="preserve-metadata-along-with-data"></a>Preservar metadados juntamente com dados

Ao copiar dados de origem para afundar, em cenários como a migração de data lake, também pode optar por preservar os metadados e ACLs juntamente com dados que usam a atividade de cópia. Consulte [os metadados para](copy-activity-preserve-metadata.md) obter mais detalhes.

## <a name="schema-and-data-type-mapping"></a>Mapa de esquema e tipo de dados

Consulte [o Schema e o mapeamento do tipo de dados](copy-activity-schema-and-type-mapping.md) para obter informações sobre como a atividade copy mapeia os seus dados de origem para a pia.

## <a name="add-additional-columns-during-copy"></a>Adicione colunas adicionais durante a cópia

Além de copiar dados da loja de dados de origem para afundar, também pode configurar para adicionar colunas de dados adicionais para copiar junto ao lavatório. Por exemplo:

- Quando copiar a partir de uma fonte baseada em ficheiros, guarde o caminho relativo do ficheiro como uma coluna adicional para rastrear de que ficheiro os dados provêm.
- Duplique a coluna de origem especificada como outra coluna. 
- Adicione uma coluna com expressão ADF, para anexar variáveis do sistema ADF como o nome do gasoduto/iD do gasoduto, ou armazenar outro valor dinâmico da produção da atividade a montante.
- Adicione uma coluna com valor estático para satisfazer as suas necessidades de consumo a jusante.

Pode encontrar a seguinte configuração no separador fonte de origem da atividade da cópia. Também pode mapear essas colunas adicionais no [mapeamento de esquemas de](copy-activity-schema-and-type-mapping.md#schema-mapping) cópia como de costume, utilizando os nomes das colunas definidas. 

![Adicionar colunas adicionais na atividade de cópia](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Esta funcionalidade funciona com o mais recente modelo de conjunto de dados. Se não vir esta opção a partir da UI, tente criar um novo conjunto de dados.

Para configurá-lo programáticamente, adicione o `additionalColumns` imóvel na sua fonte de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| micos adicionais | Adicione colunas de dados adicionais para copiar para afundar.<br><br>Cada objeto sob a `additionalColumns` matriz representa uma coluna extra. O `name` define o nome da coluna, e o indica o valor de `value` dados dessa coluna.<br><br>Os valores de dados permitidos são:<br>- **`$$FILEPATH`** - uma variável reservada indica para armazenar o caminho relativo dos ficheiros de origem para o caminho da pasta especificado no conjunto de dados. Aplicar a fonte baseada em ficheiros.<br>- **`$$COLUMN:<source_column_name>`** - um padrão variável reservado indica duplicar a coluna de origem especificada como outra coluna<br>- **Expressão**<br>- **Valor estático** | No |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Auto criar mesas de pia

Ao copiar dados na base de dados SQL/Azure Synapse Analytics, se a tabela de destino não existir, a atividade de cópia suporta a sua criação automática com base nos dados de origem. Tem como objetivo ajudá-lo a começar rapidamente a carregar os dados e a avaliar a base de dados SQL/Azure Synapse Analytics. Após a ingestão de dados, pode rever e ajustar o esquema da tabela da pia de acordo com as suas necessidades.

Esta funcionalidade é suportada ao copiar dados de qualquer fonte para as seguintes lojas de dados de sumidouros. Pode encontrar a opção na *ui* (> *Copy activity sink>* a *opção > Tabela* – > *tabela de criação automática*, ou através de propriedade em `tableOption` função da atividade de cópia.

- [Base de Dados SQL do Azure](connector-azure-sql-database.md)
- [Exemplo gerido pela base de dados Azure SQL](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Criar mesas de pia](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Tolerância a falhas

Por predefinição, a atividade Copy para de copiar dados e retorna uma falha quando as linhas de dados de origem são incompatíveis com as linhas de dados do lavatório. Para que a cópia tenha sucesso, pode configurar a atividade Copy para saltar e registar as linhas incompatíveis e copiar apenas os dados compatíveis. Consulte [a tolerância à falha da atividade da cópia](copy-activity-fault-tolerance.md) para obter detalhes.

## <a name="data-consistency-verification"></a>Verificação da consistência dos dados

Ao mover dados de origem para loja de destino, a atividade de cópia da Azure Data Factory fornece uma opção para que você faça uma verificação adicional de consistência de dados para garantir que os dados não são apenas copiados com sucesso de fonte para loja de destino, mas também verificado como consistente entre a loja de origem e destino. Uma vez encontrados ficheiros inconsistentes durante o movimento de dados, pode abortar a atividade da cópia ou continuar a copiar o resto, permitindo que a definição de tolerância a falhas ignore ficheiros inconsistentes. Pode obter os nomes de ficheiros ignorados, permitindo a definição de registo de sessão na atividade de cópia. Consulte [a verificação da consistência dos dados na atividade da cópia](copy-activity-data-consistency.md) para obter mais detalhes.

## <a name="session-log"></a>Registo de sessão
Pode registar os nomes dos ficheiros copiados, o que pode ajudá-lo a garantir que os dados não são apenas copiados com sucesso da fonte para a loja de destino, como também consistentes entre a loja de origem e destino, revendo os registos de sessão de atividade de cópia. Consulte [a atividade de cópia de sessão](copy-activity-log.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes quickstarts, tutoriais e amostras:

- [Copiar dados de um local para outro local na mesma conta de armazenamento Azure Blob](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do armazenamento do Azure Blob para a Base de Dados Azure SQL](tutorial-copy-data-dot-net.md)
- [Copiar dados de uma base de dados do SQL Server para Azure](tutorial-hybrid-copy-powershell.md)
