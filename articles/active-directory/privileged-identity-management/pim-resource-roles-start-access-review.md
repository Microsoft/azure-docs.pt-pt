---
title: Crie uma revisão de acesso das funções de recursos Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções de recursos Azure em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 110a94c78427087f4ca5555f59055ab8e3bebcee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592666"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções de recursos da Azure na Gestão de Identidade Privilegiada

A necessidade de acesso a funções privilegiadas de recursos Azure por parte dos colaboradores muda ao longo do tempo. Para reduzir o risco associado a atribuições de funções velhas, deve rever regularmente o acesso. Você pode usar O Diretório Ativo Azure (Azure AD) Gestão de Identidade Privilegiada (PIM) para criar avaliações de acesso para acesso privilegiado a funções de recurso Azure. Também pode configurar comentários de acesso recorrentes que ocorrem automaticamente. Este artigo descreve como criar uma ou mais avaliações de acesso.

## <a name="prerequisite-role"></a>Papel pré-requisito

 Para criar revisões de acesso, tem de ser atribuído à função [Azure do Administrador de](../../role-based-access-control/built-in-roles.md#owner) Acesso ao Utilizador ou ao Proprietário ou ao Administrador de Acesso ao [Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) para o recurso.

## <a name="open-access-reviews"></a>Comentários de acesso aberto

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que está atribuído a uma das funções pré-requisitos.

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. No menu esquerdo, selecione **recursos Azure**.

1. Selecione o recurso que pretende gerir, como uma subscrição.

1. Em Gestão, selecione **avaliações de Acesso**.

    ![Recursos Azure - Lista de comentários de acesso mostrando o estado de todas as avaliações](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. Clique **em Novo** para criar uma nova revisão de acesso.

1. Diga o nome da revisão de acesso. Opcionalmente, dê à revisão uma descrição. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - Rever nome e descrição](./media/pim-resource-roles-start-access-review/name-description.png)

1. Definir a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora que é criada, e termina em um mês. Pode alterar as datas de início e fim para ter um início de revisão de acesso no futuro e durar quantos dias quiser.

    ![Data de início, frequência, duração, fim, número de vezes e data de fim](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a definição de **Frequência** de **uma vez** para **semanalmente,** **mensal,** **trimestral,** **anualmente** ou **semi-anual.** Utilize o slider de **duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para a entrada dos revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é de 27 dias, para evitar comentários sobrepostos.

1. Utilize a definição **'Fim'** para especificar como terminar as séries de revisão de acesso recorrentes. A série pode terminar de três maneiras: funciona continuamente para iniciar revisões indefinidamente, até uma data específica, ou depois de um número definido de ocorrências ter sido concluída. Você, outro administrador do Utilizador ou outro administrador Global pode parar a série após a criação alterando a data em **Definições**, de modo que termine nessa data.

1. Na secção **Utilizadores,** selecione uma ou mais funções que pretende rever a adesão.

    ![Margem de utilizadores para rever a adesão à função](./media/pim-resource-roles-start-access-review/users.png)

    > [!NOTE]
    > - As funções selecionadas aqui incluem [papéis permanentes e elegíveis.](../privileged-identity-management/pim-how-to-add-role-to-user.md)
    > - Selecionar mais de uma função criará várias avaliações de acesso. Por exemplo, selecionar cinco funções criará cinco avaliações de acesso separadas.
    Se estiver a criar uma revisão de acesso das **funções AD do Azure,** o seguinte mostra um exemplo da lista de membros da Revisão.

    ![Rever o painel de adesão listando as funções Azure AD que pode selecionar](./media/pim-resource-roles-start-access-review/review-membership.png)

    Se estiver a criar uma revisão de acesso das funções de **recursos do Azure,** a imagem a seguir mostra um exemplo da lista de membros da Revisão.

    ![Rever painel de adesão listando funções de recursos Azure que você pode selecionar](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. Na secção **de Revisores,** selecione uma ou mais pessoas para rever todos os utilizadores. Ou pode selecionar para que os membros revejam o seu próprio acesso.

    ![Lista de revisores de utilizadores ou membros selecionados (auto)](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **Utilizadores selecionados** - Utilize esta opção quando não souber quem precisa de acesso. Com esta opção, pode atribuir o comentário a um proprietário de recursos ou gestor de grupo para completar.
    - **Membros (self)** - Utilize esta opção para que os utilizadores revejam as suas próprias atribuições de funções. 
    - **Gestor** – Utilize esta opção para que o gestor do utilizador reveja a sua atribuição de funções. Ao selecionar Manager, também terá a opção de especificar um revisor de recuo. Os revisores de recuo são convidados a rever um utilizador quando o utilizador não tem nenhum gestor especificado no diretório. 

### <a name="upon-completion-settings"></a>Após definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a secção **de definições de conclusão após conclusão.**

    ![Após definições de conclusão para aplicar automaticamente e deve rever não responder](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. Se pretender remover automaticamente o acesso aos utilizadores que foram negados, desaprote **de Resultados de Aplicação Automática para obter o recurso** para **ativar**. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desave o interruptor para **Desativar**.

1. Utilize a lista **De revisores de casos não responda** para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que tenham sido revistos manualmente pelos revisores. Se a decisão do revisor final for Deny, então o acesso do utilizador será removido.

    - **Nenhuma alteração** - Deixe o acesso do utilizador inalterado
    - **Remover acesso** - Remover o acesso do utilizador
    - **Aprovar acesso** - Aprovar o acesso do utilizador
    - **Tome recomendações** - Tome a recomendação do sistema sobre a negação ou aprovação do acesso continuado do utilizador

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar definições adicionais, expanda a secção **de definições Avançadas.**

    ![Definições avançadas para recomendações de exibição, requerem razão sobre aprovação, notificações de correio e lembretes](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. Definir **recomendações** para **Permitir** mostrar aos revisores as recomendações do sistema baseadas nas informações de acesso do utilizador.

1. **Desemba a razão do requerer a aprovação** para **permitir** que o revisor forneça uma razão para aprovação.

1. Desconfiem **notificações de Correio** eletrónico para **permitir** que o Azure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar e aos administradores quando uma revisão estiver concluída.

1. Definir **Lembretes** para **Permitir** que a Azure AD envie lembretes de avaliações de acesso em curso a revisores que não tenham concluído a sua revisão.
1. O conteúdo do e-mail enviado aos revisores é autogerido com base nos detalhes da revisão, tais como nome de revisão, nome de recurso, data de vencimento, etc. Se precisar de uma forma de comunicar informações adicionais, tais como instruções adicionais ou informações de contacto, pode especificar esses dados no **conteúdo adicional para o e-mail do revisor** que será incluído nos e-mails de convite e lembrete enviados aos revisores designados. A secção realçada abaixo é onde esta informação será exibida.

    ![Conteúdo do e-mail enviado aos revisores com destaques](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Uma vez especificadas as definições para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso aparecerá na sua lista com um indicador do seu estado.

![Lista de comentários de acesso mostrando o estado da revisão iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Por padrão, a Azure AD envia um e-mail aos revisores logo após o início da revisão. Se optar por não ter a Azure AD a enviar o e-mail, certifique-se de informar os revisores de que uma revisão de acesso está à espera que eles estejam concluídos. Pode mostrar-lhes as instruções de como rever o [acesso às funções de recurso Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerir a revisão de acesso

Pode acompanhar o progresso à medida que os revisores completam as suas avaliações na página **geral** da revisão de acesso. Não são alterados os direitos de acesso no diretório até que a [revisão esteja concluída.](pim-resource-roles-complete-access-review.md)

![Aceder à página geral das avaliações mostrando os detalhes da análise](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se esta for uma revisão única, então após o fim do período de revisão de acesso ou o administrador parar a revisão de acesso, siga os passos em [Concluir uma revisão de acesso das funções de recursos Azure](pim-resource-roles-complete-access-review.md) para ver e aplicar os resultados.  

Para gerir uma série de avaliações de acesso, navegue para a revisão de acesso e encontrará as próximas ocorrências em avaliações agendadas e editará a data final ou adicione/remova os revisores em conformidade.

Com base nas suas seleções nas **definições de conclusão,** a inscrição automática será executada após a data de fim da revisão ou quando parar manualmente a revisão. O estado da revisão passará de **Concluído** através de estados intermédios como **a Aplicação** e, finalmente, para o Estado **Aplicado.** Deverá esperar ver utilizadores negados, caso existam, a serem removidos das funções em poucos minutos.

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso às funções de recursos do Azure](pim-resource-roles-perform-access-review.md)
- [Complete uma revisão de acesso das funções de recursos da Azure](pim-resource-roles-complete-access-review.md)
- [Crie uma revisão de acesso dos papéis da AD Azure](pim-how-to-start-security-review.md)
