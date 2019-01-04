---
title: Criar e gerir regras de firewall do MySQL na base de dados do Azure para MySQL
description: Criar e gerir a base de dados do Azure para as regras de firewall do MySQL com o portal do Azure
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 598af6bb945318f0a76ffe094dd5786abacccc3f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543534"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Criar e gerir a base de dados do Azure para as regras de firewall do MySQL com o portal do Azure
Regras de firewall ao nível do servidor permitem que os administradores aceder a uma base de dados do Azure para o servidor MySQL a partir de um endereço IP especificado ou um intervalo de endereços IP. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall ao nível do servidor no portal do Azure

1. Na página do servidor MySQL, em definições, clique em **segurança de ligação** para abrir a página de segurança da ligação da base de dados do Azure para MySQL.

   ![Portal do Azure - clique em segurança de ligação](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **adicionar o meu IP** na barra de ferramentas. Isso cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como percebido pelo sistema do Azure.

   ![Portal do Azure - clique em adicionar o meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique se o seu endereço IP antes de guardar a configuração. Em algumas situações, o endereço IP observado pelo portal do Azure é diferente do endereço IP utilizado ao aceder à internet e os servidores do Azure. Por conseguinte, terá de alterar o IP inicial e o IP final para tornar a função de regra conforme esperado.

   Utilize um motor de busca ou outra ferramenta online para verificar o seu endereço IP. Por exemplo, pesquise "qual é o meu endereço IP". 

   ![Bing para o que é o meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicione intervalos de endereços adicional. Nas regras da firewall da base de dados do Azure para MySQL, pode especificar um único endereço IP ou um intervalo de endereços. Se pretende limitar a regra para um único endereço IP, escreva o mesmo endereço os campos de IP inicial e final. Abrir a firewall permite aos administradores, utilizadores e aplicações para aceder a qualquer base de dados no servidor do MySQL para o qual tenham credenciais válidas.

   ![Portal do Azure – regras de firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **guardar** na barra de ferramentas para guardar esta regra de firewall ao nível do servidor. Aguarde a confirmação de que a atualização para as regras de firewall é efetuada com êxito.

   ![Portal do Azure - clique em Guardar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que aplicações do Azure ligar à base de dados do Azure para o servidor MySQL, ligações do Azure tem de estar ativadas. Por exemplo, para alojar uma aplicação de aplicações Web do Azure ou um aplicativo que é executado numa VM do Azure ou para ligar a partir de um gateway de gestão de dados do Azure Data Factory. Os recursos não é necessário estar na mesma rede Virtual (VNet) ou grupo de recursos para a regra de firewall para permitir que essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem dois métodos para permitir que esses tipos de ligações. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir o **permitir o acesso aos serviços do Azure** a opção de **ON** no portal do **segurança da ligação** painel e clique em **guardar**. Se a tentativa de ligação não for permitida, o pedido não chega a base de dados do Azure para o servidor MySQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existente com o portal do Azure
Repita os passos para gerir as regras de firewall.
* Para adicionar o computador atual, clique em **+ adicionar o meu IP**. Clique em **Guardar** para guardar as alterações.
* Para adicionar endereços IP adicionais, escreva o **nome da regra**, **IP inicial**, e **IP final**. Clique em **Guardar** para guardar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e, em seguida, modifique. Clique em **Guardar** para guardar as alterações.
* Para eliminar uma regra existente, clique nas reticências [...] e, em seguida, clique em **eliminar**. Clique em **Guardar** para guardar as alterações.


## <a name="next-steps"></a>Passos Seguintes
- Da mesma forma, pode criar scripts para [criar e gerir a base de dados do Azure para as regras de firewall do MySQL com a CLI do Azure](howto-manage-firewall-using-cli.md).
- Para obter ajuda na conexão com uma base de dados do Azure para o servidor MySQL, consulte [bibliotecas de ligação para base de dados do Azure para MySQL](./concepts-connection-libraries.md)
