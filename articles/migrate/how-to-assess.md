---
title: Adicionar ferramentas de avaliação no Azure Migrate | Documentos da Microsoft
description: Descreve como adicionar ferramentas de avaliação no hub do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811651"
---
# <a name="add-assessment-tools"></a>Adicionar ferramentas de avaliação

Este artigo descreve como adicionar ferramentas de avaliação na [do Azure Migrate](migrate-overview.md).

O Azure Migrate fornece um hub de ferramentas para avaliação e migração para o Azure. Ele inclui ferramentas nativas, ferramentas fornecidas por outros serviços do Azure e ofertas de (ISV fornecedor) de terceiros independentes de software.

Se pretender adicionar uma ferramenta de avaliação e ainda não tiver um projeto do Azure Migrate, siga este [artigo](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Selecionar uma ferramenta de ISV

Se escolher uma [ferramenta de ISV](migrate-services-overview.md#isv-integration) para avaliação, pode começar por obter uma licença ou inscrever-se numa avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para ligar ao Azure Migrate. Siga as instruções de ferramenta e a documentação para se ligar a área de trabalho da ferramenta com o Azure Migrate. 


## <a name="select-an-assessment-scenario"></a>Selecionar um cenário de avaliação

1. No projeto do Azure Migrate, clique em **descrição geral**.
2. Selecione o cenário de avaliação que pretende utilizar:

    - Para detetar e avaliar máquinas e cargas de trabalho para a migração para o Azure, selecione **avaliar e migrar servidores**.
    - Para avaliar máquinas SQL no local, selecione **avaliar e migrar bases de dados**.
    - Para avaliar a aplicações web no local, selecione **avaliar e migrar aplicações web**.

    ![Cenário de avaliação](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selecione uma ferramenta de avaliação do servidor 

1. Clique em **avaliar e migrar servidores**.
2. Na **do Azure Migrate - servidores**, se ainda não adicionou uma ferramenta de avaliação, em **ferramentas de avaliação**, selecione **clique aqui para adicionar uma ferramenta de avaliação**. Se já adicionou ferramentas de avaliação na **adicionar mais ferramentas de avaliação**, selecione **alteração**.

    > [!NOTE]
    > Se precisar de navegar para um projeto diferente, no **do Azure Migrate - servidores**, junto a **ver detalhes de um projeto de migração diferentes**, clique em **clique aqui**.

3. Na **do Azure Migrate**, selecione a ferramenta de avaliação que pretende utilizar.

    
    ![Ferramentas de avaliação](./media/how-to-assess/assess-tool.png)

    - Se utilizar a avaliação do servidor de migrar do Azure, pode configurar, executar e ver avaliações diretamente no projeto do Azure Migrate.
    - Se usar uma ferramenta de avaliação de terceiros, navegue para a ligação fornecida para o site e execute a avaliação de acordo com as instruções fornecem.


## <a name="select-a-database-assessment-tool"></a>Selecione uma ferramenta de avaliação da base de dados

1. Clique em **avaliar e migrar bases de dados**
2. Na **bases de dados**, clique em **adicionar ferramentas**.
3. Adicionar uma ferramenta > **ferramenta de avaliação selecione**, selecione a ferramenta que pretende utilizar para avaliar a sua base de dados.

## <a name="select-a-web-app-assessment-tool"></a>Selecione uma ferramenta de avaliação de aplicação web

1. Clique em **avaliar e migrar aplicações web**.
2. Siga a ligação para a ferramenta de migração para o serviço de aplicações do Azure. Utilize a ferramenta de migração para:

    - **Avaliar aplicações online**: Pode avaliar aplicações com um URL público online, usando o Assistente de migração do Azure App Service.
    - **.NET/PHP**: Para aplicações de .NET e o PHP internas, pode transferir e executar o Assistente de migração.



## <a name="next-steps"></a>Passos Seguintes

Experimentar uma avaliação com o Azure Migrate servidor avaliação para [Hyper-V](tutorial-prepare-hyper-v.md) ou [VMware](tutorial-prepare-vmware.md) VMs.
