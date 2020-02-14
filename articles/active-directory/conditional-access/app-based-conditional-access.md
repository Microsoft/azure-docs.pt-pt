---
title: Aplicativos de clientes aprovados com Acesso Condicional - Diretório Ativo Azure
description: Saiba como exigir aplicações de clientes aprovadas para acesso a aplicações na nuvem com Acesso Condicional no Diretório Ativo Azure.
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
ms.openlocfilehash: 1a8832234978a2c8b2db25d88b5dd6c211b634b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186455"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: Exigir aplicações de clientes aprovadas para acesso a aplicações na nuvem com acesso condicional 

Os seus empregados utilizam dispositivos móveis para tarefas pessoais e profissionais. Ao mesmo tempo que se certifica de que os seus colaboradores podem ser produtivos, também pretende evitar a perda de dados. Com o Acesso Condicional do Diretório Ativo Azure (Azure AD), pode restringir o acesso às suas aplicações na nuvem a aplicações de clientes aprovadas que possam proteger os seus dados corporativos.  

Este tópico explica como configurar políticas de acesso às condições que requerem aplicações de clientes aprovadas.

## <a name="overview"></a>Descrição geral

Com o [Acesso Condicional Azure AD,](overview.md)pode afinar a forma como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, pode limitar o acesso às suas aplicações em nuvem a dispositivos fidedignos.

