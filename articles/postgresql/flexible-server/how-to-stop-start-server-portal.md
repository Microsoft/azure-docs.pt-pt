---
title: Stop/start - Portal Azure - Base de Dados Azure para Servidor Flexível Pós-SQL
description: Este artigo descreve como parar/iniciar operações na Base de Dados Azure para PostgreSQL através do portal Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e28243f5919c355e6ad0d4998ff8388a56e3d0ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607373"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Parar/Iniciar uma base de dados de Azure para PostgreSQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT]
> A base de dados Azure para PostgreSQL - Servidor Flexível está atualmente em pré-visualização.

Este artigo fornece instruções passo a passo para parar e iniciar um servidor flexível.

## <a name="pre-requisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter uma base de dados Azure para o Servidor Flexível PostgreSQL.

## <a name="stop-a-running-server"></a>Parar um servidor de execução

1.  No [portal Azure,](https://portal.azure.com/)escolha o servidor flexível que pretende parar.

2.  Na página **'Vista Geral',** clique no botão **Stop** na barra de ferramentas.

> [!NOTE]
> Uma vez que o servidor é parado, outras operações de gestão não estão disponíveis para o servidor flexível.

Por favor, note que os servidores parados recomeçarão automaticamente após sete dias. Quaisquer atualizações de manutenção pendentes serão aplicadas quando o servidor for iniciado da próxima vez.

## <a name="start-a-stopped-server"></a>Inicie um servidor parado

1.  No [portal Azure,](https://portal.azure.com/)escolha o servidor flexível que pretende iniciar.

2.  A partir da página **'Vista Geral',** clique no botão **Iniciar** na barra de ferramentas.

> [!NOTE]
> Uma vez iniciado o servidor, todas as operações de gestão estão agora disponíveis para o servidor flexível.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [opções de computação e armazenamento na Base de Dados Azure para o Servidor Flexível PostgreSQL](./concepts-compute-storage.md).
