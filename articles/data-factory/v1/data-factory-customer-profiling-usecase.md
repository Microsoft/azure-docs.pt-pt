---
title: Caso de Utilização - Perfis de Cliente
description: Saiba como a Azure Data Factory é usada para criar um fluxo de trabalho (pipeline) baseado em dados para perfilar os clientes de jogos.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5436e50a23b73e1a10cf42b3dd8790c5b0620ac3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021896"
---
# <a name="use-case---customer-profiling"></a>Caso de Utilização - Perfis de Cliente
A Azure Data Factory é um dos muitos serviços utilizados para implementar o Cortana Intelligence Suite de aceleradores de soluções.  Para mais informações sobre a Cortana Intelligence, visite a [Cortana Intelligence Suite.](https://www.microsoft.com/cortanaanalytics) Neste documento, descrevemos um caso de uso simples para ajudá-lo a começar a entender como a Azure Data Factory pode resolver problemas de análise comuns.

## <a name="scenario"></a>Cenário
A Contoso é uma empresa de jogos que cria jogos para várias plataformas: consolas de jogos, dispositivos portáteis e computadores pessoais (PCs). À medida que os jogadores jogam estes jogos, é produzido um grande volume de dados de registo que rastreia os padrões de utilização, estilo de jogo e preferências do utilizador.  Quando combinado com dados demográficos, regionais e de produtos, Contoso pode realizar análises para guiá-los sobre como melhorar a experiência dos jogadores e direcioná-los para upgrades e compras no jogo. 

O objetivo da Contoso é identificar oportunidades de venda/venda cruzada com base no histórico de jogos dos seus jogadores e adicionar funcionalidades convincentes para impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes. Para este caso de uso, usamos uma empresa de jogos como um exemplo de um negócio. A empresa quer otimizar os seus jogos com base no comportamento dos jogadores. Estes princípios aplicam-se a qualquer negócio que queira envolver os seus clientes em torno dos seus bens e serviços e melhorar a experiência dos seus clientes.

Nesta solução, a Contoso quer avaliar a eficácia de uma campanha de marketing que lançou recentemente. Começamos com os registos de jogos brutos, processamo-los e enriquecemo-los com dados de geolocalização, juntamo-nos a dados de referência publicitários e, por último, copiamo-los numa Base de Dados Azure SQL para analisar o impacto da campanha.

## <a name="deploy-solution"></a>Solução de implantação
Tudo o que precisa para aceder e experimentar este caso de uso simples é uma [subscrição Azure,](https://azure.microsoft.com/pricing/free-trial/)uma conta de [armazenamento Azure Blob](../../storage/common/storage-account-create.md)e uma Base de [Dados Azure SQL.](../../sql-database/sql-database-get-started.md) Implementa o pipeline de perfis de clientes a partir do azulejo de **pipelines sample** na página inicial da sua fábrica de dados.

1. Criar uma fábrica de dados ou abrir uma fábrica de dados existente. Consulte [os dados de Cópia do Armazenamento Blob para a Base de Dados SQL utilizando](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a Data Factory para obter medidas para criar uma fábrica de dados.
2. Na lâmina **DATA FACTORY** para a fábrica de dados, clique no azulejo da **amostra.**

    ![Amostra de azulejos de gasodutos](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na lâmina de **gasodutos Sample,** clique no perfil do **Cliente** que pretende implementar.

    ![Lâmina de gasodutos de amostra](./media/data-factory-samples/SampleTile.png)
4. Especifique as definições de configuração para a amostra. Por exemplo, o nome e a chave da sua conta de armazenamento Azure, nome lógico do servidor SQL, base de dados, ID do utilizador e palavra-passe.

    ![Lâmina de amostra](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar com a especificação das definições de configuração, clique em **Criar/implementar** os gasodutos de amostra e serviços/tabelas ligados utilizados pelos gasodutos.
6. Vê o estado de implantação no azulejo da amostra clicou anteriormente na lâmina do **gasoduto Sample.**

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir a mensagem **de implantação bem sucedida** no azulejo para a amostra, feche a lâmina de **gasodutos sample.**  
8. Na lâmina **DATA FACTORY,** vê-se que os serviços ligados, conjuntos de dados e oleodutos são adicionados à sua fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Descrição Geral da Solução
Este caso de utilização simples pode ser usado como um exemplo de como pode usar a Azure Data Factory para ingerir, preparar, transformar, analisar e publicar dados.

![Fluxo de trabalho ponto a ponto](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta figura mostra como os gasodutos de dados aparecem no portal Azure depois de terem sido implantados.

1. O **PartitionGameLogsPipeline** lê os eventos de jogos brutos a partir do armazenamento de blob e cria divisórias baseadas em ano, mês e dia.
2. O **EnrichGameLogsPipeline** junta eventos de jogos divididos com dados de referência de código geográfico e enriquece os dados mapeando endereços IP para as geolocalizações correspondentes.
3. O pipeline **AnalyzeMarketingCampaignPipeline** utiliza os dados enriquecidos e processa-os com os dados publicitários para criar a saída final que contém a eficácia da campanha de marketing.

Neste exemplo, a Data Factory é usada para orquestrar atividades que copiam dados de entrada, transformam e processam os dados, e forminam os dados finais para uma Base de Dados Azure SQL.  Também pode visualizar a rede de gasodutos de dados, geri-los e monitorizar o seu estado a partir da UI.

## <a name="benefits"></a>Benefícios
Ao otimizar a análise do seu perfil de utilizador e alinhando-a com objetivos de negócio, a empresa de jogos é capaz de recolher rapidamente padrões de utilização e analisar a eficácia das suas campanhas de marketing.

