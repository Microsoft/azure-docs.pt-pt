---
title: Adicionar ferramentas de avaliação em migrações para Azure
description: Saiba como adicionar ferramentas de avaliação nas migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185919"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação nas [migrações para Azure](migrate-overview.md).

As migrações para Azure fornecem um hub de ferramentas para avaliação e migração para o Azure. Ele inclui as ferramentas de migrações para Azure, bem como outras ferramentas e ofertas de fornecedores independentes de software (ISV).

Se você quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto de migrações para Azure, siga este [artigo](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Selecionar ferramenta

Se você escolher uma ferramenta de migração não Azure para avaliação, comece obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ferramentas. As ferramentas têm uma opção para se conectar a migrações para Azure. Siga as instruções e a documentação para conectar a ferramenta à migração do Azure. [Saiba mais](migrate-services-overview.md) sobre as ferramentas.


## <a name="select-an-assessment-scenario"></a>Selecionar um cenário de avaliação

1. No projeto migrações para Azure, clique em **visão geral**.
2. Selecione o cenário de avaliação que você deseja usar:

    - Para descobrir e avaliar máquinas e cargas de trabalho para migração para o Azure, selecione **avaliar e migrar servidores**.
    - Para avaliar as máquinas locais do SQL, selecione **avaliar e migrar bancos de dados**.
    - Para avaliar aplicativos Web locais, selecione **avaliar e migrar aplicativos Web**.

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 

1. Clique em **avaliar e migrar servidores**.
2. Em **migrações para Azure-servidores**, se você ainda não tiver adicionado uma ferramenta de avaliação, em **ferramentas de avaliação**, selecione **clique aqui para adicionar uma ferramenta de avaliação**. Se você já tiver adicionado ferramentas de avaliação, em **adicionar mais ferramentas de avaliação**, selecione **alterar**.

    > [!NOTE]
    > Se você precisar navegar para um projeto diferente, no **Migrations-Servers do Azure**, ao lado de **Ver os detalhes de um projeto de migração diferente**, clique **em clique aqui**.

3. Em **migrações para Azure**, selecione a ferramenta de avaliação que você deseja usar.

    - Se você usar a avaliação de servidor de migrações para Azure, poderá configurar, executar e exibir avaliações diretamente no projeto de migrações para Azure.
    - Se você usar uma ferramenta de avaliação diferente, navegue até o link fornecido para seu site e execute a avaliação de acordo com as instruções que eles fornecem.


## <a name="select-a-database-assessment-tool"></a>Selecionar uma ferramenta de avaliação de banco de dados

1. Clique em **avaliar e migrar bancos de dados**
2. Em **bancos de dados**, clique em **Adicionar ferramentas**.
3. Em adicionar uma ferramenta > **selecione ferramenta de avaliação**, selecione a ferramenta que você deseja usar para avaliar seu banco de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativo Web

1. Clique em **avaliar e migrar aplicativos Web**.
2. Siga o link para a ferramenta de migração para o serviço de Azure App. Use a ferramenta de migração para:

    - **Avaliar aplicativos online**: você pode avaliar aplicativos com uma URL pública online, usando o assistente de migração de serviço Azure app.
    - **.Net/php**: para aplicativos internos do .net e PHP, você pode baixar e executar o assistente de migração.



## <a name="next-steps"></a>Passos seguintes

Experimente uma avaliação usando o Azure Migrate Server Assessment para VMs [VMware](tutorial-prepare-vmware.md) , [Hyper-V](tutorial-prepare-hyper-v.md)ou [servidores físicos](tutorial-prepare-physical.md)
