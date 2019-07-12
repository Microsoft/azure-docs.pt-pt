---
title: Adicionar uma ferramenta de avaliação/migração pela primeira vez no Azure Migrate | Documentos da Microsoft
description: Descreve como criar um projeto do Azure Migrate e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812028"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar uma ferramenta de migração ou avaliação para uma [do Azure Migrate](migrate-overview.md) projeto pela primeira vez.  
O Azure Migrate fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e pública/privada na cloud VMs, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como fabricante de software de terceiros, independentes (ISV) [ofertas](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Crie um projeto e adicionar uma ferramenta

Configurar um novo projeto do Azure Migrate uma subscrição do Azure e adicionar uma ferramenta.

- Projetos do Azure Migrate é utilizado para armazenar a deteção, avaliação e os metadados de migração recolhidos a partir do ambiente está a avaliar ou a migrar. 
- Num projeto pode controlar ativos detetados e orquestrar a avaliação e migração.

1. No portal do Azure > **todos os serviços**, procure **Azure Migrate**.
2. Sob **serviços**, selecione **Azure Migrate**.

    ![Configurar o Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Na **descrição geral**, clique em **avaliar e migrar servidores**.
4. Sob **detetar, avaliar e migrar os servidores**, clique em **avaliar e migrar servidores**.

    ![Detetar e avaliar os servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Na **detetar, avaliar e migrar os servidores**, clique em **adicionar ferramentas**.
2. Na **migrar projeto**, selecione a sua subscrição do Azure e criar um grupo de recursos se não tiver uma.
3. Na **detalhes do projeto**, especifique o nome do projeto e na geografia, no qual pretende criar o projeto. 

    ![Criar um projeto do Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Pode criar um projeto do Azure Migrate em qualquer uma dessas áreas geográficas.

    **Geografia** | **Região de localização de armazenamento**
    --- | ---
    Ásia | Sudeste asiático ou Ásia Oriental
    Europa | Centro-Sul Europa ou Europa Ocidental
    Reino Unido | Sul do Reino Unido ou oeste do Reino Unido
    Estados Unidos | Centro dos E.U.A. ou E.U.A. oeste 2

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região de destino para a migração real.

4. Clique em **seguinte**e adicionar uma ferramenta de migração ou avaliação.

    > [!NOTE]
    > Quando cria um projeto terá de adicionar pelo menos uma ferramenta de migração ou avaliação.

5. Na **ferramenta de avaliação selecione**, adicionar uma ferramenta de avaliação. Se não precisar de uma ferramenta de avaliação, selecione **ignorar a adição de uma ferramenta de avaliação por enquanto** > **seguinte**. 
2. Na **ferramenta de migração selecione**, adicionar uma ferramenta de migração, conforme necessário. Se não precisar de uma ferramenta de migração neste momento, selecione **ignorar a adição de uma ferramenta de migração por enquanto** > **seguinte**.
3. Na **rever + adicionar ferramentas**, reveja as definições e clique em **adicionar ferramentas**.

Depois de criar o projeto, pode selecionar ferramentas adicionais para avaliação e migração de servidores e cargas de trabalho, bases de dados e aplicações web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, poderá ter de criar projetos adicionais do Azure Migrate. Por exemplo, se tem datacenters em diferentes geografias ou tiver de armazenar metadados de uma localização geográfica de diferente. Crie um projeto adicional da seguinte forma:

1. No projeto atual do Azure Migrate, clique em **servidores** ou **bases de dados**.
2. No canto superior direito, clique em **alteração** junto ao nome do projeto atual.
3. Na **configurações**, selecione **clique aqui para criar um novo projeto**.
4. Criar um novo projeto e adicione uma nova ferramenta, conforme descrito no procedimento anterior.

## <a name="next-steps"></a>Passos Seguintes

Saiba como adicionar adicionais [assessment](how-to-assess.md) e [migração](how-to-migrate.md) ferramentas. 
