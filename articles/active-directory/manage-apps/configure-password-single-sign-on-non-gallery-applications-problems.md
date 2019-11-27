---
title: Problemas ao configurar o SSO de senha para aplicativos que não são da Galeria
description: Problemas comuns que ocorrem quando você configura o SSO (logon único) de senha para aplicativos personalizados que não estão na Galeria de aplicativos do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274138"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemas ao configurar o logon único com senha para um aplicativo inexistente na Galeria

Este artigo descreve problemas comuns que podem ocorrer quando você configura o SSO ( *logon único* ) de senha para um aplicativo inexistente na galeria.

## <a name="capture-sign-in-fields-for-an-app"></a>Capturar campos de entrada para um aplicativo

A captura de campo de entrada tem suporte apenas para páginas de entrada habilitadas para HTML. Não há suporte para páginas de entrada não padrão, como as que usam o Adobe Flash ou outras tecnologias não habilitadas para HTML.

Há duas maneiras de capturar os campos de entrada para seus aplicativos personalizados:

- A **captura automática do campo de entrada** funciona bem com a maioria das páginas de entrada habilitadas para HTML, *se usarem IDs de div conhecidos* para os campos nome de usuário e senha. O HTML na página é recortado para localizar IDs de DIV que correspondem a determinados critérios. Esses metadados são salvos para que possam ser reproduzidos para o aplicativo posteriormente.

- A **captura de campo de entrada manual** será usada se o fornecedor do aplicativo *não rotular os campos de entrada de conexão*. A captura manual também será usada se o fornecedor *renderizar vários campos que não podem ser detectados automaticamente*. O Azure Active Directory (AD do Azure) pode armazenar dados para o máximo de campos que houver na página de entrada, se você informar onde esses campos estão na página.

Em geral, se a captura automática do campo de entrada não funcionar, tente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capturar automaticamente os campos de entrada para um aplicativo

Para configurar o SSO baseado em senha usando a captura automática de campo de entrada, siga estas etapas:

