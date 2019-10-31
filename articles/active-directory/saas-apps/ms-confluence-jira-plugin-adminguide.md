---
title: Guia do administrador do Atlassian JIRA/Confluence-Azure Active Directory | Microsoft Docs
description: Guia do administrador para usar o Atlassian JIRA e o Confluence com o Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161203"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guia de administração do Atlassian JIRA e Confluence para Azure Active Directory

## <a name="overview"></a>Visão geral

O plug-in de SSO (logon único) do Azure Active Directory (Azure AD) permite que Microsoft Azure AD clientes usem sua conta corporativa ou de estudante para entrar em produtos baseados no Atlassian JIRA e no Confluence Server. Ele implementa o SSO baseado em SAML 2,0.

## <a name="how-it-works"></a>Como funciona

Quando os usuários desejarem entrar no aplicativo Atlassian JIRA ou Confluence, eles verão o botão **logon com o Azure ad** na página de entrada. Quando eles o selecionam, eles são solicitados a entrar usando a página de entrada da organização do Azure AD (ou seja, sua conta corporativa ou de estudante).

Depois que os usuários são autenticados, eles devem ser capazes de entrar no aplicativo. Se eles já estiverem autenticados com a ID e a senha de sua conta corporativa ou de estudante, eles entrarão diretamente no aplicativo. 

A entrada funciona em Jira e Confluence. Se os usuários estiverem conectados ao aplicativo Jira e o Confluence for aberto na mesma janela do navegador, eles não precisarão fornecer as credenciais para o outro aplicativo. 

Os usuários também podem acessar o produto Atlassian por meio de meus aplicativos na conta corporativa ou de estudante. Eles devem ser conectados sem serem solicitados a fornecer credenciais.

> [!NOTE]
> O provisionamento do usuário não é feito por meio do plug-in do.

## <a name="audience"></a>Audiência

Os administradores do JIRA e do Confluence podem usar o plug-in para habilitar o SSO usando o Azure AD.

## <a name="assumptions"></a>Pressupostos

* As instâncias Jira e Confluence são habilitadas para HTTPS.
* Os usuários já estão criados em JIRA ou Confluence.
* Os usuários têm funções atribuídas em JIRA ou Confluence.
* Os administradores têm acesso às informações necessárias para configurar o plug-in.
* JIRA ou Confluence também está disponível fora da rede da empresa.
* O plug-in funciona apenas com a versão local do JIRA e do Confluence.

## <a name="prerequisites"></a>Pré-requisitos

Observe as seguintes informações antes de instalar o plug-in:

* Jira e Confluence são instalados em uma versão do Windows de 64 bits.
* As versões Jira e Confluence são habilitadas para HTTPS.
* Jira e Confluence estão disponíveis na Internet.
* As credenciais de administrador estão em vigor para Jira e Confluence.
* As credenciais de administrador estão em vigor para o Azure AD.
* O websudo está desabilitado em Jira e Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versões com suporte do JIRA e do Confluence

O plug-in do oferece suporte às seguintes versões do JIRA e Confluence:

