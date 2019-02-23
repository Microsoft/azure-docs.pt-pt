---
title: Criar uma revisão de acesso de grupos ou aplicações nas revisões de acesso do Azure AD | Documentos da Microsoft
description: Saiba como criar uma revisão de acesso de membros do grupo ou o acesso de aplicação nas revisões de acesso do Azure AD.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca27e89ad67b0f0755a7dda50572f3dcd857189
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733934"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Criar uma revisão de acesso de grupos ou aplicações nas revisões de acesso do Azure AD

Acesso a grupos e aplicações para os funcionários e convidados altera ao longo do tempo. Para reduzir o risco associado atribuições de acesso obsoletos, os administradores podem utilizar o Azure Active Directory (Azure AD) para criar as revisões de acesso para membros do grupo ou de acesso à aplicação. Se precisar de rever regularmente o acesso, também pode criar as revisões de acesso recorrentes. Para obter mais informações sobre estes cenários, consulte [gerir o acesso de utilizador](manage-user-access-with-access-reviews.md) e [gerir o acesso de convidado](manage-guest-access-with-access-reviews.md).

Este artigo descreve como criar um ou mais as revisões de acesso para membros do grupo ou de acesso à aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- [Revisões de acesso ativadas](access-reviews-overview.md)
- Administrador global ou administrador de conta de utilizador

## <a name="create-one-or-more-access-reviews"></a>Criar um ou mais as revisões de acesso

