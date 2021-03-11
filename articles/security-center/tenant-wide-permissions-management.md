---
title: Conceder e solicitar permissões em todo o inquilino no Centro de Segurança Azure
description: Saiba como gerir permissões em todo o inquilino no Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617493"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Conceder e solicitar visibilidade ao inquilina

Um utilizador com o papel de Administrador Ativo (AD) da Azure Ative **Directory** (AD) pode ter responsabilidades em todo o inquilino, mas não tem as permissões do Azure para visualizar essa informação em toda a organização no Azure Security Center. A elevação da permissão é necessária porque as atribuições de funções AZure AD não dão acesso aos recursos da Azure. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Conceder permissões a si mesmo

Para atribuir permissão ao nível do inquilino:

1. Se a sua organização gerir o acesso a recursos com [a Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)ou qualquer outra ferramenta PIM, a função de administrador global deve estar ativa para o utilizador seguindo o procedimento abaixo.

1. Como utilizador do Administrador Global sem uma atribuição no grupo de gestão de raiz do inquilino, abra a página **geral** do Security Center e selecione o link **de visibilidade** em todo o inquilino no banner. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Permitir permissões ao nível do inquilino no Centro de Segurança Azure":::

1. Selecione a nova função Azure a atribuir. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulário para definir as permissões ao nível do inquilino a atribuir ao seu utilizador":::

    > [!TIP]
    > Geralmente, o papel de Administrador de Segurança é necessário para aplicar políticas ao nível da raiz, enquanto o Leitor de Segurança será suficiente para fornecer visibilidade ao nível do inquilino. Para obter mais informações sobre as permissões concedidas por estas funções, consulte a descrição da [função incorporada do Administrador de Segurança](../role-based-access-control/built-in-roles.md#security-admin) ou a descrição da [função incorporada](../role-based-access-control/built-in-roles.md#security-reader)do Leitor de Segurança .
    >
    > Para diferenças entre estas funções específicas do Centro de Segurança, consulte a tabela em [Papéis e as ações permitidas.](security-center-permissions.md#roles-and-allowed-actions)

    A visão organizacional é conseguida através da concessão de funções ao nível do grupo de gestão de raiz do arrendatário.  

1. Faça login fora do portal Azure e, em seguida, faça login novamente.

1. Uma vez elevado acesso, abra ou refresque o Centro de Segurança Azure para verificar se tem visibilidade em todas as subscrições sob o seu inquilino AZure AD. 

O processo simples acima realiza uma série de operações automaticamente para si:

1. As permissões do utilizador são temporariamente elevadas.
1. Utilizando as novas permissões, o utilizador é designado para o papel de Azure RBAC desejado no grupo de gestão de raiz.
1. As permissões elevadas são removidas.

Para obter mais detalhes sobre o processo de elevação da AD Azure, consulte [o acesso da Elevate para gerir todas as subscrições e grupos de gestão da Azure.](../role-based-access-control/elevate-access-global-admin.md)


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Solicite permissões em todo o inquilino quando as suas são insuficientes

Se iniciar sessão no Security Center e vir um banner a dizer-lhe que a sua vista é limitada, pode clicar para enviar um pedido ao administrador global para a sua organização. No pedido, pode incluir o papel que gostaria de ser atribuído e o administrador global tomará uma decisão sobre qual o papel a conceder. 

É a decisão do administrador global de aceitar ou rejeitar estes pedidos. 

> [!IMPORTANT]
> Só pode apresentar um pedido a cada sete dias.

Para solicitar permissões elevadas ao seu administrador global:

1. A partir do portal Azure, abra o Centro de Segurança Azure.

1. Se vires o banner "Estás a ver informações limitadas". selecioná-lo.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner informando um utilizador que pode solicitar permissões em todo o inquilino.":::

1. No formulário de pedido detalhado, selecione a função desejada e a justificação para o porquê destas permissões.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Página de detalhes para solicitar permissões ao largo do inquilino do seu administrador global da Azure":::

1. Selecione **O acesso ao Pedido**.

    Um e-mail é enviado ao administrador global. O e-mail contém um link para o Centro de Segurança onde podem aprovar ou rejeitar o pedido.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-mail para o administrador global para novas permissões":::

    Depois de o administrador global selecionar **Rever o pedido** e concluir o processo, a decisão é enviada por e-mail ao utilizador que solicita. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as permissões do Centro de Segurança na página relacionada:

- [Permissions in Azure Security Center](security-center-permissions.md) (Permissões no Centro de Segurança do Azure)