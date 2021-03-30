---
title: Gerir regras de firewall - Hyperscale (Citus) - Base de Dados Azure para PostgreSQL
description: Criar e gerir regras de firewall para Azure Database for PostgreSQL - Hyperscale (Citus) usando o portal Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/11/2020
ms.openlocfilehash: dadd04497eae0e91bdf5ea3caad38beda35f7fa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91275426"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gerir regras de firewall para Azure Database for PostgreSQL - Hyperscale (Citus)
As regras de firewall ao nível do servidor podem ser usadas para gerir o acesso a um nó coordenador de Hiperescala (Citus) a partir de um endereço IP especificado ou intervalo de endereços IP especificados.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- Um grupo de [servidores Criar uma base de dados Azure para o grupo de servidores PostgreSQL – Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

> [!NOTE]
> Estas definições também estão acessíveis durante a criação de um grupo de servidores Azure Database for PostgreSQL - Hyperscale (Citus). No **separador 'Rede',** clique **no ponto final do Público.**

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Portal Azure - separador de rede":::

1. Na página do grupo do servidor PostgreSQL, no título de Segurança, clique em **Networking** para abrir as regras de Firewall.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Portal Azure - clique em Networking":::

2. Clique **em Adicionar o endereço IP do cliente atual** para criar uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema Azure.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Portal Azure - clique em Adicionar IP ao cliente":::

Em alternativa, clicar **em +Adicionar 0.0.0.0 - 255.255.255.255** (à direita da opção B) permite não só o seu IP, mas toda a internet para aceder à porta 5432 do nó coordenador. Nesta situação, os clientes ainda devem iniciar sessão com o nome de utilizador e palavra-passe corretos para utilizar o cluster. No entanto, recomendamos que se possa permitir o acesso a nível mundial por apenas curtos períodos de tempo e apenas para bases de dados não produtivos.

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da Internet e do Azure. Portanto, pode ser necessário alterar o IP de início e o IP final para fazer a regra funcionar como esperado.
   Utilize um motor de busca ou outra ferramenta on-line para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu IP".

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Bing pesquisa por Qual é o meu IP":::

4. Adicione intervalos de endereços adicionais. Nas regras de firewall, pode especificar um único endereço IP ou uma gama de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço no campo para Iniciar IP e IP final. A abertura da firewall permite aos administradores, utilizadores e aplicações aceder ao nó coordenador na porta 5432.

5. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem sucedida.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure

Existe uma maneira fácil de conceder acesso à base de dados Hyperscale (Citus) a aplicações hospedadas no Azure (como uma aplicação Azure Web Apps, ou aquelas que estão a funcionar num VM Azure). Basta definir os **serviços e recursos do Allow Azure para aceder a esta** opção de grupo de servidor para **Sim** no portal a partir do painel de **networking** e bater **Save**.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita os passos para gerir as regras da firewall.
* Para adicionar o computador atual, clique no botão para + Adicionar o **endereço IP do cliente atual**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique na elipse [...] e clique em **Eliminar** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o conceito de regras de firewall,](concepts-hyperscale-firewall-rules.md)incluindo como resolver problemas de conexão.
