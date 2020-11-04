---
title: 'Tutorial: Conecte a piscina SQL sem servidor ao Power BI Desktop & criar relatório'
description: Neste tutorial, aprenda a ligar a piscina SQL sem servidor em Azure Synapse Analytics ao desktop Power BI e crie um relatório de demonstração baseado numa vista.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: dc2b068dd7c5e7fb3f9e3505f93245515d90ae23
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317198"
---
# <a name="tutorial-use-serverless-sql-pool-with-power-bi-desktop--create-a-report"></a>Tutorial: Use a piscina SQL sem servidor com o Power BI Desktop & criar um relatório

Neste tutorial, irá aprender a:

> [!div class="checklist"]
>
> - Criar base de dados de demonstração
> - Criar vista usada para relatório
> - Conecte o Power BI Desktop à piscina SQL sem servidor
> - Criar relatório com base na visualização

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- [Power BI Desktop](https://powerbi.microsoft.com/downloads/) - necessário para visualizar os dados e criar um relatório.
- [Espaço de trabalho Azure Synapse](https://docs.microsoft.com/azure/synapse-analytics/quickstart-synapse-studio) - necessário para criar base de dados, fonte de dados externa, e visualização.

Opcional:

- Uma ferramenta de consulta SQL, como [o Azure Data Studio,](/sql/azure-data-studio/download-azure-data-studio)ou [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

Valores para os seguintes parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço final do serviço de piscina sem servidor SQL    | Usado como nome de servidor                                   |
| Região de ponto final de serviço de piscina sem servidor SQL     | Usado para determinar o armazenamento utilizado nas amostras |
| Nome de utilizador e senha para acesso ao ponto final | Usado para aceder ao ponto final                               |
| Base de dados que utilizará para criar vistas     | A base de dados utilizada como ponto de partida nas amostras       |

## <a name="1---create-database"></a>1 - Criar base de dados

Para o ambiente de demonstração, crie a sua própria base de dados de demonstração. Utiliza esta base de dados para visualizar metadados, não para armazenar dados reais.

Crie a base de dados de demonstração (e deixe cair uma base de dados existente se necessário) executando o seguinte script Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 - Criar fonte de dados

Uma fonte de dados é necessária para que o serviço de piscina SQL sem servidor aceda a ficheiros no armazenamento. Crie a fonte de dados para uma conta de armazenamento que esteja localizada na mesma região que o seu ponto final. Embora o pool SQL sem servidor possa aceder a contas de armazenamento de diferentes regiões, ter o armazenamento e o ponto final na mesma região proporciona um melhor desempenho.

Crie a fonte de dados executando o seguinte script Transact-SQL (T-SQL):

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 - Preparar vista

Crie a vista com base nos dados de demonstração externa para o Power BI consumir executando o seguinte script Transact-SQL (T-SQL):

Criar a vista `usPopulationView` dentro da base de dados com a seguinte `Demo` consulta:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

Os dados de demonstração contêm os seguintes conjuntos de dados:

População dos EUA por sexo e raça para cada condado dos EUA, proveniente de 2000 e 2010 Censos Decennial em formato parquet.

| Folder path                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /libertação/                                                    | Pasta-mãe para dados na conta de armazenamento de demonstração               |
| /lançamento/us_population_county/                               | Ficheiros de dados populacionais dos EUA no formato Parquet, divididos por ano usando o esquema de partição hive/Hadoop. |

## <a name="4---create-power-bi-report"></a>4 - Criar relatório Power BI

Crie o relatório para Power BI Desktop utilizando os seguintes passos:

1. Abra a aplicação Power BI Desktop e selecione **Obter dados**.

   ![Abrir a aplicação de ambiente de trabalho DO BI e selecionar obter dados.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selecione **Azure**  >  **Azure SQL Database**. 

   ![Selecione fonte de dados.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Digite o nome do servidor onde a base de dados está localizada no campo **Server** e, em seguida, digite `Demo` o nome da base de dados. Selecione a opção **Importar** e, em seguida, selecione **OK**. 

   ![Selecione base de dados no ponto final.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Selecione o método de autenticação preferido:

    - Exemplo para AAD 
  
        ![Clique em Iniciar sessão.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Exemplo para Sessão De Acesso SQL - Digite o nome de utilizador e a palavra-passe.

        ![Utilize o login SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selecione a vista `usPopulationView` e, em seguida, **selecione Carregar**. 

   ![Selecione uma vista na base de dados selecionada.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Aguarde que a operação esteja concluída e, em seguida, aparecerá um pop-up indicando `There are pending changes in your queries that haven't been applied` . **Selecione Aplicar alterações**. 

   ![Clique em aplicar alterações.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Aguarde que a caixa de diálogo **de alterações** de consulta de Aplicação desapareça, o que pode demorar alguns minutos. 

   ![Espere que a consulta termine.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Uma vez concluída a carga, selecione as seguintes colunas neste despacho para criar o relatório:
   - nome do condado
   - população
   - nome do estado

   ![Selecione colunas de interesse para gerar um relatório de mapa.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Uma vez feito o uso deste relatório, elimine os recursos com os seguintes passos:

1. Apagar a credencial para a conta de armazenamento

   ```sql
   DROP EXTENAL DATA SOURCE AzureOpenData
   ```

2. Apagar a vista

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Deixe cair a base de dados

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Passos seguintes

Avance para os [ficheiros de armazenamento de consulta](develop-storage-files-overview.md) para aprender a consultar ficheiros de armazenamento usando o Synapse SQL.
