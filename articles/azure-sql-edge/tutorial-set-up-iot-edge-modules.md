---
title: Configurar módulos IoT Edge em Azure SQL Edge
description: Na segunda parte deste tutorial de três partes do Azure SQL Edge para prever impurezas de minério de ferro, você vai configurar módulos e conexões IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235029"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurar módulos e ligações IoT Edge

Na segunda parte deste tutorial em três partes para prever impurezas de minério de ferro em Azure SQL Edge, você vai configurar os seguintes módulos IoT Edge:

- SQL do Azure no Edge
- Módulo IoT Edge do gerador de dados

## <a name="create-azure-stream-analytics-module"></a>Criar módulo Azure Stream Analytics

Crie um módulo Azure Stream Analytics que será utilizado neste tutorial. Para saber mais sobre a utilização de trabalhos de streaming com SQL Edge, consulte [utilizando trabalhos de streaming com SQL Edge.](stream-analytics.md)

Assim que o trabalho Azure Stream Analytics for criado com o ambiente de hospedagem definido como Edge, confmem as entradas e saídas para o tutorial.

1. Para criar a **entrada,** clique **em +Adicionar entrada de fluxo**. Preencha a secção de detalhes utilizando as seguintes informações:

   Campo|Valor
   -----|-----
   Formato de Serialização de Eventos|JSON
   Codificação|UTF-8
   Tipo de compressão de eventos|Nenhuma

2. Para criar a **saída,** clique **em +Adicionar** e escolha base de dados SQL. Preencha a secção de detalhes utilizando as seguintes informações.

   > [!NOTE]
   > A palavra-passe especificada nesta secção tem de ser especificada para a palavra-passe SQL SA ao implementar o módulo SQL Edge na secção **"Implementar o módulo Azure SQL Edge".**

   Campo|Valor
   -----|-----
   Base de Dados|IronOreSilicaPrediction
   Nome do servidor|tcp:.,1433
   Nome de utilizador|SG
   Palavra-passe|Especifique uma senha forte
   Tabela|IronOreMeasures1

3. Navegue para a secção **de consulta** e configurar a consulta da seguinte forma:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. Em **Configuração**, **selecione Publicar**e, em seguida, selecione o botão **Publicar.** Guarde o SAS URI para utilização com o módulo SQL Database Edge.

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
   Name|Nome de registo
   Endereço|Servidor de início de sessão
   Nome de Utilizador|Nome de utilizador
   Palavra-passe|Palavra-passe
  
## <a name="deploy-the-data-generator-module"></a>Implementar o módulo gerador de dados

1. Na secção **módulos IoT Edge Modules,** clique **em + ADD** e selecione o módulo **IoT Edge**.

2. Forneça um nome de módulo IoT Edge e o URI de imagem.
   O Image URI pode ser encontrado no registo do contentor no grupo de recursos. Selecione a secção **repositórios** em **Serviços**. Para este tutorial, escolha o repositório `silicaprediction` nomeado. Selecione a etiqueta apropriada. O Image URI será do formato:

   *servidor de login da regiessão de* / contentores *nome do repositório*:*nome da etiqueta*

   Por exemplo:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. CLick **Add**.

## <a name="deploy-the-azure-sql-edge-module"></a>Implementar o módulo Azure SQL Edge

1. Implementar o módulo Azure SQL Edge seguindo os passos listados na [Borda Azure SQL (Pré-visualização)](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

2. Na **rota de especificação** da página **De Módulos Definidos, especifique** as rotas do módulo para a comunicação do hub IoT Edge da seguinte forma. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Por exemplo:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Nas definições gémeas do **módulo,** certifique-se de que o SQLPackage e o ASAJonInfo são atualizados com os URLs SAS relevantes que guardou anteriormente no tutorial.

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

- [Implementar o modelo ML na Borda Azure SQL utilizando ONNX](tutorial-run-ml-model-on-sql-edge.md)
