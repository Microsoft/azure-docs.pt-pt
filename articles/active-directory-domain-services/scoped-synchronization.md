---
title: Sincronização âmbito para Serviços de Domínio AD Azure / Microsoft Docs
description: Saiba como utilizar o portal Azure para configurar a sincronização telescópio do AD AD para um domínio gerido pelos Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: joflore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 514932726d9283af0c3fb404f787a10057ce8842
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967856"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Configure sincronização telescópio de Ad AD a Azure Ative Directory Domain Services usando o portal Azure

Para fornecer serviços de autenticação, o Azure Ative Directory Domain Services (Azure AD DS) sincroniza os utilizadores e grupos da Azure AD. Num ambiente híbrido, os utilizadores e grupos de um ambiente de Serviços de Domínio de Diretório Ativo (AD DS) podem ser primeiro sincronizados para Azure AD usando Azure AD Connect e, em seguida, sincronizados para um domínio gerido AZURE AD DS.

Por padrão, todos os utilizadores e grupos de um diretório AD Azure são sincronizados a um domínio gerido. Se tiver necessidades específicas, pode optar por sincronizar apenas um conjunto definido de utilizadores.

Este artigo mostra-lhe como configurar a sincronização telescópio e, em seguida, alterar ou desativar o conjunto de utilizadores com âmbito através do portal Azure. Também pode [completar estes passos utilizando o PowerShell][scoped-sync-powershell].

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][tutorial-create-instance].
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para alterar o âmbito de sincronização Azure AD DS.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização de âmbito

Por padrão, todos os utilizadores e grupos de um diretório AD Azure são sincronizados a um domínio gerido. Se apenas alguns utilizadores precisarem de aceder ao domínio gerido, pode sincronizar apenas essas contas de utilizador. Esta sincronização âmbito é baseada em grupo. Quando configurar a sincronização de âmbito de grupo, apenas as contas de utilizador que pertencem aos grupos especificados são sincronizadas com o domínio gerido. Os grupos aninhados não são sincronizados, apenas os grupos específicos que seleciona.

Pode alterar o âmbito de sincronização quando criar o domínio gerido, ou uma vez implementado. Também pode agora alterar o âmbito da sincronização num domínio gerido existente sem precisar de o recriar.

Para saber mais sobre o processo de sincronização, consulte [a sincronização do Azure AD Domain Services][concepts-sync].

> [!WARNING]
> Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. As seguintes considerações são aplicáveis:
>
>  * Quando altera o âmbito de sincronização para um domínio gerido, ocorre uma ressincronização completa.
>  * Os objetos que já não são necessários no domínio gerido são eliminados. Novos objetos são criados no domínio gerido.

## <a name="enable-scoped-synchronization"></a>Permitir a sincronização de âmbito

Para permitir a sincronização no portal Azure, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Selecione **a sincronização** do menu do lado esquerdo.
1. Para o *tipo de sincronização,* selecione **Scoped**.
1. **Escolha Selecionar grupos,** em seguida, procurar e escolher os grupos a adicionar.
1. Quando todas as alterações forem efetuadas, **selecione Guardar o âmbito de sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar algum tempo a ser concluída.

## <a name="modify-scoped-synchronization"></a>Modificar a sincronização âmbito

Para modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Selecione **a sincronização** do menu do lado esquerdo.
1. Para adicionar um grupo, escolha **+ Selecione os grupos** no topo e, em seguida, escolha os grupos a adicionar.
1. Para remover um grupo do âmbito de sincronização, selecione-o da lista de grupos atualmente sincronizados e escolha **grupos Remover**.
1. Quando todas as alterações forem efetuadas, **selecione Guardar o âmbito de sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar algum tempo a ser concluída.

## <a name="disable-scoped-synchronization"></a>Desativar a sincronização de âmbito

Para desativar a sincronização de âmbito de grupo para um domínio gerido, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Selecione **a sincronização** do menu do lado esquerdo.
1. Altere o *tipo de sincronização* de **Scoped** para **All**e, em seguida, selecione Guardar o âmbito **de sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar algum tempo a ser concluída.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o processo de sincronização, consulte [a sincronização do Azure AD Domain Services][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
