---
title: Implementar o modelo ML na Borda Azure SQL utilizando ONNX
description: Na terceira parte deste tutorial de três partes do Azure SQL Edge para prever impurezas de minério de ferro, você executará os modelos de aprendizagem de máquinas ONNX em SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422200"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Implementar o modelo ML na Borda Azure SQL utilizando ONNX 

Na parte três deste tutorial em três partes para prever impurezas de minério de ferro em Azure SQL Edge, você:

1. Utilize o Azure Data Studio para ligar à Base de Dados SQL no caso Azure SQL Edge.
2. Prever impurezas de minério de ferro com ONNX em Azure SQL Edge.

## <a name="key-components"></a>Componentes-chave

1. A solução utiliza um padrão de 500 milissegundos entre cada mensagem enviada para o Edge Hub. Isto pode ser alterado no ficheiro **.cs Programa** 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. A solução gerou uma mensagem, com os seguintes atributos. Adicione ou remova os atributos de acordo com os requisitos. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Ligue-se à Base de Dados SQL na instância Azure SQL Edge para treinar, implantar e testar o modelo ML

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

4. Na secção **Ficheiro,** abra **/DeploymentScripts/MiningProcess_ONNX.jpynb** da pasta onde clonou os ficheiros do projeto na sua máquina.

5. Coloque o núcleo em Python 3.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização de modelos ONNX em Azure SQL Edge, consulte [machine learning e AI com ONNX em SQL Edge](onnx-overview.md).