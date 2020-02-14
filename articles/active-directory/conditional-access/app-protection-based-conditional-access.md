---
title: Políticas de proteção de aplicativos com Acesso Condicional - Diretório Ativo Azure
description: Saiba como exigir a política de proteção de aplicações para o acesso de apps na nuvem com acesso condicional no Diretório Ativo Azure.
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
ms.openlocfilehash: 3c853ef3a5a40381aba4e1c13eaf9ad7d8653170
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186622"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Exigir política de proteção de aplicativos para acesso a aplicações na nuvem com Acesso Condicional (pré-visualização)

Os seus empregados utilizam dispositivos móveis para tarefas pessoais e profissionais. Ao mesmo tempo que se certifica de que os seus colaboradores podem ser produtivos, também pretende evitar a perda de dados. Com o Acesso Condicional do Diretório Ativo Azure (Azure AD), pode proteger os seus dados corporativos limitando o acesso às suas aplicações na nuvem. Utilize as aplicações dos clientes com uma política de proteção de aplicações primeiro.

Este artigo explica como configurar políticas de Acesso Condicional que podem exigir uma política de proteção de aplicações antes de o acesso ser concedido aos dados.

## <a name="overview"></a>Descrição geral

Com o [Acesso Condicional Azure AD,](overview.md)pode afinar a forma como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, pode limitar o acesso às suas aplicações em nuvem a dispositivos fidedignos.

Pode utilizar políticas de proteção de [aplicações Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da sua empresa. As políticas de proteção de aplicações intune não requerem uma solução de gestão de dispositivos móveis (MDM). Pode proteger os dados da sua empresa com ou sem dispositivos de inscrição numa solução de gestão de dispositivos.

O Azure Ative Directory Conditional Access restringe o acesso às suas aplicações na nuvem a aplicações de clientes que a Intune reportou à Azure AD como recebendo uma política de proteção de aplicações. Por exemplo, pode restringir o acesso ao Exchange Online à aplicação Outlook que tem uma política de proteção de aplicações Intune.

Na terminologia de Acesso Condicional, estas aplicações de clientes são conhecidas por serem protegidas com uma política de proteção de *aplicações.*  

![Acesso Condicional](./media/app-protection-based-conditional-access/05.png)

Para obter uma lista de aplicações de clientes protegidas por políticas, consulte a exigência da política de proteção de [aplicações](concept-conditional-access-grant.md).

Pode combinar políticas de acesso condicional baseadas em aplicações com outras políticas, tais como [políticas de acesso condicional baseadas em dispositivos.](require-managed-devices.md) Desta forma, pode fornecer flexibilidade na forma de proteger dados tanto para dispositivos pessoais como corporativos.

> [!NOTE]
> As políticas de proteção de aplicações de acesso condicional não podem ser aplicadas aos utilizadores B2B porque a organização convidativa não tem visibilidade na organização doméstica do utilizador B2B.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Benefícios da exigência de acesso condicional baseado na proteção de aplicativos

À semelhança do cumprimento que é relatado pela Intune para iOS e Android para um dispositivo gerido, intune reporta agora ao Azure AD se for aplicada uma política de proteção de aplicações. O Acesso Condicional pode usar esta política como verificação de acesso. Esta nova política de Acesso Condicional, a política de proteção de aplicações, aumenta a segurança. Protege contra erros administrativos, tais como:

- Utilizadores que não têm licença Intune.
- Utilizadores que não possam receber uma política de proteção de aplicações Intune.
- Insinto aplicações de política de proteção de aplicações que não estão configuradas para receber uma política.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você esteja familiarizado com:

- O [requisito da política de proteção](concept-conditional-access-grant.md)de aplicações .
- O [requisito de aplicação de cliente aprovado](concept-conditional-access-grant.md).
- Os conceitos básicos de [Acesso Condicional no Diretório Ativo Azure.](overview.md)
- Como [configurar uma política](app-based-mfa.md)de acesso condicional.

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional baseada na proteção de aplicações, deve:

- Tenha uma Mobilidade Empresarial + Segurança ou uma subscrição premium azure Ative Directory + Intune.
- Certifique-se de que os utilizadores estão licenciados para Mobilidade Empresarial + Segurança ou Azure AD + Intune.
- Certifique-se de que a aplicação do cliente está configurada em Intune para receber uma política de proteção de aplicações.
- Certifique-se de que os utilizadores estão configurados em Intune para receber uma política de proteção de aplicações Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Política baseada na proteção de aplicativos para exchange online

Este cenário consiste numa política de acesso condicional baseada na proteção de aplicações para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que um utilizador:

- Configura o e-mail utilizando uma aplicação de correio nativo no iOS ou Android para ligar ao Exchange.
- Recebe um e-mail que indica que o acesso só está disponível através da aplicação Outlook.
- Descarrega a aplicação com o link.
- Abre a aplicação Outlook e assina com credenciais Azure AD.
- É solicitado a instalar a **aplicação Microsoft Authenticator** ou o Portal da **Empresa Intune** para continuar.
- Instala a aplicação e regressa à aplicação Outlook para continuar.
- É solicitado a registar um dispositivo.
- Pode receber uma política de proteção de aplicações Intune.
- Pode aceder ao e-mail.

Quaisquer políticas de proteção de aplicações Intune devem estar na aplicação para aceder a dados corporativos. As políticas podem levar o utilizador a reiniciar a aplicação ou a utilizar um PIN adicional. É o caso se as políticas estiverem configuradas para a aplicação e plataforma.

### <a name="configuration"></a>Configuração

**Passo 1: Configure uma política de acesso condicional Azure AD para troca online**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/01.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online**.

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **Condições,** configure **as plataformas do Dispositivo** e as **aplicações do Cliente (pré-visualização)**
   1. Nas **plataformas do Dispositivo**, selecione **Android** e **iOS.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes de **autenticação moderna.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

1. Sob **os controlos de acesso,** selecione **Exigir a política de proteção da aplicação (pré-visualização)** .

   ![Acesso Condicional](./media/app-protection-based-conditional-access/05.png)

**Passo 2: Configure uma política de acesso condicional Azure AD para troca online com ActiveSync (EAS)**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online**.

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **Condições,** configure **as aplicações do Cliente (pré-visualização)** . 

   1. Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/92.png)

   1. Sob **os controlos de acesso,** selecione **Exigir a política de proteção da aplicação (pré-visualização)** .

      ![Acesso Condicional](./media/app-protection-based-conditional-access/05.png)

