---
title: Gerir regras de firewall - Portal Azure - Azure Database for MariaDB
description: Criar e gerir a Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 895c8e51e16cb0b82ebf3edcf954dabe62b08ff4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021780"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Criar e gerir a Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor MariaDB a partir de um endereço IP especificado ou de uma série de endereços IP.

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais de serviços de Rede Virtual e regras utilizando o portal Azure.](howto-manage-vnet-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. Na página do servidor MariaDB, no título Definições, clique em **'Connection Security'** para abrir a página de Segurança de Ligação para a Base de Dados Azure para MariaDB.

   ![Portal Azure - clique na segurança de conexão](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Clique em **Adicionar o meu IP** na barra de ferramentas. Isto cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema Azure.

   ![Portal Azure - clique em Adicionar O Meu IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da Internet e do Azure. Portanto, pode ser necessário alterar o IP de início e o IP final para fazer a regra funcionar como esperado.

   Utilize um motor de busca ou outra ferramenta on-line para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu endereço IP".

4. Adicione intervalos de endereços adicionais. Nas regras de firewall para a Base de Dados Azure para MariaDB, pode especificar um único endereço IP ou uma série de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço nos campos IP e IP inicial. A abertura da firewall permite aos administradores, utilizadores e aplicações acederem a qualquer base de dados no servidor MariaDB à qual possuem credenciais válidas.

   ![Portal Azure - regras de firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização das regras de firewall é bem sucedida.

   ![Portal Azure - clique em Guardar](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que as aplicações do Azure se conectem à sua Base de Dados Azure para o servidor MariaDB, as ligações Azure devem ser ativadas. Por exemplo, para hospedar uma aplicação Azure Web Apps, ou uma aplicação que funciona num Azure VM, ou para ligar a partir de um gateway de gestão de dados da Azure Data Factory. Os recursos não precisam de estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall para permitir essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem alguns métodos para permitir este tipo de ligações. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir a opção **de acesso a serviços Azure** a **ON** no portal a partir do painel de segurança **De Ligação** e clique em **Guardar**. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor MariaDB.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Gerir as regras de firewall existentes no portal Azure
Repita os passos para gerir as regras da firewall.
* Para adicionar o computador atual, clique **em + Adicionar o meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, digite o **NOME DE REGRA,** **START IP** e END **IP**. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos da regra e, em seguida, modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique na elipse [...], e clique em **apagar**. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
 - Da mesma forma, pode escrever para [criar e gerir a Base de Dados Azure para regras de firewall MariaDB usando Azure CLI](howto-manage-firewall-cli.md).
 - Acesso mais seguro ao seu [servidor, criando e gerindo pontos finais de serviço de Rede Virtual e regras utilizando o portal Azure](howto-manage-vnet-portal.md).