1. Início de sessão no portal do Azure e abra o [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Clique em **controles**.

1. Clique em **nova revisão de acesso** para criar uma nova revisão de acesso.

    ![Examine - controlos de acesso](./media/create-access-review/controls.png)

1. Nome da revisão de acesso. Opcionalmente, forneça a revisão de uma descrição. O nome e descrição são apresentadas aos revisores.

    ![Criar uma revisão de acesso - revisão de nome e descrição](./media/create-access-review/name-description.png)

1. Definir o **data de início**. Por predefinição, uma revisão de acesso ocorre uma vez, é iniciada ao mesmo tempo, que é criado e termina dentro de um mês. Pode alterar o início e datas de término para ter um acesso rever início no futuro e pela última vez no entanto, o número de dias que pretende.

    ![Criar uma revisão de acesso - início e datas de término](./media/create-access-review/start-end-dates.png)

1. Para tornar a revisão de acesso recorrente, altere a **frequência** definição **uma vez** para **semanal**, **mensal**,  **Trimestral** ou **anualmente**e utilizar os **duração** caixa de texto ou de controlo de deslize para definir o número de dias será cada revisão da série recorrente abrir para a entrada de revisores. Por exemplo, a duração máxima que pode definir para uma revisão mensal é dias 27, para evitar a sobreposição de revisões.

1. Utilize o **final** definição para especificar como terminar o acesso recorrente rever série. A série pode ser encerrada de três formas: ela seja executada continuamente para iniciar as revisões indefinidamente, até uma data específica ou após um determinado número de ocorrências de conclusão. , Outro administrador de conta de utilizador ou outro administrador Global pode parar a série após a criação, alterando a data no **definições**, para que ele termina a essa data.

1. Na **utilizadores** secção, especifique os utilizadores que a revisão de acesso aplica-se a. As revisões de acesso podem ser para os membros de um grupo ou para os utilizadores que foram atribuídos a uma aplicação. Pode ainda mais definir o âmbito de acesso apenas de revisão para rever os utilizadores convidados que são membros (ou atribuídos à aplicação), em vez de examinar todos os utilizadores que são membros ou que têm acesso à aplicação.

    ![Criar uma revisão de acesso - utilizadores](./media/create-access-review/users.png)

1. Na **grupos** secção, selecione um ou mais grupos que gostaria de rever a associação de.

    > [!NOTE]
    > Selecionar mais de um grupo, irá criar várias revisões de acesso. Por exemplo, se selecionar grupos de cinco, irá criar cinco revisões de acesso separado.
    
    ![Criar uma revisão de acesso - grupo](./media/create-access-review/select-group.png)

1. Na **aplicativos** secção (se tiver selecionado **atribuídos a uma aplicação** no passo 8), selecione as aplicações que pretende rever o acesso a.

    > [!NOTE]
    > Selecionar mais de uma aplicação, irá criar várias revisões de acesso. Por exemplo, a seleção de cinco aplicativos criará cinco revisões de acesso separado.
    
    ![Criar uma revisão de acesso - selecionar aplicação](./media/create-access-review/select-application.png)

1. Na **revisores** secção, selecione um ou mais pessoas para rever todos os utilizadores no âmbito. Ou pode selecionar para que os membros, reveja o seu próprio acesso. Se o recurso é um grupo, é possível pedir os proprietários do grupo para rever. Também pode exigir que os revisores fornecem um motivo quando eles aprovar o acesso.

    ![Criar uma revisão de acesso - revisores](./media/create-access-review/reviewers.png)

1. Na **programas** secção, selecione o programa que pretende utilizar. Pode simplificar a forma de controlar e recolher as revisões de acesso para diferentes fins, organizando-os em programas. **Programa predefinido** sempre esteja presente, ou pode criar um programa diferente. Por exemplo, pode optar por ter um programa para cada iniciativa de conformidade ou de suas metas de negócios.

    ![Criar uma revisão de acesso – programas](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>Após as definições de conclusão

1. Para especificar o que acontece após a conclusão de uma revisão, expanda o **nas definições de conclusão** secção.

    ![Após as definições de conclusão](./media/create-access-review/upon-completion-settings.png)

1. Se quiser automaticamente remover acesso para utilizadores que foram negados, definida **automaticamente aplicar resultados ao recurso** ao **ativar**. Se deseja aplicar manualmente os resultados quando a revisão terminar, defina o comutador **desativar**.

1. Utilize o **deve o revisor não responder** lista para especificar o que acontece para os utilizadores que não são revistos pelo revisor dentro do período de revisão. Esta definição não afeta os utilizadores que foram revistos, os revisores manualmente. Se a decisão do revisor final é negar, em seguida, o acesso do utilizador será removido.

    - **Nenhuma alteração** -deixe sem alterações de acesso de utilizador
    - **Remover o acesso** -remover o acesso do utilizador
    - **Aprovar o acesso** -aprovar o acesso do utilizador
    - **Efetuar recomendações** - tirar a recomendação do sistema ao negar ou aprovar o utilizador do acesso contínuo

### <a name="advanced-settings"></a>Definições avançadas

1. Para especificar definições adicionais, expanda o **definições avançadas** secção.

    ![Definições avançadas](./media/create-access-review/advanced-settings.png)

1. Definir **Mostrar recomendações** ao **ativar** para mostrar os revisores do sistema recomendações com base em informações de acesso do utilizador.

1. Definir **exigir razão na aprovação** ao **ativar** para solicite ao revisor que forneça um motivo para aprovação.

1. Definir **notificações de E-Mail** ao **ativar** para ter o Azure AD para enviar notificações por e-mail para os revisores quando é iniciado uma revisão de acesso e para os administradores quando uma revisão é concluída.

1. Definir **lembretes** ao **ativar** para que o Azure AD envie lembretes de revisões de acesso em curso para revisores que não concluíram as respetivas revisões.

## <a name="start-the-access-review"></a>Iniciar a revisão de acesso

Depois de especificar as definições para uma revisão de acesso, clique em **iniciar**.

Por predefinição, o Azure AD envia um e-mail para os revisores logo após a revisão é iniciado. Se optar por não ter o Azure AD para enviar o e-mail, certifique-se de que informe os revisores de que uma revisão de acesso está a aguardar a sua conclusão. Pode mostrar-lhes as instruções sobre como a [rever o acesso a grupos ou aplicações](perform-access-review.md). Se sua revisão for para convidados rever o seu próprio acesso, mostrar-lhes as instruções sobre como a [rever acesso para si próprio para grupos ou aplicações](review-your-access.md).

Se alguns dos revisores são convidados, convidados são notificados por e-mail apenas se eles já já aceitou o convite.

## <a name="manage-the-access-review"></a>Gerir a revisão de acesso

Pode monitorizar o progresso conforme os revisores de concluir as revisões no dashboard do Azure AD no **revisões de acesso** secção. Nenhum direito de acesso forem alterado no diretório até [a revisão é concluída](complete-access-review.md).

Se se tratar de uma revisão de uso individual, em seguida, após o período de revisão de acesso ou o administrador para a revisão de acesso, siga os passos em [concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md) para ver e aplicar os resultados.  

Para gerir uma série de revisões de acesso, navegue para a revisão de acesso de **controles**, e será encontrar ocorrências futuras nas revisões de agendada e edite a data de fim ou adicionar/remover os revisores da mesma forma. 

Com base nas suas seleções após as definições de conclusão,-aplicar automaticamente irá ser executado após a data de fim a revisão ou quando manualmente para a revisão. O estado da revisão deixará de concluído por meio de Estados intermediários como aplicar e, finalmente, para o estado aplicado. Deve esperar para ver utilizadores sem permissão, caso haja algum, que está a ser removido a atribuição de aplicação ou associação de grupo dentro de alguns minutos.

## <a name="create-reviews-via-apis"></a>Criar análises através de APIs

Também pode criar as revisões de acesso através de APIs. O que fazer para gerir o acesso a revisões de grupos e utilizadores da aplicação no portal do Azure também podem ser feitos usando o Microsoft Graph APIs. Para obter mais informações, consulte a [referência de revisões de acesso do Azure AD API](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Para obter um exemplo de código, consulte [exemplo de obtenção de revisões de acesso do Azure AD através do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passos Seguintes

- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Rever acesso para si próprio para grupos ou aplicações](review-your-access.md)
- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
