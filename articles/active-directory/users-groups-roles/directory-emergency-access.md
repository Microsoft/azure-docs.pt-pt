---
title: Gerenciar contas de administrador de acesso de emergência-Azure AD | Microsoft Docs
description: Este artigo descreve como usar contas de acesso de emergência para ajudar a evitar que seja bloqueado inadvertidamente da organização do Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028135"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gerenciar contas de acesso de emergência no Azure AD

É importante que você impeça que você seja bloqueado acidentalmente da organização do Azure Active Directory (Azure AD) porque não é possível entrar ou ativar outra conta de usuário como administrador. Você pode mitigar o impacto da falta acidental de acesso administrativo criando duas ou mais *contas de acesso de emergência* em sua organização.

As contas de acesso de emergência são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de "interrupção" em que as contas administrativas normais não podem ser usadas. Recomendamos que você mantenha uma meta de restringir o uso da conta de emergência apenas às vezes em que ela é absolutamente necessária.

Este artigo fornece diretrizes para gerenciar contas de acesso de emergência no Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Por que usar uma conta de acesso de emergência

Uma organização pode precisar usar uma conta de acesso de emergência nas seguintes situações:

- As contas de usuário são federadas e a Federação não está disponível no momento devido a uma quebra de rede de célula ou uma interrupção do provedor de identidade. Por exemplo, se o host do provedor de identidade em seu ambiente for desativado, os usuários poderão não conseguir entrar quando o Azure AD redirecionar para seu provedor de identidade.
- Os administradores são registrados por meio da autenticação multifator do Azure e todos os seus dispositivos individuais não estão disponíveis ou o serviço não está disponível. Os usuários podem não conseguir concluir a autenticação multifator para ativar uma função. Por exemplo, uma interrupção de rede de célula está impedindo que eles respondam a chamadas telefônicas ou recebam mensagens de texto, os únicos mecanismos de autenticação que eles registraram para seu dispositivo.
- A pessoa com o acesso de administrador global mais recente saiu da organização. O Azure AD impede que a última conta de administrador global seja excluída, mas não impede que a conta seja excluída ou desativada localmente. Qualquer uma das situações pode fazer com que a organização não consiga recuperar a conta.
- Circunstâncias imprevistas, como uma emergência de desastre natural, durante a qual um telefone celular ou outras redes podem estar indisponíveis. 

## <a name="create-emergency-access-accounts"></a>Criar contas de acesso de emergência

Crie duas ou mais contas de acesso de emergência. Essas contas devem ser contas somente em nuvem que usam o domínio \*. onmicrosoft.com e que não são federadas ou sincronizadas de um ambiente local.

Ao configurar essas contas, os requisitos a seguir devem ser atendidos:

- As contas de acesso de emergência não devem ser associadas a nenhum usuário individual na organização. Verifique se suas contas não estão conectadas a nenhum telefone celular fornecido pelo funcionário, tokens de hardware que viajam com funcionários individuais ou outras credenciais específicas do funcionário. Essa precaução abrange instâncias em que um funcionário individual está inacessível quando a credencial é necessária. É importante garantir que todos os dispositivos registrados sejam mantidos em um local seguro e conhecido que tenha vários meios de comunicação com o Azure AD.
- O mecanismo de autenticação usado para uma conta de acesso de emergência deve ser diferente daquele usado por suas outras contas administrativas, incluindo outras contas de acesso de emergência.  Por exemplo, se sua entrada de administrador normal for por meio de MFA local, o Azure MFA seria um mecanismo diferente.  No entanto, se o Azure MFA for sua parte principal da autenticação para suas contas administrativas, considere uma abordagem diferente para eles, como usar o acesso condicional com um provedor de MFA de terceiros.
- O dispositivo ou a credencial não deve expirar ou estar no escopo da limpeza automatizada devido à falta de uso.  
- Você deve tornar a atribuição de função de administrador global permanente para suas contas de acesso de emergência. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Exclua pelo menos uma conta da autenticação multifator baseada em telefone

Para reduzir o risco de um ataque resultante de uma senha comprometida, o AD do Azure recomenda que você exija a autenticação multifator para todos os usuários individuais. Esse grupo inclui administradores e todos os outros (por exemplo, gerentes financeiros) cuja conta comprometida teria um impacto significativo.

