---
title: 'Azure Active Directory Domain Services: Guia de administração | Documentos da Microsoft'
description: Criar uma unidade organizacional (UO) nos domínios gerido do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: e7ff91a87377deed06fa4214cc1e5764e8b2da92
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246273"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma unidade organizacional (UO) num domínio gerido do Azure AD Domain Services
Domínios de geridos de serviços de domínio do AD do Azure incluem dois contentores incorporados, chamados "Computadores do aad DC" e "Utilizadores do aad DC", respectivamente. O contentor de computadores do aad DC tem objetos de computador para todos os computadores que estão associados ao domínio gerido. O contentor de "Utilizadores do aad DC" inclui os utilizadores e grupos no inquilino do Azure AD. Ocasionalmente, poderá ser necessário criar contas de serviço no domínio gerido para implementar cargas de trabalho. Para essa finalidade, pode criar uma unidade organizacional personalizado (UO) no domínio gerido e crie contas de serviço no UO. Este artigo mostra-lhe como criar uma UO no seu domínio gerido.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:

1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](create-instance.md).
4. Uma máquina de virtual associado a um domínio do qual administra o domínio gerido do Azure AD Domain Services. Se não tiver um computador virtual, siga todas as tarefas descritas no artigo intitulado [associar uma máquina de virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).
5. Terá das credenciais de uma **conta de utilizador que pertencem ao grupo "Administradores do AAD DC"** no seu diretório, para criar uma UO personalizada no seu domínio gerido.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar ferramentas de administração do AD numa máquina de virtual associado a um domínio para administração remota
Domínios de geridos de serviços de domínio do AD do Azure podem ser geridos remotamente utilizando o Active Directory administrativas ferramentas familiares como o Active Directory Centro de administração (ADAC) ou o AD PowerShell. Os administradores de inquilinos não tem privilégios para ligar aos controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Para administrar o domínio gerido, instale a funcionalidade de ferramentas de administração do AD numa máquina virtual associado a um domínio gerido. Veja o artigo intitulado [gerir um domínio do Azure AD Domain Services](manage-domain.md) para obter instruções.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Criar uma unidade organizacional no domínio gerido
Agora que as ferramentas administrativas do AD estão instaladas no domínio associado a um máquina virtual, podemos usar essas ferramentas para criar uma unidade organizacional no domínio gerido. Execute os seguintes passos:

> [!NOTE]
> Apenas os membros do grupo "Administradores do AAD DC" tem os privilégios necessários para criar uma UO personalizada. Certifique-se de que execute os seguintes passos como um utilizador que pertença a este grupo.
>
>

1. No ecrã Iniciar, clique em **ferramentas administrativas**. Deverá ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Para ver o domínio, clique no nome de domínio no painel da esquerda (por exemplo, ' contoso100.com').

    ![ADAC - domínio de vista](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. No lado direito **tarefas** painel, clique em **New** sob o nó de nome de domínio. Neste exemplo, vamos clicar **New** sob o nó 'contoso100(local)' no lado direito **tarefas** painel.

    ![ADAC - nova UO](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Deverá ver a opção para criar uma unidade organizacional. Clique em **unidade organizacional** para iniciar o **Criar unidade organizacional** caixa de diálogo.
6. Na **criar a unidade organizacional** caixa de diálogo, especifique um **nome** para a nova UO. Forneça uma breve descrição para a UO. Também pode definir o **gerido por** campo para a UO. Para criar a UO personalizada, clique em **OK**.

    ![ADAC - criar caixa de diálogo de UO](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. A UO recém-criado deverá agora aparecer no Centro de administração AD (ADAC).

    ![ADAC - UO criada](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>As permissões/de segurança para UOs criadas recentemente
Por predefinição, (membro do grupo "Administradores do AAD DC") que criou a UO personalizada é concedido ao utilizador privilégios administrativos (controlo total) ao longo da UO. O utilizador pode, em seguida, vá em frente e conceder privilégios para outros utilizadores ou para o grupo "Administradores do AAD DC" conforme pretendido. Conforme mostrado na captura de ecrã seguinte, o utilizador 'bob@domainservicespreview.onmicrosoft.com"quem criou a nova unidade organizacional 'MyCustomOU' é concedido controlo total sobre ele.

 ![ADAC - segurança de UO novo](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Notas na administração de UOs personalizadas
Agora que criou uma UO personalizada, pode ir em frente e criar utilizadores, grupos, computadores e contas de serviço nessa UO. Não é possível mover os utilizadores ou grupos do 'utilizadores do aad DC"UO para UOs personalizadas.

> [!WARNING]
> Contas de utilizador, grupos, contas de serviço e objetos de computador que cria UOs personalizadas não estão disponíveis no seu inquilino do Azure AD. Em outras palavras, esses objetos não mostrar com o Azure AD Graph API ou na IU do AD do Azure. Esses objetos só estão disponíveis no seu domínio gerido do Azure AD Domain Services.
>
>

## <a name="related-content"></a>Conteúdo relacionado
* [Gerir um domínio do Azure AD Domain Services](manage-domain.md)
* [Gerir política de grupo para o Azure AD Domain Services](manage-group-policy.md)
* [Centro de administração do Active Directory: Introdução ao](https://technet.microsoft.com/library/dd560651.aspx)
* [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)
