---
title: Ligue-se à Fábrica de Dados Azure
description: Este artigo descreve como ligar a Azure Data Factory e a Azure Purview para rastrear a linhagem de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 55651b3201676ee5cddb5412e950791afaa4e87a
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852137"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Como ligar a Azure Data Factory e a Azure Purview

Este documento explica os passos necessários para ligar uma conta Azure Data Factory a uma conta Azure Purview para rastrear a linhagem de dados. O documento também entra nos detalhes do âmbito de cobertura e padrões de linhagem suportados.

## <a name="view-existing-data-factory-connections"></a>Ver as ligações existentes da Data Factory

Várias fábricas de dados Azure podem ligar-se a um único Catálogo de Dados Azure Purview para pressionar informações de linhagem. O limite atual permite-lhe ligar dez contas data factory de cada vez a partir do centro de gestão de Purview. Para mostrar a lista de contas data factory ligadas ao seu Catálogo de Dados Purview, faça o seguinte:

1. Selecione **Centro de Gestão** no painel de navegação à esquerda.
2. Em **ligações externas,** selecione **a ligação data factory**.
3. A lista de ligação da Data Factory aparece.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Imagem de ecrã mostrando uma lista de ligação à fábrica de dados." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Note os vários valores para a ligação **Estado:**

    - **Ligado**: A fábrica de dados está ligada ao catálogo de dados.
    - **Desligado**: A fábrica de dados tem acesso ao catálogo, mas está ligada a outro catálogo. Como resultado, a linhagem de dados não será reportada automaticamente ao catálogo.
    - **Não-Adcêncio**: O utilizador atual não tem acesso à fábrica de dados, pelo que o estado de ligação é desconhecido.
 >[!Note]
 >Para visualizar as ligações da Data Factory, é necessário atribuir-lhe qualquer uma das funções de Purview:
 >- Contribuidor
 >- Proprietário
 >- Leitor
 >- Administrador de Acesso dos Utilizadores

## <a name="create-new-data-factory-connection"></a>Criar nova ligação à Fábrica de Dados

Siga os passos abaixo para ligar as contas existentes da Data Factory ao seu Catálogo de Dados de Purview.

1. Selecione **Centro de Gestão** no painel de navegação à esquerda.
2. Em **ligações externas,** selecione **a ligação data factory**.
3. Na página de ligação data **factory,** selecione **New**.

4. Selecione a sua conta Data Factory da lista e selecione **OK**. Também pode filtrar por nome de subscrição para limitar a sua lista.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Screenshot mostrando como ligar Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Algumas instâncias da Data Factory podem ser desativadas se a fábrica de dados já estiver ligada à conta atual de Purview, ou se a fábrica de dados não tiver uma identidade gerida.

    Será apresentada uma mensagem de aviso se alguma das fábricas de dados selecionadas já estiver ligada a outra conta Descontida. Ao selecionar OK, a ligação data factory com a outra conta Purview será desligada. Não são necessárias confirmações adicionais.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Screenshot mostrando aviso para desligar AZure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>Agora apoiamos a adição de não mais de 10 Fábricas de Dados ao mesmo tempo. Se quiser adicionar mais de 10 Fábricas de Dados de uma só vez, por favor preencha um bilhete de apoio.


### <a name="remove-data-factory-connections"></a>Remover ligações de fábrica de dados
Para remover uma ligação à fábrica de dados, faça o seguinte:

1. Na página de ligação data **factory,** selecione o botão **Remover** ao lado de uma ou mais ligações de fábrica de dados.
1. **Selecione Confirme** no popup para eliminar as ligações de fábrica de dados selecionadas.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Screenshot mostrando como selecionar fábricas de dados para remover a ligação." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

>[!Note]
>Para adicionar ou remover as ligações data factory, você precisa ser atribuído qualquer uma das funções de Purview:
>- Proprietário
>- Administrador de Acesso ao Utilizador Além disso, exige que os utilizadores sejam "Proprietário" ou "Contribuinte" da fábrica de dados. 

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>Configure um IR auto-hospedado para recolher a linhagem a partir de SQL on-prem

A linhagem para a atividade de Cópia de Fábrica de Dados está disponível para bases de dados SQL no local. Se estiver a executar o tempo de integração auto-hospedado para o movimento de dados com a Azure Data Factory e quiser capturar a linhagem em Azure Purview, certifique-se de que a versão é 4.8.7418.1 ou mais tarde. Para obter mais informações sobre o tempo de integração auto-hospedado, consulte [Criar e configurar um tempo de integração auto-hospedado.](../data-factory/create-self-hosted-integration-runtime.md)

