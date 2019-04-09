---
title: Como requerer a política de proteção de acesso à aplicação de cloud com o acesso condicional no Azure Active Directory | Documentos da Microsoft
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
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288505"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Como: Exigir a política de proteção de acesso à aplicação de cloud com o acesso condicional (pré-visualização)

Os seus funcionários utilizem os dispositivos móveis para tarefas pessoais e profissionais. Certificar-se de que seus funcionários são produtivo, também quer evitar perda de dados. Com o acesso condicional do Azure Active Directory (Azure AD), pode proteger os seus dados empresariais ao restringir o acesso às suas aplicações na cloud para aplicações de cliente com uma política de proteção de aplicações pela primeira vez.

Este tópico explica como configurar políticas de acesso condicional que podem exigir a política de proteção de aplicações antes do acesso aos dados.

## <a name="overview"></a>Descrição geral

Com o [acesso condicional do Azure AD](overview.md), pode ajustar como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, pode limitar o acesso às suas aplicações na cloud para dispositivos fidedignos.

Pode usar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger dados da sua empresa. Políticas de proteção de aplicações do Intune não necessitam de solução de gestão de dispositivos móveis (MDM), que permite-lhe proteger os dados da sua empresa com ou sem inscrição de dispositivos numa solução de gestão de dispositivos.

Acesso condicional do Azure Active Directory restringe o acesso às suas aplicações na cloud para aplicações de cliente Intune comunicou com o Azure AD como receber uma política de proteção de aplicações. Por exemplo, pode restringir o acesso ao Exchange Online para o Outlook que tem uma política de proteção de aplicações do Intune.

Na terminologia de acesso condicional, estas aplicações de cliente são conhecidas por ser política protegida com uma **política de proteção de aplicações**.  

![Acesso condicional](./media/app-protection-based-conditional-access/05.png)

Para obter uma lista da política de aplicações de cliente protegido, consulte [requisito de política de proteção de aplicações](technical-reference.md#approved-client-app-requirement).

Pode combinar políticas de acesso condicional com base na proteção de aplicações com outras políticas, como [políticas de acesso condicional com base no dispositivo](require-managed-devices.md) para fornecer flexibilidade em como proteger os dados para dispositivos pessoais e empresariais.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Benefícios do requisito de acesso condicional com base em proteção de aplicações

Semelhante a conformidade está a ser comunicado pelo Intune para iOS e Android para o dispositivo gerido, Intune agora relatórios para o Azure AD Se aplicar a política de proteção de aplicações para que o acesso condicional, pode utilizá-lo como uma verificação de acesso. Esta nova política de acesso condicional **política de proteção de aplicações** aumenta a segurança protegendo contra erros de administrador, tais como:

- utilizadores que não têm uma licença do Intune
- utilizadores que não podem receber uma política de proteção de aplicações do Intune
- Política do Intune app protection aplicações que não tenha sido configuradas para receber uma política


## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que está familiarizado com:

- O [requisito de política de proteção de aplicações](technical-reference.md#app-protection-policy-requirement) referência técnica.

- O [aprovado requisito de aplicação de cliente](technical-reference.md#approved-client-app-requirement) referência técnica.

- Os conceitos básicos do [acesso condicional no Azure Active Directory](overview.md).

- Como [configurar uma política de acesso condicional](app-based-mfa.md).


## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional com base em proteção de aplicações, tem de
- Tem um Enterprise Mobility + Security ou uma subscrição do Azure Active Directory premium + Intune
- Certifique-se de que os utilizadores estão licenciados para o EMS ou do Azure AD + Intune
- Certifique-se de que a aplicação de cliente foi configurada no Intune para receber uma política de proteção de aplicações
- Certifique-se de que os utilizadores estão configurados no Intune para receber uma política de proteção de aplicações do Intune

## <a name="app-protection-based-policy-for-exchange-online"></a>Política de proteção com base em aplicações para o Exchange Online

Este cenário é constituído por uma política de acesso condicional com base em proteção de aplicações para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:

- Configura o e-mail através de uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange

- Recebe um e-mail a indicar que o acesso apenas está disponível através da aplicação Outlook

- Downloads do aplicativo com a ligação

- Abre a aplicação do Outlook e inicia sessão com as credenciais do Azure AD

- É-lhe pedido que instale o autenticador (iOS) ou do Portal da empresa (Android), para continuar

- Instala a aplicação e pode regressar à aplicação Outlook para continuar

- É-lhe pedido para registar um dispositivo

- É capaz de receber uma política de proteção de aplicações do Intune

- É capaz de aceder ao e-mail

Quaisquer políticas de proteção de aplicações do Intune tem de estar num aplicativo para aceder aos dados da empresa e podem pedir ao utilizador para reiniciar a aplicação, utilize um adicionais etc. da PIN (se configurada para a plataforma e aplicação).

### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**:

    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e aplicações de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter **necessitam de política de proteção de aplicações (pré-visualização)** selecionado.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)
 

**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.


3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente (pré-visualização)**. 

    a. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

    b. Como **controlos de acesso**, tem de ter **necessitam de política de proteção de aplicações (pré-visualização)** selecionado.

    ![Acesso condicional](./media/app-protection-based-conditional-access/05.png)


**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Política de dispositivo em conformidade ou com base em proteção de aplicações para o Exchange Online

Este cenário é constituído por uma política de acesso condicional de dispositivo em conformidade ou com base em proteção de aplicações para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que:
 
- Um utilizador já está inscrito (com ou sem dispositivos da empresa)

- Os utilizadores que não estão inscritos e registados no Azure AD com uma aplicação protegida a necessidade de aplicação para registar um dispositivo para aceder aos recursos

- Os utilizadores inscritos utilizando a aplicação de aplicação protegida não é preciso voltar a registar o dispositivo

- Utilizador pode receber uma política de proteção de aplicações do Intune se não estiver inscrito

- Utilizador possa aceder ao e-mail com o Outlook e uma política de proteção de aplicações do Intune se não estiver inscrito

- Utilizador pode aceder ao e-mail com o Outlook se o dispositivo é inscrito


### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Pedir que o dispositivo seja marcado como compatível**

   - **Exigir política de proteção de aplicação (pré-visualização)**

   - **Exigir um dos controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente**. 

    Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Pedir que o dispositivo seja marcado como compatível**

   - **Exigir política de proteção de aplicação (pré-visualização)**

   - **Exigir um dos controlos selecionados**   
    ![Acesso condicional](./media/app-protection-based-conditional-access/11.png)



**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Política de dispositivo compatível e com base em proteção de aplicações para o Exchange Online

Este cenário é constituído por uma política de acesso condicional de dispositivo em conformidade e proteção-baseado na aplicação para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:
 
-   Configura o e-mail através de uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange
-   Recebe um e-mail a indicar que acesso exige que o dispositivo seja inscrito
-   Downloads do portal da empresa e iniciar sessão no portal da empresa
-   Verifica o correio e é-lhe pedido para utilizar a aplicação Outlook
-   Transfere o Outlook
-   É aberto o Outlook e insere as credenciais utilizadas na inscrição
-   É capaz de receber para receber uma política de proteção de aplicações do Intune
-   É capaz de aceder ao e-mail com o Outlook e uma política de proteção de aplicações do Intune

Quaisquer políticas de proteção de aplicações do Intune estão ativadas antes de acesso para os dados da empresa e podem pedir ao utilizador para reiniciar a aplicação, utilize um adicionais etc. da PIN (se configurada para a plataforma e aplicação)


### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/01.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e aplicações de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Pedir que o dispositivo seja marcado como compatível**

   - **Exigir política de proteção de aplicação (pré-visualização)**

   - **Exigir todos os controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)



**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-protection-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente (pré-visualização)**. 

    Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/92.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Pedir que o dispositivo seja marcado como compatível**

   - **Exigir aplicação aprovada do cliente (pré-visualização)**

   - **Exigir todos os controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/13.png)




