---
title: Gestão de direitos de resolução de problemas - Azure AD
description: Saiba mais sobre alguns itens que deve verificar para ajudá-lo a resolver a gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e291a032c1aac45ebc783126e69b524e1d0af95b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261687"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Resolução de problemas Azure Gestão de direitos da AD

Este artigo descreve alguns itens que deve verificar para ajudá-lo a resolver a gestão de direitos do Azure Ative Directory (Azure AD).

## <a name="administration"></a>Administração

* Se obtém uma mensagem negada ao configurar a gestão de direitos, e é administrador global, certifique-se de que o seu diretório tem uma [licença Azure AD Premium P2 (ou EMS E5).](entitlement-management-overview.md#license-requirements)

* Se obtém uma mensagem negada de acesso ao criar ou visualizar pacotes de acesso, e é membro de um grupo criador do Catálogo, tem de [criar um catálogo](entitlement-management-catalog-create.md) antes de criar o seu primeiro pacote de acesso.

## <a name="resources"></a>Recursos

* As funções das aplicações são definidas pela aplicação em si e são geridas no Azure Active Directory. Se uma aplicação não tiver quaisquer funções de recurso, a gestão do direito atribui aos utilizadores uma função de **Acesso Predefinido.**

    Note que o portal Azure também pode apresentar diretores de serviço para serviços que não podem ser selecionados como aplicações.  Em particular, **o Exchange Online** e o **SharePoint Online** são serviços, não aplicações que têm funções de recursos no diretório, pelo que não podem ser incluídas num pacote de acesso.  Em vez disso, utilize um licenciamento baseado em grupos para estabelecer uma licença adequada para um utilizador que necessite de acesso a esses serviços.

* Para que um grupo seja um recurso num pacote de acesso, tem de ser passível de modificação no Azure Active Directory.  Os grupos com origem num Azure Active Directory no local não podem ser atribuídos como recursos, pois os atributos de proprietário ou de membro não podem ser alterados no Azure Active Directory.   Os grupos com origem no Exchange Online como Grupos de distribuição também não podem ser modificados no Azure Active Directory. 

* As bibliotecas de documentos e os documentos individuais do SharePoint Online não podem ser adicionados como recursos.  Em vez disso, crie um grupo de [segurança Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md), inclua esse grupo e uma função de site no pacote de acesso, e no SharePoint Online use esse grupo para controlar o acesso à biblioteca ou documento de documentos.

* Se existirem utilizadores que já tenham sido atribuídos a um recurso que quer gerir com um pacote de acesso, confirme que os utilizadores são atribuídos ao pacote de acesso com uma política adequada. Por exemplo, poderá incluir um grupo num pacote de acesso que já tenha utilizadores nesse grupo. Caso esses utilizadores no grupo necessitem de acesso contínuo, têm de ter uma política adequada para os pacotes de acesso para que não percam o acesso ao grupo. Pode atribuir o pacote de acesso ao solicitar que os utilizadores façam o pedido do pacote de acesso que contém esse recurso ou ao atribuir-lhes diretamente o pacote de acesso. Para mais informações, consulte as definições de [pedido de alteração e aprovação para um pacote](entitlement-management-access-package-request-policy.md)de acesso .

* Quando remove um membro de uma equipa, este também é removido do Grupo do Office 365. A remoção da funcionalidade de chat da equipa poderá ser atrasada. Para mais informações, consulte [a adesão ao Grupo.](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)

* Confirme que o seu diretório não está configurado para várias regiões. De momento, a gestão de direitos não suporta localizações de várias regiões no SharePoint Online. Os sites do SharePoint Online têm de estar na localização geográfica predefinida para serem regidos pela gestão de direitos. Para mais informações, consulte [capacidades multi-Geo no OneDrive e SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Utilizadores externos

* Quando um utilizador externo quiser solicitar o acesso a um pacote de acesso, certifique-se de que está a utilizar o **link do portal My Access** para o pacote de acesso. Para mais informações, consulte o [link Share para solicitar um pacote](entitlement-management-access-package-settings.md)de acesso . Se um utilizador externo apenas visitar **myaccess.microsoft.com** e não utilizar o link completo do portal My Access, então verá os pacotes de acesso disponíveis na sua própria organização e não na sua organização.

* Se um utilizador externo não puder solicitar o acesso a um pacote de acesso ou não conseguir aceder a recursos, certifique-se de verificar as suas [definições para utilizadores externos](entitlement-management-external-users.md#settings-for-external-users).

* Se um novo utilizador externo, que tenha previamente iniciado sessão no seu diretório, receber um pacote de acesso que inclui um site do SharePoint Online, o pacote de acesso será mostrado como não completamente entregue até que a conta do utilizador seja aprovisionada no SharePoint Online. Para obter mais informações sobre definições de partilha, consulte Rever as definições de [partilha externa do SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Pedidos

* Quando um utilizador quiser solicitar o acesso a um pacote de acesso, certifique-se de que está a utilizar o **link do portal My Access** para o pacote de acesso. Para mais informações, consulte o [link Share para solicitar um pacote](entitlement-management-access-package-settings.md)de acesso .

* Se abrir o portal Os Meus Acessos com o browser definido como modo privado ou incógnito, tal poderá entrar em conflito com o comportamento de início de sessão. Recomendamos que não utilize o modo privado ou incógnito no browser quando aceder ao portal Os Meus Acessos.

* Quando um utilizador que ainda não esteja no seu diretório iniciar sessão no portal Os Meus Acessos para pedir um pacote de acesso, confirme que este se autentica com a conta profissional. A conta profissional pode ser uma conta no diretório de recursos ou num diretório incluído numa das políticas do pacote de acesso. Se a conta do utilizador não for uma conta profissional ou se o diretório onde se autenticarem não estiver incluído na política, o utilizador não verá o pacote de acesso. Para mais informações, consulte [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md).

* Se um utilizador for bloqueado e impedido de iniciar sessão no diretório de recursos, não poderá pedir acesso no portal Os Meus Acessos. Para o utilizador poder pedir acesso, tem de remover o bloqueio de início de sessão no perfil do utilizador. Para remover o bloco de entrada, no portal Azure, clique no **Diretório Ativo do Azure,** clique em **Utilizadores,** clique no utilizador e clique em **Perfil**. Editar a secção **Definições** e alterar **o sinal de bloco** para **O**. Para mais informações, consulte [Adicionar ou atualizar as informações de perfil de um utilizador utilizando o Diretório Ativo do Azure](../fundamentals/active-directory-users-profile-azure-portal.md).  Também pode verificar se o utilizador foi bloqueado devido a uma política de [Proteção de Identidade](../identity-protection/howto-unblock-user.md).

* No portal My Access, se um utilizador for simultaneamente um solicitor e um executor, não verá o seu pedido de um pacote de acesso na página **aprovações.** Este comportamento é intencional: um utilizador não pode aprovar o seu próprio pedido. Confirme que o pacote de acesso que o utilizar está a pedir tem outros aprovadores configurados na política. Para mais informações, consulte as definições de [pedido de alteração e aprovação para um pacote](entitlement-management-access-package-request-policy.md)de acesso .

### <a name="view-a-requests-delivery-errors"></a>Ver erros de entrega de um pedido

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Pedidos**.

1. Selecione o pedido que pretende ver.

    Se o pedido tiver erros de entrega, o estado de pedido será **não entregue** ou **parcialmente entregue**.

    Se houver erros de entrega, no painel de detalhes do pedido, haverá uma contagem de erros de entrega.

1. Clique na contagem para ver todos os erros de entrega do pedido.

### <a name="reprocess-a-request"></a>Reprocessar um pedido

Se um pedido encontrar um erro, pode reprocessar o pedido para tentar novamente. Só pode reprocessar um pedido que tenha um estado de **Entrega falhado** ou **parcialmente entregue** e uma data completa de menos de uma semana.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Pedidos**.

1. Clique no pedido que pretende reprocessar.

1. No painel de detalhes do pedido, clique em **Reprocessar**pedido .

    ![Reprocessar um pedido falhado](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Cancelar um pedido pendente

Só pode cancelar um pedido pendente que ainda não tenha sido entregue ou cuja entrega falhou.

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **Pedidos**.

1. Clique no pedido que pretende cancelar.

1. No painel de detalhes do pedido, clique em **Cancelar pedido**.

## <a name="multiple-policies"></a>Múltiplas políticas

* A gestão de direitos segue as melhores práticas de privilégio. Quando um utilizador pede acesso a um pacote de acesso que tem múltiplas políticas que se aplicam, a gestão de direitos inclui lógica para ajudar a garantir que políticas mais rigorosas ou mais específicas são priorizadas sobre políticas genéricas. Se uma política for genérica, a gestão dos direitos pode não apresentar a política ao solicitador ou pode selecionar automaticamente uma política mais rigorosa.

* Por exemplo, considere um pacote de acesso com duas políticas para os trabalhadores internos em que ambas as políticas se aplicam ao solicitador. A primeira política é para utilizadores específicos que incluem o solicitador. A segunda política é para todos os utilizadores num diretório do que o solicitador é membro. Neste cenário, a primeira política é automaticamente selecionada para o solicitador porque é mais rigorosa. O solicitador não tem a opção de selecionar a segunda política.

* Quando se aplicam várias políticas, a política que é automaticamente selecionada ou as políticas que são apresentadas ao solicitador baseia-se na seguinte lógica prioritária:

    | Prioridade política | Âmbito |
    | --- | --- |
    | P1 | Utilizadores e grupos específicos no seu diretório OU organizações conectadas específicas |
    | P2 | Todos os membros do seu diretório (excluindo convidados) |
    | P3 | Todos os utilizadores do seu diretório (incluindo convidados) OU Organizações conectadas específicas |
    | P4 | Todas as organizações conectadas OU Todos os utilizadores (todas as organizações conectadas + quaisquer novos utilizadores externos) |
    
    Se alguma política estiver numa categoria de prioridade superior, as categorias de prioridade inferior são ignoradas. Para um exemplo de como várias políticas com a mesma prioridade são exibidas ao solicitador, consulte [Selecione uma política](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Passos seguintes

- [Governar o acesso a utilizadores externos](entitlement-management-external-users.md)
- [Ver relatórios de como os utilizadores tiveram acesso na gestão de direitos](entitlement-management-reports.md)
