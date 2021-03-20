---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com aplicação móvel do workday | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Workday Mobile Application.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182024"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Tutorial: Azure Ative Directory integração única (SSO) com aplicação móvel workday

Neste tutorial, você vai aprender a integrar O Azure Ative Directy (Azure AD), Acesso Condicional e Intune com Aplicação Móvel Workday. Quando integrar a Aplicação Móvel workday com a Microsoft, pode:

* Certifique-se de que os dispositivos estão em conformidade com as suas políticas antes de iniciar a sômposições.
* Adicione controlos à Aplicação Móvel Workday para garantir que os utilizadores acedem de forma segura aos dados corporativos. 
* Controlo em Azure AD que tem acesso ao Workday.
* Permita que os seus utilizadores sejam automaticamente inscritos no Workday com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar:

* Integrar o Workday com a Azure AD.
* Leia [a integração única do Azure Ative Directory (SSO) com o Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa as políticas de acesso condicional Azure AD e Intune com aplicação móvel workday.

Para ativar um único sinal de sso, pode configurar a aplicação Federada workday com Azure AD. Para obter mais informações, consulte [a integração única de sign-on (SSO) do Azure Ative Directory com o Workday](./workday-tutorial.md).

> [!NOTE] 
> O workday não suporta as políticas de proteção de aplicações do Intune. Tem de utilizar a gestão do dispositivo móvel para utilizar o Acesso Condicional.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Garantir que os utilizadores têm acesso à Aplicação Móvel workday

Configurar o Workday para permitir o acesso às suas aplicações móveis. Tem de configurar as seguintes políticas para o Workday Mobile:

1. Aceda às políticas de segurança do domínio para relatório de área funcional.
1. Selecione a política de segurança adequada:
    * Utilização móvel - Android
    * Utilização móvel - iPad
    * Utilização móvel - iPhone
1. Selecione **Permissões de Edição**.
1. Selecione a caixa de verificação **'Ver ou Modificar'** para permitir aos grupos de segurança o acesso ao relatório ou itens de tarefas securáveis.
1. Selecione a caixa de verificação **Get or Put** para garantir aos grupos de segurança acesso à integração e reportar ou ações securáveis da tarefa.

Ativar alterações de política de segurança pendentes executando **Ativar alterações da política de segurança pendentes**.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Página de s-in open workday no navegador móvel workday

Para aplicar o Acesso Condicional à Aplicação Móvel workday, tem de abrir a aplicação num browser externo. Na **Configuração do Inquilino de Edição - Segurança**, selecione Enable Mobile Browser **SSO for Native Apps**. Isto requer que um navegador aprovado pelo Intune seja instalado no dispositivo para iOS, e no perfil de trabalho para Android.

![Screenshot do navegador móvel workday.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Configurar a política de acesso condicional

Esta política apenas afeta a sessão de sessão num dispositivo iOS ou Android. Se quiser alargá-lo a todas as plataformas, selecione **Qualquer Dispositivo**. Esta política requer que o dispositivo esteja em conformidade com a política, e verifica-o através do Intune. Como o Android tem perfis de trabalho, isto impede qualquer utilizadores de iniciar sessão no Workday, a menos que estejam a iniciar sessão através do seu perfil de trabalho e tenham instalado a app através do portal da empresa Intune. Há um passo adicional para o iOS garantir que a mesma situação se aplica.

O workday suporta os seguintes controlos de acesso:
* Requerem autenticação multifactor
* Exigir que o dispositivo seja marcado como conforme

A Workday App não suporta o seguinte:
* Requera uma aplicação de cliente aprovada
* Requerer a política de proteção de aplicações (pré-visualização)

Para configurar o Workday como um dispositivo gerido, execute os seguintes passos:

![Screenshot de aplicações ou ações de Dispositivos Geridos e Cloud.](./media/workday-tutorial/managed-devices-only.png)

1. Selecione **Home**  >  **Microsoft Intune**  >  **Conditional Access-Policies**. Em seguida, **selecione Apenas dispositivos geridos**. 

1. Apenas em **Dispositivos Geridos**, em **Nome**, selecione **Apenas dispositivos geridos** e, em seguida, selecione **aplicações ou ações cloud**.

1. Em **aplicativos ou ações cloud**:

    a. Switch **Selecione o que esta política se aplica às** **aplicações Cloud**.

    b. In **Incluir**, escolha **selecione aplicações**.

    c. Na lista **Select,** escolha **Workday**.

    d. Selecione **Concluído**.

1. Switch **Ativar** a política para **ligar**.

1. Selecione **Guardar**.

Para o acesso **ao Grant,** execute os seguintes passos:

![Screenshot de Apenas dispositivos geridos e Grant.](./media/workday-tutorial/managed-devices-only-2.png)

1. Selecione **Home**  >  **Microsoft Intune**  >  **Conditional Access-Policies**. Em seguida, **selecione Apenas dispositivos geridos**. 

1. Apenas nos **Dispositivos Geridos**, sob **Nome,** **selecione Apenas dispositivos geridos**. Em **Controlos de acesso**, selecione **Concessão**.

1. Em **Grant:**

    a. Selecione os controlos a aplicar como **acesso grant**.

    b. Selecione **Pedir que o dispositivo seja marcado como conforme**.

    c. **Selecione Exigir um dos controlos selecionados**.

    d. Escolha **Selecionar**.

1. Switch **Ativar** a política para **ligar**.

1. Selecione **Guardar**.

## <a name="set-up-device-compliance-policy"></a>Configurar a política de conformidade do dispositivo

Para garantir que os dispositivos iOS só podem iniciar súplica através do Workday geridos pela gestão de dispositivos móveis, é necessário bloquear a aplicação da App Store adicionando **o com.workday.workdayapp** à lista de aplicações restritas. Isto garante que apenas os dispositivos que tenham o Workday instalado através do portal da empresa podem aceder ao Workday. Para o navegador, os dispositivos só podem aceder ao Workday se o dispositivo for gerido pelo Intune e estiver a utilizar um navegador gerido.

![Screenshot da política de conformidade do dispositivo iOS.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Configurar políticas de configuração de aplicativos Intune

| Scenario | Pares de valor chave |
|----------------------------------------------------------------------------------------   |-----------|
| Povoar automaticamente os campos de Endereços De Inquilino e Web para:<br>● Dia de trabalho no Android quando ativa o Android para perfis de trabalho.<br>● Dia de trabalho no iPad e iPhone.     | Utilize estes valores para configurar o seu Inquilino: <br>● Chave de configuração = `UserGroupCode`<br>● Tipo de Valor = Cadeia <br>● Valor de Configuração = Nome do seu inquilino. Exemplo: `gms`<br>Utilize estes valores para configurar o seu Endereço Web:<br>● Chave de configuração = `AppServiceHost`<br>● Tipo de Valor = Cadeia<br>● Valor de Configuração = O URL base para o seu inquilino. Exemplo: `https://www.myworkday.com`                                |   |
| Desativar estas ações para o Workday no iPad e iPhone:<br>● Corte, Copie e Pasta<br>● Imprimir                       | Desative o valor (Boolean) nestas `False` teclas para desativar a funcionalidade:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Desative as imagens para workday no Android. |Desative o valor (Boolean) na `False` `AllowScreenshots` chave para desativar a funcionalidade.|
| Desative as atualizações sugeridas para os seus utilizadores.|Desative o valor (Boolean) na `False` `AllowSuggestedUpdates` chave para desativar a funcionalidade.|
|Personalize o URL da loja de aplicações para direcionar os utilizadores móveis para a loja de aplicações à sua escolha.|Utilize estes valores para alterar o URL da loja de aplicações:<br>● Chave de configuração = `AppUpdateURL`<br>● Tipo de Valor = Cadeia<br> ● Valor de Configuração = URL da loja de aplicações|
|       |


## <a name="ios-configuration-policies"></a>Políticas de configuração para iOS

1. Aceda ao [portal do Azure](https://portal.azure.com/) e inicie sessão.
1. Procure **por Intune** ou selecione o widget da lista.
1. Vá às Políticas de  >  Configuração de **Aplicativos de Aplicações de Aplicações**  >  **de Aplicativos do** Cliente. Em seguida, **selecione + Adicionar**  >  **Dispositivos Geridos**.
1. Insira um nome.
1. Em **Plataforma,** escolha **iOS/iPadOS**.
1. No âmbito **da App Associated**, escolha o Workday para a aplicação iOS que adicionou.
1. Selecione **Definições de configuração**. No **formato configuração,** selecione **Introduzir dados XML**.
1. Aqui está um ficheiro XML exemplo. Adicione as configurações que pretende aplicar. `STRING_VALUE`Substitua-a pela corda que pretende utilizar. `<true /> or <false />` `<true />` Substitua-o por ou `<false />` . Se não adicionar uma configuração, este exemplo funciona como se estivesse definido para `True` .

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Selecione **Adicionar**.
1. Refresque a página e selecione a política recém-criada.
1. Selecione **Atribuições** e escolha a quem pretende que a aplicação se aplique.
1. Selecione **Guardar**.

## <a name="android-configuration-policies"></a>Políticas de configuração para Android

1. Aceda ao [portal do Azure](https://portal.azure.com/) e inicie sessão.
2. Procure **por Intune** ou selecione o widget da lista.
3. Vá às Políticas de  >  Configuração de **Aplicativos de Aplicações de Aplicações**  >  **de Aplicativos do** Cliente. Em seguida, **selecione + Adicionar**  >  **Dispositivos Geridos**.
5. Insira um nome. 
6. Em **Plataforma,** escolha **Android.**
7. No âmbito **da App Associated,** escolha o Workday para a aplicação Android que adicionou.
8. Selecione **Definições de configuração**. No **formato de definições de configuração**, selecione **Introduzir dados JSON**.