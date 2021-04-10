---
title: Gerir certificados da federação em Azure AD | Microsoft Docs
description: Saiba como personalizar a data de validade dos certificados da federação e como renovar certificados que em breve expirarão.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63054b18589cdcc2d950d70452b4f982c324a6c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604003"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerir certificados para inscrição única federada no Azure Ative Directory

Neste artigo, cobrimos questões e informações comuns relacionadas com certificados que o Azure Ative Directory (Azure AD) cria para estabelecer um único sign-on federado (SSO) para o seu software como aplicações de serviço (SaaS). Adicione aplicações na galeria de aplicações AZure AD ou utilizando um modelo de aplicação não-galeria. Configure a aplicação utilizando a opção SSO federada.

Este artigo é relevante apenas para aplicações configuradas para usar Azure AD SSO através da federação [de Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) de afirmação de segurança.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado por automático para aplicações de galeria e não galeria

Quando adiciona uma nova aplicação na galeria e configura um sign-on baseado em SAML (selecionando UM ÚNICO SINAL **DE**  >  **SNA** A PARTIR DA PÁGINA Geral da aplicação), a Azure AD gera um certificado para a aplicação que é válido por três anos. Para descarregar o certificado ativo como um ficheiro de certificado de segurança **(.cer),** volte a essa página **(sign-on baseado em SAML)** e selecione um link de descarregamento no título do Certificado de **Assinatura SAML.** Pode escolher entre o certificado bruto (binário) ou o certificado Base64 (texto codificado base 64). Para aplicações de galeria, esta secção também pode mostrar um link para descarregar o certificado como metadados da federação XML (um ficheiro **.xml),** dependendo da exigência da aplicação.

