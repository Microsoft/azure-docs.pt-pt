---
title: Configurar a segurança para aceder e gerir a pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como configurar permissões e segurança que o acesso de gestão políticas e acesso a dados políticas para proteger a pré-visualização do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 53489ef9c1264fa31b06f4660e545e6efbd1f616
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237039"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso de pré-visualização do Azure Time Series Insights.

## <a name="sign-in-to-time-series-insights"></a>Inicie sessão no Time Series Insights

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Localize o seu ambiente do Time Series Insights. Introduza `Time Series` no **pesquisa** caixa. Selecione **ambiente de Time Series** nos resultados da pesquisa.
1. Selecione o seu ambiente do Time Series Insights na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estes passos para conceder acesso a dados para um principal de utilizador.

1. Selecione **políticas de acesso de dados**e, em seguida, selecione **+ adicionar**.

    [![um de acesso de dados](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Escolher **selecionar utilizador**. Procure o endereço de e-mail ou nome de utilizador localizar o utilizador que pretende adicionar. Clique em **selecione** para confirmar a seleção.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Escolher **selecionar função**. Escolha a função de acesso apropriados para o utilizador:

    * Selecione **contribuinte** se pretender permitir que o utilizador alterar dados de referência e consultas de partilha guardada e perspetivas com outros utilizadores do ambiente.

    * Caso contrário, selecione **leitor** para permitir que o utilizador para consultar dados no ambiente e guardar consultas pessoais, não partilhadas, no ambiente.

   Selecione **OK** para confirmar a escolha de função.

    [![acesso a dados-três](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Selecione **OK** sobre o **selecionar função de utilizador** página.

    [![quarto de acesso de dados](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Confirme que o **políticas de acesso de dados** página lista os utilizadores e as funções para cada utilizador.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Fornecer acesso de convidado de outro inquilino do AAD

`Guest` Não é uma função de gestão. É um termo utilizado para uma conta que é convidada a partir de um inquilino para outro. Depois da conta de convidado é convidada para o diretório do inquilino, ele pode ter o mesmo controle de acesso aplicado à mesma, como qualquer outra conta. Pode conceder acesso de gestão para um ambiente de informações de série de tempo utilizando o painel de controlo de acesso (IAM). Ou pode conceder acesso aos dados no ambiente por meio do painel de políticas de acesso de dados. Para obter mais informações sobre o acesso de convidado de inquilino do Azure Active Directory (Azure AD), leia [utilizadores de colaboração de adicionar Azure Active Directory B2B no portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estes passos para conceder acesso de convidado para um ambiente do Time Series Insights para um utilizador de outro inquilino.

1. Selecione **políticas de acesso de dados**e, em seguida, selecione **+ convidar**.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Introduza o endereço de e-mail do utilizador que pretende convidar. Este endereço de e-mail tem de ser associado com o Azure AD. Opcionalmente, pode incluir uma mensagem pessoal com o convite.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Procure a bolha de confirmação que aparece na tela.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Escolher **selecionar utilizador**. Procure o endereço de e-mail do utilizador convidado que convidou para localizar o utilizador que pretende adicionar. Clique em **selecione** para confirmar a seleção.

    [![dados e acesso-nove](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Escolher **selecionar função**. Escolha a função de acesso apropriados para o utilizador convidado:

    * Selecione **contribuinte** se pretender permitir que o utilizador alterar dados de referência e consultas de partilha guardada e perspetivas com outros utilizadores do ambiente.

    * Caso contrário, selecione **leitor** para permitir que o utilizador para consultar dados no ambiente e guardar consultas pessoais, não partilhadas, no ambiente.

   Selecione **OK** para confirmar a escolha de função.

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Selecione **OK** sobre o **selecionar função de utilizador** página.

1. Confirme que o **políticas de acesso de dados** página lista o utilizador convidado e as funções para cada utilizador convidado.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Agora, o utilizador convidado terá de seguir os passos para aceder ao ambiente localizado no inquilino do Azure para o qual convidado. Em primeiro lugar, eles aceitarem o convite enviado-los. Este convite é enviado por e-mail para o endereço de e-mail utilizado no passo 5. Eles selecionam **começar** para aceitar.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Em seguida, o utilizador convidado aceita as permissões associadas a organização do administrador.

    [![treze dados-acesso](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Depois do utilizador convidado é iniciar sessão para o endereço de e-mail que utilizou para convidá-los e eles aceitarem o convite, eles vão para insights.azure.com. Uma vez lá, eles selecionam o avatar junto ao respetivo endereço de e-mail no canto superior direito da tela.

    [![data-access-quatorze](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Em seguida, o utilizador selecionar de convidado de inquilino do Azure no menu de lista pendente de diretório. Este inquilino é aquela à qual convidado.

    [![acesso de dados-quinze](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Após o utilizador convidado seleciona o seu inquilino, verão o ambiente do Time Series Insights para que que forneceu-lhes acesso. Tem agora todas as funcionalidades associadas à função que forneceu-os na **passo 5**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais [como adicionar uma origem de eventos do Event Hubs do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente do Time Series Insights.

* Envie [eventos para a origem do evento](./time-series-insights-send-events.md).

* Modo de exibição [seu ambiente no Explorador do Time Series Insights pré-visualização](./time-series-insights-update-explorer.md).
