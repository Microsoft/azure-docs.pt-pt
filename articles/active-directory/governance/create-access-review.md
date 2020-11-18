---
title: Criar uma revisão de acesso de grupos & aplicações - Azure AD
description: Saiba como criar uma revisão de acesso aos membros do grupo ou acesso a aplicações em avaliações de acesso ao Azure Ative.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18f0627b809f56b813052cc763e6ff961f31aa02
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697140"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicações em avaliações de acesso Azure AD

O acesso a grupos e aplicações para funcionários e hóspedes muda ao longo do tempo. Para reduzir o risco associado a atribuições de acessos à falência, os administradores podem utilizar o Azure Ative Directory (Azure AD) para criar revisões de acesso para membros do grupo ou acesso a aplicações. Se precisar de rever rotineiramente o acesso, também pode criar avaliações de acesso recorrentes. Para obter mais informações sobre estes cenários, consulte [Gerir o acesso ao utilizador](manage-user-access-with-access-reviews.md) e Gerir o acesso ao [hóspede.](manage-guest-access-with-access-reviews.md)

Pode ver um vídeo rápido a falar sobre como ativar as avaliações de acesso:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Este artigo descreve como criar uma ou mais avaliações de acesso para membros do grupo ou acesso a aplicações.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de utilizadores
- (Pré-visualização) Os proprietários de recursos dos Grupos Microsoft 365 podem criar avaliações nos grupos Microsoft 365 que possuem
- (Pré-visualização) Os proprietários de recursos dos Grupos de Segurança Azure AD podem criar revisões sobre os grupos de segurança AD Azure que possuem

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais avaliações de acesso

