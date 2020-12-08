---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: active-directory
author: barclayn
ms.service: active-directory
ms.topic: include
ms.date: 12/07/2020
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: cbcd4b459faa3bf67f591cc7afab0bf0027062e1
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842324"
---
## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais avaliações de acesso

1. Clique **em Novo** para criar uma nova revisão de acesso.

1. Diga o nome da revisão de acesso. Opcionalmente, dê à revisão uma descrição. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - Rever nome e descrição](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Definir a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora que é criada, e termina em um mês. Pode alterar as datas de início e fim para ter um início de revisão de acesso no futuro e durar quantos dias quiser.

    ![Data de início, frequência, duração, fim, número de vezes e data de fim](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a definição de **Frequência** de **uma vez** para **semanalmente,** **mensal,** **trimestral,** **anualmente** ou **semi-anual.** Utilize o slider de **duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para a entrada dos revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é de 27 dias, para evitar comentários sobrepostos.

1. Utilize a definição **'Fim'** para especificar como terminar as séries de revisão de acesso recorrentes. A série pode terminar de três maneiras: funciona continuamente para iniciar revisões indefinidamente, até uma data específica, ou depois de um número definido de ocorrências ter sido concluída. Você, outro administrador do Utilizador ou outro administrador Global pode parar a série após a criação alterando a data em **Definições**, de modo que termine nessa data.

1. Na secção **Utilizadores,** selecione uma ou mais funções que pretende rever a adesão.

    ![Margem de utilizadores para rever a adesão à função](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - As funções selecionadas aqui incluem [papéis permanentes e elegíveis.](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)
    > - Selecionar mais de uma função criará várias avaliações de acesso. Por exemplo, selecionar cinco funções criará cinco avaliações de acesso separadas.

    Se estiver a criar uma revisão de acesso das **funções AD do Azure,** o seguinte mostra um exemplo da lista de membros da Revisão.

    ![Rever o painel de adesão listando as funções Azure AD que pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Se estiver a criar uma revisão de acesso das funções de **recursos do Azure,** a imagem a seguir mostra um exemplo da lista de membros da Revisão.

    ![Rever painel de adesão listando funções de recursos Azure que você pode selecionar](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Na secção **de Revisores,** selecione uma ou mais pessoas para rever todos os utilizadores. Ou pode selecionar para que os membros revejam o seu próprio acesso.

    ![Lista de revisores de utilizadores ou membros selecionados (auto)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Utilizadores selecionados** - Utilize esta opção quando não souber quem precisa de acesso. Com esta opção, pode atribuir o comentário a um proprietário de recursos ou gestor de grupo para completar.
    - **Membros (self)** - Utilize esta opção para que os utilizadores revejam as suas próprias atribuições de funções.
    - **(Preview) Manager** – Utilize esta opção para que o gestor do utilizador reveja a sua atribuição de funções. Ao selecionar (Preview) Manager, também terá a opção de especificar um revisor de recuo. Os revisores de recuo são convidados a rever um utilizador quando o utilizador não tem nenhum gestor especificado no diretório.

### <a name="upon-completion-settings"></a>Após definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a secção **de definições de conclusão após conclusão.**

    ![Após definições de conclusão para aplicar automaticamente e deve rever não responder](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Se pretender remover automaticamente o acesso aos utilizadores que foram negados, desaprote **de Resultados de Aplicação Automática para obter o recurso** para **ativar**. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desave o interruptor para **Desativar**.

1. Utilize a lista **De revisores de casos não responda** para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que tenham sido revistos manualmente pelos revisores. Se a decisão do revisor final for Deny, então o acesso do utilizador será removido.

    - **Nenhuma alteração** - Deixe o acesso do utilizador inalterado
    - **Remover acesso** - Remover o acesso do utilizador
    - **Aprovar acesso** - Aprovar o acesso do utilizador
    - **Tome recomendações** - Tome a recomendação do sistema sobre a negação ou aprovação do acesso continuado do utilizador

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar definições adicionais, expanda a secção **de definições Avançadas.**

    ![Definições avançadas para recomendações de exibição, requerem razão sobre aprovação, notificações de correio e lembretes](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Definir **recomendações** para **Permitir** mostrar aos revisores as recomendações do sistema baseadas nas informações de acesso do utilizador.

1. **Desemba a razão do requerer a aprovação** para **permitir** que o revisor forneça uma razão para aprovação.

1. Desconfiem **notificações de Correio** eletrónico para **permitir** que o Azure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar e aos administradores quando uma revisão estiver concluída.

1. Definir **Lembretes** para **Permitir** que a Azure AD envie lembretes de avaliações de acesso em curso a revisores que não tenham concluído a sua revisão.
1. O conteúdo do e-mail enviado aos revisores é autogerido com base nos detalhes da revisão, tais como nome de revisão, nome de recurso, data de vencimento, etc. Se precisar de uma forma de comunicar informações adicionais, tais como instruções adicionais ou informações de contacto, pode especificar esses dados no **conteúdo adicional para o e-mail do revisor** que será incluído nos e-mails de convite e lembrete enviados aos revisores designados. A secção realçada abaixo é onde esta informação será exibida.

    ![Conteúdo do e-mail enviado aos revisores com destaques](./media/active-directory-privileged-identity-management-access-reviews/email-info.png)