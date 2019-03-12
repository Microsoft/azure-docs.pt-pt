---
title: Copiar dados do SAP Business Warehouse através de Hub aberto com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do SAP Business Warehouse (BW) através de Hub aberta para arquivos de dados de sink suportado com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 474ebaad60328b011e91337c46040ae37c603e21
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731053"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse através de Hub aberto com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados a partir de um SAP Business Warehouse (BW) através de Hub aberto. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do SAP Business Warehouse através do Hub de abrir qualquer arquivo de dados sink suportado. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector SAP Business Warehouse aberto Hub suporta:

- SAP Business Warehouse **versão 7.01 ou superior (numa recente SAP suporte pacote pilha lançada após o ano de 2015)**.
- Cópia de dados por meio da tabela de local de destino de Hub aberto que, por baixo, pode ser DSO, consulta do InfoCube, MultiProvider, origem de dados, etc.
- Cópia de dados usando a autenticação básica.
- A ligar ao servidor de aplicações.

## <a name="sap-bw-open-hub-integration"></a>Integração de Hub do SAP BW aberto 

[Serviço de Hub aberto do SAP BW](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) é uma maneira eficiente para extrair dados do SAP BW. O diagrama seguinte mostra um dos fluxos típicos, os clientes têm no seu sistema SAP, os fluxos de dados de caso do SAP ECC -> PSA -> DSO -> cubo.

SAP BW aberto Hub destino (OHD) define o destino ao qual os dados do SAP são passados. Todos os objetos suportados pelo SAP dados de transferência de processo (DTP) podem ser usados como origens de dados de hub aberto, por exemplo, DSO, consulta do InfoCube, origem de dados, etc. Tipo de destino de Hub aberto - onde estão armazenados os dados retransmitidos - pode ser tabelas de base de dados (locais ou remotas) e ficheiros simples. Este suporte de conector de Hub aberto do SAP BW copiar dados de tabela local OHD BW. No caso de estiver a utilizar outros tipos, pode ligar diretamente ao sistema de base de dados ou ficheiro através de outros conectores.

