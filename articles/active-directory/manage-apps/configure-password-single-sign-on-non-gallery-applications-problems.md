---
title: Problemas de configuração de senha SSO para aplicações não-galeria
description: Problemas comuns que ocorrem quando configura uma única assinatura de senha (SSO) para aplicações personalizadas que não estão na galeria de aplicações da AD Azure.
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274138"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemas de configuração de um único sinal de senha para uma aplicação não-galeria

Este artigo descreve problemas comuns que podem ocorrer quando configura uma *única assinatura* de senha (SSO) para uma aplicação não-galeria.

## <a name="capture-sign-in-fields-for-an-app"></a>Capture campos de inscrição para uma aplicação

A captura de campo de iniciar sessão é suportada apenas para páginas de inscrição ativadas por HTML. Não é suportado para páginas de inscrição não padrão, como as que usam Adobe Flash ou outras tecnologias não-HTML.

Existem duas formas de capturar campos de sessão para as suas aplicações personalizadas:

- **A captura automática de campo de entrada** funciona bem com a maioria das páginas de entrada ativadas por HTML, se *utilizarem iDs DIV bem conhecidos* para o nome do utilizador e campos de palavra-passe. O HTML na página é raspado para encontrar IDs DIV que correspondam a determinados critérios. Os metadados são guardados para que possa ser reproduzido para a aplicação mais tarde.

- **A captura manual de campo de entrada** é utilizada se o fornecedor de aplicações não rotular os campos de entrada de entrada de *entrada*. A captura manual também é usada se o fornecedor *renderizar vários campos que não podem ser detetados automaticamente*. O Azure Ative Directory (Azure AD) pode armazenar dados para o maior número de campos que existem na página de entrada, se lhe disser onde esses campos estão na página.

Em geral, se a captura automática de campo de entrada não funcionar, tente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capture automaticamente os campos de inscrição para uma aplicação

Para configurar o SSO baseado em palavra-passe utilizando a captura automática de campo de entrada, siga estes passos:

