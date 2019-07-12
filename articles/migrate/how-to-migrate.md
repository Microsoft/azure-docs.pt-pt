---
title: Adicionar ferramentas de migração no Azure Migrate
description: Descreve como adicionar ferramentas de migração no hub do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811872"
---
# <a name="add-migration-tools"></a>Adicionar ferramentas de migração

Este artigo descreve como adicionar ferramentas de migração no [do Azure Migrate](migrate-overview.md).

O Azure Migrate fornece um hub de ferramentas para avaliação e migração para o Azure. Ele inclui ferramentas nativas, ferramentas fornecidas por outros serviços do Azure e ofertas de (ISV fornecedor) de terceiros independentes de software.

Se deseja adicionar uma ferramenta de migração e ainda não configurou a projetos do Azure Migrate, siga este [artigo](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Selecionar uma ferramenta de ISV

Se escolher uma [ferramenta de ISV](migrate-services-overview.md#isv-integration) para a migração, pode começar por obter uma licença ou inscrever-se numa avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Implementar a ferramenta e siga as instruções de ferramenta e a documentação para se ligar a área de trabalho da ferramenta com o Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Selecionar um cenário de migração

1. No projeto do Azure Migrate, clique em **descrição geral**.
2. Selecione o cenário de migração que pretende utilizar:

    - Para migrar as máquinas e cargas de trabalho para o Azure, selecione **avaliar e migrar servidores**.
    - Para migrar máquinas SQL no local, selecione **avaliar e migrar bases de dados**.
    - Para migrar aplicações web no local, selecione **avaliar e migrar aplicações web**.
    - Para migrar grandes quantidades de dados no local para o Azure no modo offline, selecione **encomendar um Data Box**.

    ![Cenário de avaliação](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selecione uma ferramenta de migração do servidor

1. Clique em **avaliar e migrar servidores**.
2. Na **do Azure Migrate - servidores**, se ainda não adicionou ainda, ferramentas de migração sob **ferramentas de migração**, selecione **clique aqui para adicionar uma ferramenta de migração**. Se já adicionou ferramentas de migração, na **adicionar mais ferramentas de migração**, selecione **alteração**.

    > [!NOTE]
    > Se precisar de navegar para um projeto diferente, no **do Azure Migrate - servidores**, junto a **ver detalhes de um projeto de migração diferentes**, clique em **clique aqui**.

3. Na **do Azure Migrate**, selecione a ferramenta de migração que pretende utilizar.
    - Se utilizar a migração de servidor de migrar do Azure, pode configurar e executar migrações diretamente no projeto do Azure Migrate.
    - Se usar uma ferramenta de avaliação de terceiros, navegue para a ligação fornecida para o ISV e executar a migração em conformidade com as instruções fornecem.

## <a name="select-a-database-migration-tool"></a>Selecione uma ferramenta de migração de base de dados

1. Clique em **avaliar e migrar bases de dados**
2. Na **bases de dados**, clique em **adicionar ferramentas**.
3. Adicionar uma ferramenta > **ferramenta de migração selecione**, selecione a ferramenta que pretende utilizar para migrar a sua base de dados.

## <a name="select-a-web-app-migration-tool"></a>Selecione uma ferramenta de migração de aplicações web

1. Clique em **avaliar e migrar aplicações web**.
2. Siga a ligação para a ferramenta de migração para o serviço de aplicações do Azure. Utilize a ferramenta de migração para:

    - **Avaliar aplicações online**: Pode avaliar e migrar aplicações com um URL público online, com o Assistente de migração do Azure App Service.
    - **.NET/PHP**: Para aplicações de .NET e o PHP internas, pode transferir e executar o Assistente de migração.

## <a name="order-an-azure-data-box"></a>Ordem de uma caixa de dados do Azure

Para migrar grandes quantidades de dados para o Azure, é possível pedir um Azure DAta Box para a transferência de dados offline.

1. Clique em **encomendar um Data Box**.
2. Na **selecionar o Azure Data Box**, especifique a sua subscrição. 
3. A transferência será uma importação para o Azure. Especifique a origem de dados e o destino de região do Azure para os dados.

## <a name="next-steps"></a>Passos Seguintes

Experimente uma migração com o Azure Migrate Server Migration para [Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware](tutorial-migrate-vmware.md) VMs.
