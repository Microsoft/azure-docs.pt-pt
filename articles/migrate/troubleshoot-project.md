---
title: Solucionar problemas de projetos de migração do Azure
description: Ajuda a solucionar problemas com a criação e o gerenciamento de projetos de migrações para Azure.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725731"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Solucionar problemas de projetos de migração do Azure

Este artigo ajuda a solucionar problemas ao criar e gerenciar projetos de [migrações para Azure](migrate-services-overview.md) .

## <a name="how-to-add-new-project"></a>Como adicionar um novo projeto?

Você pode ter vários projetos de migrações para Azure em uma assinatura. [Saiba como](how-to-add-tool-first-time.md) criar um projeto pela primeira vez ou [Adicionar outros](create-manage-projects.md#create-additional-projects) projetos.

## <a name="what-azure-permissions-are-needed"></a>Quais permissões do Azure são necessárias?

Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.

## <a name="cant-find-a-project"></a>Não é possível localizar um projeto

Encontrar um projeto de migrações para Azure existente depende se você está usando a versão atual ou antiga das migrações para Azure. [Seguir](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Não é possível encontrar uma geografia

Você pode criar um projeto de migrações [para Azure em geografias com suporte](migrate-support-matrix.md#supported-geographies). Observe que a geografia do projeto é usada para armazenar metadados de máquina descobertos. Você também pode avaliar ou migrar computadores em outros locais.

## <a name="what-are-vm-limits"></a>O que são limites de VM?

Você pode avaliar até 35.000 VMs VMware ou até 35.000 VMs Hyper-V em um único projeto. Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.

## <a name="can-i-upgrade-old-project"></a>Posso atualizar o projeto antigo?

Os projetos da versão anterior do migrações para Azure não podem ser atualizados. Você precisa [criar um novo projeto](how-to-add-tool-first-time.md)e adicionar ferramentas a ele.

## <a name="cant-create-a-project"></a>Não é possível criar um projeto

Se você tentar criar um projeto e encontrar um erro de implantação:

- Tente criar o projeto novamente caso seja um erro transitório. Em **implantações**, clique em **reimplantar** para tentar novamente.
- Verifique se você tem permissões de proprietário ou colaborador na assinatura.
- Se você estiver implantando em uma geografia recém adicionada, aguarde um pouco e tente novamente.
- Se você receber o erro "as solicitações devem conter cabeçalhos de identidade do usuário", isso pode indicar que você não tem acesso ao locatário do Azure Active Directory (Azure AD) da organização. Neste caso:
    - Quando você é adicionado a um locatário do Azure AD pela primeira vez, você recebe um convite por email para ingressar no locatário.
    - Aceite o convite a ser adicionado ao locatário.
    - Se você não conseguir ver o email, entre em contato com um usuário com acesso ao locatário e peça para [reenviar o convite](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) para você.
    - Depois de receber o email de convite, abra-o e selecione o link para aceitar o convite. Em seguida, saia do portal do Azure e entre novamente. (a atualização do navegador não funcionará.) Em seguida, você pode começar a criar o projeto de migração.

## <a name="how-do-i-delete-a-project"></a>Como fazer excluir um projeto

[Siga estas instruções](create-manage-projects.md#delete-a-project) para excluir um projeto. Observe que quando você exclui um projeto, o projeto e os metadados sobre computadores descobertos no projeto são excluídos.

## <a name="added-tools-dont-show"></a>As ferramentas adicionadas não são mostradas

Verifique se você tem o projeto correto selecionado. No Hub migrações para Azure > **servidores** ou em **bancos de dados**, clique em **alterar** ao lado de **migrar projeto (alterar)** no canto superior direito da tela. Escolha a assinatura correta e o nome do projeto > **OK**. A página deve ser atualizada com as ferramentas adicionadas do projeto selecionado.

## <a name="next-steps"></a>Passos seguintes

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) para projetos de migrações para Azure.