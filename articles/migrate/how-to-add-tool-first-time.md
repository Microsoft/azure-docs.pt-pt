---
title: Adicionar uma ferramenta de avaliação/migração em migrações para Azure
description: Descreve como criar um projeto de migrações para Azure e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185940"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar uma ferramenta de avaliação ou migração a um projeto de [migrações para Azure](migrate-overview.md) pela primeira vez.  
As migrações para Azure fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece as ferramentas de migração do Azure para avaliação e migração, bem como outras ferramentas e [ofertas](migrate-services-overview.md#isv-integration) de fornecedores independentes de software (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Criar um projeto e adicionar uma ferramenta

Configure um novo projeto de migrações para Azure em uma assinatura do Azure e adicione uma ferramenta.

- Um projeto de migrações para Azure é usado para armazenar metadados de descoberta, avaliação e migração coletados do ambiente que você está avaliando ou migrando. 
- Em um projeto, você pode acompanhar os ativos descobertos e orquestrar a avaliação e a migração.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.

    ![Configurar migrações para Azure](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **descobrir, avaliar e migrar servidores**, clique em **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto. 

    ![Criar um projeto de migrações para Azure](./media/how-to-add-tool-first-time/migrate-project.png)

    Você pode criar um projeto de migrações para Azure em qualquer uma dessas regiões.

   **Geografia** | **Região do local de armazenamento**
    --- | ---
    Ásia   | Sudeste da Ásia ou Ásia Oriental
    Europa | Europa Setentrional ou Europa Ocidental
    Japão  | Leste do Japão ou oeste do Japão
    Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
    Estados Unidos | EUA Central ou oeste dos EUA 2
    Canadá | Canadá Central
    Índia  | Índia central ou sul da Índia
    Austrália | Sudeste da Austrália

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Você pode selecionar qualquer região de destino para a migração real.

    Se você quiser especificar uma região específica em uma geografia para implantar o projeto de migração e seus recursos associados (restrições de política em sua assinatura podem permitir a implantação de recursos do Azure somente em uma região específica do Azure), você pode usar a API abaixo para Crie um projeto de migração. Especifique a ID da assinatura, o nome do grupo de recursos, migre o nome do projeto junto com o local (qualquer uma das regiões do Azure mencionadas na tabela em que a migração do Azure está implantada.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Clique em **Avançar**e adicione uma ferramenta de avaliação ou migração.

    > [!NOTE]
    > Ao criar um projeto, você precisa adicionar pelo menos uma ferramenta de avaliação ou migração.

5. Em **selecionar ferramenta de avaliação**, adicione uma ferramenta de avaliação. Se você não precisar de uma ferramenta de avaliação, selecione **ignorar a adição de uma ferramenta de avaliação por enquanto** > **em Avançar**. 
2. Em **selecionar ferramenta de migração**, adicione uma ferramenta de migração conforme necessário. Se você não precisar de uma ferramenta de migração no momento, selecione **ignorar a adição de uma ferramenta de migração por enquanto** > **Avançar**.
3. Em **examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**.

Depois de criar o projeto, você pode selecionar ferramentas adicionais para avaliação e migração de servidores e cargas de trabalho, bancos de dados e aplicativos Web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, talvez seja necessário criar projetos adicionais de migrações para Azure. Por exemplo, se você tiver data centers em geografias diferentes, ou se precisar armazenar metadados em uma geografia diferente. Crie um projeto adicional da seguinte maneira:

1. No projeto atual de migrações para Azure, clique em **servidores** ou **bancos de dados**.
2. No canto superior direito, clique em **alterar** ao lado do nome do projeto atual.
3. Em **configurações**, selecione **clique aqui para criar um novo projeto**.
4. Crie um novo projeto e adicione uma nova ferramenta, conforme descrito no procedimento anterior.

## <a name="next-steps"></a>Passos Seguintes

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração](how-to-migrate.md) . 
