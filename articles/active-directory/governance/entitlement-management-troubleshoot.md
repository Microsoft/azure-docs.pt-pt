---
title: Solucionar problemas de gerenciamento de direitos-Azure AD
description: Saiba mais sobre alguns itens que você deve verificar para ajudá-lo a solucionar problemas de gerenciamento de direitos Azure Active Directory.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422481"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Solucionar problemas de gerenciamento de direitos do Azure AD

Este artigo descreve alguns itens que você deve verificar para ajudá-lo a solucionar problemas de gerenciamento de direitos do Azure Active Directory (AD do Azure).

## <a name="administration"></a>Administração

* Se você receber uma mensagem de acesso negado ao configurar o gerenciamento de direitos e for um administrador global, verifique se o diretório tem uma [licença Azure ad Premium P2 (ou EMS E5)](entitlement-management-overview.md#license-requirements).

* Se você receber uma mensagem de acesso negado ao criar ou exibir pacotes de acesso e for membro de um grupo de criador de catálogo, deverá [criar um catálogo](entitlement-management-catalog-create.md) antes de criar seu primeiro pacote de acesso.

## <a name="resources"></a>Recursos

* As funções para aplicativos são definidas pelo próprio aplicativo e são gerenciadas no Azure AD. Se um aplicativo não tiver nenhuma função de recurso, o gerenciamento de direitos atribuirá usuários a uma função de **acesso padrão** .

    Observe que o portal do Azure também pode mostrar entidades de serviço para serviços que não podem ser selecionados como aplicativos.  Em particular, o **Exchange Online** e o **SharePoint Online** são serviços, não aplicativos que têm funções de recurso no diretório, para que não possam ser incluídos em um pacote de acesso.  Em vez disso, use o licenciamento baseado em grupo para estabelecer uma licença apropriada para um usuário que precisa acessar esses serviços.

* Para que um grupo seja um recurso em um pacote do Access, ele deve ser possível modificá-lo no Azure AD.  Os grupos que se originam em um Active Directory local não podem ser atribuídos como recursos porque seus atributos de proprietário ou membro não podem ser alterados no Azure AD.   Os grupos que se originam no Exchange Online como grupos de distribuição também não podem ser modificados no Azure AD. 

* As bibliotecas de documentos do SharePoint Online e os documentos individuais não podem ser adicionados como recursos.  Em vez disso, crie um [grupo de segurança do Azure ad](../fundamentals/active-directory-groups-create-azure-portal.md), inclua esse grupo e uma função de site no pacote de acesso e, no SharePoint Online, use esse grupo para controlar o acesso à biblioteca de documentos ou ao documento.

* Se houver usuários que já foram atribuídos a um recurso que você deseja gerenciar com um pacote do Access, certifique-se de que os usuários sejam atribuídos ao pacote do Access com uma política apropriada. Por exemplo, talvez você queira incluir um grupo em um pacote de acesso que já tenha usuários no grupo. Se esses usuários no grupo exigirem acesso contínuo, eles deverão ter uma política apropriada para os pacotes de acesso para que não percam seu acesso ao grupo. Você pode atribuir o pacote de acesso solicitando que os usuários solicitem o pacote de acesso que contém esse recurso ou atribuindo-os diretamente ao pacote de acesso. Para obter mais informações, consulte [alterar as configurações de solicitação e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md).

* Quando você remove um membro de uma equipe, eles são removidos do grupo do Office 365 também. A remoção da funcionalidade de chat da equipe pode ser atrasada. Para obter mais informações, consulte [Associação de grupo](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Verifique se o diretório não está configurado para várias regiões geográficas. O gerenciamento de direitos atualmente não dá suporte a locais de várias regiões geográficas para o SharePoint Online. Os sites do SharePoint Online devem estar na localização geográfica padrão a ser governada com o gerenciamento de direitos. Para obter mais informações, consulte [recursos de várias regiões geográficas no onedrive e no SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Utilizadores externos

* Quando um usuário externo quiser solicitar acesso a um pacote do Access, verifique se ele está usando o **link meu portal de acesso** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md). Se um usuário externo apenas visitar **myaccess.Microsoft.com** e não usar o link completo meu portal de acesso, eles verão os pacotes de acesso disponíveis para eles em sua própria organização e não em sua organização.

* Se um usuário externo não puder solicitar acesso a um pacote de acesso ou não puder acessar recursos, certifique-se de verificar suas [configurações para usuários externos](entitlement-management-external-users.md#settings-for-external-users).

* Se um novo usuário externo, que não tenha sido assinado anteriormente no seu diretório, receber um pacote de acesso, incluindo um site do SharePoint Online, seu pacote de acesso será exibido como não totalmente entregue até que sua conta seja provisionada no SharePoint Online. Para obter mais informações sobre configurações de compartilhamento, consulte [examinar suas configurações de compartilhamento externo do SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Pedidos

* Quando um usuário quiser solicitar acesso a um pacote do Access, verifique se ele está usando o **link meu portal de acesso** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md).

* Se você abrir o portal meu acesso com seu navegador definido como modo in-private ou Incognito, isso poderá entrar em conflito com o comportamento de entrada. Recomendamos que você não use o modo em particular ou Incognito para o navegador ao visitar o portal meu acesso.

* Quando um usuário que ainda não está no seu diretório entra no portal do meu acesso para solicitar um pacote de acesso, certifique-se de autenticar usando sua conta institucional. A conta institucional pode ser uma conta no diretório de recursos ou em um diretório que está incluído em uma das políticas do pacote de acesso. Se a conta do usuário não for uma conta organizacional ou o diretório em que ele se autenticar não estiver incluído na política, o usuário não verá o pacote de acesso. Para obter mais informações, consulte [solicitar acesso a um pacote de acesso](entitlement-management-request-access.md).

* Se um usuário estiver impedido de entrar no diretório de recursos, ele não poderá solicitar acesso no meu portal de acesso. Antes que o usuário possa solicitar acesso, você deve remover o bloco de entrada do perfil do usuário. Para remover o bloco de entrada, na portal do Azure, clique em **Azure Active Directory**, clique em **usuários**, clique no usuário e, em seguida, clique em **perfil**. Edite a seção **configurações** e altere a **entrada de bloco** para **não**. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um usuário usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Você também pode verificar se o usuário foi bloqueado devido a uma [política de proteção de identidade](../identity-protection/howto-unblock-user.md).

* No portal meu acesso, se um usuário for um solicitante e um aprovador, ele não verá a solicitação de um pacote de acesso na página **aprovações** . Esse comportamento é intencional: um usuário não pode aprovar sua própria solicitação. Verifique se o pacote de acesso que eles estão solicitando tem aprovadores adicionais configurados na política. Para obter mais informações, consulte [alterar as configurações de solicitação e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Exibir os erros de entrega de uma solicitação

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Selecione a solicitação que você deseja exibir.

    Se a solicitação tiver erros de entrega, o status da solicitação será sem **entrega** ou **parcialmente entregue**.

    Se houver erros de entrega, no painel de detalhes da solicitação, haverá uma contagem de erros de entrega.

1. Clique na contagem para ver todos os erros de entrega da solicitação.

### <a name="reprocess-a-request"></a>Reprocessar uma solicitação

Se uma solicitação encontrar um erro, você poderá reprocessar a solicitação para tentar novamente. Você só pode reprocessar uma solicitação que tenha um status de **entrega com falha** ou **parcialmente entregue** e uma data de conclusão inferior a uma semana.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique na solicitação que você deseja reprocessar.

1. No painel detalhes da solicitação, clique em **Reprocessar solicitação**.

    ![Reprocessar uma solicitação com falha](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Você só pode cancelar uma solicitação pendente que ainda não foi entregue ou cuja entrega falhou.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **solicitações**.

1. Clique na solicitação que você deseja cancelar.

1. No painel detalhes da solicitação, clique em **Cancelar solicitação**.

## <a name="multiple-policies"></a>Várias políticas

* O gerenciamento de direitos segue as práticas recomendadas de privilégio mínimo. Quando um usuário solicita acesso a um pacote de acesso que tem várias políticas que se aplicam, o gerenciamento de direitos inclui a lógica para ajudar a garantir que as políticas mais rígidas ou mais específicas sejam priorizadas em relação a políticas genéricas. Se uma política for genérica, o gerenciamento de direitos poderá não exibir a política para o solicitante ou poderá selecionar automaticamente uma política mais estrita.

* Por exemplo, considere um pacote de acesso com duas políticas para funcionários internos nos quais ambas as políticas se aplicam ao solicitante. A primeira política é para usuários específicos que incluem o solicitante. A segunda política é para todos os usuários em um diretório do qual o solicitante é membro. Nesse cenário, a primeira política é selecionada automaticamente para o solicitante porque é mais estrita. O solicitante não recebeu a opção de selecionar a segunda política.

* Quando várias políticas se aplicam, a política selecionada automaticamente ou as políticas que são exibidas para o solicitante baseiam-se na seguinte lógica de prioridade:

    | Prioridade da política | Âmbito |
    | --- | --- |
    | P1 | Usuários e grupos específicos em seu diretório ou em organizações conectadas específicas |
    | P2 | Todos os membros em seu diretório (exceto convidados) |
    | P3 | Todos os usuários em seu diretório (incluindo convidados) ou organizações conectadas específicas |
    | P4 | Todas as organizações conectadas ou todos os usuários (todas as organizações conectadas + quaisquer novos usuários externos) |
    
    Se alguma política estiver em uma categoria de prioridade mais alta, as categorias de prioridade mais baixa serão ignoradas. Para obter um exemplo de como várias políticas com a mesma prioridade são exibidas para o solicitante, consulte [selecionar uma política](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Passos seguintes

- [Controlar o acesso para usuários externos](entitlement-management-external-users.md)
- [Exibir relatórios de como os usuários têm acesso no gerenciamento de direitos](entitlement-management-reports.md)
