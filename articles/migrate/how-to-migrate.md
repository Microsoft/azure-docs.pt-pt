---
title: Adicione ferramentas de migração em Azure Migrate
description: Aprenda a adicionar ferramentas de migração em Azure Migrate.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: e7d36a642f41b77e3b4c4125bae64f02def3b306
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901446"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração em [Azure Migrate](migrate-overview.md).

- Se quiser adicionar uma ferramenta de migração e ainda não tiver montado um projeto Azure Migrate, siga este [artigo.](how-to-add-tool-first-time.md)
- Se adicionou uma ferramenta ISV para migração, [siga os passos,](prepare-isv-movere.md)prepare-se para trabalhar com a ferramenta.

## <a name="select-a-migration-scenario"></a>Selecione um cenário de migração

1. No projeto Azure Migrate, clique em **Visão Geral**.
2. Selecione o cenário de migração que pretende utilizar:

    - Para migrar máquinas e cargas de trabalho para O Azure, selecione **avaliar e migrar servidores**.
    - Para migrar no local máquinas SQL, selecione **avaliar e migrar bases**de dados .
    - Para migrar no local aplicações web, selecione **Avaliar e migrar aplicações web**.
    - Para migrar grandes quantidades de dados no local para Azure em modo offline, selecione **Order a Data Box**.

    ![Cenário de avaliação](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração do servidor

1. Clique em **Avaliar e migrar servidores**.
2. No **Azure Migrate - Servidores,** se ainda não adicionou ferramentas de migração, sob **as ferramentas de Migração,** selecione Clique aqui para adicionar uma ferramenta de **migração**. Se já adicionou ferramentas de migração, em Adicionar mais ferramentas de **migração,** selecione **Change**.

    > [!NOTE]
    > Se precisar de navegar para um projeto diferente, em **Azure Migrate - Servers,** ao lado de **Ver detalhes para um projeto migratório diferente,** clique em Clique **aqui**.

3. No **Azure Migrate,** selecione a ferramenta de migração que pretende utilizar.
    - Se utilizar a Migração do Servidor Migratório Azure, pode configurar e executar migrações diretamente no projeto Azure Migrate.
    - Se utilizar uma ferramenta de avaliação de terceiros, navegue para o link fornecido para o ISV e ecorra a migração de acordo com as instruções que fornecem.

## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de base de dados

1. Clique em **avaliar e migrar bases de dados**
2. Nas **Bases de Dados,** clique em **Adicionar ferramentas**.
3. Adicione uma ferramenta > **Selecione**a ferramenta de migração que pretende utilizar para migrar a sua base de dados.

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicativos web

1. Clique em **Avaliar e migrar aplicações web**.
2. Siga o link para a ferramenta Migração para o Serviço de Aplicações Azure. Utilize a ferramenta de migração para:

    - **Avaliar aplicações online**: Pode avaliar e migrar aplicações com um URL público online, utilizando o Assistente de Migração do Serviço de Aplicações Azure.
    - **.NET/PHP**: Para aplicações internas .NET e PHP, pode descarregar e executar o Assistente de Migração.

## <a name="order-an-azure-data-box"></a>Encomende uma caixa de dados Azure

Para migrar grandes quantidades de dados para o Azure, pode encomendar uma Caixa Azure DAta para transferência de dados offline.

1. Clique **em Encomendar uma Caixa de Dados**.
2. Em **Select your Azure Data Box,** especifique a sua subscrição. 
3. A transferência será uma importação para Azure. Especifique a fonte de dados e o destino da região de Azure para os dados.

## <a name="next-steps"></a>Passos seguintes

Experimente uma migração utilizando a migração do servidor de migração azure migrate para VMs [hiper-V](tutorial-migrate-hyper-v.md) ou [VMware.](tutorial-migrate-vmware.md)
