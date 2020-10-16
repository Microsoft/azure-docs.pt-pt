---
title: Stop/start - Portal Azure - Base de Dados Azure para O Servidor Flexível MySQL
description: Este artigo descreve como parar/iniciar operações na Base de Dados Azure para o MySQL através do portal Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567509"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Parar/Iniciar uma base de dados de Azure para o MySQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo fornece procedimento passo a passo para executar Stop and Start do servidor flexível.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter uma base de dados Azure para o MySQL Flexible Server.

## <a name="stop-a-running-server"></a>Parar um servidor de execução

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível que pretende parar.

2.  Na página **'Vista Geral',** clique no botão **Stop** na barra de ferramentas.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Pare o servidor flexível."::: 

3.  Clique **em Sim** para confirmar a paragem do seu servidor.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Pare o servidor flexível."::: 

> [!NOTE]
> Uma vez que o servidor é parado, as outras operações de gestão não estão disponíveis para o servidor flexível.

## <a name="start-a-stopped-server"></a>Inicie um servidor parado

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível que pretende iniciar.

2.  A partir da página **'Vista Geral',** clique no botão **Iniciar** na barra de ferramentas.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Pare o servidor flexível.":::  

> [!NOTE]
> Uma vez iniciado o servidor, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [networking em Azure Database para MySQL Flexible Server](./concepts-networking.md)
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).