**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Política de proteção com base no ou com base na aplicação de aplicações para Exchange Online e SharePoint Online

Este cenário é constituído por uma política de aplicações aprovadas ou com base em proteção de aplicações para acesso ao Exchange Online e SharePoint Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:

- Configura a qualquer uma das aplicações de cliente que são a lista de aplicações que suportam o requisito de política de proteção de aplicação ou o requisito de aplicações aprovadas.  
- Utilizador utiliza as aplicações cliente que cumprem o requisito de política de proteção de aplicação podem receber uma política de proteção de aplicações do Intune
- Utilizador utiliza aplicações de cliente que cumpram o requisito de política de aplicações aprovadas que suporta a política de proteção de aplicações do Intune
- Abre a aplicação para aceder ao e-mail ou documentos
- Abre a aplicação do Outlook e inicia sessão com as credenciais do Azure AD
- É-lhe pedido que instale o autenticador (iOS) ou do Portal da empresa (Android), para continuar (se não estiver instalado)
- Instala a aplicação e pode regressar à aplicação Outlook para continuar
- É-lhe pedido para registar um dispositivo
- É capaz de receber uma política de proteção de aplicações do Intune
- É capaz de aceder ao e-mail com o Outlook e uma política de proteção de aplicações do Intune
- É possível aceder a sites/documentos com uma aplicação não sobre o requisito de política de proteção de aplicações mas listados os requisitos de aplicação aprovada.

Quaisquer políticas de proteção de aplicações do Intune são necessárias antes de acesso para os dados da empresa e podem pedir ao utilizador para reiniciar a aplicação, utilize um adicionais etc. da PIN (se configurada para a plataforma e aplicação)

**Observações**

- Pode utilizar este cenário se pretende suportar ambas as políticas de acesso condicional com base na aplicação e em proteção de aplicações.

- Nesta *ou* política, aplicações com **política de proteção de aplicações** requisito são avaliados para acesso ao primeiro antes **aplicações de cliente aprovadas** requisito.

### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-protection-based-conditional-access/62.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

     ![Acesso condicional](./media/app-protection-based-conditional-access/02.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e aplicações de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-protection-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Requer aplicação aprovada do cliente**

   - **Exigir política de proteção de aplicação (pré-visualização)**

   - **Exigir um dos controlos selecionados**   
 
     ![Acesso condicional](./media/app-protection-based-conditional-access/12.png)


**Passo 2 – configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-protection-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.




## <a name="next-steps"></a>Passos Seguintes

Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja [Best practices for conditional access in Azure Active Directory](best-practices.md) (Melhores práticas do acesso condicional no Azure Active Directory). 