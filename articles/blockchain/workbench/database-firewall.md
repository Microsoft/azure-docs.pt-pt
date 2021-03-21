---
title: Configure Azure Blockchain Workbench firewall
description: Saiba como configurar a firewall da base de dados de pré-visualização do workbench Azure Blockchain para permitir a ligação de clientes e aplicações externos.
ms.date: 09/09/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: dc7a1dc7e4a083ef8f5f7650f29ed1430b32e88b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009454"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Configurar a firewall da base de dados do Azure Blockchain Workbench

Este artigo mostra como configurar uma regra de firewall com o portal do Azure. As regras de firewall permitem às aplicações ou clientes externos ligar à base de dados do Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ligar à base de dados do Blockchain Workbench

Para ligar à base de dados onde quer configurar uma regra:

1. Inscreva-se no portal Azure com uma conta que tem permissões **do Proprietário**     para os recursos da bancada de trabalho Azure Blockchain.
2. Na navegação à esquerda, selecione **Grupos de recursos**.
3. Escolha o nome do grupo de recursos para a sua implementação do Blockchain Workbench.
4. Selecione **Tipo** para ordenar a lista de recursos e, em seguida, selecione **SQL Server**.
5. O exemplo da lista de recursos na captura de ecrã seguinte mostra duas bases de dados: *mestra* e *lsgn-sdk*. Pode configurar a regra de firewall em *lsgn-sdk*.

![Listar os recursos do Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Criar uma regra de firewall de base de dados

Para criar uma regra de firewall:

1. Escolha a ligação à base de dados "lsgn-sdk".
2. Na barra de menus, selecione **Definir firewall do servidor**.

   ![Definir firewall do servidor](./media/database-firewall/configure-server-firewall.png)

3. Para criar uma regra para a sua organização:

   * Introduza um **NOME DA REGRA**
   * Introduza um endereço IP para o **IP INICIAL** do intervalo de endereços
   * Introduza um endereço IP para o **IP FINAL** do intervalo de endereços

   ![Criar regra de firewall](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Se apenas quiser adicionar o endereço IP do seu computador, selecione **+ Adicionar IP do cliente**.
        
1. Para guardar a configuração da firewall, selecione **Guardar**.
2. Teste o intervalo de endereços IP que configurou para a base de dados ao ligar a uma aplicação ou ferramenta. Por exemplo, SQL Server Management Studio.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)