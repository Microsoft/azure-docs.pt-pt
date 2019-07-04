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
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476260"
---
## <a name="create-one-or-more-access-reviews"></a>Criar um ou mais as revisões de acesso

1. Clique em **New** para criar uma nova revisão de acesso.

1. Nome da revisão de acesso. Opcionalmente, forneça a revisão de uma descrição. O nome e descrição são apresentadas aos revisores.

    ![Criar uma revisão de acesso - revisão de nome e descrição](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Definir o **data de início**. Por predefinição, uma revisão de acesso ocorre uma vez, é iniciada ao mesmo tempo, que é criado e termina dentro de um mês. Pode alterar o início e datas de término para ter um acesso rever início no futuro e pela última vez no entanto, o número de dias que pretende.

    ![Comece a data, a frequência, a duração, a fim, a número de vezes e data de fim](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a **frequência** definição **uma vez** para **semanal**, **mensal**,  **Trimestral**, **anualmente**, ou **semiestruturados annually**. Utilize o **duração** caixa de texto ou de controlo de deslize para definir o número de dias será cada revisão da série recorrente abrir para a entrada de revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é dias 27, para evitar a sobreposição de revisões.

1. Utilize o **final** definição para especificar como terminar o acesso recorrente rever série. A série pode ser encerrada de três formas: ela seja executada continuamente para iniciar as revisões indefinidamente, até uma data específica ou após um determinado número de ocorrências de conclusão. , Outro administrador de utilizador ou outro administrador Global pode parar a série após a criação, alterando a data no **definições**, para que ele termina a essa data.

1. Na **utilizadores** secção, selecione uma ou mais funções que pretende rever a associação de.

    ![Âmbito de utilizadores para rever a associação de função de](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Selecionar mais de uma função, irá criar várias revisões de acesso. Por exemplo, a seleção de cinco funções irá criar cinco revisões de acesso separado.

    Se estiver a criar uma revisão de acesso de funções do Azure AD, o seguinte mostra um exemplo da lista de membros de revisão.

    ![Painel de associação de revisão listagem funções do Azure AD, pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Se estiver a criar uma revisão de acesso das funções de recursos do Azure, o seguinte mostra um exemplo da lista de membros de revisão.

    ![Painel de associação de revisão listagem funções de recursos do Azure, pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Na **revisores** secção, selecione uma ou mais pessoas para rever todos os utilizadores. Ou pode selecionar para que os membros, reveja o seu próprio acesso.

    ![Lista de revisores de utilizadores selecionados ou membros (auto)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Utilizadores selecionados** -Utilize esta opção se não sabe quem precisa de acesso. Com esta opção, pode atribuir a revisão a um proprietário do recurso ou o Gestor de grupo para concluir.
    - **Membros (auto)** -Utilize esta opção para que os utilizadores, reveja as suas próprias atribuições de funções.

### <a name="upon-completion-settings"></a>Após as definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda o **nas definições de conclusão** secção.

    ![Após a conclusão aplicam definições automaticamente e deverá rever não responder](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Se quiser automaticamente remover acesso para utilizadores que foram negados, definida **automaticamente aplicar resultados ao recurso** ao **ativar**. Se deseja aplicar manualmente os resultados quando a revisão terminar, defina o comutador **desativar**.

1. Utilize o **deve o revisor não responder** lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que foram revistos, os revisores manualmente. Se a decisão do revisor final é negar, em seguida, o acesso do utilizador será removido.

    - **Nenhuma alteração** -deixe sem alterações de acesso de utilizador
    - **Remover o acesso** -remover o acesso do utilizador
    - **Aprovar o acesso** -aprovar o acesso do utilizador
    - **Efetuar recomendações** - tirar a recomendação do sistema ao negar ou aprovar o utilizador do acesso contínuo

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar definições adicionais, expanda o **definições avançadas** secção.

    ![Definições avançadas para mostrar recomendações, exigir razão na aprovação, notificações de correio e lembretes](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Definir **Mostrar recomendações** ao **ativar** para mostrar os revisores do sistema recomendações com base em informações de acesso do utilizador.

1. Definir **exigir razão na aprovação** ao **ativar** para solicite ao revisor que forneça um motivo para aprovação.

1. Definir **notificações de E-Mail** ao **ativar** para ter o Azure AD para enviar notificações por e-mail para os revisores quando é iniciado uma revisão de acesso e para os administradores quando uma revisão é concluída.

1. Definir **lembretes** ao **ativar** para que o Azure AD envie lembretes de revisões de acesso em curso para revisores que não concluíram as respetivas revisões.
