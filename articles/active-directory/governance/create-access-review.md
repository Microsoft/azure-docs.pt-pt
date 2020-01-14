---
title: Criar uma revisão de acesso de grupos & aplicativos-Azure AD
description: Saiba como criar uma revisão de acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e65eb08873da71c7683fe3347484831dfff58793
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932628"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicativos nas revisões de acesso do Azure AD

O acesso a grupos e aplicativos para funcionários e convidados muda ao longo do tempo. Para reduzir o risco associado às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar revisões de acesso para membros do grupo ou acesso ao aplicativo. Se você precisar examinar o acesso rotineiramente, também poderá criar revisões de acesso recorrentes. Para obter mais informações sobre esses cenários, consulte [gerenciar o acesso do usuário](manage-user-access-with-access-reviews.md) e gerenciar o acesso de [convidado](manage-guest-access-with-access-reviews.md).

Este artigo descreve como criar uma ou mais revisões de acesso para membros do grupo ou acesso ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de usuários

Para obter mais informações, consulte [requisitos de licença](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais revisões de acesso

1. Entre no portal do Azure e abra a [página governança de identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu à esquerda, clique em **revisões de acesso**.

1. Clique em **nova revisão de acesso** para criar uma nova revisão de acesso.

    ![Painel de revisões de acesso no controle de identidade](./media/create-access-review/access-reviews.png)

1. Nomeie a revisão de acesso. Opcionalmente, dê uma descrição à revisão. O nome e a descrição são mostrados para os revisores.

    ![Criar uma revisão de acesso-nome e descrição da revisão](./media/create-access-review/name-description.png)

1. Defina a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para que uma revisão de acesso seja iniciada no futuro e o último, no entanto, vários dias desejados.

    ![Criar uma revisão de acesso-datas de início e término](./media/create-access-review/start-end-dates.png)

1. Para fazer com que a revisão de acesso seja recorrente, altere a configuração de **frequência** de **uma vez** para **semanal**, **mensal**, **trimestral** ou **anual**. Use o controle deslizante **duração** ou a caixa de texto para definir o número de dias que cada revisão da série recorrente será aberta para entrada de revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Use a configuração **end** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou depois que um número definido de ocorrências tiver sido concluído. Você, outro administrador de usuário ou outro administrador global pode interromper a série após a criação alterando a data em **configurações**, de modo que ela termine nessa data.

1. Na seção **usuários** , especifique os usuários aos quais a revisão de acesso se aplica. As revisões de acesso podem ser para os membros de um grupo ou para usuários que foram atribuídos a um aplicativo. Além disso, você pode fazer o escopo da revisão de acesso para examinar somente os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de revisar todos os usuários que são membros ou que têm acesso ao aplicativo.

    ![Criar uma revisão de acesso-usuários](./media/create-access-review/users.png)

1. Na seção **grupo** , selecione um ou mais grupos dos quais você gostaria de examinar a associação.

    > [!NOTE]
    > A seleção de mais de um grupo criará várias revisões de acesso. Por exemplo, a seleção de cinco grupos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar grupo](./media/create-access-review/select-group.png)

1. Na seção **aplicativos** (se você selecionou **atribuído a um aplicativo** na etapa 8), selecione os aplicativos para os quais deseja revisar o acesso.

    > [!NOTE]
    > A seleção de mais de um aplicativo criará várias revisões de acesso. Por exemplo, a seleção de cinco aplicativos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar aplicativo](./media/create-access-review/select-application.png)

1. Na seção **revisores** , selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode optar por fazer com que os membros revisem seu próprio acesso. Se o recurso for um grupo, você poderá solicitar que os proprietários do grupo sejam revisados. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

    ![Criar uma revisão de acesso-revisores](./media/create-access-review/reviewers.png)

