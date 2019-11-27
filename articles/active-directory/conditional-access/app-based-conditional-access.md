---
title: Aplicativos cliente aprovados com acesso condicional-Azure Active Directory
description: Saiba como exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381127"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: exigir aplicativos cliente aprovados para acesso ao aplicativo de nuvem com acesso condicional 

Seus funcionários usam dispositivos móveis para tarefas pessoais e de trabalho. Ao garantir que seus funcionários possam ser produtivos, você também deseja evitar a perda de dados. Com o acesso condicional do Azure Active Directory (AD do Azure), você pode restringir o acesso aos aplicativos de nuvem a aplicativos cliente aprovados que podem proteger seus dados corporativos.  

Este tópico explica como configurar políticas de acesso de condição que exigem aplicativos cliente aprovados.

## <a name="overview"></a>Descrição geral

Com o [acesso condicional do Azure ad](overview.md), você pode ajustar como os usuários autorizados podem acessar seus recursos. Por exemplo, você pode limitar o acesso aos seus aplicativos de nuvem a dispositivos confiáveis.

Você pode usar [as políticas de proteção de aplicativo do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa. As políticas de proteção de aplicativo do Intune não exigem a solução de MDM (gerenciamento de dispositivo móvel), que permite que você proteja os dados da sua empresa com ou sem registrar dispositivos em uma solução de gerenciamento de dispositivo.

Azure Active Directory acesso condicional permite que você limite o acesso aos aplicativos de nuvem para aplicativos cliente que dão suporte a políticas de proteção de aplicativo do Intune. Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo Outlook.

Na terminologia de acesso condicional, esses aplicativos cliente são conhecidos como **aplicativos cliente aprovados**.  

![Acesso Condicional](./media/app-based-conditional-access/05.png)

Para obter uma lista de aplicativos cliente aprovados, consulte [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).

Você pode combinar políticas de acesso condicional com base no aplicativo com outras políticas, como [políticas de acesso condicional com base no dispositivo](require-managed-devices.md) , para fornecer flexibilidade na proteção de dados para dispositivos pessoais e corporativos.

## <a name="before-you-begin"></a>Antes de começar

Este tópico pressupõe que você esteja familiarizado com:

- A referência técnica do [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement) .
- Os conceitos básicos de [acesso condicional no Azure Active Directory](overview.md).
- Como [Configurar uma política de acesso condicional](app-based-mfa.md).
- A [migração de políticas de acesso condicional](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional com base no aplicativo, você deve ter uma assinatura Enterprise Mobility + Security ou Azure Active Directory Premium e os usuários devem ser licenciados para o EMS ou o Azure AD. 

## <a name="exchange-online-policy"></a>Política do Exchange Online 

Esse cenário consiste em uma política de acesso condicional baseada em aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico de cenário

Este cenário pressupõe que um usuário:

- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
- Recebe um email que indica que o acesso está disponível apenas usando o aplicativo do Outlook
- Baixa o aplicativo com o link
- Abre o aplicativo do Outlook e entra com as credenciais do Azure AD
- É solicitado a instalar o autenticador (iOS) ou o Portal da Empresa (Android) para continuar
- Instala o aplicativo e pode retornar ao aplicativo Outlook para continuar
- É solicitado a registrar um dispositivo
- É capaz de acessar o email

Todas as políticas de proteção de aplicativo do Intune são ativadas no momento em que os dados corporativos do Access e podem solicitar que o usuário reinicie o aplicativo, use um PIN adicional, etc. (se configurado para o aplicativo e a plataforma).

### <a name="configuration"></a>Configuração 

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.
1. **Condições:** Como **condições**, você precisa configurar **plataformas de dispositivo** e **aplicativos cliente**:
   1. Como **plataformas de dispositivo**, selecione **Android** e **Ios**.
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e aplicativos de área de trabalho** e clientes de **autenticação moderna**.
1. Como **controles de acesso**, você precisa ter o **aplicativo cliente aprovado (versão prévia)** selecionado.

   ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**.
1. **Condições:** Como **condições**, você precisa configurar **aplicativos cliente (versão prévia)** . 
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.
   1. Como **controles de acesso**, você precisa ter o **aplicativo cliente aprovado (versão prévia)** selecionado.

      ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

Consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.

## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e do SharePoint Online

Esse cenário consiste em um acesso condicional com a política de gerenciamento de aplicativo móvel para acesso ao Exchange Online e ao SharePoint Online com aplicativos aprovados.

### <a name="scenario-playbook"></a>Guia estratégico de cenário

Este cenário pressupõe que um usuário:

- Tenta usar o aplicativo do SharePoint para se conectar e também para exibir seus sites corporativos
- Tentativas de entrar com as mesmas credenciais que as credenciais do aplicativo Outlook
- Não precisa registrar novamente e pode obter acesso aos recursos

### <a name="configuration"></a>Configuração

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** Como **condições**, você precisa configurar **plataformas de dispositivo** e **aplicativos cliente**:
   1. Como **plataformas de dispositivo**, selecione **Android** e **Ios**.
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes de **autenticação modernos**.
1. Como **controles de acesso**, você precisa ter o **aplicativo cliente aprovado (versão prévia)** selecionado.

   ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. Online 
1. **Condições:** Como **condições**, você precisa configurar **aplicativos cliente**:
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.
   1. Como **controles de acesso**, você precisa ter o **aplicativo cliente aprovado (versão prévia)** selecionado.

      ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível ou baseada em aplicativo para o Exchange Online e o SharePoint Online

Esse cenário consiste em uma política de acesso condicional de dispositivo compatível ou baseada em aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico de cenário

Este cenário pressupõe que:
 
- Alguns usuários já estão registrados (com ou sem dispositivos corporativos)
- Os usuários que não estão inscritos e registrados com o Azure AD usando um aplicativo protegido por aplicativo precisam registrar um dispositivo para acessar recursos
- Os usuários registrados usando o aplicativo protegido do aplicativo não precisam registrar o dispositivo novamente

### <a name="configuration"></a>Configuração

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** Como **condições**, você precisa configurar **plataformas de dispositivo** e **aplicativos cliente**. 
   1. Como **plataformas de dispositivo**, selecione **Android** e **Ios**.
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes de **autenticação modernos**.
1. Como **controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir aplicativo cliente aprovado (versão prévia)**
   - **Exigir um dos controles selecionados**   
 
      ![Acesso Condicional](./media/app-based-conditional-access/11.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 
1. **Condições:** Como **condições**, você precisa configurar **aplicativos cliente**. 
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.
1. Como **controles de acesso**, você precisa ter o **aplicativo cliente aprovado (versão prévia)** selecionado.
 
   ![Acesso Condicional](./media/app-based-conditional-access/11.png)

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivo compatível e baseada em aplicativo para o Exchange Online e o SharePoint Online

Esse cenário consiste em uma política de acesso condicional de dispositivo compatível e com base no aplicativo para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Guia estratégico de cenário

Este cenário pressupõe que um usuário:
 
- Configura o email usando um aplicativo de email nativo no iOS ou Android para se conectar ao Exchange
- Recebe um email que indica que o acesso exige que o dispositivo seja registrado
- Baixa o portal da empresa e entra no portal da empresa
- Verifica o email e é solicitado a usar o aplicativo Outlook
- Baixa o aplicativo Outlook
- Abre o aplicativo Outlook e insere as credenciais usadas no registro
- O usuário é capaz de acessar o email

Todas as políticas de proteção de aplicativo do Intune são ativadas no momento do acesso aos dados corporativos e podem solicitar que o usuário reinicie o aplicativo, use um PIN adicional, etc. (se configurado para o aplicativo e a plataforma)

### <a name="configuration"></a>Configuração

**Etapa 1-configurar uma política de acesso condicional do Azure AD para o Exchange Online e o SharePoint Online**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **office 365 Exchange Online** e o **Office 365 SharePoint Online**. 
1. **Condições:** Como **condições**, você precisa configurar **plataformas de dispositivo** e **aplicativos cliente**. 
   1. Como **plataformas de dispositivo**, selecione **Android** e **Ios**.
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e aplicativos de área de trabalho** e clientes de **autenticação moderna**.
1. Como **controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir aplicativo cliente aprovado (versão prévia)**
   - **Exigir todos os controles selecionados**   
 
      ![Acesso Condicional](./media/app-based-conditional-access/13.png)

**Etapa 2 – configurar uma política de acesso condicional do Azure AD para o Exchange Online com o EAS (Active Sync)**

Para a política de acesso condicional nesta etapa, você precisa configurar os seguintes componentes:

1. O **nome** da política de acesso condicional.
1. **Usuários e grupos**: cada política de acesso condicional deve ter pelo menos um usuário ou grupo selecionado.
1. **Aplicativos de nuvem:** Como aplicativos de nuvem, você precisa selecionar o **Office 365 Exchange Online**. 
1. **Condições:** Como **condições**, você precisa configurar **aplicativos cliente (versão prévia)** . 
   1. Como **aplicativos cliente (versão prévia)** , selecione **aplicativos móveis e clientes de área de trabalho** e clientes do **Exchange ActiveSync**.

   ![Acesso Condicional](./media/app-based-conditional-access/92.png)

1. Como **controles de acesso**, você precisa ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como em conformidade**
   - **Exigir aplicativo cliente aprovado (versão prévia)**
   - **Exigir todos os controles selecionados**   

**Etapa 3 – configurar a política de proteção de aplicativo do Intune para aplicativos cliente iOS e Android**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Consulte [proteger aplicativos e dados com Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).

Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md). 