![Opções de descarregamento de certificado de assinatura ativa SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Também pode descarregar um certificado ativo ou inativo selecionando o ícone de **Edição** do Certificado de **Assinatura SAML** (um lápis), que exibe a página **do Certificado de Assinatura SAML.** Selecione a elipse **(...**) ao lado do certificado que pretende descarregar e, em seguida, escolha qual o formato de certificado que deseja. Tem a opção adicional de descarregar o certificado em formato de correio eletrónico (PEM) melhorado para a privacidade. Este formato é idêntico ao Base64 mas com uma extensão de nome de ficheiro **.pem,** que não é reconhecida no Windows como um formato de certificado.

![Opções de descarregamento de certificado de assinatura SAML (ativas e inativas)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalize a data de validade do certificado da federação e rebola-a para um novo certificado

Por predefinição, o Azure configura um certificado para expirar após três anos quando é criado automaticamente durante a configuração de entrada única da SAML. Porque não pode alterar a data de um certificado depois de guardá-lo, tem que:

1. Crie um novo certificado com a data desejada.
1. Guarde o novo certificado.
1. Faça o download do novo certificado no formato correto.
1. Faça o upload do novo certificado para o pedido.
1. Faça o novo certificado ativo no portal Azure Ative Directory.

As duas secções seguintes ajudam-no a executar estes passos.

### <a name="create-a-new-certificate"></a>Criar um novo certificado

Primeiro, crie e guarde novo certificado com uma data de validade diferente:

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/). Aparece a página **do centro de administração Azure Ative Directory.**
1. No painel esquerdo, selecione **Aplicações empresariais**. Aparece uma lista das aplicações empresariais na sua conta.
1. Selecione a aplicação afetada. Aparece uma página geral para a aplicação.
1. No painel esquerdo da página geral da aplicação, selecione **Single sign-on**.
1. Se aparecer uma única página **de método de inscrição,** selecione **SAML**.
1. Na **configuração single Sign-On com página DE pré-visualização SAML,** encontre o título **do Certificado de Assinatura SAML** e selecione o ícone **editar** (um lápis). Aparece a página **do Certificado de Assinatura SAML,** que apresenta o estado **(Ativo** ou **Inativo),** a data de validade e a impressão digital (uma corda de haxixe) de cada certificado.
1. Selecione **Novo Certificado**. Uma nova linha aparece abaixo da lista de certificados, onde a data de validade é exatamente três anos após a data atual. (As suas alterações ainda não foram guardadas, pelo que ainda pode modificar a data de validade.)
1. Na nova linha de certificado, paire sobre a coluna de data de validade e selecione o ícone **'Data Seletiva'** (um calendário). Aparece um controlo de calendário, exibindo os dias de um mês da data de validade atual da nova linha.
1. Utilize o controlo do calendário para definir uma nova data. Pode definir qualquer data entre a data atual e três anos após a data atual.
1. Selecione **Guardar**. O novo certificado aparece agora com um estado de **Inativo,** a data de validade que escolheu, e uma impressão digital.
1. Selecione o **X** para voltar à configuração single Sign-On com a página **DE pré-visualização SAML.**

### <a name="upload-and-activate-a-certificate"></a>Faça upload e ativar um certificado

Em seguida, faça o download do novo certificado no formato correto, faça-o para a aplicação e torne-o ativo no Diretório Ativo Azure:

1. Consulte as instruções adicionais de configuração de inscrição do SAML da aplicação:

   - Selecionando o link guia de **configuração** para ver numa janela ou separado do navegador ou separador, ou
   - Indo para a posição **de** configuração e selecionando **Ver as instruções passo a passo** para ver numa barra lateral.

1. Nas instruções, anote o formato de codificação necessário para o upload do certificado.
1. Siga as instruções no certificado gerado por si mesmo para a secção [de aplicações de galeria e não galeria](#auto-generated-certificate-for-gallery-and-non-gallery-applications) mais cedo. Este passo descarrega o certificado no formato de codificação necessário para o upload pela aplicação.
1. Quando quiser passar para o novo certificado, volte para a página **do Certificado de Assinatura SAML** e na linha de certificado recém-guardada, selecione a elipse (**...**) e selecione Faça o **certificado ativo**. O estado do novo certificado altera-se ao **Ative,** e o certificado anteriormente ativo altera-se a um estado de **Inativo**.
1. Continue seguindo as instruções de configuração de inscrição SAML da aplicação que apresentou anteriormente, para que possa carregar o certificado de assinatura SAML no formato de codificação correto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Adicione endereços de notificação de e-mail para expiração do certificado

A Azure AD enviará uma notificação por e-mail 60, 30 e 7 dias antes do termo do certificado SAML. Pode adicionar mais de um endereço de e-mail para receber notificações. Para especificar o endereço de e-mail(es) pretende que as notificações sejam enviadas para:

1. Na página do **Certificado de Assinatura SAML,** aceda ao título **de endereços de e-mail de notificação.** Por predefinição, esta rubrica utiliza apenas o endereço de e-mail do administrador que adicionou a aplicação.
1. Abaixo do endereço de e-mail final, digite o endereço de e-mail que deve receber o aviso de expiração do certificado e, em seguida, prima Enter.
1. Repita o passo anterior para cada endereço de e-mail que pretende adicionar.
1. Para cada endereço de e-mail que pretende eliminar, selecione o ícone **Eliminar** (uma lata de lixo) ao lado do endereço de e-mail.
1. Selecione **Guardar**.

Pode adicionar até 5 endereços de e-mail à lista de Notificações (incluindo o endereço de e-mail do administrador que adicionou a aplicação). Se precisar de mais pessoas para ser notificado, use os e-mails da lista de distribuição.

Receberá o e-mail de notificação de aadnotification@microsoft.com . Para evitar que o e-mail vá para a sua localização de spam, adicione este e-mail aos seus contactos.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que em breve expirará

Se um certificado estiver prestes a expirar, pode renová-lo usando um procedimento que não resulte em tempo de inatividade significativo para os seus utilizadores. Para renovar um certificado de caducidade:

1. Siga as instruções na secção [Criar um novo certificado](#create-a-new-certificate) mais cedo, utilizando uma data que se sobreponha ao certificado existente. Essa data limita o tempo de inatividade causado pela expiração do certificado.
1. Se o pedido puder passar automaticamente por cima de um certificado, desaceia o novo certificado de ativo seguindo estes passos:
   1. Volte para a página do **Certificado de Assinatura SAML.**
   1. Na linha de certificado recentemente guardada, selecione a elipse **(...**) e, em seguida, **selecione Faça o certificado ativo**.
   1. Salta os próximos dois passos.

1. Se a aplicação só conseguir manusear um certificado de cada vez, escolha um intervalo de tempo de inatividade para executar o próximo passo. (Caso contrário, se o pedido não levantar automaticamente o novo certificado mas conseguir lidar com mais do que um certificado de assinatura, pode realizar o próximo passo a qualquer momento.)
1. Antes que o certificado antigo expire, siga as instruções no [Upload e ative uma](#upload-and-activate-a-certificate) secção de certificado mais cedo.
1. Inscreva-se no pedido para se certificar de que o certificado funciona corretamente.

## <a name="related-articles"></a>Artigos relacionados

- [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)
- [Gestão de aplicações com o Azure Active Directory](what-is-application-management.md)
- [Inscrição única para aplicações no Azure Ative Directory](what-is-single-sign-on.md)
- [Depurar o início de sessão único baseado em SAML para as aplicações no Azure Active Directory](./debug-saml-sso-issues.md)
