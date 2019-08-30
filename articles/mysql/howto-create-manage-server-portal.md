---
title: Criar e gerenciar o servidor de banco de dados do Azure para MySQL usando o portal do Azure
description: Este artigo descreve como você pode criar rapidamente um novo servidor de banco de dados do Azure para MySQL e gerenciar o servidor usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fdcb302d3a14b02ea86fb92c8dbf822ef3f42177
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142161"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Criar e gerenciar o servidor de banco de dados do Azure para MySQL usando o portal do Azure
Este tópico descreve como você pode criar rapidamente um novo servidor de banco de dados do Azure para MySQL. Ele também inclui informações sobre como gerenciar o servidor usando o portal do Azure. O gerenciamento de servidor inclui a exibição de detalhes do servidor e bancos de dados, a redefinição da senha, o dimensionamento de recursos e a exclusão do servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Siga estas etapas para criar um servidor de banco de dados do Azure para MySQL chamado "mydemoserver".

1. Clique no botão **criar um recurso** localizado no canto superior esquerdo da portal do Azure.

2. Na página novo, selecione **bancos**de dados e, em seguida, na página bancos de dados, selecione **Azure Database para MySQL**.

    > Um banco de dados do Azure para servidor MySQL é criado com um conjunto definido de recursos de [computação e armazenamento](./concepts-pricing-tiers.md) . O banco de dados é criado dentro de um grupo de recursos do Azure e em um servidor de banco de dados do Azure para MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Preencha o formulário do banco de dados do Azure para MySQL usando as seguintes informações:

    | **Campo do Formulário** | **Descrição do Campo** |
    |----------------|-----------------------|
    | *Nome do servidor* | mydemoserver (o nome do servidor é globalmente exclusivo) |
    | *Subscrição* | mysubscription (selecione no menu suspenso) |
    | *Grupo de recursos* | MyResource Group (criar um novo grupo de recursos ou usar um existente) |
    | *Selecionar origem* | Em branco (criar um servidor MySQL em branco) |
    | *Início de sessão de administrador do servidor* | myadmin (configure o nome da conta de administração) |
    | *Palavra-passe* | Definir senha da conta de administrador |
    | *Confirmar palavra-passe* | confirme a palavra-passe da conta de administrador |
    | *Location* | Sudeste Asiático (selecione entre Europa Setentrional e oeste dos EUA) |
    | *Versão* | 5,7 (escolha o banco de dados do Azure para a versão do servidor MySQL) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Clique em **tipo de preço** para especificar a camada de serviço e o nível de desempenho para o novo servidor. Selecione a guia **uso geral** . *Geração 5*, *2 vCores*, *5 GB* e *7 dias* são os valores predefinidos de **Geração de Computação**, **vCore**, **Armazenamento** e **Período de Retenção da Cópia de Segurança**. Pode deixar os controlos de deslize como estão. Para ativar as cópias de segurança do servidor no armazenamento georredundante, selecione **Geograficamente Redundante** nas **Opções de Redundância da Cópia de Segurança**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.

    > Selecione a opção **fixar no painel** para permitir o controle fácil de suas implantações.

## <a name="update-an-azure-database-for-mysql-server"></a>Atualizar um banco de dados do Azure para servidor MySQL
Depois que o novo servidor tiver sido provisionado, o usuário terá várias opções para configurar o servidor existente, incluindo a redefinição da senha do administrador, a alteração do tipo de preço e a expansão ou redução vertical do servidor, alterando vCore ou armazenamento

### <a name="change-the-administrator-user-password"></a>Alterar a senha de usuário do administrador
1. Na **visão geral**do servidor, clique em **Redefinir senha** para mostrar a janela de redefinição de senha.

   ![descrição geral](./media/howto-create-manage-server-portal/overview.png)

2. Insira uma nova senha e confirme a senha na janela, conforme mostrado:

   ![Redefinir senha](./media/howto-create-manage-server-portal/reset-password.png)

3. Clique em **OK** para salvar a nova senha.

### <a name="change-the-pricing-tier"></a>Alterar o tipo de preço
> [!NOTE]
> O dimensionamento só tem suporte de Uso Geral para camadas de serviço com otimização de memória e vice-versa. Observe que a alteração de e para o tipo de preço básico após a criação do servidor não tem suporte no banco de dados do Azure para MySQL.
> 
1. Clique em **tipo de preço**, localizado em **configurações**.
2. Selecione o **tipo de preço** para o qual você deseja alterar.

    ![alterar-camada de preços](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Clique em **OK** para guardar as alterações. 

### <a name="scale-vcores-updown"></a>Dimensionar vCores para cima/para baixo

1. Clique em **tipo de preço**, localizado em **configurações**.

2. Altere a configuração de **vCore** movendo o controle deslizante para o valor desejado.

    ![escala-computação](./media/howto-create-manage-server-portal/scale-compute.png)

3. Clique em **OK** para guardar as alterações.

### <a name="scale-storage-up"></a>Escalar o armazenamento verticalmente

1. Clique em **tipo de preço**, localizado em **configurações**.

2. Altere a configuração de **armazenamento** movendo o controle deslizante para o valor desejado.

    ![armazenamento em escala](./media/howto-create-manage-server-portal/scale-storage.png)

3. Clique em **OK** para guardar as alterações.

## <a name="delete-an-azure-database-for-mysql-server"></a>Excluir um servidor de banco de dados do Azure para MySQL

1. Na **visão geral**do servidor, clique no botão **excluir** para abrir o prompt de confirmação de exclusão.

    ![eliminar](./media/howto-create-manage-server-portal/delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmação dupla.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Clique no botão **excluir** para confirmar a exclusão do servidor. Aguarde até que o pop-up "servidor MySQL excluído com êxito" apareça na barra de notificação.

## <a name="list-the-azure-database-for-mysql-databases"></a>Listar os bancos de dados do Azure para MySQL
Na **visão geral**do servidor, role para baixo até ver o bloco do banco de dados na parte inferior. Todos os bancos de dados no servidor são listados na tabela.

   ![Mostrar-bancos de dados](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Mostrar detalhes de um servidor de banco de dados do Azure para MySQL
Clique em **Propriedades**, localizado em **configurações** , para exibir informações detalhadas sobre o servidor.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar banco de dados do Azure para servidor MySQL usando portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)