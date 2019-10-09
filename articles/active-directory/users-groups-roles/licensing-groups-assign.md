---
title: Atribuir licenças a um grupo-Azure Active Directory | Microsoft Docs
description: Como atribuir licenças a usuários por meio de Azure Active Directory licenciamento de grupo
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497efda857dcd7de3079d702be00a094d221b779
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034808"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Atribuir licenças a usuários por associação de grupo no Azure Active Directory

Este artigo explica como atribuir licenças de produto a um grupo de usuários e verificar se eles estão licenciados corretamente no Azure Active Directory (AD do Azure).

Neste exemplo, o locatário contém um grupo de segurança chamado **departamento de RH**. Esse grupo inclui todos os membros do departamento de recursos humanos (cerca de 1.000 usuários). Você deseja atribuir licenças do Office 365 Enterprise E3 a todo o departamento. O serviço Yammer Enterprise que está incluído no produto deve ser temporariamente desabilitado até que o departamento esteja pronto para começar a usá-lo. Você também deseja implantar licenças Enterprise Mobility + Security no mesmo grupo de usuários.

> [!NOTE]
> Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade local de uso no usuário.
>
> Para a atribuição de licença de grupo, todos os usuários sem um local de uso especificado herdam o local do diretório. Se você tiver usuários em vários locais, recomendamos que sempre defina o local de uso como parte do fluxo de criação do usuário no Azure AD (por exemplo, por meio da configuração do AAD Connect) – que garante que o resultado da atribuição de licença esteja sempre correto e os usuários não recebam serviços em locais que não são permitidos.

## <a name="step-1-assign-the-required-licenses"></a>Passo 1: Atribuir as licenças necessárias

1. Entre no centro de [**Administração do Azure ad**](https://aad.portal.azure.com) com uma conta de administrador de licenças. Para gerenciar licenças, a conta deve ser um administrador de licenças, administrador de usuários ou administrador global.

1. Selecione **licenças** para abrir uma página onde você pode ver e gerenciar todos os produtos licenciados no locatário.

1. Em **todos os produtos**, selecione o Office 365 Enterprise E5 e Enterprise Mobility + Security E3 selecionando os nomes dos produtos. Para iniciar a atribuição, selecione **atribuir** na parte superior da página.

   ![Selecionar produtos para atribuir licenças](./media/licensing-groups-assign/all-products-assign.png)
  
1. Na página **atribuir licença** , selecione **usuários e grupos** para abrir uma lista de usuários e grupos.

1. Selecione um usuário ou grupo e, em seguida, use o botão **selecionar** na parte inferior da página para confirmar sua seleção.

1. Na página **atribuir licença** , clique em **Opções de atribuição**, que exibe todos os planos de serviço incluídos nos dois produtos que selecionamos anteriormente. Localize o **Yammer Enterprise** e desative **-o** para desabilitar o serviço da licença do produto. Confirme clicando em **OK** na parte inferior das **Opções de licença**.

   ![selecionar planos de serviço para licenças](./media/licensing-groups-assign/assignment-options.png)
  
1. Para concluir a atribuição, na página **atribuir licença** , clique em **atribuir** na parte inferior da página.

1. Uma notificação é exibida no canto superior direito que mostra o status e o resultado do processo. Se a atribuição ao grupo não pôde ser concluída (por exemplo, devido a licenças já existentes no grupo), clique na notificação para exibir os detalhes da falha.

Ao atribuir licenças a um grupo, o Azure AD processa todos os membros existentes desse grupo. Esse processo pode levar algum tempo, variando com o tamanho do grupo. A próxima etapa descreve como verificar se o processo foi concluído e determinar se é necessária mais atenção para resolver problemas.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Passo 2: Verificar se a atribuição inicial foi concluída

1. Vá para **Azure Active Directory** > **grupos**. Selecione o grupo ao qual as licenças foram atribuídas.

1. Na página grupo, selecione **licenças**. Isso permite que você confirme rapidamente se as licenças foram totalmente atribuídas aos usuários e se há erros que você precisa examinar. As informações seguintes estão disponíveis:

   - Licenças de serviço que estão atribuídas ao grupo no momento. Selecione uma entrada para mostrar os serviços específicos que foram habilitados e para fazer alterações.

   - Atualizações de status das últimas alterações de licença, que estarão disponíveis se as alterações estiverem sendo processadas ou se o processamento tiver sido concluído para todos os membros do usuário.

   - Informações sobre atribuições de licença de usuário que estão em estado de erro.

   ![erros de licenciamento e status da licença](./media/licensing-groups-assign/assignment-errors.png)

1. Consulte informações mais detalhadas sobre o processamento de licenças em **Azure Active Directory** > **usuários e grupos** > *nome do grupo* >  logs de**auditoria**. Verifique as seguintes atividades:

   - Atividade: `Start applying group based license to users`. Isso é registrado quando o sistema pega a alteração da atribuição de licença no grupo e começa a aplicá-la a todos os membros do usuário. Ele contém informações sobre a alteração feita.

   - Atividade: `Finish applying group based license to users`. Isso é registrado quando o sistema conclui o processamento de todos os usuários no grupo. Ele contém um resumo de quantos usuários foram processados com êxito e de quantos usuários não podiam receber licenças de grupo.

   [Leia esta seção](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para saber mais sobre como os logs de auditoria podem ser usados para analisar as alterações feitas pelo licenciamento baseado em grupo.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Passo 3: Verificar se há problemas de licença e resolvê-los

1. Vá para **Azure Active Directory** **grupos**de  >  e localize o grupo ao qual as licenças foram atribuídas.
1. Na página grupo, selecione **licenças**. A notificação na parte superior da página mostra que há 10 usuários para os quais não foi possível atribuir licenças. Abra-o para ver uma lista de todos os usuários em um estado de erro de licenciamento para esse grupo.
1. A coluna **atribuições com falha** informa que as duas licenças de produto não puderam ser atribuídas aos usuários. A coluna **principal motivo da falha** contém a causa da falha. Nesse caso, eles são **planos de serviço conflitantes**.

   ![licenças que não puderam ser atribuídas](./media/licensing-groups-assign/failed-assignments.png)

1. Selecione um usuário para abrir a página **licenças** do usuário. Esta página mostra todas as licenças atribuídas ao usuário no momento. Neste exemplo, o usuário tem a licença do Office 365 Enterprise E1 que foi herdada do grupo **usuários de quiosque** . Isso entra em conflito com a licença E3 que o sistema tentou aplicar do grupo de **departamento de RH** . Como resultado, nenhuma das licenças desse grupo foi atribuída ao usuário.

   ![Exibir todos os conflitos de licença de um usuário](./media/licensing-groups-assign/user-license-view.png)

1. Para resolver esse conflito, remova o usuário do grupo **usuários de quiosque** . Depois que o Azure AD processa a alteração, as licenças do **departamento de RH** são atribuídas corretamente.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o conjunto de recursos para atribuição de licença usando grupos, consulte os seguintes artigos:

- [O que é o licenciamento baseado em grupo no Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
- [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](licensing-ps-examples.md)
