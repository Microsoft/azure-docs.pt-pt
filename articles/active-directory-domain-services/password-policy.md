---
title: 'Azure Active Directory Domain Services: Política de senha | Microsoft Docs'
description: Entender as políticas de senha em domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 8829f16e580d0b926781ce0a3e9f8e6a63cf3110
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853766"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de bloqueio de senha e conta em domínios gerenciados
Este artigo explica as políticas de senha padrão em um domínio gerenciado. Ele também aborda como você pode configurar essas políticas.

## <a name="fine-grained-password-policies-fgpp"></a>FGPP (políticas de senha refinadas)
Use políticas de senha refinadas para especificar muitas políticas de senha em um único domínio. O FGPP permite que você aplique diferentes restrições para políticas de bloqueio de senha e conta para diferentes conjuntos de usuários em um domínio. Por exemplo, você pode aplicar configurações de senha estritas a contas com privilégios.

Você pode definir as seguintes configurações de senha usando FGPP:
* Comprimento mínimo da senha
* Histórico de senha
* As senhas devem atender aos requisitos de complexidade
* Duração mínima da senha
* Duração máxima da senha
* Política de bloqueio de conta
    * Duração do bloqueio de conta
    * Número de tentativas de logon com falha permitidas
    * Redefinir a contagem de tentativas de logon com falha após


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Configurações de política de senha refinadas padrão em um domínio gerenciado
A captura de tela a seguir ilustra a política de senha refinada padrão configurada em um Azure AD Domain Services domínio gerenciado.

![Política de senha refinada padrão](./media/how-to/default-fgpp.png)

> [!NOTE]
> Você não pode modificar ou excluir a política de senha refinada padrão interna. Os membros do grupo "administradores de controlador de domínio do AAD" podem criar FGPP personalizados e configurá-lo para substituir (ter precedência) o FGPP interno padrão.
>
>

## <a name="password-policy-settings"></a>Configurações de política de senha
Em um domínio gerenciado, as seguintes políticas de senha são configuradas por padrão:
* Comprimento mínimo da senha (caracteres): 7
* Duração máxima da senha (tempo de vida): 90 dias
* As senhas devem atender aos requisitos de complexidade

### <a name="account-lockout-settings"></a>Configurações de bloqueio de conta
Em um domínio gerenciado, as seguintes políticas de bloqueio de conta são configuradas por padrão:
* Duração do bloqueio de conta: 30
* Número de tentativas de logon com falha permitidas: 5
* Falha ao redefinir a contagem de tentativas de logon após: 30 minutos

Efetivamente, as contas de usuário são bloqueadas por 30 minutos se cinco senhas inválidas forem usadas em 2 minutos. As contas são desbloqueadas automaticamente após 30 minutos.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Criar uma FGPP (política de senha refinada personalizada) em um domínio gerenciado
Você pode criar um FGPP personalizado e aplicá-lo a grupos específicos em seu domínio gerenciado. Essa configuração substitui efetivamente o FGPP padrão configurado para o domínio gerenciado.

> [!TIP]
> Somente os membros do grupo **' Administradores de controlador de domínio do AAD '** têm as permissões para criar políticas de senha refinadas personalizadas.
>
>

Além disso, você também pode criar políticas de senha refinadas personalizadas e aplicá-las a quaisquer UOs personalizadas criadas no domínio gerenciado.

Você pode configurar um FGPP personalizado pelos seguintes motivos:
* Para definir uma política de bloqueio de conta diferente.
* Para definir uma configuração de tempo de vida de senha padrão para o domínio gerenciado.

Para criar um FGPP personalizado em seu domínio gerenciado:
1. Entre na VM do Windows que você usa para administrar seu domínio gerenciado (deve ser no mínimo o Windows Server 2012 R2). Se você não tiver uma, siga as instruções para [gerenciar um domínio de Azure AD Domain Services](manage-domain.md).
2. Inicie o **centro administrativo do Active Directory** na VM.
3. Clique no nome de domínio (por exemplo, ' contoso100.com ').
4. Clique duas vezes em **sistema** para abrir o contêiner do sistema.
5. Clique duas vezes em **contêiner de configuração de senha**.
6. Você verá o FGPP interno padrão para o domínio gerenciado chamado **AADDSSTFPSO**. Você não pode modificar este FGPP interno. No entanto, você pode criar um novo FGPP personalizado para substituir o FGPP padrão.
7. No painel **tarefas** à direita, clique em **novo** e clique em **configurações de senha**.
8. Na caixa de diálogo **criar configurações de senha** , especifique as configurações de senha personalizadas a serem aplicadas como parte do FGPP personalizado. Lembre-se de definir a precedência adequadamente para substituir o FGPP padrão.

   ![Criar FGPP personalizado](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Lembre-se de desmarcar a opção proteger contra exclusão acidental.** Se essa opção estiver selecionada, o FGPP não poderá ser salvo.
   >
   >

9. Em **aplica-se diretamente a**, clique no botão **Adicionar** . Na caixa de diálogo **Selecionar usuários ou grupos** , clique no botão **locais** .

   ![Selecione utilizadores e grupos](./media/how-to/fgpp-applies-to.png)

10. Na caixa de diálogo **locais** , expanda o nome de domínio e clique em **AADDC usuários**. Agora você pode selecionar um grupo na UO usuários internos, para a qual aplicará o FGPP.

    ![Selecione a UO à qual o grupo pertence](./media/how-to/fgpp-container.png)

11. Digite o nome do grupo e clique no botão **verificar nomes** para validar se o grupo existe.

    ![Selecione o grupo para aplicar FGPP](./media/how-to/fgpp-apply-group.png)

12. O nome do grupo é exibido na seção **aplica-se diretamente a** . Clique no botão **OK** para salvar essas alterações.

    ![FGPP aplicado](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Para aplicar políticas de senha personalizadas para contas de usuário em uma UO personalizada:** Políticas de senha refinadas podem ser aplicadas somente a grupos. Para configurar uma política de senha personalizada somente para usuários de uma UO personalizada, crie um grupo que inclua usuários nessa UO.
>
>

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre as políticas de senha refinadas Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurar políticas de senha refinadas usando o centro de administração do AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
