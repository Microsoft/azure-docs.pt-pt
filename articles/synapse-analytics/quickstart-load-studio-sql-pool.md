---
title: 'Quickstart: Dados de carga a granel com uma piscina SQL dedicada'
description: Utilize o Synapse Studio para carregar em massa dados de carga numa piscina DE SQL dedicada em Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: be15a37a9a2965da36f7e8f884a0a3112106b9ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586731"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Quickstart: Carregamento a granel com Estúdio Synapse

Os dados de carregamento são fáceis com o assistente de carga a granel no Synapse Studio. O Synapse Studio é uma característica da Azure Synapse Analytics. O assistente de carga a granel guia-o através da criação de um script T-SQL com a [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) para carregar em massa dados numa piscina DE SQL dedicada. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Pontos de entrada para o assistente de carga a granel

Pode carregar em massa dados clicando na seguinte área dentro do Synapse Studio: um ficheiro ou pasta de uma conta de armazenamento Azure que está anexada ao seu espaço de trabalho.

![Screenshot que mostra um clique direito de um ficheiro ou pasta de uma conta de armazenamento.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Pré-requisitos

- O assistente gera uma declaração COPY, que utiliza o diretório Azure Ative (Azure AD) para autenticação. O utilizador [Azure AD deve ter acesso](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) ao espaço de trabalho com, pelo menos, o papel de Azure Data Contributor Azure para a conta Azure Data Lake Storage Gen2. 

- Tem de ter as [permissões necessárias para utilizar a declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) e criar permissões de Tabela se estiver a criar uma nova tabela para carregar.

- O serviço ligado associado à conta Data Lake Storage Gen2 *deve ter acesso ao ficheiro ou pasta* para carregar. Por exemplo, se o mecanismo de autenticação do serviço ligado for uma identidade gerida, a identidade gerida do espaço de trabalho deve ter pelo menos a permissão do Leitor de Dados blob de armazenamento na conta de armazenamento.

- Se uma rede virtual estiver ativada no seu espaço de trabalho, certifique-se de que o tempo de funcionamento integrado associado aos serviços ligados da conta Data Lake Storage Gen2 para os dados de origem e localização de ficheiros de erro tem a autoria interativa ativada. A autoria interativa é necessária para a deteção de autoschema, pré-visualização do conteúdo do ficheiro de origem e navegação nas contas de armazenamento de Data Lake Gen2 dentro do assistente.

## <a name="steps"></a>Passos

1. No painel **de localização de armazenamento Source,** selecione a conta de armazenamento e o ficheiro ou pasta a partir do qual está a carregar. O assistente tenta automaticamente detetar ficheiros Parquet e ficheiros de texto delimitado (CSV), incluindo mapear os campos de origem do ficheiro para os tipos de dados SQL alvo apropriados. 

   ![Screenshot que mostra a seleção de uma localização de origem.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecione as definições do formato de ficheiro, incluindo as definições de erro para quando há linhas rejeitadas durante o processo de carga a granel. Também pode selecionar **dados de pré-visualização** para ver como a declaração COPY irá analisar o ficheiro para o ajudar a configurar as definições do formato de ficheiro. Selecione **os dados de pré-visualização** sempre que alterar uma definição de formato de ficheiro, para ver como a declaração COPY analisará o ficheiro com a definição atualizada.

   ![Screenshot que mostra dados de pré-visualização.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - O assistente de carga em massa não suporta a pré-visualização dos dados com exterminadores de campo de vários caracteres. Quando especificar um exterminador de campo de vários caracteres, o assistente irá visualizar os dados dentro de uma única coluna. 
   > - Quando selecionar **os nomes das colunas Infer,** o assistente de carga a granel analisará os nomes das colunas da primeira linha especificada pelo campo **da primeira linha.** O assistente de carga a granel irá incrementar automaticamente o `FIRSTROW` valor na declaração COPY por 1 para ignorar esta linha do cabeçalho. 
   > - Especificar exterminadores de linha de vários caracteres é suportado na declaração COPY. No entanto, o assistente de carga a granel não o suporta e lançará um erro.

3. Selecione a piscina SQL dedicada que está a usar para carregar, incluindo se a carga será para uma mesa existente ou para uma nova mesa.
   ![Screenshot que mostra a seleção de um local de alvo.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Selecione **o mapeamento de colunas configurar** para se certificar de que tem o mapeamento de colunas apropriado. Os nomes das colunas de notas serão detetados automaticamente se ativar **os nomes das colunas Infer**. Para novas tabelas, configurar o mapeamento da coluna é fundamental para atualizar os tipos de dados da coluna-alvo.

   ![Screenshot que mostra o mapeamento de colunas configurantes.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Selecione **o script aberto**. Um script T-SQL é gerado com a declaração COPY para carregar a partir do seu lago de dados.
   ![Screenshot que mostra a abertura do script SQL.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Passos seguintes

- Consulte o artigo [de declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) para obter mais informações sobre as capacidades COPY.
- Consulte o artigo [de visão geral de carregamento de dados](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) para obter informações sobre a utilização de um processo de extrato, transformação e carga (ETL).
