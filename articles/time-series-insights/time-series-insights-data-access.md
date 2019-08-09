---
title: Configurar a segurança para acessar e gerenciar o Azure Time Series Insights Preview | Microsoft Docs
description: Este artigo descreve como configurar a segurança e as permissões como políticas de acesso de gerenciamento e políticas de acesso a dados para proteger Azure Time Series Insights visualização.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 4455d499ab4c52a27a7d9cf878e8130ff38b1c62
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846943"
---
# <a name="grant-data-access-to-an-environment"></a>Conceder acesso a dados a um ambiente

Este artigo aborda os dois tipos de políticas de acesso de Azure Time Series Insights visualização.

## <a name="sign-in-to-time-series-insights"></a>Entrar no Time Series Insights

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Localize seu ambiente de Time Series Insights. Digite `Time Series` na caixa de **pesquisa** . Selecione o **ambiente de série temporal** nos resultados da pesquisa.
1. Selecione o seu ambiente do Time Series Insights na lista.

## <a name="grant-data-access"></a>Conceder acesso a dados

Siga estas etapas para conceder acesso a dados para uma entidade de usuário.

1. Selecione **políticas de acesso a dados**e, em seguida, selecione **+ Adicionar**.

    [![Acesso a dados-um](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Escolha **Selecionar usuário**. Procure o nome de usuário ou endereço de email para localizar o usuário que você deseja adicionar. Selecione **selecionar** para confirmar a seleção.

    [![Acesso a dados-dois](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Escolha **selecionar função**. Escolha a função de acesso apropriada para o usuário:

    * Selecione **colaborador** se desejar permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente.

    * Caso contrário, selecione **leitor** para permitir que o usuário consulte dados no ambiente e Salve consultas pessoais e não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Acesso a dados-três](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Selecione **OK** na página **selecionar função de usuário** .

    [![Acesso a dados-quatro](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Confirme se a página **políticas de acesso a dados** lista os usuários e as funções para cada usuário.

    [![Acesso a dados-cinco](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Fornecer acesso de convidado de outro locatário do AAD

`Guest`Não é uma função de gerenciamento. É um termo usado para uma conta que é convidada de um locatário para outro. Depois que a conta de convidado é convidada no diretório do locatário, ela pode ter o mesmo controle de acesso aplicado a ela, como qualquer outra conta. Você pode conceder acesso de gerenciamento a um ambiente de Time Series Insights usando a folha de controle de acesso (IAM). Ou você pode conceder acesso aos dados no ambiente por meio da folha políticas de acesso a dados. Para obter mais informações sobre o acesso convidado do locatário do Azure Active Directory (Azure AD), leia [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Siga estas etapas para conceder acesso de convidado a um ambiente de Time Series Insights para um usuário do Azure AD de outro locatário.

1. Selecione **políticas de acesso a dados**e, em seguida, selecione **+ convidar**.

    [![Acesso a dados-seis](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Insira o endereço de email do usuário que você deseja convidar. Este endereço de email deve ser associado ao Azure AD. Opcionalmente, você pode incluir uma mensagem pessoal com o convite.

    [![Acesso a dados-sete](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Procure a bolha de confirmação que aparece na tela.

    [![Acesso a dados-oito](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Escolha **Selecionar usuário**. Procure o endereço de email do usuário convidado que você convidou para localizar o usuário que deseja adicionar. Em seguida, **selecione** para confirmar a seleção.

    [![Acesso a dados-nove](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Escolha **selecionar função**. Escolha a função de acesso apropriada para o usuário convidado:

    * Selecione **colaborador** se desejar permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente.

    * Caso contrário, selecione **leitor** para permitir que o usuário consulte dados no ambiente e Salve consultas pessoais e não compartilhadas no ambiente.

   Selecione **OK** para confirmar a escolha da função.

    [![Acesso a dados-dez](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Selecione **OK** na página **selecionar função de usuário** .

1. Confirme se a página **políticas de acesso a dados** lista o usuário convidado e as funções para cada usuário convidado.

    [![Acesso a dados-onze](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Agora, o usuário convidado deve seguir as etapas para acessar o ambiente localizado no locatário do Azure para o qual você os convidou. Primeiro, eles aceitam o convite que você os enviou. Esse convite é enviado por email para o endereço de email que você usou na etapa 5. Eles selecionam começar a aceitar.

    [![Acesso a dados-doze](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Em seguida, o usuário convidado aceita as permissões associadas à organização do administrador.

    [![Acesso a dados-treze](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Depois que o usuário convidado estiver conectado ao endereço de email que você usou para convidá-los e aceitar o convite, eles vão para insights.azure.com. Uma vez lá, eles selecionam o Avatar ao lado de seu endereço de email no canto superior direito da tela.

    [![Acesso a dados-quatorze](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Em seguida, o usuário convidado seleciona seu locatário do Azure no menu suspenso diretório. Esse locatário é aquele para o qual você os convidou.

    [![Acesso a dados-quinze](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Depois que o usuário convidado selecionar seu locatário, ele verá o Time Series Insights ambiente ao qual você forneceu acesso. Agora, eles têm todos os recursos associados à função com a qual você os forneceu na **etapa 5**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba [como adicionar uma fonte de eventos de hubs de eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente de time Series insights.

* Enviar [eventos para a origem do evento](./time-series-insights-send-events.md).

* Exiba [seu ambiente no time Series insights Preview Explorer](./time-series-insights-update-explorer.md).
