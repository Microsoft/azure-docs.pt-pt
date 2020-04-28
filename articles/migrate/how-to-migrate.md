---
title: Adicione ferramentas de migração em Azure Migrate
description: Aprenda a adicionar ferramentas de migração em Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185967"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração em [Azure Migrate](migrate-overview.md).

A Azure Migrate fornece um centro de ferramentas para avaliação e migração para Azure. Inclui ferramentas nativas, ferramentas fornecidas por outros serviços Azure e ofertas de fornecedores de software independentes de terceiros (ISV).

Se quiser adicionar uma ferramenta de migração e ainda não tiver montado um projeto Azure Migrate, siga este [artigo.](how-to-add-tool-first-time.md)



## <a name="selecting-an-isv-tool"></a>Selecionar uma ferramenta ISV

Se escolher uma [ferramenta ISV](migrate-services-overview.md#isv-integration) para migração, pode começar por obter uma licença, ou inscrever-se para um teste gratuito, de acordo com a política do ISV. Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Implante a ferramenta e siga as instruções e documentação da ferramenta para ligar o espaço de trabalho da ferramenta com o Azure Migrate. 

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
