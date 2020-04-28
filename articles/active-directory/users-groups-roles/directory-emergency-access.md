---
title: Gerir contas de administração de acesso de emergência - Azure AD / Microsoft Docs
description: Este artigo descreve como usar contas de acesso de emergência para ajudar a evitar ser bloqueado inadvertidamente fora da sua organização Azure Ative Directory (Azure AD).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74028135"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gerir contas de acesso de emergência em Azure AD

É importante que evite ser acidentalmente bloqueado fora da sua organização Azure Ative Directory (Azure AD) porque não pode iniciar sessão ou ativar a conta de outro utilizador como administrador. Pode mitigar o impacto da falta acidental de acesso administrativo através da criação de duas ou mais contas de *acesso de emergência* na sua organização.

As contas de acesso de emergência são altamente privilegiadas e não são atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários de emergência ou "vidro partido", onde contas administrativas normais não podem ser utilizadas. Recomendamos que mantenha o objetivo de restringir o uso da conta de emergência apenas aos momentos em que é absolutamente necessário.

Este artigo fornece orientações para a gestão de contas de acesso de emergência em Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Por que usar uma conta de acesso de emergência

Uma organização pode precisar de usar uma conta de acesso de emergência nas seguintes situações:

- As contas dos utilizadores são federadas e a federação está atualmente indisponível devido a uma interrupção da rede celular ou a uma interrupção do fornecedor de identidade. Por exemplo, se o fornecedor de identidade hospedado no seu ambiente tiver caído, os utilizadores podem não conseguir entrar quando a Azure AD redireciona para o seu fornecedor de identidade.
- Os administradores estão registados através da Autenticação Multi-Factor Azure, e todos os seus dispositivos individuais estão indisponíveis ou o serviço não está disponível. Os utilizadores podem não conseguir completar a autenticação multi-factor para ativar uma função. Por exemplo, uma falha na rede celular está a impedi-los de atender chamadas telefónicas ou de receber mensagens de texto, os dois únicos mecanismos de autenticação que registaram para o seu dispositivo.
- A pessoa com o mais recente acesso do Administrador Global deixou a organização. A Azure AD impede que a última conta de Administrador Global seja eliminada, mas não impede que a conta seja eliminada ou desativada no local. Qualquer uma das situações pode tornar a organização incapaz de recuperar a conta.
- Circunstâncias imprevistas, como uma emergência de emergência natural, durante as quais um telemóvel ou outras redes podem não estar disponíveis. 

## <a name="create-emergency-access-accounts"></a>Criar contas de acesso de emergência

Crie duas ou mais contas de acesso de emergência. Estas contas devem ser contas apenas \*na nuvem que utilizam o domínio .onmicrosoft.com e que não são federadas ou sincronizadas a partir de um ambiente no local.

Ao configurar estas contas, devem ser cumpridos os seguintes requisitos:

- As contas de acesso de emergência não devem ser associadas a qualquer utilizador individual da organização. Certifique-se de que as suas contas não estão ligadas a telemóveis fornecidos por funcionários, fichas de hardware que viajem com funcionários individuais ou outras credenciais específicas dos funcionários. Esta precaução abrange casos em que um empregado individual é inacessível quando a credencial é necessária. É importante garantir que quaisquer dispositivos registados sejam mantidos num local conhecido e seguro que tenha múltiplos meios de comunicação com a AD Azure.
- O mecanismo de autenticação utilizado para uma conta de acesso de emergência deve ser distinto do utilizado pelas suas outras contas administrativas, incluindo outras contas de acesso de emergência.  Por exemplo, se o seu administrador normal entrar é através de MFA no local, então o Azure MFA seria um mecanismo diferente.  No entanto, se o Azure MFA é a sua principal parte da autenticação para as suas contas administrativas, então considere uma abordagem diferente para estas, como usar o Acesso Condicional com um fornecedor de MFA de terceiros.
- O dispositivo ou a credencial não devem caducar ou estar no âmbito da limpeza automatizada devido à falta de utilização.  
- Deve tornar a atribuição de funções de Administrador Global permanente para as suas contas de acesso de emergência. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Excluir pelo menos uma conta da autenticação multifactor baseada no telefone

Para reduzir o risco de um ataque resultante de uma senha comprometida, a Azure AD recomenda que necessite de autenticação multifactor para todos os utilizadores individuais. Este grupo inclui administradores e todos os outros (por exemplo, oficiais financeiros) cuja conta comprometida teria um impacto significativo.