* JIRA Core e software: 6,0 a 7,12
* JIRA Service Desk: 3.0.0 a 3.5.0
* O JIRA também dá suporte a 5,2. Para obter mais detalhes, clique em [Microsoft Azure Active Directory logon único para JIRA 5,2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5,0 a 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Instalação

Para instalar o plug-in, siga estas etapas:

1. Entre em sua instância do JIRA ou Confluence como administrador.

2. Vá para o console de administração do JIRA/Confluence e selecione **Complementos**.

3. No centro de download da Microsoft, baixe o plugin de [SSO do Microsoft SAML para Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [o plugin de SSO do Microsoft SAML para Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   A versão apropriada do plug-in é exibida nos resultados da pesquisa.

4. Selecione o plug-in e o Gerenciador de plug-in universal (UPM) o instala.

Depois que o plug-in é instalado, ele aparece na seção **complementos instalados pelo usuário** de **Gerenciar Complementos**.

## <a name="plug-in-configuration"></a>Configuração de plug-in

Antes de começar a usar o plug-in, você deve configurá-lo. Selecione o plug-in, selecione o botão **Configurar** e forneça os detalhes de configuração.

A imagem a seguir mostra a tela de configuração em Jira e Confluence:

![Tela de configuração de plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL de metadados**: a URL para obter metadados de Federação do Azure AD.

* **Identificadores**: a URL que o AD do Azure usa para validar a origem da solicitação. Ele é mapeado para o elemento **identificador** no Azure AD. O plug-in deriva automaticamente essa URL como https:// *\<domain: port >* /.

* **URL de resposta**: a URL de resposta no seu provedor de identidade (IDP) que inicia a entrada SAML. Ele é mapeado para o elemento **URL de resposta** no Azure AD. O plug-in deriva automaticamente essa URL como https:// *\<domain: port >* /plugins/servlet/SAML/auth.

* **URL de logon**: a URL de logon no IDP que inicia a entrada SAML. Ele é mapeado para o elemento **Sign on** no Azure AD. O plug-in deriva automaticamente essa URL como https:// *\<domain: port >* /plugins/servlet/SAML/auth.

* **ID da entidade IDP**: a ID da entidade que seu IDP usa. Essa caixa é preenchida quando a URL de metadados é resolvida.

* **URL de logon**: a URL de entrada do IDP. Essa caixa é preenchida do Azure AD quando a URL de metadados é resolvida.

* **URL de logout**: a URL de logout do IDP. Essa caixa é preenchida do Azure AD quando a URL de metadados é resolvida.

* **Certificado x. 509**: o certificado x. 509 do IDP. Essa caixa é preenchida do Azure AD quando a URL de metadados é resolvida.

* **Nome do botão de logon**: o nome do botão de entrada que sua organização deseja que os usuários vejam na página de entrada.

* **Locais de ID de usuário do SAML**: o local em que a ID de usuário JIRA ou Confluence é esperada na resposta SAML. Ele pode estar em **NameID** ou em um nome de atributo personalizado.

* **Nome do atributo**: o nome do atributo em que a ID de usuário é esperada.

* **Habilitar a descoberta de realm inicial**: a seleção a ser feita se a empresa estiver usando a entrada baseada em Serviços de Federação do Active Directory (AD FS) (AD FS).

* **Nome de domínio**: o nome de domínio se a entrada for baseada AD FS.

* **Habilitar o logon único**: a seleção a ser feita se você quiser sair do Azure ad quando um usuário sair do JIRA ou Confluence.

## <a name="troubleshooting"></a>Resolução de problemas

* **Você está recebendo vários erros de certificado**: entre no Azure AD e remova os vários certificados que estão disponíveis no aplicativo. Certifique-se de que apenas um certificado esteja presente.

* **Um certificado está prestes a expirar no Azure ad**: os complementos cuidam da substituição automática do certificado. Quando um certificado está prestes a expirar, um novo certificado deve ser marcado como ativo e os certificados não utilizados devem ser excluídos. Quando um usuário tenta entrar no JIRA nesse cenário, o plug-in busca e salva o novo certificado.

* **Você deseja desabilitar o websudo (desabilitar a sessão de administrador seguro)** :

  * Para Jira, as sessões de administrador seguro (isto é, confirmação de senha antes de acessar funções de administração) são habilitadas por padrão. Se você quiser remover essa capacidade em sua instância do JIRA, especifique a seguinte linha em seu arquivo JIRA-config. Properties: `ira.websudo.is.disabled = true`

  * Para Confluence, siga as etapas no [site de suporte do Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Os campos que devem ser preenchidos pela URL de metadados não estão sendo populados**:

  * Verifique se a URL está correta. Verifique se você mapeou o locatário e a ID do aplicativo corretos.

  * Insira a URL em um navegador e veja se você recebeu o XML de metadados de Federação.

* **Há um erro de servidor interno**: examine os logs no diretório de log da instalação. Se estiver recebendo o erro quando o usuário estiver tentando entrar usando o SSO do Azure AD, você poderá compartilhar os logs com a equipe de suporte.

* **Há um erro de "ID de usuário não encontrado" quando o usuário tenta entrar**: Crie a ID de usuário em JIRA ou Confluence.

* **Há um erro "aplicativo não encontrado" no Azure ad**: Veja se a URL apropriada está mapeada para o aplicativo no Azure AD.

* **Você precisa de suporte**: alcance a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A equipe responde em 24-48 horas de negócios.

  Você também pode gerar um tíquete de suporte com a Microsoft por meio do canal portal do Azure.

## <a name="plug-in-faq"></a>Perguntas frequentes sobre plug-in

Consulte as perguntas frequentes abaixo se tiver alguma consulta sobre esse plug-in.

### <a name="what-does-the-plug-in-do"></a>O que o plug-in faz?

O plug-in fornece o recurso SSO (logon único) para Atlassian JIRA (incluindo JIRA Core, JIRA software, JIRA Service Desk) e o software local do Confluence. O plug-in funciona com o Azure Active Directory (AD do Azure) como um IdP (provedor de identidade).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Com quais produtos Atlassian o plug-in funciona?

O plug-in funciona com as versões locais do JIRA e do Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>O plug-in funciona em versões de nuvem?

Não. O plug-in dá suporte apenas a versões locais do JIRA e do Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>A quais versões do JIRA e do Confluence o plug-in oferece suporte?

O plug-in dá suporte a essas versões:

* JIRA Core e software: 6,0 a 7,12
* JIRA Service Desk: 3.0.0 a 3.5.0
* O JIRA também dá suporte a 5,2. Para obter mais detalhes, clique em [Microsoft Azure Active Directory logon único para JIRA 5,2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5,0 a 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>O plug-in é gratuito ou pago?

É um complemento gratuito.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>É necessário reiniciar o JIRA ou o Confluence depois de implantar o plug-in?

Uma reinicialização não é necessária. Você pode começar a usar o plug-in imediatamente.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Como fazer obter suporte para o plug-in?

Você pode entrar em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) para qualquer suporte necessário para esse plug-in. A equipe responde em 24-48 horas de negócios.

Você também pode gerar um tíquete de suporte com a Microsoft por meio do canal portal do Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>O plug-in funcionaria em uma instalação de Mac ou Ubuntu do JIRA e do Confluence?

Testamos o plug-in somente em instalações do Windows Server de 64 bits do JIRA e do Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>O plug-in funciona com IdPs diferente do Azure AD?

Não. Ele funciona apenas com o Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Com qual versão do SAML o plug-in funciona?

Ele funciona com o SAML 2,0.

### <a name="does-the-plug-in-do-user-provisioning"></a>O plug-in faz o provisionamento do usuário?

Não. O plug-in fornece apenas SSO baseado em SAML 2,0. O usuário deve ser provisionado no aplicativo antes da entrada de SSO.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>O plug-in dá suporte a versões de cluster do JIRA e Confluence?

Não. O plug-in funciona com as versões locais do JIRA e do Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>O plug-in funciona com as versões HTTP do JIRA e do Confluence?

Não. O plug-in funciona apenas com instalações habilitadas para HTTPS.
