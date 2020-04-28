---
title: Guia de administração Atlassian Jira/Confluence - Diretório Ativo Azure Microsoft Docs
description: Guia de administrador para usar Atlassian Jira e Confluence com Azure Ative Directory (Azure AD)..
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73161203"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guia de administração atlassian Jira e Confluence para o Diretório Ativo Azure

## <a name="overview"></a>Descrição geral

O plug-in single (Azure AD) do Azure Ative Directory (SSO) permite que os clientes da Microsoft Azure AD utilizem o seu trabalho ou conta escolar para iniciar a sessão nos produtos baseados em Atlassian Jira e Confluence Server. Implementa sSO baseado em SAML 2.0.

## <a name="how-it-works"></a>Como funciona

Quando os utilizadores querem iniciar sessão na aplicação Atlassian Jira ou Confluence, vêem o Login com botão **Azure AD** na página de entrada. Quando o selecionarem, são obrigados a inscrever-se utilizando a página de inscrição da organização Azure AD (isto é, o seu trabalho ou conta escolar).

Após a autenticação dos utilizadores, estes devem poder iniciar sessão na aplicação. Se já estão autenticados com o ID e a senha para o seu trabalho ou conta escolar, então eles acedem diretamente à aplicação. 

O sign-in trabalha em Jira e Confluence. Se os utilizadores forem inscritos na aplicação Jira e a Confluence for aberta na mesma janela do navegador, não têm de fornecer as credenciais para a outra aplicação. 

Os utilizadores também podem chegar ao produto Atlassian através das Minhas Apps no âmbito do trabalho ou da conta escolar. Deviam ser inscritos sem que lhes fosse pedida credenciais.

> [!NOTE]
> O fornecimento do utilizador não é feito através do plug-in.

## <a name="audience"></a>Audiência

Os administradores da Jira e da Confluence podem usar o plug-in para ativar o SSO utilizando o Azure AD.

## <a name="assumptions"></a>Pressupostos

* As instâncias de Jira e Confluence estão ativadas por HTTPS.
* Os utilizadores já estão criados em Jira ou Confluence.
* Os utilizadores têm funções atribuídas em Jira ou Confluence.
* Os administradores têm acesso à informação necessária para configurar o plug-in.
* Jira ou Confluence também estão disponíveis fora da rede da empresa.
* O plug-in funciona apenas com a versão no local de Jira e Confluence.

## <a name="prerequisites"></a>Pré-requisitos

Tenha em atenção as seguintes informações antes de instalar o plug-in:

* Jira e Confluence estão instalados numa versão do Windows 64 bits.
* As versões Jira e Confluence estão ativadas por HTTPS.
* Jira e Confluence estão disponíveis na internet.
* As credenciais de administração estão em vigor para Jira e Confluence.
* As credenciais de administrador estão em vigor para a AD Azure.
* WebSudo é desativado em Jira e Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versões suportadas de Jira e Confluence

O plug-in suporta as seguintes versões de Jira e Confluence:

