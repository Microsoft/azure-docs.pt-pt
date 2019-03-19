---
title: Como requerer aplicações aprovadas do cliente de acesso à aplicação de cloud com o acesso condicional no Azure Active Directory | Documentos da Microsoft
description: Saiba como requerer aplicações aprovadas do cliente de acesso à aplicação de cloud com o acesso condicional no Azure Active Directory.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b7f540ccf80c6d25a7dfcfcad1ba7ca6698167
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113236"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: Exigir aplicações aprovadas do cliente para o acesso de aplicação de cloud com o acesso condicional 

Os seus funcionários utilizem os dispositivos móveis para tarefas pessoais e profissionais. Certificar-se de que seus funcionários são produtivo, também quer evitar perda de dados. Com o acesso condicional do Azure Active Directory (Azure AD), pode restringir o acesso às suas aplicações na cloud para aplicações aprovadas do cliente que podem proteger dados da sua empresa.  

Este tópico explica como configurar políticas de acesso de condição que necessitam de aplicações aprovadas do cliente.

## <a name="overview"></a>Descrição geral

Com o [acesso condicional do Azure AD](overview.md), pode ajustar como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, pode limitar o acesso às suas aplicações na cloud para dispositivos fidedignos.

Pode usar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger dados da sua empresa. Políticas de proteção de aplicações do Intune não necessitam de solução de gestão de dispositivos móveis (MDM), que permite-lhe proteger os dados da sua empresa com ou sem inscrição de dispositivos numa solução de gestão de dispositivos.

Ativa de acesso condicional do Azure Active Directory limitar o acesso às suas aplicações na cloud para aplicações de cliente que suportam políticas de proteção de aplicações do Intune. Por exemplo, pode restringir o acesso ao Exchange Online para a aplicação Outlook.

Na terminologia de acesso condicional, estas aplicações de cliente são conhecidas como **aplicações de cliente aprovadas**.  


![Acesso condicional](./media/app-based-conditional-access/05.png)


