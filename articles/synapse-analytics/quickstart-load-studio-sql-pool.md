---
title: 'Quickstart: Dados de carga a granel com piscina SQL dedicada'
description: Utilize o Synapse Studio para carregar em massa dados de carga em piscina SQL dedicada em Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 312c57c103bf733bc72c5de1d22ab3239d5b5e96
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484719"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Quickstart: Carregamento a granel com Sinapse SQL

Os dados de carregamento são fáceis com o assistente de carga a granel no Synapse Studio. O assistente de carga em massa irá guiá-lo através da criação de um script T-SQL com a [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) para carregar em massa dados de carga. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Pontos de entrada para o assistente de carga a granel

Pode carregar facilmente dados de carga em massa utilizando piscinas SQL dedicadas com um simples clique à direita nas seguintes áreas dentro do Estúdio Synapse:

- Um ficheiro ou pasta de uma conta de armazenamento Azure anexada ao seu espaço de trabalho ![ Clicar num ficheiro ou pasta a partir de uma conta de armazenamento](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Pré-requisitos

- Este assistente gera uma declaração COPY, que utiliza o passe AD AD do Azure para autenticação. O utilizador [Azure AD deve ter acesso](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) ao espaço de trabalho com, pelo menos, a função Azure do Colaborador de Dados de Armazenamento blob para a Conta ADLS Gen2. 

- Tem de ter as [permissões necessárias para utilizar a declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) e criar permissões de mesa se estiver a criar uma nova tabela para carregar.

- O serviço ligado associado à Conta ADLS Gen2 **deve ter acesso à** pasta de / **folder** ficheiros para carregar. Por exemplo, se o mecanismo de autenticação do serviço ligado for identidade gerida, a identidade gerida pelo espaço de trabalho deve ter pelo menos permissão do leitor blob de armazenamento na conta de armazenamento.

- Se o VNet estiver ativado no seu espaço de trabalho, certifique-se de que o tempo de funcionamento integrado associado aos serviços ligados à Conta ADLS Gen2 para os dados de origem e localização de ficheiros de erro tem a autoria interativa ativada. A autoria interativa é necessária para a deteção de autoschema, pré-visualização do conteúdo dos ficheiros de origem e navegação nas contas de armazenamento da ADLS Gen2 dentro do assistente.

### <a name="steps"></a>Passos

1. Selecione a conta de armazenamento e o ficheiro ou pasta que está a carregar no painel de localização de armazenamento Source. O assistente tentará automaticamente detetar ficheiros Parquet. Se o tipo de ficheiro Parquet não puder ser confirmado, o texto delimitado (CSV) será utilizado por predefinição.

   ![Selecionando a localização da fonte](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecione as definições do formato de ficheiro, incluindo a conta de armazenamento onde pretende escrever linhas rejeitadas (ficheiro de erro). Atualmente apenas os ficheiros CSV e Parquet são suportados.

    ![Selecionando definições de formato de ficheiro](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Pode selecionar "Pré-visualizar dados" para ver como a declaração COPY irá analisar o ficheiro para o ajudar a configurar as definições do formato de ficheiro. Selecione "Preview data" sempre que alterar uma definição de formato de ficheiro para ver como a declaração COPY analisará o ficheiro com a definição atualizada: ![ Visualizar dados](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - A pré-visualização dos dados com terminadores de campo de vários caracteres não é suporte no assistente de carga a granel. O assistente de carga em massa irá visualizar os dados numa única coluna quando for especificado um exterminador de campo de vários caracteres. 
> - Especificar terminadores de linha de vários caracteres é suportado na declaração COPY; no entanto, isto não é suportado no assistente de carga a granel onde um erro será lançado.

4. Selecione a piscina SQL dedicada que está a usar para carregar, incluindo se a carga será para uma mesa ou nova tabela existente: ![ Selecionando a localização do alvo](./sql/media/bulk-load/bulk-load-target-location.png)

5. Selecione "Configure column mapping" para se certificar de que tem o mapeamento de coluna apropriado. Os nomes das colunas de notas serão automaticamente detetados se "Infer column names" estiverem ativados. Para novas tabelas, configurar o mapeamento da coluna é fundamental para atualizar os tipos de dados da coluna-alvo: ![ Configurar o mapeamento de colunas](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Selecione "Open script" e um script T-SQL será gerado com a declaração COPY para carregar a partir do seu lago de dados: ![ Abrir o script SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Passos seguintes

- Consulte o artigo [de declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) para obter mais informações sobre as capacidades COPY
- Consulte o artigo [da visão geral de carregamento de dados](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)
