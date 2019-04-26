---
title: Exigir a política de proteção de acesso à aplicação de cloud com o acesso condicional no Azure Active Directory | Documentos da Microsoft
description: Saiba como requerer a política de proteção de acesso à aplicação de cloud com o acesso condicional no Azure Active Directory.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60356074"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Exigir a política de proteção de acesso à aplicação de cloud com o acesso condicional (pré-visualização)

Os seus funcionários utilizem os dispositivos móveis para tarefas pessoais e profissionais. Certificar-se de que seus funcionários são produtivo, também quer evitar perda de dados. Com o acesso condicional do Azure Active Directory (Azure AD), pode proteger os seus dados empresariais ao restringir o acesso às suas aplicações na cloud. Utilize aplicações de cliente com uma política de proteção de aplicações pela primeira vez.

Este artigo explica como configurar políticas de acesso condicional que podem necessitar de uma política de proteção de aplicações antes do acesso é concedido aos dados.

## <a name="overview"></a>Descrição geral

Com o [acesso condicional do Azure AD](overview.md), pode ajustar como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, pode limitar o acesso às suas aplicações na cloud para dispositivos fidedignos.

Pode usar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger dados da sua empresa. Políticas de proteção de aplicações do Intune não necessitam de uma solução de gestão (MDM) de dispositivos móveis. Pode proteger os dados da sua empresa com ou sem inscrição de dispositivos numa solução de gestão de dispositivos.

Acesso condicional do Azure Active Directory restringe o acesso às suas aplicações na cloud para aplicações de cliente Intune comunicou com o Azure AD como receber uma política de proteção de aplicações. Por exemplo, pode restringir o acesso ao Exchange Online para o Outlook que tem uma política de proteção de aplicações do Intune.

Na terminologia de acesso condicional, estas aplicações de cliente são conhecidas por ser política protegida com uma *política de proteção de aplicações*.  

![Acesso condicional](./media/app-protection-based-conditional-access/05.png)

