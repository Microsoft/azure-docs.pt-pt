---
title: Configure o 'Stay signed in?' solicitação para contas Azure Ative Directory
description: Saiba mais sobre manter-me inscrito (KMSI), que mostra a Estadia assinada? pronta, como configurá-lo no portal Azure Ative Directory, e como resolver problemas de inscrição.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c4b253a3017f1a836ac989bb897065a6a7d8337
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84739953"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Configure o 'Stay signed in?' solicitação para contas AD AZure

Mantenha-me assinado em (KMSI) exibe um **Stay signed in?** Se um utilizador responder **Sim** a esta solicitação, o manter-me assinado em serviço dá-lhes um token persistente [de atualização](../develop/developer-glossary.md#refresh-token). Para os inquilinos federados, a solicitação será mostrada após o utilizador autenticar com sucesso o serviço de identidade federado.

O diagrama seguinte mostra o fluxo de entrada do utilizador para um inquilino gerido e inquilino federado e o novo manter-me assinado em sinal rápido. Este fluxo contém lógica inteligente para que a opção **Stay in?**

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagrama mostrando o fluxo de entrada do utilizador para um inquilino gerido vs. federado":::

> [!NOTE]
> Configurar a opção manter-me assinado requer que utilize o Azure Ative Directory (Azure AD) Premium 1, Premium 2 ou Edições Básicas, ou ter uma licença Office 365. Para obter mais informações sobre licenciamento e edições, consulte [Inscreva-se no Azure AD Premium.](active-directory-get-started-premium.md)<br><br>As edições Azure AD Premium e Basic estão disponíveis para clientes na China usando o exemplo mundial de Azure AD. As edições do Azure AD Premium e Básico não são atualmente suportadas pelo serviço do Azure operado pela 21Vianet na China. Para mais informações, fale connosco através do [Azure AD Forum](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="configure-kmsi"></a>Configurar KMSI

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.
1. Selecione **Azure Ative Directory**, selecione **Marca da Empresa**e, em seguida, selecione **Configure**.
1. Na secção **Definições Avançadas,** encontre **a opção 'Mostrar' para permanecer assinada na** definição.

   Esta definição permite-lhe escolher se os seus utilizadores permanecem inscritos no AZure AD até que eles assinem explicitamente.
   * Se escolher **Não,** a opção **Stay in?**
   * Se escolher **Sim,** a opção **Stay in?**

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Screenshot mostra a opção Show para permanecer assinado na definição":::

## <a name="troubleshoot-sign-in-issues"></a>Problemas de resolução de problemas

Se um utilizador não agir no **registo de permanência assinado?**

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Mostra a estadia assinada? rápido":::

Os detalhes sobre o erro de inscrição são os seguintes e realçados no exemplo.

* **Iniciar em código de erro:** 50140
* **Razão de avaria**: Este erro ocorreu devido à interrupção "Mantenha-me assinado" quando o utilizador estava a iniciar a sessão.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Exemplo de entrada de registo de login com o manter-me assinado em interrupção":::

Pode impedir que os utilizadores vejam a interrupção definindo **a opção 'Mostrar' para permanecerem assinados na** definição de **Nº** nas definições avançadas de marca.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras definições que afetam o tempo limite da sessão de sessão de sessão:

* Escritório 365 - [Tempo limite de sessão inativo](https://docs.microsoft.com/sharepoint/sign-out-inactive-users)
* Acesso Condicional Azure AD - [Frequência de inscrição do utilizador](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)
* Portal Azure – [Tempo de inatividade ao nível do diretório](https://docs.microsoft.com/azure/azure-portal/admin-timeout)
