---
title: Adicione ferramentas de avaliação em Azure Migrate
description: Saiba como adicionar ferramentas de avaliação no Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185919"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação em [Azure Migrate](migrate-overview.md).

A Azure Migrate fornece um centro de ferramentas para avaliação e migração para Azure. Inclui ferramentas Azure Migrate, bem como outras ferramentas e ofertas independentes de fornecedores de software (ISV).

Se quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto Azure Migrate, siga este [artigo.](how-to-add-tool-first-time.md)

## <a name="select-a-tool"></a>Selecione uma ferramenta

Se escolher uma ferramenta não-Azure Migrate para avaliação, comece por obter uma licença ou se inscreva para um teste gratuito, de acordo com a política de ferramentas. As ferramentas têm uma opção para se conectar em Azure Migrate. Siga as instruções e documentação, para ligar a ferramenta ao Azure Migrate. [Saiba mais](migrate-services-overview.md) sobre ferramentas.


## <a name="select-an-assessment-scenario"></a>Selecione um cenário de avaliação

1. No projeto Azure Migrate, clique em **Visão Geral**.
2. Selecione o cenário de avaliação que pretende utilizar:

    - Para descobrir e avaliar máquinas e cargas de trabalho para migração para Azure, selecione **servidores de Avaliação e migração.**
    - Para avaliar as máquinas SQL no local, selecione **avaliar e migrar bases**de dados .
    - Para avaliar as aplicações web no local, selecione **Avaliar e migrar aplicações web.**

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 

1. Clique em **Avaliar e migrar servidores**.
2. No **Azure Migrate - Servidores,** se não tiver adicionado uma ferramenta de avaliação, sob ferramentas de **avaliação,** selecione **Clique aqui para adicionar uma ferramenta de avaliação**. Se já adicionou ferramentas de avaliação, em Adicionar mais ferramentas de **avaliação,** selecione **Change**.

    > [!NOTE]
    > Se precisar de navegar para um projeto diferente, em **Azure Migrate - Servers,** ao lado de **Ver detalhes para um projeto migratório diferente,** clique em Clique **aqui**.

3. No **Azure Migrate,** selecione a ferramenta de avaliação que pretende utilizar.

    - Se utilizar a Avaliação do Servidor Migratório Azure, pode configurar, executar e visualizar avaliações diretamente no projeto Azure Migrate.
    - Se utilizar uma ferramenta de avaliação diferente, navegue para o link fornecido para o seu site e eexecute a avaliação de acordo com as instruções que fornecem.


## <a name="select-a-database-assessment-tool"></a>Selecione uma ferramenta de avaliação de base de dados

1. Clique em **avaliar e migrar bases de dados**
2. Nas **Bases de Dados,** clique em **Adicionar ferramentas**.
3. Adicione uma ferramenta > **Selecione**a ferramenta de avaliação que pretende utilizar para avaliar a sua base de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativos web

1. Clique em **Avaliar e migrar aplicações web**.
2. Siga o link para a ferramenta Migração para o Serviço de Aplicações Azure. Utilize a ferramenta de migração para:

    - **Avaliar aplicações online**: Pode avaliar as aplicações com um URL público online, utilizando o Assistente de Migração do Serviço de Aplicações Azure.
    - **.NET/PHP**: Para aplicações internas .NET e PHP, pode descarregar e executar o Assistente de Migração.



## <a name="next-steps"></a>Passos seguintes

Experimente uma avaliação utilizando a Avaliação do Servidor Migrado Azure para [VMware VMs,](tutorial-prepare-vmware.md) [Hiper-V,](tutorial-prepare-hyper-v.md)ou [servidores físicos](tutorial-prepare-physical.md)
