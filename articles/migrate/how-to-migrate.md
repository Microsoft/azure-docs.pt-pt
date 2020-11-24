---
title: Adicione ferramentas de migração em Azure Migrate
description: Saiba como adicionar ferramentas de migração em Azure Migrate.
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545339"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração em [Azure Migrate](./migrate-services-overview.md).

- Se quiser adicionar uma ferramenta de migração e ainda não tiver criado um projeto Azure Migrate, siga este [artigo.](create-manage-projects.md)
- Se adicionou uma ferramenta ISV para migração, [siga os passos,](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-a-migration-scenario"></a>Selecione um cenário de migração

1. No projeto Azure Migrate, clique em **Visão Geral**.
2. Selecione o cenário de migração que pretende utilizar:

    - Para migrar máquinas e cargas de trabalho para Azure, **selecione Avaliar e migrar servidores**.
    - Para migrar bases de dados no local, selecione **Avaliar e migrar bases de dados**.
    - Para migrar aplicações web no local, selecione **Explore mais**  >  **Aplicações Web**.
    - Para migrar dados para Azure usando a caixa de dados, **selecione Explore mais** caixa de  >  **dados**.

    ![Opções para selecionar um cenário migratório](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração de servidor

1. Adicione uma ferramenta:

    - Se criou um projeto Azure Migrate utilizando a opção **De avaliar e migrar servidores** no portal, a ferramenta de migração do servidor Azure Migrate é automaticamente adicionada ao projeto. Para adicionar ferramentas de migração adicionais, em **Servidores,** junto às **ferramentas de migração,** selecione **Adicionar mais ferramentas**.
    
         ![Botão para adicionar ferramentas de migração adicionais](./media/how-to-migrate/add-migration-tools.png)

    - Se criou um projeto utilizando uma opção diferente e ainda não tem ferramentas de migração, em ferramentas de migração **de servidores,**  >  **Migration tools** **selecione Clique aqui**.

    ![Botão para adicionar as primeiras ferramentas de migração](./media/how-to-migrate/no-migration-tool.png)

2. Nas ferramentas **Azure Migrate**  >  **Add,** selecione as ferramentas que pretende adicionar. Em seguida, **selecione Adicionar a ferramenta**.

    ![Selecione ferramentas de avaliação da lista](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de bases de dados

Se criou um projeto Azure Migrate utilizando a opção **de base de dados Avaliar e migrar** no portal, a ferramenta migração de bases de dados é automaticamente adicionada ao projeto. 

1. Se a ferramenta migração da base de dados não estiver no projeto, nas ferramentas de avaliação **de bases de dados,**  >  **Assessment tools** **selecione Clique aqui.**
    
    ![Adicionar ferramentas de migração de bases de dados](./media/how-to-migrate/no-database-migration-tool.png)


2. Nas ferramentas **Azure Migrate**  >  **Add,** selecione a ferramenta migração da base de dados. Em seguida, **selecione Adicionar a ferramenta**.

    ![Selecione a ferramenta de migração da base de dados da lista](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicativos web

Se criou um projeto Azure Migrate utilizando a opção **Explore mais**  >  **WebApps** no portal, a ferramenta de migração de aplicações Web é automaticamente adicionada ao projeto. 

1. Se a ferramenta de migração de aplicações Web não estiver no projeto, nas ferramentas de avaliação **de aplicações web,**  >  **Assessment tools** selecione **Clique aqui**.

    ![Adicionar ferramentas de migração de aplicativos web](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. Nas ferramentas **Azure Migrate**  >  **Add,** selecione a ferramenta de migração de aplicações web. Em seguida, **selecione Adicionar a ferramenta**.

    ![Selecione ferramentas de avaliação de webapp da lista](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Encomende uma Caixa de Dados Azure

Para migrar grandes quantidades de dados para o Azure, pode encomendar uma Caixa de Dados Azure para transferência de dados offline.

1. Em **Visão Geral**, selecione **Explore mais.**
2. Em **Explorar mais,** selecione **Data box**.
3. In **Get start with Data Box**, selecione o grupo de subscrição e recursos que pretende utilizar ao encomendar uma Caixa de Dados.
4. O **tipo de Transferência** é uma importação para a Azure. Especifique o país em que os dados residem e a região de Azure para onde pretende transferir os dados. 
5. Clique **em Aplicar** para guardar as definições.

## <a name="next-steps"></a>Passos seguintes

Experimente uma migração utilizando a ferramenta de migração do servidor Azure Migrate para [VMs hiper-V](tutorial-migrate-hyper-v.md) ou [VMware.](tutorial-migrate-vmware.md)