1. Abra o [Portal do Azure](https://portal.azure.com/). Entre como um administrador global ou coadministrador.

2. No painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da lista **todos os aplicativos** . Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação no lado esquerdo.

8. Selecione o modo de **logon baseado em senha** .

9. Insira a **URL de logon**, que é a URL da página em que os usuários inserem seu nome de usuário e senha para entrar. *Certifique-se de que os campos de entrada estejam visíveis na página para a URL que você fornecer*.

10. Selecione **Guardar**.

    A página é automaticamente recortada para as caixas de entrada nome de usuário e senha. Agora você pode usar o Azure AD para transmitir com segurança senhas para esse aplicativo usando a extensão do navegador do painel de acesso.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturar manualmente os campos de entrada para um aplicativo

Para capturar manualmente os campos de entrada, você deve ter a extensão de navegador do painel de acesso instalada. Além disso, seu navegador não pode ser executado no modo *InPrivate*, *Incognito*ou *Private* .

Para instalar a extensão, consulte a seção [instalar a extensão do navegador do painel de acesso](#install-the-access-panel-browser-extension) deste artigo.

Para configurar o SSO baseado em senha para um aplicativo usando a captura de campo de entrada manual, siga estas etapas:

1. Abra o [Portal do Azure](https://portal.azure.com/). Entre como um administrador global ou coadministrador.

2. No painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE] 
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da lista **todos os aplicativos** . Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação no lado esquerdo.

8. Selecione o modo de **logon baseado em senha** .

9. Insira a **URL de logon**, que é a página em que os usuários inserem seu nome de usuário e senha para entrar. *Certifique-se de que os campos de entrada estejam visíveis na página para a URL que você fornecer*.

10. Selecione **configurar *&lt;o AppName&gt;* configurações de logon único de senha**.

11. Selecione **detectar manualmente os campos de entrada**.

14. Selecione **Ok**.

15. Selecione **Guardar**.

16. Siga as instruções para usar o painel de acesso.

## <a name="troubleshoot-problems"></a>Solucionar problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Recebo um erro "não foi possível encontrar nenhum campo de entrada na URL"

Você recebe essa mensagem de erro quando a detecção automática de campos de entrada falha. Para resolver o problema, experimente a detecção de campo de entrada manual. Consulte a seção [capturar manualmente os campos de entrada para um aplicativo](#manually-capture-sign-in-fields-for-an-app) deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Recebo um erro "não é possível salvar a configuração de logon único"

Raramente, a atualização da configuração de SSO falha. Para resolver esse problema, tente salvar a configuração novamente.

Se você continuar recebendo o erro, abra um caso de suporte. Inclua as informações descritas no [portal exibir detalhes da notificação](#view-portal-notification-details) e [Enviar detalhes da notificação para um engenheiro de suporte para obter ajuda](#send-notification-details-to-a-support-engineer-to-get-help) seções deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Não consigo detectar manualmente os campos de entrada para meu aplicativo

Você pode observar os seguintes comportamentos quando a detecção manual não está funcionando:

- O processo de captura manual parecia funcionar, mas os campos capturados não estão corretos.

- Os campos corretos não são realçados quando o processo de captura é executado.

- O processo de captura leva você para a página de entrada do aplicativo como esperado, mas nada acontece.

- A captura manual parece funcionar, mas o SSO não acontece quando os usuários navegam para o aplicativo no painel de acesso.

Se você tiver qualquer um desses problemas, faça o seguinte:

- Verifique se você tem a versão mais recente da extensão navegador do painel *de acesso instalada e habilitada*. Consulte a seção [instalar a extensão do navegador do painel de acesso](#install-the-access-panel-browser-extension) deste artigo.

- Verifique se o navegador não está no modo *Incognito*, *InPrivate*ou *Private* durante o processo de captura. A extensão do painel de acesso não tem suporte nesses modos.

- Verifique se os usuários não estão tentando entrar no aplicativo no painel de acesso enquanto estiver no modo *Incognito*, *InPrivate*ou *Private*.

- Tente o processo de captura manual novamente. Verifique se os marcadores vermelhos estão sobre os campos corretos.

- Se o processo de captura manual parecer parar de responder ou a página de entrada não responder, tente o processo de captura manual novamente. Mas, desta vez, depois de concluir o processo, pressione a tecla F12 para abrir o console do desenvolvedor do navegador. Selecione a guia **console** . digite **Window. location = " *&lt;a URL de entrada que você especificou ao configurar o aplicativo&gt;* "** e pressione Enter. Isso força um redirecionamento de página que encerra o processo de captura e armazena os campos que foram capturados.

### <a name="contact-support"></a>Contactar o suporte

Se você ainda tiver problemas, abra um caso com Suporte da Microsoft. Descreva o que você tentou. Inclua os detalhes descritos em [Exibir detalhes de notificação do portal](#view-portal-notification-details) e [Enviar detalhes da notificação a um engenheiro de suporte para obter](#send-notification-details-to-a-support-engineer-to-get-help) as seções de ajuda deste artigo (se aplicável).

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do painel de acesso

Siga estes passos.

1. Abra o [painel de acesso](https://myapps.microsoft.com) em um navegador com suporte. Entre no Azure AD como um *usuário*.

2. Selecione o **aplicativo senha-SSO** no painel de acesso.

3. Quando for solicitado a instalar o software, selecione **instalar agora**.

4. Você será direcionado para uma página de download para seu navegador. Escolha **Adicionar** a extensão.

5. Se for solicitado, selecione **habilitar** ou **permitir**.

6. Após a instalação, reinicie o navegador.

7. Entre no painel de acesso. Veja se você pode abrir seus aplicativos habilitados para SSO de senha.

Você também pode baixar diretamente a extensão do navegador para Chrome e Firefox por meio destes links:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Exibir detalhes de notificação do portal

Para ver os detalhes de qualquer notificação do portal, siga estas etapas:

1. Selecione o ícone de **notificações** (o Bell) no canto superior direito do portal do Azure.

2. Selecione qualquer notificação que mostre um estado de *erro* . (Eles têm um "!" vermelho.)

   > [!NOTE]
   > Não é possível selecionar notificações que estejam no estado *bem-sucedido* ou *em andamento* .

3. O painel **detalhes da notificação** é aberto. Leia as informações para saber mais sobre o problema.

5. Se você ainda precisar de ajuda, compartilhe as informações com um engenheiro de suporte ou o grupo de produtos. Selecione o ícone de **cópia** à direita da caixa de **erro de cópia** para copiar os detalhes de notificação a serem compartilhados.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Enviar detalhes de notificação para um engenheiro de suporte para obter ajuda

É importante que você compartilhe *todos* os detalhes listados nesta seção com suporte para que eles possam ajudá-lo rapidamente. Para gravá-lo, você pode fazer uma captura de tela ou selecionar **erro de cópia**.

As informações a seguir explicam o que significa cada item de notificação e fornece exemplos.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

- **Título**: o título descritivo da notificação.

   Exemplo: *configurações de proxy de aplicativo*

- **Descrição**: o que ocorreu como resultado da operação.

   Exemplo: *a URL interna inserida já está sendo usada por outro aplicativo.*

- **ID da notificação**: a ID exclusiva da notificação.

    Exemplo: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID de solicitação do cliente**: a ID de solicitação específica que seu navegador fez.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Carimbo de data/hora UTC**: o carimbo de hora de quando a notificação ocorreu, em UTC.

    Exemplo: *2017-03-23T19:50:43.7583681 z*

- **ID da transação interna**: a ID interna que é usada para pesquisar o erro em nossos sistemas.

    Exemplo: **71a2f329-ca29-402F-AA72-bc00a7aca603**

- **UPN**: o usuário que executou a operação.

    Exemplo: *tperkins\@f128.info*

- **ID do locatário**: a ID exclusiva do locatário do qual o usuário que executou a operação é membro.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID de objeto de usuário**: a ID exclusiva do usuário que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

- **Nome de exibição**: (pode estar vazio) um nome de exibição mais detalhado para o erro.

    Exemplo: *configurações de proxy de aplicativo*

- **Status**: o status específico da notificação.

    Exemplo: *falha*

- **ID do objeto**: (pode estar vazio) a ID de objeto na qual a operação foi executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Detalhes**: a descrição detalhada do que ocorreu como resultado da operação.

    Exemplo: a *URL interna '<https://bing.com/>' é inválida porque já está em uso.*

- **Erro de cópia**: permite que você selecione o **ícone de cópia** à direita da caixa de texto **copiar erro** para copiar os detalhes de notificação para ajudar com o suporte.

    Exemplo: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passos seguintes
[Fornecer logon único para seus aplicativos com o proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