## <a name="supported-azure-data-factory-activities"></a>Atividades da Azure Data Factory apoiadas

A Azure Purview captura a linhagem de tempo de execução das seguintes atividades da Azure Data Factory:

- Copiar Dados
- Fluxo de Dados
- Executar pacote SSIS

> [!IMPORTANT]
> A Azure Purview deixa cair a linhagem se a fonte ou destino utilizar um sistema de armazenamento de dados não suportado.

A integração entre a Data Factory e a Purview suporta apenas um subconjunto dos sistemas de dados que a Data Factory suporta, conforme descrito nas secções seguintes.

### <a name="data-factory-copy-data-support"></a>Suporte de dados de cópia de fábrica de dados

| Sistema de armazenamento de dados | Suportado como fonte | Suportado como pia |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 (sem suporte JSON) | Sim | Sim (apenas cópia não binária) |
| ADLS Gen2 (sem suporte JSON) | Sim | Sim |
| Azure Blob (sem suporte JSON) | Sim | Sim |
| Azure Cosmos DB (SQL API) | Sim | Sim |
| Azure Cosmos DB (Mongo API) | Sim | Sim |
| Azure Cognitive Search | Sim | Sim |
| Azure Data Explorer | Sim | Sim |
| Base de Dados Azure para Maria DB \* | Sim | Sim |
| Base de Dados Azure para MYSQL \* | Sim | Sim |
| Base de Dados Azure para PostgreSQL \* | Sim | Sim |
| Armazenamento de Ficheiros do Azure | Sim | Sim |
| Armazenamento de Tabelas do Azure | Sim | Sim |
| Base de Dados Azure SQL \* | Sim | Sim |
| Azure SQL MI \* | Sim | Sim |
| Azure Synapse Analytics (anteriormente SQL DW) \* | Sim | Sim |
| SQL Server On-prem (SHIR obrigatório) \* | Sim | Sim |
| Amazon S3 | Sim | Sim |
| Teradata | Sim | Sim |
| SAP s4 Hana | Sim | Sim |
| SAP ECC | Sim | Sim |
| Hive | Sim | Sim |

> [!Note]
> A funcionalidade de linhagem tem uma certa sobrecarga de desempenho na atividade de cópia da Data Factory. Para aqueles que configuram ligações de fábrica de dados em Purview, você pode observar certos trabalhos de cópia demorando mais tempo a completar. A maior parte do impacto não é insignificante. Por favor contacte o suporte com a comparação de tempo se os trabalhos de cópia demorarem significativamente mais tempo a terminar do que o habitual.

### <a name="data-factory-data-flow-support"></a>Suporte ao fluxo de dados da fábrica de dados

| Sistema de armazenamento de dados | Suportado |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | Sim |
| ADLS Gen2 | Sim |
| Blob do Azure | Sim |
| Base de Dados Azure SQL \* | Sim |
| Azure Synapse Analytics (anteriormente SQL DW) \* | Sim |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory Executa suporte ao pacote SSIS

| Sistema de armazenamento de dados | Suportado |
| ------------------- | ------------------- | ----------------- |
| Blob do Azure | Sim |
| ADLS Gen1 | Sim |
| ADLS Gen2 | Sim |
| Base de Dados Azure SQL \* | Sim |
| Azure SQL MI \*| Sim |
| Azure Synapse Analytics (anteriormente SQL DW) \* | Sim |
| SQL Server On-prem \* | Sim |
| Armazenamento de Ficheiros do Azure | Sim |

*\* Para cenários SQL (Azure e no local), o Azure Purview não suporta procedimentos ou scripts armazenados para linhagem ou digitalização. A linhagem é limitada apenas a fontes de mesa e visualização.*

> [!Note]
> O Azure Data Lake Storage Gen2 já está em disponibilidade geral. Recomendamos que comece a utilizar hoje. Para mais informações, consulte a página do [produto.](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)

## <a name="supported-lineage-patterns"></a>Padrões de linhagem suportados

