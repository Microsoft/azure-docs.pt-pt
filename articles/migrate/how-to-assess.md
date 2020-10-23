---
title: Adicionar ferramentas de avaliação em Azure Migrate
description: Saiba como adicionar ferramentas de avaliação no Azure Migrate.
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 5dee9ec2210bf6e04255b07cf641ced6807c8b5a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317355"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação em [Azure Migrate](./migrate-services-overview.md). 

- Se quiser adicionar uma ferramenta de avaliação e ainda não tiver um projeto Azure Migrate, siga este [artigo](how-to-add-tool-first-time.md).-
- Se adicionou uma ferramenta ISV, ou Movere, para avaliação, [siga os passos,](prepare-isv-movere.md)para se preparar para trabalhar com a ferramenta.

## <a name="select-an-assessment-scenario"></a>Selecione um cenário de avaliação

1. No projeto Azure Migrate, clique em **Visão Geral**.
2. Selecione o cenário de avaliação que pretende utilizar:

    - Para descobrir e avaliar máquinas e cargas de trabalho para a migração para Azure, **selecione Avaliar e migrar servidores**.
    - Para avaliar as bases de dados do SQL Server no local, selecione **Avaliar e migrar bases de dados**.
    - Para avaliar as aplicações web no local, selecione **Avaliar e migrar aplicações web**.

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 

1. Clique em **Avaliar e Migrar Servidores**.
2. No **Azure Migrate - Servidores,** se não tiver adicionado uma ferramenta de avaliação, em **ferramentas de avaliação,** selecione **Clique aqui para adicionar uma ferramenta de avaliação**. Se já adicionou ferramentas de avaliação, em **Adicionar mais ferramentas de avaliação,** selecione **Change**.

    > [!NOTE]
    > Se precisar de navegar para um projeto diferente, em **Azure Migrate - Servidores**, ao lado **de Ver detalhes para um projeto migratório diferente,** clique aqui em Clique **aqui**.

3. Em **Azure Migrate,** selecione a ferramenta de avaliação que pretende utilizar.

    - Se utilizar a Avaliação do Servidor Azure Migrate, pode configurar, executar e visualizar avaliações diretamente no projeto Azure Migrate.
    - Se utilizar uma ferramenta de avaliação diferente, navegue até ao link fornecido para o seu site e faça a avaliação de acordo com as instruções que fornecerem.


## <a name="select-a-database-assessment-tool"></a>Selecione uma ferramenta de avaliação de bases de dados

1. Clique em **Avaliar e migrar bases de dados**
2. Em **Bases de Dados,** clique em **Adicionar ferramentas**.
3. In Adicionar uma ferramenta > **Selecione a ferramenta de avaliação,** selecione a ferramenta que pretende utilizar para avaliar a sua base de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicativos web

1. Clique **em Avaliar e migrar aplicações web.**
2. Siga o link para a ferramenta migração para o Serviço de Aplicações Azure. Utilize a ferramenta de migração para:

    - **Avaliar aplicativos online**: Pode avaliar aplicações com um URL público on-line, utilizando o Assistente de Migração do Serviço de Aplicações Azure.
    - **.NET/PHP**: Para aplicações internas .NET e PHP, pode descarregar e executar o Assistente de Migração.



## <a name="next-steps"></a>Passos seguintes

Experimente uma avaliação utilizando a avaliação do servidor Azure Migrate para [VMware](./tutorial-discover-vmware.md) VMs, [Hiper-V](./tutorial-discover-hyper-v.md)ou [servidores físicos](./tutorial-discover-physical.md)