No entanto, pelo menos uma das suas contas de acesso de emergência não deve ter o mesmo mecanismo de autenticação multifator que suas outras contas não emergenciais. Isso inclui soluções de autenticação multifator de terceiros. Se você tiver uma política de acesso condicional para exigir a [autenticação multifator para cada administrador](../authentication/howto-mfa-userstates.md) do Azure AD e outros aplicativos SaaS (software como serviço) conectados, você deverá excluir contas de acesso de emergência desse requisito e configurar um mecanismo diferente. Além disso, você deve verificar se as contas não têm uma política de autenticação multifator por usuário.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Exclua pelo menos uma conta das políticas de acesso condicional

Durante uma emergência, você não deseja que uma política bloqueie potencialmente seu acesso para corrigir um problema. Pelo menos uma conta de acesso de emergência deve ser excluída de todas as políticas de acesso condicional. Se você tiver habilitado uma [política de linha de base](../conditional-access/baseline-protection.md), deverá excluir suas contas de acesso de emergência.

## <a name="federation-guidance"></a>Diretrizes da Federação

Uma opção adicional para organizações que usam serviços de domínio do AD e ADFS ou provedor de identidade semelhante para federar ao Azure AD é configurar uma conta de acesso de emergência cuja declaração de MFA possa ser fornecida por esse provedor de identidade.  Por exemplo, a conta de acesso de emergência pode ser apoiada por um certificado e um par de chaves, como um armazenado em um cartão inteligente.  Quando esse usuário é autenticado no AD, o ADFS pode fornecer uma declaração ao AD do Azure indicando que o usuário atende aos requisitos de MFA.  Mesmo com essa abordagem, as organizações ainda devem ter contas de acesso de emergência baseadas em nuvem caso a Federação não possa ser estabelecida. 

## <a name="store-account-credentials-safely"></a>Armazenar credenciais de conta com segurança

As organizações precisam garantir que as credenciais para contas de acesso de emergência sejam mantidas seguras e conhecidas somente por indivíduos autorizados a usá-las. Alguns clientes usam um cartão inteligente e outros usam senhas. Uma senha para uma conta de acesso de emergência geralmente é separada em duas ou três partes, escrita em partes separadas de papel e armazenada em seguros seguros à prova de fogo que estão em locais seguros e separados.

Se estiver usando senhas, verifique se as contas têm senhas fortes que não expiram a senha. O ideal é que as senhas tenham pelo menos 16 caracteres e geradas aleatoriamente.

## <a name="monitor-sign-in-and-audit-logs"></a>Monitorar a entrada e os logs de auditoria

As organizações devem monitorar a atividade de entrada e de log de auditoria das contas de emergência e disparar notificações para outros administradores. Ao monitorar a atividade em contas de vidro, você pode verificar se essas contas são usadas apenas para teste ou emergências reais. Você pode usar o Azure Log Analytics para monitorar os logs de entrada e disparar alertas de email e SMS para seus administradores sempre que as contas de interrupção entrarem.

### <a name="prerequisites"></a>Pré-requisitos

1. [Envie logs de entrada do Azure ad](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) para Azure monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Obter IDs de objeto das contas de interrupção

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de administrador de usuários.
1. Selecione **Azure Active Directory** > **usuários**.
1. Procure a conta de vidro e selecione o nome do usuário.
1. Copie e salve o atributo ID de objeto para que você possa usá-lo mais tarde.
1. Repita as etapas anteriores para a segunda conta de vidro.

### <a name="create-an-alert-rule"></a>Criar uma regra de alerta

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de colaborador de monitoramento no Azure monitor.
1. Selecione **todos os serviços**", insira" log Analytics "na pesquisa e, em seguida, selecione **log Analytics espaços de trabalho**.
1. Selecione uma área de trabalho.
1. Em seu espaço de trabalho, selecione **alertas** > **nova regra de alerta**.
    1. Em **recurso**, verifique se a assinatura é aquela com a qual você deseja associar a regra de alerta.
    1. Em **condição**, selecione **Adicionar**.
    1. Selecione **pesquisa de logs personalizada** em **nome do sinal**.
    1. Em **consulta de pesquisa**, insira a consulta a seguir, inserindo as IDs de objeto das duas contas de vidro de interrupção.
        > [!NOTE]
        > Para cada conta de vidro adicional que você deseja incluir, adicione outro "ou UserId = =" objectGUID "" à consulta.

        ![Adicionar as IDs de objeto das contas de interrupção a uma regra de alerta](./media/directory-emergency-access/query-image1.png)

    1. Em **lógica de alerta**, insira o seguinte:

        - Com base em: número de resultados
        - Operador: maior que
        - Valor do limite: 0

    1. Em **avaliado com base em**, selecione o **período (em minutos)** para o tempo que você deseja que a consulta seja executada e a **frequência (em minutos)** para a frequência com que você deseja que a consulta seja executada. A frequência deve ser menor ou igual ao período.

        ![lógica de alerta](./media/directory-emergency-access/alert-image2.png)

    1. Selecione **Done** (Concluído). Agora você pode exibir o custo mensal estimado deste alerta.
