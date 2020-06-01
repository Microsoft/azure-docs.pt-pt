---
title: Implementar borda Azure SQL (Pré-visualização) utilizando o portal Azure
description: Saiba como implementar o Azure SQL Edge (Preview) utilizando o portal Azure
keywords: implementar SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 43359b66ba747dba7b3294d022a2c1aa2a3e624c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233247"
---
# <a name="deploy-azure-sql-edge-preview"></a>Implementar borda Azure SQL (pré-visualização) 

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado para implementações IoT e Azure IoT Edge. Fornece capacidades para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicações e soluções IoT. Este quickstart mostra-lhe como começar a criar um módulo Azure SQL Edge através do Azure IoT Edge utilizando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Inicie sessão no [portal do Azure](https://portal.azure.com/).
* Criar um [Hub Azure IoT](../iot-hub/iot-hub-create-through-portal.md).
* Registar um [dispositivo de borda IoT a partir do portal Azure](../iot-edge/how-to-register-device-portal.md).
* Prepare o dispositivo IoT Edge para [implantar o módulo IoT Edge a partir do portal Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Para implementar um Azure Linux VM como um dispositivo IoT Edge, consulte este [guia de arranque rápido](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Implementar módulo sql edge a partir do Azure Marketplace

O Azure Marketplace é um mercado de aplicações e serviços online onde pode navegar através de uma vasta gama de aplicações e soluções empresariais que são certificadas e otimizadas para funcionar em Azure, incluindo [módulos IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) O Azure SQL Edge pode ser implantado num dispositivo de borda através do mercado.

1. Encontre o módulo Azure SQL Edge no Mercado Azure.<br><br>

   ![SqL Edge no MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Escolha o plano de software que melhor corresponda aos seus requisitos e clique em **Criar**. <br><br>

   ![Escolha o plano de software correto](media/deploy-portal/pick-correct-plan.png)

3. Nos dispositivos-alvo para a página do módulo IoT Edge, especifique os seguintes detalhes e, em seguida, clique em **Criar**

   |**Campo**  |**Descrição**  |
   |---------|---------|
   |Subscrição  |  A subscrição Azure sob a qual o IoT Hub foi criado |
   |IoT Hub   |  Nome do Hub IoT onde o dispositivo IoT Edge está registado e, em seguida, selecione "Implementar para um dispositivo"|
   |Nome do dispositivo de borda IoT  |  Nome do dispositivo IoT Edge onde o SQL Edge seria implantado |

4. Na página **'Definir Módulos',** navegue na secção dos módulos de implantação e clique em **Configurar** contra o módulo SQL Edge. 

5. No painel **IoT Edge Custom Modules, especifique os valores desejados** para as variáveis ambientais e/ou personalize as opções de criação e as propriedades desejadas para o módulo. Para obter uma lista completa de variáveis de ambiente suportado, consulte [variáveis de ambiente de contentores](/sql/linux/sql-server-linux-configure-environment-variables/)de servidor SQL .

   |**Parâmetro**  |**Descrição**|
   |---------|---------|
   | Name | Nome para o módulo. |
   |SA_PASSWORD  | Especifique uma palavra-passe forte para a conta de administração SQL Edge. |
   |MSSQL_LCID   | Define o ID do idioma para usar para o SQL Server. Por exemplo, 1036 é francês. |
   |MSSQL_COLLATION | Define a colagem padrão para o SQL Server. Esta definição substitui o mapeamento padrão do ID da linguagem (LCID) à colagem. |

   > [!NOTE]
   > Por favor, não altere ou atualize o **Image URI** ou as definições **ACCEPT_EULA** no módulo.

6. No painel **IoT Edge Custom Modules,** atualize o recipiente para criar opções de valor desejado para a **Porta anfitriã.** Se precisar de implementar mais de um módulo SQL DB Edge, certifique-se de atualizar a opção de montagem para criar uma nova fonte & par alvo para o volume persistente. Para obter mais informações sobre suportes e volume, consulte [utilize volumes](https://docs.docker.com/storage/volumes/) na documentação do estivador. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. No painel **de módulos personalizados IoT Edge,** atualize as *propriedades desejadas do Módulo set Twin* para incluir a localização do pacote SQL e a informação de trabalho de stream analytics. Estes dois campos são opcionais e devem ser utilizados se pretender implantar o módulo SQL Edge com uma base de dados e um trabalho de streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. No painel **de módulos personalizados IoT Edge,** desacorda a *Política de Reinício* para sempre e *o Estado Desejado* para funcionar.
9. No painel **de módulos personalizados IoT Edge,** clique em **Guardar**.
10. Na página **de módulos set** clique em **Seguinte**.
11. Na **Rota de Especificação (opcional)** da página **De Módulos Definidos, especifique** as rotas do módulo para módulo ou módulo para comunicação IoT Edge Hub ver [Módulos de Implantação e estabelecer rotas em IoT Edge](../iot-edge/module-composition.md).
12. Clique em **Seguinte**.
13. Clique **em Submeter.**

Neste arranque rápido, lançou um Módulo SQL Edge num dispositivo IoT Edge.

## <a name="next-steps"></a>Passos Seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md).
- [Construção de uma solução IoT de ponta a ponta com aresta SQL utilizando IoT Edge](tutorial-deploy-azure-resources.md).