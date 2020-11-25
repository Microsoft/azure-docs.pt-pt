---
title: Criar um grupo básico e adicionar membros - Azure Ative Directory / Microsoft Docs
description: Instruções sobre como criar um grupo básico utilizando o Azure Ative Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e4533334204a3a1cfd46ff27b04ff0c05350dfc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973920"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Crie um grupo básico e adicione membros usando o Azure Ative Directory
Pode criar um grupo básico através do portal do Microsoft Azure Active Directory (Microsoft Azure AD). Para os fins deste artigo, um grupo básico é adicionado a um único recurso pelo proprietário do recurso (administrador) e inclui membros específicos (funcionários) que precisam de aceder a esse recurso. Para cenários mais complexos, incluindo associações dinâmicas e criação de regras, veja [Documentação da gestão de utilizadores do Azure Active Directory](../enterprise-users/index.yml).

## <a name="group-and-membership-types"></a>Tipos de grupo e de membros
Existem vários tipos de grupos e membros. As seguintes informações explicam cada grupo e tipo de membro e por que são usados, para ajudá-lo a decidir que opções usar quando criar um grupo.

### <a name="group-types"></a>Tipos de grupo:
- **Segurança**. Serve para gerir o acesso de membros e de computadores a recursos partilhados de um grupo de utilizadores. Por exemplo, pode criar um grupo de segurança para uma política de segurança específica. Ao fazê-lo desta maneira, pode dar um conjunto de permissões a todos os membros de uma só vez, em vez de ter de adicionar permissões a cada membro individualmente. Um grupo de segurança pode ter utilizadores, dispositivos, grupos e diretores de serviço como seus membros e utilizadores e diretores de serviço como seus proprietários. Para obter mais informações sobre a gestão de acesso a recursos, veja [Gerir o acesso a recursos com grupos do Azure Active Directory](active-directory-manage-groups.md).
- **Microsoft 365**. Proporciona oportunidades de colaboração ao conceder aos membros o acesso a uma caixa de correio partilhada, calendário, ficheiros, site do SharePoint, entre outros. Esta opção também lhe permite conceder às pessoas fora da organização acesso ao grupo. Um grupo Microsoft 365 só pode ter utilizadores como membros. Tanto os utilizadores como os principais de serviço podem ser proprietários de um grupo Microsoft 365. Para obter mais informações sobre os Grupos Microsoft 365, consulte [Saiba mais sobre os Grupos Microsoft 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Tipos de adesão:
- **Designado.** Permite-lhe adicionar utilizadores específicos para serem membros deste grupo e terem permissões exclusivas. Para os fins deste artigo, estamos a utilizar esta opção.
- **Utilizador dinâmico.** Permite-lhe utilizar regras dinâmicas de adesão para adicionar e remover automaticamente os membros. Se os atributos de um membro mudarem, o sistema analisará as regras de grupo dinâmicas do diretório para ver se o membro cumpre os requisitos da regra (é adicionado) ou se já não cumpre os requisitos das regras (é removido).
- **Dispositivo dinâmico.** Permite-lhe utilizar regras de grupo dinâmicas para adicionar e remover dispositivos automaticamente. Se os atributos de um dispositivo mudarem, o sistema analisa as regras de grupo dinâmicas para o diretório para ver se o dispositivo cumpre os requisitos da regra (é adicionado) ou já não cumpre os requisitos das regras (é removido).

    > [!IMPORTANT]
    > Pode criar um grupo dinâmico para dispositivos ou utilizadores, mas não para ambos. Também não pode criar um grupo de dispositivos com base nos atributos dos proprietários de dispositivos. As regras de associação de dispositivos só podem referenciar atribuições de dispositivos. Para obter mais informações sobre a criação de um grupo dinâmico para utilizadores e dispositivos, consulte [Criar um grupo dinâmico e verificar o estado](../enterprise-users/groups-create-rule.md)

## <a name="create-a-basic-group-and-add-members"></a>Criar um grupo básico e adicionar membros
Pode criar um grupo básico e adicionar os membros ao mesmo tempo. Para criar um grupo básico e adicionar membros, utilize o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

1. Procure e selecione **Azure Active Directory**.

1. Na página **Ative Directory,** selecione **Grupos** e, em seguida, selecione **Novo grupo**.

    ![Página AD Azure, com grupos mostrando](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Aparecerá o painel **do Novo Grupo** e deverá preencher as informações necessárias.

    ![Nova página de grupo, preenchida com informações de exemplo](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Selecione um tipo de **grupo** pré-definido . Para obter mais informações sobre os tipos de grupo, consulte [os tipos de grupo e de membros](#group-types).

1. Crie e adicione um **nome de grupo.** Escolha um nome de que se lembre e isso faz sentido para o grupo. Será realizada uma verificação para determinar se o nome já está a ser utilizado por outro grupo. Se o nome já estiver em uso, para evitar o nome duplicado, será solicitado que altere o nome do seu grupo.

1. Adicione um **endereço de e-mail do Grupo** para o grupo, ou guarde o endereço de e-mail que é preenchido automaticamente.

1. **Descrição do grupo.** Adicione uma descrição opcional ao grupo.

1. Selecione um tipo de adesão pré-definido **(obrigatório).** Para obter mais informações sobre os tipos de membros, consulte [os tipos de grupo e de membros](#membership-types).

1. Selecione **Criar**. O grupo é criado e está pronto para adicionar membros.

1. Selecione a área **Membros** na página **Grupo** e, em seguida, comece a procurar membros para adicionar ao seu grupo na página **Selecionar membros**.

    ![Selecionar membros para o seu grupo durante o processo de criação do grupo](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Quando tiver terminado a adição de membros, escolha **Selecionar**.

    A página **Descrição Geral do Grupo** é atualizada para ver o número de membros que estão agora adicionados ao grupo.

    ![Página Descrição Geral do Grupo com o número de membros realçado](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Ligue ou desligue o e-mail de boas-vindas do grupo

Quando qualquer novo grupo Microsoft 365 é criado, seja com adesão dinâmica ou estática, é enviada uma notificação bem-vinda a todos os utilizadores que são adicionados ao grupo. Quando quaisquer atributos de um utilizador ou dispositivo mudam, todas as regras dinâmicas do grupo na organização são processadas para potenciais alterações de membros. Os utilizadores que são adicionados também recebem a notificação de boas-vindas. Pode desligar este comportamento em [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso a aplicações SaaS através de grupos](../enterprise-users/groups-saasapps.md)
- [Gerir grupos através de comandos do PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)