1. Abra o [portal Azure.](https://portal.azure.com/) Inscreva-se como administrador global ou coadministrador.

2. No painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas apps.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da lista **de Todas as Aplicações.** Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel de navegação do lado esquerdo.

8. Selecione o modo **de acesso baseado em palavra-passe.**

9. Introduza o **URL de iniciar**sessão , que é o URL da página onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sessão. *Certifique-se de que os campos de inscrição estão visíveis na página para o URL que fornece*.

10. Selecione **Guardar**.

    A página é automaticamente raspada para o nome do utilizador e caixas de entrada de senha. Agora pode utilizar o Azure AD para transmitir de forma segura senhas para essa aplicação utilizando a extensão do navegador Access Panel.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturar manualmente campos de inscrição para uma aplicação

Para capturar manualmente os campos de acesso, tem de instalar a extensão do navegador Access Panel. Além disso, o seu navegador não pode estar a funcionar *em modo Privado*, *incógnito*ou *privado.*

Para instalar a extensão, consulte a secção de [extensão do navegador](#install-the-access-panel-browser-extension) do Painel de Acesso deste artigo.

Para configurar o SSO baseado em palavra-passe para uma aplicação utilizando a captura manual de campo de entrada, siga estes passos:

1. Abra o [portal Azure.](https://portal.azure.com/) Inscreva-se como administrador global ou coadministrador.

2. No painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas apps.

   > [!NOTE] 
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da lista **de Todas as Aplicações.** Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel de navegação do lado esquerdo.

8. Selecione o modo **de acesso baseado em palavra-passe.**

9. Introduza o **URL de iniciar**sessão , que é a página onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sessão. *Certifique-se de que os campos de inscrição estão visíveis na página para o URL que fornece*.

10. **Selecione * &lt;&gt; * Configurar o nome de nome de palavra-passe Definições de sinal único**.

11. Selecione **manualmente detetar campos de sessão**.

14. Selecione **Ok**.

15. Selecione **Guardar**.

16. Siga as instruções de utilização do Painel de Acesso.

## <a name="troubleshoot-problems"></a>Resolver problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Tenho um erro de "Não conseguimos encontrar nenhum erro de inscrição naquele URL".

Obtém-se esta mensagem de erro quando a deteção automática dos campos de inscrição falha. Para resolver o problema, tente a deteção manual de campo de sessão. Consulte os [campos de inscrição manualmente para obter uma](#manually-capture-sign-in-fields-for-an-app) secção de aplicação deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Tenho um erro "Incapaz de salvar uma única configuração de inscrição"

Raramente, a atualização da configuração SSO falha. Para resolver este problema, tente salvar a configuração novamente.

Se continuar a cometer o erro, abra um caso de apoio. Inclua as informações descritas nos detalhes da [notificação](#view-portal-notification-details) do portal View e envie detalhes de [notificação a um engenheiro](#send-notification-details-to-a-support-engineer-to-get-help) de suporte para obter secções de ajuda deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Não consigo detetar manualmente campos de inscrição para a minha aplicação.

Pode observar os seguintes comportamentos quando a deteção manual não está a funcionar:

- O processo de captura manual parecia funcionar, mas os campos capturados não estão corretos.

- Os campos corretos não são realçados quando o processo de captura decorre.

- O processo de captura leva-o à página de inscrição da aplicação como esperado, mas nada acontece.

- A captura manual parece funcionar, mas o SSO não acontece quando os utilizadores navegam para a aplicação a partir do Painel de Acesso.

Se experimentar algum destes problemas, faça as seguintes coisas:

- Certifique-se de que tem a versão mais recente da extensão do navegador do Painel de Acesso *instalada e ativada*. Consulte a secção de [extensão do navegador Do Painel](#install-the-access-panel-browser-extension) de Acesso deste artigo.

- Certifique-se de que o seu navegador não está em modo *incógnito,* *inprivate*ou *privado* durante o processo de captura. A extensão do Painel de Acesso não é suportada nestes modos.

- Certifique-se de que os seus utilizadores não estão a tentar iniciar sessão na aplicação a partir do Painel de Acesso enquanto estão no modo *incógnito*, *inPrivate*ou *Private*.

- Tente novamente o processo de captura manual. Certifique-se de que os marcadores vermelhos estão sobre os campos corretos.

- Se o processo de captura manual parecer parar de responder ou a página de inscrição não responder, tente novamente o processo de captura manual. Mas desta vez, depois de concluir o processo, prima a tecla F12 para abrir a consola de desenvolvimento do seu navegador. Selecione **o** separador consola. Digite **janela.localização="*&lt;o URL&gt;de entrada que especificou ao configurar a aplicação*"**, e, em seguida, prima Enter. Isto força um redirecionamento de página que termina o processo de captura e armazena os campos que foram capturados.

### <a name="contact-support"></a>Contactar o suporte

Se ainda tiver problemas, abra um caso com o Microsoft Support. Descreva o que tentou. Inclua os detalhes descritos nos detalhes de [notificação](#view-portal-notification-details) do portal View e envie detalhes de [notificação a um engenheiro](#send-notification-details-to-a-support-engineer-to-get-help) de suporte para obter secções de ajuda deste artigo (se aplicável).

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do Painel de Acesso

Siga estes passos.

1. [Open Access Panel](https://myapps.microsoft.com) em um navegador suportado. Inscreva-se no Azure AD como *utilizador*.

2. Selecione **a aplicação password-SSO** no Painel de Acesso.

3. Quando lhe for solicitado que instale o software, selecione **Instalar Agora**.

4. Será direcionado para uma página de descarregamento para o seu navegador. Escolha **adicionar** a extensão.

5. Se for solicitado, selecione **Ativar** ou **permitir**.

6. Após a instalação, reinicie o seu navegador.

7. Inscreva-se no Painel de Acesso. Veja se consegue abrir as suas aplicações ativadas por palavra-passe SSO.

Também pode descarregar diretamente a extensão do navegador para O Chrome e Firefox através destas links:

-   [Extensão do Painel de Acesso Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Ver detalhes da notificação do portal

Para ver os detalhes de qualquer notificação do portal, siga estes passos:

1. Selecione o ícone **notificações** (o sino) no canto superior direito do portal Azure.

2. Selecione qualquer notificação que mostre um estado *de erro.* (Eles têm um "!".) vermelho

   > [!NOTE]
   > Não é possível selecionar notificações que estejam no estado *de Sucesso* ou *Em Progresso.*

3. Abre o painel de dados de **notificação.** Leia a informação para saber sobre o problema.

5. Se ainda precisar de ajuda, partilhe a informação com um engenheiro de suporte ou com o grupo de produtos. Selecione o ícone da **cópia** à direita da caixa de **erro Copy** para copiar os dados de notificação para partilhar.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Envie detalhes de notificação a um engenheiro de suporte para obter ajuda

É importante que partilhe *todos os* detalhes que estão listados nesta secção com apoio para que possam ajudá-lo rapidamente. Para gravar, pode tirar uma imagem ou selecionar **erro de cópia**.

As seguintes informações explicam o que cada item de notificação significa e dá exemplos.

### <a name="essential-notification-items"></a>Itens essenciais de notificação

- **Denominação**: o título descritivo da notificação.

   Exemplo: Definições de *procuração de aplicação*

- **Descrição:** o que ocorreu em resultado da operação.

   Exemplo: *O URL interno introduzido já está a ser utilizado por outra aplicação.*

- ID de **notificação**: a identificação única da notificação.

    Exemplo: *clienteNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID**de pedido de cliente : o ID de pedido específico que o seu navegador fez.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- Carimbo de **tempo UTC**: o carimbo de quando a notificação ocorreu, na UTC.

    Exemplo: *2017-03-23T19:50:43.7583681Z*

- **ID de Transação Interna**: o ID interno que é usado para procurar o erro nos nossos sistemas.

    Exemplo: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** O utilizador que dirigiu a operação.

    Exemplo: *tperkins\@f128.info*

- **ID**do inquilino : a identificação única do inquilino que o utilizador que dirigia a operação é membro.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f962ece*

- **ID do objeto**do utilizador : A identificação única do utilizador que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-38379127a99*

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

- Nome do **mostrador**: (pode estar vazio) um nome de exibição mais detalhado para o erro.

    Exemplo: Definições de *procuração de aplicação*

- **Estado**: o estado específico da notificação.

    Exemplo: *Falhado*

- **ID do objeto:**(pode estar vazio) o ID do objeto contra o qual a operação foi executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Pormenores**: a descrição pormenorizada do que ocorreu em resultado da operação.

    Exemplo: *O<https://bing.com/>url interno ' ' é inválido* uma vez que já está em uso.

- **Erro de cópia**: Permite-lhe selecionar o ícone da **cópia** à direita da caixa de texto de **erro Copiar** para copiar os dados de notificação para ajudar com o suporte.

    Exemplo:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
