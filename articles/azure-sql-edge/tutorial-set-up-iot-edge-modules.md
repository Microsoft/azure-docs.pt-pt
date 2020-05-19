---
title: Configurar módulos IoT Edge em Azure SQL Edge
description: Na segunda parte deste tutorial Azure SQL Edge em três partes para prever impurezas de minério de ferro, você vai configurar módulos e ligações IoT Edge.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599686"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurar módulos e ligações IoT Edge

Na segunda parte deste tutorial em três partes para prever impurezas de minério de ferro em Azure SQL Edge, você vai configurar os seguintes módulos IoT Edge:

- Borda SQL Azure
- Módulo IoT Edge gerador de dados

## <a name="create-azure-stream-analytics-module"></a>Criar módulo Azure Stream Analytics

Crie um módulo Azure Stream Analytics que será utilizado neste tutorial. Para saber mais sobre o uso de trabalhos de streaming com o SQL Edge, consulte [Utilizar trabalhos de streaming com a SQL Database Edge](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

Assim que o trabalho do Azure Stream Analytics for criado com o ambiente de hospedagem definido como Edge, configurar as inputs e saídas para o tutorial.

1. Para criar a **entrada,** clique em **+Adicionar entrada**de fluxo . Preencha a secção de detalhes utilizando as seguintes informações:

   Campo|Valor
   -----|-----
   Formato de Serialização de Eventos|JSON
   Encoding|UTF-8
   Tipo de compressão de eventos|Nenhum

2. Para criar a **saída,** clique em **+Adicionar** e escolher base de dados SQL. Preencha a secção de detalhes utilizando as seguintes informações.

   > [!NOTE]
   > A palavra-passe especificada nesta secção precisa de ser especificada para a palavra-passe SQL SA ao implementar o módulo SQL Edge na secção **"Implementar o módulo Azure SQL Edge".**

   Campo|Valor
   -----|-----
   Base de Dados|Previsão IronOreSilica
   Nome do servidor|tcp:.1433
   Nome de utilizador|SG
   Palavra-passe|Especificar uma senha forte
   Tabela|IronOreMeasurements1

3. Navegue para a secção **Consulta** e instale a consulta da seguinte forma:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. Em **Configurar, selecione** **Publicar**e, em seguida, selecione o botão **Publicar.** Guarde o SAS URI para utilização com o módulo SQL Database Edge.

## <a name="specify-container-registry-credentials"></a>Especificar credenciais de registo de contentores

As credenciais para os registos de contentores que hospedam imagens do módulo devem ser especificadas. Estes podem ser encontrados no registo de contentores que foi criado no seu grupo de recursos. Navegue para a secção Chaves de **Acesso.** Tome nota dos seguintes campos:

- Nome de registo
- Servidor de início de sessão
- Nome de utilizador
- Palavra-passe

Agora, especifique as credenciais do recipiente no módulo IoT Edge.

1. Navegue para o centro ioT que foi criado no seu grupo de recursos.

2. Na secção **IoT Edge** em **Gestão Automática de Dispositivos,** clique no **ID do dispositivo**. Para este tutorial, o ID é `IronOrePredictionDevice` .

3. Selecione a secção **Deconjunto de Módulos.**

4. Sob as credenciais de registo de **contentores,** insira os seguintes valores:

   _Campo_|_Valor_
   -------|-------
   Name|Nome de registo
   Endereço|Servidor de início de sessão
   Nome de Utilizador|Nome de utilizador
   Palavra-passe|Palavra-passe
  
## <a name="deploy-the-data-generator-module"></a>Implementar o módulo gerador de dados

1. Na secção **Módulos IoT Edge,** clique **+ ADD** e selecione **módulo IoT Edge**.

2. Forneça um nome de módulo IoT Edge e a imagem URI.
   A imagem URI pode ser encontrada no registo de contentores no grupo de recursos. Selecione a secção **Repositórios** nos **Serviços**. Para este tutorial, escolha o repositório nomeado `silicaprediction` . Selecione a etiqueta apropriada. A Imagem URI será do formato:

   *servidor de login do registo* / de contentores *nome do repositório*:*nome de etiqueta*

   Por exemplo:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. **Adicione**click .

## <a name="deploy-the-azure-sql-edge-module"></a>Implementar o módulo Azure SQL Edge

1. Implemente o módulo Azure SQL Edge seguindo os passos listados na implementação de borda de base de [dados Azure SQL](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge).

2. Na **rota de especificação** da página **Delinemódulos,** especifique as rotas para o módulo para a comunicação do hub IoT Edge da seguinte forma. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Por exemplo:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Nas configurações gémeas do **módulo,** certifique-se de que o SQLPackage e o ASAJonInfo são atualizados com os URLs SAS relevantes que guardou anteriormente no tutorial.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Passos Seguintes

- [Implementar modelo ML no Azure SQL Edge utilizando onNX](tutorial-run-ml-model-on-sql-edge.md)
