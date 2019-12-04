---
title: Gerenciar regras de firewall-portal do Azure-banco de dados do Azure para MariaDB
description: Criar e gerenciar as regras de firewall do banco de dados do Azure para MariaDB usando o portal do Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 70e6611cc3006424bcd1bc3a0f0838ad40c09634
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766026"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Criar e gerenciar o banco de dados do Azure para regras de firewall MariaDB usando o portal do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um banco de dados do Azure para MariaDB Server a partir de um endereço IP especificado ou de um intervalo de endereços IP.

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. Na página do servidor MariaDB, em título de configurações, clique em **segurança de conexão** para abrir a página segurança de conexão do banco de dados do Azure para MariaDB.

   ![portal do Azure clique em segurança de conexão](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Clique em **Adicionar meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema do Azure.

   ![portal do Azure clique em Adicionar meu IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado por portal do Azure difere do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para que a regra funcione conforme o esperado.

   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, pesquise "o que é meu endereço IP".

4. Adicione intervalos de endereços adicionais. Nas regras de firewall para o banco de dados do Azure para MariaDB, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra a um único endereço IP, digite o mesmo endereço nos campos IP inicial e IP final. Abrir o firewall permite que administradores, usuários e aplicativos acessem qualquer banco de dados no servidor MariaDB para o qual têm credenciais válidas.

   ![Regras de portal do Azure firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Clique em **salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização para as regras de firewall foi bem-sucedida.

   ![portal do Azure clique em salvar](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que os aplicativos do Azure se conectem ao banco de dados do Azure para MariaDB Server, as conexões do Azure devem ser habilitadas. Por exemplo, para hospedar um aplicativo de aplicativos Web do Azure, ou um aplicativo que é executado em uma VM do Azure, ou para se conectar de um Azure Data Factory gateway de gerenciamento de dados. Os recursos não precisam estar na mesma rede virtual (VNet) ou grupo de recursos para que a regra de firewall habilite essas conexões. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Há alguns métodos para habilitar esses tipos de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como **ativado** no portal no painel **segurança de conexão** e clicar em **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o banco de dados do Azure para o servidor MariaDB.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Gerenciar regras de firewall existentes no portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique em **+ Adicionar meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar outros endereços IP, digite o **nome da regra**, o **IP inicial**e o **IP final**. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e, em seguida, modifique. Clique em **Guardar** para guardar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e, em seguida, clique em **excluir**. Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes
 - Da mesma forma, você pode gerar script para [criar e gerenciar as regras de firewall do banco de dados do Azure para MariaDB usando CLI do Azure](howto-manage-firewall-cli.md).
 - Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-portal.md).