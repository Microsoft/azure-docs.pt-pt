---
title: Copiar dados do SAP Business Warehouse via Open Hub
description: Saiba como copiar dados do SAP Business Warehouse (BW) via Open Hub para armazenar dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto da Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/02/2021
ms.openlocfilehash: 5efc27a1ad1a26c1ae50b6aecf250afef052e3de
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220543"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse via Open Hub usando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um Sap Business Warehouse (BW) via Open Hub. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!TIP]
>Para obter o apoio global da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP utilizando o papel branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada em cada conector SAP, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este SAP Business Warehouse via Conector Open Hub é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do SAP Business Warehouse via Open Hub para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP Business Warehouse Open Hub suporta:

- Versão 7.01 ou superior do SAP Business Warehouse **(num recente Pacote de Pacotes de Suporte SAP lançado após o ano de 2015)**. O SAP BW/4HANA não é suportado por este conector.
- Copiar dados através da tabela local Open Hub Destination, que por baixo pode ser DSO, InfoCube, MultiProvider, DataSource, etc.
- Copiar dados utilizando a autenticação básica.
- Ligação a um servidor de aplicações SAP ou servidor de mensagens SAP.
- Recuperação de dados via RFC.

## <a name="sap-bw-open-hub-integration"></a>Integração do Hub Aberto SAP BW 