1. Na seção **programas** , selecione o programa que você deseja usar. O **programa padrão** está sempre presente.

    ![Criar uma revisão de acesso-programas](./media/create-access-review/programs.png)

    Você pode simplificar a forma de controlar e coletar revisões de acesso para diferentes finalidades, organizando-as em programas. Cada revisão de acesso pode ser vinculada a um programa. Em seguida, ao preparar relatórios para um auditor, você pode se concentrar nas revisões de acesso no escopo de uma iniciativa específica. Os resultados de análise de programas e acesso são visíveis para os usuários no administrador global, administrador de usuários, administrador de segurança ou função de leitor de segurança.

    Para ver uma lista de programas, vá para a página revisões de acesso e selecione **programas**. Se você estiver em uma função de administrador global ou de administrador de usuários, poderá criar programas adicionais. Por exemplo, você pode optar por ter um programa para cada iniciativa de conformidade ou meta de negócios. Se você não precisar mais de um programa e ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

### <a name="upon-completion-settings"></a>Após as configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a seção **sobre as configurações de conclusão** .

    ![Criar uma revisão de acesso-após as configurações de conclusão](./media/create-access-review/upon-completion-settings.png)

1. Se você quiser remover automaticamente o acesso para usuários que foram negados, defina **os resultados de aplicação automática para recurso** a ser **habilitado**. Se você quiser aplicar manualmente os resultados quando a revisão for concluída, defina a opção como **desabilitar**.

1. Use a lista se o **revisor não responder** para especificar o que acontece para os usuários que não são revisados pelo revisor no período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar, o acesso do usuário será removido.

    - **Sem alteração** – deixar acesso do usuário inalterado
    - **Remover acesso** -remover acesso do usuário
    - **Aprovar acesso** -aprovar acesso do usuário
    - **Faça recomendações** – tome a recomendação do sistema ao negar ou aprovar o acesso contínuo do usuário

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar configurações adicionais, expanda a seção **Configurações avançadas** .

    ![Criar uma revisão de acesso – configurações avançadas](./media/create-access-review/advanced-settings.png)

1. Defina **Mostrar recomendações** para **permitir** que o mostre aos revisores as recomendações do sistema com base nas informações de acesso do usuário.

1. Defina o **motivo da aprovação** para **habilitar** o para exigir que o revisor forneça um motivo para aprovação.

1. Defina **notificações de email** para **habilitar** o para que o Azure ad envie notificações por email aos revisores quando uma revisão de acesso for iniciada e aos administradores quando uma análise for concluída.

1. Defina **lembretes** para **permitir** que o Azure ad envie lembretes de revisões de acesso em andamento para revisores que não concluíram a revisão.

    Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.

## <a name="start-the-access-review"></a>Iniciar a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso e seu status](./media/create-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da revisão. Se você optar por não fazer o Azure AD enviar o email, certifique-se de informar aos revisores que uma revisão de acesso está aguardando para que eles sejam concluídos. Você pode mostrar a eles as instruções de como [revisar o acesso a grupos ou aplicativos](perform-access-review.md). Se sua análise for para convidados para revisar seu próprio acesso, mostre-lhes as instruções de como [examinar o acesso a grupos ou aplicativos](review-your-access.md).

Se você tiver atribuído convidados como revisores e eles não tiverem aceitado o convite, eles não receberão um email das revisões de acesso, pois eles devem primeiro aceitar o convite antes da revisão.

## <a name="create-reviews-via-apis"></a>Criar análises por meio de APIs

Você também pode criar revisões de acesso usando APIs. O que você faz para gerenciar revisões de acesso de grupos e usuários de aplicativos no portal do Azure também pode ser feito usando APIs Microsoft Graph. Para obter mais informações, consulte a [referência da API de revisões de acesso do Azure ad](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter um exemplo de código, consulte o [exemplo de recuperação de revisões de acesso do Azure ad via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passos seguintes

- [Examinar o acesso a grupos ou aplicativos](perform-access-review.md)
- [Examinar o acesso a grupos ou aplicativos](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
