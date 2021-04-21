---
title: Integre o Azure Data Explorer para retenção de registos a longo prazo | Microsoft Docs
description: Envie registos do Azure Sentinel ao Azure Data Explorer para uma retenção a longo prazo para reduzir os custos de armazenamento de dados.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836290"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integre o Azure Data Explorer para retenção de registos a longo prazo

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Por predefinição, os registos ingeridos no Azure Sentinel são armazenados no Azure Monitor Log Analytics. Este artigo explica como reduzir os custos de retenção no Azure Sentinel enviando-os para o Azure Data Explorer (ADX) para retenção a longo prazo.

Armazenar registos em ADX reduz os custos, mantendo a sua capacidade de consultar os seus dados, e é especialmente útil à medida que os seus dados crescem. Por exemplo, embora os dados de segurança possam perder valor ao longo do tempo, pode ser-lhe exigido que mantenha registos para requisitos regulamentares ou para realizar investigações periódicas sobre dados mais antigos.

## <a name="about-azure-data-explorer"></a>Sobre o Azure Data Explorer

O ADX é uma plataforma de análise de dados grande que está altamente otimizada para a análise de registos e dados. Uma vez que a ADX usa a Linguagem de Consulta de Kusto (KQL) como sua linguagem de consulta, é uma boa alternativa para o armazenamento de dados do Azure Sentinel. A utilização do ADX para o armazenamento de dados permite-lhe executar consultas de plataforma cruzada e visualizar dados tanto em ADX como em Azure Sentinel.

Para mais informações, consulte a [documentação](/azure/data-explorer/) e [blog](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/)do ADX.

### <a name="when-to-integrate-with-adx"></a>Quando se integrar com o ADX

O Azure Sentinel fornece todas as capacidades SIEM e SOAR, implementação e configuração rápidas, bem como funcionalidades avançadas de segurança incorporadas para equipas SOC. No entanto, o valor de armazenar dados de segurança no Azure Sentinel pode cair após alguns meses, uma vez que os utilizadores de SOC não precisam de aceder aos mesmos com tanta frequência como acedem a dados mais recentes.

Se você só precisa de aceder a tabelas específicas ocasionalmente, como para investigações periódicas ou auditorias, você pode considerar que reter os seus dados em Azure Sentinel já não é rentável. Neste momento, recomendamos armazenar dados em ADX, que custam menos, mas ainda assim permite explorar usando as mesmas consultas de KQL que você corre em Azure Sentinel.

Pode aceder aos dados em ADX diretamente a partir de Azure Sentinel utilizando a [funcionalidade de procuração ADX log Analytics](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy). Para tal, utilize consultas de clusters cruzados na sua pesquisa de registo ou livros de trabalho. 

> [!IMPORTANT]
> As capacidades do CORE SIEM, incluindo as regras analíticas, a UEBA e o gráfico de investigação, não suportam os dados armazenados no ADX.
>

