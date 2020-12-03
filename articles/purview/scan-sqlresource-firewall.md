---
title: ScanE Azure SQL DB atrás de uma firewall
description: Saiba como digitalizar recursos atrás de uma firewall utilizando o portal Azure Purview.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553811"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Scan Azure SQL DB atrás de uma firewall em Azure Purview

Neste artigo, você aprende a usar a Azure Purview para digitalizar um recurso por trás de uma firewall.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Seu próprio [inquilino do Azure Ative Directory.](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Configurar armazenamento SQL atrás de uma firewall

O primeiro passo é adicionar o MSI do catálogo a um DB Azure SQL através do [Registo e digitalizar uma Base de Dados Azure SQL](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Scan SQL DB de Purview

1. Navegue pela página inicial do Purview.

1. Selecione **Centro de Gestão** na navegação à esquerda.

1. Selecione **Fontes de Dados** em **Fontes e Digitalização**.

1. Selecione **+ Novo** para adicionar a Fonte de Dados.

1. Selecione **Azure SQL Database**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Screenshot para selecionar o servidor sql.":::

1. Forneça um nome para a nova fonte de dados, selecione **Adução Azure**, e selecione o nome da subscrição e do servidor a partir do dropdown.

   **Selecione Terminar** para completar a inscrição. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Screenshot para terminar a seleção":::

1. Selecione **Configurar scan** para o armazenamento que está por trás da firewall e da ligação de teste. A ligação indica que foi um sucesso. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Screenshot da seleção t0 configurar a varredura.":::

1. Configurar a frequência de digitalização e selecionar **Continuar**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Screenshot da seleção para lançar o scan sql.":::

1.  A digitalização completa e o estado é atualizado na lista de fontes de Dados.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Screenshot da mensagem completada":::
   
## <a name="next-steps"></a>Passos seguintes

Em seguida, pode configurar uma regra de digitalização [Criar uma regra de digitalização definida](create-a-scan-rule-set.md) para analisar em conjunto.
