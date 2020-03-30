---
title: Gerir certificados da federação em Azure AD [ Microsoft Docs
description: Saiba como personalizar a data de validade dos certificados da sua federação e como renovar os certificados que em breve expirarão.
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
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159034"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerir certificados para inscrição única federada no Diretório Ativo azure

Neste artigo, cobrimos questões e informações comuns relacionadas com certificados que o Azure Ative Directory (Azure AD) cria para estabelecer uma única inscrição federada (SSO) no seu software como aplicações de serviço (SaaS). Adicione aplicações na galeria de aplicações Azure AD ou utilizando um modelo de aplicação não-galeria. Configure a aplicação utilizando a opção SSO federada.

Este artigo é relevante apenas para apps que estão configuradas para usar O SSO Azure AD através da federação de Linguagem de Marcação de [Afirmação](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) de Segurança (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para aplicações de galerias e não galerias

Quando adiciona uma nova aplicação da galeria e configura um sign-on baseado em SAML (selecionando**Um SAML** de **inscrição** > única na página geral da aplicação), a Azure AD gera um certificado para a aplicação que é válido por três anos. Para descarregar o certificado ativo como certificado de segurança **(ficheiro .cer),** volte a essa página **(saml-based sign-on**) e selecione um link de descarregamento na rubrica Certificado de **Assinatura SAML.** Pode escolher entre o certificado bruto (binário) ou o certificado Base64 (texto codificado base 64). Para aplicações de galerias, esta secção também pode mostrar um link para descarregar o certificado como metadados da federação XML (um ficheiro **.xml),** dependendo da exigência da aplicação.

![Opções de descarregamento de certificadode assinatura ativa SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Também pode descarregar um certificado ativo ou inativo selecionando o ícone **editar** do certificado de **assinatura SAML** (um lápis), que apresenta a página de Certificado de **Assinatura SAML.** Selecione a elipse (**...)** ao lado do certificado que pretende descarregar e, em seguida, escolha qual o formato de certificado que deseja. Tem a opção adicional de descarregar o certificado em formato de correio reforçado pela privacidade (PEM). Este formato é idêntico ao Base64 mas com uma extensão de nome de ficheiro **.pem,** que não é reconhecida no Windows como um formato de certificado.

![Opções de descarregamento de certificados de assinatura SAML (ativa e inativa)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalize a data de validade do seu certificado da federação e entregue-o a um novo certificado

Por predefinição, o Azure configura um certificado para expirar após três anos quando é criado automaticamente durante a configuração de inscrição única saml. Porque não se pode alterar a data de um certificado depois de o guardar, tem de:

1. Crie um novo certificado com a data desejada.
1. Guarde o novo certificado.
1. Descarregue o novo certificado no formato correto.
1. Faça o upload do novo certificado para o pedido.
1. Ative Directy do Novo Certificado.

As duas secções seguintes ajudam-no a executar estes passos.

### <a name="create-a-new-certificate"></a>Criar um novo certificado

Primeiro, crie e guarde um novo certificado com uma data de validade diferente:

1. Inscreva-se no [portal de Diretório Ativo Azure.](https://aad.portal.azure.com/) Aparece a página do centro de **administração do Azure Ative Directory.**
1. No painel esquerdo, selecione **Aplicações empresariais**. Aparece uma lista dos pedidos da empresa na sua conta.
1. Selecione a aplicação afetada. Aparece uma página geral da aplicação.
1. No painel esquerdo da página geral da aplicação, selecione **Single sign-on**.
1. Se aparecer uma única página de método de **sinalização,** selecione **SAML**.
1. Na configuração de um único sign-on com a página **SAML - Pré-visualização,** encontre a rubrica do Certificado de **Assinatura SAML** e selecione o ícone **Editar** (um lápis). Aparece a página do Certificado de **Assinatura SAML,** que apresenta o estado (**Ativo** ou **Inativo),** data de validade e impressão digital (uma cadeia de hash) de cada certificado.
1. Selecione **Novo Certificado**. Uma nova linha aparece abaixo da lista de certificados, onde a data de validade não termina exatamente três anos após a data atual. (As suas alterações ainda não foram guardadas, pelo que ainda pode modificar a data de validade.)
1. Na nova linha de certificado, paire sobre a coluna da data de validade e selecione o ícone **Select Date** (um calendário). Aparece um controlo de calendário, mostrando os dias de um mês da data de validade atual da nova linha.
1. Utilize o controlo do calendário para definir uma nova data. Pode definir qualquer data entre a data atual e três anos após a data atual.
1. Selecione **Guardar**. O novo certificado aparece agora com um estatuto de **Inative,** a data de validade que escolheu, e uma impressão digital.
1. Selecione o **X** para voltar ao **set up Single Sign-On com a página SAML - Pré-visualização.**

### <a name="upload-and-activate-a-certificate"></a>Carregar e ativar um certificado

Em seguida, descarregue o novo certificado no formato correto, carregue-o para a aplicação e torne-o ativo no Diretório Ativo do Azure:

1. Consulte as instruções adicionais de configuração de saml da aplicação:

   - Selecionando o link de guia de **configuração** para visualizar numa janela ou separado separado do separado do navegador, ou
   - Indo para a posição **configurada** e selecionando **ver instruções passo a passo** para ver numa barra lateral.

1. Nas instruções, note o formato de codificação necessário para o upload do certificado.
1. Siga as instruções no certificado gerado automaticamente para a secção de [aplicações](#auto-generated-certificate-for-gallery-and-non-gallery-applications) de galerias e não galerias mais cedo. Este passo descarrega o certificado no formato de codificação necessário para o upload pela aplicação.
1. Quando pretender passar para o novo certificado, volte à página de Certificado de **Assinatura SAML** e na linha de certificado recém-guardada, selecione a elipse (**...**) e selecione Certifique-se de que o certificado **está ativo**. O estado do novo certificado altera-se ao **Ativo,** e o certificado anteriormente ativo altera-se a um estatuto de **Inativo.**
1. Continue a seguir as instruções de configuração de sessão SAML da aplicação que apresentou anteriormente, para que possa carregar o certificado de assinatura SAML no formato de codificação correto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Adicionar endereços de notificação de e-mail para expiração do certificado

A Azure AD enviará uma notificação por e-mail 60, 30 e 7 dias antes do certificado SAML expirar. Pode adicionar mais de um endereço de e-mail para receber notificações. Para especificar o endereço de e-mail(es) pretende que as notificações sejam enviadas para:

1. Na página de Certificado de **Assinatura SAML, dirija-se** à rubrica de endereços de **e-mail de notificação.** Por predefinição, esta rubrica utiliza apenas o endereço de e-mail do administrador que adicionou a aplicação.
1. Abaixo do endereço de e-mail final, digite o endereço de e-mail que deve receber o aviso de validade do certificado e, em seguida, prima Enter.
1. Repita o passo anterior para cada endereço de e-mail que pretende adicionar.
1. Para cada endereço de e-mail que pretende eliminar, selecione o ícone **Eliminar** (um caixote do lixo) ao lado do endereço de e-mail.
1. Selecione **Guardar**.

Receberá o e-mail aadnotification@microsoft.comde notificação de . Para evitar que o e-mail vá ao seu local de spam, adicione este e-mail aos seus contactos.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que em breve expirará

Se um certificado estiver prestes a expirar, pode renová-lo usando um procedimento que não resulte em tempo de inatividade significativo para os seus utilizadores. Para renovar um certificado de caducidade:

1. Siga as instruções na secção [Criar um novo certificado](#create-a-new-certificate) mais cedo, utilizando uma data que se sobreponha ao certificado existente. Essa data limita o período de inatividade causado pela caducidade do certificado.
1. Se a aplicação puder passar automaticamente por cima de um certificado, detetete o novo certificado para ativo seguindo estas etapas:
   1. Volte para a página de certificado de **assinatura SAML.**
   1. Na linha de certificado recém-guardada, selecione a elipse (**...**) e, em seguida, selecione Certifique-se de **que o certificado está ativo**.
   1. Salta os próximos dois passos.

1. Se a aplicação só conseguir manusear um certificado de cada vez, escolha um intervalo de tempo de inatividade para realizar o próximo passo. (Caso contrário, se o pedido não pegar automaticamente no novo certificado, mas conseguir manusear mais do que um certificado de assinatura, pode realizar o próximo passo a qualquer momento.)
1. Antes de expirar o certificado antigo, siga as instruções no [Upload e ative uma](#upload-and-activate-a-certificate) secção de certificado mais cedo.
1. Inscreva-se no pedido para se certificar de que o certificado funciona corretamente.

## <a name="related-articles"></a>Artigos relacionados

- [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)
- [Gestão de aplicações com o Microsoft Azure Active Directory](what-is-application-management.md)
- [Inscrição única para candidaturas no Diretório Ativo do Azure](what-is-single-sign-on.md)
- [Debug SAML com base em assinaturaúnica para aplicações no Diretório Ativo Azure](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
