---
title: Caso de utilização - Perfis de Clientes
description: Saiba como a Azure Data Factory é usada para criar um fluxo de trabalho (pipeline) orientado para dados para perfilar os clientes de jogos.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 313050035694eaeabaefc8e09383ec3a887eb32b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495927"
---
# <a name="use-case---customer-profiling"></a>Caso de utilização - Perfis de Clientes
A Azure Data Factory é um dos muitos serviços utilizados para implementar o Conjunto de Inteligência Cortana de aceleradores de soluções.  Para mais informações sobre a Cortana Intelligence, visite [a Cortana Intelligence Suite.](https://www.microsoft.com/cortanaanalytics) Neste documento, descrevemos um caso de uso simples para ajudá-lo a começar a entender como a Azure Data Factory pode resolver problemas de análise comuns.

## <a name="scenario"></a>Cenário
A Contoso é uma empresa de jogos que cria jogos para várias plataformas: consolas de jogos, dispositivos portáteis e computadores pessoais (PCs). À medida que os jogadores jogam estes jogos, é produzido um grande volume de dados de registo que acompanha os padrões de utilização, o estilo de jogo e as preferências do utilizador.  Quando combinado com dados demográficos, regionais e de produtos, o Contoso pode realizar análises para os orientar sobre como melhorar a experiência dos jogadores e direcioná-los para upgrades e compras dentro do jogo. 

O objetivo da Contoso é identificar oportunidades de venda/venda cruzada baseadas no histórico de jogos dos seus jogadores e adicionar funcionalidades convincentes para impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes. Para este caso de uso, usamos uma empresa de jogos como exemplo de um negócio. A empresa quer otimizar os seus jogos com base no comportamento dos jogadores. Estes princípios aplicam-se a qualquer negócio que queira envolver os seus clientes em torno dos seus bens e serviços e melhorar a experiência dos seus clientes.

Nesta solução, a Contoso quer avaliar a eficácia de uma campanha de marketing que lançou recentemente. Começamos com os registos de jogos brutos, processamos e enriquecemos com dados de geolocalização, juntamo-nos a ele com dados de referência publicitários e, por último, copiamos-os numa Base de Dados Azure SQL para analisar o impacto da campanha.

## <a name="deploy-solution"></a>Implementar solução
Tudo o que precisa de aceder e experimentar este caso de utilização simples é uma [subscrição Azure,](https://azure.microsoft.com/pricing/free-trial/)uma [conta de armazenamento Azure Blob](../../storage/common/storage-account-create.md)e uma [Base de Dados Azure SQL](../../azure-sql/database/single-database-create-quickstart.md). Você implanta o pipeline de perfis do cliente a partir do azulejo **sample pipelines** na página inicial da sua fábrica de dados.

1. Criar uma fábrica de dados ou abrir uma fábrica de dados existente. Consulte [os dados de cópia do Blob Storage para a BASE de Dados SQL utilizando](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a Data Factory para obter medidas para criar uma fábrica de dados.
2. Na lâmina **data factory** para a fábrica de dados, clique no azulejo da **amostra.**

    ![Azulejos de amostras](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na lâmina dos **gasodutos Sample,** clique no perfil do **Cliente** que pretende implantar.

    ![Lâmina de gasodutos de amostra](./media/data-factory-samples/SampleTile.png)
4. Especifique as definições de configuração para a amostra. Por exemplo, o nome da sua conta de armazenamento Azure e chave, nome lógico do servidor SQL, base de dados, ID do utilizador e senha.

    ![Lâmina de amostra](./media/data-factory-samples/SampleBlade.png)
5. Depois de ter terminado a especificação das definições de configuração, clique em **Criar** para criar/implantar os gasodutos de amostra e serviços/tabelas ligados utilizados pelos oleodutos.
6. Vê o estado de implantação no azulejo da amostra que clicou anteriormente na lâmina dos **gasodutos Sample.**

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir a mensagem **de implementação conseguida** no azulejo para a amostra, feche a lâmina dos **gasodutos sample.**  
8. Na lâmina **data factory,** você vê que serviços ligados, conjuntos de dados e oleodutos são adicionados à sua fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Descrição Geral da Solução
Este simples caso de utilização pode ser usado como um exemplo de como pode usar a Azure Data Factory para ingerir, preparar, transformar, analisar e publicar dados.

![Fluxo de trabalho ponto a ponto](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Este Número mostra como os gasodutos de dados aparecem no portal Azure depois de terem sido implantados.

1. O **PartitionGameLogsPipeline** lê os eventos de jogos crus do armazenamento de bolhas e cria divisórias com base no ano, mês e dia.
2. O **EnrichGameLogsPipeline** junta eventos de jogos divididos com dados de referência de código geo e enriquece os dados mapeando endereços IP para as geo-localizações correspondentes.
3. O pipeline **AnalyzeMarketingCampaignPipeline** utiliza os dados enriquecidos e processa-os com os dados publicitários para criar a produção final que contém a eficácia da campanha de marketing.

Neste exemplo, a Data Factory é usada para orquestrar atividades que copiam dados de entrada, transformam e processam os dados, e produzem os dados finais para uma Base de Dados Azure SQL.  Também pode visualizar a rede de oleodutos de dados, geri-los e monitorizar o seu estado a partir da UI.

## <a name="benefits"></a>Benefícios
Ao otimizar a análise do seu perfil de utilizador e alinhá-lo com objetivos de negócio, a empresa de jogos é capaz de recolher rapidamente padrões de utilização e analisar a eficácia das suas campanhas de marketing.