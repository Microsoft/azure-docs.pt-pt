---
title: Criar uma revisão de acesso de grupos & aplicações - Azure AD
description: Saiba como criar uma revisão de acesso aos membros do grupo ou ao acesso a aplicações nas avaliações de acesso ao Diretório Ativo do Azure.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/08/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e01951b6147cfc39fe6c46035db822071bda3aa
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984080"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicações em avaliações de acesso a AD Azure

O acesso a grupos e aplicações para funcionários e hóspedes muda ao longo do tempo. Para reduzir o risco associado a atribuições de acesso stale, os administradores podem utilizar o Azure Ative Directory (Azure AD) para criar avaliações de acesso para membros do grupo ou acesso a aplicações. Se precisar de rever rotineiramente o acesso, também pode criar avaliações de acesso recorrentes. Para obter mais informações sobre estes cenários, consulte [Gerir o acesso ao utilizador](manage-user-access-with-access-reviews.md) e gerir o acesso ao [hóspede](manage-guest-access-with-access-reviews.md).

Este artigo descreve como criar uma ou mais avaliações de acesso para membros do grupo ou acesso a aplicações.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de utilizador

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais avaliações de acesso

1. Inscreva-se no portal Azure e abra a [página de Governança de Identidade.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

1. No menu esquerdo, clique em comentários de **acesso.**

1. Clique em **Nova revisão de acesso** para criar uma nova revisão de acesso.

    ![Painel de avaliações de acesso na Governança da Identidade](./media/create-access-review/access-reviews.png)

1. Diga o nome da revisão de acesso. Opcionalmente, dê uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - Rever o nome e a descrição](./media/create-access-review/name-description.png)

