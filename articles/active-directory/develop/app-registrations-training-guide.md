---
title: Registos de aplicações no manual de treinamento de portal do Azure - Azure
description: Crie fluxos de autenticação incorporado e sem navegador com a concessão do código de dispositivo.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870121"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Guia de treinamento: Registos de aplicações no portal do Azure  

Pode encontrar diversos aprimoramentos na nova [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) experiência no portal do Azure. Se estiver mais familiarizado com a experiência legada, utilize este guia de treinamento para começar a utilizar a nova experiência.

## <a name="key-changes"></a>Principais alterações

- Registos de aplicações não estão limitados a que está a ser um uma **aplicação/API web** ou uma **nativo** aplicação. Pode utilizar o mesmo registo de aplicações para todos esses registrando-se os respetivos URIs de redirecionamento.
- As aplicações de experiência legada suportada que inicie sessão no organizacional (Azure AD) contas apenas. Aplicações que foram registadas como inquilino individual (suportam contas organizacionais apenas do diretório a aplicação foi registrada no) e pode ser modificado para ser o multi-inquilino (suportam todas as contas organizacionais). A nova experiência permite-lhe registar aplicações que podem suportar ambas as essas opções, bem como uma terceira opção: todas as contas organizacionais, bem como contas Microsoft pessoais.
- A experiência legada só estava disponível quando iniciar sessão no portal do Azure com uma conta institucional. Com a nova experiência, pode usar contas Microsoft pessoais, que não estão associadas um diretório.

## <a name="list-of-applications"></a>Lista de aplicações

