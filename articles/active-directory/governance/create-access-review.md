---
title: Criar uma revisão de acesso de grupos & aplicações - Azure AD
description: Saiba como criar uma revisão de acesso aos membros do grupo ou acesso a aplicações em avaliações de acesso ao Azure Ative.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143c3f9786d41c32ae954ab219197a9cfaa1050
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176880"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos e aplicações em avaliações de acesso Azure AD

O acesso a grupos e aplicações para funcionários e hóspedes muda ao longo do tempo. Para reduzir o risco associado a atribuições de acessos à falência, os administradores podem utilizar o Azure Ative Directory (Azure AD) para criar revisões de acesso para membros do grupo ou acesso a aplicações. Se precisar de rever rotineiramente o acesso, também pode criar avaliações de acesso recorrentes. Para obter mais informações sobre estes cenários, consulte [Gerir o acesso ao utilizador](manage-user-access-with-access-reviews.md) e Gerir o acesso ao [hóspede.](manage-guest-access-with-access-reviews.md)

Pode ver um vídeo rápido a falar sobre como ativar as avaliações de acesso:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Este artigo descreve como criar uma ou mais avaliações de acesso para membros do grupo ou acesso a aplicações.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2
- Administrador global ou administrador de utilizadores

Para mais informações, consulte [os requisitos da Licença.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Criar uma ou mais avaliações de acesso

1. Inscreva-se no portal Azure e abra a [página de Governação da Identidade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. No menu esquerdo, clique nas **avaliações de Acesso**.

3. Clique em **Nova análise de acesso** para criar uma nova revisão de acesso.

    ![Acesso análises painel na Governação de Identidade](./media/create-access-review/access-reviews.png)

4. No **passo 1: Selecione o que rever** selecione qual o recurso que pretende rever.

    ![Criar uma revisão de acesso - Rever nome e descrição](./media/create-access-review/select-what-review.png)

5. Se selecionou **Equipas + Grupos** no Passo 1, tem duas opções no Passo 2
   - **Todos os grupos Microsoft 365 com utilizadores convidados.** Selecione esta opção se quiser criar avaliações recorrentes em todos os seus utilizadores convidados em todos os seus grupos Microsoft Teams e M365 na sua organização. Pode optar por excluir determinados grupos clicando em 'Select group(s) para excluir".
   - **Selecione equipas + grupos.** Selecione esta opção se quiser especificar um conjunto finito de equipas e/ou grupos para rever. Depois de clicar nesta opção, verá uma lista de grupos à direita para escolher.

     ![Equipas e grupos](./media/create-access-review/teams-groups.png)

     ![Equipas e grupos escolhidos na interface de utilizador](./media/create-access-review/teams-groups-detailed.png)

6. Se selecionou **aplicações** no Passo 1, pode selecionar uma ou mais aplicações no Passo 2.

    >[!NOTE]
    > A seleção de vários grupos e/ou aplicações resultará em múltiplas avaliações de acesso criadas. Por exemplo, se selecionar 5 grupos para rever, isso resultará em 5 avaliações de acesso separadas

   ![A interface exibida se escolher aplicações em vez de grupos](./media/create-access-review/select-application-detailed.png)

7. Em seguida, no passo 3 pode selecionar uma margem para a revisão. As suas opções são
   - **Apenas utilizadores convidados.** A seleção desta opção limita a revisão de acesso apenas aos utilizadores convidados Azure AD B2B no seu diretório.
   - **Toda a gente.** A seleção desta opção permite analisar o acesso a todos os objetos do utilizador associados ao recurso.

    >[!NOTE]
    > Se selecionou todos os grupos Microsoft 365 com utilizadores convidados no Passo 2, então a sua única opção é rever os utilizadores convidados no Passo 3

8. Clique em Seguinte: Opiniões
9. Na secção **De revisores Select,** selecione uma ou mais pessoas para realizar as avaliações de acesso. Pode escolher entre:
    - **Proprietários do grupo (Apenas** disponíveis para a realização de uma revisão em uma equipa ou grupo)
    - **Selecionados(s) ou grupos**
    - **Os utilizadores analisam o acesso próprio**
    - **Gestores de utilizadores.**
    Se escolher **os Gestores de utilizadores** ou **os proprietários do Grupo,**  também tem a opção de especificar um revisor de recuo. Os revisores de retorno são convidados a fazer uma revisão quando o utilizador não tem nenhum gestor especificado no diretório ou o grupo não tem um proprietário.

    ![nova revisão de acessos](./media/create-access-review/new-access-review.png)

10. Na secção **De recorrência de especificação,** pode especificar uma frequência como **Weekly, Monthly, Quarterly, Semi-anualmente, Anualmente**. Em seguida, especifica uma **Duração**, que define quanto tempo uma revisão estará aberta para a entrada dos revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é de 27 dias, para evitar comentários sobrepostos. É melhor encurtar a duração para garantir que a entrada dos seus revisores seja aplicada mais cedo. Em seguida, pode selecionar uma **data de início** e data de **fim**.

    ![Escolha a frequência com que a revisão deve acontecer](./media/create-access-review/frequency.png)

11. Clique no **seguinte: Botão de definições** na parte inferior da página
12. Nas **definições de conclusão do Pós-Conclusão** pode especificar o que acontece após a conclusão da revisão

    ![Criar uma revisão de acesso - após definições de conclusão](./media/create-access-review/upon-completion-settings-new.png)

Se pretender remover automaticamente o acesso a utilizadores negados, desaprote de Resultados de Aplicação automática para obter o recurso para ativar. Se pretender aplicar manualmente os resultados quando a revisão estiver concluída, desave o interruptor para Desativar.
Utilize a lista Se os revisores não responderem à lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que tenham sido revistos manualmente pelos revisores. Se a decisão do revisor final for Deny, então o acesso do utilizador será removido.

- **Nenhuma alteração** - Deixe o acesso do utilizador inalterado
- **Remover acesso** - Remover o acesso do utilizador
- **Aprovar acesso** - Aprovar o acesso do utilizador
- **Tome recomendações** - Tome a recomendação do sistema sobre a negação ou aprovação do acesso continuado do utilizador

    ![Após a conclusão, as opções de definição](./media/create-access-review/upon-completion-settings-new.png)

Utilize a Ação para aplicar aos utilizadores **convidados** negados para especificar o que acontece aos utilizadores convidados se forem negados.
- Remover a adesão do utilizador do recurso removerá o acesso negado do utilizador ao grupo ou aplicação que está a ser revista, ainda assim poderão iniciar sação no arrendatário.
- Bloquear o utilizador de iniciar sessão durante 30 dias e, em seguida, remover o utilizador do arrendatário impedirá os utilizadores recusados de se inscreverem no inquilino, independentemente de terem acesso a outros recursos. Se houve um erro ou se um administrador decidir voltar a ativar o acesso de alguém, podem fazê-lo no prazo de 30 dias após o utilizador ter sido desativado. Se não houver medidas contra os utilizadores deficientes, serão eliminadas do arrendatário.

Para saber mais sobre as melhores práticas para remover utilizadores convidados que já não têm acesso a recursos na sua organização, leia o artigo intitulado [Use Azure AD Identity Governance para rever e remover utilizadores externos que já não têm acesso](access-reviews-external-users.md) a recursos.

   >[!NOTE]
   >As medidas a aplicar aos utilizadores convidados negados não são configuráveis em comentários a mais do que os utilizadores convidados. Também não é configurável para avaliações de **todos os grupos M365 com utilizadores convidados.** Quando não é configurável, a opção padrão de remover a adesão do utilizador do recurso é usada em utilizadores negados.

13. Em **Ativar programas auxiliares de decisão de revisão**, escolha se quer que o seu revisor receba recomendações durante o processo de revisão.

    ![Ativar opções de ajudantes de decisão](./media/create-access-review/helpers.png)

14. Na secção **definições Avançadas** pode escolher o seguinte
    - **Definir justificação necessária** para permitir **que** o revisor forneça uma razão de aprovação.
    - Desconfiem **notificações por e-mail** para **permitir** que o AZure AD envie notificações de e-mail aos revisores quando uma revisão de acesso começar e aos administradores quando uma revisão estiver concluída.
    - Definir **Lembretes** para **Permitir** que a Azure AD envie lembretes de avaliações de acesso em curso a revisores que não tenham concluído a sua revisão. Estes lembretes serão a meio da revisão.
    - O conteúdo do e-mail enviado aos revisores é autogerido com base nos detalhes da revisão, tais como nome de revisão, nome de recurso, data de vencimento, etc. Se precisar de uma forma de comunicar informações adicionais, tais como instruções adicionais ou informações de contacto, pode especificar estes dados na secção **de email do revisor.** A informação que inseriu está incluída nos e-mails de convite e lembrete enviados aos revisores designados. A secção realçada na imagem abaixo mostra onde esta informação é exibida.


      ![conteúdo adicional para revisor](./media/create-access-review/additional-content-reviewer.png)

15. Clique em **Seguinte: Revisão + Criar** para passar para a página seguinte
16. Diga o nome da revisão de acesso. Opcionalmente, dê à revisão uma descrição. O nome e a descrição são mostrados aos revisores.
17. Reveja as informações e **selecione Criar**

       ![criar ecrã de revisão](./media/create-access-review/create-review.png)

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