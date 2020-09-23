---
title: Stop/start - Portal Azure - Base de Dados Azure para O Servidor Flexível MySQL
description: Este artigo descreve como parar/iniciar operações na Base de Dados Azure para o MySQL através do portal Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: b9f406035d32a9af9ba2f5b085bcaca1b51e9d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937282"
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

> [!NOTE]
> Uma vez que o servidor é parado, as outras operações de gestão não estão disponíveis para o servidor flexível.

## <a name="start-a-stopped-server"></a>Inicie um servidor parado

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível que pretende iniciar.

2.  Na página **'Vista Geral',** clique no botão **Stop** na barra de ferramentas.

> [!NOTE]
> Uma vez iniciado o servidor, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [networking em Azure Database para MySQL Flexible Server](./concepts-networking.md)
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).

