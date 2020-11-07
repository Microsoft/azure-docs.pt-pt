---
title: Guia de administração Atlassian Jira/Confluence - Azure Ative Directory Microsoft Docs
description: Guia de administração para usar Atlassian Jira e Confluence com Azure Ative Directory (Azure AD)..
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8e73ea3650e631bed277ab95092b714eef7596d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359162"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guia de administração Atlassian Jira e Confluence para o Azure Ative Directory

## <a name="overview"></a>Descrição geral

O plug-in de assinatura única (SSO) do Azure Ative Directory (Azure AD) permite que os clientes da Microsoft Azure AD utilizem a sua conta de trabalho ou escola para iniciar sessão nos produtos baseados em Atlassian Jira e Confluence Server. Implementa sSO baseado em SAML 2.0.

## <a name="how-it-works"></a>Como funciona

Quando os utilizadores querem iniciar sessão na aplicação Atlassian Jira ou Confluence, vêem o botão **Login com Azure AD** na página de login. Quando o selecionam, são obrigados a inscreverem-se utilizando a página de inscrição da organização Azure AD (isto é, o seu trabalho ou conta escolar).

Depois de autenticados os utilizadores, deverão poder inscrever-se na aplicação. Se já estão autenticados com o ID e a palavra-passe para o seu trabalho ou conta escolar, então eles assinam diretamente a aplicação. 

As inscrições atravessam a Jira e a Confluência. Se os utilizadores estiverem inscritos na aplicação Jira e a Confluence for aberta na mesma janela do navegador, não terá de fornecer as credenciais para a outra app. 

Os utilizadores também podem chegar ao produto Atlassian através das Minhas Apps no âmbito do trabalho ou da conta escolar. Devem ser assinados sem que lhes seja pedido credenciais.

> [!NOTE]
> O fornecimento do utilizador não é feito através do plug-in.

## <a name="audience"></a>Audiência

Os administradores da Jira e da Confluence podem utilizar o plug-in para ativar o SSO utilizando o Azure AD.

## <a name="assumptions"></a>Pressupostos

* As instâncias Jira e Confluence estão ativadas em HTTPS.
* Os utilizadores já são criados em Jira ou Confluence.
* Os utilizadores têm funções atribuídas em Jira ou Confluence.
* Os administradores têm acesso às informações necessárias para configurar o plug-in.
* Jira ou Confluence também estão disponíveis fora da rede da empresa.
* O plug-in funciona apenas com a versão no local de Jira e Confluence.

## <a name="prerequisites"></a>Pré-requisitos

Note as seguintes informações antes de instalar o plug-in:

* Jira e Confluence estão instalados numa versão do Windows 64 bits.
* As versões Jira e Confluence estão ativadas em HTTPS.
* Jira e Confluence estão disponíveis na internet.
* As credenciais de administração estão no lugar de Jira e Confluence.
* As credenciais de administração estão no lugar para a Azure AD.
* WebSudo está desativado em Jira e Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versões apoiadas de Jira e Confluência

O plug-in suporta as seguintes versões de Jira e Confluence:

* Jira Core e Software: 6.0 a 7.12
* Balcão de Serviço Jira: 3.0.0 a 3.5.0
* JIRA também suporta 5.2. Para mais detalhes, clique [no Microsoft Azure Ative Directory para o JIRA 5.2](./jira52microsoft-tutorial.md)
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

1. Inscreva-se na sua instância Jira ou Confluência como administradora.

2. Vá à consola de administração Jira/Confluence e selecione **Add-ons**.