1. Inscreva-se no portal Azure e abra a [página de Governação da Identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. No menu esquerdo, clique nas **avaliações de Acesso**.

1. Clique em **Nova análise de acesso** para criar uma nova revisão de acesso.

    ![Acesso análises painel na Governação de Identidade](./media/create-access-review/access-reviews.png)

1. Diga o nome da revisão de acesso. Opcionalmente, dê à revisão uma descrição. O nome e a descrição são mostrados aos revisores.

    ![Criar uma revisão de acesso - Rever nome e descrição](./media/create-access-review/name-description.png)

1. Definir a **data de início**. Por padrão, uma revisão de acesso ocorre uma vez, começa na mesma hora que é criada, e termina em um mês. Pode alterar as datas de início e fim para ter um início de revisão de acesso no futuro e durar quantos dias quiser.

    ![Criar uma revisão de acesso - Iniciar e terminar datas](./media/create-access-review/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a definição de **Frequência** de uma vez para **semanalmente,** **mensal,** **trimestral,** **Weekly** **semi-anual** ou **anualmente.** Utilize o slider de **duração** ou caixa de texto para definir quantos dias cada revisão da série recorrente estará aberta para a entrada dos revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é de 27 dias, para evitar comentários sobrepostos.

1. Utilize a definição **'Fim'** para especificar como terminar as séries de revisão de acesso recorrentes. A série pode terminar de três maneiras: 
    1. Funciona continuamente para iniciar revisões indefinidamente
    1. Até uma data específica,
    1. Até que um número definido de ocorrências tenha terminado. 
  
    Você, outro administrador do Utilizador ou outro administrador Global pode parar a série após a criação alterando a data em **Definições**, de modo que termine nessa data.

1. Na secção **Utilizadores,** especifique os utilizadores a que a revisão de acesso se aplica. As avaliações de acesso podem ser para os membros de um grupo ou para utilizadores que foram designados para uma aplicação. Pode ainda analisar a revisão de acesso para rever apenas os utilizadores convidados que são membros (ou atribuídos à aplicação), em vez de rever todos os utilizadores que são membros ou que têm acesso à aplicação.

    ![Criar uma análise de acesso - Utilizadores](./media/create-access-review/users.png)

1. Na secção **Grupo,** selecione um ou mais grupos que pretende rever a adesão.

    > [!NOTE]
    > Selecionar mais de um grupo criará várias avaliações de acesso. Por exemplo, selecionar cinco grupos criará cinco avaliações de acesso separadas.
    
    ![Criar uma revisão de acesso - Selecione grupo](./media/create-access-review/select-group.png)

1. Na secção **Aplicações** (se selecionou **Atribuído a uma aplicação** no passo 8), selecione as aplicações a que pretende rever o acesso.

    > [!NOTE]
    > A seleção de mais de uma aplicação criará várias avaliações de acesso. Por exemplo, a seleção de cinco aplicações criará cinco avaliações de acesso separadas.
    
    ![Criar uma revisão de acesso - Selecione a aplicação](./media/create-access-review/select-application.png)

1. Na secção **de Revisores,** selecione uma ou mais pessoas para rever todos os utilizadores no âmbito. Ou pode selecionar para que os membros revejam o seu próprio acesso. Se o recurso for um grupo, pode pedir aos proprietários do grupo que revejam. Também pode exigir que os revisores forneçam uma razão quando aprovam o acesso.

    ![Criar uma análise de acesso - Revisores](./media/create-access-review/reviewers.png)

1. Na secção **Programas,** selecione o programa que pretende utilizar. **O Programa Predefinido** está sempre presente.

    ![Criar uma revisão de acesso - Programas](./media/create-access-review/programs.png)

    Pode simplificar a recolha e rastreio de avaliações de acesso organizando-as em programas. Cada revisão de acesso pode ser ligada a um programa. Depois, quando se preparam relatórios para um auditor, podem concentrar-se nas análises de acesso no âmbito de uma determinada iniciativa. Os resultados dos programas e da revisão de acesso são visíveis para os utilizadores na função de administrador global, administrador de utilizador, administrador de segurança ou leitor de segurança.

    Para ver uma lista de programas, vá à página de comentários de acesso e selecione **Programas**. Se estiver numa função de administrador global ou administrador de utilizador, pode criar programas adicionais. Por exemplo, pode optar por ter um programa para cada iniciativa de conformidade ou objetivo de negócio. Quando já não precisa de um programa e não tiver controlos ligados a ele, pode eliminá-lo.

### <a name="upon-completion-settings"></a>Após definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda a secção **de definições de conclusão após conclusão.**

    ![Criar uma revisão de acesso - Após definições de conclusão](./media/create-access-review/upon-completion-settings-new.png)

2. Se pretender remover automaticamente o acesso a utilizadores negados, desaprote **de Resultados para o Recurso** **ativar**. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desave o interruptor para **Desativar**.

3. Utilize a lista **Se os revisores não responderem** à lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que tenham sido revistos manualmente pelos revisores. Se a decisão do revisor final for Deny, então o acesso do utilizador será removido.

    - **Nenhuma alteração** - Deixe o acesso do utilizador inalterado
    - **Remover acesso** - Remover o acesso do utilizador
    - **Aprovar acesso** - Aprovar o acesso do utilizador
    - **Tome recomendações** - Tome a recomendação do sistema sobre a negação ou aprovação do acesso continuado do utilizador

    ![Criar uma revisão de acesso - Configurações avançadas](./media/create-access-review/advanced-settings-preview-new.png)

4. (Pré-visualização) Utilize a Ação para aplicar aos utilizadores negados para especificar o que acontece aos utilizadores convidados se forem negados.
    - **A opção 1** removerá o acesso negado do utilizador ao grupo ou à aplicação que está a ser revista, ainda assim poderá iniciar sedus ao arrendatário. 
    - **A opção 2** impedirá os utilizadores recusados de se inscreverem no arrendatário, independentemente de terem acesso a outros recursos. Se houve um erro ou se um administrador decidir voltar a ativar o acesso de alguém, podem fazê-lo no prazo de 30 dias após o utilizador ter sido desativado. Se não houver medidas contra os utilizadores deficientes, serão eliminadas do arrendatário.

Para saber mais sobre as melhores práticas para remover utilizadores convidados que já não têm acesso a recursos na sua organização, leia o artigo intitulado [Use Azure AD Identity Governance para rever e remover utilizadores externos que já não têm acesso](access-reviews-external-users.md)a recursos.

>[!NOTE]
> A ação a aplicar aos utilizadores recusados só funciona se previamente analisar apenas um reexame aos utilizadores convidados (Ver **Criar uma ou mais avaliações de acessos** passo 8)

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar definições adicionais, expanda a secção **de definições Avançadas.**

1. Definir **recomendações** para **Permitir** mostrar aos revisores as recomendações do sistema baseadas nas informações de acesso do utilizador.

1. **Desemba a razão do requerer a aprovação** para **permitir** que o revisor forneça uma razão para aprovação.

1. Desconfiem **notificações de Correio** eletrónico para **permitir** que o Azure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar e aos administradores quando uma revisão estiver concluída.

1. Definir **Lembretes** para **Permitir** que a Azure AD envie lembretes de avaliações de acesso em curso a revisores que não tenham concluído a sua revisão. 

    >[!NOTE]
    > Por padrão, a Azure AD envia automaticamente um lembrete a meio caminho da data de fim para os revisores que ainda não responderam

1. (Pré-visualização) O conteúdo do e-mail enviado aos revisores é autogerido com base nos detalhes da revisão, tais como nome de revisão, nome de recurso, data de vencimento, etc. Se precisar de uma forma de comunicar informações adicionais, tais como instruções adicionais ou informações de contacto, pode especificar esses dados no **conteúdo adicional para o e-mail do revisor** que será incluído nos e-mails de convite e lembrete enviados aos revisores designados. A secção realçada abaixo é onde esta informação será exibida.

    ![Rever o acesso dos utilizadores a um grupo](./media/create-access-review/review-users-access-group.png)

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Uma vez especificadas as definições para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso aparecerá na sua lista com um indicador do seu estado.

![Lista de avaliações de acesso e seu estado](./media/create-access-review/access-reviews-list.png)

Por padrão, a Azure AD envia um e-mail aos revisores logo após o início da revisão. Se optar por não ter a Azure AD a enviar o e-mail, certifique-se de informar os revisores de que uma revisão de acesso está à espera que eles estejam concluídos. Pode mostrar-lhes as instruções de como rever o [acesso a grupos ou aplicações](perform-access-review.md). Se a sua avaliação for para que os hóspedes revejam o seu próprio acesso, mostre-lhes as instruções para como rever o [acesso a grupos ou aplicações.](review-your-access.md)

Se você atribuiu os hóspedes como revisores e eles não aceitaram o convite, eles não receberão um e-mail de comentários de acesso porque eles devem primeiro aceitar o convite antes de rever.

## <a name="access-review-status-table"></a>Tabela de estado de revisão de acesso

| Estado | Definição |
|--------|------------|
|Não Começou | A análise foi criada, a descoberta do utilizador está à espera de começar. |
|A inicializar   | A descoberta do utilizador está em andamento para identificar todos os utilizadores que fazem parte da revisão. |
|A iniciar | A revisão está a começar. Se as notificações por e-mail estiverem ativadas, os e-mails serão enviados para os revisores. |
|InProgress | A revisão já começou. Se as notificações por e-mail forem ativadas, os e-mails foram enviados para os revisores. Os revisores podem submeter decisões até à data de vencimento. |
|Completar | A revisão está a ser concluída e os e-mails estão a ser enviados para o proprietário da revisão. |
|Revisão automática | A revisão está numa fase de revisão do sistema. O sistema está a registar decisões para utilizadores que não foram revistos com base em recomendações ou decisões pré-configuradas. |
|Autorrevisto | As decisões foram registadas pelo sistema para todos os utilizadores que não foram revistos. A revisão está pronta para proceder à **Aplicação** se o Auto-Apply estiver ativado. |
|Aplicação | Não haverá alteração de acesso para utilizadores que tenham sido aprovados. |
|Aplicado | Os utilizadores negados, caso existam, foram removidos do recurso ou diretório. |
|Com falhas | A revisão não pôde progredir. Este erro pode estar relacionado com a supressão do arrendatário, uma alteração nas licenças ou outras alterações internas do arrendatário. |

## <a name="create-reviews-via-apis"></a>Criar comentários via APIs

Também pode criar comentários de acesso utilizando APIs. O que faz para gerir as análises de acesso de grupos e utilizadores de aplicações no portal Azure também pode ser feito usando APIs do Microsoft Graph. Para mais informações, consulte a referência API de acesso a [Azure AD](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter uma amostra de código, consulte [Exemplo de recuperação de comentários de acesso a AD do Azure através do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passos seguintes

- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Reveja o acesso a grupos ou aplicações](review-your-access.md)
- [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md)