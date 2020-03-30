---
title: Adicione uma ferramenta de avaliação/migração em Azure Migrate
description: Descreve como criar um projeto Azure Migrate e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185940"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar pela primeira vez uma ferramenta de avaliação ou migração a um projeto [Azure Migrate.](migrate-overview.md)  
A Azure Migrate fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para o Azure. O hub fornece ferramentas Azure Migrate para avaliação e migração, bem como outras ferramentas e [ofertas independentes](migrate-services-overview.md#isv-integration) de fornecedores de software (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Criar um projeto e adicionar uma ferramenta

Criar um novo projeto Azure Migrate numa subscrição azure e adicionar uma ferramenta.

- Um projeto Azure Migrate é usado para armazenar metadados de descoberta, avaliação e migração recolhidos do ambiente que você está avaliando ou migrando. 
- Num projeto pode rastrear bens descobertos, e orquestrar a avaliação e migração.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.

    ![Criar o Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. Em **Discover, avalie e emigra os servidores,** clique em **avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
3. Em Detalhes do **Projeto,** especifique o nome do projeto e geografia em que pretende criar o projeto. 

    ![Criar um projeto Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Você pode criar um projeto Azure Migrate em qualquer uma destas geografias.

   **Geografia** | **Região de localização de armazenamento**
    --- | ---
    Ásia   | Sudeste Asiático ou Leste asiático
    Europa | Norte da Europa ou Europa Ocidental
    Japão  | Japão Leste ou Japão Oeste
    Reino Unido | Reino Unido Sul ou Reino Unido Oeste
    Estados Unidos | Centro dos EUA ou Oeste DOS EUA 2
    Canadá | Canadá Central
    Índia  | Índia Central ou Índia Sul
    Austrália | Austrália Sudeste

    A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região-alvo para a migração real.

    Se pretender especificar uma região específica dentro de uma geografia para a implementação do projeto migratório e dos seus recursos associados (Restrições de política na sua subscrição podem permitir a implantação de recursos Azure apenas para uma região específica do Azure), pode utilizar a API abaixo para criar um projeto de migração. Especifique o ID de subscrição, nome do grupo de recursos, nome do projeto migrate juntamente com a localização (qualquer uma das regiões azure mencionadas no quadro onde o Azure Migrate é implantado.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Clique em **Seguinte**, e adicione uma ferramenta de avaliação ou migração.

    > [!NOTE]
    > Quando cria um projeto, é necessário adicionar pelo menos uma ferramenta de avaliação ou migração.

5. Na ferramenta de **avaliação Select,** adicione uma ferramenta de avaliação. Se não precisar de uma ferramenta de avaliação, selecione **Skip adicionando uma ferramenta** > de avaliação para já**Seguinte**. 
2. Na **ferramenta de migração Select,** adicione uma ferramenta de migração conforme necessário. Se não precisar de uma ferramenta de migração agora, selecione **Skip adicionando uma ferramenta** > de migração para já**Seguinte**.
3. Em **Rever + adicionar ferramentas,** reveja as definições e clique em Adicionar **ferramentas**.

Após a criação do projeto, pode selecionar ferramentas adicionais para avaliação e migração de servidores e cargas de trabalho, bases de dados e aplicações web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, você pode precisar de criar projetos adicionais de migração Azure. Por exemplo, se tiver datacenters em diferentes geografias, ou precisar de armazenar metadados numa geografia diferente. Crie um projeto adicional da seguinte forma:

1. No atual projeto Azure Migrate, clique em **Servidores** ou **Bases de Dados**.
2. No canto superior direito, clique em **Alterar** ao lado do nome atual do projeto.
3. Em **Definições,** selecione **Clique aqui para criar um novo projeto**.
4. Crie um novo projeto e adicione uma nova ferramenta como descrito no procedimento anterior.

## <a name="next-steps"></a>Passos seguintes

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração.](how-to-migrate.md) 
