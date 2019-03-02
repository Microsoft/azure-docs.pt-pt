---
title: Criar um grupo básico e adicionar membros - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como criar um grupo básico com o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19f912ce2989a11e1a27fd27e584159a70d026d4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245765"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Criar um grupo básico e adicionar membros com o Azure Active Directory
Pode criar um grupo básico através do portal do Microsoft Azure Active Directory (Microsoft Azure AD). Para os fins deste artigo, um grupo básico é adicionado a um único recurso pelo proprietário do recurso (administrador) e inclui membros específicos (funcionários) que precisam de aceder a esse recurso. Para cenários mais complexos, incluindo associações dinâmicas e criação de regras, veja [Documentação da gestão de utilizadores do Azure Active Directory](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Criar um grupo básico e adicionar membros
Pode criar um grupo básico e adicionar os membros ao mesmo tempo.

### <a name="to-create-a-basic-group-and-add-members"></a>Para criar um grupo básico e adicionar membros
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Active Directory**, **Grupos** e, em seguida, selecione **Novo grupo**.

    ![Microsoft Azure AD com Grupos apresentados](media/active-directory-groups-create-azure-portal/group-full-screen.png)

3. Na página **Grupo**, preencha as informações necessárias.

    ![Nova página de grupo, preenchida com informações de exemplo](media/active-directory-groups-create-azure-portal/new-group-blade.png)

    - **Tipo de grupo (obrigatório).** Selecione um tipo de grupo predefinido. Isto inclui:
        
        - **Segurança**. Serve para gerir o acesso de membros e de computadores a recursos partilhados de um grupo de utilizadores. Por exemplo, pode criar um grupo de segurança para uma política de segurança específica. Ao fazê-lo desta maneira, pode dar um conjunto de permissões a todos os membros de uma só vez, em vez de precisar de adicionar permissões a cada membro individualmente. Para obter mais informações sobre a gestão de acesso a recursos, veja [Gerir o acesso a recursos com grupos do Azure Active Directory](active-directory-manage-groups.md).
        
        - **Office 365**. Proporciona oportunidades de colaboração ao conceder aos membros o acesso a uma caixa de correio partilhada, calendário, ficheiros, site do SharePoint, entre outros. Esta opção também lhe permite conceder às pessoas fora da organização acesso ao grupo. Para obter mais informações sobre os Grupos do Office 365, veja [Saiba mais sobre os Grupos do Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

    - **Nome do grupo (obrigatório).** Adicione um nome ao grupo, algo fácil de lembrar e que faça sentido.

    - **Descrição do grupo.** Adicione uma descrição opcional ao grupo.

    - **Tipo de associação (obrigatório).** Selecione um tipo de associação predefinido. Isto inclui:

        - **Atribuído.** Permite-lhe adicionar utilizadores específicos para serem membros deste grupo e terem permissões exclusivas. Para os fins deste artigo, estamos a utilizar esta opção.

        - **Utilizador dinâmico.** Permite-lhe utilizar regras de associação dinâmica para adicionar e remover membros automaticamente. Se os atributos de um membro mudarem, o sistema analisará as regras de grupo dinâmicas do diretório para ver se o membro cumpre os requisitos da regra (é adicionado) ou se já não cumpre os requisitos das regras (é removido).

        - **Dispositivo dinâmico.** Permite-lhe utilizar regras de grupo dinâmicas para adicionar e remover dispositivos automaticamente. Se os atributos de um dispositivo mudarem, o sistema analisa as regras de grupo dinâmicas para o diretório para ver se o dispositivo cumpre os requisitos da regra (é adicionado) ou já não cumpre os requisitos das regras (é removido).

        >[!Important]
        >Pode criar um grupo dinâmico para dispositivos ou utilizadores, mas não para ambos. Também não pode criar um grupo de dispositivos com base nos atributos dos proprietários de dispositivos. As regras de associação de dispositivos só podem referenciar atribuições de dispositivos. Para mais informações sobre como criar um grupo dinâmico para utilizadores e dispositivos, veja [criar um grupo dinâmico e verificar o estado](../users-groups-roles/groups-create-rule.md).

4. Selecione **Criar**.

    O grupo é criado e está pronto para adicionar membros.

5. Selecione a área **Membros** na página **Grupo** e, em seguida, comece a procurar membros para adicionar ao seu grupo na página **Selecionar membros**.

    ![Selecionar membros para o seu grupo durante o processo de criação do grupo](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

6. Quando tiver terminado a adição de membros, escolha **Selecionar**.

    A página **Descrição Geral do Grupo** é atualizada para ver o número de membros que estão agora adicionados ao grupo.

    ![Página Descrição Geral do Grupo com o número de membros realçado](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar o e-mail de boas-vindas

Quando é criado qualquer novo grupo do Office 365, se com a associação dinâmica ou estática, é enviada uma notificação de boas-vindas para todos os utilizadores que são adicionados ao grupo. Quando alterar a todos os atributos de um utilizador ou dispositivo, todas as regras de grupo dinâmico na organização são processadas para possíveis alterações de associação. Em seguida, a utilizadores que são adicionados também recebem a notificação de boas-vindas. Pode desativar esse comportamento [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Passos Seguintes
Agora que adicionou um grupo e, pelo menos, um utilizador, pode:

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Gerir associação ao grupo](active-directory-groups-membership-azure-portal.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Gerir o acesso a aplicações SaaS através de grupos](../users-groups-roles/groups-saasapps.md)

- [Gerir grupos através de comandos do PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Associar ou adicionar uma subscrição do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
