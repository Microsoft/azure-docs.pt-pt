---
title: Copiar dados do SAP Business Warehouse via hub aberto
description: Saiba como copiar dados do SAP Business Warehouse (BW) via hub aberto para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 8048d64eccab26477b83031b96fe810796486668
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895575"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via hub aberto usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um SAP Business Warehouse (BW) via Open Hub. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, análise e diretrizes detalhadas.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este SAP Business Warehouse via conector de Hub aberto tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do SAP Business Warehouse via hub aberto para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do Hub aberto do SAP Business Warehouse dá suporte a:

- SAP Business Warehouse **versão 7, 1 ou superior (em uma pilha recente do pacote de suporte SAP lançada após o ano 2015)** .
- Copiar dados por meio da tabela local de destino de Hub aberto que está abaixo de pode ser DSO, InfoCube, multifornecedor, DataSource, etc.
- Copiar dados usando a autenticação básica.
- Conectando ao servidor de aplicativos.

## <a name="sap-bw-open-hub-integration"></a>Integração do hub de SAP BW Open 

[SAP BW serviço de Hub aberto](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma maneira eficiente de extrair dados de SAP BW. O diagrama a seguir mostra um dos fluxos típicos que os clientes têm em seu sistema SAP, caso em que os dados fluem do cubo SAP ECC-> PSA-> DSO->.

SAP BW OHD (Open Hub Destination) define o destino para o qual os dados SAP são retransmitidos. Todos os objetos com suporte do SAP Transferência de Dados Process (DTP) podem ser usados como fontes de dados de Hub aberto, por exemplo, DSO, InfoCube, DataSource, etc. Tipo de destino de Hub aberto-onde os dados retransmitidos são armazenados-podem ser tabelas de banco de dados (locais ou remotos) e arquivos simples. Este SAP BW Open Hub Connector dá suporte à cópia de dados da tabela local OHD em BW. Caso você esteja usando outros tipos, você pode se conectar diretamente ao banco de dados ou ao sistema de arquivos usando outros conectores.

![SAP BW Hub aberto](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração de Delta

O ADF SAP BW Open Hub Connector oferece duas propriedades opcionais: `excludeLastRequest` e `baseRequestId` que podem ser usadas para lidar com a carga Delta do Open Hub. 

- **excludeLastRequestId**: se os registros da última solicitação devem ser excluídos. O valor padrão é true. 
- **baseRequestId**: a ID da solicitação de carregamento Delta. Depois de definido, somente os dados com requestId maior do que o valor dessa propriedade serão recuperados. 

Em geral, a extração do SAP InfoProviders para Azure Data Factory (ADF) consiste em duas etapas: 

1. **Processo de transferência de dados de SAP BW (DTP)** Esta etapa copia os dados de um SAP BW Infoprovider para uma SAP BW tabela de Hub aberta 

1. **Cópia de dados do ADF** Nesta etapa, a tabela de Hub aberta é lida pelo conector do ADF 

![Fluxo de extração de Delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Na primeira etapa, um DTP é executado. Cada execução cria uma nova ID de solicitação do SAP. A ID da solicitação é armazenada na tabela de Hub aberta e, em seguida, é usada pelo conector do ADF para identificar o Delta. As duas etapas são executadas de forma assíncrona: o DTP é disparado pelo SAP e a cópia de dados do ADF é disparada por meio do ADF. 

Por padrão, o ADF não está lendo o último Delta da tabela de Hub aberta (a opção "excluir última solicitação" é verdadeira). Aqui, os dados no ADF não são 100% atualizados com os dados na tabela de Hub aberta (o último Delta está ausente). Em retorno, esse procedimento garante que nenhuma linha seja perdida causada pela extração assíncrona. Ele funciona bem mesmo quando o ADF está lendo a tabela de Hub aberto enquanto o DTP ainda está gravando na mesma tabela. 

Normalmente, você armazena a ID de solicitação máxima copiada na última execução pelo ADF em um armazenamento de dados de preparo (como o blob do Azure no diagrama acima). Portanto, a mesma solicitação não é lida uma segunda vez pelo ADF na execução subsequente. Enquanto isso, observe que os dados não são excluídos automaticamente da tabela de Hub aberta.

Para uma manipulação de Delta adequada, não é permitido ter IDs de solicitação de DTPs diferentes na mesma tabela de Hub aberta. Portanto, você não deve criar mais de um DTP para cada destino de Hub aberto (OHD). Ao precisar de extração completa e Delta do mesmo Infoprovider, você deve criar dois OHDs para o mesmo Infoprovider. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector de Hub aberto do SAP Business Warehouse, você precisa:

- Configure um Integration Runtime auto-hospedado com a versão 3,13 ou superior. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

- Baixe o  **[conector do SAP .net](https://support.sap.com/en/product/connectors/msnet.html) de 64 bits 3,0** do site da SAP e instale-o na máquina ir auto-hospedado. Ao instalar o, na janela etapas de instalação opcionais, certifique-se de selecionar a opção **instalar assemblies no GAC** , conforme mostrado na imagem a seguir. 

    ![Instalar o conector do SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário SAP que está sendo usado no conector Data Factory BW precisa ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a atividade "executar" do objeto de autorização "S_SDSAUTH".

- Crie o tipo de destino do Hub aberto do SAP como **tabela de banco de dados** com a opção "chave técnica" marcada.  Também é recomendável deixar a exclusão de dados da tabela como desmarcado, embora não seja necessário. Aproveite o DTP (executar ou integrar diretamente na cadeia de processo existente) para esterrar dados do objeto de origem (como cubo) que você escolheu para a tabela de destino de Hub aberto.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para obter uma explicação sobre como usar SAP BW conector de Hub aberto, consulte [carregar dados do SAP Business Warehouse (BW) usando Azure data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector de Hub aberto do SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado de Hub aberto do SAP Business Warehouse:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SapOpenHub** | Sim |
| servidor | Nome do servidor no qual reside a instância de SAP BW. | Sim |
| systemNumber | Número de sistema do sistema de SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| clientId | ID do cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| language | Idioma usado pelo sistema SAP. | Não (o valor padrão é **en**)|
| userName | Nome do usuário que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de SAP BW Open Hub.

Para copiar dados de e para SAP BW Open Hub, defina a propriedade Type do conjunto de dados como **SapOpenHubTable**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome do destino do Hub aberto para o qual copiar dados. | Sim |

Se você estivesse definindo `excludeLastRequest` e `baseRequestId` no DataSet, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo modelo na origem da atividade no futuro.

**Exemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo SAP BW fonte de Hub aberta.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW abrir o Hub como fonte

Para copiar dados de SAP BW Hub aberto, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **SapOpenHubSource**. | Sim |
| excludeLastRequest | Se os registros da última solicitação devem ser excluídos. | Não (o padrão é **true**) |
| baseRequestId | A ID da solicitação de carregamento Delta. Depois de definido, somente os dados com requestId **maior do que** o valor dessa propriedade serão recuperados.  | Não |

>[!TIP]
>Se a tabela de Hub aberto contiver apenas os dados gerados por uma única ID de solicitação, por exemplo, você sempre faz a carga completa e substitui os dados existentes na tabela, ou só executa o DTP uma vez para teste, lembre-se de desmarcar a opção "excludeLastRequest" para copiar a d saída do ATA.

Para acelerar o carregamento de dados, você pode definir [`parallelCopies`](copy-activity-performance.md#parallel-copy) na atividade de cópia para carregar dados de SAP BW Hub aberto em paralelo. Por exemplo, se você definir `parallelCopies` como quatro, Data Factory executar simultaneamente quatro chamadas RFC, e cada chamada RFC recuperará uma parte dos dados da tabela SAP BW Open Hub particionada pela ID da solicitação DTP e ID do pacote. Isso se aplica quando o número de ID de solicitação DTP + ID de pacote exclusiva é maior que o valor de `parallelCopies`. Ao copiar dados para o armazenamento de dados baseado em arquivo, ele também é recriado para gravar em uma pasta como vários arquivos (apenas especifique o nome da pasta); nesse caso, o desempenho é melhor do que gravar em um único arquivo.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para SAP BW Hub aberto

Ao copiar dados do SAP BW Hub aberto, os seguintes mapeamentos são usados de SAP BW tipos de dados para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de ABAP SAP | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| C (cadeia de caracteres) | String |
| I (Integer) | Int32 |
| F (float) | Double |
| D (Data) | String |
| T (hora) | String |
| P (BCD empacotado, moeda, Decimal, Qtd) | Decimal |
| N (NUMC) | String |
| X (binário e bruto) | String |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
