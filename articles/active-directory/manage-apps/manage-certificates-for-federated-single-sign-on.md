---
title: Gerir certificados de Federação no Azure AD | Documentos da Microsoft
description: Saiba como personalizar a data de expiração para os certificados de Federação e como renovar certificados que irão expirar em breve.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecadb499d140ccfc993820080cae0b749977fc61
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824749"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerir certificados para início de sessão único federado no Azure Active Directory

Neste artigo, vamos abordar as questões comuns e informações relacionadas com certificados que o Azure Active Directory (Azure AD) cria para estabelecer único início de sessão Federado (SSO) com o seu software como um aplicativo de serviço (SaaS). Adicione aplicações de Galeria de aplicações do Azure AD ou utilizando um modelo de aplicativo externas à galeria. Configure a aplicação ao utilizar a opção de SSO federada.

Este artigo é relevante apenas para aplicações que estão configuradas para utilizar o SSO do Azure AD através de [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) Federação (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para as aplicações de galeria e externas à Galeria

Quando adicionar um novo aplicativo a partir da Galeria e configurar um SAML com base no início de sessão (selecionando **início de sessão único** > **SAML** da página de descrição geral da aplicação), o Azure AD gera um certificado para a aplicação que é válido durante três anos. Para transferir o certificado do Active Directory como um certificado de segurança (**. cer**) ficheiro, retorno a essa página (**baseado em SAML início de sessão**) e selecione uma ligação de transferência no **decertificadodeassinaturadeSAML** cabeçalho. Pode escolher entre o certificado não processado (binário) ou o certificado de (base 64 codificada texto) de Base64. Para aplicações de galeria, esta secção também poderá mostrar uma ligação para transferir o certificado de como metadados de Federação XML (uma **. XML** ficheiro), dependendo do requisito da aplicação.

![SAML active assinatura certificado opções de transferência](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Também pode transferir um certificado ativo ou inativo, selecionando o **certificado de assinatura SAML** do cabeçalho **editar** ícone (um lápis), que exibe o **decertificadodeassinaturadeSAML** página. Selecione as reticências (**...** ) junto ao certificado que pretende transferir e, em seguida, escolha qual formato de certificado que pretende. Tem a opção adicional para transferir o certificado no formato de correio avançado de privacidade (. PEM). Este formato é idêntico para Base64, mas com uma **. pem** extensão, que não é reconhecido no Windows como um formato de certificado.

![Opções de transferência do certificado (ativas e Inativas) de assinatura SAML](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar a data de expiração para o seu certificado de Federação e acumulam-lo para um novo certificado

Por predefinição, o Azure configura um certificado de expiração após três anos, quando é criado automaticamente durante a configuração SAML do única início de sessão. Uma vez que não é possível alterar a data de um certificado depois de o guardar, tem de:

1. Crie um novo certificado com a data pretendida.
2. Guarde o novo certificado.
3. Baixe o novo certificado no formato correto.
4. Carregar o novo certificado para a aplicação.
5. Ativar o novo certificado no portal do Azure Active Directory.

As duas secções seguintes ajudam a realizar estes passos.

### <a name="create-a-new-certificate"></a>Criar um novo certificado

Em primeiro lugar, crie e guarde o novo certificado com uma data de expiração diferentes:

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/). O **Centro de administração do Azure Active Directory** é apresentada a página.

2. No painel esquerdo, selecione **Aplicações empresariais**. É apresentada uma lista de aplicações empresariais na sua conta.

3. Selecione a aplicação afetada. É apresentada uma página de descrição geral para a aplicação.

4. No painel esquerdo da página de descrição geral do aplicativo, selecione **início de sessão único**.

5. Se o **selecionar um método de início de sessão único** for apresentada a página, selecione **SAML**.

6. Na **definir a segurança de início de sessão único com o SAML - pré-visualização** página, encontre o **certificado de assinatura SAML** cabeçalho e selecione o **editar** ícone (um lápis). O **certificado de assinatura SAML** é apresentada a página, que apresenta o estado (**Active** ou **Inactive**), data de expiração e thumbprint (uma cadeia de hash) de cada certificado.

7. Selecione **novo certificado**. Uma nova linha é apresentada abaixo da lista de certificados, em que a data de expiração padrão é exatamente três anos após a data atual. (As alterações ainda não foram guardadas, portanto, ainda é possível modificar a data de expiração.)

8. Na nova linha de certificado, coloque o cursor sobre a coluna de data de expiração e selecione o **selecionar data** ícone (calendário). É apresentada um controle de calendário, exibindo os dias de um mês da data de expiração atual a nova linha.

9. Use o controle de calendário para definir uma nova data. Pode definir qualquer data entre a data atual e a três anos após a data atual.

10. Selecione **Guardar**. O novo certificado aparece agora com o estado **Inactive**, a expiração de datas que escolha e um thumbprint.

11. Selecione o **X** para voltar para o **definir a segurança de início de sessão único com o SAML - pré-visualização** página.

### <a name="upload-and-activate-a-certificate"></a>Carregar e ativar um certificado

Em seguida, transferir o novo certificado no formato correto, carregue-o para a aplicação e ativá-lo no Azure Active Directory:

1. Veja a instruções de configuração de início de sessão SAML adicionais do aplicativo, qualquer um:
   - Selecionar o **guia de configuração** link para exibir uma janela do browser separado ou uma guia, ou
   - vai para o **configurar** cabeçalho e selecionando **ver instruções passo a passo** para exibir numa barra lateral.

2. Nas instruções, tenha em atenção o formato de codificação necessário para o carregamento do certificado.

3. Siga as instruções no [gerado automaticamente o certificado para aplicações de galeria e externas à galeria](#auto-generated-certificate-for-gallery-and-non-gallery-applications) secção anteriormente. Este passo transfere o certificado no formato de codificação necessário para carregamento para a aplicação.

4. Quando quiser acumulam para o novo certificado, volte para o **certificado de assinatura SAML** página e, na linha do certificado salvo recentemente, selecione as reticências (**...** ) e selecione **ativar o certificado**. O estado do novo certificado é alterado para **Active Directory**, e o certificado anteriormente ativo é alterado para um Estado de **Inactive**.

5. Continue a seguir instruções de configuração de início de sessão SAML do aplicativo que apresentado anteriormente, para que pode carregar o tipo de assinatura de SAML de certificado no formato de codificação correto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Adicionar endereços de notificação de e-mail de expiração do certificado

Azure AD irá enviar um dias de 7, 30 e 60 de notificação de e-mail antes de expira o certificado SAML. Pode adicionar mais do que um endereço de e-mail para receber notificações. Para especificar os endereços de e-mail que pretende que as notificações para ser enviado para:

1. Na **certificado de assinatura SAML** página, vá para o **endereços de e-mail de notificação** cabeçalho. Por predefinição, este título utiliza apenas o endereço de e-mail do administrador que adicionaram a aplicação.

2. Abaixo o endereço de e-mail final, escreva o endereço de e-mail que deve receber o aviso de expiração do certificado e, em seguida, prima Enter.

3. Repita o passo anterior para cada endereço de e-mail que pretende adicionar.

4. Para cada endereço de e-mail que pretende eliminar, selecione o **eliminar** ícone (uma lata de lixo) junto ao endereço de e-mail.

5. Selecione **Guardar**.

Irá receber o e-mail de notificação da aadnotification@microsoft.com. Para evitar o seu local de spam de e-mail, adicione este e-mail aos seus contatos.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que irá expirar em breve

Se um certificado está prestes a expirar, poderá renová-lo usando um procedimento que resulta em sem períodos de indisponibilidade significativo para os seus utilizadores. Para renovar um certificado prestes a expirar:

1. Siga as instruções no [criar um novo certificado](#create-a-new-certificate) secção anteriormente, com uma data que sobrepõe-se com o certificado existente. Essa data limita a quantidade de tempo de inatividade causado pela expiração do certificado.

2. Se o aplicativo pode fazer o roll ao longo de um certificado, defina o novo certificado para o Active Directory ao seguir estes passos:
   1. Volte para o **certificado de assinatura SAML** página.
   2. Na linha do certificado salvo recentemente, selecione as reticências (**...** ) e, em seguida, selecione **ativar o certificado**.
   3. Ignore os dois passos seguintes.

3. Se a aplicação só poderá lidar com um certificado ao mesmo tempo, escolha um intervalo de tempo de inatividade para executar o passo seguinte. (Caso contrário, se o aplicativo não obtenha automaticamente o novo certificado, mas pode lidar com mais de um certificado de assinatura, pode executar o passo seguinte em qualquer altura.)

4. Antes do antigo certificado expira, siga as instruções no [carregar e ativar um certificado](#upload-and-activate-a-certificate) secção anteriormente.

5. Inicie sessão para a aplicação para se certificar de que o certificado funciona corretamente.

## <a name="related-articles"></a>Artigos relacionados

* [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](../saas-apps/tutorial-list.md)
* [Gestão de aplicações no Azure Active Directory](what-is-application-management.md)
* [Início de sessão único para aplicações no Azure Active Directory](what-is-single-sign-on.md)
* [Depurar baseado em SAML início de sessão único para aplicações no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