Para obter uma lista de aplicações aprovadas do cliente, consulte [aprovado requisito de aplicação de cliente](technical-reference.md#approved-client-app-requirement).


Pode combinar políticas de acesso condicional com base na aplicação com outras políticas, como [políticas de acesso condicional com base no dispositivo](require-managed-devices.md) para fornecer flexibilidade em como proteger os dados para dispositivos pessoais e empresariais.

 


## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que está familiarizado com:

- O [aprovado requisito de aplicação de cliente](technical-reference.md#approved-client-app-requirement) referência técnica.


- Os conceitos básicos do [acesso condicional no Azure Active Directory](overview.md).

- Como [configurar uma política de acesso condicional](app-based-mfa.md).

- O [migração de políticas de acesso condicional](best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional com base na aplicação, tem de ter um Enterprise Mobility + Security ou uma subscrição do Azure Active Directory premium e os utilizadores têm de estar licenciados para o EMS ou do Azure AD. 


## <a name="exchange-online-policy"></a>Política do Exchange Online 

Este cenário é constituído por uma política de acesso condicional com base na aplicação para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:

- Configura o e-mail através de uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange

- Recebe um e-mail a indicar que o acesso apenas está disponível através da aplicação Outlook

- Downloads do aplicativo com a ligação

- Abre a aplicação do Outlook e inicia sessão com as credenciais do Azure AD

- É-lhe pedido que instale o autenticador (iOS) ou do Portal da empresa (Android), para continuar

- Instala a aplicação e pode regressar à aplicação Outlook para continuar

- É-lhe pedido para registar um dispositivo

- É capaz de aceder ao e-mail

Quaisquer políticas de proteção de aplicações do Intune são ativados no momento, os dados empresariais de acesso e pode solicitar ao utilizador para reiniciar a aplicação, utilize um adicionais etc. da PIN (se configurada para a plataforma e aplicação).

### <a name="configuration"></a>Configuração 

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/01.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**:

    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e aplicações de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter **exigir aplicação aprovada do cliente (pré-visualização)** selecionado.

    ![Acesso condicional](./media/app-based-conditional-access/05.png)
 

**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.


3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**.

    ![Acesso condicional](./media/app-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente (pré-visualização)**. 

    a. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-based-conditional-access/92.png)

    b. Como **controlos de acesso**, tem de ter **exigir aplicação aprovada do cliente (pré-visualização)** selecionado.

    ![Acesso condicional](./media/app-based-conditional-access/05.png)


**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e SharePoint Online

Este cenário é constituído por um acesso condicional com a política de gestão de aplicações móveis para acesso ao Exchange Online e SharePoint Online com aplicações aprovadas.

### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:

- Tenta utilizar a aplicação do SharePoint para ligar e também para ver os respetivos sites empresariais

- Tentativa de início de sessão com as mesmas credenciais que as credenciais de aplicação do Outlook

- Não tem de voltar a registar e pode obter acesso aos recursos


### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online e SharePoint Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/71.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.


3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 

    ![Acesso condicional](./media/app-based-conditional-access/02.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**:

    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter **exigir aplicação aprovada do cliente (pré-visualização)** selecionado.

    ![Acesso condicional](./media/app-based-conditional-access/05.png)




**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. Online 

    ![Acesso condicional](./media/app-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente**:

    a. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-based-conditional-access/92.png)

    b. Como **controlos de acesso**, tem de ter **exigir aplicação aprovada do cliente (pré-visualização)** selecionado.

    ![Acesso condicional](./media/app-based-conditional-access/05.png)




**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo em conformidade com base na aplicação ou para o Exchange Online e SharePoint Online

Este cenário é constituído por uma política de acesso condicional de dispositivo com base na aplicação ou em conformidade para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que:
 
- Alguns utilizadores já inscritos (com ou sem dispositivos da empresa)

- Os utilizadores que não estão inscritos e registados no Azure AD com uma aplicação protegida a necessidade de aplicação para registar um dispositivo para aceder aos recursos

- Os utilizadores inscritos utilizando a aplicação de aplicação protegida não é preciso voltar a registar o dispositivo


### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online e SharePoint Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/62.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/app-based-conditional-access/02.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir aplicação aprovada do cliente (pré-visualização)**

   - **Exigir um dos controlos selecionados**   
 
     ![Acesso condicional](./media/app-based-conditional-access/11.png)



**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/61.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente**. 

    Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter **exigir aplicação aprovada do cliente (pré-visualização)** selecionado.
 
    ![Acesso condicional](./media/app-based-conditional-access/11.png)




**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível e com base na aplicação para o Exchange Online e SharePoint Online

Este cenário é constituído por uma política de acesso condicional de dispositivo com base na aplicação e em conformidade para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Playbook de cenário

Este cenário pressupõe que um utilizador:
 
-   Configura o e-mail através de uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange
-   Recebe um e-mail a indicar que acesso exige que o dispositivo seja inscrito
-   Downloads do portal da empresa e iniciar sessão no portal da empresa
-   Verifica o correio e é-lhe pedido para utilizar a aplicação Outlook
-   Transfere o Outlook
-   É aberto o Outlook e insere as credenciais utilizadas na inscrição
-   Usuário é capaz de aceder ao e-mail

Quaisquer políticas de proteção de aplicações do Intune são ativadas no momento do acesso aos dados empresariais e podem solicitar ao utilizador para reiniciar a aplicação, utilize um adicionais etc. da PIN (se configurada para a plataforma e aplicação)


### <a name="configuration"></a>Configuração

**Passo 1 - configurar uma política de acesso condicional do Azure AD para o Exchange Online e SharePoint Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/62.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online** e **Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/app-based-conditional-access/02.png)

4. **Condições:** Como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/app-based-conditional-access/03.png)

    b. Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e aplicações de ambiente de trabalho** e **clientes de autenticação moderna**.

    ![Acesso condicional](./media/app-based-conditional-access/91.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir aplicação aprovada do cliente (pré-visualização)**

   - **Exigir todos os controlos selecionados**   
 
     ![Acesso condicional](./media/app-based-conditional-access/13.png)



**Passo 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/app-based-conditional-access/61.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: Cada política de acesso condicional tem de ter pelo menos um utilizador ou grupo selecionado.

3. **Aplicações na cloud:** Como as aplicações na cloud, tem de selecionar **Office 365 Exchange Online**. 

    ![Acesso condicional](./media/app-based-conditional-access/07.png)

4. **Condições:** Como **condições**, terá de configurar **aplicações de cliente (pré-visualização)**. 

    Como **aplicações de cliente (pré-visualização)**, selecione **aplicações móveis e clientes de ambiente de trabalho** e **clientes do Exchange ActiveSync**.

    ![Acesso condicional](./media/app-based-conditional-access/92.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

   - **Exigir dispositivo seja marcado como compatível**

   - **Exigir aplicação aprovada do cliente (pré-visualização)**

   - **Exigir todos os controlos selecionados**   
 
     ![Acesso condicional](./media/app-based-conditional-access/64.png)




**Passo 3 - configurar a política de proteção de aplicações do Intune para iOS e os aplicativos de cliente Android**


![Acesso condicional](./media/app-based-conditional-access/09.png)

Ver [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.






## <a name="next-steps"></a>Passos Seguintes

Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja [Best practices for conditional access in Azure Active Directory](best-practices.md) (Melhores práticas do acesso condicional no Azure Active Directory). 