[O SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma forma eficiente de extrair dados da SAP BW. O diagrama seguinte mostra um dos fluxos típicos que os clientes têm no seu sistema SAP, caso em que os dados fluem do SAP ECC -> PSA -> DSO -> Cube.

O SAP BW Open Hub Destination (OHD) define o alvo para o qual os dados SAP são retransmitidos. Quaisquer objetos suportados pelo Processo de Transferência de Dados SAP (DTP) podem ser utilizados como fontes de dados de hub aberto, por exemplo, DSO, InfoCube, DataSource, etc. O tipo de destino Open Hub - onde os dados retransmitidos são armazenados - pode ser tabelas de bases de dados (locais ou remotas) e ficheiros planos. Este conector SAP BW Open Hub suporta dados de cópia da tabela local OHD em BW. No caso de utilizar outros tipos, pode ligar-se diretamente à base de dados ou ao sistema de ficheiros utilizando outros conectores.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração delta

O ADF SAP BW Open Hub Connector oferece duas propriedades opcionais: `excludeLastRequest` e que podem ser `baseRequestId` usadas para lidar com a carga delta a partir do Open Hub. 

- **excluirLastRequestId**: Se excluir os registos do último pedido. O valor predefinido é verdadeiro. 
- **baseRequestId**: A identificação do pedido de carregamento delta. Uma vez definido, apenas os dados com pedidoId maior do que o valor desta propriedade serão recuperados. 

Globalmente, a extração de SAP InfoProviders para Azure Data Factory (ADF) consiste em dois passos: 

1. **Processo de transferência de dados SAP BW (DTP)** Este passo copia os dados de um SAP BW InfoProvider para uma tabela SAP BW Open Hub 

1. **Cópia de dados da ADF** Neste passo, a tabela Open Hub é lida pelo Conector ADF 

![Fluxo de extração delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

No primeiro passo, um DTP é executado. Cada execução cria um novo ID de pedido SAP. O ID do pedido é armazenado na tabela Open Hub e é depois utilizado pelo conector ADF para identificar o delta. Os dois passos são executados assíncronamente: o DTP é acionado pela SAP, e a cópia de dados ADF é ativada através da ADF. 

Por padrão, a ADF não está a ler o último delta da tabela Open Hub (a opção "excluir o último pedido" é verdadeira). Assim, os dados em ADF não estão 100% atualizados com os dados na tabela Open Hub (falta o último delta). Em troca, este procedimento garante que não se percam filas causadas pela extração assíncrona. Funciona bem mesmo quando a ADF está a ler a tabela Open Hub enquanto o DTP ainda está a escrever na mesma tabela. 

Normalmente armazena o ID de pedido copiado máximo na última execução pela ADF numa loja de dados de encenação (tal como a Azure Blob no diagrama acima). Por conseguinte, o mesmo pedido não é lido uma segunda vez pela ADF na sequência. Entretanto, note que os dados não são automaticamente eliminados da tabela Open Hub.

Para uma manipulação delta adequada, não é permitido ter IDs de pedido de diferentes DTPs na mesma tabela Open Hub. Portanto, não deve criar mais do que um DTP para cada Destino Open Hub (OHD). Ao necessitar da extração Full e Delta do mesmo InfoProvider, deverá criar dois OHDs para o mesmo InfoProvider. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP Business Warehouse Open Hub, é necessário:

- Confiem de um Tempo de Integração Auto-hospedado com a versão 3.13 ou superior. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

- Faça o download do **[Conector SAP .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) de 64 bits** do site da SAP e instale-o na máquina de INFRAVERMELHOs auto-hospedada. Ao instalar, na janela de etapas de configuração opcional, certifique-se de que seleciona os **Conjuntos de Instalação para a** opção GAC, como mostra a seguinte imagem. 

    ![Instalar o conector SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O utilizador SAP que está a ser utilizado no conector BW da Data Factory precisa de ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a "Executar" Atividade do Objeto de Autorização "S_SDSAUTH".

- Crie o tipo de destino SAP Open Hub como **Tabela de Bases de Dados** com a opção "Chave Técnica" verificada.  Recomenda-se também que os dados de eliminação da tabela não sejam controlados, embora não seja necessário. Utilize o DTP (execute ou integre diretamente na cadeia de processos existente) para obter dados de terra a partir de objetos de origem (como cubos) que escolheu para a tabela de destino do hub aberto.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para obter uma passagem útil da utilização do conector SAP BW Open Hub, consulte [os dados de carga do SAP Business Warehouse (BW) utilizando a Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector SAP Business Warehouse Open Hub.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao SAP Business Warehouse Open Hub:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapOpenHub** | Sim |
| servidor | Nome do servidor em que reside a instância SAP BW. | Sim |
| sistemaNumbre | Número do sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma corda. | Sim |
| mensagemServer | O nome de anfitrião do servidor de mensagens SAP.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| serviço de mensagensServer | O nome de serviço ou o número da porta do servidor de mensagens.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| systemId | A identificação do sistema SAP onde se encontra a mesa.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| logonGroup | O grupo de início de são para o sistema SAP.<br/>Utilize para ligar a um servidor de mensagens SAP. | Não |
| clientId | Identificação do cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma corda. | Sim |
| language | Linguagem que o sistema SAP usa. | Não (o valor predefinido é **EN)**|
| userName | Nome do utilizador que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Sim |

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

Para copiar dados de e para SAP BW Open Hub, defina a propriedade tipo do conjunto de dados para **o SapOpenHubTable**. As seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome do Destino Open Hub para copiar dados de. | Sim |

Se estiver a configurar `excludeLastRequest` e no conjunto de `baseRequestId` dados, ainda é suportado como está, enquanto é sugerido que utilize o novo modelo na fonte de atividade que vai para a frente.

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte DOE BW Open Hub.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub como fonte

Para copiar dados do SAP BW Open Hub, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade de cópia deve ser definida para **SapOpenHubSource**. | Sim |
| excluirLastRequest | Se excluir os registos do último pedido. | Não (o padrão é **verdadeiro)** |
| baseRequestId | A identificação do pedido de carregamento delta. Uma vez definido, apenas os dados com pedidoId **maior do que** o valor desta propriedade serão recuperados.  | Não |
| customRfcReadTableFunctionModule | Um módulo de função RFC personalizado que pode ser usado para ler dados a partir de uma tabela SAP. <br/> Pode utilizar um módulo de função RFC personalizado para definir como os dados são recuperados do seu sistema SAP e devolvidos à Data Factory. O módulo de função personalizada deve ter uma interface implementada (importação, exportação, tabelas) semelhante a `/SAPDS/RFC_READ_TABLE2` , que é a interface padrão usada pela Data Factory. | Não |

>[!TIP]
>Se a sua tabela Open Hub apenas contiver os dados gerados por um único ID de pedido, por exemplo, faz sempre a carga completa e substitui os dados existentes na tabela, ou só executou o DTP uma vez para teste, lembre-se de desmarcar a opção "excluirLastRequest" para copiar os dados.

Para acelerar o carregamento de dados, pode definir [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) a atividade de cópia para carregar os dados do SAP BW Open Hub em paralelo. Por exemplo, se definir `parallelCopies` para quatro, data factory executa simultaneamente quatro chamadas RFC, e cada chamada RFC recupera uma parte dos dados da sua tabela SAP BW Open Hub dividida pelo ID de pedido DTP e ID do pacote. Isto aplica-se quando o número de ID de pedido único de DTP + iD do pacote é maior do que o valor de `parallelCopies` . Ao copiar dados para a loja de dados baseada em ficheiros, também é recomuedado para escrever para uma pasta como vários ficheiros (apenas especificar o nome da pasta), caso em que o desempenho é melhor do que escrever para um único ficheiro.

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

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento do tipo de dados para SAP BW Open Hub

Ao copiar dados do SAP BW Open Hub, os seguintes mapeamentos são utilizados desde os tipos de dados SAP BW até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo SAP ABAP | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| C (Corda) | String |
| I (inteiro) | Int32 |
| F (Flutuar) | Double (Duplo) |
| D (Data) | String |
| T (Tempo) | String |
| P (BCD Embalado, Moeda, Decimal, Qty) | Decimal |
| N (Numc) | String |
| X (Binário e Cru) | String |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas

**Sintomas:** Se estiver a executar o SAP BW em HANA e observar que apenas o subconjunto de dados é copiado através da atividade de cópia ADF (1 milhão de linhas), a causa possível é que ativa a opção "EXECUÇÃO SAP HANA" no seu DTP, caso em que a ADF só pode recuperar o primeiro lote de dados.

**Resolução:** Desative a opção "Execução SAP HANA" em DTP, reprocesse os dados e tente executar novamente a atividade da cópia.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
