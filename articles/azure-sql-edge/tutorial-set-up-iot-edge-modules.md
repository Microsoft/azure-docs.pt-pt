---
title: Configurar módulos IoT Edge em Azure SQL Edge
description: Na segunda parte deste tutorial de três partes do Azure SQL Edge para prever impurezas de minério de ferro, você vai configurar módulos e conexões IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 7b2432fda70e8f9a5fa8bc64ede846d977672e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886488"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurar módulos e ligações IoT Edge

Na segunda parte deste tutorial em três partes para prever impurezas de minério de ferro em Azure SQL Edge, você vai configurar os seguintes módulos IoT Edge:

- SQL do Azure no Edge
- Módulo IoT Edge do gerador de dados

## <a name="specify-container-registry-credentials"></a>Especificar credenciais de registo de contentores

As credenciais dos registos dos contentores que acolhem imagens do módulo devem ser especificadas. Estes podem ser encontrados no registo de contentores que foi criado no seu grupo de recursos. Navegue para a secção **Chaves de Acesso.** Tome nota dos seguintes campos:

- Nome de registo
- Servidor de início de sessão
- Nome de utilizador
- Palavra-passe

Agora, especifique as credenciais do recipiente no módulo IoT Edge.

1. Navegue para o hub IoT que foi criado no seu grupo de recursos.

2. Na secção **IoT Edge,** sob **Gestão Automática de Dispositivos,** clique em **ID do dispositivo**. Para este tutorial, o ID é `IronOrePredictionDevice` .

3. Selecione a secção **de Módulos de Conjunto.**

4. Nas **credenciais de registo de contentores,** introduza os seguintes valores:

   _Campo_|_Valor_
   -------|-------
   Nome|Nome de registo
   Endereço|Servidor de início de sessão
   Nome de Utilizador|Nome de utilizador
   Palavra-passe|Palavra-passe
  
## <a name="deploy-the-data-generator-module"></a>Implementar o módulo gerador de dados

1. Na secção **IoT Edge,** sob **Gestão Automática de Dispositivos,** clique em **ID do dispositivo**. Para este tutorial, o ID é `IronOrePredictionDevice` e, em seguida, clique em **Definir Módulos**.

2.  Sob a secção **módulos IoT Edge** nos **módulos set no dispositivo:** página, clique **+ ADD** e selecione o **módulo IoT Edge**.

3. Forneça um nome e imagem válidos URI para o módulo IoT Edge.
   O Image URI pode ser encontrado no registo de contentores no grupo de recursos criado na primeira parte deste tutorial. Selecione a secção **repositórios** em **Serviços**. Para este tutorial, escolha o repositório `silicaprediction` nomeado. Selecione a etiqueta apropriada. O Image URI será do formato:

   *servidor de login da regiessão de* / contentores *nome do repositório*:*nome da etiqueta*

   Por exemplo:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

4. Deixe a *Política de Reinício* e os campos *de Estado Desejado* como está.

5. Clique em **Adicionar**.


## <a name="deploy-the-azure-sql-edge-module"></a>Implementar o módulo Azure SQL Edge

1. Implementar o módulo Azure SQL Edge clicando em **+ Adicionar** e, em seguida, **Módulo Marketplace**. 

2. Na lâmina **IoT Edge Module Marketplace,** procure *o Azure SQL Edge* e escolha *O Azure SQL Edge Developer*. 

3. Clique no módulo *Azure SQL Edge* recém-adicionado em **Módulos IoT Edge** para configurar o módulo Azure SQL Edge. Para obter mais informações sobre as opções de configuração, consulte [Implementar Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

4. Adicione a `MSSQL_PACKAGE` variável ambiental à implementação do módulo *Azure SQL Edge* e especifique o URL SAS do ficheiro dacpac da base de dados criado no passo 8 da [Parte um](tutorial-deploy-azure-resources.md) deste tutorial.

5. Clique **na atualização**

6. Nos **módulos set na** página do dispositivo, clique em **Seguinte: Rotas >**.

7. No painel de rotas dos módulos set na página do **dispositivo, especifique** as rotas do módulo para a comunicação do hub IoT Edge, conforme descrito abaixo. Certifique-se de atualizar os nomes do módulo nas definições de rota abaixo.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Por exemplo:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Nos **módulos set na** página do dispositivo, clique em **Seguinte: Review + create >**

8. Nos **módulos Conjunto na** página do dispositivo, clique em **Criar**

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Crie e inicie o Trabalho de Streaming T-SQL em Azure SQL Edge.

1. Abra o Azure Data Studio.

2. No separador **Welcome,** inicie uma nova ligação com os seguintes detalhes:

   |_Campo_|_Valor_|
   |-------|-------|
   |Tipo de ligação| Microsoft SQL Server|
   |Servidor|Endereço IP público mencionado no VM que foi criado para esta demonstração|
   |Nome de utilizador|SG|
   |Palavra-passe|A palavra-passe forte que foi usada ao criar a instância Azure SQL Edge|
   |Base de Dados|Predefinição|
   |Grupo de servidores|Predefinição|
   |Name (optional) (Nome [opcional])|Fornecer um nome opcional|

3. Clique **em Ligar**

4. No separador **menu 'Ficheiro',** abra um novo portátil ou utilize o atalho do teclado Ctrl + N.

5. Na nova janela de consulta, execute o script abaixo para criar o trabalho de streaming T-SQL. Antes de executar o script, certifique-se de alterar as seguintes variáveis. 
   - *SQL_SA_Password:* O valor MSSQL_SA_PASSWORD especificado durante a implantação do Módulo de Borda Azure SQL. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Utilize a seguinte consulta para verificar se os dados do módulo de geração de dados estão a ser transmitidos para a base de dados. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


Neste tutorial, implantamos o módulo gerador de dados e o módulo SQL Edge. Em seguida, criámos um trabalho de streaming para transmitir os dados gerados pelo módulo de geração de dados para o SQL. 

## <a name="next-steps"></a>Passos Seguintes

- [Implementar o modelo ML na Borda Azure SQL utilizando ONNX](tutorial-run-ml-model-on-sql-edge.md)
