---
title: Gerir regras de firewall - Hyperscale (Citus) - Base de Dados Azure para PostgreSQL
description: Crie e gerencie as regras de firewall para a Base de Dados Azure para PostgreSQL - Hyperscale (Citus) utilizando o portal Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b7de5755a9a1e49b994e7efa7fc4bca58cc6cfd9
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584033"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gerir as regras de firewall para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
As regras de firewall ao nível do servidor podem ser usadas para gerir o acesso a um nó coordenador de hiperescala (Citus) a partir de um endereço IP especificado ou gama de endereços IP.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- Um grupo de servidores Criar uma Base de Dados Azure para o grupo de [servidores PostgreSQL – Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

> [!NOTE]
> Estas configurações também são acessíveis durante a criação de uma Base de Dados Azure para o grupo de servidores PostgreSQL - Hyperscale (Citus). No separador **Networking,** clique no **ponto final do público**.
> ![Portal Azure - separador de rede](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Na página do grupo de servidorpostgreSQL, sob a rubrica Segurança, clique em **Networking** para abrir as regras de Firewall.

   ![Portal Azure - clique em Networking](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Clique em Adicionar IP do **cliente,** quer na barra de ferramentas (opção A abaixo), quer no link (opção B). De qualquer forma, cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como percebido pelo sistema Azure.

   ![Portal Azure - clique em Adicionar cliente IP](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Alternadamente, clicar **em +Adicionar 0.0.0.0 - 255.255.255.255** (à direita da opção B) permite não só o seu IP, mas toda a internet para aceder à porta 5432 do nó coordenador. Nesta situação, os clientes ainda devem fazer login com o nome de utilizador e senha corretos para utilizar o cluster. No entanto, recomendamos que se permita o acesso mundial por apenas curtos períodos de tempo e apenas para bases de dados não produtivas.

3. Verifique o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal Azure difere do endereço IP utilizado no acesso aos servidores da internet e do Azure. Por isso, poderá ter de alterar o IP inicial e o IP final para que a regra funcione como esperado.
   Utilize um motor de busca ou outra ferramenta online para verificar o seu próprio endereço IP. Por exemplo, procure "qual é o meu IP".

   ![Bing pesquisa por What is my IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereçoadicionais adicionais. Nas regras de firewall, pode especificar um único endereço IP ou um leque de endereços. Se pretender limitar a regra a um único endereço IP, digite o mesmo endereço no campo para Iniciar IP e End IP. A abertura da firewall permite aos administradores, utilizadores e aplicações aceder ao nó coordenador na porta 5432.

5. Clique em **Guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização das regras da firewall foi bem sucedida.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure

Existe uma maneira fácil de conceder acesso à base de dados Hyperscale a aplicações hospedadas no Azure (como uma aplicação Azure Web Apps, ou aquelas que estão a funcionar num VM Azure). Basta definir os **serviços e recursos do Allow Azure para aceder a esta** opção de grupo de servidor estonteante para **Sim** no portal a partir do painel **de networking** e bater **Save**.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita os passos para gerir as regras da firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar IP do cliente**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique na elipse [...] e clique em **Eliminar** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Conceito de regras de firewall](concepts-hyperscale-firewall-rules.md), incluindo como resolver problemas de ligação.
