---
title: 'Tutorial: Ligue o SQL a pedido (pré-visualização) ao Power BI Desktop & criar relatório'
description: Neste tutorial, aprenda a ligar o SQL on-demand (pré-visualização) no Azure Synapse Analytics ao power bi desktop e crie um relatório de demonstração baseado numa vista.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e0ac6ccde2443a7b374d9eb85f6f960af79c69dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769486"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Tutorial: Ligue o SQL a pedido (pré-visualização) ao Power BI Desktop & criar relatório

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar base de dados de demonstração
> - Criar vista usada para relatório
> - Ligar ao Power BI Desktop
> - Criar relatório com base na vista

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa do seguinte software:

- Uma ferramenta de consulta SQL, como [o Azure Data Studio,](/sql/azure-data-studio/download-azure-data-studio)ou [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

Valores para os seguintes parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço final do serviço sql on-demand    | Usado como nome do servidor                                   |
| Região de ponto final do serviço sql a pedido     | Usado para determinar o armazenamento usado nas amostras |
| Nome de utilizador e senha para acesso ao ponto final | Usado para aceder ao ponto final                               |
| Base de Dados que utilizará para criar pontos de vista     | A base de dados usada como ponto de partida nas amostras       |

## <a name="1---create-database"></a>1 - Criar base de dados

Para o ambiente de demonstração, crie a sua própria base de dados de demonstração. Utiliza esta base de dados para visualizar metadados, não para armazenar dados reais.

Crie a base de dados de demonstração (e deixe cair uma base de dados existente, se necessário) executando o seguinte script Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Criar credencial

É necessária uma credencial para que o serviço SQL on-demand aceda a ficheiros armazenados. Crie a credencial para uma conta de armazenamento que esteja localizada na mesma região que o seu ponto final. Embora a SQL a pedido possa aceder a contas de armazenamento de diferentes regiões, ter o armazenamento e o ponto final na mesma região proporciona um melhor desempenho.

Crie a credencial executando o seguinte script Transact-SQL (T-SQL):

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - Preparar vista

Crie a visão com base nos dados externos de demonstração para que o Power BI consuma, executando o seguinte script Transact-SQL (T-SQL):

Crie `usPopulationView` a vista `Demo` dentro da base de dados com a seguinte consulta:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

Os dados da demonstração contêm os seguintes conjuntos de dados:

População americana por sexo e raça para cada condado dos EUA provém de 2000 e 2010 Censos Decennial em formato parquet.

| Folder path                                                  | Descrição                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /libertação/                                                    | Pasta dos pais para dados na conta de armazenamento de demonstração               |
| /libertação/us_population_county/                               | Ficheiros de dados da população dos EUA em formato Parquet, divididos por ano usando o esquema de partição Hive/Hadoop. |

## <a name="4---create-power-bi-report"></a>4 - Criar relatório Power BI

Crie o relatório para o Power BI Desktop utilizando os seguintes passos:

1. Abra a aplicação Power BI Desktop e selecione **Obter dados**.

   ![Abra a aplicação de ambiente de trabalho Do Power BI e selecione obter dados.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selecione Base**de Dados SQL** **Azure** > Azure . 

   ![Selecione fonte de dados.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Digite o nome do servidor onde **Server** a base de `Demo` dados está localizada no campo Server e, em seguida, digite o nome da base de dados. Selecione a opção **Import** e, em seguida, selecione **OK**. 

   ![Selecione base de dados no ponto final.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Selecione o método de autenticação preferido:

    - Exemplo para AAD 
  
    ![Clique em Iniciar sessão.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Exemplo para SQL Login - Digite o seu nome de utilizador e palavra-passe.

    ![Utilize o login SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selecione `usPopulationView`a vista e, em seguida, selecione **Carregar**. 

   ![Selecione uma Visualização na base de dados selecionada.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Aguarde a operação e, em seguida, `There are pending changes in your queries that haven't been applied`aparecerá um pop-up afirmando . Selecione **Aplicar alterações**. 

   ![Clique em alterar alterações.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Aguarde que a caixa de dialog alterações de **consulta de aplicação** desapareça, o que pode demorar alguns minutos. 

   ![Espere uma consulta para terminar.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Uma vez concluída a carga, selecione as seguintes colunas nesta forma para criar o relatório:
   - condadoNome
   - população
   - estadoNome

   ![Selecione colunas de interesse para gerar um relatório de mapa.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Limpar recursos

Uma vez feito o uso deste relatório, elimine os recursos com os seguintes passos:

1. Eliminar a credencial da conta de armazenamento

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Apagar a vista

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Largue a base de dados

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Passos seguintes

Avance para os [ficheiros](develop-storage-files-overview.md) de armazenamento da Consulta para saber como consultar ficheiros de armazenamento usando synapse SQL.
