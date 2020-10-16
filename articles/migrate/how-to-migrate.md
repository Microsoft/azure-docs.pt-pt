---
title: Adicione ferramentas de migração em Azure Migrate
description: Saiba como adicionar ferramentas de migração em Azure Migrate.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: b6ac8ed64d3f12783cc16f0428874a19d027adf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86109812"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração em [Azure Migrate](./migrate-services-overview.md).

- Se quiser adicionar uma ferramenta de migração e ainda não tiver criado um projeto Azure Migrate, siga este [artigo.](how-to-add-tool-first-time.md)
- Se adicionou uma ferramenta ISV para migração, [siga os passos,](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-a-migration-scenario"></a>Selecione um cenário de migração

1. No projeto Azure Migrate, clique em **Visão Geral**.
2. Selecione o cenário de migração que pretende utilizar:

    - Para migrar máquinas e cargas de trabalho para Azure, **selecione Avaliar e migrar servidores**.
    - Para migrar nas bases de dados do SQL Server, selecione **Avaliar e migrar bases de dados**.
    - Para migrar aplicações web no local, selecione **Avaliar e migrar aplicações web**.
    - Para migrar grandes quantidades de dados no local para OZure em modo offline, selecione **Encomende uma Caixa de Dados**.

    ![Cenário de avaliação](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração de servidor

1. Clique em **Avaliar e Migrar Servidores**.
2. Em **Azure Migrate - Servidores**, se ainda não adicionou ferramentas de migração, sob **ferramentas de migração,** selecione **Clique aqui para adicionar uma ferramenta de migração**. Se já adicionou ferramentas de migração, em **Adicionar mais ferramentas de migração,** selecione **Change**.

    > [!NOTE]
    > Se precisar de navegar para um projeto diferente, em **Azure Migrate - Servidores**, ao lado **de Ver detalhes para um projeto migratório diferente,** clique aqui em Clique **aqui**.

3. Em **Azure Migrate,** selecione a ferramenta de migração que pretende utilizar.
    - Se utilizar a Migração do Servidor Azure Migrate, pode configurar e executar migrações diretamente no projeto Azure Migrate.
    - Se utilizar uma ferramenta de avaliação de terceiros, navegue até ao link fornecido para o ISV e execute a migração de acordo com as instruções que fornecerem.

## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de bases de dados

1. Clique em **Avaliar e migrar bases de dados**
2. Em **Bases de Dados,** clique em **Adicionar ferramentas**.
3. In Adicionar uma ferramenta > **Selecione a ferramenta de migração,** selecione a ferramenta que pretende utilizar para migrar a sua base de dados.

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicativos web

1. Clique **em Avaliar e migrar aplicações web.**
2. Siga o link para a ferramenta migração para o Serviço de Aplicações Azure. Utilize a ferramenta de migração para:

    - **Avaliar aplicativos online**: Pode avaliar e migrar aplicações com um URL público on-line, utilizando o Assistente de Migração do Serviço de Aplicações Azure.
    - **.NET/PHP**: Para aplicações internas .NET e PHP, pode descarregar e executar o Assistente de Migração.

## <a name="order-an-azure-data-box"></a>Encomende uma Caixa de Dados Azure

Para migrar grandes quantidades de dados para a Azure, pode encomendar uma Caixa DAta Azure para transferência de dados offline.

1. Clique **em Encomendar uma Caixa de Dados**.
2. Em **Selecione a sua Caixa de Dados Azure,** especifique a sua subscrição. 
3. A transferência será uma importação para Azure. Especifique a fonte de dados e o destino da região de Azure para os dados.

## <a name="next-steps"></a>Passos seguintes

Experimente uma migração utilizando a migração do servidor Azure Migrate para VMs [hiper-V](tutorial-migrate-hyper-v.md) ou [VMware.](tutorial-migrate-vmware.md)
