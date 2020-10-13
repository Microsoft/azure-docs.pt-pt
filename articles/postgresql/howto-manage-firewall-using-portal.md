---
title: Gerir regras de firewall - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Criar e gerir regras de firewall para Azure Database for PostgreSQL - Single Server usando o portal Azure
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 02bd4927216b6b60d2720e6f32c5768499e310bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710877"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Criar e gerir regras de firewall para Azure Database for PostgreSQL - Single Server usando o portal Azure
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor PostgreSQL a partir de um endereço IP especificado ou intervalo de endereços IP especificados.

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviços de Rede Virtual e regras utilizando o portal Azure.](howto-manage-vnet-using-portal.md)

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Um servidor [Criar uma base de dados de Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure
1. Na página do servidor PostgreSQL, no título Definições, clique na **segurança de ligação** para abrir a página de segurança de Ligação para a Base de Dados Azure para PostgreSQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Portal Azure - clique em Connection Security":::

2. Clique em **Adicionar o meu IP** na barra de ferramentas. Isto cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema Azure.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Portal Azure - clique em Connection Security":::

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da Internet e do Azure. Portanto, pode ser necessário alterar o IP de início e o IP final para fazer a regra funcionar como esperado.
   Utilize um motor de busca ou outra ferramenta on-line para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu IP".

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Portal Azure - clique em Connection Security":::

4. Adicione intervalos de endereços adicionais. Nas regras de firewall para a Base de Dados Azure para PostgreSQL, pode especificar um único endereço IP ou uma série de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço no campo para Iniciar IP e IP final. A abertura da firewall permite que administradores, utilizadores e aplicações acedam a qualquer base de dados no servidor PostgreSQL ao qual possuem credenciais válidas.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Portal Azure - clique em Connection Security":::

5. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem sucedida.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Portal Azure - clique em Connection Security":::

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que as aplicações do Azure se conectem à sua Base de Dados Azure para o servidor PostgreSQL, as ligações Azure devem ser ativadas. Por exemplo, para hospedar uma aplicação Azure Web Apps, ou uma aplicação que funciona num Azure VM, ou para ligar a partir de um gateway de gestão de dados da Azure Data Factory. Os recursos não precisam de estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall para permitir essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem alguns métodos para permitir este tipo de ligações. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir a opção **de acesso a Azure** de acesso a **ON** no portal a partir do painel de segurança **De Ligação** e bater **Save**. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor PostgreSQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita os passos para gerir as regras da firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar o meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique na elipse [...] e clique em **Eliminar** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode escrever para [criar e gerir a Base de Dados Azure para regras de firewall postgreSQL usando Azure CLI](howto-manage-firewall-using-cli.md).
- Acesso mais seguro ao seu [servidor, criando e gerindo pontos finais de serviço de Rede Virtual e regras utilizando o portal Azure](howto-manage-vnet-using-portal.md).
- Para obter ajuda na ligação a uma base de dados Azure para servidor PostgreSQL, consulte [as bibliotecas de conexão para a base de dados Azure para postgreSQL](concepts-connection-libraries.md).