3. A partir do Microsoft Download Center, descarregue o [Plugin SSO da Microsoft SAML para Jira](https://www.microsoft.com/download/details.aspx?id=56506) /  [Microsoft SAML SSO Plugin para Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   A versão adequada do plug-in aparece nos resultados da pesquisa.

4. Selecione o plug-in e o Universal Plug-in Manager (UPM) instala-o.

Depois de o plug-in ser instalado, aparece na secção **de Add-ons instalados** pelo utilizador de **'Gerir Add-ons'**.

## <a name="plug-in-configuration"></a>Configuração plug-in

Antes de começar a utilizar o plug-in, tem de o configurar. Selecione o plug-in, selecione o botão **Configurar** e forneça os detalhes da configuração.

A imagem a seguir mostra o ecrã de configuração tanto em Jira como confluência:

![Ecrã de configuração plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL metadados** : O URL para obter metadados da federação do Azure AD.

* **Identificadores** : O URL que a Azure AD utiliza para validar a origem do pedido. Mapeia para o elemento **identificador** em Azure AD. O plug-in deriva automaticamente este URL como https:// *\<domain:port>* /.

* **URL de resposta** : O URL de resposta no seu fornecedor de identidade (IdP) que inicia o início do sº de saúde DOL. Mapeia para o elemento **URL de resposta** em Azure AD. O plug-in deriva automaticamente este URL como https:// *\<domain:port>* /plugins/servlet/saml/auth.

* **Sinal em URL** : O URL de inscrição no seu IdP que inicia o início do súps DE SDOL. Ele mapeia para o elemento **Sign On** em Azure AD. O plug-in deriva automaticamente este URL como https:// *\<domain:port>* /plugins/servlet/saml/auth.

* **IdP Entity ID** : A entidade ID que o seu IdP utiliza. Esta caixa é povoada quando o URL dos metadados é resolvido.

* **URL de login** : O URL de entrada do seu IdP. Esta caixa é povoada a partir de Azure AD quando o URL de metadados é resolvido.

* **URL logout** : O URL logout do seu IdP. Esta caixa é povoada a partir de Azure AD quando o URL de metadados é resolvido.

* **Certificado X.509** : O certificado X.509 do seu IdP. Esta caixa é povoada a partir de Azure AD quando o URL de metadados é resolvido.

* **Início de Sessão Nome do botão** : O nome do botão de início de sessão que a sua organização quer que os utilizadores vejam na página de início de sessão.

* **Localizações do ID do utilizador SAML** : O local onde o ID do utilizador Jira ou Confluence é esperado na resposta SAML. Pode estar no **NameID** ou num nome de atributo personalizado.

* **Nome do atributo** : O nome do atributo onde se espera o ID do utilizador.

* **Enable Home Realm Discovery** : A seleção a fazer se a empresa estiver a utilizar o Ative Directory Federation Services (AD FS)baseado em sind.

* **Nome do domínio** : O nome de domínio se o srach-in for baseado em FS AD.

* **Ativar o Signout Único** : A seleção a fazer se quiser assinar a partir do Azure AD quando um utilizador assinar fora de Jira ou Confluence.

## <a name="troubleshooting"></a>Resolução de problemas

* **Está a obter vários erros** de certificado: Inscreva-se no Azure AD e remova os vários certificados disponíveis contra a app. Certifique-se de que só está presente um certificado.

* **Um certificado está prestes a expirar em Azure AD** : Os add-ons cuidam da capotagem automática do certificado. Quando um certificado está prestes a expirar, um novo certificado deve ser marcado ativo e os certificados não reutilizados devem ser eliminados. Quando um utilizador tenta entrar na Jira neste cenário, o plug-in recaem e guarda o novo certificado.

* **Pretende desativar o WebSudo (desative a sessão de administrador seguro)** :

  * Para jira, as sessões de administrador segura (isto é, confirmação de senha antes de aceder às funções de administração) são ativadas por padrão. Se pretender remover esta capacidade no seu caso Jira, especifique a seguinte linha no seu ficheiro jira-config.properties: `jira.websudo.is.disabled = true`

  * Para a Confluence, siga os passos no [local de apoio à Confluência.](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)

* **Os campos que supostamente devem ser povoados pelo URL dos metadados não estão a ser povoados:**

  * Verifique se o URL está correto. Verifique se mapeou o inquilino correto e o ID da aplicação.

  * Insira o URL num browser e veja se recebe os metadados da federação XML.

* **Há um erro do servidor interno:** Reveja os registos no diretório de registos da instalação. Se estiver a obter o erro quando o utilizador está a tentar iniciar sessão utilizando o Azure AD SSO, pode partilhar os registos com a equipa de suporte.

* **Existe um erro de "ID do utilizador não encontrado" quando o utilizador tenta iniciar sessão:** Criar o ID do utilizador em Jira ou Confluence.

* **Existe um erro de "App não encontrado" no Azure AD** : Veja se o URL apropriado está mapeado para a aplicação em Azure AD.

* **Precisa de apoio** : Contacte a [Equipa de Integração Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A equipa responde em 24-48 horas úteis.

  Também pode levantar um bilhete de suporte com a Microsoft através do canal do portal Azure.

## <a name="plug-in-faq"></a>Plug-in FAQ

Consulte as PERGUNTAS Frequentes abaixo se tiver alguma dúvida sobre este plug-in.

### <a name="what-does-the-plug-in-do"></a>O que faz o plug-in?

O plug-in fornece uma única capacidade de acesso (SSO) para o software Atlassian Jira (incluindo Jira Core, Jira Software, Jira Service Desk) e software Confluence no local. O plug-in funciona com o Azure Ative Directory (Azure AD) como fornecedor de identidade (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Com que produtos atlassianos funcionam o plug-in?

O plug-in funciona com versões no local de Jira e Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>O plug-in funciona em versões em nuvem?

Não. O plug-in suporta apenas versões no local de Jira e Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Que versões da Jira e da Confluence suportam o plug-in?

O plug-in suporta estas versões:

* Jira Core e Software: 6.0 a 7.12
* Balcão de Serviço Jira: 3.0.0 a 3.5.0
* JIRA também suporta 5.2. Para mais detalhes, clique [no Microsoft Azure Ative Directory para o JIRA 5.2](./jira52microsoft-tutorial.md)
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

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Tenho de reiniciar a Jira ou a Confluence depois de colocar o plug-in?

Não é necessário reiniciar. Pode começar a utilizar o plug-in imediatamente.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Como consigo apoio para o plug-in?

Pode contactar a [Equipa de Integração Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) para qualquer suporte necessário para este plug-in. A equipa responde em 24-48 horas úteis.

Também pode levantar um bilhete de suporte com a Microsoft através do canal do portal Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>O plug-in funcionaria numa instalação Mac ou Ubuntu de Jira e Confluence?

Testámos o plug-in apenas em instalações do Windows Server de 64 bits de Jira e Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>O plug-in funciona com IDPs para além do Azure AD?

Não. Só funciona com a Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Com que versão da SAML funciona o plug-in?

Funciona com SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>O plug-in faz o fornecimento do utilizador?

Não. O plug-in fornece apenas SSO baseado em SAML 2.0. O utilizador tem de ser apresentado na aplicação antes da sso.in.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>As versões de suporte plug-in da Jira e Confluence?

Não. O plug-in funciona com versões no local de Jira e Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>O plug-in funciona com versões HTTP da Jira e Confluence?

Não. O plug-in funciona apenas com instalações ativadas por HTTPS.