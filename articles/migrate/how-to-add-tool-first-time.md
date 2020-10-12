---
title: Adicione uma ferramenta de avaliação/migração em Azure Migrate
description: Descreve como criar um projeto Azure Migrate e adicionar uma ferramenta de avaliação/migração.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: a94e3cc18f46c457d6ed54ef88c62adefb07c5b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86102536"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar uma ferramenta de avaliação ou migração a um projeto [Azure Migrate](./migrate-services-overview.md) pela primeira vez.  
A Azure Migrate fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para Azure. O hub fornece ferramentas Azure Migrate para avaliação e migração, bem como outras ferramentas e [ofertas independentes](migrate-services-overview.md#isv-integration) de fornecedores de software (ISV). 

## <a name="check-permissions-to-create-project"></a>Verifique permissões para criar projeto

Se ainda não criou um projeto Azure Migrate, verifique se tem as permissões certas.

1. No portal do Azure, abra a subscrição e selecione **Controlo de acesso (IAM)** .
2. No Acesso ao Cheque, encontre a conta relevante e clique nela para ver permissões. Deve ter permissões de Colaborador ou Proprietário.
    - Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir a função.

## <a name="create-a-project-and-add-a-tool"></a>Criar um projeto e adicionar uma ferramenta

Crie um novo projeto Azure Migrate numa subscrição do Azure e adicione uma ferramenta.

- Um projeto Azure Migrate é usado para armazenar metadados de descoberta, avaliação e migração recolhidos do ambiente que está a avaliar ou migrar. 
- Num projeto é possível rastrear os ativos descobertos, orquestrar avaliação e migração.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.

    ![Configurar Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Em **Descrição geral**, clique em **Avaliar e migrar servidores**.
4. No **âmbito do Discover, avaliar e migrar servidores,** clique em **Avaliar e migrar servidores**.

    ![Detetar e avaliar servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Em **Detetar, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto.  Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    ![Criar um projeto do Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    - A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região-alvo para a migração real.
    - Se precisar de implementar um projeto dentro de uma região específica numa geografia, use a seguinte API para criar um projeto. Especifique o ID de subscrição, o nome do grupo de recursos e o nome do projeto, juntamente com a localização. Reveja as geografias/regiões para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Clique **em seguida**, e adicione uma ferramenta de avaliação ou migração.

    > [!NOTE]
    > Quando cria um projeto, precisa de adicionar pelo menos uma ferramenta de avaliação ou migração.

5. Na **ferramenta de avaliação Select,** adicione uma ferramenta de avaliação. Se não precisar de uma ferramenta de avaliação, selecione **Skip adicionando uma ferramenta de avaliação por**  >  **enquanto .** 
2. Na **ferramenta de migração Select**, adicione uma ferramenta de migração conforme necessário. Se não precisar de uma ferramenta de migração neste momento, selecione **Skip adicionando uma ferramenta de migração para já**Em  >  **seguida**.
3. Em **Rever + adicionar ferramentas,** rever as definições e clicar em Adicionar **ferramentas**.

Depois de criar o projeto, pode selecionar ferramentas adicionais para avaliação e migração de servidores e cargas de trabalho, bases de dados e aplicações web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, poderá ser necessário criar projetos adicionais da Azure Migrate. Por exemplo, se tiver datacenters em geografias diferentes, ou se precisar de armazenar metadados numa geografia diferente. Criar um projeto adicional da seguinte forma:

1. No projeto Azure Migrate atual, clique em **Servidores** ou **Bases de Dados**.
2. No canto superior direito, clique em **Alterar** ao lado do nome atual do projeto.
3. Em **Definições**, **selecione Clique aqui para criar um novo projeto**.
4. Crie um novo projeto e adicione uma nova ferramenta como descrito no procedimento anterior.

## <a name="next-steps"></a>Passos seguintes

- Começa com [o Azure Migrate: Avaliação do servidor,](migrate-services-overview.md#azure-migrate-server-assessment-tool)ou [Azure Migrate: Migração de servidores](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Se adicionou uma ferramenta ISV, ou Movere, [reveja os passos](prepare-isv-movere.md) para preparar a ligação da ferramenta ao Azure Migrate.
- Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração.](how-to-migrate.md) 
