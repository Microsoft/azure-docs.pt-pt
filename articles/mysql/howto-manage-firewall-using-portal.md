---
title: Gerenciar regras de firewall-portal do Azure-banco de dados do Azure para MySQL
description: Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b02bae528146a3f0c214b7fbb1d234c1a24b174f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774245"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure
As regras de firewall no nível de servidor podem ser usadas para gerenciar o acesso a um servidor de banco de dados do Azure para MySQL a partir de um endereço IP especificado ou de um intervalo de endereços IP. 

As regras de rede virtual (VNet) também podem ser usadas para proteger o acesso ao seu servidor. Saiba mais sobre como [criar e gerenciar pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. Na página do servidor MySQL, em título de configurações, clique em **segurança de conexão** para abrir a página segurança de conexão do banco de dados do Azure para MySQL.

   ![portal do Azure clique em segurança de conexão](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, conforme percebido pelo sistema do Azure.

   ![portal do Azure clique em Adicionar meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado por portal do Azure difere do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para que a regra funcione conforme o esperado.

   Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP. Por exemplo, pesquise "o que é meu endereço IP".

4. Adicione intervalos de endereços adicionais. Nas regras de firewall para o banco de dados do Azure para MySQL, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra a um único endereço IP, digite o mesmo endereço nos campos IP inicial e IP final. Abrir o firewall permite que administradores, usuários e aplicativos acessem qualquer banco de dados no servidor MySQL ao qual tenham credenciais válidas.

   ![Regras de portal do Azure firewall](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização para as regras de firewall foi bem-sucedida.

   ![portal do Azure clique em salvar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que os aplicativos do Azure se conectem ao seu banco de dados do Azure para o servidor MySQL, as conexões do Azure devem ser habilitadas. Por exemplo, para hospedar um aplicativo de aplicativos Web do Azure, ou um aplicativo que é executado em uma VM do Azure, ou para se conectar de um Azure Data Factory gateway de gerenciamento de dados. Os recursos não precisam estar na mesma rede virtual (VNet) ou grupo de recursos para que a regra de firewall habilite essas conexões. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Há alguns métodos para habilitar esses tipos de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como ativado no portal no painel **segurança de conexão** e clicar **em** **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o banco de dados do Azure para o servidor MySQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Gerenciar regras de firewall no nível de servidor existentes usando o portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique em **+ Adicionar meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar outros endereços IP, digite o **nome da regra**, o **IP inicial**e o **IP final**. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e, em seguida, modifique. Clique em **Guardar** para guardar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e, em seguida, clique em **excluir**. Clique em **Guardar** para guardar as alterações.


## <a name="next-steps"></a>Passos seguintes
- Da mesma forma, você pode gerar script para [criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando CLI do Azure](howto-manage-firewall-using-cli.md).
- Proteja ainda mais o acesso ao seu servidor [criando e gerenciando pontos de extremidade de serviço de rede virtual e regras usando o portal do Azure](howto-manage-vnet-using-portal.md).
- Para obter ajuda para se conectar a um servidor de banco de dados do Azure para MySQL, consulte [bibliotecas de conexões do banco de dados do Azure para MySQL](./concepts-connection-libraries.md).