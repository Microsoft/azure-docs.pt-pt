---
title: Gerenciar regras de firewall-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Criar e gerenciar regras de firewall para o banco de dados do Azure para PostgreSQL-Citus (hiperescala) usando o portal do Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977544"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gerenciar regras de firewall para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um nó de coordenador de hiperescala (Citus) de um endereço IP ou intervalo de endereços IP especificado.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Um grupo de servidores [crie um grupo de servidores do banco de dados do Azure para PostgreSQL – Citus (hiperescala)](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

> [!NOTE]
> Essas configurações também são acessíveis durante a criação de um grupo de servidores do banco de dados do Azure para PostgreSQL-Citus (hiperescala). Na guia **rede** , clique em **ponto de extremidade público**.
> ![portal do Azure guia rede](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Na página grupo de servidores PostgreSQL, no título segurança, clique em **rede** para abrir as regras de firewall.

   ![portal do Azure em rede](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar IP do cliente**, na barra de ferramentas (opção A abaixo) ou no link (opção B). De qualquer forma, o cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema do Azure.

   ![portal do Azure clique em Adicionar IP do cliente](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Como alternativa, clicar em **+ Adicionar 0.0.0.0-255.255.255.255** (à direita da opção B) permite não apenas seu IP, mas a Internet inteira para acessar a porta 5432 do nó do coordenador. Nessa situação, os clientes ainda devem fazer logon com o nome de usuário e a senha corretos para usar o cluster. No entanto, é recomendável permitir acesso mundial apenas por longos períodos de tempo e somente para bancos de dados de não produção.

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado por portal do Azure difere do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para que a regra funcione conforme o esperado.
   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, procure "Qual é meu IP".

   ![Pesquisa do Bing para qual é meu IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereços adicionais. Nas regras de firewall, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra a um único endereço IP, digite o mesmo endereço no campo para IP inicial e IP final. Abrir o firewall permite que administradores, usuários e aplicativos acessem o nó de coordenador na porta 5432.

5. Clique em **salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização para as regras de firewall foi bem-sucedida.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure

Há uma maneira fácil de conceder acesso ao banco de dados de hiperescala para aplicativos hospedados no Azure (como um aplicativo de aplicativos Web do Azure ou aqueles em execução em uma VM do Azure). Basta definir a opção **permitir que os serviços e recursos do Azure acessem este grupo de servidores** como **Sim** no portal do painel de **rede** e clicar em **salvar**.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar IP do cliente**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e clique em **excluir** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [conceito de regras de firewall](concepts-hyperscale-firewall-rules.md), incluindo como solucionar problemas de conexão.