No entanto, pelo menos uma das suas contas de acesso de emergência não deve ter o mesmo mecanismo de autenticação multifactor que as suas outras contas não urgentes. Isto inclui soluções de autenticação multifactor de terceiros. Se tiver uma política de Acesso Condicional para exigir a [autenticação de vários fatores para cada administrador](../authentication/howto-mfa-userstates.md) da Azure AD e de outros softwares conectados como aplicações de serviço (SaaS), deve excluir as contas de acesso de emergência deste requisito e configurar um mecanismo diferente. Além disso, deve certificar-se de que as contas não têm uma política de autenticação multifactor por utilizador.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Excluir pelo menos uma conta das políticas de Acesso Condicional

Durante uma emergência, não quer uma política que bloqueie potencialmente o seu acesso a resolver um problema. Pelo menos uma conta de acesso de emergência deve ser excluída de todas as políticas de Acesso Condicional. Se tiver ativado uma política de [base,](../conditional-access/baseline-protection.md)deve excluir as suas contas de acesso de emergência.

## <a name="federation-guidance"></a>Orientação da Federação

Uma opção adicional para organizações que utilizam Serviços de Domínio AD e ADFS ou fornecedor de identidade semelhante para federar para a AD Azure, é configurar uma conta de acesso de emergência cuja alegação de MFA poderia ser fornecida por esse fornecedor de identidade.  Por exemplo, a conta de acesso de emergência poderia ser apoiada por um certificado e um par de chaves, como um armazenado num smartcard.  Quando esse utilizador é autenticado em AD, a ADFS pode fornecer uma reclamação à Azure AD indicando que o utilizador cumpriu os requisitos do MFA.  Mesmo com esta abordagem, as organizações devem ainda ter contas de acesso de emergência baseadas na nuvem, caso a federação não possa ser estabelecida. 

## <a name="store-account-credentials-safely"></a>Armazenar credenciais de conta com segurança

As organizações devem garantir que as credenciais para as contas de acesso de emergência sejam mantidas seguras e conhecidas apenas por indivíduos autorizados a usá-las. Alguns clientes usam um smartcard e outros usam senhas. Uma palavra-passe para uma conta de acesso de emergência é geralmente separada em duas ou três partes, escrita em pedaços de papel separados, e armazenada em cofres seguros e à prova de fogo que estão em locais seguros e separados.

Se utilizar palavras-passe, certifique-se de que as contas têm senhas fortes que não expiram a palavra-passe. Idealmente, as palavras-passe devem ter pelo menos 16 caracteres de comprimento e geração aleatória.

## <a name="monitor-sign-in-and-audit-logs"></a>Monitorizar registos de login e auditoria

As organizações devem monitorizar a atividade de login e de registo de auditoria a partir das contas de emergência e desencadear notificações a outros administradores. Ao monitorizar a atividade em contas de vidro de rutura, pode verificar se estas contas são usadas apenas para testes ou emergências reais. Pode utilizar o Azure Log Analytics para monitorizar os registos de entrada e acionar os alertas de e-mail e SMS para os seus administradores sempre que as contas de vidro instituem o log in.

### <a name="prerequisites"></a>Pré-requisitos

1. [Envie registos de entrada de AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) para o Monitor Azure.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Obtenha IDs de objeto das contas de vidro de rutura

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta atribuída ao cargo de administrador do Utilizador.
1. Selecione > **Utilizadores**de **Diretório Ativo Azure**.
1. Procure a conta de break-glass e selecione o nome do utilizador.
1. Copie e guarde o atributo de ID do objeto para que possa usá-lo mais tarde.
1. Repita os passos anteriores para a segunda conta de break-glass.

### <a name="create-an-alert-rule"></a>Criar uma regra de alerta

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta atribuída à função de Colaborador de Monitorização no Monitor Azure.
1. Selecione **todos os serviços**", introduza "log analytics" em Search e, em seguida, selecione espaços de **trabalho Log Analytics**.
1. Selecione uma área de trabalho.
1. No seu espaço de trabalho, selecione **Alertas** > **Nova regra de alerta**.
    1. No âmbito **do Recurso,** verifique se a subscrição é aquela com a qual pretende associar a regra de alerta.
    1. Em **condições,** **selecione Adicionar**.
    1. Selecione **procurar registo personalizado** sob o nome do **sinal**.
    1. Sob consulta de **pesquisa,** introduza a seguinte consulta, inserindo as iDs do objeto das duas contas de vidro de rutura.
        > [!NOTE]
        > Para cada conta adicional de vidro de rutura que pretende incluir, adicione outro "ou UserId == "ObjectGuid" à consulta.

        ![Adicione os IDs do objeto das contas de vidro de rutura a uma regra de alerta](./media/directory-emergency-access/query-image1.png)

    1. Sob **a lógica de Alerta,** insira o seguinte:

        - Com base em: Número de resultados
        - Operador: Maior do que
        - Valor limiar: 0

    1. Em **avaliação com base em**, selecione o Período **(em minutos)** durante quanto tempo pretende que a consulta seja executada e a **Frequência (em minutos)** para saber quantas vezes pretende que a consulta seja executada. A frequência deve ser inferior ou igual ao período.

        ![lógica de alerta](./media/directory-emergency-access/alert-image2.png)

    1. Selecione **Done** (Concluído). Pode agora ver o custo mensal estimado deste alerta.