**Passo 3: Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para mais informações, consulte [Proteja as aplicações e dados com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)o Microsoft Intune .

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Política de dispositivos baseados na proteção de aplicações ou conformes para o Exchange Online

Este cenário consiste numa política de acesso condicional baseado em dispositivos de proteção de aplicações ou em conformidade para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que:
 
- Um utilizador já está matriculado, com ou sem dispositivos corporativos.
- Os utilizadores que não estejam matriculados e registados no Azure AD através da utilização de uma aplicação protegida por aplicações precisam de registar um dispositivo para aceder a recursos.
- Os utilizadores matriculados que utilizam a aplicação protegida da aplicação não têm de voltar a registar o dispositivo.
- O utilizador pode receber uma política de proteção de aplicações Intune se não estiver matriculado.
- O utilizador pode aceder a e-mails com o Outlook e uma política de proteção de aplicações Intune se não estiver matriculado.
- O utilizador pode aceder a e-mails com o Outlook se o dispositivo estiver matriculado.

### <a name="configuration"></a>Configuração

**Passo 1: Configure uma política de acesso condicional Azure AD para troca online**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/62.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **Condições,** configure **as plataformas do Dispositivo** e as **aplicações do Cliente (pré-visualização)** . 
 
   1. Nas **plataformas do Dispositivo**, selecione **Android** e **iOS.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes de **autenticação moderna.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

5. Sob **os controlos de acesso,** selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Requerer um dos controlos selecionados**   
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/11.png)

**Passo 2: Configure uma política de acesso condicional Azure AD para troca online com ActiveSync**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **Condições,** configure **as aplicações do Cliente (pré-visualização)** . 

   Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**

   ![Acesso Condicional](./media/app-protection-based-conditional-access/92.png)

1. Sob **os controlos de acesso,** selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Requerer um dos controlos selecionados**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/11.png)

**Passo 3: Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para mais informações, consulte [Proteja as aplicações e dados com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)o Microsoft Intune .

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Política de dispositivos baseados em proteção de aplicativos e conformes para o Exchange Online

Este cenário consiste numa política de acesso condicional ao dispositivo baseado em aplicações e em conformidade para acesso ao Exchange Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que um utilizador:
 
- Configura o e-mail utilizando uma aplicação de correio nativo no iOS ou Android para ligar ao Exchange.
- Recebe um e-mail que indica que o acesso requer que o seu dispositivo seja matriculado.
- Descarrega O Portal da Empresa Intune e entra no portal.
- Verifica o correio e é solicitado a usar a aplicação Outlook.
- Descarrega a aplicação Outlook.
- Abre a aplicação Outlook e entra nas credenciais utilizadas na inscrição.
- Pode receber uma política de proteção de aplicações Intune.
- Pode aceder a e-mails com o Outlook e uma política de proteção de aplicações Intune.

Quaisquer políticas de proteção de aplicações Intune são ativadas antes de o acesso ser concedido aos dados corporativos. As políticas podem levar o utilizador a reiniciar a aplicação ou a utilizar um PIN adicional. É o caso se as políticas estiverem configuradas para a aplicação e plataforma.

### <a name="configuration"></a>Configuração

