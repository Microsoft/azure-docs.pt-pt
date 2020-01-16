---
title: Caso de Utilização - Criação de Perfis de Cliente
description: Saiba como Azure Data Factory é usado para criar um fluxo de trabalho controlado por dados (Pipeline) para fazer o perfil de clientes de jogos.
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
ms.openlocfilehash: c570f988dea894b8106405f4e427edb386a3e74a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969297"
---
# <a name="use-case---customer-profiling"></a>Caso de Utilização - Criação de Perfis de Cliente
Azure Data Factory é um dos muitos serviços usados para implementar o Cortana Intelligence Suite de aceleradores de solução.  Para obter mais informações sobre Cortana Intelligence, visite [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). Neste documento, descrevemos um caso de uso simples para ajudá-lo a começar a entender como Azure Data Factory pode resolver problemas comuns de análise.

## <a name="scenario"></a>Cenário
A contoso é uma empresa de jogos que cria jogos para várias plataformas: consoles de jogos, dispositivos de mão e computadores pessoais (PCs). À medida que os jogadores desempenham esses jogos, grandes volumes de dados de log são produzidos para acompanhar os padrões de uso, o estilo de jogos e as preferências do usuário.  Quando combinados com dados demográficos, regionais e de produtos, a Contoso pode executar análises para orientá-los sobre como aprimorar a experiência dos jogadores e direcioná-los para atualizações e compras em jogos. 

A meta da Contoso é identificar oportunidades de venda/venda cruzada com base no histórico de jogos de seus jogadores e adicionar recursos atraentes para impulsionar o crescimento dos negócios e fornecer uma experiência melhor aos clientes. Para esse caso de uso, usamos uma empresa de jogos como um exemplo de negócios. A empresa deseja otimizar seus jogos com base no comportamento dos jogadores. Esses princípios se aplicam a qualquer empresa que queira envolver seus clientes em todos os seus bens e serviços e aprimorar a experiência de seus clientes.

Nesta solução, a contoso deseja avaliar a eficácia de uma campanha de marketing que foi iniciada recentemente. Começamos com os logs brutos de jogos, processamos-os e enriquecemos-os com dados de localização geográfica, unem-nos com dados de referência de anúncio e os copiamos por último em um banco de dado SQL do Azure para analisar o impacto da campanha.

## <a name="deploy-solution"></a>Implantar solução
Tudo o que você precisa para acessar e testar esse caso de uso simples é uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/), uma [conta de armazenamento de BLOBs do Azure](../../storage/common/storage-account-create.md)e um [banco de dados SQL do Azure](../../sql-database/sql-database-get-started.md). Você implanta o pipeline de criação de perfil do cliente do bloco **pipelines de exemplo** no Home Page do seu data Factory.

1. Crie um data factory ou abra um data factory existente. Confira [copiar dados do armazenamento de BLOBs para o SQL Database usando data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter as etapas para criar um data Factory.
2. Na folha **Data Factory** do data Factory, clique no bloco **pipelines de exemplo** .

    ![Bloco pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na folha **pipelines de exemplo** , clique na **criação de perfil do cliente** que você deseja implantar.

    ![Folha pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especifique as definições de configuração para o exemplo. Por exemplo, o nome e a chave da conta de armazenamento do Azure, o nome do Azure SQL Server, o banco de dados, a ID de usuário e a senha.

    ![Folha de exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de concluir a especificação das definições de configuração, clique em **criar** para criar/implantar os pipelines de exemplo e os serviços/tabelas vinculados usados pelos pipelines.
6. Você verá o status da implantação no bloco de exemplo em que você clicou anteriormente na folha **pipelines de exemplo** .

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando você vir a mensagem **implantação bem-sucedida** no bloco do exemplo, feche a folha **pipelines de exemplo** .  
8. Na folha **Data Factory** , você vê que os serviços vinculados, os conjuntos de dados e os pipelines são adicionados à sua data Factory.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Descrição Geral da Solução
Esse caso de uso simples pode ser usado como um exemplo de como você pode usar Azure Data Factory para ingerir, preparar, transformar, analisar e publicar dados.

![Fluxo de trabalho ponto a ponto](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta figura descreve como os pipelines de dados aparecem no portal do Azure depois que eles são implantados.

1. O **PartitionGameLogsPipeline** lê os eventos brutos do jogo do armazenamento de BLOBs e cria partições com base no ano, mês e dia.
2. O **EnrichGameLogsPipeline** une eventos de jogos particionados com dados de referência de código geográfico e enriquece os dados Mapeando endereços IP para as localizações geográficas correspondentes.
3. O pipeline **AnalyzeMarketingCampaignPipeline** usa os dados aprimorados e os processa com os dados de publicidade para criar a saída final que contém a eficácia da campanha de marketing.

Neste exemplo, Data Factory é usado para orquestrar atividades que copiam dados de entrada, transformam e processam os dados e geram os dados finais para um banco de dado SQL do Azure.  Você também pode visualizar a rede de pipelines de dados, gerenciá-los e monitorar seu status da interface do usuário.

## <a name="benefits"></a>Vantagens
Otimizando sua análise de perfil de usuário e alinhando-a com as metas de negócios, a empresa de jogos é capaz de coletar rapidamente os padrões de uso e analisar a eficácia de suas campanhas de marketing.

