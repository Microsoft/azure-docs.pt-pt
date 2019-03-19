---
title: Problema ao configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria | Documentos da Microsoft
description: Compreender os rostos de pessoas de problemas comuns durante a configuração do logon único de palavra-passe para aplicações externas à Galeria personalizadas que não estão listados na Galeria de aplicações do Azure AD
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
ms.openlocfilehash: c769d241f54d5d86ef4954609eec555d8aab947a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57858002"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problema ao configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria

Este artigo ajuda-o a compreender os rostos de pessoas de problemas comuns durante a configuração do **palavra-passe início de sessão único** com uma aplicação de externas à galeria.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Como capturar os campos de início de sessão para uma aplicação

Captura de campo de início de sessão só é suportada para habilitados por HTML páginas de início de sessão e é **não é suportada para as páginas de início de sessão não padrão**, como aqueles que usam Flash ou outras tecnologias não-habilitados por HTML.

Existem duas formas, pode capturar os campos de início de sessão para as suas aplicações personalizadas:

-   Captura de campo de início de sessão automático

-   Captura de campo de início de sessão manual

**Captura de campo de início de sessão automático** funciona bem com a maioria das habilitados por HTML início de sessão páginas, se estes utilizarem **bem conhecidos DIV IDs para o nome de utilizador e palavra-passe de entrada** campo. A maneira como isso funciona é pelo captura o HTML na página para encontrar os IDs de DIV que correspondem a determinados critérios e, em seguida, guarde esses metadados para esta aplicação de reprodução de palavras-passe para ela mais tarde.

**Captura de campo de início de sessão manual** pode ser utilizado no caso de que o aplicativo **fornecedor não Etiquetar** os campos de entrada utilizados para início de sessão. Captura de campo de início de sessão manual, também pode ser utilizada no caso de quando os **fornecedor renderiza vários campos** que não pode ser detetado automaticamente. O Azure AD pode armazenar dados para os campos que estejam na página de início de sessão, desde que diga-nos onde esses campos estão na página.

Em geral, **se captura do campo de início de sessão automático não funcionar, tente a opção manual.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Como capturar automaticamente os campos de início de sessão para uma aplicação

Para configurar **baseado em palavra-passe de início de sessão único** para uma aplicação com **captura do campo de início de sessão automático**, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione o modo **baseado em palavra-passe de início de sessão.**

9. Introduza o **URL de início de sessão**, o URL em que os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão. **Certifique-se de que os campos de início de sessão são visíveis no URL é fornecer**.

10. Clique no botão **Guardar**.

11. Depois de o fazer isso, o URL automaticamente é extraído para um nome de utilizador e palavra-passe de caixa de entrada e permite-lhe utilizar o Azure AD para transmitir palavras-passe para esse aplicativo usando a extensão de browser do painel de acesso de forma segura.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Como capturar manualmente os campos de início de sessão para uma aplicação

