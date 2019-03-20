---
title: Atribuir licenças a um grupo - Azure Active Directory | Documentos da Microsoft
description: Como atribuir licenças aos utilizadores por meio do licenciamento de grupo do Azure Active Directory
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a54d1ad3ab809f2a2f8df6ae0e30b1b061c2be1
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201091"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Atribuir licenças aos utilizadores pela associação de grupo no Azure Active Directory

Este artigo o orienta através da atribuição de licenças de produto a um grupo de utilizadores no Azure Active Directory (Azure AD) e, em seguida, verificar que eles estão corretamente licenciados.

Neste exemplo, o inquilino contém um grupo de segurança chamado **departamento de RH**. Esse grupo inclui todos os membros do departamento de recursos humanos (aproximadamente 1.000 usuários). Pretende atribuir licenças do Office 365 Enterprise E3 ao departamento de todo. O serviço de Yammer empresarial que está incluído no produto tem de ser temporariamente desativado até que o departamento está pronto para começar a utilizar. Também queira implementar licenças Enterprise Mobility + Security para o mesmo grupo de utilizadores.

> [!NOTE]
> Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar a propriedade de localização de utilização no utilizador.
> 
> Para a atribuição de licença de grupo, todos os utilizadores sem uma localização de utilização definida herdam a localização do diretório. Se tiver usuários em vários locais, recomendamos que defina a localização de utilização sempre como parte do seu fluxo de criação do utilizador no Azure AD (por exemplo, através do AAD Connect configuração) – que garante o resultado da atribuição de licenças que sempre está correto e se os utilizadores não recebem serviços em locais que não são permitidas.

## <a name="step-1-assign-the-required-licenses"></a>Passo 1: Atribuir as licenças necessárias

1. Inicie sessão para o [ **Centro de administração do Azure AD** ](https://aad.portal.azure.com) com uma conta de administrador de licença. Para gerir licenças, a conta tem de ser um administrador de licenças, o administrador de utilizadores ou o administrador global.

2. Selecione **licenças** para abrir um painel onde pode ver e gerir todos os produtos sujeito a licença no inquilino.

4. Sob **todos os produtos**, selecione Office 365 Enterprise E5 e Enterprise Mobility + Security E3, selecionando os nomes de produtos. Para começar a atribuição, selecione **atribuir** na parte superior do painel.

   ![Selecione os produtos para atribuir licenças](./media/licensing-groups-assign/all-products-assign.png)
  
5. Sobre o **atribuir licenças** painel, selecione **utilizadores e grupos** para abrir uma lista de utilizadores e grupos.

6. Selecione um utilizador ou grupo e, em seguida, utilize o **selecione** na parte inferior do painel para confirmar a sua seleção.

7. Sobre o **atribuir licenças** painel, clique em **opções de atribuição**, que apresenta todos os planos de serviço incluídos nos dois produtos que selecionamos anteriormente. Encontrar **Yammer Enterprise** e ativá-la **desativar** para desativar o serviço de licença de produto. Confirme clicando **OK** na parte inferior **opções de licença**.

   ![Selecione os planos de serviço de licenças](./media/licensing-groups-assign/assignment-options.png)
  
8. Para concluir a atribuição, no painel **Atribuir licença**, clique em **Atribuir**, na parte inferior.

9. É apresentada uma notificação no canto superior direito que mostra o estado e o resultado do processo. Se não foi possível concluir a atribuição ao grupo (por exemplo, por causa de licenças pré-existentes no grupo), clique na notificação para ver os detalhes da falha.

Quando atribuir licenças a um grupo, do Azure AD processa todos os membros existentes desse grupo. Este processo poderá demorar algum tempo, variando o tamanho do grupo. O passo seguinte descreve como verificar se o processo foi concluída e determinar se as atenção adicional é necessário para resolver problemas.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Passo 2: Certifique-se de que a atribuição inicial foi concluída

1. Aceda a **do Azure Active Directory** > **grupos**. Selecione o grupo que licenças foram atribuídas a.

2. No painel de grupo, selecione **licenças**. Isto permite-lhe confirmar rapidamente se licenças foram atribuídas totalmente a utilizadores e se existem quaisquer erros que precisa examinar. As informações seguintes estão disponíveis:

   - Lista de licenças de produtos que estão atualmente atribuída ao grupo. Selecione uma entrada para mostrar os serviços específicos que foram ativados e fazer alterações.

   - Estado das mais recentes alterações de licença que foram feitas para o grupo (se as alterações estão a ser processadas ou se o processamento foi concluído para todos os membros de utilizador).

   - Informações sobre os utilizadores que estão em estado de erro porque não foi possível atribuir licenças às mesmas.

   ![erros de licenciamento e o estado de licença](./media/licensing-groups-assign/assignment-errors.png)

3. Ver informações mais detalhadas sobre a licença de processamento sob **do Azure Active Directory** > **utilizadores e grupos** > *nome do grupo*  >  **Registos de auditoria**. Tenha em atenção as seguintes atividades:

   - Atividade: **Começar a aplicar licença baseada em grupo aos utilizadores**. Isto é registado quando o sistema Deteta a alteração de atribuição de licenças do grupo e começa a aplicá-lo a todos os membros de utilizador. Ele contém informações sobre a alteração que foi efetuada.

   - Atividade: **Concluir a aplicação de licença baseada em grupo aos utilizadores**. Isto é registado quando o sistema termina o processamento de todos os utilizadores no grupo. Ele contém um resumo de quantos utilizadores foram processados com êxito e o número de utilizadores não foi possível atribuir licenças de grupo.

   [Leia esta secção](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para saber mais sobre como os registos de auditoria podem ser usados para analisar as alterações efetuadas pelo licenciamento baseado em grupo.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Passo 3: Verifique a existência de problemas de licença e resolvê-los

1. Aceda a **do Azure Active Directory** > **grupos**e encontrar o grupo que licenças foram atribuídas a.
2. No painel de grupo, selecione **licenças**. A notificação na parte superior do painel mostra que há 10 utilizadores licenças não foi possível atribuir a. Abra-o para ver uma lista de todos os utilizadores num Estado de erro de licenciamento para este grupo.
3. O **atribuições falhadas** coluna nos informa que ambas as licenças de produto não foi possível atribuir aos utilizadores. O **principais motivo da falha** coluna contém a causa da falha. Neste caso, ele possui **planos do serviço em conflito**.

   ![licenças que não foi possível atribuir](./media/licensing-groups-assign/failed-assignments.png)

4. Selecione um utilizador para abrir o **licenças** painel. Este painel mostra todas as licenças que estão atualmente atribuídas ao utilizador. Neste exemplo, o utilizador tem a licença do Office 365 Enterprise E1 que foi herdada a partir da **os utilizadores de local público** grupo. Entrar em conflito com a licença de E3 que o sistema estava a tentar aplicar a partir da **departamento de RH** grupo. Assim, nenhuma das licenças desse grupo foi atribuída ao utilizador.

   ![Ver todos os conflitos de licença para um utilizador](./media/licensing-groups-assign/user-license-view.png)

5. Para resolver este conflito, remova o utilizador a partir da **os utilizadores de local público** grupo. Depois do Azure AD processa a alteração, o **departamento de RH** corretamente as licenças são atribuídas.

   ![Aqui estão corretamente as licenças atribuídas](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o conjunto para gestão de licenças por meio dos grupos de recursos, veja os artigos seguintes:

* [O que é o licenciamento no Azure Active Directory com base em grupo?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](../active-directory-licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](licensing-ps-examples.md)