Para obter uma lista de aplicações protegidas de política de cliente, consulte [requisito de política de proteção de aplicações](technical-reference.md#approved-client-app-requirement).

Pode combinar políticas de acesso condicional com base na proteção de aplicações com outras políticas, como [políticas de acesso condicional com base no dispositivo](require-managed-devices.md). Dessa forma, pode fornecer flexibilidade em como proteger os dados para dispositivos pessoais e empresariais.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Benefícios do requisito de acesso condicional com base em proteção de aplicações

Semelhante a conformidade é comunicada pelo Intune para iOS e Android para um dispositivo gerido, o Intune agora relatórios para o Azure AD, se uma política de proteção de aplicações é aplicada. Acesso condicional pode utilizar esta política como uma verificação de acesso. Esta nova política de acesso condicional, a política de proteção de aplicações, aumenta a segurança. Ele protege contra erros de administrador, tais como:

- Utilizadores que não têm uma licença do Intune.
- Utilizadores que não podem receber uma política de proteção de aplicações do Intune.
- Política do Intune app protection aplicações que não está configuradas para receber uma política.


## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que esteja familiarizado com:

- O [requisito de política de proteção de aplicações](technical-reference.md#app-protection-policy-requirement) referência técnica.
- O [aprovado requisito de aplicação de cliente](technical-reference.md#approved-client-app-requirement) referência técnica.
- Os conceitos básicos do [acesso condicional no Azure Active Directory](overview.md).
- Como [configurar uma política de acesso condicional](app-based-mfa.md).


## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional com base em proteção de aplicações, tem de:

- Tem um Enterprise Mobility + Security ou uma subscrição do Azure Active Directory premium + Intune.
- Certifique-se de que os utilizadores estão licenciados para o Enterprise Mobility + Security ou do Azure AD + Intune.
- Certifique-se de que a aplicação de cliente está configurada no Intune para receber uma política de proteção de aplicações.
- Certifique-se de que os utilizadores estão configurados no Intune para receber uma política de proteção de aplicações do Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Política de proteção com base em aplicações para o Exchange Online

Este cenário é constituído por uma política de acesso condicional com base em proteção de aplicações para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:

- Configura o e-mail ao utilizar uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange.
- Recebe um e-mail a indicar que o acesso está disponível apenas através da aplicação Outlook.
- Transfere o aplicativo com a ligação.
- Abre a aplicação do Outlook e inicia sessão com credenciais do Azure AD.
- É-lhe pedido para instalar o Microsoft Authenticator para iOS utilização ou o Portal da empresa do Intune para Android utilizam continuar.
- Instala o aplicativo e retorna para a aplicação Outlook para continuar.
- É-lhe pedido para registar um dispositivo.
- Pode receber uma política de proteção de aplicações do Intune.
- Pode aceder ao e-mail.

Quaisquer políticas de proteção de aplicações do Intune tem de estar num aplicativo para aceder a dados empresariais. As políticas podem pedir ao utilizador para reiniciar a aplicação ou utilizar um PIN adicional. Esse é o caso, se as políticas estão configuradas para a plataforma e aplicativo.

### <a name="configuration"></a>Configuração

**Passo 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.

3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **plataformas de dispositivos** e **aplicações de cliente (pré-visualização)**:

    a. Na **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controlos de acesso**, selecione **necessitam de política de proteção de aplicações (pré-visualização)**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)
 

**Passo 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync (EAS)**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.


3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **aplicações de cliente (pré-visualização)**. 

    a. Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

    b. Sob **controlos de acesso**, selecione **necessitam de política de proteção de aplicações (pré-visualização)**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)


**Passo 3: Configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Política de dispositivo em conformidade ou com base em proteção de aplicações para o Exchange Online

Este cenário é constituído por uma política de acesso condicional de dispositivo em conformidade ou com base em proteção de aplicações para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que:
 
- Um utilizador já estiver inscrito, com ou sem dispositivos da empresa.
- Os utilizadores que não estão inscritos e registados com o Azure AD com uma aplicação protegida necessidade de aplicação para registar um dispositivo para aceder aos recursos.
- Os utilizadores inscritos que usam o aplicativo de aplicação protegida não é preciso voltar a registar o dispositivo.
- O utilizador pode receber uma política de proteção de aplicações do Intune se não estiver inscrito.
- O utilizador possa aceder ao e-mail com o Outlook e uma política de proteção de aplicações do Intune se não estiver inscrito.
- O utilizador pode aceder ao e-mail com o Outlook, se o dispositivo é inscrito.


### <a name="configuration"></a>Configuração

**Passo 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.

3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **plataformas de dispositivos** e **aplicações de cliente (pré-visualização)**. 
 
    a. Na **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controlos de acesso**, selecione as seguintes opções:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir a política de proteção de aplicações (pré-visualização)**

   - **Exigir um dos controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Passo 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.

3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **aplicações de cliente (pré-visualização)**. 

    Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Sob **controlos de acesso**, selecione as seguintes opções:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir a política de proteção de aplicações (pré-visualização)**

   - **Exigir um dos controlos selecionados**

     ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Passo 3: Configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível e com base em proteção de aplicações para o Exchange Online

Este cenário é constituído por uma política de acesso condicional de dispositivo em conformidade e proteção-baseado na aplicação para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:
 
-   Configura o e-mail ao utilizar uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange.
-   Recebe uma mensagem de e-mail que indica que acesso necessita que os respetivos dispositivos estejam inscritos.
-   Downloads do Portal da empresa do Intune e iniciar sessão no portal.
-   Verifica o correio e é-lhe pedido para utilizar a aplicação Outlook.
-   Transfere o Outlook.
-   É aberto o Outlook e insere as credenciais utilizadas na inscrição.
-   Pode receber uma política de proteção de aplicações do Intune.
-   Aceder ao e-mail com o Outlook e uma política de proteção de aplicações do Intune.

Quaisquer políticas de proteção de aplicações do Intune são ativadas antes do acesso é concedido aos dados da empresa. As políticas podem pedir ao utilizador para reiniciar a aplicação ou utilizar um PIN adicional. Esse é o caso, se as políticas estão configuradas para a plataforma e aplicativo.


### <a name="configuration"></a>Configuração

**Passo 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.

3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **plataformas de dispositivos** e **aplicações de cliente (pré-visualização)**. 
 
    a. Na **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controlos de acesso**, selecione as seguintes opções:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir a política de proteção de aplicações (pré-visualização)**

   - **Exigir todos os controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)