> [!NOTE]
> A integração com o ADX também pode permitir-lhe ter controlo e granularidade nos seus dados. Para mais informações, consulte [considerações de Design.](#design-considerations)
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Envie dados diretamente para Azure Sentinel e ADX em paralelo

Você pode querer reter quaisquer dados *com valor de segurança* em Azure Sentinel para usar em deteções, investigações de incidentes, caça a ameaças, UEBA, e assim por diante. Manter estes dados no Azure Sentinel beneficia principalmente os utilizadores do Security Operations Center (SOC), onde normalmente, 3-12 meses de armazenamento são suficientes.

Também pode configurar todos os seus *dados, independentemente do seu valor de segurança,* para serem enviados para o ADX ao mesmo tempo, onde pode armazená-lo por mais tempo. Ao enviar dados tanto para a Azure Sentinel como para o ADX ao mesmo tempo, resulta em alguma duplicação, a poupança de custos pode ser significativa à medida que reduz os custos de retenção em Azure Sentinel.

> [!TIP]
> Esta opção permite também correlacionar dados espalhados por lojas de dados, tais como enriquecer os dados de segurança armazenados no Azure Sentinel com dados operacionais ou de longo prazo armazenados em ADX. Para obter mais informações, consulte [a consulta de recursos cruzados Azure Data Explorer utilizando o Azure Monitor](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy).
>

A imagem a seguir mostra como pode reter todos os seus dados em ADX, ao mesmo tempo que envia apenas os seus dados de segurança para o Azure Sentinel para uso diário.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Armazenar dados em ADX e Azure Sentinel em paralelo.":::

Para obter mais informações sobre a implementação desta opção de arquitetura, consulte [a monitorização do Azure Data Explorer.](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)

## <a name="export-data-from-log-analytics-into-adx"></a>Dados de exportação do Log Analytics para o ADX

Em vez de enviar os seus dados diretamente para o ADX, pode optar por exportar os seus dados do Log Analytics para o ADX através de um centro de eventos ADX ou da Azure Data Factory.

### <a name="data-export-architecture"></a>Arquitetura de exportação de dados

A imagem a seguir mostra um fluxo de amostra de dados exportados através do gasoduto de ingestão do Monitor Azure. Os seus dados são direcionados para o Log Analytics por padrão, mas também pode configugá-lo para exportar para uma Conta de Armazenamento Azure ou centro de eventos.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Dados de exportação do Azure Monitor - arquitetura.":::

Ao configurar as regras de exportação de dados, selecione os tipos de registos que pretende exportar. Uma vez configurados, novos dados que chegam ao ponto final de ingestão de Log Analytics, e direcionados para o seu espaço de trabalho para as tabelas selecionadas, são exportados para a sua Conta de Armazenamento ou Centro de Eventos.

Ao configurar os dados para exportação, note as seguintes considerações:

|Consideração  | Detalhes |
|---------|---------|
|**Âmbito dos dados exportados**     |  Uma vez configurada a exportação para um quadro específico, todos os dados enviados para esse quadro são exportados, sem exceção. Não é suportado um subconjunto filtrado dos seus dados, nem limitando a exportação para eventos específicos.       |
|**Requisitos de localização**     |   Tanto o espaço de trabalho Azure Monitor / Azure Sentinel, como a localização do destino (uma Conta de Armazenamento Azure ou centro de eventos) devem estar localizados na mesma região geográfica.      |
|**Mesas apoiadas**     | Nem todas as tabelas são suportadas para exportação, como as tabelas de registos personalizados, que não são suportadas. <br><br>Para obter mais informações, consulte a exportação de [dados do espaço de trabalho do Log Analytics no Azure Monitor](/azure/azure-monitor/logs/logs-data-export) e a lista de [tabelas suportadas.](/azure/azure-monitor/logs/logs-data-export#supported-tables)         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Métodos e procedimentos de exportação de dados

Utilize um dos seguintes procedimentos para exportar dados do Azure Sentinel para o ADX:

- **Através de um centro de eventos ADX**. Os dados de exportação do Log Analytics para um centro de eventos, onde você pode ingerê-lo em ADX. Este método armazena alguns dados (os primeiros meses X) tanto no Azure Sentinel como no ADX.

- **Através do Azure Storage e da Azure Data Factory.** Exporte os seus dados do Log Analytics para o Azure Blob Storage, em seguida, a Azure Data Factory é usada para executar um trabalho de cópia periódica para exportar os dados para ODX. Este método permite-lhe copiar dados da Azure Data Factory apenas quando se aproxima do seu limite de retenção em Azure Sentinel / Log Analytics, evitando duplicação.

### <a name="adx-event-hub"></a>[Centro de eventos ADX](#tab/adx-event-hub)

Esta secção descreve como exportar dados do Azure Sentinel do Log Analytics para um centro de eventos, onde você pode ingeri-lo em ADX. Semelhante ao [envio de dados diretamente para Azure Sentinel e ADX em paralelo](#send-data-directly-to-azure-sentinel-and-adx-in-parallel), este método inclui alguma duplicação de dados à medida que os dados são transmitidos para ADX à medida que chegam no Log Analytics.

A imagem a seguir mostra um fluxo de amostra de dados exportados para um centro de eventos, de onde é ingerido em ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Exportar dados para o ADX através de um centro de eventos ADX.":::

A arquitetura mostrada na imagem anterior fornece toda a experiência Azure Sentinel SIEM, incluindo gestão de incidentes, investigações visuais, caça a ameaças, visualizações avançadas, UEBA, e muito mais, para dados que devem ser acedidos frequentemente, a cada *X* meses. Ao mesmo tempo, esta arquitetura também permite consultar dados de longo prazo acedendo-os diretamente no ADX, ou via Azure Sentinel graças à funcionalidade de procuração ADX. As consultas para armazenamento de dados a longo prazo no ADX podem ser portadas sem alterações de Azure Sentinel para ADX.

> [!TIP]
> Para obter mais informações sobre este procedimento, consulte [Tutorial: Ingerir e consultar dados de monitorização no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code).
>

**Para exportar dados para o ADX através de um centro de eventos:**

1. **Configure a exportação de dados do Log Analytics para um centro de eventos.** Para obter mais informações, consulte [a exportação de dados do espaço de trabalho do Log Analytics no Azure Monitor](/azure/azure-monitor/platform/logs-data-export).

1. **Crie um cluster E uma base de dados ADX.** Para obter mais informações, consulte:

    - [Criar um cluster e base de dados Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    - [Selecione o SKU computado correto para o seu cluster Azure Data Explorer](/azure/data-explorer/manage-cluster-choose-sku)

1. **Criar tabelas-alvo**. Os dados brutos são primeiro ingeridos numa tabela intermédia, onde os dados brutos são armazenados, manipulados e expandidos.

    Uma política de atualização, semelhante a uma função aplicada a todos os novos dados, é usada para ingerir os dados expandidos na tabela final, que tem o mesmo esquema que a tabela original em Azure Sentinel.

    Coloque a retenção na mesa crua para **0** dias. Os dados são armazenados apenas na tabela devidamente formatada, e eliminados na tabela bruta assim que são transformados.

    Para obter mais informações, consulte [Inger e consultar dados de monitorização no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Criar mapeamento de mesa**. Mapear as tabelas JSON para definir como os registos aterram na tabela de eventos brutos à medida que chegam de um centro de eventos. Para obter mais informações, consulte [Criar a política de atualização para dados métricos e de registo](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Crie uma política de atualização e anexe-a à tabela de registos brutos**. Neste passo, crie uma função, chamada política de atualização, e anexe-a à tabela de destino para que os dados se transformem no momento da ingestão.

    > [!NOTE]
    > Este passo só é necessário quando pretender ter tabelas de dados no ADX com o mesmo esquema e formato que no Azure Sentinel.
    >

    Para obter mais informações, consulte [o Connect a event hub ao Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Crie uma ligação de dados entre o centro de eventos e a tabela de dados brutos no ADX**. Configure o ADX com detalhes de como exportar os dados para o centro do evento.

    Utilize as instruções na documentação do [Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) e especifique os seguintes detalhes:

    - **Alvo**. Especifique a tabela específica com os dados brutos.
    - **Formato**. Especifique `.json` como o formato da tabela.
    - **Mapeamento a aplicar**. Especifique a tabela de mapeamento criada no [passo 4](#mapping) acima.


1. **Modificar a retenção para a tabela-alvo**. A [política de retenção padrão do Azure Data Explorer](/azure/data-explorer/kusto/management/retentionpolicy) pode ser muito mais longa do que necessita.

    Utilize o seguinte comando para atualizar a política de retenção para um ano:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage / Azure Data Factory](#tab/azure-storage-azure-data-factory)

Esta secção descreve como exportar dados do Azure Sentinel do Log Analytics para o Azure Storage, onde a Azure Data Factory pode executar um trabalho regular para exportar os dados para o ADX.

A utilização do Azure Storage e da Azure Data Factory permite-lhe copiar dados do Azure Storage apenas quando estiver perto do limite de retenção em Azure Sentinel / Log Analytics. Não há duplicação de dados, e o ADX é usado *apenas* para aceder a dados mais antigos do que o limite de retenção em Azure Sentinel.

> [!TIP]
> Embora a arquitetura para usar a Azure Storage e a Azure Data Factory para os seus dados antigos seja mais complexa, este método pode oferecer maiores economias de custos em geral.
>
A imagem a seguir mostra um fluxo de amostra de dados exportados para um Azure Storage, de onde a Azure Data Factory gere um trabalho regular para continuar a exportá-lo para ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Dados de exportação para ADX via Azure Storage e Azure Data Factory.":::

**Exportar dados para o ADX através de uma Azure Storage e Azure Data Factory:**

1. **Configure a exportação de dados do Log Analytics para um centro de eventos.** Para obter mais informações, consulte [a exportação de dados do espaço de trabalho do Log Analytics no Azure Monitor](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export).

1. **Crie um cluster E uma base de dados ADX.** Para obter mais informações, consulte:

    - [Criar um cluster e base de dados Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    - [Selecione o SKU computado correto para o seu cluster Azure Data Explorer](/azure/data-explorer/manage-cluster-choose-sku)

1. **Criar tabelas-alvo**. Os dados brutos são primeiro ingeridos numa tabela intermédia, onde os dados brutos são armazenados, manipulados e expandidos.

    Uma política de atualização, semelhante a uma função aplicada a todos os novos dados, é usada para ingerir os dados expandidos na tabela final, que tem o mesmo esquema que a tabela original em Azure Sentinel.

    Coloque a retenção na mesa crua para **0** dias. Os dados são armazenados apenas na tabela devidamente formatada, e eliminados na tabela bruta assim que são transformados.

    Para obter mais informações, consulte [Inger e consultar dados de monitorização no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Criar mapeamento de mesa**. Mapear as tabelas JSON para definir como os registos aterram na tabela de eventos brutos à medida que chegam de um centro de eventos. Para obter mais informações, consulte [Criar a política de atualização para dados métricos e de registo](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Crie uma política de atualização e anexe-a à tabela de registos brutos**. Neste passo, crie uma função, chamada política de atualização, e anexe-a à tabela de destino para que os dados se transformem no momento da ingestão.

    > [!NOTE]
    > Este passo só é necessário quando pretender ter tabelas de dados no ADX com o mesmo esquema e formato que no Azure Sentinel.
    >

    Para obter mais informações, consulte [o Connect a event hub ao Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Crie uma ligação de dados entre o centro de eventos e a tabela de dados brutos no ADX**. Configure o ADX com detalhes de como exportar os dados para o centro do evento.

    Utilize as instruções na documentação do [Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) e especifique os seguintes detalhes:

    - **Alvo**. Especifique a tabela específica com os dados brutos.
    - **Formato**. Especifique `.json` como o formato da tabela.
    - **Mapeamento a aplicar**. Especifique a tabela de mapeamento criada no [passo 4](#mapping) acima.

1. **Configurar o oleoduto Azure Data Factory:**

    - Crie serviços ligados para Azure Storage e Azure Data Explorer. Para obter mais informações, consulte:

        - [Copiar e transformar dados no armazenamento da Azure Blob utilizando a Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Copie os dados de ou para o Azure Data Explorer utilizando a Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

    - Crie um conjunto de dados a partir do Azure Storage. Para obter mais informações, consulte [datasets na Azure Data Factory](/azure/data-factory/concepts-datasets-linked-services).

    - Crie um pipeline de dados com uma operação de cópia, com base nas propriedades **LastModifiedDate.**

        Para obter mais informações, consulte [copiar ficheiros novos e alterados pela **LastModifiedDate** com a Azure Data Factory](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate).

---

## <a name="design-considerations"></a>Considerações de conceção

Ao armazenar os seus dados Azure Sentinel em ADX, considere os seguintes elementos:

|Consideração  |Description  |
|---------|---------|
|**Tamanho do cluster e SKU**     | Planeie cuidadosamente o número de nós e o VM SKU no seu cluster. Estes fatores determinarão a quantidade de poder de processamento e o tamanho da sua cache quente (SSD e memória). Quanto maior for a cache, mais dados poderá consultar com um desempenho mais elevado. <br><br>Encorajamo-lo a visitar a [calculadora de dimensionamento ADX,](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)onde pode jogar com diferentes configurações e ver o custo resultante. <br><br>O ADX também tem uma capacidade de autoescalação que toma decisões inteligentes para adicionar/remover nós conforme necessário com base na carga do cluster. Para obter mais informações, consulte [Gerir a escala horizontal do cluster (escala para fora) no Azure Data Explorer para acomodar a procura em mudança](/azure/data-explorer/manage-cluster-horizontal-scaling).      |
|**Cache quente/frio**     | O ADX fornece controlo sobre as tabelas de dados que estão em cache quente, e os resultados de retorno mais rápido. Se tiver grandes quantidades de dados no seu cluster ADX, pode querer quebrar as tabelas por mês, para que tenha uma maior granularidade nos dados que estão presentes na sua cache quente. <br><br>Para obter mais informações, consulte [a política de Cache (cache quente e fria)](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Retenção**     |   No ADX, pode configurar quando os dados são removidos de uma base de dados ou de uma tabela individual, o que também é uma parte importante da limitação dos custos de armazenamento. <br><br> Para mais informações, consulte [a política de retenção.](/azure/data-explorer/kusto/management/retentionpolicy)       |
|**Segurança**     |  Várias definições de ADX podem ajudá-lo a proteger os seus dados, tais como gestão de identidade, encriptação, e assim por diante. Especificamente para o controlo de acesso baseado em funções (RBAC), o ADX pode ser configurado para restringir o acesso a bases de dados, tabelas ou mesmo linhas dentro de uma tabela. Para obter mais informações, consulte [segurança no Azure Data Explorer](/azure/data-explorer/security) e segurança ao [nível da linha](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy).|
|**Partilha de dados**     |   O ADX permite disponibilizar peças de dados a outras partes, como parceiros ou fornecedores, e até mesmo comprar dados de outras partes. Para obter mais informações, consulte [Azure Data Share para partilhar dados com o Azure Data Explorer](/azure/data-explorer/data-share).      |
| **Outros componentes de custos** | Considere os outros componentes de custos para os seguintes métodos: <br><br>**Dados de exportação através de um centro de eventos ADX:** <br>- Registar os custos de exportação de dados da Analytics, cobrados por GB exportados. <br>- Custos do hub de evento, cobrados pela unidade de produção.  <br><br>**Dados de exportação através do Azure Storage e da Azure Data Factory:** <br>- Registar a exportação de dados da Analytics, cobrado por GB exportado. <br>- Azure Storage, carregado por GBs armazenados. <br>- Azure Data Factory, cobrada por cópia de atividades executadas.
|     |         |

## <a name="next-steps"></a>Passos seguintes

Independentemente de onde armazenar os seus dados, continue a caçar e a investigar usando o Azure Sentinel.

Para obter mais informações, consulte:

- [Tutorial: Investigar incidentes com Azure Sentinel](tutorial-investigate-cases.md)
- [Caça às ameaças com Azure Sentinel](hunting.md)