![Hub do SAP BW aberto](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Fluxo de extração de delta

Conector do Hub ADF SAP BW aberto oferece duas propriedades opcionais: `excludeLastRequest` e `baseRequestId` que podem ser utilizadas para processar a carga de delta do Hub aberto. 

- **excludeLastRequestId**: Se exclui os registos do último pedido. Valor predefinido é verdadeiro. 
- **baseRequestId**: O ID do pedido de carregamento delta. Depois de definida, serão possível obter apenas os dados com requestId maior do que o valor desta propriedade. 

Em geral, a extração de SAP InfoProviders para o Azure Data Factory (ADF) consiste em 2 passos: 

1. **SAP BW dados de transferência de processo (DTP)** este passo copia os dados de um InfoProvider BW do SAP para uma tabela de Hub aberto do SAP BW 

1. **Cópia de dados do ADF** neste passo, a tabela de Hub aberto é lido pelo conector do ADF 

![Fluxo de extração de delta](media\connector-sap-business-warehouse-open-hub\delta-extraction-flow.png)

No primeiro passo é executado um DTP. Cada execução cria um novo ID de pedido SAP. O ID do pedido é armazenado na tabela Hub aberto e, em seguida, é utilizado pelo conector do ADF para identificar o delta. Os dois passos executam de maneira assíncrona: o DTP é acionada pelo SAP, e a cópia de dados do ADF é disparada por meio do ADF. 

Por predefinição, ADF não é ler o delta mais recente a partir da tabela de Hub aberto (a opção "excluir o último pedido" é o verdadeira). Pelo presente, os dados no ADF não são 100% atualizado com os dados na tabela aberto Hub (o último delta está em falta). Em troca, este procedimento garante que nenhuma linha se perder causado pela extração assíncrona. Ele funciona bem mesmo quando o ADF está lendo a tabela de Hub aberta enquanto o DTP ainda é escrita na mesma tabela. 

Onde geralmente armazena o ID do pedido copiado máximo na última execução pelo ADF num arquivo de dados de teste (por exemplo, o Azure Blob na acima diagrama). Por conseguinte, o mesmo pedido não é ler uma segunda vez pelo ADF na execução subsequente. Enquanto isso, tenha em atenção que os dados não são automaticamente eliminados da tabela de Hub aberto.

Para diferenças adequada manipulá-la não pode ter pedido IDs de DTPs diferentes na mesma tabela de Hub aberto. Portanto, deve não criar DTP mais de um para cada aberto Hub destino (OHD). Quando precisar de extração diferenciais e completas do mesmo InfoProvider, deve criar dois OHDs para o mesmo InfoProvider. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP Business Warehouse aberto Hub, terá de:

- Configure um Runtime de integração autoalojado com a versão 3.13 ou superior. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

- Transfira o **64-bit [3.0 de conector do SAP .NET](https://support.sap.com/en/product/connectors/msnet.html)**  partir do site do SAP e instalá-lo na máquina do Runtime de integração autoalojado. Quando instalar, na janela de passos de configuração opcionais, certifique-se de que seleciona os **instalar Assemblies ao GAC** opção conforme mostrado na imagem seguinte. 

    ![Instalar o conector do SAP .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Utilizador SAP que está a ser utilizado no conector do BW de fábrica de dados tem de ter as seguintes permissões: 

    - Autorização para RFC e SAP BW. 
    - Permissões para a atividade "Executar" do objeto de autorização "S_SDSAUTH".

- Criar tipo de destino de Hub aberto SAP como **tabela de base de dados** com opção de "Chave técnicos" marcada.  Também é recomendável deixar os dados a eliminação da tabela como não verificados, embora não seja necessário. Tire partido do DTP (diretamente executar ou integrar a cadeia de processo existente) para entrada de dados de objeto de origem (por exemplo, o cubo) que escolheu para a tabela de destino de hub aberto.

## <a name="getting-started"></a>Introdução

> [!TIP]
>
> Para obter instruções para utilizar o conector do Hub aberto do SAP BW, consulte [carregar dados do SAP Business Warehouse (BW) através do Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do SAP Business Warehouse aberto Hub.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de SAP Business Warehouse aberto Hub ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **SapOpenHub** | Sim |
| servidor | Nome do servidor no qual reside a instância do SAP BW. | Sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| clientId | ID de cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| language | Idioma utilizado pelo sistema SAP. | Não (valor predefinido é **EN**)|
| userName | Nome de utilizador que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados do Salesforce.

Para copiar dados de e para o Hub aberto do SAP BW, defina a propriedade de tipo de conjunto de dados para **SapOpenHubTable**. São suportadas as seguintes propriedades.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **SapOpenHubTable**.  | Sim |
| openHubDestinationName | O nome do destino de Hub aberto para copiar dados a partir de. | Sim |
| excludeLastRequest | Se exclui os registos do último pedido. | Não (a predefinição é **true**) |
| baseRequestId | O ID do pedido de carregamento delta. Depois de definida, apenas os dados com requestId **maior do que** será possível obter o valor desta propriedade.  | Não |

>[!TIP]
>Se a sua tabela de Hub aberto contém apenas os dados gerados pelo ID do pedido único, por exemplo, sempre completa de carga e substituir os dados existentes na tabela, ou apenas executar DTP uma vez para teste, não se esqueça de desmarcar a opção "excludeLastRequest" para poder copiar o d ATA horizontalmente.

**Exemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de Hub aberto do SAP BW.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW aberto Hub, como origem

Para copiar dados de Hub aberto do SAP BW, definir o tipo de origem na atividade de cópia para **SapOpenHubSource**. Embora não haja nenhum propriedades de específicos do tipo adicionais necessárias na atividade de cópia **origem** secção.

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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapeamento de tipo de dados para o Hub aberto do SAP BW

Quando se copiam dados a partir do Hub aberto do SAP BW, os seguintes mapeamentos são utilizados entre tipos de dados do SAP BW aos tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo ABAP SAP | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| C (cadeia) | String |
| Eu (número inteiro) | Int32 |
| F (número de vírgula flutuante) | Double |
| 1!d (data) | String |
| T (hora) | String |
| P (BCD incluídos, moeda, Decimal,. de defeitos) | Decimal |
| N (Numc) | String |
| X (binário e não processados) | String |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
