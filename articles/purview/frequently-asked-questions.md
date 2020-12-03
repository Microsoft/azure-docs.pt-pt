---
title: Perguntas Frequentes (FAQ)
description: Este artigo responde frequentemente a perguntas sobre a Azure Purview.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: eca0b9986c4da30adeeb02bc3d90d1e3d2892df7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553878"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Perguntas frequentes (FAQ) sobre Azure Purview

## <a name="overview"></a>Descrição geral

Muitas organizações carecem de uma compreensão holística dos seus dados. É um desafio entender que dados existem, onde os dados estão localizados, e como encontrar e aceder a dados relevantes. Os dados carecem de contexto como a linhagem, classificação e metadados abrangentes, dificultando aos utilizadores empresariais a procura dos dados certos e a utilização adequada desses dados. Como resultado, apenas uma pequena fração de dados recolhidos é usada para informar as decisões empresariais. Por último, é inconsistente identificar questões de segurança dos dados e proteger dados sensíveis. Requer tempo e esforço contínuos, especialmente mantendo a agilidade dos dados.

A Azure Purview é uma solução de governação de dados. Ajuda os clientes a adquirirem um conhecimento profundo de todos os seus dados, mantendo o controlo sobre o seu uso. Com a Azure Purview, as organizações descobrem e curam dados. Eles obtêm informações sobre o seu espólio de dados, e centralmente regem o acesso aos dados.

## <a name="purpose-of-this-faq"></a>Propósito desta FAQ

Esta FAQ responde a perguntas comuns que os clientes e as equipas de campo muitas vezes fazem. Pretende-se esclarecer questões sobre o Azure Purview e soluções relacionadas, tais como O Catálogo de Dados Azure (ADC) Gen 2 (prevadido) e Proteção de Informação Azure.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Quais são os tipos de origem disponíveis para digitalização e classificação de metadados?

|Azure|Não-Azure|
|---------|---------|
|Armazenamento de Blobs do Azure|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (Disponível até final de 2020)|
|Instância Gerida do SQL do Azure|SAP ECC (Disponível até final de 2020)|
|Azure Data Explorer|SAP S/4 HANA (Disponível até final de 2020)|
|Armazenamento do Azure Data Lake Ger1|Hive Metastore (Disponível até final de 2020)|
|Armazenamento do Azure Data Lake Ger2|--|
|Ficheiros do Azure|--|
|Base de Dados SQL do Azure|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Que sistemas/processadores de dados podemos ligar e obter linhagem?

|Sistema de dados/processador 
|---------
|Azure Data Factory: Atividade de cópia, atividade do Fluxo de Dados 
|Linhagem personalizada   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Como estão relacionados a ADC Gen 2, Azure Information Protection e Azure Purview?

Azure Purview começou originalmente como ADC Gen 2, mas desde então alargou o seu âmbito. Agora, abrange as capacidades avançadas de catálogo da ADC Gen 2 combinadas com a classificação de dados, rotulagem e capacidade de aplicação da política de conformidade da Azure Information Protection. Hoje, alinha-se mais estreitamente com a definição mais ampla da indústria de governação de dados.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>O que acontece aos clientes que usam a ADC Gen 1?

A Azure Purview é o foco de toda a inovação de produtos no espaço de solução de catálogo para a Microsoft. A ADC Gen 1 continuará a ser apoiada.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Os clientes podem ter várias contas Azure Purview na mesma subscrição?

Sim, apoiamos muitas contas Azure Purview por subscrição e por inquilino.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Posso dirigir a ADC Gen 1 e Azure Purview em paralelo?

Sim. Ambos são serviços independentes.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Como posso migrar os ativos de dados existentes da ADC Gen 1 para a Azure Purview?

Utilize as APIs de visão Azure para extrair da ADC Gen 1 e ingerir no Azure Purview. Para o glossário, apoiamos ferramentas a granel baseadas no CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Como criptografo dados sensíveis para tabelas SQL utilizando o Azure Purview?

A encriptação de dados é feita ao nível da fonte de dados. A Azure Purview armazena apenas os metadados. Não pré-visualiza os dados.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Todas as capacidades da ADC Gen 2 existirão em Azure Purview?

Sim.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Qual é a diferença entre um glossário e uma classificação?

Um glossário utiliza uma convenção de nomeação seguida de utilizadores não técnicos/empresariais dos dados, também conhecidos como consumidores de dados. Este tipo de pessoas são analistas de negócios ou cientistas de dados que usam o Azure Purview para procurar certos tipos de dados, com base no uso do negócio. Por exemplo, os analistas da cadeia de fornecimento podem precisar de procurar os termos *SKU tipos* e *detalhes de envio*. Procuram nestes termos o glossário para encontrar dados relevantes.
A classificação é uma etiqueta aplicada a um ativo de dados na tabela, coluna ou nível de ficheiro, que identifica os dados existentes no ativo. A classificação pode ser aplicada automaticamente ou manualmente, com base no tipo de dados encontrados. Normalmente, você usa tags de classificação para identificar se um ativo contém dados sensíveis, e que tipo de dados sensíveis podem ser.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>O Azure Purview digitaliza e classifica e-mails, PDFs etc. no meu Sharepoint e OneDrive?

A verificação de sites e bibliotecas sharePoint no local é fornecida através do scanner de Proteção de Informação Azure. O scanner está disponível para utilização através da subscrição Microsoft 365 de um cliente com os seguintes SKUs: AIP P1, EMS E3 e M365 E3. Se tiver algum destes SKUs, deve ter os direitos certos para começar a usar o scanner de Proteção de Informação Azure.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Qual é o cálculo usado para a varredura?
Existe uma infraestrutura de digitalização gerida pela Microsoft. Para a maioria dos recursos Azure/AWS que apoiamos, você não precisa de implantar uma infraestrutura de digitalização.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Existe uma forma de providenciar a Azure Purview via Azure Resource Manager (ARM) modelo / CLI / PowerShell?

Sim, o modelo ARM está disponível

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Já estou a usar o Atlas, posso facilmente mudar-me para Azure Purview?

O Azure Purview é compatível com a Atlas API. Se estiver a migrar do Atlas, é aconselhável digitalizar as suas fontes de dados primeiro utilizando o Azure Purview. Uma vez que os ativos estejam disponíveis na sua conta, pode utilizar APIs atlas semelhantes para integrar, tais como atualizar ativos ou adicionar linhagem personalizada. O Azure Purview modifica a API de Pesquisa para utilizar a Azure Search para que possa utilizar a Pesquisa Antecipada.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Posso criar vários catálogos no meu inquilino?

Sim, você pode criar várias contas Azure Purview por subscrição e por inquilino. Pode rever a página limites [Gerir e aumentar quotas de recursos com a Azure Purview.](how-to-manage-quotas.md)

Recomendação adicional sobre quando deve ou não ter várias contas são documentadas nas nossas [melhores práticas de implementação do Azure Purview](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Posso registar vários inquilinos numa única conta Azure Purview?

Não, atualmente para digitalizar a fonte de dados de outro inquilino, você precisa criar uma conta Azure Purview separada nesse inquilino.

### <a name="does-azure-purview-support-column-level-lineage"></a>A Azure Purview suporta a linhagem do nível da coluna?

Sim, a Azure Purview suporta a linhagem do nível da coluna.