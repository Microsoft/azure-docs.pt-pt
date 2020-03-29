---
title: Configure parâmetros do servidor - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar os parâmetros Postgres na Base de Dados Azure para PostgreSQL através do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763681"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configure parâmetros de servidor na Base de Dados Azure para PostgreSQL - Servidor Único através do portal Azure 
Pode listar, mostrar e atualizar parâmetros de configuração para uma Base de Dados Azure para servidor PostgreSQL através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar precisa:
- [Base de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Parâmetros de visualização e edição
1. Abra o [portal Azure.](https://portal.azure.com)

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na secção **DEFINIÇÕES,** selecione **os parâmetros do Servidor**. A página mostra uma lista de parâmetros, os seus valores e descrições.
![Página de visão geral para parâmetros](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecione o botão **de descaído** para ver os valores possíveis para parâmetros do tipo enumerado como client_min_messages.
![Enumerar a queda](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecione ou paire sobre o botão **i** (informação) para ver a gama de valores possíveis para parâmetros numéricos como cpu_index_tuple_cost.
![botão de informação](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessário, utilize a caixa de **pesquisa** para se limitar a um parâmetro específico. A procura está no nome e descrição dos parâmetros.
![Resultados da pesquisa](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Altere os valores do parâmetro que gostaria de ajustar. Todas as mudanças que fizer numa sessão são destacadas em roxo. Depois de alterar os valores, pode selecionar **Guardar**. Ou pode **descartar** as suas alterações.
![Guardar ou Descartar alterações](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se guardou novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **tudo para padrão**.
![Redefinir tudo para padrão](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Visão geral dos parâmetros do servidor na Base de Dados Azure para PostgreSQL](concepts-servers.md)
- [Configurar parâmetros utilizando o Azure CLI](howto-configure-server-parameters-using-cli.md)
