---
title: Copiar dados do SAP Business Warehouse via Open Hub
description: Saiba como copiar dados do SAP Business Warehouse (BW) via Open Hub para armazenar dados de sink suportados utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: b905c75e920577e46017caeb456f8237421086b2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421215"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via Open Hub utilizando a Azure Data Factory

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um Armazém De Negócios SAP (BW) via Open Hub. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para conhecer o suporte global da ADF no cenário de integração de dados DoSAP, consulte a integração de [dados do SAP utilizando o livro branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este Armazém De Negócios SAP via Conector Open Hub é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do SAP Business Warehouse via Open Hub para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP Business Warehouse Open Hub suporta:

- SAP Business Warehouse **versão 7.01 ou superior (num recente Pacote de Suporte SAP Stack lançado após o ano de 2015)**.
- Copiando dados através da tabela local Open Hub Destination que por baixo pode ser DSO, InfoCube, MultiProvider, DataSource, etc.
- Copiar dados utilizando a autenticação básica.
- Ligação ao Servidor de Aplicações.

## <a name="sap-bw-open-hub-integration"></a>Integração do Hub Aberto SAP BW 

[O Serviço SAP BW Open Hub](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma forma eficiente de extrair dados do SAP BW. O diagrama seguinte mostra um dos fluxos típicos que os clientes têm no seu sistema SAP, caso em que os dados do SAP ECC -> PSA -> DSO -> Cube.

O SAP BW Open Hub Destination (OHD) define o alvo para o qual os dados do SAP são retransmitidos. Quaisquer objetos suportados pelo SAP Data Transfer Process (DTP) podem ser usados como fontes de dados de hub aberto, por exemplo, DSO, InfoCube, DataSource, etc. O tipo Open Hub Destination - onde os dados retransmitidos são armazenados - pode ser tabelas de bases de dados (locais ou remotas) e ficheiros planos. Este conector SAP BW Open Hub suporta copiar dados da tabela local OHD em BW. Caso esteja a utilizar outros tipos, pode ligar-se diretamente à base de dados ou ao sistema de ficheiros utilizando outros conectores.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração delta

O ADF SAP BW Open Hub `excludeLastRequest` Connector oferece duas propriedades opcionais: e `baseRequestId` que podem ser usadas para lidar com a carga delta a partir do Open Hub. 

- **excluirLastRequestId**: Se excluir os registos do último pedido. O valor padrão é verdadeiro. 
- **baseRequestId**: A identificação do pedido de carregamento delta. Uma vez definido, apenas serão recuperados dados com pedido Deid maior do que o valor desta propriedade. 

Globalmente, a extração da SAP InfoProviders para a Azure Data Factory (ADF) consiste em 2 etapas: 

1. Processo de **Transferência de Dados SAP BW (DTP)** Este passo copia os dados de um SAP BW InfoProvider para uma tabela SAP BW Open Hub 

1. **Cópia de dados da ADF** Neste passo, a tabela Open Hub é lida pelo Conector ADF 

![Fluxo de extração delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

No primeiro passo, um DTP é executado. Cada execução cria uma nova identificação de pedido SAP. O ID de pedido é armazenado na tabela Open Hub e é depois utilizado pelo conector ADF para identificar o delta. Os dois passos são executados assincronicamente: o DTP é acionado pelo SAP, e a cópia de dados da ADF é desencadeada através da ADF. 

Por padrão, a ADF não está a ler o mais recente delta da tabela Open Hub (a opção "excluir o último pedido" é verdadeira). Assim, os dados em ADF não estão 100% atualizados com os dados na tabela Open Hub (falta o último delta). Em troca, este procedimento garante que não se percam filas causadas pela extração assíncrona. Funciona bem mesmo quando a ADF está a ler a tabela Open Hub enquanto o DTP ainda está a escrever na mesma mesa. 

Normalmente, armazena o ID de pedido copiado max na última execução pela ADF numa loja de dados de encenação (como o Azure Blob no diagrama acima). Por conseguinte, o mesmo pedido não é lido uma segunda vez pela ADF na execução subsequente. Entretanto, note que os dados não são automaticamente eliminados da tabela Open Hub.

Para um manuseamento adequado do delta, não é permitido ter iDs de dTPs diferentes na mesma tabela Open Hub. Portanto, não deve criar mais do que um DTP para cada Destino Open Hub (OHD). Ao necessitar de extração completa e delta do mesmo InfoProvider, deverá criar dois OHDs para o mesmo InfoProvider. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP Business Warehouse Open Hub, é necessário:

- Configurar um Runtime de Integração Auto-hospedado com a versão 3.13 ou superior. Consulte o artigo [De Integração Autónoma](create-self-hosted-integration-runtime.md) para obter mais detalhes.

- Descarregue o ** [Conector 3.0](https://support.sap.com/en/product/connectors/msnet.html) ** sAP .NET de 64 bits e instale-o na máquina de INFRAVERMELHOS auto-hospedada. Ao instalar, na janela de passos de configuração opcional, certifique-se de que seleciona os Conjuntos de Instalação para a opção **GAC,** conforme mostrado na imagem seguinte. 

    ![Instale o Conector SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O utilizador SAP utilizado no conector BW da Fábrica de Dados precisa de ter permissões seguintes: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a atividade "Executar" do objeto de autorização "S_SDSAUTH".

- Crie o tipo de destino SAP Open Hub como Tabela de Bases de **Dados** com opção "Chave Técnica" verificada.  Recomenda-se também deixar os Dados de Aleting da Tabela como não verificados, embora não sejam necessários. Aproveite o DTP (executar ou integrar-se diretamente na cadeia de processos existente) para obter dados de objeto sinuoso (como cubo) que escolheu para a tabela de destino open hub.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para uma passagem pela utilização do conector SAP BW Open Hub, consulte [os dados de carga do SAP Business Warehouse (BW) utilizando](load-sap-bw-data.md)a Azure Data Factory .

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas para o conector SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao SAP Business Warehouse Open Hub:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapOpenHub** | Sim |
| servidor | Nome do servidor em que reside a instância SAP BW. | Sim |
| sistemaNúmero | Número do sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma corda. | Sim |
| clientId | Identificação do cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma corda. | Sim |
| language | Linguagem que o sistema SAP usa. | Não (valor predefinido é **EN)**|
| userName | Nome do utilizador que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, tal como mencionado nos [pré-requisitos.](#prerequisites) |Sim |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados SAP BW Open Hub.

Para copiar dados de e para O SAP BW Open Hub, detete a propriedade do tipo do conjunto de dados para **SapOpenHubTable**. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome do Open Hub Destination para copiar dados de. | Sim |

Se estava `excludeLastRequest` a `baseRequestId` definir e no conjunto de dados, ainda é suportado como está, enquanto é sugerido que utilize o novo modelo na fonte de atividade.

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte SAP BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub como fonte

Para copiar dados do SAP BW Open Hub, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade da cópia deve ser definida para **SapOpenHubSource**. | Sim |
| excluirLastRequest | Se excluir os registos do último pedido. | Não (o padrão é **verdadeiro)** |
| baseRequestId | A identificação do pedido de carregamento delta. Uma vez definido, apenas serão recuperados dados com pedido Deid **maior do que** o valor desta propriedade.  | Não |

>[!TIP]
>Se a tabela Open Hub contiver apenas os dados gerados por um único ID de pedido, por exemplo, faz sempre a carga completa e substitui os dados existentes na tabela, ou só executa o DTP uma vez para teste, lembre-se de desmarcar a opção "excluirO Pedido" para copiar os dados.

Para acelerar o carregamento de [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) dados, pode definir a atividade de cópia para carregar dados do SAP BW Open Hub em paralelo. Por exemplo, se `parallelCopies` você definir para quatro, Data Factory executa simultaneamente quatro chamadas RFC, e cada chamada RFC recupera uma parte dos dados da sua tabela SAP BW Open Hub dividida pelo ID de pedido dTP e ID do pacote. Isto aplica-se quando o número de ID de pedido de `parallelCopies`DTP exclusivo + id pacote é maior do que o valor de . Ao copiar dados para uma loja de dados baseada em ficheiros, também é re-ordenado para escrever para uma pasta como múltiplos ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

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

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para SAP BW Open Hub

Ao copiar dados do SAP BW Open Hub, os seguintes mapeamentos são utilizados desde tipos de dados SAP BW para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo SAP ABAP | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| C (Corda) | Cadeia |
| I (inteiro) | Int32 |
| F (Flutuação) | Double |
| D (Data) | Cadeia |
| T (Tempo) | Cadeia |
| P (BCD Embalado, Moeda, Decimal, Qty) | Decimal |
| N (Numc) | Cadeia |
| X (Binário e Cru) | Cadeia |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

**Sintomas:** Se estiver a executar sap BW em HANA e observar apenas o subconjunto de dados é copiado através da atividade de cópia ADF (1 milhão de linhas), a causa possível é que você ativa a opção "SAP HANA Execution" no seu DTP, caso em que a ADF só pode recuperar o primeiro lote de dados.

**Resolução:** Desative a opção "Execução SAP HANA" no DTP, reprocesse os dados e tente executar novamente a atividade da cópia.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