1. Definir a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora que é criada, e termina em um mês. Pode alterar as datas de início e fim para ter uma revisão de acesso no futuro e durar quantos dias quiser.

    ![Criar uma revisão de acesso - Datas de início e fim](./media/create-access-review/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a definição de **Frequência** de **Uma vez** para **Semanal**, **Mensal,** **Trimestral,** **Semi-anualou**ou **Anualmente.** Utilize o slider **de duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para entrada dos revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é de 27 dias, para evitar comentários sobrepostos.

1. Utilize a definição **'Fim'** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: funciona continuamente para iniciar as revisões indefinidamente, até que uma data específica, ou após um número definido de ocorrências tenha sido concluída. Você, outro administrador de Utilizador, ou outro administrador global pode parar a série após a criação alterando a data em **Definições**, para que termine nessa data.

1. Na secção **Utilizadores,** especifique os utilizadores a que a revisão de acesso se aplica. As avaliações de acesso podem ser para os membros de um grupo ou para utilizadores que foram atribuídos a uma aplicação. Pode ainda analisar a revisão de acesso para rever apenas os utilizadores convidados que são membros (ou atribuídos à aplicação), em vez de rever todos os utilizadores que são membros ou que tenham acesso à aplicação.

    ![Criar uma revisão de acesso - Utilizadores](./media/create-access-review/users.png)

1. Na secção **Grupo,** selecione um ou mais grupos que deseja rever a adesão.

    > [!NOTE]
    > A seleção de mais de um grupo criará múltiplas avaliações de acesso. Por exemplo, a seleção de cinco grupos criará cinco avaliações de acesso separadas.
    
    ![Criar uma revisão de acesso - Selecione grupo](./media/create-access-review/select-group.png)

1. Na secção **Aplicações** (se selecionou **O Que foi atribuído a uma aplicação** no passo 8), selecione as aplicações a que gostaria de rever o acesso.

    > [!NOTE]
    > A seleção de mais de uma aplicação criará múltiplas avaliações de acesso. Por exemplo, a seleção de cinco aplicações criará cinco avaliações de acesso separadas.
    
    ![Criar uma revisão de acesso - Selecione aplicação](./media/create-access-review/select-application.png)

1. Na secção **Revisores,** selecione uma ou mais pessoas para rever todos os utilizadores no âmbito. Ou pode selecionar para que os membros revejam o seu próprio acesso. Se o recurso for um grupo, pode pedir aos proprietários do grupo que revejam. Também pode exigir que os revisores forneçam uma razão para aprovaro acesso.

    ![Criar uma revisão de acesso - Revisores](./media/create-access-review/reviewers.png)

1. Na secção **Programas,** selecione o programa que pretende utilizar. **O Programa Predefinido** está sempre presente.

    ![Criar uma revisão de acesso - Programas](./media/create-access-review/programs.png)

    Pode simplificar como rastrear e recolher avaliações de acesso para diferentes fins, organizando-as em programas. Cada revisão de acesso pode ser ligada a um programa. Depois, quando preparar relatórios para um auditor, pode focar-se nas avaliações de acesso no âmbito de uma determinada iniciativa. Os programas e os resultados da revisão de acesso são visíveis para os utilizadores no administrador global, administrador de utilizador, administrador de segurança ou papel de leitor de segurança.

    Para ver uma lista de programas, aceda à página de comentários de acesso e selecione **Programas**. Se estiver numa função de administrador global ou de administrador de utilizador, pode criar programas adicionais. Por exemplo, pode optar por ter um programa para cada iniciativa de conformidade ou objetivo de negócio. Se já não precisar de um programa e não tiver quaisquer controlos ligados ao mesmo, pode eliminá-lo.

### <a name="upon-completion-settings"></a>Após definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a secção de **definições após a conclusão.**

    ![Criar uma revisão de acesso - Após definições de conclusão](./media/create-access-review/upon-completion-settings.png)

1. Se pretender remover automaticamente, aceda aos utilizadores que foram negados, defina **os resultados de aplicação automática ao recurso** para **Enable**. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desloque o interruptor para **Desativar**.

1. Utilize o **revisor Caso não responda** à lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que tenham sido revistos manualmente pelos revisores. Se a decisão do revisor final for Deny, o acesso do utilizador será removido.

    - **Sem alterações** - Deixe o acesso do utilizador inalterado
    - **Remover o acesso** - Remover o acesso do utilizador
    - **Aprovar acesso** - Aprovar o acesso do utilizador
    - **Aceite recomendações** - Aceite a recomendação do sistema sobre negar ou aprovar o acesso continuado do utilizador

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar configurações adicionais, expanda a secção de **definições Avançadas.**

    ![Criar uma revisão de acesso - Configurações avançadas](./media/create-access-review/advanced-settings.png)

1. Definir **recomendações** para **permitir** mostrar aos revisores as recomendações do sistema basearam as informações de acesso do utilizador.

1. O conjunto **Requer uma razão na aprovação** para **permitir** que o revisor forneça uma razão de aprovação.

1. Detete **as notificações do Mail** para **permitir** que o Azure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar, e aos administradores quando uma revisão estiver concluída.

1. Delineie **lembretes** para **permitir** que o Azure AD envie lembretes das avaliações de acesso em curso aos revisores que não tenham concluído a sua revisão.

    Por predefinição, o Azure AD envia automaticamente um lembrete aos revisores que ainda não responderam a meio do prazo para a data de fim.

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as definições para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso aparecerá na sua lista com um indicador do seu estado.

![Lista de avaliações de acesso e seu estado](./media/create-access-review/access-reviews-list.png)

Por padrão, a Azure AD envia um e-mail aos revisores pouco depois do início da revisão. Se optar por não enviar o e-mail do Azure AD, certifique-se de informar os revisores de que uma revisão de acesso está à espera que sejam concluídas. Pode mostrar-lhes as instruções para rever o [acesso a grupos ou aplicações](perform-access-review.md). Se a sua análise for para que os hóspedes revejam o seu próprio acesso, mostre-lhes as instruções para como rever o [acesso a grupos ou aplicações](review-your-access.md).

Se você atribuiu os hóspedes como revisores e eles não aceitaram o convite, eles não receberão um e-mail de comentários de acesso porque eles devem primeiro aceitar o convite antes de rever.

## <a name="access-review-status-table"></a>Tabela de estado de revisão de acesso

| Estado | Definição |
|--------|------------|
|Não Iniciado | A revisão foi criada, a descoberta do utilizador está à espera de começar. |
|A inicializar   | A descoberta do utilizador está em andamento para identificar todos os utilizadores que fazem parte da revisão. |
|A iniciar | A revisão está a começar. Se as notificações de e-mail estiverem ativadas, os e-mails serão enviados para os revisores. |
|Inprogress | A revisão já começou. Se as notificações de e-mail estiverem ativadas, os e-mails foram enviados aos revisores. Os revisores podem submeter decisões até à data de vencimento. |
|Conclusão | A revisão está a ser concluída e os e-mails estão a ser enviados para o dono da revisão. |
|Revisão automática | A revisão está numa fase de revisão do sistema. O sistema está a registar decisões para utilizadores que não foram revistos com base em recomendações ou decisões pré-configuradas. |
|Autorevisto | As decisões foram registadas pelo sistema para todos os utilizadores que não foram revistos. A revisão está pronta para proceder à aplicação se estiver ativada a **aplicação** automática. |
|Aplicação | Não haverá alteração no acesso aos utilizadores que foram aprovados. |
|Aplicado | Os utilizadores negados, caso o tenham, foram removidos do recurso ou do diretório. |

## <a name="create-reviews-via-apis"></a>Criar comentários via APIs

Também pode criar avaliações de acesso utilizando APIs. O que faz para gerir as análises de acesso de grupos e utilizadores de aplicações no portal Azure também pode ser feito usando APIs do Microsoft Graph. Para mais informações, consulte o [acesso da AD Azure à referência da API](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter uma amostra de código, consulte [Exemplo de recuperação de avaliações de acesso a Anúncio saqueadas através do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Reveja o acesso a grupos ou aplicações](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
