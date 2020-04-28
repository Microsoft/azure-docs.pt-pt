---
title: Gerir regras de firewall - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Crie e gereas regras de firewall para a Base de Dados Azure para PostgreSQL - Servidor Único utilizando o portal Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74770310"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Crie e gereas regras de firewall para a Base de Dados Azure para PostgreSQL - Servidor Único utilizando o portal Azure
As regras de firewall ao nível do servidor podem ser utilizadas para gerir o acesso a uma Base de Dados Azure para o PostgreSQL Server a partir de um endereço IP especificado ou gama de endereços IP.

As regras da Rede Virtual (VNet) também podem ser usadas para garantir o acesso ao seu servidor. Saiba mais sobre [a criação e gestão de pontos finais e regras de serviço da Rede Virtual utilizando o portal Azure.](howto-manage-vnet-using-portal.md)

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Um servidor [Criar uma Base de Dados Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure
1. Na página do servidor PostgreSQL, na rubrica Definições, clique na **segurança de Ligação** para abrir a página de segurança da Ligação para a Base de Dados Azure para PostgreSQL.

   ![Portal Azure - clique em Segurança de Ligação](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em adicionar o **meu IP** na barra de ferramentas. Isto cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como percebido pelo sistema Azure.

   ![Portal Azure - clique em Adicionar o meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da internet e do Azure. Por isso, poderá ter de alterar o IP inicial e o IP final para que a regra funcione como esperado.
   Utilize um motor de busca ou outra ferramenta online para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu IP".

   ![Bing pesquisa por What is my IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereçoadicionais adicionais. Nas regras de firewall para a Base de Dados Azure para PostgreSQL, pode especificar um único endereço IP, ou um leque de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço no campo para Iniciar IP e End IP. A abertura da firewall permite aos administradores, utilizadores e aplicações acederem a qualquer base de dados do servidor PostgreSQL para os quais possuem credenciais válidas.

   ![Portal Azure - regras de firewall](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização das regras da firewall foi bem sucedida.

   ![Portal Azure - clique em Guardar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que as aplicações do Azure se conectem à sua Base de Dados Azure para servidor PostgreSQL, as ligações Azure devem ser ativadas. Por exemplo, hospedar uma aplicação de Web Apps Azure, ou uma aplicação que funciona num VM Azure, ou para se conectar a partir de um gateway de gestão de dados da Azure Data Factory. Os recursos não precisam de estar na mesma Rede Virtual (VNet) ou Grupo de Recursos para a regra de firewall para permitir essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem alguns métodos para permitir este tipo de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir a opção **permitir o acesso aos serviços Azure** para **ON** no portal a partir do painel de segurança **de ligação** e bater **Save**. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor PostgreSQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita os passos para gerir as regras da firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar o meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique na elipse [...] e clique em **Eliminar** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, pode escrever para Criar e gerir a Base de Dados Azure para regras de [firewall PostgreSQL utilizando o Azure CLI](howto-manage-firewall-using-cli.md).
- Acesso mais seguro ao seu servidor [criando e gerindo pontos finais de serviço de Rede Virtual e regras usando o portal Azure](howto-manage-vnet-using-portal.md).
- Para obter ajuda na ligação a uma Base de Dados Azure para servidor PostgreSQL, consulte bibliotecas de [ligação para base de dados Azure para PostgreSQL](concepts-connection-libraries.md).
