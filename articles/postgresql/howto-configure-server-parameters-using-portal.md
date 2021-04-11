---
title: Configurar parâmetros de servidor - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar os parâmetros Postgres na Base de Dados Azure para PostgreSQL através do portal Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: 2e9cecc03c1240d625fa8f6bbdcd633ada978082
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604214"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configurar parâmetros de servidor na Base de Dados Azure para PostgreSQL - Servidor Único através do portal Azure 
Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor PostgreSQL através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- [Base de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Parâmetros de visualização e edição
1. Abra o [portal do Azure](https://portal.azure.com).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na secção **DEFINIÇÕES,** selecione **os parâmetros do Servidor**. A página mostra uma lista de parâmetros, seus valores e descrições.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Página geral para parâmetros":::

4. Selecione o botão **drop down** para ver os valores possíveis para parâmetros do tipo enumerados como client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Enumerar queda":::

5. Selecione ou paire sobre o botão **i** (informação) para ver a gama de valores possíveis para parâmetros numéricos como cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="botão de informação":::

6. Se necessário, utilize a **caixa de pesquisa** para reduzir a um parâmetro específico. A pesquisa está no nome e descrição dos parâmetros.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Resultados da pesquisa":::

7. Altere os valores de parâmetro que pretende ajustar. Todas as alterações que fizer numa sessão são destacadas em púrpura. Depois de alterar os valores, pode selecionar **Guardar**. Ou pode **descartar as** suas alterações.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Guardar ou descartar alterações":::

8. Se tiver guardado novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **Reset all to predefinido**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Redefinir tudo para o padrão":::

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Visão geral dos parâmetros do servidor na Base de Dados Azure para PostgreSQL](concepts-servers.md)
- [Configurar parâmetros utilizando o Azure CLI](howto-configure-server-parameters-using-cli.md)
