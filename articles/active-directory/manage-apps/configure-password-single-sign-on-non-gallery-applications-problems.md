---
title: Problemas ao configurar a palavra-passe SSO para um aplicativo de externas à Galeria | Documentos da Microsoft
description: Problemas comuns que ocorrem quando configurar a palavra-passe início de sessão único (SSO) para aplicações que não estão na Galeria de aplicações do Azure AD.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440359"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemas ao configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria

Este artigo descreve problemas comuns que podem ocorrer quando configura *palavra-passe início de sessão único* (SSO) para uma aplicação de externas à galeria.

## <a name="capture-sign-in-fields-for-an-app"></a>Recolher campos de início de sessão para uma aplicação

Captura de campo de início de sessão é suportada apenas para habilitados por HTML páginas de início de sessão. Ele não deu suporte por não-início de sessão páginas padrão, como os que utilizam Adobe Flash ou outras tecnologias não-habilitados por HTML.

Existem duas formas de recolher campos de início de sessão para as suas aplicações personalizadas:

- **Captura de campo de início de sessão automático** funciona bem com a maioria das habilitados por HTML início de sessão páginas, *se utilizarem o IDs de DIV bem conhecidos* para os campos de nome e palavra-passe do utilizador. O HTML da página é extraído para localizar os IDs de DIV que correspondem a determinados critérios. Esses metadados é guardado para que ele pode ser reproduzido para a aplicação mais tarde.

- **Captura de campo de início de sessão manual** é utilizado se o fornecedor da aplicação *não etiquetar os campos de entrada início de sessão*. Captura manual também é utilizada se o fornecedor *renderiza vários campos que não podem ser detetado automaticamente*. Azure Active Directory (Azure AD) pode armazenar os dados para os campos que existem na página de início de sessão, se mandar onde esses campos estão na página.

Em geral, se o campo de início de sessão automático capturar não funcionar, experimente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Recolher campos de início de sessão para uma aplicação automaticamente

Para configurar o SSO baseado em palavra-passe com a captura de campo de início de sessão automático, siga estes passos:

