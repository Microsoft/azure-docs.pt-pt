---
title: Criar uma unidade organizacional (OU) em Azure AD Domain Services / Microsoft Docs'
description: Aprenda a criar e gerir uma Unidade Organizacional personalizada (OU) num domínio gerido por Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620023"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Criar uma Unidade Organizacional (OU) num domínio gerido por Serviços de Domínio do Diretório Ativo Azure

As unidades organizacionais (OUs) num domínio gerido por Serviços de Domínio de Diretório Ativo (DS AD) permitem-lhe, logicamente, agrupar objetos como contas de utilizador, contas de serviço ou contas de computador. Em seguida, pode atribuir administradores a OUs específicos e aplicar a política de grupo para impor configurações de configuração direcionadas.

Os domínios geridos pela Azure AD DS incluem as seguintes duas OUs incorporadas:

* *AADDC Computers* - contém objetos de computador para todos os computadores que estão unidos ao domínio gerido.
* *Utilizadores AADDC* - inclui utilizadores e grupos sincronizados do inquilino AZURE AD.

À medida que cria e executa cargas de trabalho que utilizam O Azure AD DS, poderá ter de criar contas de serviço para as aplicações para se autenticarem. Para organizar estas contas de serviço, muitas vezes cria um OU personalizado no domínio gerido e, em seguida, cria contas de serviço dentro dessa OU.

Num ambiente híbrido, as OUs criadas num ambiente AD DS no local não são sincronizadas com o domínio gerido. Os domínios geridos utilizam uma estrutura plana da U. Todas as contas e grupos de utilizadores são armazenados no contentor *AADDC Users,* apesar de serem sincronizados de diferentes domínios ou florestas no local, mesmo que tenha configurado uma estrutura hierárquica da OU lá.

Este artigo mostra-lhe como criar um OU no seu domínio gerido.

## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido Azure AD DS.
    * Se necessário, complete o tutorial para [criar um VM de gestão.][tutorial-create-management-vm]
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Considerações e limitações personalizadas da OU

Quando cria OUs personalizados num domínio gerido, ganha flexibilidade de gestão adicional para a gestão do utilizador e aplica a política de grupo. Em comparação com um ambiente AD DS no local, existem algumas limitações e considerações ao criar e gerir uma estrutura ou personalizada num domínio gerido:

* Para criar OUs personalizados, os utilizadores devem ser membros do grupo *de administradores da AAD DC.*
* Um utilizador que cria um OU personalizado recebe privilégios administrativos (controlo total) sobre esse OU e é o proprietário do recurso.
    * Por padrão, o grupo *de administradores AAD DC* também tem o controlo total da OU personalizada.
* É criado um OU padrão para *utilizadores AADDC* que contém todas as contas de utilizador sincronizadas do seu inquilino AD AZure.
    * Não é possível mover utilizadores ou grupos do *AADDC Users* OU para as OUs personalizadas que cria. Apenas as contas de utilizador ou recursos criados no domínio gerido podem ser transferidos para OUs personalizados.
* As contas de utilizador, grupos, contas de serviço e objetos de computador que cria sob as OUs personalizadas não estão disponíveis no seu inquilino AZure AD.
    * Estes objetos não aparecem utilizando a API do Gráfico microsoft ou no UI AD Azure; só estão disponíveis no seu domínio gerido.

## <a name="create-a-custom-ou"></a>Criar um OU personalizado

Para criar um OU personalizado, utilize as Ferramentas Administrativas do Diretório Ativo a partir de um VM de domínio. O Ative Directory Administrative Center permite-lhe visualizar, editar e criar recursos num domínio gerido, incluindo OUs.

> [!NOTE]
> Para criar um OU personalizado num domínio gerido, tem de ser inscrito numa conta de utilizador que seja membro do grupo *de Administradores AAD DC.*

1. Inscreva-se na sua gestão VM. Para obter etapas sobre como ligar utilizando o portal Azure, consulte [Connect to a Windows Server VM][connect-windows-server-vm].
1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É mostrada uma lista de ferramentas de gestão disponíveis que foram instaladas no tutorial para [criar um VM de gestão.][tutorial-create-management-vm]
1. Para criar e gerir as OUs, selecione **Ative Directory Administrative Center** a partir da lista de ferramentas administrativas.
1. No painel esquerdo, escolha o seu domínio gerido, como *aaddscontoso.com*. É apresentada uma lista das OUs e dos recursos existentes:

    ![Selecione o seu domínio gerido no Ative Directory Administrative Center](./media/create-ou/create-ou-adac-overview.png)

1. O painel **de tarefas** é mostrado no lado direito do Ative Directory Administrative Center. Sob o domínio, como *aaddscontoso.com,* selecione **New > Organizational Unit**.

    ![Selecione a opção de criar um novo OU no Ative Directory Administrative Center](./media/create-ou/create-ou-adac-new-ou.png)

1. No diálogo **da Unidade Organizacional Create,** especifique um **nome** para o novo U, tal como *MyCustomOu*. Forneça uma breve descrição para a U, como *a Ou personalizada para contas de serviço.* Se desejar, também pode definir o campo **Managed By** para o OU. Para criar o OU personalizado, selecione **OK**.

    ![Criar um OU personalizado a partir do Ative Directory Administrative Center](./media/create-ou/create-ou-dialog.png)

1. De volta ao Ative Directory Administrative Center, o OU personalizado está agora listado e está disponível para uso:

    ![UO personalizado disponível para uso no Ative Directory Administrative Center](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização das ferramentas administrativas ou a criação e utilização de contas de serviço, consulte os seguintes artigos:

* [Ative Directory Administrative Center: Começar](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Guia Passo a Passo de Contas de Serviço](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm