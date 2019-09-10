---
title: Configurar a firewall da BD SQL do Azure Blockchain Workbench
description: Saiba como configurar o firewall de banco de BD SQL do Azure Blockchain Workbench Preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0153065ca0ccd6cf34456d630d7437d5ea7c5b48
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845228"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Configurar a firewall da base de dados do Azure Blockchain Workbench

Este artigo mostra como configurar uma regra de firewall com o portal do Azure. As regras de firewall permitem às aplicações ou clientes externos ligar à base de dados do Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ligar à base de dados do Blockchain Workbench

Para ligar à base de dados onde quer configurar uma regra:

1. Entre no portal do Azure com uma conta que tenha permissões de **proprietário** para os recursos do Azure Blockchain Workbench.
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)