1. Abra o [Portal do Azure](https://portal.azure.com/). Inicie sessão como um administrador global ou coadministrador.

2. No painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todos os aplicativos** lista. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel de navegação no lado esquerdo.

8. Selecione **baseado em palavra-passe de início de sessão** modo.

9. Introduza o **URL de início de sessão**, que é o URL da página onde os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão. *Certifique-se de que os campos de início de sessão estão visíveis na página para o URL que fornecer*.

10. Selecione **Guardar**.

    A página automaticamente é extraída para as caixas de entrada nome e palavra-passe de utilizador. Agora pode utilizar o Azure AD para transmitir palavras-passe para essa aplicação de forma segura utilizando a extensão de browser do painel de acesso.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturar manualmente os campos de início de sessão para uma aplicação

Para capturar os campos de início de sessão manualmente, tem de ter a extensão de browser do painel de acesso instalada. Além disso, não pode estar em seu navegador *inPrivate*, *incognito*, ou *privada* modo.

Para instalar a extensão, consulte a [instalar a extensão de Browser do painel de acesso](#install-the-access-panel-browser-extension) seção deste artigo.

Para configurar o SSO baseado em palavra-passe para uma aplicação com a captura de campo de início de sessão manual, siga estes passos:

1. Abra o [Portal do Azure](https://portal.azure.com/). Inicie sessão como um administrador global ou coadministrador.

2. No painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE] 
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todos os aplicativos** lista. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel de navegação no lado esquerdo.

8. Selecione **baseado em palavra-passe de início de sessão** modo.

9. Introduza o **URL de início de sessão**, que é a página onde os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão. *Certifique-se de que os campos de início de sessão estão visíveis na página para o URL que fornecer*.

10. Selecione **configurar *&lt;appname&gt;* definições de início de sessão único de palavra-passe**.

11. Selecione **manualmente detetar campos de início de sessão**.

14. Selecione **Ok**.

15. Selecione **Guardar**.

16. Siga as instruções para utilizar o painel de acesso.

## <a name="troubleshoot-problems"></a>Resolução de problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Recebo uma mensagem de erro "Não foi possível encontrar quaisquer campos de início de sessão neste URL"

Receber esta mensagem de erro quando ocorre uma falha de deteção automática de campos de início de sessão. Para resolver o problema, experimente a deteção de campo de início de sessão manual. Consulte a [capturar manualmente os campos de início de sessão para uma aplicação](#manually-capture-sign-in-fields-for-an-app) seção deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Posso obter uma "não é possível guardar a configuração de início de sessão única" erro

Raramente, a atualizar a configuração de SSO falha. Para resolver este problema, tente guardar a configuração novamente.

Se continuar a receber o erro, abra um incidente de suporte. Inclua as informações que estão descritas com o [ver os detalhes de notificação do portal](#view-portal-notification-details) e [enviar detalhes de notificação para um engenheiro de suporte para obter ajuda](#send-notification-details-to-a-support-engineer-to-get-help) seções deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Manualmente, não é possível detetar campos de início de sessão para a minha aplicação

Poderá reparar os comportamentos seguintes quando a deteção manual não está a funcionar:

- O processo de captura manual parecia funcionar, mas os campos capturados não corretos.

- Os campos corretos não obter realçados quando o processo de captura é executado.

- O processo de captura leva-o para a página de início de sessão da aplicação conforme esperado, mas não acontece nada.

- Captura manual parece funcionar, mas o SSO não acontece quando os utilizadores navegam para a aplicação do painel de acesso.

Se tiver qualquer um desses problemas, efetue os seguintes procedimentos:

- Certifique-se de que tem a versão mais recente da extensão de browser do painel de acesso *instalado e ativado*. Consulte a [instalar a extensão de browser do painel de acesso](#install-the-access-panel-browser-extension) seção deste artigo.

- Certifique-se de que o seu browser não está no *incognito*, *inPrivate*, ou *privada* modo durante o processo de captura. A extensão do painel de acesso não é suportada nesses modos.

- Certifique-se de que os utilizadores não estão tentando iniciar sessão aplicação do painel de acesso ao mesmo tempo no *incognito*, *inPrivate*, ou *modo privado*.

- Repita o processo de captura manual. Certifique-se de que os marcadores vermelhos são sobre os campos corretos.

- Se o processo de captura manual parece deixe de responder ou a página de início de sessão não responder, tente novamente o processo de captura manual. Mas, desta vez, depois de concluir o processo, prima a tecla F12 para abrir a consola de programador do seu browser. Selecione o **consola** separador. Tipo **window.location= " *&lt;o URL de início de sessão que especificou quando configurar a aplicação&gt;* "** , e, em seguida, prima Enter. Isso força uma página de redirecionamento que termina o processo de captura e armazena os campos que foram capturados.

### <a name="contact-support"></a>Contacte o suporte

Se ainda tiver problemas, abra um caso Support da Microsoft. Descreva o que tentou. Incluir os detalhes que são descritos a [ver os detalhes de notificação do portal](#view-portal-notification-details) e [enviar detalhes de notificação para um engenheiro de suporte para obter ajuda](#send-notification-details-to-a-support-engineer-to-get-help) seções deste artigo (se aplicável).

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão de browser do painel de acesso

Siga estes passos.

1. Open [painel de acesso](https://myapps.microsoft.com) num browser suportado. Inicie sessão no Azure AD como um *utilizador*.

2. Selecione **aplicação SSO de palavra-passe** no painel de acesso.

3. Quando lhe for pedido para instalar o software, selecione **instalar agora**.

4. Será redirecionado para uma página de transferência para o seu navegador. Optar por **adicionar** a extensão.

5. Se lhe for pedido, selecione **habilitar** ou **permitir**.

6. Após a instalação, reinicie o navegador.

7. Inicie sessão no painel de acesso. Veja se é possível abrir as suas aplicações com capacidade de SSO de palavra-passe.

Diretamente também pode transferir a extensão do browser para o Chrome e Firefox através destas ligações:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Ver detalhes de notificação do portal

Para ver os detalhes de qualquer notificação do portal, siga estes passos:

1. Selecione o **notificações** ícone (na campainha) no canto superior direito do portal do Azure.

2. Selecione qualquer notificação que mostra uma *erro* estado. (Eles têm um vermelho "!".)

   > [!NOTE]
   > Não é possível selecionar notificações que se encontrem no *Successful* ou *em curso* estado.

3. O **detalhes de notificação** painel abre-se. Leia as informações para saber mais sobre o problema.

5. Se ainda precisar de ajuda, partilhe as informações com um engenheiro de suporte ou o grupo de produtos. Selecione o **cópia** ícone à direita do **copiar erro** caixa para copiar os detalhes de notificação para partilhar.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Enviar detalhes de notificação para um engenheiro de suporte para obter ajuda

É importante que partilham *todos os* os detalhes que são apresentados nesta secção com suporte para que eles podem ajudá-lo a rapidamente. Para registrá-lo, pode tirar uma captura de ecrã ou selecione **copiar erro**.

As seguintes informações explicam o que cada item de notificação significa e fornece exemplos.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

- **Título**: o título descritivo da notificação.

   Exemplo: *Definições de proxy da aplicação*

- **Descrição**: o que ocorreu em resultado da operação.

   Exemplo: *O URL interno introduzido já está a ser utilizado por outra aplicação.*

- **ID de notificação**: o ID exclusivo da notificação.

    Exemplo: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID do pedido de cliente**: o ID de pedido específico que fez o seu browser.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Hora UTC do carimbo de data /** : o carimbo de hora de quando a notificação ocorreu, em UTC.

    Exemplo: *2017-03-23T19:50:43.7583681Z*

- **ID de transação interno**: o ID interno que é utilizado para procurar o erro nos nossos sistemas.

    Exemplo: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: O utilizador que executou a operação.

    Exemplo: *tperkins\@f128.info*

- **ID do inquilino**: o ID exclusivo do inquilino que o utilizador que executou a operação seja um membro.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID de objeto de utilizador**: O ID exclusivo do utilizador que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Itens de notificação detalhada

- **Nome a apresentar**: (pode estar vazio) um nome a apresentar mais detalhada do erro.

    Exemplo: *Definições de proxy da aplicação*

- **Estado**: o estado específico da notificação.

    Exemplo: *Falhou*

- **ID de objeto**: (pode estar vazio) o ID de objeto no qual a operação estava a ser executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Detalhes**: a descrição detalhada para o que ocorreu em resultado da operação.

    Exemplo: *Url interno "<https://bing.com/>" é inválido, uma vez que já está em utilização.*

- **Erro ao copiar**: Permite-lhe selecionar os **ícone de cópia** à direita do **copiar erro** caixa de texto para copiar os detalhes de notificação para o ajudar com suporte.

    Exemplo:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