**Passo 1: Configure uma política de acesso condicional Azure AD para troca online**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/01.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **Condições,** configure **as plataformas do Dispositivo** e as **aplicações do Cliente (pré-visualização)** . 
   1. Nas **plataformas do Dispositivo**, selecione **Android** e **iOS.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes de **autenticação moderna.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

1. Sob **os controlos de acesso,** selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Exigir todos os controlos selecionados**   
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/13.png)

**Passo 2: Configure uma política de acesso condicional Azure AD para troca online com ActiveSync**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/06.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/07.png)

1. Em **Condições,** configure **as aplicações do Cliente (pré-visualização)** . 

   Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**

   ![Acesso Condicional](./media/app-protection-based-conditional-access/92.png)

1. Sob **os controlos de acesso,** selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como conforme**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Exigir todos os controlos selecionados**   
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/13.png)

**Passo 3: Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para mais informações, consulte [Proteja as aplicações e dados com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)o Microsoft Intune .

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Política baseada na proteção de aplicativos ou baseada em aplicativos para exchange online e SharePoint Online

Este cenário consiste numa política de aplicações baseadas em proteção de aplicações ou aprovadas para acesso ao Exchange Online e Ao SharePoint Online.

### <a name="scenario-playbook"></a>Livro de jogadas de cenário

Este cenário pressupõe que um utilizador:

- Configura aplicações de clientes que estão na lista de aplicações que suportam o requisito da política de proteção de aplicações ou o requisito de aplicações aprovadas.  
- Utiliza aplicações de clientes que cumprem o requisito da política de proteção de aplicações e podem receber uma política de proteção de aplicações Intune.
- Utiliza aplicações de clientes que cumprem o requisito de política de aplicações aprovada que suporta a política de proteção de aplicações Intune.
- Abre a aplicação para aceder a emails ou documentos.
- Abre a aplicação Outlook e assina com credenciais Azure AD.
- É solicitado a instalar o Microsoft Authenticator para uso iOS ou o Portal da Empresa Intune para uso Android se ainda não estiverem instalados.
- Instala a aplicação e pode voltar à aplicação Outlook para continuar.
- É solicitado a registar um dispositivo.
- Pode receber uma política de proteção de aplicações Intune.
- Pode aceder a e-mails com o Outlook e uma política de proteção de aplicações Intune.
- Pode aceder a sites e documentos com uma aplicação que não está no requisito da política de proteção de aplicações, mas listado no requisito da aplicação aprovada.

Quaisquer políticas de proteção de aplicações Intune são necessárias antes de o acesso ser concedido aos dados corporativos. As políticas podem levar o utilizador a reiniciar a aplicação ou a utilizar um PIN adicional. É o caso se as políticas estiverem configuradas para a aplicação e plataforma.

**Observações**

- Pode utilizar este cenário se quiser apoiar as políticas de acesso condicional baseadas na proteção de aplicações e com base em aplicações.
- Nesta política *de OR,* as aplicações com um requisito de política de proteção de aplicações são avaliadas para acesso primeiro antes do requisito das aplicações de clientes aprovados.

### <a name="configuration"></a>Configuração

**Passo 1: Configure uma política de acesso condicional Azure AD para troca online e SharePoint Online**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

![Acesso Condicional](./media/app-protection-based-conditional-access/62.png)

1. Insira o nome da sua política de Acesso Condicional.
1. No âmbito **de Missões**, em **Utilizadores e grupos,** selecione pelo menos um utilizador ou grupo para cada política de Acesso Condicional.
1. Nas **aplicações Cloud**, selecione **Office 365 Exchange Online** e Office **365 SharePoint Online**. 

   ![Acesso Condicional](./media/app-protection-based-conditional-access/02.png)

1. Em **Condições,** configure **as plataformas do Dispositivo** e as **aplicações do Cliente (pré-visualização)** . 
   1. Nas **plataformas do Dispositivo**, selecione **Android** e **iOS.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/03.png)

   1. Nas **aplicações Cliente (pré-visualização)** selecione **aplicações móveis e clientes de desktop** e clientes de **autenticação moderna.**

      ![Acesso Condicional](./media/app-protection-based-conditional-access/91.png)

1. Sob **os controlos de acesso,** selecione as seguintes opções:
   - **Exigir aplicação de cliente aprovada**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Requerer um dos controlos selecionados**
 
      ![Acesso Condicional](./media/app-protection-based-conditional-access/12.png)

**Passo 2: Configure política de proteção de aplicações Intune para aplicações de iOS e clientes Android**

![Acesso Condicional](./media/app-protection-based-conditional-access/09.png)

Para mais informações, consulte [Proteja as aplicações e dados com](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)o Microsoft Intune .

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber configurar uma política de Acesso Condicional, consulte [RequireM para aplicações específicas com Acesso Condicional de Diretório Ativo Azure](app-based-mfa.md).
- Se estiver pronto para configurar as políticas de Acesso Condicional para o seu ambiente, consulte [as melhores práticas de acesso condicional no Diretório Ativo Azure](best-practices.md).