* Jira Core e Software: 6.0 a 7.12
* Mesa de Serviço Jira: 3.0.0 a 3.5.0
* Jira também suporta 5.2. Para mais detalhes, clique no [Microsoft Azure Ative Directory single sign-on para JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluência: 5.0 a 5.10
* Confluência: 6.0.1
* Confluência: 6.1.1
* Confluência: 6.2.1
* Confluência: 6.3.4
* Confluência: 6.4.0
* Confluência: 6.5.0
* Confluência: 6.6.2
* Confluência: 6.7.0
* Confluência: 6.8.1
* Confluência: 6.9.0
* Confluência: 6.10.0
* Confluência: 6.11.0
* Confluência: 6.12.0

## <a name="installation"></a>Instalação

Para instalar o plug-in, siga estes passos:

1. Inscreva-se na sua instância Jira ou Confluence como administradora.

2. Vá à consola de administração Jira/Confluence e selecione **Add-ons**.

3. A partir do Microsoft Download Center, descarregue o [Microsoft SAML SSO Plugin para Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO Plugin para Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   A versão adequada do plug-in aparece nos resultados da pesquisa.

4. Selecione o plug-in e o Universal Plug-in Manager (UPM) instala-o.

Depois de instalado o plug-in, aparece na secção **de addons instalado** pelo utilizador de **'Manage Add-ons**'.

## <a name="plug-in-configuration"></a>Configuração plug-in

Antes de começar a utilizar o plug-in, tem de configurá-lo. Selecione o plug-in, selecione o botão **Configurar** e forneça os detalhes de configuração.

A imagem que se segue mostra o ecrã de configuração tanto em Jira como em Confluence:

![Ecrã de configuração plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL de metadados**: O URL para obter metadados da federação a partir de Azure AD.

* **Identificadores**: O URL que a Azure AD utiliza para validar a origem do pedido. Mapeia o elemento **identificador** em Azure AD. O plug-in obtém automaticamente este URL como domínio*\<https://:porta>*/.

* **URL de resposta**: O URL de resposta no seu fornecedor de identidade (IDP) que inicia o login SAML. Mapeia o elemento URL de **resposta** em Azure AD. O plug-in obtém automaticamente este URL como domínio*\<https://:porta>*/plugins/servlet/saml/auth.

* **Iniciar sessão url**: O URL de início de sessão no seu IDP que inicia o login SAML. Mapeia para o elemento **Sign On** em Azure AD. O plug-in obtém automaticamente este URL como domínio*\<https://:porta>*/plugins/servlet/saml/auth.

* **ID Id entidade ID**: O ID da entidade que o seu ID utiliza. Esta caixa é povoada quando o URL dos metadados é resolvido.

* URL de **login**: O URL de início de sessão do seu IDP. Esta caixa é povoada a partir de Azure AD quando o URL dos metadados é resolvido.

* URL de **logout**: O URL de logout do seu IDP. Esta caixa é povoada a partir de Azure AD quando o URL dos metadados é resolvido.

* **Certificado X.509**: Certificado X.509 do seu IDP. Esta caixa é povoada a partir de Azure AD quando o URL dos metadados é resolvido.

* Nome do **botão de início**de sessão : O nome do botão de início de sessão que a sua organização quer que os utilizadores vejam na página de início de sessão.

* Localizações de IDENTIFICAÇÃO do **Utilizador SAML**: A localização onde o ID do utilizador Jira ou Confluence é esperado na resposta SAML. Pode estar no **NameID** ou num nome de atributo personalizado.

* Nome do **atributo**: O nome do atributo onde se espera o ID do utilizador.

* **Enable Home Realm Discovery**: A seleção a fazer se a empresa estiver a utilizar o Ative Directory Federation Services (AD FS) com base no sign-in.

* **Nome do domínio**: O nome de domínio se o sign-in for baseado em AD FS.

* Ativar o **Signout Único**: A seleção a fazer se quiser assinar a partir de Azure AD quando um utilizador assinar a partir de Jira ou Confluence.

## <a name="troubleshooting"></a>Resolução de problemas

* **Está a receber vários erros**de certificado : Inscreva-se no Azure AD e remova os vários certificados disponíveis contra a app. Certifique-se de que apenas um certificado está presente.

* **Um certificado está prestes a expirar em Azure AD**: Add-ons cuidam da capotação automática do certificado. Quando um certificado está prestes a expirar, um novo certificado deve ser marcado ativo e os certificados não utilizados devem ser eliminados. Quando um utilizador tenta iniciar sessão na Jira neste cenário, o plug-in pega e guarda o novo certificado.

* **Desativar o WebSudo (desativar a sessão de administrador seguro)**:

  * Para a Jira, as sessões de administrador segura (isto é, a confirmação de palavra-passe antes de aceder às funções da administração) são ativadas por defeito. Se pretender remover esta capacidade na sua instância Jira, especifique a seguinte linha no seu ficheiro jira-config.properties:`ira.websudo.is.disabled = true`

  * Para confluência, siga os passos no site de apoio da [Confluência](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Os campos que supostamente são povoados pelo URL dos metadados não estão a ser povoados:**

  * Verifique se o URL está correto. Verifique se mapeou o inquilino e a identificação correta da aplicação.

  * Introduza o URL num browser e veja se recebe os metadados da federação XML.

* **Há um erro interno**do servidor: Reveja os registos no diretório de registo da instalação. Se estiver a ter o erro quando o utilizador está a tentar iniciar sessão utilizando o Azure AD SSO, pode partilhar os registos com a equipa de suporte.

* **Existe um erro de "ID do utilizador não encontrado" quando o utilizador tenta iniciar sessão**: Criar o ID do utilizador em Jira ou Confluence.

* **Há um erro "App não encontrado" no Azure AD**: Veja se o URL apropriado está mapeado para a aplicação em Azure AD.

* **Você precisa de apoio**: Contacte a Equipa de [Integração Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A equipa responde em 24-48 horas úteis.

  Também pode levantar um bilhete de suporte com a Microsoft através do canal portal Azure.

## <a name="plug-in-faq"></a>Plug-in FAQ

Consulte as FAQs abaixo se tiver alguma dúvida sobre este plug-in.

### <a name="what-does-the-plug-in-do"></a>O que faz o plug-in?

O plug-in fornece uma única capacidade de inscrição (SSO) para o Atlassian Jira (incluindo Jira Core, Jira Software, Jira Service Desk) e software Confluence on-pre-premissa. O plug-in trabalha com o Azure Ative Directory (Azure AD) como fornecedor de identidade (IDP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Com que produtos atlassianos funcionam?

O plug-in funciona com versões no local de Jira e Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>O plug-in funciona em versões em nuvem?

Não. O plug-in suporta apenas versões no local de Jira e Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Que versões de Jira e Confluence suportam o plug-in?

O plug-in suporta estas versões:

* Jira Core e Software: 6.0 a 7.12
* Mesa de Serviço Jira: 3.0.0 a 3.5.0
* Jira também suporta 5.2. Para mais detalhes, clique no [Microsoft Azure Ative Directory single sign-on para JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluência: 5.0 a 5.10
* Confluência: 6.0.1
* Confluência: 6.1.1
* Confluência: 6.2.1
* Confluência: 6.3.4
* Confluência: 6.4.0
* Confluência: 6.5.0
* Confluência: 6.6.2
* Confluência: 6.7.0
* Confluência: 6.8.1
* Confluência: 6.9.0
* Confluência: 6.10.0
* Confluência: 6.11.0
* Confluência: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>O plug-in é gratuito ou pago?

É um complemento gratuito.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Preciso reiniciar a Jira ou a Confluence depois de ligar o plug-in?

Não é necessário reiniciar. Pode começar a utilizar o plug-in imediatamente.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Como posso obter apoio para o plug-in?

Pode contactar a Equipa de [Integração Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) para qualquer suporte necessário para este plug-in. A equipa responde em 24-48 horas úteis.

Também pode levantar um bilhete de suporte com a Microsoft através do canal portal Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>O plug-in funcionaria numa instalação mac ou ubuntu de Jira e Confluence?

Testámos o plug-in apenas em instalações de 64 bits do Windows Server de Jira e Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>O plug-in funciona com idPs que não o Azure AD?

Não. Funciona apenas com a Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Com que versão da SAML funciona o plug-in?

Funciona com a SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>O plug-in faz o fornecimento do utilizador?

Não. O plug-in fornece apenas SSO baseado em SAML 2.0. O utilizador tem de ser aprovisionado na aplicação antes do sessão sso.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>As versões plug-in do cluster de jira e confluence?

Não. O plug-in funciona com versões no local de Jira e Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>O plug-in funciona com versões HTTP de Jira e Confluence?

Não. O plug-in funciona apenas com instalações ativadas por HTTPS.