Para capturar os campos de início de sessão manualmente, primeiro tem de ter a extensão de Browser do painel de acesso instalada e **não estar em execução no modo inPrivate, incógnito ou privado.** Para instalar a extensão do browser, siga os passos a [como instalar a extensão de Browser do painel de acesso](#i-cannot-manually-detect-sign-in-fields-for-my-application) secção.

Para configurar **baseado em palavra-passe de início de sessão único** para uma aplicação com **captura do campo de início de sessão manual**, siga os passos abaixo:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione o modo **baseado em palavra-passe de início de sessão.**

9. Introduza o **URL de início de sessão**, o URL em que os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão. **Certifique-se de que os campos de início de sessão são visíveis no URL é fornecer**.

10. Clique no botão **Guardar**.

11. Depois de o fazer isso, o URL automaticamente é extraído para um nome de utilizador e palavra-passe de caixa de entrada e permite-lhe utilizar o Azure AD para transmitir palavras-passe para esse aplicativo usando a extensão de browser do painel de acesso de forma segura. No caso de falha, pode **alterar o modo de início de sessão para utilizar a captura de campo de início de sessão manual** ao continuar para o passo 12.

12. Clique em **configurar &lt;appname&gt; as definições de início de sessão único de palavra-passe**.

13. Selecione o **manualmente detetar campos de início de sessão** opção de configuração.

14. Clique em **OK**.

15. Clique em **Guardar**.

16. Siga as instruções de tela em usar o painel de acesso.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Posso ver um erro "Não foi possível encontrar quaisquer campos de início de sessão neste URL"

Verá este erro quando ocorre uma falha de deteção automática de campos de início de sessão. Para resolver o problema, experimente deteção de campo de início de sessão manual, seguindo os passos a [How to capture manualmente os campos de início de sessão para uma aplicação como](#how-to-manually-capture-sign-in-fields-for-an-application) secção.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Vejo uma "não é possível guardar a configuração de início de sessão único" erro

Em certos casos raros, a atualizar a configuração de início de sessão única pode falhar. Para resolver, experimente a guardar a única início de sessão na configuração de novo.

Se continuar a falhar consistentemente, abra um incidente de suporte e forneça as informações coletadas a [como ver os detalhes de uma notificação no portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [como obter ajuda com o envio de detalhes de notificação para um suporte engenheiro de](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) secções.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Manualmente, não é possível detetar campos de início de sessão para a minha aplicação

Alguns dos comportamentos que poderá ver quando a deteção manual não está a funcionar incluem:

-   O processo de captura manual parecia funcionar, mas os campos capturados não eram corretos

-   Os campos de direito não obter realçados ao realizar o processo de captura

-   O processo de captura me leva à página de início de sessão do aplicativo conforme o esperado, mas não acontece nada

-   Captura manual parece funcionar, mas o SSO não acontece quando meus usuários navegam para a aplicação a partir do painel de acesso.

Verifique o seguinte se encontrar algum desses problemas:

-   Certifique-se de que tem a versão mais recente da extensão de browser do painel de acesso **instalados** e **ativado** seguindo os passos a [como instalar a extensão de Browser do painel de acesso](#how-to-install-the-access-panel-browser-extension) secção.

-   Certifique-se de que não está a tentar o processo de captura ao navegador, **modo inPrivate, incógnito ou privado**. A extensão do painel de acesso não é suportada nesses modos.

-   Certifique-se de que os utilizadores não estão a tentar iniciar sessão para a aplicação do painel de acesso ao mesmo tempo no **modo inPrivate, incógnito ou privado**. A extensão do painel de acesso não é suportada nesses modos.

-   Experimente o processo de captura manual novamente, para garantir que os marcadores vermelhos sejam sobre os campos corretos.

-   Se o processo de captura manual parece suspender ou a página de início de sessão não faz nada (caso 3 acima), tente novamente o processo de captura manual. Mas, desta vez depois de concluir o processo, prima a **F12** botão para abrir a consola de programador do seu browser. Uma vez, abra a **consola** e escreva **window.location= "&lt;introduza o url de início de sessão que especificou quando configurar a aplicação&gt;"** e, em seguida, prima **Enter** . Isso força uma página de redirecionamento que termina o processo de captura e armazena os campos que tenham sido capturados.

Se nenhuma dessas abordagens funcionam para si, pode ajudar o suporte. Abrir um incidente de suporte com os detalhes do que tentasse, bem como as informações coletadas a [como ver os detalhes de uma notificação no portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) secções (se aplicável).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) em um dos browsers suportados e inicie sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação SSO de palavra-passe** no painel de acesso.

3.  Na linha de comandos pedindo para instalar o software, selecione **instalar agora**.

4.  Com base no seu browser, direcionado para a ligação de transferência. **Adicionar** a extensão ao seu navegador.

5.  Se o browser pede-lhe, selecione a qualquer um **habilitar** ou **permitir** a extensão.

6.  Depois de instalada **reiniciar** a sessão do browser.

7.  Iniciar sessão no painel de acesso e veja se pode **inicie** seus aplicativos de SSO de palavra-passe.

Também pode baixar a extensão para o Chrome e Firefox dos links diretos abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação no portal

Pode ver os detalhes de qualquer notificação do portal, seguindo os passos abaixo:

1. Clique nas **notificações** ícone (na campainha) no canto superior direito do portal do Azure

2. Selecione qualquer notificação numa **erro** Estado (aquelas com linhas vermelhas (!!!) junto a eles).

   >! Tenha em atenção], não é possível clicar em notificações numa **Successful** ou **em curso** estado.
   >
   >

3. O **detalhes de notificação** painel abre-se.

4. Utilize as informações por si para obter mais detalhes sobre o problema de compreender.

5. Se ainda precisar de ajuda, também pode partilhar as informações com um engenheiro de suporte ou o grupo de produto para obter ajuda com o seu problema.

6. Clique nas **cópia** **ícone** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um engenheiro de grupo de suporte ou o produto.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte

É muito importante que partilham **todos os detalhes indicados abaixo** com um engenheiro de suporte se precisar de ajuda, para que eles podem ajudá-lo a rapidamente. Pode **tirar uma captura de ecrã** ou clique no **ícone de erro da cópia**, apresentado à direita do **copiar erro** caixa de texto.

## <a name="notification-details-explained"></a>Detalhes da notificação explicados

A seguir explica mais o que cada da notificação significa que os itens e fornece exemplos de cada um deles.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

-   **Título** – o título descritivo da notificação

    -   Exemplo – **as definições de proxy de aplicação**

-   **Descrição** – a descrição, o que ocorreu em resultado da operação

    -   Exemplo – **interno url introduzido já está a ser utilizado por outra aplicação**

-   **ID de notificação** – o id exclusivo da notificação

    -   Exemplo – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID do pedido de cliente** – o id de pedido específico feito pelo browser

    -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Hora UTC do carimbo de data /** – o período de tempo durante o qual a notificação ocorreu, em UTC

    -   Exemplo – **2017-03-23T19:50:43.7583681Z**

-   **ID de transação interno** – o ID interno utilizado para procurar o erro nos nossos sistemas

    -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – o utilizador que executou a operação

    -   Exemplo – **tperkins\@f128.info**

-   **ID do inquilino** – o ID exclusivo do inquilino que fazia parte, o utilizador que executou a operação

    -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **ID de objeto de utilizador** – o ID exclusivo do utilizador que executou a operação

    -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhada

-   **Nome a apresentar** – **(pode estar vazio)** um nome a apresentar mais detalhado do erro

    -   Exemplo – **as definições de proxy de aplicação**

-   **Estado** – o estado específico da notificação

    -   Exemplo – **falhou**

-   **ID de objeto** – **(pode estar vazio)** o ID de objeto em relação aos quais a operação foi executada

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – o detalhadas descrição, o que ocorreu em resultado da operação

    -   Exemplo – **url interno "<https://bing.com/>" é inválido, uma vez que já está em utilização**

-   **Erro ao copiar** – clique no **ícone de cópia** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um engenheiro de grupo do produto ou de suporte

    -   Exemplo – ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)

