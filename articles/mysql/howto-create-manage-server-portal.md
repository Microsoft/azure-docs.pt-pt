---
title: Criar e gerir a base de dados do Azure para o servidor MySQL com o portal do Azure
description: Este artigo descreve como pode rapidamente a criar uma nova base de dados do Azure para o servidor MySQL e gerir o servidor com o Portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6d6f24475497382dd9e04d3335fb89d6f0bdd514
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57528683"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Criar e gerir a base de dados do Azure para o servidor MySQL com o portal do Azure
Este tópico descreve como pode criar rapidamente uma nova base de dados do Azure para o servidor MySQL. Também inclui informações sobre como gerir o servidor com o portal do Azure. Gestão de servidor inclui a visualização de detalhes do servidor e bases de dados, repor a palavra-passe, dimensionar os recursos e a eliminação do servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL
Siga estes passos para criar uma base de dados do Azure para o servidor MySQL com o nome "mydemoserver."

1. Clique nas **criar um recurso** botão localizado no canto superior esquerdo do portal do Azure.

2. Na página nova, selecione **bases de dados**e, em seguida, na página de bases de dados, selecione **base de dados do Azure para MySQL**.

    > Uma base de dados do Azure para o servidor MySQL é criada com um conjunto definido de [computação e armazenamento](./concepts-pricing-tiers.md) recursos. A base de dados é criado dentro de um grupo de recursos do Azure e numa base de dados do Azure para o servidor MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Preencha a base de dados do Azure para MySQL formulário ao utilizar as seguintes informações:

    | **Campo do Formulário** | **Descrição do Campo** |
    |----------------|-----------------------|
    | *Nome do servidor* | mydemoserver (o nome do servidor é exclusivo globalmente) |
    | *Subscrição* | mysubscription (selecione no menu pendente) |
    | *Grupo de recursos* | myresourcegroup (criar um novo grupo de recursos ou utilize um já existente) |
    | *Selecionar origem* | Em branco (criar um servidor MySQL em branco) |
    | *Início de sessão de administrador do servidor* | myadmin (configure o nome da conta de administração) |
    | *Palavra-passe* | senha de conta de administrador de conjunto |
    | *Confirmar palavra-passe* | confirme a palavra-passe da conta de administrador |
    | *Localização* | Sudeste Asiático (selecione entre Europa do Norte e E.U.A. oeste) |
    | *Versão* | 5.7 (escolher a base de dados do Azure para a versão do servidor MySQL) |

4. Clique em **escalão de preço** para especificar o nível de desempenho e a camada de serviço do novo servidor. Selecione o **fins gerais** separador. *Geração 5*, *2 vCores*, *5 GB* e *7 dias* são os valores predefinidos de **Geração de Computação**, **vCore**, **Armazenamento** e **Período de Retenção da Cópia de Segurança**. Pode deixar os controlos de deslize como estão. Para ativar as cópias de segurança do servidor no armazenamento georredundante, selecione **Geograficamente Redundante** nas **Opções de Redundância da Cópia de Segurança**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.

    > Selecione o **afixar ao dashboard** opção para permitir uma fácil monitorização das suas implementações.

## <a name="update-an-azure-database-for-mysql-server"></a>Atualizar uma base de dados do Azure para o servidor MySQL
Depois do novo servidor tenha sido provisionado, o utilizador tem várias opções para configurar o servidor existente, incluindo a repor a palavra-passe de administrador e aumentando ou reduzindo, o servidor alterando vCore ou armazenamento.

### <a name="change-the-administrator-user-password"></a>Alterar a palavra-passe de utilizador do administrador
1. Do servidor **descrição geral**, clique em **Repor palavra-passe** para mostrar a janela de repor a palavra-passe.

   ![descrição geral](./media/howto-create-manage-server-portal/overview.png)

2. Introduza uma nova palavra-passe e confirme a palavra-passe na janela, conforme mostrado:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Clique em **OK** para guardar a nova palavra-passe.

### <a name="scale-vcores-updown"></a>VCores de dimensionamento para cima/para baixo

1. Clique em **escalão de preço**, localizado em **definições**.

2. Alteração da **vCore** definição ao mover o controlo de deslize para o valor pretendido.

    ![Dimensionar a computação](./media/howto-create-manage-server-portal/scale-compute.png)

3. Clique em **OK** para guardar as alterações.

### <a name="scale-storage-up"></a>Armazenamento à escala cópia de segurança

1. Clique em **escalão de preço**, localizado em **definições**.

2. Alteração da **armazenamento** definição ao mover o controlo de deslize para o valor pretendido.

    ![armazenamento de escala](./media/howto-create-manage-server-portal/scale-storage.png)

3. Clique em **OK** para guardar as alterações.

## <a name="delete-an-azure-database-for-mysql-server"></a>Eliminar uma base de dados do Azure para o servidor MySQL

1. Do servidor **descrição geral**, clique nas **eliminar** botão para abrir o pedido de confirmação de eliminação.

    ![eliminar](./media/howto-create-manage-server-portal/delete.png)

2. Escreva o nome do servidor na caixa de entrada dupla confirmação.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Clique nas **eliminar** botão para confirmar a eliminação do servidor. Aguarde até que o pop de "servidor MySQL eliminado com êxito" aparecer na barra de notificação.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista a base de dados do Azure para bases de dados MySQL
Do servidor **descrição geral**, desloque para baixo até ver a base de dados do mosaico na parte inferior. Todas as bases de dados do servidor estão listados na tabela.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Mostrar detalhes de uma base de dados do Azure para o servidor MySQL
Clique em **propriedades**, localizado em **definições** para ver informações detalhadas sobre o servidor.

![propriedades](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Passos Seguintes

[Quickstart: Criar base de dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)