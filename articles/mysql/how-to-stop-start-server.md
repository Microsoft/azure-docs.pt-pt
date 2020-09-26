---
title: Stop/start - Portal Azure - Base de Dados Azure para servidor MySQL
description: Este artigo descreve como parar/iniciar operações na Base de Dados Azure para o MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: f09b6d48e8a98b0995c882769d6c978996324dad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346114"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Parar/Iniciar uma Base de Dados Azure para o MySQL

> [!IMPORTANT]
> A funcionalidade stop/Start para Azure Database para o MySQL encontra-se atualmente em pré-visualização pública.

Este artigo fornece procedimento passo a passo para executar Stop and Start do servidor flexível.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter uma base de dados Azure para o MySQL Flexible Server.

> [!NOTE]
> Consulte a limitação da utilização do [stop/start](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Como parar/iniciar a Base de Dados Azure para o MySQL utilizando o portal Azure

### <a name="stop-a-running-server"></a>Parar um servidor de execução

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor MySQL que pretende parar.

2.  Na página **'Vista Geral',** clique no botão **Stop** na barra de ferramentas.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Base de dados Azure para o servidor MySQL Stop":::

    > [!NOTE]
    > Uma vez que o servidor é parado, as outras operações de gestão não estão disponíveis para o servidor flexível.

### <a name="start-a-stopped-server"></a>Inicie um servidor parado

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível que pretende iniciar.

2.  A partir da página **'Vista Geral',** clique no botão **Iniciar** na barra de ferramentas.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database para o servidor de arranque mySQL":::

    > [!NOTE]
    > Uma vez iniciado o servidor, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Como parar/iniciar a Base de Dados Azure para o MySQL utilizando o CLI

### <a name="stop-a-running-server"></a>Parar um servidor de execução

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor MySQL que pretende parar.

2.  Na página **'Vista Geral',** clique no botão **Stop** na barra de ferramentas.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Uma vez que o servidor é parado, as outras operações de gestão não estão disponíveis para o servidor flexível.

### <a name="start-a-stopped-server"></a>Inicie um servidor parado

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível que pretende iniciar.

2.  A partir da página **'Vista Geral',** clique no botão **Iniciar** na barra de ferramentas.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Uma vez iniciado o servidor, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="next-steps"></a>Passos seguintes
Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)
