---
title: Crie um grupo básico e adicione membros - Azure Ative Diretórios [ Azure Ative Diretório ] Microsoft Docs
description: Instruções sobre como criar um grupo básico usando o Diretório Ativo Azure.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c551a81788df8d68664abaa03f467a4521222d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73473465"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Crie um grupo básico e adicione membros usando o Diretório Ativo Azure
Pode criar um grupo básico através do portal do Microsoft Azure Active Directory (Microsoft Azure AD). Para os fins deste artigo, um grupo básico é adicionado a um único recurso pelo proprietário do recurso (administrador) e inclui membros específicos (funcionários) que precisam de aceder a esse recurso. Para cenários mais complexos, incluindo associações dinâmicas e criação de regras, veja [Documentação da gestão de utilizadores do Azure Active Directory](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Criar um grupo básico e adicionar membros
Pode criar um grupo básico e adicionar os membros ao mesmo tempo.

### <a name="to-create-a-basic-group-and-add-members"></a>Para criar um grupo básico e adicionar membros
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

1. Procure e selecione **Azure Active Directory**.

    ![Página da AD Azure, com grupos mostrando](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. Na página **do Diretório Ativo,** selecione **Grupos** e, em **seguida,** selecione Novo grupo .

    ![Página da AD Azure, com grupos mostrando](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Na página do **Novo Grupo,** preencha as informações necessárias.

    ![Nova página de grupo, preenchida com informações de exemplo](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Tipo de grupo (obrigatório).** Selecione um tipo de grupo predefinido. Isto inclui: - **Segurança**. Serve para gerir o acesso de membros e de computadores a recursos partilhados de um grupo de utilizadores. Por exemplo, pode criar um grupo de segurança para uma política de segurança específica. Ao fazê-lo desta maneira, pode dar um conjunto de permissões a todos os membros de uma só vez, em vez de precisar de adicionar permissões a cada membro individualmente. Para obter mais informações sobre a gestão de acesso a recursos, veja [Gerir o acesso a recursos com grupos do Azure Active Directory](active-directory-manage-groups.md).
               - **Escritório 365.** Proporciona oportunidades de colaboração ao conceder aos membros o acesso a uma caixa de correio partilhada, calendário, ficheiros, site do SharePoint, entre outros. Esta opção também lhe permite conceder às pessoas fora da organização acesso ao grupo. Para obter mais informações sobre os Grupos do Office 365, veja [Saiba mais sobre os Grupos do Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Nome do grupo (obrigatório).** Adicione um nome ao grupo, algo fácil de lembrar e que faça sentido. Será feita uma verificação para determinar se o nome já é usado para outro grupo. Se o nome já estiver em uso, para evitar o nome duplicado, será-lhe pedido que modifique o nome do seu grupo.

   - **Endereço de e-mail de grupo (obrigatório).** Adicione um endereço de e-mail para o grupo ou guarde o endereço de e-mail que é preenchido automaticamente.

   - **Descrição do grupo.** Adicione uma descrição opcional ao grupo.

   - **Tipo de associação (obrigatório).** Selecione um tipo de associação predefinido. Isto inclui: - **Atribuído.** Permite-lhe adicionar utilizadores específicos para serem membros deste grupo e terem permissões exclusivas. Para os fins deste artigo, estamos a utilizar esta opção.
          - **Utilizador dinâmico.** Permite-lhe utilizar regras dinâmicas de adesão para adicionar e remover automaticamente os membros. Se os atributos de um membro mudarem, o sistema analisará as regras de grupo dinâmicas do diretório para ver se o membro cumpre os requisitos da regra (é adicionado) ou se já não cumpre os requisitos das regras (é removido).
          - **Dispositivo dinâmico.** Permite-lhe utilizar regras de grupo dinâmicas para adicionar e remover dispositivos automaticamente. Se os atributos de um dispositivo mudarem, o sistema analisa as regras de grupo dinâmicas para o diretório para ver se o dispositivo cumpre os requisitos da regra (é adicionado) ou já não cumpre os requisitos das regras (é removido).

        >[!Important]
        >Pode criar um grupo dinâmico para dispositivos ou utilizadores, mas não para ambos. Também não pode criar um grupo de dispositivos com base nos atributos dos proprietários de dispositivos. As regras de associação de dispositivos só podem referenciar atribuições de dispositivos. Para obter mais informações sobre a criação de um grupo dinâmico para utilizadores e dispositivos, consulte [Criar um grupo dinâmico e verificar o estado](../users-groups-roles/groups-create-rule.md).

1. Selecione **Criar**.

    O grupo é criado e está pronto para adicionar membros.

1. Selecione a área **Membros** na página **Grupo** e, em seguida, comece a procurar membros para adicionar ao seu grupo na página **Selecionar membros**.

    ![Selecionar membros para o seu grupo durante o processo de criação do grupo](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Quando tiver terminado a adição de membros, escolha **Selecionar**.

    A página **Descrição Geral do Grupo** é atualizada para ver o número de membros que estão agora adicionados ao grupo.

    ![Página Descrição Geral do Grupo com o número de membros realçado](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Ligue ou desligue e-mail de boas-vindas

Quando for criado um novo grupo office 365, seja com adesão dinâmica ou estática, é enviada uma notificação de boas-vindas a todos os utilizadores que sejam adicionados ao grupo. Quando qualquer atributo de um utilizador ou dispositivo muda, todas as regras dinâmicas do grupo na organização são processadas para potenciais alterações de adesão. Os utilizadores que são adicionados também recebem a notificação de boas-vindas. Pode desligar este comportamento em [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Passos seguintes
Agora que adicionou um grupo e, pelo menos, um utilizador, pode:

- [Ver os seus grupos e membros](active-directory-groups-view-azure-portal.md)

- [Gerir associação ao grupo](active-directory-groups-membership-azure-portal.md)

- [Gerir regras dinâmicas dos utilizadores num grupo](../users-groups-roles/groups-create-rule.md)

- [Editar as suas definições de grupo](active-directory-groups-settings-azure-portal.md)

- [Gerir o acesso a recursos através de grupos](active-directory-manage-groups.md)

- [Gerir o acesso a aplicações SaaS através de grupos](../users-groups-roles/groups-saasapps.md)

- [Gerir grupos através de comandos do PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Associar ou adicionar uma subscrição Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md)
