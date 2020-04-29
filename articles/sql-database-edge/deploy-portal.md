---
title: Implementar a pré-visualização da borda da base de dados SQL utilizando o portal Azure [ Microsoft Docs
description: Saiba como implementar o Azure SQL Database Edge utilizando o portal Azure
keywords: implementar borda de base de dados sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246726"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Implementar a pré-visualização da borda da base de dados Azure SQL

A Pré-visualização do Edge De base de dados Azure SQL é um motor de base de dados relacional otimizado para implementações IoT e Azure IoT Edge. Fornece capacidades para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicações e soluções IoT. Este quickstart mostra-lhe como começar com a criação de um módulo Azure SQL Database Edge através do Azure IoT Edge utilizando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inicie sessão no [portal do Azure](https://portal.azure.com/).
* Envie aqui um [pedido,](https://azure.microsoft.com/services/sql-database-edge/#contact)para que a subscrição seja ativada para a implementação do SQL Database Edge.
* Crie um [Hub Azure IoT.](../iot-hub/iot-hub-create-through-portal.md)
* Registe um [Dispositivo IoT Edge a partir do portal Azure](../iot-edge/how-to-register-device-portal.md).
* Prepare o dispositivo IoT Edge para [implantar o módulo IoT Edge a partir do portal Azure](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Para implementar um VM Azure Linux como um dispositivo IoT Edge, consulte este [guia de arranque rápido](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Implementar módulo de borda de base de dados SQL do Mercado Azure

O Azure Marketplace é um mercado de aplicações e serviços online onde pode navegar através de uma vasta gama de aplicações e soluções empresariais certificadas e otimizadas para funcionar no Azure, incluindo [módulos IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) O Edge de base de dados Azure SQL pode ser implantado num dispositivo de borda através do mercado.

1. Encontre o módulo De base de dados Azure SQL Edge no Mercado Azure.<br><br>

   ![Borda da base de dados SQL no MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Escolha o plano de software que melhor corresponda aos seus requisitos e clique em **Criar**. <br><br>

   ![Escolha o plano de software correto](media/deploy-portal/pick-correct-plan.png)

3. No Target Devices para a página Módulo IoT Edge, especifique os seguintes detalhes e clique em **Criar**

   |**Campo**  |**Descrição**  |
   |---------|---------|
   |Subscrição  |  A assinatura Azure sob a qual o IoT Hub foi criado |
   |IoT Hub   |  Nome do Hub IoT onde o dispositivo IoT Edge está registado e, em seguida, selecione a opção "Implementar para um dispositivo"|
   |Nome do dispositivo iot edge  |  Nome do dispositivo IoT Edge onde o SQL Database Edge seria implantado |

4. Na página **Dedefinir Módulos,** navegue para a secção em módulos de implementação e clique em **Configurar** com o módulo SQL Database Edge. 

5. No painel **ioT Edge Custom Modules,** especifique os valores desejados para as variáveis ambientais e/ou personalize as opções de criação e as propriedades desejadas para o módulo. Para uma lista completa de variáveis ambientais suportadas, consulte [variáveis de ambiente de contentores do servidor SQL](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parâmetro**  |**Descrição**|
   |---------|---------|
   | Nome | Nome para o módulo. |
   |SA_PASSWORD  | Especifique uma palavra-passe forte para a conta de administração SQL Database Edge. |
   |MSSQL_LCID   | Define o ID de idioma para o Servidor SQL. Por exemplo, 1036 é francês. |
   |MSSQL_COLLATION | Define a colagem predefinida para o Servidor SQL. Esta definição substitui o mapeamento padrão do ID de idioma (LCID) à colagem. |

   > [!NOTE]
   > Por favor, não altere ou atualize o **Image URI** ou as definições **de ACCEPT_EULA** no módulo.

6. No painel **ioT Edge Custom Modules,** atualização o recipiente cria opções de valor desejado para a **Porta anfitriã .** Se necessitar de implantar mais do que um módulo SQL DB Edge, certifique-se de atualizar a opção de montagem para criar um novo par de alvos de fonte & para o volume persistente. Para obter mais informações sobre suportes e volumes, consulte [os volumes de utilização](https://docs.docker.com/storage/volumes/) na documentação do estivador. 

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

7. No painel **ioT Edge Custom Modules,** atualize as *propriedades desejadas pelo módulo set twin* para incluir a localização do pacote SQL e a informação de trabalho de análise de fluxo. Estes dois campos são opcionais e devem ser utilizados se pretender implementar o módulo SQL Database Edge com uma base de dados e um trabalho de streaming.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. No painel **ioT Edge Custom Modules,** desloque a Política de *Reiniciar* para sempre e o *Estado Desejado* para funcionar.
9. No painel **ioT Edge Custom Modules,** clique em **Guardar**.
10. Na página **de módulos set** clique **em Seguinte**.
11. Na **Rota de Especificação (opcional)** da página **Demódulos,** especifique as rotas do módulo para módulo ou módulo para a comunicação IoT Edge Hub ver [módulos de implantação e estabelecer rotas em IoT Edge](../iot-edge/module-composition.md).
12. Clique em **Seguinte**.
13. Clique em **Submeter**.

Neste arranque rápido, implementou um Módulo de Borda de Base de Dados SQL num dispositivo IoT Edge.

## <a name="next-steps"></a>Passos Seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Database Edge](onnx-overview.md).
- Construindo uma solução ioT final com solução de base de dados SQL utilizando IoT Edge.
