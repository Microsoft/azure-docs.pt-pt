---
title: Adicione uma organização conectada na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como permitir que pessoas fora da sua organização solicitem pacotes de acesso para que possa colaborar em projetos.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50c5c02327aa9f48a605607de901258827b14896
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783948"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Adicione uma organização conectada na gestão de direitos AD Azure

Com a gestão de direitos do Azure Ative Directory (Azure AD), pode colaborar com pessoas fora da sua organização. Se colaborar frequentemente com os utilizadores num diretório ou domínio AD AZure externo, pode adicioná-los como uma organização conectada. Este artigo descreve como adicionar uma organização conectada para que possa permitir que utilizadores fora da sua organização solicitem recursos no seu diretório.

## <a name="what-is-a-connected-organization"></a>O que é uma organização ligada?

Uma organização conectada é um diretório ou domínio AD Azure externo com o qual você tem uma relação.

Por exemplo, suponha que trabalha no Woodgrove Bank e que quer colaborar com duas organizações externas. Estas duas organizações têm configurações diferentes:

- O Instituto de Design Gráfico utiliza a Azure AD, e os seus utilizadores têm um nome principal de utilizador que termina com *graphicdesigninstitute.com*.
- Contoso ainda não usa Azure AD. Os utilizadores da Contoso têm um nome principal de utilizador que termina com *contoso.com*.

Neste caso, pode configurar duas organizações ligadas. Cria-se uma organização conectada para o Instituto de Design Gráfico e outra para o Contoso. Se adicionar então as duas organizações conectadas a uma política, os utilizadores de cada organização com um nome principal de utilizador que corresponda à política podem solicitar pacotes de acesso. Os utilizadores com um nome principal de utilizador que tenha um domínio de *graphicdesigninstitute.com* corresponderiam à organização ligada ao Instituto de Design Gráfico e seriam autorizados a apresentar pedidos. Os utilizadores com um nome principal de utilizador que tenha um domínio de *contoso.com* corresponderiam à organização ligada à Contoso e também seriam autorizados a solicitar pacotes. E, como o Graphic Design Institute utiliza a Azure AD, qualquer utilizadores com um nome principal que [corresponda](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) a um domínio verificado que seja adicionado ao seu inquilino, como *o graphicdesigninstitute.exemplo,* também poderia solicitar pacotes de acesso utilizando a mesma política.

![Exemplo de organização conectada](./media/entitlement-management-organization/connected-organization-example.png)

A forma como os utilizadores do diretório AD ou do domínio Azure autenticam depende do tipo de autenticação. Os tipos de autenticação para organizações ligadas são:

- Azure AD
- [Federação direta](../external-identities/direct-federation.md)
- [Senha única](../external-identities/one-time-passcode.md) (domínio)

Para uma demonstração de como adicionar uma organização conectada, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Adicionar uma organização associada

Para adicionar um diretório ou domínio AD AD externo como uma organização conectada, siga as instruções nesta secção.

**Função pré-requisito**: *Administrador global* ou *administrador de utilizador*

1. No portal Azure, selecione **Azure Ative Directory**e, em seguida, selecione **A Identity Governance**.

1. No painel esquerdo, selecione **Organizações Conectadas**e, em seguida, selecione **Adicionar organização conectada**.

    ![O botão "Adicionar organização conectada"](./media/entitlement-management-organization/connected-organization.png)

1. Selecione o **separador Básicos** e, em seguida, introduza um nome de exibição e descrição para a organização.

    ![O painel básico "Adicionar organização conectada"](./media/entitlement-management-organization/organization-basics.png)

1. Selecione o **separador de domínio Do Diretório +** e, em seguida, selecione **Adicionar diretório + domínio**.

    O **painel Select directories + domínios** abre.s.

1. Na caixa de pesquisa, introduza um nome de domínio para procurar o diretório ou domínio Azure AD. Certifique-se de inserir todo o nome de domínio.

