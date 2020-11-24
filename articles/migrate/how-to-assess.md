---
title: Adicionar ferramentas de avaliação em Azure Migrate
description: Saiba como adicionar ferramentas de avaliação no Azure Migrate.
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 17113d167476c62a97031721b73183d0b0da18af
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95535035"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação em [Azure Migrate](./migrate-services-overview.md). 

- Se quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto Azure Migrate, siga este [artigo](create-manage-projects.md).
- Se adicionou uma ferramenta ISV, ou Movere, para avaliação, [siga os passos,](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-an-assessment-scenario"></a>Selecione um cenário de avaliação

1. No projeto Azure Migrate, clique em **Visão Geral**.
2. Selecione o cenário de avaliação:

    - Para descobrir e avaliar máquinas e cargas de trabalho para a migração para Azure, **selecione Avaliar e migrar servidores**.
    - Para avaliar as bases de dados do SQL Server no local, selecione **Avaliar e migrar bases de dados**.
    - Para avaliar ou migrar aplicações web no local, selecione **Explore mais**  >  **Aplicações Web**.
    - Para avaliar a sua infraestrutura virtual de desktop, **selecione Explore mais**  >  **Infraestruturas de Ambiente de Trabalho Virtuais.**

    ![Opções para selecionar um cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 


1. Adicione uma ferramenta:

    - Se criou um projeto Azure Migrate utilizando a opção **Avaliar e migrar servidores** no portal, a ferramenta Azure Migrate Server Assessment é automaticamente adicionada ao projeto. Para adicionar ferramentas de avaliação adicionais, em **Servidores,** ao lado de **ferramentas de Avaliação,** selecione **Adicionar mais ferramentas**.
    
         ![Botão para adicionar ferramentas de avaliação adicionais](./media/how-to-assess/add-assessment-tool.png)

    - Se criou um projeto utilizando uma opção diferente e ainda não tiver ferramentas de avaliação, em ferramentas de avaliação **de servidores,**  >  **Assessment tools** **selecione Clique aqui**.

        ![Botão para adicionar a primeira ferramenta de avaliação](./media/how-to-assess/no-assessment-tool.png)

2. Nas ferramentas **Azure Migrate**  >  **Add,** selecione as ferramentas que pretende adicionar. Em seguida, **selecione Adicionar a ferramenta**.

    ![Selecione ferramentas de avaliação da lista](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Selecione uma ferramenta de avaliação de bases de dados

1. Adicione uma ferramenta:

    - Se criou um projeto Azure Migrate utilizando a opção **de base de dados Avaliar e migrar** no portal, a ferramenta de Avaliação de Bases de Dados é automaticamente adicionada ao projeto. Para adicionar ferramentas de avaliação adicionais, em **Bases de Dados,** junto a **ferramentas de Avaliação,** selecione **Adicionar mais ferramentas**.

    - Se criou um projeto utilizando uma opção diferente e ainda não tiver ferramentas de avaliação de bases de dados, em ferramentas de avaliação **de bases de dados,**  >  **Assessment tools** **selecione Clique aqui**.

2. Nas ferramentas **Azure Migrate**  >  **Add,** selecione as ferramentas que pretende adicionar. Em seguida, **selecione Adicionar a ferramenta**.

    ![Selecione ferramentas de avaliação de bases de dados da lista](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativos web

Se criou um projeto Azure Migrate utilizando a opção **Explore mais**  >  **WebApps** no portal, a ferramenta de Avaliação de Aplicações Web é automaticamente adicionada ao projeto. 


1. Se a ferramenta de avaliação de aplicações web não estiver no projeto, em ferramentas de avaliação **de aplicações web,**  >  **Assessment tools** **selecione Clique aqui**.
    
    ![Adicionar ferramentas de avaliação de aplicativos web](./media/how-to-assess/no-web-app-assessment-tool.png)


2. Nas ferramentas **Azure Migrate**  >  **Add,** selecione a ferramenta de avaliação de aplicações Web. Em seguida, **selecione Adicionar a ferramenta**.

    ![Selecione a ferramenta de migração da base de dados da lista](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Passos seguintes

Descubra máquinas no local para avaliação utilizando a ferramenta de avaliação do servidor Azure Migrate para [VMware](./tutorial-discover-vmware.md) VMs, [Hiper-V](./tutorial-discover-hyper-v.md)ou [servidores físicos](./tutorial-discover-physical.md)