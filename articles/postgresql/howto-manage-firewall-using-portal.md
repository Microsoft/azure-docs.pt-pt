---
title: Gerenciar regras de firewall-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Criar e gerenciar regras de firewall para o banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: aeef22bf96221061a444f40e16e33343fafe511c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770310"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Criar e gerenciar regras de firewall para o banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um banco de dados do Azure para o servidor PostgreSQL a partir de um endereço IP ou intervalo de endereços IP especificado.

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- Um servidor [criar um banco de dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure
1. Na página do servidor PostgreSQL, no título configurações, clique em **segurança de conexão** para abrir a página segurança de conexão do banco de dados do Azure para PostgreSQL.

   ![portal do Azure clique em segurança de conexão](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema do Azure.

   ![portal do Azure clique em Adicionar meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado por portal do Azure difere do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para que a regra funcione conforme o esperado.
   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, procure "Qual é meu IP".

   ![Pesquisa do Bing para qual é meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereços adicionais. Nas regras de firewall para o banco de dados do Azure para PostgreSQL, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra a um único endereço IP, digite o mesmo endereço no campo para IP inicial e IP final. Abrir o firewall permite que administradores, usuários e aplicativos acessem qualquer banco de dados no servidor PostgreSQL ao qual têm credenciais válidas.

   ![Regras de portal do Azure firewall](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização para as regras de firewall foi bem-sucedida.

   ![portal do Azure clique em salvar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que os aplicativos do Azure se conectem ao seu banco de dados do Azure para o servidor PostgreSQL, as conexões do Azure devem ser habilitadas. Por exemplo, para hospedar um aplicativo de aplicativos Web do Azure, ou um aplicativo que é executado em uma VM do Azure, ou para se conectar de um Azure Data Factory gateway de gerenciamento de dados. Os recursos não precisam estar na mesma rede virtual (VNet) ou grupo de recursos para que a regra de firewall habilite essas conexões. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Há alguns métodos para habilitar esses tipos de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como ativado no portal no painel **segurança de conexão** e clicar **em** **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor de banco de dados do Azure para PostgreSQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique no botão para + **Adicionar meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Guardar** para guardar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e clique em **excluir** para remover a regra. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, você pode gerar script para [criar e gerenciar regras de firewall do banco de dados do Azure para PostgreSQL usando CLI do Azure](howto-manage-firewall-using-cli.md).
- Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-using-portal.md).
- Para obter ajuda para se conectar a um servidor de banco de dados do Azure para PostgreSQL, consulte [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
