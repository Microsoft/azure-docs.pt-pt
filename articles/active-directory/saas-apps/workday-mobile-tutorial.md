---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com aplicação móvel do workday / Microsoft Docs'
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
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754722"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Tutorial: Azure Ative Directory integração única (SSO) com aplicação móvel workday

Neste tutorial, você vai aprender a integrar O Azure Ative Directy (Azure AD), Acesso Condicional e Intune com as Aplicações Móveis Workday. Quando integrar as Aplicações Móveis do Workday com a Microsoft, pode:

* Certifique-se de que os dispositivos estão em conformidade com as suas políticas antes de iniciar sôm.
* Adicione controlos à App workday para garantir que os utilizadores estão a aceder de forma segura aos dados corporativos. 
* Controlo em Azure AD que tem acesso ao Workday.
* Ative os seus utilizadores a serem automaticamente inscritos no Workday com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Integrar o Dia de Trabalho com a Azure AD
* Tutorial: [Azure Ative Directory integração única (SSO) com Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa as Políticas de Acesso Condicional da Microsoft e Intune com as Aplicações Móveis do Workday.

* A aplicação federada workday pode agora ser configurada com Azure AD para permitir sSO. Para mais detalhes sobre como configurar, siga [este](workday-tutorial.md) link.

> [!NOTE] 
> O Workday não suporta as Políticas de Proteção de Aplicações da Intune. Tem de utilizar a Gestão de Dispositivos Móveis para utilizar o Acesso Condicional.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Garantir que os utilizadores têm acesso à Aplicação Móvel Workday:

Configure o Workday para permitir o acesso às suas ofertas de Aplicações Móveis. Terá de configurar as seguintes políticas para o Mobile:

Pode configurmentar estas as instruções seguindo estas instruções:

1. Aceda ao relatório de Políticas de Segurança do Domínio para Área Funcional.
2. Selecione uma política de segurança.
    * Utilização móvel - Android
    * Utilização móvel - iPad
    * Utilização móvel - iPhone
3. Clique em Permissões de Edição.
4. Selecione a caixa de verificação 'Ver ou Modificar' para permitir aos grupos de segurança o acesso ao relatório ou itens de tarefas securáveis.
5. Selecione a caixa de verificação Get or Put para garantir aos grupos de segurança acesso à integração e reportar ou ações securáveis da tarefa.

Ative as alterações da política de segurança pendentes executando a tarefa **De alterar a política de segurança pendente.**

## <a name="open-workday-login-page-in-mobile-browser"></a>Página de início de sessão aberta no navegador móvel:

Para aplicar o Acesso Condicional à aplicação móvel do Workday, é necessário que a aplicação abra num navegador externo. Isto pode ser feito verificando a caixa **Enable Mobile Browser SSO para aplicações nativas** na **configuração do inquilino de edição - Segurança.** Isto exigirá a instalação de um navegador aprovado pelo Intune no dispositivo para iOS e no Perfil de Trabalho para Android

![Login do Navegador Móvel](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Política de Acesso Condicional de Configuração:

Esta política apenas irá afetar o login num dispositivo iOS ou Android. Se quiser alargá-lo a todas as plataformas, basta selecionar **Qualquer Dispositivo.** Esta política exigirá que o dispositivo esteja em conformidade com a política e verificará isto através do Microsoft Intune. Devido ao Android ter Perfis de Trabalho, isto deve bloquear qualquer utilizadores de iniciar sessão no Workday (Web ou App), a menos que estejam a iniciar sessão através do seu Perfil de Trabalho e tenham instalado a aplicação através do Portal da Empresa Intune. Há um passo adicional para o iOS garantir que a mesma situação se aplicará. Aqui estão algumas imagens da configuração do Acesso Condicional.

**O workday suporta os seguintes Controlos de Acesso:**
* Requerem autenticação de vários fatores
* Exigir que o dispositivo seja marcado como conforme

**A Aplicação Workday não suporta o seguinte:**
* Requera uma aplicação de cliente aprovada
* Requerer a política de proteção de aplicações (Pré-visualização)

Para configurar o **Workday** como **Dispositivo Gerido** execute os seguintes passos:

![Política de Acesso Condicional de Configuração](./media/workday-tutorial/managed-devices-only.png)

1. Clique em **Home > Microsoft Intune > apenas Access-Policies > dispositivos geridos condicional** 

1. Na página **'Apenas' dispositivos geridos,** dê o valor do campo **Nome** como `Managed Devices Only` e clique em **aplicações ou ações** cloud .

1. Execute os seguintes passos em **aplicativos ou ações cloud** .

    a. Switch **Selecione o que esta política aplica como** **aplicações Cloud** .

    b. In Include, clique em **Select apps** .

    c. Escolha **o Dia de Trabalho** na lista selecionada.

    d. Clique em **Done** (Concluído).

1. Ligue a **política de Ativação** .

1. Clique em **Guardar** .

Para o acesso **ao Grant,** execute os seguintes passos:

![Política de acesso condicional de configuração do dia de trabalho](./media/workday-tutorial/managed-devices-only-2.png)

1. Clique em **Home > Microsoft Intune > apenas Access-Policies > dispositivos geridos condicional** 

1. Na página **'Apenas dispositivos geridos',** dê o valor do campo **Nome** à medida `Managed Devices Only` que clice nos **controlos de acesso > Grant** .

1. Execute os seguintes passos na página **grant.**

    a. Selecione os controlos a aplicar como **acesso grant** .

    b. Verifique se o **dispositivo 'Exigir' deve ser marcado como** caixa conforme.

    c. **Selecione Exigir um dos controlos selecionados** .

    d. Clique em **Select** .

1. Ligue a **política de Ativação** .

1. Clique em **Guardar**

## <a name="set-up-device-compliance-policy"></a>Configurar a Política de Conformidade do Dispositivo:

Para garantir que os dispositivos iOS só são capazes de iniciar sessão através de uma App workday gerida pelo MDM, é necessário bloquear a aplicação da App Store adicionando **o com.workday.workdayapp** à lista de aplicações restritas. Isto irá garantir que apenas os dispositivos que tenham a aplicação Workday instalada através do portal da empresa podem aceder ao Workday. Para o navegador, eles só poderão aceder ao Workday se o dispositivo for gerido pelo Intune e estiverem a utilizar um navegador gerido.

![Política de conformidade do dispositivo de configuração do dia de trabalho](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Configurar as políticas de configuração da aplicação do Microsoft Intune:

| Cenário | Pares de valor chave |
|----------------------------------------------------------------------------------------   |-----------|
| Povoar automaticamente os campos de Endereços De Inquilino e Web para:<br>● Dia de trabalho no Android quando ativa perfis android para trabalho.<br>● Dia de trabalho no iPad e iPhone.     | Utilize estes valores para configurar o seu Inquilino: <br>● Chave de configuração = UserGroupCode<br>● Tipo de Valor = Cadeia <br>● Valor de Configuração = Nome do seu inquilino. Exemplo: gms<br>Utilize estes valores para configurar o seu Endereço Web:<br>● Chave de configuração = AppServiceHost<br>● Tipo de Valor = Cadeia<br>● Valor de Configuração = O URL base para o seu inquilino. Exemplo: https://www.myworkday.com                              |   |
| Desativar estas ações para o Workday no iPad e iPhone:<br>● Corte, Copie e Pasta<br>● Imprimir                       | Desative o valor (booleano) a Falso nestas teclas para desativar a funcionalidade:<br>● PermitirCutCopyPaste<br>● Permitir a Impressão  |
| Desative as imagens para workday no Android. |Desative o valor (booleano) para falso na tecla AllowScreenshots para desativar a funcionalidade.|
| Desative as atualizações sugeridas para os seus utilizadores.|Defina o valor (booleano) para Falso na tecla AllowSuggestedUpdates para desativar a funcionalidade.|
|Personalize o URL da loja de aplicações para direcionar os utilizadores móveis para a loja de aplicações à sua escolha.|Utilize estes valores para alterar o URL da loja de aplicações:<br>● Chave de configuração = AppUpdateURL<br>● Tipo de Valor = Cadeia<br> ● Valor de Configuração = URL da loja de aplicações|
|       |


## <a name="ios-configuration-policies"></a>Políticas de configuração do iOS:

1. Ir https://portal.azure.com/ e fazer login
2. Procure **por Intune** ou clique no widget da lista.
3. Vá a aplicações de **> apps -> Políticas de Configuração de Aplicações -> + Adicionar dispositivos geridos de >**
4. Insira um nome.
5. Em **Plataforma** , escolha **iOS/iPadOS**
6. Na **Aplicação Associated** , escolha o Workday para a aplicação iOS que adicionou
7. Clique em **Definições de Configuração** e no **formato de configuração** escolha introduzir **dados XML**
8. Aqui está um ficheiro XML exemplo. Adicione as configurações que gostaria de aplicar. Substitua **STRING_VALUE** pela corda que pretende utilizar. Substitua `<true />` ou `<false />` com . `<true />`  `<false />` . Se não adicionar uma configuração, funcionará como se fosse definido para True.

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
9. Clique em Adicionar
10. Refresque a página e clique na política recém-criada.
11. Clique em Atribuições e escolha a quem pretende que a aplicação se aplique.
12. Clique em Guardar.

## <a name="android-configuration-policies"></a>Políticas de configuração do Android:

1. Aceda a `https://portal.azure.com/` e inicie sessão.
2. Procure **por Intune** ou clique no widget da lista.
3. Vá a aplicações de **> apps -> Políticas de Configuração de Aplicações -> + Adicionar dispositivos geridos de >**
5. Insira um nome. 
6. Em **Plataforma** , escolha **Android**
7. No Âmbito **da App Associated** , escolha o Workday para a aplicação Android que adicionou
8. Clique em **Definições de Configuração** e no **formato de configuração** escolha introduzir **dados JSON**