Pode utilizar políticas de proteção de [aplicações Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa. As políticas de proteção de aplicações intonantes não requerem uma solução de gestão de dispositivos móveis (MDM), que lhe permite proteger os dados da sua empresa com ou sem dispositivos de inscrição numa solução de gestão de dispositivos.

O Azure Ative Directory Conditional Access permite-lhe limitar o acesso às suas aplicações na nuvem a aplicações de clientes que suportam políticas de proteção de aplicações Intune. Por exemplo, pode restringir o acesso ao Exchange Online à aplicação Outlook.

Na terminologia de Acesso Condicional, estas aplicações de clientes são conhecidas como **aplicações de clientes aprovadas.**  

![Acesso Condicional](./media/app-based-conditional-access/05.png)

Para obter uma lista de aplicações de clientes aprovadas, consulte o requisito de [aplicação de cliente aprovado](concept-conditional-access-grant.md).

Pode combinar políticas de acesso condicional baseadas em aplicações com outras políticas, tais como [políticas de acesso condicional baseadas em dispositivos,](require-managed-devices.md) para fornecer flexibilidade na forma de proteger dados para dispositivos pessoais e corporativos.

## <a name="before-you-begin"></a>Antes de começar

Este tópico assume que está familiarizado com:

- O [requisito de aplicação de cliente aprovado](concept-conditional-access-grant.md).
- Os conceitos básicos de [Acesso Condicional no Diretório Ativo Azure.](overview.md)
- Como [configurar uma política](app-based-mfa.md)de acesso condicional.
- A [migração das políticas de Acesso Condicional.](best-practices.md#policy-migration)

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional baseada em aplicativos, deve ter uma empresa de Mobilidade + Segurança ou uma subscrição premium azure Ative Directory, e os utilizadores devem ser licenciados para EMS ou Azure AD. 

## <a name="exchange-online-policy"></a>Política de troca online 

Este cenário consiste numa política de acesso condicional baseada em aplicações para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que um utilizador:

- Configures e-mail usando uma aplicação de correio nativo no iOS ou Android para ligar ao Exchange
- Recebe um e-mail que indica que o acesso só está disponível usando a aplicação Outlook
- Descarrega a aplicação com o link
- Abre a aplicação Outlook e assina com as credenciais da AD Azure
- É solicitado a instalar o Autenticador (iOS) ou o Portal da Empresa (Android) para continuar
- Instala a aplicação e pode voltar à app Outlook para continuar
- É solicitado a registar um dispositivo
- É capaz de aceder a e-mail

Quaisquer políticas de proteção de aplicações Intune são ativadas no momento em que os dados corporativos de acesso e podem levar o utilizador a reiniciar a aplicação, utilizar um PIN adicional etc. (se configurado para a aplicação e plataforma).

### <a name="configuration"></a>Configuração 

**Passo 1 - Configure uma política de acesso condicional Azure AD para troca online**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online**.
1. **Condições:** Como **Condições,** é necessário configurar **plataformas de Dispositivos** e **aplicações de Clientes:**
   1. Como **plataformas de Dispositivos**, selecione **Android** e **iOS.**
   1. Como **aplicações cliente (pré-visualização),** selecione **aplicações móveis e aplicações de desktop** e clientes de **autenticação moderna.**
1. Como **controla**o Acesso, é necessário ter a aplicação do **cliente aprovada (pré-visualização)** selecionada.

   ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Passo 2 - Configure uma política de acesso condicional Azure AD para troca online com Ative Sync (EAS)**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online**.
1. **Condições:** Como **Condições,** precisa configurar **aplicações do Cliente (pré-visualização)** . 
   1. Como **aplicações cliente (pré-visualização)** , selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**
   1. Como **controla**o Acesso, é necessário ter a aplicação do **cliente aprovada (pré-visualização)** selecionada.

      ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Passo 3 - Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

Consulte [aplicativos e dados protect com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) o Microsoft Intune para obter mais informações.

## <a name="exchange-online-and-sharepoint-online-policy"></a>Política online de troca online e sharePoint

Este cenário consiste num Acesso Condicional com política de gestão de aplicações móveis para acesso ao Exchange Online e Ao SharePoint Online com aplicações aprovadas.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que um utilizador:

- Tenta usar a app SharePoint para conectar e também para ver os seus sites corporativos
- Tentativas de entrar com as mesmas credenciais que as credenciais de aplicação do Outlook
- Não tem que se reregistar e pode ter acesso aos recursos

### <a name="configuration"></a>Configuração

**Passo 1 - Configure uma política de acesso condicional Azure AD para troca online e SharePoint Online**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online** e Office **365 SharePoint Online**. 
1. **Condições:** Como **Condições,** é necessário configurar **plataformas de Dispositivos** e **aplicações de Clientes:**
   1. Como **plataformas de Dispositivos**, selecione **Android** e **iOS.**
   1. Como **aplicações cliente (pré-visualização),** selecione **aplicações móveis e clientes de desktop** e clientes de **autenticação moderna.**
1. Como **controla**o Acesso, é necessário ter a aplicação do **cliente aprovada (pré-visualização)** selecionada.

   ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Passo 2 - Configure uma política de acesso condicional Azure AD para troca online com Ative Sync (EAS)**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online**. Online 
1. **Condições:** Como **Condições,** precisa configurar **aplicações do Cliente:**
   1. Como **aplicações cliente (pré-visualização)** , selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**
   1. Como **controla**o Acesso, é necessário ter a aplicação do **cliente aprovada (pré-visualização)** selecionada.

      ![Acesso Condicional](./media/app-based-conditional-access/05.png)

**Passo 3 - Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Consulte [aplicativos e dados protect com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) o Microsoft Intune para obter mais informações.

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivos baseado seleções ou conformes para exchange online e SharePoint Online

Este cenário consiste numa política de acesso condicional ao dispositivo baseado em aplicações ou em conformidade para o acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que:
 
- Alguns utilizadores já estão matriculados (com ou sem dispositivos corporativos)
- Os utilizadores que não estejam matriculados e registados na Azure AD utilizando uma aplicação protegida por aplicações precisam de registar um dispositivo para aceder a recursos
- Os utilizadores matriculados que utilizam a aplicação protegida da aplicação não têm de voltar a registar o dispositivo

### <a name="configuration"></a>Configuração

**Passo 1 - Configure uma política de acesso condicional Azure AD para troca online e SharePoint Online**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online** e Office **365 SharePoint Online**. 
1. **Condições:** Como **Condições,** é necessário configurar **plataformas de Dispositivos** e **aplicações de Clientes.** 
   1. Como **plataformas de Dispositivos**, selecione **Android** e **iOS.**
   1. Como **aplicações cliente (pré-visualização),** selecione **aplicações móveis e clientes de desktop** e clientes de **autenticação moderna.**
1. Como controlos de **acesso,** é necessário ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir aplicação de cliente aprovada (pré-visualização)**
   - **Requerer um dos controlos selecionados**   
 
      ![Acesso Condicional](./media/app-based-conditional-access/11.png)

**Passo 2 - Configure uma política de acesso condicional Azure AD para troca online com Ative Sync (EAS)**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online**. 
1. **Condições:** Como **Condições,** precisa configurar **aplicações do Cliente.** 
   1. Como **aplicações cliente (pré-visualização)** , selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**
1. Como **controla**o Acesso, é necessário ter a aplicação do **cliente aprovada (pré-visualização)** selecionada.
 
   ![Acesso Condicional](./media/app-based-conditional-access/11.png)

**Passo 3 - Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Consulte [aplicativos e dados protect com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) o Microsoft Intune para obter mais informações.

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivos baseado seleções e conformes para exchange online e SharePoint Online

Este cenário consiste numa política de acesso condicional ao dispositivo baseado em aplicações e em conformidade para o acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que um utilizador:
 
- Configures e-mail usando uma aplicação de correio nativo no iOS ou Android para ligar ao Exchange
- Recebe um e-mail que indica que o acesso requer que o seu dispositivo seja inscrito
- Descarrega o portal da empresa e entra no portal da empresa
- Verifica o correio e é solicitado a usar a app Outlook
- Descarrega a aplicação Outlook
- Abre a app Outlook e entra nas credenciais utilizadas na inscrição
- O utilizador pode aceder a e-mails

Quaisquer políticas de proteção de aplicações Intune são ativadas no momento do acesso aos dados corporativos e podem levar o utilizador a reiniciar a aplicação, utilizar um PIN adicional etc. (se configurado para a aplicação e plataforma)

### <a name="configuration"></a>Configuração

**Passo 1 - Configure uma política de acesso condicional Azure AD para troca online e SharePoint Online**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online** e Office **365 SharePoint Online**. 
1. **Condições:** Como **Condições,** é necessário configurar **plataformas de Dispositivos** e **aplicações de Clientes.** 
   1. Como **plataformas de Dispositivos**, selecione **Android** e **iOS.**
   1. Como **aplicações cliente (pré-visualização),** selecione **aplicações móveis e aplicações de desktop** e clientes de **autenticação moderna.**
1. Como controlos de **acesso,** é necessário ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir aplicação de cliente aprovada (pré-visualização)**
   - **Exigir todos os controlos selecionados**   
 
      ![Acesso Condicional](./media/app-based-conditional-access/13.png)

**Passo 2 - Configure uma política de acesso condicional Azure AD para troca online com Ative Sync (EAS)**

Para a política de Acesso Condicional neste passo, é necessário configurar os seguintes componentes:

1. O **nome** da sua política de acesso condicional.
1. **Utilizadores e grupos**: Cada política de Acesso Condicional deve ter pelo menos um utilizador ou grupo selecionado.
1. **Aplicativos em nuvem:** Como aplicações em nuvem, você precisa selecionar **Office 365 Exchange Online**. 
1. **Condições:** Como **Condições,** precisa configurar **aplicações do Cliente (pré-visualização)** . 
   1. Como **aplicações cliente (pré-visualização)** , selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**

   ![Acesso Condicional](./media/app-based-conditional-access/92.png)

1. Como controlos de **acesso,** é necessário ter o seguinte selecionado:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir aplicação de cliente aprovada (pré-visualização)**
   - **Exigir todos os controlos selecionados**   

**Passo 3 - Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-based-conditional-access/09.png)

Consulte [aplicativos e dados protect com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) o Microsoft Intune para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

Se quiser saber configurar uma política de Acesso Condicional, consulte [RequireM para aplicações específicas com Acesso Condicional de Diretório Ativo Azure](app-based-mfa.md).

Se estiver pronto para configurar as políticas de Acesso Condicional para o seu ambiente, consulte as [melhores práticas de Acesso Condicional no Diretório Ativo Azure](best-practices.md). 