**Passo 2: Configurar uma política de acesso condicional do Azure AD para o Exchange Online com o ActiveSync**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.

3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. Na **condições**, configure **aplicações de cliente (pré-visualização)**. 

    Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Sob **controlos de acesso**, selecione as seguintes opções:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir a política de proteção de aplicações (pré-visualização)**

   - **Exigir todos os controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)




**Passo 3: Configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Política de proteção com base no ou com base na aplicação de aplicações para Exchange Online e SharePoint Online

Este cenário é constituído por uma política de aplicações aprovadas ou com base em proteção de aplicações para acesso ao Exchange Online e SharePoint Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:

- Configura as aplicações cliente que são a lista de aplicações que suportam o requisito de política de proteção de aplicação ou o requisito de aplicações aprovadas.  
- Utiliza aplicações de cliente que cumprem o requisito de política de proteção de aplicações e podem receber uma política de proteção de aplicações do Intune.
- Utiliza aplicações de cliente que cumpram o requisito de política de aplicações aprovadas que suporta a política de proteção de aplicações do Intune.
- Abre a aplicação para aceder ao e-mail ou documentos.
- Abre a aplicação do Outlook e inicia sessão com credenciais do Azure AD.
- É-lhe pedido para instalar o Microsoft Authenticator para iOS utilização ou o Portal da empresa do Intune para Android utilizam se eles ainda não estiverem instalados.
- Instala a aplicação e pode regressar à aplicação Outlook para continuar.
- É-lhe pedido para registar um dispositivo.
- Pode receber uma política de proteção de aplicações do Intune.
- Aceder ao e-mail com o Outlook e uma política de proteção de aplicações do Intune.
- Pode aceder a sites e documentos com uma aplicação não sobre o requisito de política de proteção de aplicações, mas indicado o requisito de aplicação aprovada.

Quaisquer políticas de proteção de aplicações do Intune são necessárias antes do acesso é concedido aos dados da empresa. As políticas podem pedir ao utilizador para reiniciar a aplicação ou utilizar um PIN adicional. Esse é o caso, se as políticas estão configuradas para a plataforma e aplicativo.

**Observações**

- Pode utilizar este cenário se pretende suportar ambas as políticas de acesso condicional com base na aplicação e em proteção de aplicações.
- Nesta *ou* políticas, aplicações com um requisito de política de proteção de aplicações são avaliados para acesso ao primeiro antes do requisito de aplicações aprovadas do cliente.

### <a name="configuration"></a>Configuração

**Passo 1: Configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, configure os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. Introduza o nome da sua política de acesso condicional.

2. Sob **atribuições**, na **utilizadores e grupos**, selecione pelo menos um utilizador ou grupo para cada política de acesso condicional.

3. Na **aplicações na Cloud**, selecione **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/02.png)

4. Na **condições**, configure **plataformas de dispositivos** e **aplicações de cliente (pré-visualização)**. 
 
    a. Na **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Na **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **controlos de acesso**, selecione as seguintes opções:

   - **Exigir aplicação aprovada do cliente**

   - **Exigir a política de proteção de aplicações (pré-visualização)**

   - **Exigir um dos controlos selecionados**
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/12.png)


**Passo 2: Configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Para obter mais informações, consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Passos Seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).
- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md). 