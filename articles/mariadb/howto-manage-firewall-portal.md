---
title: Gerir regras de firewall - Portal Azure - Base de Dados Azure para MariaDB
description: Crie e gerea Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8be8e948595cfb93049c0d6c93f421e4902e771d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530670"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Crie e gerea Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o Servidor MariaDB a partir de um endereço IP especificado ou de uma série de endereços IP.

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais e regras de serviço da Rede Virtual utilizando o portal Azure.](howto-manage-vnet-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. Na página do servidor MariaDB, em título Definições, clique em **Segurança de Ligação** para abrir a página de Segurança de Ligação para a Base de Dados Azure para MariaDB.

   ![Portal Azure - clique em segurança de ligação](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Clique em adicionar o **meu IP** na barra de ferramentas. Isto cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como percebido pelo sistema Azure.

   ![Portal Azure - clique em Adicionar o meu IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da internet e do Azure. Por isso, poderá ter de alterar o IP inicial e o IP final para que a regra funcione como esperado.

   Utilize um motor de busca ou outra ferramenta online para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu endereço IP".

4. Adicione intervalos de endereçoadicionais adicionais. Nas regras de firewall para a Base de Dados Azure para MariaDB, pode especificar um único endereço IP ou um leque de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço nos campos IP e END IP iniciar. A abertura da firewall permite aos administradores, utilizadores e aplicação aceder a qualquer base de dados do servidor MariaDB a que tenham credenciais válidas.

   ![Portal Azure - regras de firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização das regras de firewall é bem sucedida.

   ![Portal Azure - clique em Guardar](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que as aplicações do Azure se conectem à sua Base de Dados Azure para o servidor MariaDB, as ligações Azure devem ser ativadas. Por exemplo, hospedar uma aplicação de Web Apps Azure, ou uma aplicação que funciona num VM Azure, ou para se conectar a partir de um gateway de gestão de dados da Azure Data Factory. Os recursos não precisam de estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall para permitir essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem alguns métodos para permitir este tipo de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir a opção **permitir o acesso aos serviços Azure** para **ON** no portal a partir do painel de segurança **de Ligação** e clicar em **Guardar**. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor MariaDB.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Gerir as regras de firewall existentes no portal Azure
Repita os passos para gerir as regras da firewall.
* Para adicionar o computador atual, clique + **Adicione o meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, digite o NOME DE **REGRA,** **START IP**, e **END IP**. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos da regra e, em seguida, modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique na elipse [...], e, em seguida, clique em **Eliminar**. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
 - Da mesma forma, pode escrever para Criar e gerir a Base de Dados Azure para regras de [firewall MariaDB utilizando o Azure CLI](howto-manage-firewall-cli.md).
 - Acesso mais seguro ao seu servidor [criando e gerindo pontos finais de serviço de Rede Virtual e regras usando o portal Azure](howto-manage-vnet-portal.md).