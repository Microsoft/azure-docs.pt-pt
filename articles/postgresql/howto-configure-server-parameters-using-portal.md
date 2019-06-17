---
title: Configurar parâmetros do servidor na base de dados do Azure para PostgreSQL através do portal do Azure
description: Este artigo descreve como configurar os parâmetros de servidor na base de dados do Azure para PostgreSQL através do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: ed19083c6a4245a1b4bf7af166ae965d956c9e37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067128"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configurar parâmetros do servidor na base de dados do Azure para PostgreSQL - servidor único através do portal do Azure 
Pode listar, mostrar e atualizar os parâmetros de configuração para uma base de dados do Azure para o servidor PostgreSQL através do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, que tem de:
- [Base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Visualizar e editar parâmetros
1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Sob o **configurações** secção, selecione **parâmetros do servidor**. A página mostra uma lista de parâmetros, valores e descrições.
![Página de descrição geral para parâmetros](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecione o **menu pendente** botão para ver os possíveis valores para parâmetros de tipo enumerado como client_min_messages.
![Enumerar para baixo](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecione ou coloque o cursor sobre o **eu** botão (informações) para ver o intervalo de valores possíveis para parâmetros numéricos, como cpu_index_tuple_cost.
![botão de informações](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessário, use o **caixa de pesquisa** para limitar a um parâmetro específico. A pesquisa é sobre o nome e descrição dos parâmetros.
![Resultados da pesquisa](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Altere os valores de parâmetro que pretende ajustar. Todas as alterações efetuadas numa sessão são realçadas em Roxo. Assim que tiver alterado os valores, pode selecionar **guardar**. Ou pode **descartar** as suas alterações.
![Guardar ou alterações de rejeição](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se guardar novos valores para os parâmetros, poderá sempre reverter tudo volta para os valores predefinidos, selecionando **repor tudo para predefinição**.
![Repor tudo para predefinição](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre:
- [Descrição geral dos parâmetros do servidor na base de dados do Azure para PostgreSQL](concepts-servers.md)
- [Configurar parâmetros com a CLI do Azure](howto-configure-server-parameters-using-cli.md)
