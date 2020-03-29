---
title: Atribuir licenças a um grupo - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Como atribuir licenças aos utilizadores através do licenciamento do grupo Azure Ative Directory
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
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253068"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Atribuir licenças aos utilizadores por associação de grupo seleções em Diretório Ativo Azure

Este artigo acompanha-o através da atribuição de licenças de produtos a um grupo de utilizadores e verificando se estão licenciados corretamente no Azure Ative Directory (Azure AD).

Neste exemplo, o inquilino contém um grupo de segurança chamado **Departamento de RH.** Este grupo inclui todos os membros do departamento de recursos humanos (cerca de 1.000 utilizadores). Quer atribuir licenças do Office 365 Enterprise E3 a todo o departamento. O serviço Yammer Enterprise que está incluído no produto deve ser temporariamente desativado até que o departamento esteja pronto para começar a usá-lo. Também pretende implementar licenças de Mobilidade Empresarial + Segurança para o mesmo grupo de utilizadores.

> [!NOTE]
> Alguns serviços Microsoft não estão disponíveis em todas as localizações. Antes de uma licença poder ser atribuída a um utilizador, o administrador tem de especificar a propriedade de localização de utilização no utilizador.
>
> Para a atribuição de licença de grupo, quaisquer utilizadores sem localização de utilização especificada herdam a localização do diretório. Se tiver utilizadores em vários locais, recomendamos que detete sempre a localização de utilização como parte do fluxo de criação do utilizador em Azure AD (por exemplo, através da configuração AAD Connect) - que garante que o resultado da atribuição da licença está sempre correto e os utilizadores não recebem serviços em locais que não são permitidos.

## <a name="step-1-assign-the-required-licenses"></a>Passo 1: Atribuir as licenças necessárias

1. Inscreva-se no [**centro de administração da Azure AD**](https://aad.portal.azure.com) com uma conta de administrador de licença. Para gerir licenças, a conta deve ser um administrador de licença, administrador de utilizador ou administrador global.

1. Selecione **Licenças** para abrir uma página onde pode ver e gerir todos os produtos licenciáveis no inquilino.

1. Em **todos os produtos,** selecione tanto o Office 365 Enterprise E5 como o Enterprise Mobility + Security E3 selecionando os nomes do produto. Para iniciar a atribuição, **selecione Atribuir** no topo da página.

   ![Selecione produtos para atribuir licenças](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Na página de **licença de Atribuir,** selecione **Utilizadores e grupos** para abrir uma lista de utilizadores e grupos.

1. Selecione um utilizador ou grupo e, em seguida, use o botão **Select** na parte inferior da página para confirmar a sua seleção.

1. Na página de **licença de Atribuição,** clique em **opções de Atribuição,** que exibe todos os planos de serviço incluídos nos dois produtos que selecionamos anteriormente. Encontre a **Yammer Enterprise** e **desligue-o** para desativar esse serviço da licença do produto. Confirme **clicando** ok na parte inferior das **opções**de Licença .

   ![selecionar planos de serviço para licenças](./media/licensing-groups-assign/assignment-options.png)
  
1. Para completar a atribuição, na página de **licença de Atribuição,** clique em **Atribuir** na parte inferior da página.

1. É apresentada uma notificação no canto superior direito que mostra o estado e o resultado do processo. Se a atribuição ao grupo não puder ser concluída (por exemplo, devido às licenças pré-existentes no grupo), clique na notificação para visualizar detalhes da falha.

Ao atribuir licenças a um grupo, a Azure AD processa todos os membros existentes desse grupo. Este processo pode demorar algum tempo, variando com o tamanho do grupo. O próximo passo descreve como verificar se o processo terminou e determinar se é necessária mais atenção para resolver problemas.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Passo 2: Verificar se a atribuição inicial terminou

1. Vá a **Azure Ative Directory** > **Groups**. Selecione o grupo a que as licenças foram atribuídas.

1. Na página do grupo, selecione **Licenças**. Isto permite-lhe confirmar rapidamente se as licenças foram totalmente atribuídas aos utilizadores e se existem erros que você precisa investigar. As seguintes informações estão disponíveis:

   - Licenças de serviço que estão atualmente atribuídas ao grupo. Selecione uma entrada para mostrar os serviços específicos que foram ativados e para fazer alterações.

   - Atualizações de estado das últimas alterações de licença, que estão disponíveis se as alterações estiverem a ser processadas ou se o processamento tiver terminado para todos os membros do utilizador.

   - Informações sobre atribuições de licença de utilizador que se encontram em estado de erro.

   ![erros de licenciamento e estatuto de licença](./media/licensing-groups-assign/assignment-errors.png)

1. Consulte informações mais detalhadas sobre o processamento de licenças no âmbito dos utilizadores de **diretórios** > ativos do Azure e dos**grupos** > de > nome de*grupoRegistos*de**auditoria**. Consulte as seguintes atividades:

   - Atividade: `Start applying group based license to users`. Isto é registado quando o sistema apanha a alteração da atribuição de licenças no grupo e começa a aplicá-la a todos os utilizadores. Contém informações sobre a mudança que foi feita.

   - Atividade: `Finish applying group based license to users`. Isto é registado quando o sistema termina o processamento de todos os utilizadores do grupo. Contém um resumo de quantos utilizadores foram processados com sucesso e quantos utilizadores não podiam ser atribuídos licenças de grupo.

   [Leia esta secção](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para saber mais sobre como os registos de auditoria podem ser usados para analisar as alterações efetuadas pelo licenciamento baseado em grupo.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Passo 3: Verifique se há problemas de licença e resolva-os

1. Vá ao **Azure Ative Directory** > **Groups**, e encontre o grupo a que as licenças foram atribuídas.
1. Na página do grupo, selecione **Licenças**. A notificação no topo da página mostra que há 10 utilizadores a quem as licenças não puderam ser atribuídas. Abra-a para ver uma lista de todos os utilizadores num estado de erro de licenciamento para este grupo.
1. A coluna de **atribuições falhadas** diz-nos que ambas as licenças de produto não podiam ser atribuídas aos utilizadores. A **principal razão para a coluna de falha** contém a causa da falha. Neste caso, são **planos de serviço contraditórios.**

   ![licenças que não podiam ser atribuídas](./media/licensing-groups-assign/failed-assignments.png)

1. Selecione um utilizador para abrir a página **licenças** do utilizador. Esta página mostra todas as licenças que estão atualmente atribuídas ao utilizador. Neste exemplo, o utilizador tem a licença Office 365 Enterprise E1 que foi herdada do grupo de utilizadores do **Quiosque.** Isto entra em conflito com a licença E3 que o sistema tentou aplicar do grupo do Departamento de **RH.** Como resultado, nenhuma das licenças desse grupo foi atribuída ao utilizador.

   ![Ver todos os conflitos de licença para um utilizador](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Para resolver este conflito, remova o utilizador do grupo de utilizadores do **Quiosque.** Após a AD Azure processar a mudança, as licenças **do Departamento** de RH são corretamente atribuídas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o conjunto de funcionalidades para atribuição de licençautilizando grupos, consulte os seguintes artigos:

- [O que é o licenciamento baseado em grupo no Azure Ative Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
- [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
- [Como migrar os utilizadores entre licenças de produtos utilizando licenciamento baseado em grupo no Diretório Ativo azure](licensing-groups-change-licenses.md)
- [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [Exemplos da PowerShell para licenciamento baseado em grupo no Diretório Ativo Azure](licensing-ps-examples.md)