1. Verifique se o nome da organização e o tipo de autenticação estão corretos. A forma como os utilizadores se inscrevem depende do tipo de autenticação.

    ![O painel "Selecione diretórios + domínios"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. **Selecione Adicionar** para adicionar o diretório ou domínio Azure AD. Atualmente, pode adicionar apenas um diretório AD Ad Azure ou domínio por organização conectada.

    > [!NOTE]
    > Todos os utilizadores do diretório ou domínio Azure AD poderão solicitar este pacote de acesso. Isto inclui utilizadores em Azure AD de todos os subdomínios associados ao diretório, a menos que esses domínios sejam bloqueados pelo negócio Azure AD para negócios (B2B) permitir ou negar a lista. Para obter mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas.](../external-identities/allow-deny-list.md)

1. Depois de adicionar o diretório ou domínio AZure AD, **selecione**Select .

    A organização aparece na lista.

    ![O painel "Diretório + domínio"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecione o **separador Patrocinadores** e, em seguida, adicione patrocinadores opcionais para esta organização conectada.

    Os patrocinadores são utilizadores internos ou externos já no seu diretório que são o ponto de contacto para a relação com esta organização conectada. Os patrocinadores internos são utilizadores membros no seu diretório. Patrocinadores externos são utilizadores convidados da organização conectada que foram previamente convidados e já estão no seu diretório. Os patrocinadores podem ser utilizados como aprovadores quando os utilizadores desta organização conectada solicitarem o acesso a este pacote de acesso. Para obter informações sobre como convidar um utilizador convidado para o seu diretório, consulte [os utilizadores de colaboração do Add Azure Ative Directory B2B no portal Azure](../external-identities/add-users-administrator.md).

    Quando seleciona **Add/Remove,** abre-se um painel no qual pode escolher patrocinadores internos ou externos. O painel apresenta uma lista não filtrada de utilizadores e grupos no seu diretório.

    ![O painel de patrocinadores](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecione o separador **'Rever +' criar,** rever as definições da organização e, em seguida, selecione **Criar**.

    ![O painel "Rever + criar"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Atualizar uma organização conectada 

Se a organização ligada mudar para um domínio diferente, o nome da organização muda ou pretende alterar os patrocinadores, pode atualizar a organização conectada seguindo as instruções desta secção.

**Função pré-requisito**: *Administrador global* ou *administrador de utilizador*

1. No portal Azure, selecione **Azure Ative Directory**e, em seguida, selecione **A Identity Governance**.

1. No painel esquerdo, selecione **Organizações Conectadas**e, em seguida, selecione a organização conectada para abri-la.

1. No painel de visão geral da organização conectada, **selecione Editar** para alterar o nome ou descrição da organização.  

1. No painel **de domínio Do Diretório +,** selecione **Update directy + domínio** para alterar para um diretório ou domínio diferente.

1. No painel **de Patrocinadores,** selecione **Adicionar patrocinadores internos** ou **adicionar patrocinadores externos** para adicionar um utilizador como patrocinador. Para remover um patrocinador, selecione o patrocinador e, no painel direito, selecione **Delete**.


## <a name="delete-a-connected-organization"></a>Excluir uma organização conectada

Se já não tiver uma relação com um diretório ou domínio Azure AD externo, pode eliminar a organização conectada.

**Função pré-requisito**: *Administrador global* ou *administrador de utilizador*

1. No portal Azure, selecione **Azure Ative Directory**e, em seguida, selecione **A Identity Governance**.

1. No painel esquerdo, selecione **Organizações Conectadas**e, em seguida, selecione a organização conectada para abri-la.

1. No painel de visão geral da organização ligada, **selecione Delete** para eliminá-lo.

    Atualmente, só pode excluir uma organização conectada se não houver utilizadores ligados.

    ![O botão delete da organização conectada](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Gerir uma organização conectada programáticamente

Também pode criar, listar, atualizar e eliminar organizações conectadas usando o Microsoft Graph. Um utilizador numa função adequada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode ligar para a API para gerir objetos de organização [conectados](/graph/api/resources/connectedorganization?view=graph-rest-beta) e definir patrocinadores para eles.

## <a name="next-steps"></a>Passos seguintes

- [Gerir o acesso dos utilizadores externos](./entitlement-management-external-users.md)
- [Regule o acesso aos utilizadores que não estão no seu diretório](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)