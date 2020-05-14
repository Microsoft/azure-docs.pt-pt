---
title: Dados de carga a granel com Synapse SQL
description: Use o Estúdio Synapse para carregar em massa dados em Synapse SQL
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 96e79fdfeed5b56a4e0a33229f419f439b20b04c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124542"
---
# <a name="bulk-loading-with-synapse-sql"></a>Carregamento a granel com Synapse SQL

Os dados de carregamento nunca foram tão fáceis quando se utiliza o assistente de carga a granel no Estúdio Synapse. Este assistente irá guiá-lo através da criação de um script T-SQL com a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) para dados de carga em massa. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Pontos de entrada para o assistente de carga a granel

Agora pode carregar facilmente dados de carga a granel usando piscinas SQL com um simples clique direito nas seguintes áreas dentro do Estúdio Synapse:

- Um ficheiro ou pasta de uma conta de armazenamento Azure anexada ao seu espaço de trabalho Clique num ficheiro ou pasta a partir de uma conta de ![ armazenamento](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter acesso ao espaço de trabalho com, pelo menos, a função RBAC do Colaborador de Dados do Armazenamento Blob para a Conta ADLS Gen2.

- Deve ter as [permissões necessárias para utilizar a declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#permissions) e criar permissões de mesa se estiver a criar uma nova tabela para carregar.

- O serviço vinculado associado à Conta ADLS Gen2 deve ter acesso à pasta **de ficheiros** / **folder** para carregar. Por exemplo, se o mecanismo de autenticação do serviço ligado for identidade gerida, a identidade gerida pelo espaço de trabalho deve ter pelo menos permissão do leitor de armazenamento blob na conta de armazenamento.

- Se o VNet estiver ativado no seu espaço de trabalho, certifique-se de que o tempo de funcionamento integrado associado aos serviços ligados à Conta ADLS Gen2 para os dados de origem e localização de ficheiros de erro tem a autoria interativa ativada. A autoria interativa é necessária para deteção automática de esquemas, pré-visualização do conteúdo do ficheiro de origem e navegação nas contas de armazenamento ADLS Gen2 dentro do assistente.

### <a name="steps"></a>Passos

1. Selecione a conta de armazenamento e o ficheiro ou pasta a partir do painel de localização de armazenamento Fonte: ![ Selecionando a localização da fonte](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecione as definições do formato de ficheiro, incluindo a conta de armazenamento onde pretende escrever linhas rejeitadas (ficheiro de erro). Atualmente apenas os ficheiros CSV e Parquet são suportados.

    ![Seleção de definições de formato de ficheiro](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Pode clicar em "Dados de Pré-visualização" para ver como a declaração copy irá analisar o ficheiro para o ajudar a configurar as definições do formato de ficheiro. Clique em "Dados de Pré-visualização" sempre que alterar uma definição de formato de ficheiro para ver como a declaração do COPY analisará o ficheiro com a definição atualizada: ![ Dados de pré-visualização](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Selecione o pool SQL que está a usar para carregar, incluindo se a carga será para uma tabela ou nova tabela existente: ![ Selecionando a localização do alvo](./sql/media/bulk-load/bulk-load-target-location.png)

5. Clique em "Configurar o mapeamento da coluna" para se certificar de que tem o mapeamento adequado da coluna. Para novas tabelas, configurar o mapeamento da coluna é fundamental para atualizar os tipos de dados da coluna de destino: Configurar o mapeamento da ![ coluna](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Clique em "Open script" e um script T-SQL será gerado com a declaração COPY para carregar a partir do seu lago de dados: ![ Abertura do script SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo de declaração da [COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) para obter mais informações sobre as capacidades do COPY
- Verifique o artigo sobre [visão geral](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) dos dados
