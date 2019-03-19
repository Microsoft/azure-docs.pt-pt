---
title: Guia do administrador do Atlassian Jira/confluência - Azure Active Directory | Documentos da Microsoft
description: Guia do administrador para utilizar o Atlassian Jira e confluência com o Azure Active Directory (Azure AD)....
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a674748e9e95828436df25222f6daad881e0487
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109142"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Guia do administrador do Atlassian Jira e confluência para o Azure Active Directory

## <a name="overview"></a>Descrição geral

O Azure Active Directory (Azure AD) início de sessão único (SSO) Plug-in permite que os clientes do Microsoft Azure AD utilizar a respetiva conta escolar ou profissional para iniciar sessão no Atlassian Jira e produtos baseados em servidor confluência. Ele implementa baseado em SAML 2.0 SSO.

## <a name="how-it-works"></a>Como funciona

Quando os utilizadores querem entrar para a aplicação Atlassian Jira ou confluência, consulte a **início de sessão com o Azure AD** botão na página de início de sessão. Quando estes selecionarem-,-lhes pedido que inicie sessão com a página do Azure AD organização início de sessão (ou seja, sua conta escolar ou profissional).

Depois dos utilizadores são autenticados, deverá conseguir iniciar sessão na aplicação. Se já estão autenticados com o ID e palavra-passe para a respetiva conta escolar ou profissional, em seguida, iniciar sessão diretamente no aplicativo. 

Início de sessão a funciona em Jira e confluência. Se os utilizadores com sessão iniciados aplicativo Jira e confluência é aberta na mesma janela de browser, não precisam fornecer as credenciais para outra aplicação. 

Os usuários também podem obter o produto da Atlassian por meio de minhas aplicações sob a conta escolar ou profissional. Eles devem estar conectados sem a ser pedidas credenciais.

> [!NOTE]
> Aprovisionamento de utilizadores não é efetuado através do plug-in.

## <a name="audience"></a>Audiência

Jira e confluência os administradores podem utilizar o plug-in para ativar o SSO ao utilizar o Azure AD.

## <a name="assumptions"></a>Suposições

* As instâncias no Jira e confluência estão ativadas por HTTPS.
* Os utilizadores já são criados no Jira ou confluência.
* Os utilizadores têm funções atribuídas no Jira ou confluência.
* Os administradores têm acesso às informações necessárias para configurar o plug-in.
* Jira ou confluência está disponível fora da rede da empresa.
* O plug-in funciona com apenas a versão no local do Jira e confluência.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar o plug-in, tenha em atenção as seguintes informações:

* Jira e confluência são instalados numa versão do Windows de 64 bits.
* Versões de Jira e confluência estão ativadas por HTTPS.
* Jira e confluência estão disponíveis na internet.
* Credenciais de administrador são para Jira e confluência.
* Credenciais de administrador são in-loco para o Azure AD.
* WebSudo está desativada no Jira e confluência.

## <a name="supported-versions-of-jira-and-confluence"></a>Versões suportadas do Jira e confluência

O plug-in-suporta as seguintes versões do Jira e confluência:

* Núcleo do Jira e Software: 6.0 para 7.12
* Jira Service Desk: 3.0.0 para 3.5.0
* JIRA também suporta 5.2. Para obter mais detalhes, clique em [Microsoft Azure Active Directory início de sessão único para JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluência: 5.0 para 5.10
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

1. Iniciar sessão na sua instância do Jira ou confluência como administrador.

2. Aceda à consola de administração do Jira/confluência e selecione **suplementos**.

3. Do Microsoft Download Center, transfira o [Microsoft SAML SSO Plug-in para Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO Plug-in para confluência](https://www.microsoft.com/download/details.aspx?id=56503).

   A versão adequada do plug-in é apresentado nos resultados da pesquisa.

4. Selecione o plug-in e instala-o o Gestor de plug-in Universal (UPM).

Depois do plug-in estiver instalado, ele aparece no **suplementos instalados do utilizador** secção **gerir suplementos**.

## <a name="plug-in-configuration"></a>Configuração de plug-in

Antes de começar a utilizar o plug-in, tem de o configurar. Selecione a Plug-in, selecione o **configurar** botão e forneça os detalhes de configuração.

A imagem seguinte mostra a tela de configuração no Jira e confluência:

![Ecrã de configuração de plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **URL de metadados**: O URL para obter metadados de Federação do Azure AD.

* **Identificadores**: O URL que o Azure AD utiliza para validar a origem do pedido. Mapeia para o **identificador** elemento no Azure AD. O plug-in automaticamente deriva este URL como https://*< domínio: porta >*/.

* **URL de resposta**: O URL de resposta no seu fornecedor de identidade (IdP), que inicia o início de sessão no SAML. Mapeia para o **URL de resposta** elemento no Azure AD. O plug-in automaticamente deriva este URL como https://*< domínio: porta >*/plugins/servlet/saml/auth.

* **Inicie sessão no URL**: O URL início de sessão na sua IdP que inicia o início de sessão no SAML. Mapeia para o **sessão** elemento no Azure AD. O plug-in automaticamente deriva este URL como https://*< domínio: porta >*/plugins/servlet/saml/auth.

* **ID de entidade de IdP**: O ID de entidade que o IdP utiliza. Esta caixa é preenchida quando o URL de metadados é resolvido.

* **URL de início de sessão**: O URL de início de sessão do seu IdP. Esta caixa é preenchida a partir do Azure AD quando o URL de metadados é resolvido.

* **URL de fim de sessão**: O URL de fim de sessão do seu IdP. Esta caixa é preenchida a partir do Azure AD quando o URL de metadados é resolvido.

* **Certificado X.509**: Certificado X.509 do IdP. Esta caixa é preenchida a partir do Azure AD quando o URL de metadados é resolvido.

* **Nome do botão de início de sessão**: O nome do botão início de sessão que a organização quer que os utilizadores vejam na página de início de sessão.

* **Localizações de ID de utilizador SAML**: A localização onde o ID de utilizador no Jira ou confluência é esperado na resposta SAML. Pode ter **NameID** ou um nome de atributo personalizado.

* **Nome de atributo**: O nome do atributo onde é esperado o ID de utilizador.

* **Deteção de Realm inicial de Enable**: A seleção fazer se a empresa estiver a utilizar os serviços de Federação do Active Directory (AD FS) - com base em sessão - na.

* **Nome de domínio**: O nome de domínio se início de sessão é do AD FS com base.

* **Ativar a fim de sessão único**: A seleção para que se pretende terminar a sessão do Azure AD quando um utilizador termina sessão Jira ou confluência.

## <a name="troubleshooting"></a>Resolução de problemas

* **Esteja tirando vários erros de certificado**: Inicie sessão para o Azure AD e remover os vários certificados que estão disponíveis em relação a aplicação. Certifique-se de que apenas um certificado estiver presente.

* **Um certificado está prestes a expirar no Azure AD**: Suplementos cuidam de rollover automático do certificado. Quando um certificado está prestes a expirar, um novo certificado deve ser marcado como ativo e não utilizados certificados devem ser eliminados. Quando um utilizador tenta iniciar sessão no Jira neste cenário, as plug-ins buscas e guarda o novo certificado.

* **Pretende desativar WebSudo (desativar a sessão de administrador seguras)**:

  * Para Jira, sessões de seguro de administrador (ou seja, confirmação de palavra-passe antes de acessar as funções de administração) estão ativadas por predefinição. Se pretende remover esta capacidade na sua instância do Jira, especifica a seguinte linha no ficheiro jira-Config. Properties: `ira.websudo.is.disabled = true`

  * Para confluência, siga os passos a [site de suporte de confluência](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Campos que devem ser preenchidos pelo URL de metadados não são obtenção preenchidos**:

  * Verifique se o URL está correto. Verifique se mapeou a ID de inquilino e a aplicação correta

  * Introduza o URL num browser e veja se receber o XML de metadados de Federação.

* **Existe um erro de servidor interno**: Reveja os registos no diretório de registo da instalação. Se estiver recebendo o erro quando o utilizador está a tentar iniciar sessão com o SSO do Azure AD, pode partilhar os registos com a equipa de suporte.

* **Existe um erro de "ID de utilizador não foi encontrada" quando o utilizador tentar iniciar sessão**: Crie o ID de utilizador no Jira ou confluência.

* **Existe um erro de "Aplicação não foi encontrada" no Azure AD**: Veja se o URL adequado é mapeado para a aplicação no Azure AD.

* **Precisa de suporte**: Entrar em contacto com o [equipa de integração do SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). A equipe responde em 24-48 horas de expediente.

  Também pode fazer um pedido de suporte com a Microsoft por meio do canal de portal do Azure.

## <a name="plug-in-faq"></a>FAQ de plug-in

Veja abaixo FAQs se tiver qualquer consulta sobre este plug-in.

### <a name="what-does-the-plug-in-do"></a>O que faz o plug-in fazer?

O plug-in fornece a capacidade de início de sessão único (SSO) para Atlassian Jira (incluindo o Jira Core, Jira Software, serviço de atendimento de Jira) e software do confluência no local. O plug-in funciona com o Azure Active Directory (Azure AD) como fornecedor de identidade (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Quais produtos da Atlassian faz o trabalho de plug-in com?

O plug-in funciona com as versões no local do Jira e confluência.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>A Plug-in funciona em versões de cloud?

Não. O plug-in suporta apenas no local as versões do Jira e confluência.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Quais versões do Jira e confluência é que o suporte de plug-in?

O plug-in oferece suporte a essas versões:

* Núcleo do Jira e Software: 6.0 para 7.12
* Jira Service Desk: 3.0.0 para 3.5.0
* JIRA também suporta 5.2. Para obter mais detalhes, clique em [Microsoft Azure Active Directory início de sessão único para JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluência: 5.0 para 5.10
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

### <a name="is-the-plug-in-free-or-paid"></a>É o plug-in gratuito ou pago?

É um complemento gratuito.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>É necessário reiniciar o Jira ou confluência depois posso implementar o plug-in?

Não é necessário um reinício. Pode começar a utilizar o plug-in imediatamente.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Como posso obter suporte para o plug-in?

Pode entrar para o [equipa de integração do SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) para qualquer suporte necessária para este plug-in. A equipe responde em 24-48 horas de expediente.

Também pode fazer um pedido de suporte com a Microsoft por meio do canal de portal do Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Seria o trabalho de plug-in numa instalação Mac ou Ubuntu do Jira e confluência?

Nós o Testamos o plug-in apenas em instalações do Windows Server de 64 bits do Jira e confluência.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>A Plug-in funciona com IdPs diferente do Azure AD?

Não. Ele funciona apenas com o Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Qual a versão de SAML faz o trabalho de plug-in com?

Funciona com o SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>O plug-in faz o aprovisionamento de utilizadores?

Não. O plug-in fornece apenas o SSO baseado em SAML 2.0. O utilizador tem de ser aprovisionado no aplicativo antes do início de sessão-in do SSO.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>É que as versões de cluster de suporte de plug-in do Jira e confluência?

Não. O plug-in funciona com as versões no local do Jira e confluência.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>A Plug-in funciona com versões HTTP do Jira e confluência?

Não. O plug-in funciona com HTTPS ativado apenas em instalações.
