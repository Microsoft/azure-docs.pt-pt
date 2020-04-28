---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67476260"
---
## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais avaliações de acesso

1. Clique em **New** para criar uma nova revisão de acesso.

1. Diga o nome da revisão de acesso. Opcionalmente, dê uma descrição à revisão. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - Rever o nome e a descrição](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Definir a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora que é criada, e termina em um mês. Pode alterar as datas de início e fim para ter uma revisão de acesso no futuro e durar quantos dias quiser.

    ![Data de início, frequência, duração, fim, número de vezes e data de fim](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a definição de **Frequência** de **Uma vez** para **Semanal**, **Mensal,** **Trimestral,** **Anualou**ou **Semi-anualmente.** Utilize o slider **de duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para entrada dos revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é de 27 dias, para evitar comentários sobrepostos.

1. Utilize a definição **'Fim'** para especificar como terminar a série de revisão de acesso recorrente. A série pode terminar de três maneiras: funciona continuamente para iniciar as revisões indefinidamente, até que uma data específica, ou após um número definido de ocorrências tenha sido concluída. Você, outro administrador de Utilizador, ou outro administrador global pode parar a série após a criação alterando a data em **Definições**, para que termine nessa data.

1. Na secção **Utilizadores,** selecione uma ou mais funções que pretende rever a adesão.

    ![Âmbito dos utilizadores para rever a adesão à função](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > A seleção de mais de uma função criará múltiplas avaliações de acesso. Por exemplo, a seleção de cinco funções criará cinco avaliações de acesso separadas.

    Se está a criar uma revisão de acesso das funções da AD Azure, o seguinte mostra um exemplo da lista de membros da Review.

    ![Reveja o painel de membros listando funções azure AD que pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Se está a criar uma revisão de acesso das funções de recurso Azure, o seguinte mostra um exemplo da lista de membros da Review.

    ![Reveja o painel de membros listando funções de recursos do Azure que pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Na secção **Revisores,** selecione uma ou mais pessoas para rever todos os utilizadores. Ou pode selecionar para que os membros revejam o seu próprio acesso.

    ![Lista de revisores de utilizadores ou membros selecionados (self)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Utilizadores selecionados** - Utilize esta opção quando não sabe quem precisa de acesso. Com esta opção, pode atribuir a revisão a um proprietário de recursos ou gestor de grupo para completar.
    - **Membros (auto)** - Utilize esta opção para que os utilizadores revejam as suas próprias atribuições de funções.

### <a name="upon-completion-settings"></a>Após definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a secção de **definições após a conclusão.**

    ![Após a conclusão das definições para aplicar automaticamente e deve rever não responder](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Se pretender remover automaticamente o acesso aos utilizadores que foram negados, defina os **resultados de aplicação automática ao recurso** para **enable**. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desloque o interruptor para **Desativar**.

1. Utilize o **revisor Caso não responda** à lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que tenham sido revistos manualmente pelos revisores. Se a decisão do revisor final for Deny, o acesso do utilizador será removido.

    - **Sem alterações** - Deixe o acesso do utilizador inalterado
    - **Remover o acesso** - Remover o acesso do utilizador
    - **Aprovar acesso** - Aprovar o acesso do utilizador
    - **Aceite recomendações** - Aceite a recomendação do sistema sobre negar ou aprovar o acesso continuado do utilizador

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar configurações adicionais, expanda a secção de **definições Avançadas.**

    ![Configurações avançadas para recomendações de exibição, requerem razão na aprovação, notificações de correio e lembretes](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Definir **recomendações** para **permitir** mostrar aos revisores as recomendações do sistema basearam as informações de acesso do utilizador.

1. O conjunto **Requer uma razão na aprovação** para **permitir** que o revisor forneça uma razão de aprovação.

1. Detete **as notificações do Mail** para **permitir** que o Azure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar, e aos administradores quando uma revisão estiver concluída.

1. Delineie **lembretes** para **permitir** que o Azure AD envie lembretes das avaliações de acesso em curso aos revisores que não tenham concluído a sua revisão.
