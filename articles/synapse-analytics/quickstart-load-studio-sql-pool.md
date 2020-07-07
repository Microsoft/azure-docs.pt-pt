---
title: Dados de carga a granel com Sinaapse SQL
description: Utilize o Estúdio Synapse para carregar em massa dados de carga em Synapse SQL
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 959742ec3c0434213d19b0f92fe523671fd60f33
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964554"
---
# <a name="bulk-loading-with-synapse-sql"></a>Carregamento a granel com Sinaapse SQL

Os dados de carregamento nunca foram tão fáceis quando se utiliza o assistente de carga a granel no Estúdio Synapse. Este assistente irá guiá-lo através da criação de um script T-SQL com a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) para os dados de carga em massa. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Pontos de entrada para o assistente de carga a granel

Agora pode carregar facilmente dados de carga em massa utilizando piscinas SQL com um simples clique direito nas seguintes áreas dentro do Estúdio Synapse:

- Um ficheiro ou pasta de uma conta de armazenamento Azure anexada ao seu espaço de trabalho ![ Clicar num ficheiro ou pasta a partir de uma conta de armazenamento](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Pré-requisitos

- Este assistente gera uma declaração COPY que utiliza o passe de AAD para a authentcation. O utilizador [da AAD deve ter acesso](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples#d-azure-active-directory-authentication-aad) ao espaço de trabalho com, pelo menos, a função DE Colaborador de Dados de Armazenamento Blob RBAC para a Conta Gen2 da ADLS.

- Tem de ter as [permissões necessárias para utilizar a declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#permissions) e criar permissões de mesa se estiver a criar uma nova tabela para carregar.

- O serviço ligado associado à Conta ADLS Gen2 **deve ter acesso à**pasta de / **folder** ficheiros para carregar. Por exemplo, se o mecanismo de autenticação do serviço ligado for identidade gerida, a identidade gerida pelo espaço de trabalho deve ter pelo menos permissão do leitor blob de armazenamento na conta de armazenamento.

- Se o VNet estiver ativado no seu espaço de trabalho, certifique-se de que o tempo de funcionamento integrado associado aos serviços ligados à Conta ADLS Gen2 para os dados de origem e localização de ficheiros de erro tem a autoria interativa ativada. A autoria interativa é necessária para a deteção automática de esquemas, pré-visualização do conteúdo dos ficheiros de origem e navegação nas contas de armazenamento da ADLS Gen2 dentro do assistente.

### <a name="steps"></a>Passos

1. Selecione a conta de armazenamento e o ficheiro ou pasta que está a carregar no painel de localização de armazenamento Source: ![ Selecionando a localização da origem](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecione as definições do formato de ficheiro, incluindo a conta de armazenamento onde pretende escrever linhas rejeitadas (ficheiro de erro). Atualmente apenas os ficheiros CSV e Parquet são suportados.

    ![Selecionando definições de formato de ficheiro](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Pode clicar em "Pré-visualizar dados" para ver como a declaração COPY irá analisar o ficheiro para o ajudar a configurar as definições do formato de ficheiro. Clique em "Pré-visualizar dados" sempre que alterar uma definição de formato de ficheiro para ver como a declaração COPY analisará o ficheiro com a definição atualizada: ![ Visualizar dados](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Selecione a piscina SQL que está a usar para carregar, incluindo se a carga será para uma mesa ou nova tabela existente: ![ Selecionando a localização do alvo](./sql/media/bulk-load/bulk-load-target-location.png)

5. Clique em "Configure column mapping" para se certificar de que tem o mapeamento de coluna apropriado. Para novas tabelas, configurar o mapeamento da coluna é fundamental para atualizar os tipos de dados da coluna-alvo: ![ Configurar o mapeamento de colunas](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Clique em "Open script" e um script T-SQL será gerado com a declaração COPY para carregar a partir do seu lago de dados: ![ Abrir o script SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Passos seguintes

- Consulte o artigo [de declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) para obter mais informações sobre as capacidades COPY
- Consulte o artigo [da visão geral de carregamento de dados](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt)
