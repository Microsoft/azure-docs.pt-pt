---
title: Criar uma unidade organizacional (OU) nos Serviços de Domínio da AD azure [ Microsoft Docs'
description: Aprenda a criar e gerir uma Unidade Organizacional Personalizada (OU) num domínio gerido pelo Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 61f951c0dd6561fc8d5a5de6b80e3759fd42eb78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655547"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Criar uma Unidade Organizacional (OU) num domínio gerido pela Azure AD Domain Services

As unidades organizacionais (OUs) nos Serviços de Domínio do Diretório Ativo (AD DS) permitem agrupar logicamente objetos como contas de utilizador, contas de serviço ou contas de computador. Em seguida, pode atribuir administradores a OUs específicos e aplicar a política do grupo para impor configurações de configuração específicas.

Os domínios geridos pela Azure AD DS incluem as seguintes duas OUs incorporadas:

* *AADDC Computers* - contém objetos de computador para todos os computadores que estão unidos ao domínio gerido.
* *Utilizadores AADDC* - inclui utilizadores e grupos sincronizados com o inquilino da AD Azure.

À medida que cria e executa cargas de trabalho que utilizam O DS Azure, poderá ter de criar contas de serviço para aplicações que se autentem. Para organizar estas contas de serviço, muitas vezes cria um OU personalizado no domínio gerido pelo Azure AD DS e, em seguida, cria contas de serviço dentro desse OU.

Num ambiente híbrido, as OUs criadas num ambiente AD DS no local não são sincronizadas com o Azure AD DS. Os domínios geridos pela Azure AD DS utilizam uma estrutura de U. plana. Todas as contas e grupos de utilizadores estão armazenados no contentor *aaddc utilizadores,* apesar de estarem sincronizados de diferentes domínios ou florestas no local, mesmo que tenha configurado uma estrutura hierárquica de Ou lá.

Este artigo mostra-lhe como criar um OU no seu domínio gerido pelo Azure AD DS.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Um VM de gestão do Servidor Windows que se junta ao domínio gerido pelo Azure AD DS.
    * Se necessário, complete o tutorial para criar um VM de [gestão.][tutorial-create-management-vm]
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Considerações e limitações personalizadas da OU

Quando cria OUs personalizadonum domínio gerido pelo Azure AD DS, ganha-se flexibilidade de gestão adicional para a gestão do utilizador e aplica a política de grupo. Em comparação com um ambiente AD DS no local, existem algumas limitações e considerações ao criar e gerir uma estrutura de Ou personalizada em Azure AD DS:

* Para criar OUs personalizados, os utilizadores devem ser membros do grupo de *administradores da AAD DC.*
* Um utilizador que cria um OU personalizado recebe privilégios administrativos (controlo total) sobre esse Ou e é o proprietário de recursos.
    * Por predefinição, o grupo de administradores da *AAD DC* também tem o controlo total da Ou personalizada.
* É criado um OU padrão para *utilizadores AADDC* que contém todas as contas de utilizador sincronizadas do seu inquilino Azure AD.
    * Não é possível mover utilizadores ou grupos da *AADDC Users* OU para o costume de OUs que cria. Apenas as contas ou recursos de utilizador criados no domínio gerido pela AD DS azure podem ser transferidos para OUs personalizados.
* Contas de utilizador, grupos, contas de serviço e objetos de computador que cria sob OUs personalizados não estão disponíveis no seu inquilino Azure AD.
    * Estes objetos não aparecem usando a Microsoft Graph API ou no Azure AD UI; só estão disponíveis no seu domínio gerido pelo Azure AD DS.

## <a name="create-a-custom-ou"></a>Criar um OU personalizado

Para criar um OU personalizado, utilize as Ferramentas Administrativas de Diretório Ativo a partir de um VM filiado em domínios. O Ative Directory Administrative Center permite-lhe visualizar, editar e criar recursos num domínio gerido pela Azure AD DS, incluindo OUs.

> [!NOTE]
> Para criar um OU personalizado num domínio gerido pelo Azure AD DS, deve ser inscrito numa conta de utilizador que seja membro do grupo de administradores da *AAD DC.*

1. Inscreva-se na sua VM de gestão. Para obter passos sobre como se conectar utilizando o portal Azure, consulte [Connect to a Windows Server VM][connect-windows-server-vm].
1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É mostrada uma lista de ferramentas de gestão disponíveis que foram instaladas no tutorial para criar um VM de [gestão.][tutorial-create-management-vm]
1. Para criar e gerir as OUs, selecione **Ative Directory Administrative Center** a partir da lista de ferramentas administrativas.
1. No painel esquerdo, escolha o seu domínio gerido azure AD DS, como *aaddscontoso.com*. É apresentada uma lista das OUs e recursos existentes:

    ![Selecione o seu domínio gerido azure AD DS no Ative Directory Administrative Center](./media/create-ou/create-ou-adac-overview.png)

1. O painel **Tasks** é mostrado no lado direito do Centro Administrativo de Diretório Ativo. Sob o domínio, como *aaddscontoso.com,* selecione **Unidade Organizacional nova >.**

    ![Selecione a opção de criar um novo OU no Ative Directory Administrative Center](./media/create-ou/create-ou-adac-new-ou.png)

1. No diálogo **Create Organizational Unit,** especifique um **nome** para o novo OU, como *MyCustomOu*. Forneça uma breve descrição para a U, como a Custom OU para contas de *serviço.* Se desejar, também pode definir o campo **Managed By** para a U. Para criar o OU personalizado, selecione **OK**.

    ![Criar uma Ou personalizada do Ative Directory Administrative Center](./media/create-ou/create-ou-dialog.png)

1. De volta ao Ative Directory Administrative Center, o OU personalizado está agora listado e está disponível para utilização:

    ![Ou personalizado disponível para uso no Ative Directory Administrative Center](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização das ferramentas administrativas ou sobre a criação e utilização de contas de serviço, consulte os seguintes artigos:

* [Centro Administrativo de Diretório Ativo: Começar](https://technet.microsoft.com/library/dd560651.aspx)
* [Guia Passo a Passo de Contas de Serviço](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