Existem vários padrões de linhagem que a Azure Purview suporta. Os dados de linhagem gerados baseiam-se no tipo de fonte e pia utilizados nas atividades da Data Factory. Embora a Data Factory suporte mais de 80 fontes e pias, a Azure Purview suporta apenas um subconjunto, conforme listado nas [atividades da Azure Data Factory.](#supported-azure-data-factory-activities)

Para configurar a Data Factory para enviar informações de linhagem, consulte [Começar com a linhagem](catalog-lineage-user-guide.md#get-started-with-lineage).

Algumas formas adicionais de encontrar informações na visão da linhagem, incluem as seguintes:

- No **separador Linhagem,** paire sobre formas para visualizar informações adicionais sobre o ativo na ponta da ferramenta .
- Selecione o nó ou borda para ver o tipo de ativo a que pertence ou para mudar de ativo.
- As colunas de um conjunto de dados são apresentadas no lado esquerdo do **separador Linhagem.** Para obter mais informações sobre a linhagem ao nível da coluna, consulte [a linhagem ao nível da coluna](catalog-lineage-user-guide.md#column-level-lineage).

### <a name="data-lineage-for-11-operations"></a>Linhagem de dados para operações 1:1

O padrão mais comum para capturar a linhagem de dados é mover dados de um único conjunto de dados de entrada para um conjunto de dados de saída único, com um processo no meio.

Um exemplo deste padrão seria o seguinte:

- 1 fonte/entrada: *Cliente* (Tabela SQL)
- 1 pia/saída: *Customer1.csv* (Azure Blob)
- 1 processo: *CopyCustomerInfo1 \#Customer1.csv* (atividade de cópia de fábrica de dados)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Screenshot mostrando a linhagem para uma operação de cópia de uma fábrica de dados." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Movimento de dados com linhagem 1:1 e suporte wildcard

Outro cenário comum para capturar a linhagem é usar um wildcard para copiar ficheiros de um único conjunto de dados de entrada para um único conjunto de dados de saída. O wildcard permite que a atividade da cópia corresponda a vários ficheiros para copiar utilizando uma parte comum do nome do ficheiro. O Azure Purview captura a linhagem de nível de ficheiro para cada ficheiro copiado pela correspondente atividade de cópia.

Um exemplo deste padrão seria o seguinte:

* Fonte/entrada: *CustomerCall \* .csv* (caminho ADLS Gen2)
* Pia/saída: *.csv \* customerCall* (ficheiro blob Azure)
* 1 processo: *CopyGen2ToBlob \#CustomerCall.csv* (atividade de cópia de fábrica de dados)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Screenshot mostrando linhagem para uma operação copy one-one com suporte wildcard." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Movimento de dados com linhagem n:1

Pode utilizar as atividades do Data Flow para realizar operações de dados como fusão, junção e assim por diante. Mais de um conjunto de dados de origem pode ser usado para produzir um conjunto de dados-alvo. Neste exemplo, o Azure Purview captura a linhagem de nível de ficheiro para ficheiros de entrada individuais para uma tabela SQL que faz parte de uma atividade do Data Flow.

Um exemplo deste padrão seria o seguinte:

* 2 fontes/entradas: *Customer.csv,* *Sales.parquet* (Caminho ADLS Gen2)
* 1 pia/saída: *Dados da empresa* (tabela Azure SQL)
* 1 processo: *DataFlowBlobsToSQL* (atividade de fluxo de dados da fábrica de dados)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Screenshot mostrando a linhagem para uma operação de fluxo de dados A D F." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Linhagem para conjuntos de recursos

Um conjunto de recursos é um objeto lógico no catálogo que representa muitos ficheiros de partição no armazenamento subjacente. Para obter mais informações, consulte [os conjuntos de Recursos de Compreensão](concept-resource-sets.md). Quando o Azure Purview captura a linhagem da Fábrica de Dados Azure, aplica as regras para normalizar os ficheiros de partição individuais e criar um único objeto lógico.

No exemplo seguinte, um conjunto de recursos Azure Data Lake Gen2 é produzido a partir de uma Bolha Azure:

* 1 fonte/entrada: *management.csvdo empregado \_* (Azure Blob)
* 1 pia/saída: *management.csvdos empregados \_* (Azure Data Lake Gen 2)
* 1 processo: *CopyBlobToAdlsGen2 \_ RS* (atividade de cópia de fábrica de dados)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Screenshot mostrando a linhagem para um conjunto de recursos." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Passos seguintes

- [Guia do utilizador da linhagem do catálogo](catalog-lineage-user-guide.md)
- [Link para Azure Data Share para linhagem](how-to-link-azure-data-share.md)