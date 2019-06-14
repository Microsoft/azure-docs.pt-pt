---
title: Caso de Utilização - Criação de Perfis de Cliente
description: Saiba como o Azure Data Factory é usado para criar um controlado por dados de fluxo de trabalho (pipeline) para criar um perfil de clientes de jogos.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bb7d6531da330bcfbf6de786ffb19984cfd1964e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60487217"
---
# <a name="use-case---customer-profiling"></a>Caso de Utilização - Criação de Perfis de Cliente
O Azure Data Factory é um dos vários serviços utilizados para implementar o Cortana Intelligence Suite de Aceleradores de solução.  Para obter mais informações sobre o Cortana Intelligence, visite [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). Neste documento, descrevemos um caso simples de utilização para ajudar a começar com a compreensão de como o Azure Data Factory pode resolver problemas comuns de análise.

## <a name="scenario"></a>Cenário
A Contoso é uma empresa de jogos que cria jogos para várias plataformas: consolas, dispositivos de mão mantido e computadores pessoais (PCs) de jogos. Como leitores de reproduzir esses jogos, grande volume de dados de log é produzido que controla os padrões de utilização, o estilo de jogos e as preferências do utilizador.  Quando combinado com demográficos, regionais e dados de produto, Contoso pode efetuar a análise para obter orientações sobre como melhorar a experiência dos jogadores e compras de destino-los para atualizações e no jogo. 

Objetivo da Contoso consiste em identificar oportunidades de venda/entre-superior com base no histórico de jogos das respetivas jogadores e adicionar funcionalidades apelativas para impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes. Neste caso, vamos utilizar uma empresa de jogos como um exemplo de uma empresa. A empresa quer otimizar seus jogos com base no comportamento dos jogadores. Esses princípios se aplicam a todas as empresas que desejam interagir com os seus clientes em torno de seus bens e serviços e melhorar a experiência dos clientes.

Nesta solução, a Contoso pretende avaliar a eficácia de uma campanha de marketing, que ele lançou recentemente. Vamos começar com os registos de jogos não processados, processar e enriquecê-los com dados de geolocalização, associe-o com dados de referência de publicidade e por último copiá-los numa base de dados do SQL do Azure para analisar o impacto da campanha.

## <a name="deploy-solution"></a>Implementar a solução
Tudo o que precisa para aceder e experimentar este caso de utilização simples é uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/), uma [conta de armazenamento de Blobs do Azure](../../storage/common/storage-quickstart-create-account.md)e um [base de dados do Azure SQL](../../sql-database/sql-database-get-started.md). Implementar o pipeline de criação de perfis de cliente do **pipelines de exemplo** mosaico na home page de sua fábrica de dados.

1. Crie uma fábrica de dados ou abra uma fábrica de dados existente. Ver [copiar dados de armazenamento de BLOBs para base de dados SQL com o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter os passos criar uma fábrica de dados.
2. Na **DATA FACTORY** painel do data Factory, clique nas **pipelines de exemplo** mosaico.

    ![Mosaico de pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na **pipelines de exemplo** painel, clique no **criação de perfis de cliente** que pretende implementar.

    ![Painel de pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especifica definições de configuração para o exemplo. Por exemplo, o nome da conta de armazenamento do Azure e chave, nome do servidor SQL do Azure, base de dados, ID de utilizador e palavra-passe.

    ![Painel de exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar com a especificação das definições de configuração, clique em **criar** para criar/implementar os pipelines de exemplo e serviços/tabelas ligadas utilizadas pelos pipelines.
6. Ver o estado da implementação no mosaico de exemplo que clicou anteriormente a **pipelines de exemplo** painel.

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir o **implementação concluída com êxito** mensagem no mosaico para o exemplo, feche a **pipelines de exemplo** painel.  
8. No **fábrica de dados** painel, verá que os serviços ligados, conjuntos de dados e pipelines são adicionados à sua fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Descrição geral da solução
Este caso de utilização simples pode ser utilizado como um exemplo de como pode utilizar o Azure Data Factory para ingerir, preparar, transformar, analisar e publicar os dados.

![Fluxo de trabalho ponto a ponto](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta figura ilustra a forma como os pipelines de dados aparecem no portal do Azure após a implementação.

1. O **PartitionGameLogsPipeline** lê os eventos de jogos não processados a partir do armazenamento de BLOBs e cria partições com base no ano, mês e dia.
2. O **EnrichGameLogsPipeline** é associado a eventos de jogos particionados com dados de referência de código de área geográfica e otimiza os dados por mapear endereços IP para as localizações de geo correspondentes.
3. O **AnalyzeMarketingCampaignPipeline** pipeline utiliza os dados plena e processa-os com os dados de publicidade para criar a saída final que contém a eficácia de campanha marketing.

Neste exemplo, o Data Factory é utilizado para orquestrar as atividades que copiar dados de entrada, transformação e processá-los e a saída de dados finais para uma base de dados do SQL do Azure.  Também pode visualizar a rede de pipelines de dados, geri-los e monitorizar o estado da interface do Usuário.

## <a name="benefits"></a>Benefícios
Ao otimizar suas análises do perfil de utilizador e alinhando esta informação com objetivos de negócio, empresa de jogos é capaz de rapidamente padrões de utilização de recolher e analisar a eficiência de suas campanhas de marketing.