1. Selecione um grupo de ação de utilizadores para ser notificado pelo alerta. Se quiser criar um, consulte [Criar um grupo de ação.](#create-an-action-group)
1. Para personalizar a notificação de e-mail enviada aos membros do grupo de ação, selecione ações em ações **personalizadas**.
1. Em **detalhes de alerta,** especifique o nome da regra do alerta e adicione uma descrição opcional.
1. Estabeleça o **nível** de gravidade do evento. Recomendamos que o desloque para **A Crítica (Sev 0)**.
1. Sob **a regra Enable sobre a criação,** deixe-a definida como **sim**.
1. Para desligar os alertas durante algum tempo, selecione a caixa de verificação de **alertas de suprimir** e introduza a duração da espera antes de voltar a alertar e, em seguida, selecione **Guardar**.
1. Clique **em Criar a regra**de alerta .

### <a name="create-an-action-group"></a>Criar um grupo de ação

1. Selecione **Criar um grupo de ação**.

    ![criar um grupo de ação para ações de notificação](./media/directory-emergency-access/action-group-image3.png)

1. Introduza o nome do grupo de ação e um nome curto.
1. Verifique o grupo de subscrição e recursos.
1. Sob o tipo de ação, selecione **Email/SMS/Push/Voice**.
1. Introduza um nome de ação como Notificar a **administração global**.
1. Selecione o Tipo de **Ação** como **E-mail/SMS/Push/Voice**.
1. Selecione **detalhes editar** para selecionar os métodos de notificação que pretende configurar e introduzir as informações de contacto necessárias e, em seguida, selecione **Ok** para guardar os detalhes.
1. Adicione quaisquer ações adicionais que queira desencadear.
1. Selecione **OK**.

## <a name="validate-accounts-regularly"></a>Validar contas regularmente

Quando treina os funcionários para utilizar as contas de acesso de emergência e validar as contas de acesso de emergência, faça no mínimo os seguintes passos em intervalos regulares:

- Certifique-se de que os funcionários de controlo de segurança estão cientes de que a atividade de verificação de contas está em curso.
- Certifique-se de que o processo de vidro de rutura de emergência para a utilização destas contas está documentado e atual.
- Certifique-se de que os administradores e oficiais de segurança que possam precisar de realizar estes passos durante uma emergência são treinados sobre o processo.
- Atualize as credenciais de conta, em particular quaisquer palavras-passe, para as suas contas de acesso de emergência e, em seguida, valide que as contas de acesso de emergência podem iniciar sessão e executar tarefas administrativas.
- Certifique-se de que os utilizadores não registaram a autenticação de vários fatores ou o reset de palavra-passe de autosserviço (SSPR) para qualquer dispositivo ou dados pessoais de qualquer utilizador. 
- Se as contas estiverem registadas para autenticação multi-factor a um dispositivo, para utilização durante o início ou ativação de funções, certifique-se de que o dispositivo está acessível a todos os administradores que possam precisar de o utilizar durante uma emergência. Verifique também se o dispositivo pode comunicar através de pelo menos dois caminhos de rede que não partilham um modo de falha comum. Por exemplo, o dispositivo pode comunicar à internet através da rede sem fios de uma instalação e de uma rede de fornecedores de células.

Estes passos devem ser realizados regularmente e para alterações fundamentais:

- Pelo menos a cada 90 dias
- Quando houve uma mudança recente no pessoal de TI, como uma mudança de emprego, uma partida, ou um novo contrato
- Quando as subscrições da AD Azure na organização mudaram

## <a name="next-steps"></a>Passos seguintes

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](directory-admin-roles-secure.md)
- [Adicione utilizadores usando AD Azure](../fundamentals/add-users-azure-active-directory.md) e [atribua o novo utilizador à função de Administrador Global](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Inscreva-se no Azure AD Premium,](../fundamentals/active-directory-get-started-premium.md)se ainda não se inscreveu
- [Como exigir verificação em duas etapas para um utilizador](../authentication/howto-mfa-userstates.md)
- [Configure proteções adicionais para administradores globais no Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se estiver a utilizar o Office 365
- [Inicie uma revisão de acesso dos Administradores Globais](../privileged-identity-management/pim-how-to-start-security-review.md) e a transição dos [administradores globais existentes para funções de administrador mais específicas](directory-assign-admin-roles.md)
