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
ms.date: 02/06/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb2198ea3f01e923022c205e478167240a01894
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084443"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicativos nas revisões de acesso do Azure AD

O acesso a grupos e aplicativos para funcionários e convidados muda ao longo do tempo. Para reduzir o risco associado às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar revisões de acesso para membros do grupo ou acesso ao aplicativo. Se você precisar examinar o acesso rotineiramente, também poderá criar revisões de acesso recorrentes. Para obter mais informações sobre estes cenários, consulte [Gerir o acesso ao utilizador](manage-user-access-with-access-reviews.md) e gerir o acesso ao [hóspede](manage-guest-access-with-access-reviews.md).

Este artigo descreve como criar uma ou mais revisões de acesso para membros do grupo ou acesso ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de usuários

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais revisões de acesso

1. Inscreva-se no portal Azure e abra a [página de Governança de Identidade.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

1. No menu esquerdo, clique em comentários de **acesso.**

1. Clique em **Nova revisão de acesso** para criar uma nova revisão de acesso.

    ![Painel de revisões de acesso no controle de identidade](./media/create-access-review/access-reviews.png)

1. Nomeie a revisão de acesso. Opcionalmente, dê uma descrição à revisão. O nome e a descrição são mostrados para os revisores.

    ![Criar uma revisão de acesso-nome e descrição da revisão](./media/create-access-review/name-description.png)

1. Definir a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora em que é criada e termina em um mês. Você pode alterar as datas de início e de término para que uma revisão de acesso seja iniciada no futuro e o último, no entanto, vários dias desejados.

    ![Criar uma revisão de acesso-datas de início e término](./media/create-access-review/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a definição de **Frequência** de **Uma vez** para **Semanal**, **Mensal,** **Trimestral** ou **Anual .** Utilize o slider **de duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para entrada dos revisores. Por exemplo, a duração máxima que você pode definir para uma revisão mensal é de 27 dias, para evitar revisões sobrepostas.

1. Utilize a definição **'Fim'** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: ela é executada continuamente para iniciar revisões indefinidamente, até uma data específica ou depois que um número definido de ocorrências tiver sido concluído. Você, outro administrador de Utilizador, ou outro administrador global pode parar a série após a criação alterando a data em **Definições**, para que termine nessa data.

1. Na secção **Utilizadores,** especifique os utilizadores a que a revisão de acesso se aplica. As revisões de acesso podem ser para os membros de um grupo ou para usuários que foram atribuídos a um aplicativo. Além disso, você pode fazer o escopo da revisão de acesso para examinar somente os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de revisar todos os usuários que são membros ou que têm acesso ao aplicativo.

    ![Criar uma revisão de acesso-usuários](./media/create-access-review/users.png)

1. Na secção **Grupo,** selecione um ou mais grupos que deseja rever a adesão.

    > [!NOTE]
    > A seleção de mais de um grupo criará várias revisões de acesso. Por exemplo, a seleção de cinco grupos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar grupo](./media/create-access-review/select-group.png)

1. Na secção **Aplicações** (se selecionou **O Que foi atribuído a uma aplicação** no passo 8), selecione as aplicações a que gostaria de rever o acesso.

    > [!NOTE]
    > A seleção de mais de um aplicativo criará várias revisões de acesso. Por exemplo, a seleção de cinco aplicativos criará cinco revisões de acesso separadas.
    
    ![Criar uma revisão de acesso-selecionar aplicativo](./media/create-access-review/select-application.png)

1. Na secção **Revisores,** selecione uma ou mais pessoas para rever todos os utilizadores no âmbito. Ou você pode optar por fazer com que os membros revisem seu próprio acesso. Se o recurso for um grupo, você poderá solicitar que os proprietários do grupo sejam revisados. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

    ![Criar uma revisão de acesso-revisores](./media/create-access-review/reviewers.png)

1. Na secção **Programas,** selecione o programa que pretende utilizar. **O Programa Predefinido** está sempre presente.

    ![Criar uma revisão de acesso-programas](./media/create-access-review/programs.png)

    Você pode simplificar a forma de controlar e coletar revisões de acesso para diferentes finalidades, organizando-as em programas. Cada revisão de acesso pode ser vinculada a um programa. Em seguida, ao preparar relatórios para um auditor, você pode se concentrar nas revisões de acesso no escopo de uma iniciativa específica. Os resultados de análise de programas e acesso são visíveis para os usuários no administrador global, administrador de usuários, administrador de segurança ou função de leitor de segurança.

    Para ver uma lista de programas, aceda à página de comentários de acesso e selecione **Programas**. Se você estiver em uma função de administrador global ou de administrador de usuários, poderá criar programas adicionais. Por exemplo, você pode optar por ter um programa para cada iniciativa de conformidade ou meta de negócios. Se você não precisar mais de um programa e ele não tiver nenhum controle vinculado a ele, você poderá excluí-lo.

### <a name="upon-completion-settings"></a>Após as configurações de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a secção de **definições após a conclusão.**

    ![Criar uma revisão de acesso-após as configurações de conclusão](./media/create-access-review/upon-completion-settings.png)

1. Se pretender remover automaticamente, aceda aos utilizadores que foram negados, defina **os resultados de aplicação automática ao recurso** para **Enable**. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desloque o interruptor para **Desativar**.

1. Utilize o **revisor Caso não responda** à lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Essa configuração não afeta os usuários que foram revisados pelos revisores manualmente. Se a decisão do revisor final for negar, o acesso do usuário será removido.

    - **Sem alterações** - Deixe o acesso do utilizador inalterado
    - **Remover o acesso** - Remover o acesso do utilizador
    - **Aprovar acesso** - Aprovar o acesso do utilizador
    - **Aceite recomendações** - Aceite a recomendação do sistema sobre negar ou aprovar o acesso continuado do utilizador

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar configurações adicionais, expanda a secção de **definições Avançadas.**

    ![Criar uma revisão de acesso – configurações avançadas](./media/create-access-review/advanced-settings.png)

1. Definir **recomendações** para **permitir** mostrar aos revisores as recomendações do sistema basearam as informações de acesso do utilizador.

1. O conjunto **Requer uma razão na aprovação** para **permitir** que o revisor forneça uma razão de aprovação.

1. Detete **as notificações do Mail** para **permitir** que o Azure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar, e aos administradores quando uma revisão estiver concluída.

1. Delineie **lembretes** para **permitir** que o Azure AD envie lembretes das avaliações de acesso em curso aos revisores que não tenham concluído a sua revisão.

    Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.

## <a name="start-the-access-review"></a>Iniciar a revisão de acesso

Depois de especificar as definições para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso e seu status](./media/create-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da revisão. Se você optar por não fazer o Azure AD enviar o email, certifique-se de informar aos revisores que uma revisão de acesso está aguardando para que eles sejam concluídos. Pode mostrar-lhes as instruções para rever o [acesso a grupos ou aplicações](perform-access-review.md). Se a sua análise for para que os hóspedes revejam o seu próprio acesso, mostre-lhes as instruções para como rever o [acesso a grupos ou aplicações](review-your-access.md).

Se você tiver atribuído convidados como revisores e eles não tiverem aceitado o convite, eles não receberão um email das revisões de acesso, pois eles devem primeiro aceitar o convite antes da revisão.

## <a name="access-review-status-table"></a>Tabela de estado de revisão de acesso

| Estado | Definição |
|--------|------------|
|NotStarted | A revisão foi criada, a descoberta do utilizador está à espera de começar. |
|Inicialização   | A descoberta do utilizador está em andamento para identificar todos os utilizadores que fazem parte da revisão. |
|A iniciar | A revisão está a começar. Se as notificações de e-mail estiverem ativadas, os e-mails serão enviados para os revisores. |
|Em curso | A revisão já começou. Se as notificações de e-mail estiverem ativadas, os e-mails foram enviados aos revisores. Os revisores podem submeter decisões até à data de vencimento. |
|Conclusão | A revisão está a ser concluída e os e-mails estão a ser enviados para o dono da revisão. |
|Revisão automática | A revisão está numa fase de revisão do sistema. O sistema está a registar decisões para utilizadores que não foram revistos com base em recomendações ou decisões pré-configuradas. |
|Autorevisto | As decisões foram registadas pelo sistema para todos os utilizadores que não foram revistos. A revisão está pronta para proceder à aplicação se estiver ativada a **aplicação** automática. |
|Aplicação | Não haverá alteração no acesso aos utilizadores que foram aprovados. |
|Aplicado | Os utilizadores negados, caso o tenham, foram removidos do recurso ou do diretório. |

## <a name="create-reviews-via-apis"></a>Criar análises por meio de APIs

Você também pode criar revisões de acesso usando APIs. O que você faz para gerenciar revisões de acesso de grupos e usuários de aplicativos no portal do Azure também pode ser feito usando APIs Microsoft Graph. Para mais informações, consulte o [acesso da AD Azure à referência da API](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter uma amostra de código, consulte [Exemplo de recuperação de avaliações de acesso a Anúncio saqueadas através do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Reveja o acesso a grupos ou aplicações](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