1. Selecione um grupo de ações de usuários a ser notificado pelo alerta. Se você quiser criar um, consulte [criar um grupo de ação](#create-an-action-group).
1. Para personalizar a notificação por email enviada aos membros do grupo de ações, selecione ações em **Personalizar ações**.
1. Em **detalhes do alerta**, especifique o nome da regra de alerta e adicione uma descrição opcional.
1. Defina o **nível de severidade** do evento. É recomendável defini-lo como **crítico (Sev 0)** .
1. Em **Habilitar Regra após a criação**, deixe definida como **Sim**.
1. Para desativar os alertas por um tempo, marque a caixa de seleção **suprimir alertas** e insira a duração da espera antes de alertar novamente e, em seguida, selecione **salvar**.
1. Clique em **criar regra de alerta**.

### <a name="create-an-action-group"></a>Criar um grupo de ação

1. Selecione **criar um grupo de ação**.

    ![criar um grupo de ações para ações de notificação](./media/directory-emergency-access/action-group-image3.png)

1. Insira o nome do grupo de ações e um nome curto.
1. Verifique a assinatura e o grupo de recursos.
1. Em tipo de ação, selecione **email/SMS/Push/voz**.
1. Insira um nome de ação, como **notificar administrador global**.
1. Selecione o **tipo de ação** como **email/SMS/Push/voz**.
1. Selecione **Editar detalhes** para selecionar os métodos de notificação que você deseja configurar e insira as informações de contato necessárias e, em seguida, selecione **OK** para salvar os detalhes.
1. Adicione quaisquer ações adicionais que você deseja disparar.
1. Selecione **OK**.

## <a name="validate-accounts-regularly"></a>Validar contas regularmente

Ao treinar os membros da equipe para usar contas de acesso de emergência e validar as contas de acesso de emergência, no mínimo, execute as seguintes etapas em intervalos regulares:

- Verifique se a equipe de monitoramento de segurança está ciente de que a atividade de verificação de conta está em andamento.
- Verifique se o processo de interrupção de emergência para usar essas contas está documentado e atual.
- Verifique se os administradores e os responsáveis pela segurança que podem precisar executar essas etapas durante uma emergência são treinados no processo.
- Atualize as credenciais da conta, em particular quaisquer senhas, para suas contas de acesso de emergência e, em seguida, valide que as contas de acesso de emergência podem entrar e executar tarefas administrativas.
- Verifique se os usuários não registraram a autenticação multifator ou a SSPR (redefinição de senha de autoatendimento) em qualquer dispositivo ou detalhes pessoais do usuário individual. 
- Se as contas estiverem registradas para autenticação multifator para um dispositivo, para uso durante a entrada ou ativação de função, verifique se o dispositivo está acessível a todos os administradores que talvez precisem usá-lo durante uma emergência. Verifique também se o dispositivo pode se comunicar por pelo menos dois caminhos de rede que não compartilham um modo de falha comum. Por exemplo, o dispositivo pode se comunicar com a Internet por meio de uma rede sem fio de um recurso e de uma rede de provedor de célula.

Essas etapas devem ser executadas em intervalos regulares e para as principais alterações:

- Pelo menos a cada 90 dias
- Quando houve uma alteração recente na equipe de ti, como uma alteração de trabalho, uma partida ou uma nova contratação
- Quando as assinaturas do Azure AD na organização foram alteradas

## <a name="next-steps"></a>Passos seguintes

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](directory-admin-roles-secure.md)
- [Adicionar usuários usando o Azure ad](../fundamentals/add-users-azure-active-directory.md) e [atribuir o novo usuário à função de administrador global](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Inscreva-se para Azure ad Premium](../fundamentals/active-directory-get-started-premium.md), se você ainda não se inscreveu
- [Como exigir a verificação em duas etapas para um usuário](../authentication/howto-mfa-userstates.md)
- [Configurar proteções adicionais para administradores globais no Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se você estiver usando o Office 365
- [Iniciar uma revisão de acesso de administradores globais](../privileged-identity-management/pim-how-to-start-security-review.md) e [fazer a transição de administradores globais existentes para funções de administrador mais específicas](directory-assign-admin-roles.md)
