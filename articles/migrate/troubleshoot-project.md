---
title: Resolver problemas relacionados com projetos do Azure Migrate
description: Ajuda-o a resolver problemas com a criação e gestão de projetos da Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753387"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Resolver problemas relacionados com projetos do Azure Migrate

Este artigo ajuda-o a resolver problemas ao criar e gerir projetos [da Azure Migrate.](migrate-services-overview.md)

## <a name="how-to-add-new-project"></a>Como adicionar novo projeto?

Pode ter vários projetos da Azure Migrate numa subscrição. [Saiba como](./create-manage-projects.md) criar um projeto pela primeira vez, ou adicione projetos [adicionais.](create-manage-projects.md#create-additional-projects)

## <a name="what-azure-permissions-are-needed"></a>Que permissões do Azure são necessárias?

Precisa de permissões de Colaborador ou Proprietário na subscrição para criar um projeto Azure Migrate.

## <a name="cant-find-a-project"></a>Não consigo encontrar um projeto

Encontrar um projeto Azure Migrate existente depende se está a usar a versão atual ou antiga do Azure Migrate. [Seguir](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Não consigo encontrar uma geografia

Você pode criar um projeto Azure Migrate em geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

## <a name="what-are-vm-limits"></a>O que são os limites de VM?

Você pode avaliar até 35.000 VMware VMs ou até 35.000 VMs Hiper-V em um único projeto. Um projeto pode incluir VMware VMs e VMs hiper-V, até os limites de avaliação.

## <a name="can-i-upgrade-old-project"></a>Posso atualizar o projeto antigo?

Os projetos da versão anterior do Azure Migrate não podem ser atualizados. É preciso [criar um novo projeto](./create-manage-projects.md)e adicionar-lhe ferramentas.

## <a name="cant-create-a-project"></a>Não se pode criar um projeto

Se tentar criar um projeto e encontrar um erro de implantação:

- Tente criar o projeto de novo no caso de ser um erro transitório. Em **Implementações,** clique em **Re-implantar para** tentar novamente.
- Verifique se tem permissões de Colaborador ou Proprietário na subscrição.
- Se estiver a implementar uma geografia recém-adicionada, aguarde um pouco de tempo e tente de novo.
- Se receber o erro, "Os pedidos devem conter cabeçalhos de identidade do utilizador", o que pode indicar que não tem acesso ao inquilino do Azure Ative Directory (Azure AD) da organização. Neste caso:
    - Quando você é adicionado a um inquilino AZure AD pela primeira vez, você recebe um convite de e-mail para se juntar ao inquilino.
    - Aceite o convite para ser adicionado ao inquilino.
    - Se não conseguir ver o e-mail, contacte um utilizador com acesso ao inquilino e peça-lhe que [reencaia o convite](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) para si.
    - Depois de receber o e-mail do convite, abra-o e selecione o link para aceitar o convite. Em seguida, assine fora do portal Azure e inscreva-se novamente. (refrescar o navegador não vai funcionar.) Pode então começar a criar o projeto de migração.

## <a name="how-do-i-delete-a-project"></a>Como posso apagar um projeto

[Siga estas instruções](create-manage-projects.md#delete-a-project) para apagar um projeto. Note que quando elimina um projeto, tanto o projeto como os metadados sobre máquinas descobertas no projeto são eliminados.

## <a name="added-tools-dont-show"></a>As ferramentas adicionadas não mostram

Certifique-se de que tem o projeto certo selecionado. No hub Azure Migrate > **Servers** ou em **Bases de Dados,** clique em **Change** next to **Migrate project (Change)** no canto superior direito do ecrã. Escolha o nome correto da subscrição e do projeto > **OK**. A página deve refrescar-se com as ferramentas adicionadas do projeto selecionado.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) a projetos da Azure Migrate.