---
title: Resolver problemas relacionados com projetos do Azure Migrate
description: Ajuda-o a resolver problemas com a criação e gestão de projetos da Azure Migrate.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535405"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Resolver problemas relacionados com projetos do Azure Migrate

Este artigo ajuda-o a resolver problemas ao criar e gerir projetos [da Azure Migrate.](migrate-services-overview.md)

## <a name="how-to-add-new-project"></a>Como adicionar novo projeto?

Você pode ter vários projetos Azure Migrate em uma subscrição. [Aprenda](how-to-add-tool-first-time.md) a criar um projeto pela primeira vez, ou adicione projetos [adicionais.](create-manage-projects.md#create-additional-projects)

## <a name="what-azure-permissions-are-needed"></a>Que permissões azure são necessárias?

Você precisa de permissões de Colaborador ou Proprietário na subscrição para criar um projeto Azure Migrate.

## <a name="cant-find-a-project"></a>Não consigo encontrar um projeto.

Encontrar um projeto Azure Migrate existente depende se está a usar a versão atual ou antiga do Azure Migrate. [Siga.](create-manage-projects.md#find-a-project)


## <a name="cant-find-a-geography"></a>Não consigo encontrar uma geografia

Você pode criar um projeto Azure Migrate em geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="what-are-vm-limits"></a>O que são os limites vm?

Você pode avaliar até 35.000 VMs VMware ou até 35.000 VMs Hiper-V em um único projeto. Um projeto pode incluir vMs VMware e VMs Hiper-V, até os limites de avaliação.

## <a name="can-i-upgrade-old-project"></a>Posso atualizar o projeto antigo?

Os projetos da versão anterior do Azure Migrate não podem ser atualizados. É necessário [criar um novo projeto](how-to-add-tool-first-time.md)e adicionar-lhe ferramentas.

## <a name="cant-create-a-project"></a>Não pode criar um projeto

Se tentar criar um projeto e encontrar um erro de implantação:

- Tente criar o projeto de novo no caso de ser um erro transitório. Em **Implementações,** clique em **Re-deploy** para tentar novamente.
- Verifique se tem permissões de Contribuinte ou Proprietário na subscrição.
- Se está sintetizador numa geografia recém-adicionada, espere um pouco e tente de novo.
- Se receber o erro, "Os pedidos devem conter cabeçalhos de identidade do utilizador", o que pode indicar que não tem acesso ao inquilino do Azure Ative Directory (Azure AD) da organização. Neste caso:
    - Quando você é adicionado a um inquilino DaD Azure pela primeira vez, você recebe um convite por e-mail para se juntar ao inquilino.
    - Aceite o convite para ser adicionado ao inquilino.
    - Se não conseguir ver o e-mail, contacte um utilizador com acesso ao inquilino e peça-lhe que lhe [envie o convite.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - Depois de receber o e-mail de convite, abra-o e selecione o link para aceitar o convite. Então, assine o portal Azure e inscreva-se novamente. (refrescar o navegador não funcionará.) Pode então começar a criar o projeto de migração.

## <a name="how-do-i-delete-a-project"></a>Como posso apagar um projeto

[Siga estas instruções](create-manage-projects.md#delete-a-project) para apagar um projeto. Note que ao apagar um projeto, tanto o projeto como os metadados sobre máquinas descobertas no projeto são eliminados.

## <a name="added-tools-dont-show"></a>Ferramentas adicionadas não mostram

Certifique-se de que tem o projeto certo selecionado. No hub de Migração Azure > **Servidores** ou em Bases de **Dados,** clique em **Alterar** ao lado do **projeto Migrate (Alterar)** no canto superior direito do ecrã. Escolha a subscrição correta e o nome do projeto > **OK**. A página deve refrescar-se com as ferramentas adicionais do projeto selecionado.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) aos projetos da Azure Migrate.