- A nova lista de aplicações mostra aplicações que foram registadas através da aplicação legada experiência de registos no portal do Azure (as aplicações que iniciam a sessão de contas do Azure AD), bem como às aplicações registadas no entanto a [Portal de registo de aplicação](https://apps.dev.microsoft.com/) (as aplicações que iniciam no Azure AD e as contas Microsoft pessoais).
- A nova lista de aplicações não tem um **tipo de aplicação** coluna (uma vez que um registo de aplicação única pode ser vários tipos) e tem duas colunas adicionais: um **criada no** coluna e um **certificados & segredos** coluna que mostra o estado (atuais, prestes a expirar ou expirado) de credenciais que foram registadas na aplicação.

## <a name="new-app-registration"></a>Novo registo de aplicação

A experiência legada, para registar uma aplicação foram terá de fornecer: **Nome**, **tipo de aplicação**, e **início de sessão do URL/Redirect URI**. As aplicações que foram criadas foram aplicações de inquilino único apenas do Azure AD que significa que apenas suportado contas institucionais do diretório que a aplicação foi registrada no.

Na nova experiência, tem de indicar um **Name** para a aplicação e escolha o **tipos de conta suportados**. Opcionalmente, pode fornecer uma **URI de redirecionamento**. Se fornecer um URI de redirecionamento, terá de especificar se é web/público (ambiente de trabalho e móvel). Para obter mais informações sobre como registar uma aplicação com os registos de aplicações nova experiência, consulte [este guia de introdução](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>A página de propriedades de legado

A experiência legada tinha uma **propriedades** página que não possui a nova experiência. O **propriedades** painel tinha os seguintes campos: **Nome**, **ID do objeto**, **ID da aplicação**, **App ID URI**, **logótipo**, **URL da Home page** , **URL de fim de sessão**, **termos do URL de serviço**, **URL da declaração de privacidade**, **tipo de aplicação**, e  **Multi-inquilino.**

É aqui onde é possível encontrar a funcionalidade equivalente na nova experiência:

- **Nome**, **logótipo**, **URL da Home page**, **termos de URL do serviço**, e **URL da declaração de privacidade** está agora no daaplicação **Imagem corporativa** página.
- **ID de objeto** e **ID da aplicação (cliente)** está ativada a **descrição geral** página.
- A funcionalidade controlada pelos **multi-inquilino** alternância na experiência de legado foi substituída por **tipos de conta suportados** no **autenticação** página. Para obter mais informações sobre como o multi-inquilino mapeada para as opções de tipo de conta suportados, consulte [este guia de introdução](quickstart-modify-supported-accounts.md).
- **URL de fim de sessão** está agora no **autenticação** página.
- **Tipo de aplicação** já não é um campo válido. Em vez disso, os URIs de redirecionamento (que pode ser encontrado no **autenticação** página) determinar que tipos de aplicação são suportados.
- **URI de ID de aplicação** é agora designada **URI de ID de aplicação** e pode encontrá-lo no **expor uma API** painel. Na experiência legada, esta propriedade foi registrado automaticamente com o seguinte formato: `https://{tenantdomain}/{appID}` (por exemplo, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). No novo formato, é gerado automaticamente como `api://{appID}`, mas têm de ser guardadas explicitamente.

## <a name="reply-urlsredirect-urls"></a>URLs/redirecionamento URls de resposta

Na experiência legada, um aplicativo tinha uma **URLs de resposta** página. Na nova experiência, URLs de resposta podem ser encontrados num aplicativo **autenticação** secção. Além disso, eles são denominados **URIs de redirecionamento**. Além disso, o formato para redirecionamento URIs mudou. Eles têm de estar associado um tipo de aplicação (web ou público). Além disso, por motivos de segurança, os esquemas de carateres universais e http:// não são suportados (com exceção do http://localhost).

## <a name="keyscertificates--secrets"></a>Certificados/chaves e segredos

Na experiência legada, um aplicativo tinha **chaves** página. Na nova experiência, foi alterado para **certificados e segredos**. Além disso, **chaves públicas** são denominados **certificados** e **palavras-passe** são referidas como **segredos de cliente**.

## <a name="required-permissionsapi-permissions"></a>Permissões necessárias permissões/API

- Na experiência legada, um aplicativo tinha uma **permissões obrigatórias** página. Na nova experiência, foi alterado para **permissões API**.
- Ao selecionar uma API na experiência de legado, pode escolher de uma lista pequena de APIs da Microsoft ou de pesquisa por meio de principais de serviço no inquilino. Na nova experiência, pode escolher entre vários separadores: **APIs da Microsoft**, **APIs minha organização utiliza**, ou **as minhas APIs**. Na barra de pesquisa **APIs minha organização** utiliza o separador procura principais de serviço no inquilino. 

   > [!NOTE]
   > Não verá este separador se seu aplicativo não está associado a um inquilino. Para mais informações sobre como pedir permissões com a nova experiência, veja [este guia de introdução](quickstart-configure-app-access-web-apis.md).

- A experiência legada tinha uma **conceder permissões** botão na parte superior a **solicitadas permissões** página. Na nova experiência, existe uma **conceder autorização** secção com um **conceder autorização de administrador** botão num aplicativo **permissões de API** secção. Além disso, existem algumas diferenças das formas a função de botões:
   - Na experiência de legado, a lógica diversificados consoante o utilizador com sessão iniciada e as permissões que está a ser solicitadas. A lógica era:
      - Se apenas permissões capaz de consentimento do utilizador foram solicitadas e o utilizador com sessão iniciada não era um administrador, o utilizador foi capaz de conceder autorização de utilizador para as permissões pedidas.
      - Se, pelo menos, uma permissão que necessita de consentimento de administrador foi solicitada e o utilizador com sessão iniciada não era um administrador, o utilizador recebeu um erro durante a tentativa de conceder autorização.
      - Se o utilizador com sessão iniciada é administrador, o consentimento de administrador foi concedido para todas as permissões pedidas.
   - Na nova experiência, apenas um administrador pode conceder autorização. Quando seleciona um administrador do **conceder autorização de administrador** botão, o consentimento de administrador é concedido a todas as permissões pedidas.

## <a name="deleting-an-app-registration"></a>A eliminar um registo de aplicações

Na experiência legada, uma aplicação precisava ser inquilino único para serem eliminados. O botão Eliminar foi desativado para aplicações multi-inquilino. Na nova experiência, as aplicações podem ser eliminadas em qualquer Estado, mas tem de confirmar a ação. Para obter mais informações sobre como eliminar registos de aplicações, consulte [este guia de introdução](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifesto de aplicação

As experiências de novas e legadas utilizam versões diferentes para o formato do JSON no editor de manifesto. Para mais informações, veja [manifesto do aplicativo](reference-app-manifest.md).

## <a name="new-ui"></a>Nova interface do Usuário

Há uma nova interface do Usuário para propriedades que foi anteriormente apenas ser definido utilizando o editor de manifesto ou a API ou não existe.

- **Fluxo de concessão implícita** (oauth2AllowImplicitFlow) pode ser encontrado no **autenticação** página. Ao contrário da experiência legada, pode habilitar **tokens de acesso** ou **tokens de id**, ou ambos.
- **Âmbitos definidos por esta API** (oauth2Permissions) e **autorizado a aplicações de cliente** (preAuthorizedApplications) pode ser configurada através da **expor uma API** página. Para obter mais informações sobre como configurar uma aplicação para ser uma API web e expor/âmbitos de permissões, consulte [este guia de introdução](quickstart-configure-app-expose-web-apis.md).
- **Domínio de publicador** (que é apresentado aos utilizadores no [solicitação de consentimento da aplicação](application-consent-experience.md)) pode ser encontrado no **marca painel** página. Para mais informações sobre como configurar um domínio de publicador, veja [nesta explicação de procedimento](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limitações

A nova experiência tem as seguintes limitações:

- A nova experiência não está atualmente disponível em inquilinos do Azure AD B2C.
- O formato de segredos de cliente (palavras-passe de aplicação) é diferente do que a experiência legada e quebra da CLI.
- Alterar o valor para contas suportadas não é suportado na interface do Usuário. Tem de utilizar o manifesto da aplicação, a menos que estar a mudar entre o Azure AD do inquilino único e multi-inquilino.
