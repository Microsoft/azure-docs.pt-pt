---
title: Configure o 'Stay signed in?' solicitação para contas Azure Ative Directory
description: Saiba mais sobre manter-me inscrito (KMSI), que mostra a Estadia assinada? pronta, como configurá-lo no portal Azure Ative Directory, e como resolver problemas de inscrição.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320261"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Configure o 'Stay signed in?' solicitação para contas AD AZure

Mantenha-me assinado em (KMSI) exibe um **Stay signed in?** Se um utilizador responder **Sim** a esta solicitação, o manter-me assinado em serviço dá-lhes um token persistente [de atualização](../develop/developer-glossary.md#refresh-token). Para os inquilinos federados, a solicitação será mostrada após o utilizador autenticar com sucesso o serviço de identidade federado.

O diagrama seguinte mostra o fluxo de entrada do utilizador para um inquilino gerido e inquilino federado e o novo manter-me assinado em sinal rápido. Este fluxo contém lógica inteligente para que a opção **Stay in?**

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagrama mostrando o fluxo de entrada do utilizador para um inquilino gerido vs. federado":::

> [!NOTE]
> Configurar a opção manter-me assinado requer que utilize o Azure Ative Directory (Azure AD) Premium 1, Premium 2 ou Edições Basic, ou ter uma licença Microsoft 365. Para obter mais informações sobre licenciamento e edições, consulte [Inscreva-se no Azure AD Premium.](active-directory-get-started-premium.md)<br><br>As edições Azure AD Premium e Basic estão disponíveis para clientes na China usando o exemplo mundial de Azure AD. As edições do Azure AD Premium e Básico não são atualmente suportadas pelo serviço do Azure operado pela 21Vianet na China. Para mais informações, fale connosco através do [Azure AD Forum](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="configure-kmsi"></a>Configurar KMSI

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.
1. Selecione **Azure Ative Directory**, selecione **Marca da Empresa**e, em seguida, selecione **Configure**.
1. Na secção **Definições Avançadas,** encontre **a opção 'Mostrar' para permanecer assinada na** definição.

   Esta definição permite-lhe escolher se os seus utilizadores permanecem inscritos no AZure AD até que eles assinem explicitamente.
   * Se escolher **Não,** a opção **Stay in?**
   * Se escolher **Sim,** a opção **Stay in?**

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Diagrama mostrando o fluxo de entrada do utilizador para um inquilino gerido vs. federado":::

## <a name="troubleshoot-sign-in-issues"></a>Problemas de resolução de problemas

Se um utilizador não agir no **registo de permanência assinado?**

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Diagrama mostrando o fluxo de entrada do utilizador para um inquilino gerido vs. federado" quando o utilizador estava a iniciar a sessão.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Diagrama mostrando o fluxo de entrada do utilizador para um inquilino gerido vs. federado":::

Pode impedir que os utilizadores vejam a interrupção definindo **a opção 'Mostrar' para permanecerem assinados na** definição de **Nº** nas definições avançadas de marca. Isto desativa a solicitação KMSI para todos os utilizadores do seu diretório AD Azure.

Também pode utilizar os controlos persistentes da sessão do navegador no acesso condicional para impedir que os utilizadores possam ver a solicitação KMSI. Esta opção permite desativar a solicitação KMSI para um grupo selecionado de utilizadores (como os administradores globais) sem afetar o comportamento de inscrição para os restantes utilizadores no diretório. Para obter mais informações, consulte [a frequência de inscrição do utilizador.](../conditional-access/howto-conditional-access-session-lifetime.md) 

Para garantir que o pedido KMSI só é mostrado quando pode beneficiar o utilizador, o pedido KMSI não é intencionalmente mostrado nos seguintes cenários:

* O utilizador é assinado através de SSO sem costura e autenticação integrada do Windows (IWA)
* O utilizador é assinado através dos Serviços da Federação de Diretórios Ativos e da IWA
* O utilizador é um hóspede no inquilino
* A pontuação de risco do utilizador é alta
* O s-in ocorre durante o fluxo de consentimento do utilizador ou administração
* O controlo persistente da sessão do navegador está configurado numa política de acesso condicional

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras definições que afetam o tempo limite da sessão de sessão de sessão:

* Microsoft 365 - [Intervalo de sessão inativo](/sharepoint/sign-out-inactive-users)
* Acesso Condicional Azure AD - [Frequência de inscrição do utilizador](../conditional-access/howto-conditional-access-session-lifetime.md)
* Portal Azure – [Tempo de inatividade ao nível do diretório](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)