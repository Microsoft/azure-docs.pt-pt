---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar os parâmetros postgres no banco de dados do Azure para PostgreSQL por meio do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763681"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configurar parâmetros de servidor no banco de dados do Azure para PostgreSQL-servidor único por meio do portal do Azure 
Você pode listar, mostrar e atualizar parâmetros de configuração para um servidor de banco de dados do Azure para PostgreSQL por meio do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa:
- [Banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Exibindo e editando parâmetros
1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na seção **configurações** , selecione **parâmetros do servidor**. A página mostra uma lista de parâmetros, seus valores e descrições.
![página de visão geral para parâmetros](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecione o botão **suspenso** para ver os valores possíveis para parâmetros de tipo enumerado, como client_min_messages.
![lista suspensa enumerar](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecione ou passe o mouse sobre o botão **i** (informações) para ver o intervalo de valores possíveis para parâmetros numéricos como cpu_index_tuple_cost.
![botão informações](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessário, use a **caixa de pesquisa** para restringir a um parâmetro específico. A pesquisa está no nome e na descrição dos parâmetros.
resultados da pesquisa do ![](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Altere os valores de parâmetro que você deseja ajustar. Todas as alterações feitas em uma sessão são realçadas em roxo. Depois de alterar os valores, você pode selecionar **salvar**. Ou você pode **descartar** suas alterações.
![salvar ou descartar alterações](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se você salvou novos valores para os parâmetros, sempre será possível reverter tudo para os valores padrão selecionando **Redefinir tudo para o padrão**.
![redefinir tudo para o](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png) padrão

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre:
- [Visão geral dos parâmetros de servidor no banco de dados do Azure para PostgreSQL](concepts-servers.md)
- [Configurando parâmetros usando o CLI do Azure](howto-configure-server-parameters-using-cli.md)
