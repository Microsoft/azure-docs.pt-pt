---
title: Resolução de problemas com base em um único sign-on baseado em Azure Ative Directory
description: Problemas de resolução de problemas com uma aplicação AD AZure que está configurada para um único sign-on baseado em palavra-passe.
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 84aec5031dd8d19f8f2ad0aa32185002add378b1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053984"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Resolver problemas de início de sessão único baseado em palavra-passe no Microsoft Azure AD

Para utilizar o sign-on único baseado em palavra-passe (SSO) nas Minhas Apps, a extensão do navegador deve ser instalada. A extensão descarrega automaticamente quando seleciona uma aplicação configurada para SSO baseado em palavras-passe. Para aprender a utilizar as Minhas Apps numa perspetiva de utilizador final, consulte [o portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="my-apps-browser-extension-not-installed"></a>A extensão do navegador My Apps não instalada
Certifique-se de que a extensão do navegador está instalada. Para saber mais, consulte [Plan a Azure Ative Directory My Apps deployment](my-apps-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Único sinal não configurado
Certifique-se de que o único sinal baseado em palavra-passe está configurado. Para saber mais, consulte [configurar o sign-on único baseado em palavras-passe.](configure-password-single-sign-on-non-gallery-applications.md)

## <a name="users-not-assigned"></a>Utilizadores não atribuídos
Certifique-se de que o utilizador está atribuído à aplicação. Para saber mais, consulte [Atribuir um utilizador ou grupo a uma aplicação.](assign-user-or-group-access-portal.md)

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não as envia

Este problema normalmente acontece se o fornecedor de aplicações mudou a sua página de entrada recentemente para adicionar um campo, alterou um identificador utilizado para detetar o nome de utilizador e os campos de palavra-passe, ou modificado como a experiência de entrada funciona para a sua aplicação. Felizmente, em muitos casos, a Microsoft pode trabalhar com os fornecedores de aplicações para resolver rapidamente estes problemas.

Embora a Microsoft tenha tecnologias para detetar automaticamente quando as integrações quebram, pode não ser possível encontrar os problemas imediatamente, ou os problemas demoram algum tempo a corrigir. No caso de uma destas integrações não funcionar corretamente, abra um processo de apoio para que possa ser corrigido o mais rapidamente possível.

**Se estiver em contacto com o fornecedor desta aplicação,** envie-as à nossa maneira para que a Microsoft possa trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../develop/v2-howto-app-gallery-listing.md) começar.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais são preenchidas e enviadas, mas a página indica que as credenciais estão incorretas

Para resolver esta questão, primeiro tente estas coisas:

- O utilizador primeiro tente **iniciar sustura no site da aplicação diretamente** com as credenciais armazenadas para os mesmos.

  * Se o sins insérculo funcionar, então faça com que o utilizador clique no botão **'Atualização'** no Bloco de **Aplicações** na secção **aplicações** das [minhas apps](https://myapps.microsoft.com/) para os atualizar para o mais recente nome de utilizador e palavra-passe de trabalho conhecido.

  * Se você, ou outro administrador atribuiu as credenciais para este utilizador, encontre a atribuição de aplicação do utilizador ou grupo navegando no separador **Grupos & Utilizadores** da aplicação, selecionando a atribuição e clicando no botão **'Credenciais de Atualização'.**

- Se o utilizador tiver atribuído as suas próprias credenciais, certifique-se de que a **sua palavra-passe não expirou na aplicação** e, em caso afirmativo, **atualize a sua palavra-passe expirada** ao iniciar sessão diretamente na aplicação.

  * Depois de a palavra-passe ter sido atualizada na aplicação, solicite ao utilizador que clique no botão **de credenciais De Atualização** no Bloco de **Aplicações** na secção **aplicações** das [Minhas Apps](https://myapps.microsoft.com/) para as atualizar para o mais recente nome de utilizador e senha de trabalho conhecido.

  * Se você, ou outro administrador atribuiu as credenciais para este utilizador, encontre a atribuição de aplicação do utilizador ou grupo navegando no separador **Grupos & Utilizadores** da aplicação, selecionando a atribuição e clicando no botão **'Credenciais de Atualização'.**

- Certifique-se de que a extensão do navegador My Apps está a funcionar e ativada no navegador do seu utilizador.

- Certifique-se de que os seus utilizadores não estão a tentar iniciar sôms à aplicação a partir das Minhas Apps enquanto estiverem **incógnitos, incógnitos ou no modo Private.** A extensão My Apps não é suportada nestes modos.

Caso as sugestões anteriores não funcionem, pode acontecer uma alteração no lado da aplicação que infringiu temporariamente a integração da aplicação com a Azure AD. Por exemplo, isto pode ocorrer quando o fornecedor de aplicações introduz um script na sua página que se comporta de forma diferente para entrada manual vs automatizada, o que faz com que a integração automatizada, como a nossa, se rompa. Felizmente, em muitos casos, a Microsoft pode trabalhar com os fornecedores de aplicações para resolver rapidamente estes problemas.

Apesar de a Microsoft ter tecnologias para detetar automaticamente quando as integrações da aplicação quebram, pode não ser possível encontrar os problemas de imediato, ou os problemas podem demorar algum tempo a corrigir. Quando uma integração não funciona corretamente, pode abrir um caso de apoio para o corrigir o mais rapidamente possível. 

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-os à nossa maneira** para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../develop/v2-howto-app-gallery-listing.md) começar.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de login da aplicação mudou recentemente ou se requer um campo adicional

Se a página de login da aplicação mudou drasticamente, por vezes isto faz com que as nossas integrações se rompam. Um exemplo disso é quando um fornecedor de aplicações adiciona um campo de inscrição, uma captcha ou autenticação multi-factor às suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com os fornecedores de aplicações para resolver rapidamente estes problemas.

Apesar de a Microsoft ter tecnologias para detetar automaticamente quando as integrações da aplicação quebram, pode não ser possível encontrar os problemas de imediato, ou os problemas podem demorar algum tempo a corrigir. Quando uma integração não funciona corretamente, pode abrir um caso de apoio para o corrigir o mais rapidamente possível. 

Além disso, **se estiver em contacto com o fornecedor desta aplicação,** **envie-os à nossa maneira** para que possamos trabalhar com eles para integrar de forma nativa a sua aplicação com o Azure Ative Directory. Pode enviar o fornecedor para a listagem da [sua aplicação na galeria de aplicações Azure Ative Para](../develop/v2-howto-app-gallery-listing.md) começar.

## <a name="capture-sign-in-fields-for-an-app"></a>Capture campos de inscrição para uma aplicação

A captura de campo de entrada de sedus é suportada apenas para páginas de inscrição ativadas por HTML. Não é suportado para páginas de inscrição não padrão, como as que usam o Adobe Flash ou outras tecnologias não ativadas por HTML.

Existem duas formas de capturar campos de inscrição para as suas aplicações personalizadas:

- **A captura automática de campo de entrada funciona** bem com a maioria das páginas de entrada ativadas por HTML, se *utilizarem iDs DIV bem conhecidos* para o nome de utilizador e os campos de palavra-passe. O HTML na página é raspado para encontrar IDs DIV que correspondam a determinados critérios. Esses metadados são guardados para que possa ser reproduzido na aplicação mais tarde.

- **A captura manual do campo de entrada** é utilizada se o fornecedor de aplicações não *rotular os campos de entrada de entrada de entrada* de entrada . A captura manual também é utilizada se o fornecedor *renderizar vários campos que não podem ser detetados automaticamente.* O Azure Ative Directory (Azure AD) pode armazenar dados para o maior número de campos que existem na página de inscrição, se lhe disser onde esses campos estão na página.

Em geral, se a captura automática de campo de inscrição não funcionar, experimente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capture automaticamente campos de inscrição para uma aplicação

Para configurar o SSO baseado em palavra-passe utilizando a captura automática de campo de entrada de registo, siga estes passos:
1. Abra o [portal do Azure](https://portal.azure.com/). Inscreva-se como administrador global ou coadministrador.
2. No painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão AD AZure.
3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.
4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.
5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.
   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **filter** no topo da lista **de Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".
6. Selecione a aplicação que pretende configurar para SSO.
7. Depois de carregar a aplicação, selecione **Single sign-on no** painel de navegação do lado esquerdo.
8. Selecione o modo **de acesso baseado em palavras-passe.**
9. Introduza o **URL de entrada** de inscrição, que é o URL da página onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sposição. *Certifique-se de que os campos de inscrição estão visíveis na página para o URL que fornece*.
10. Selecione **Guardar**.
    A página é automaticamente raspada para o nome de utilizador e caixas de entrada de palavra-passe. Agora pode utilizar o AZure AD para transmitir de forma segura palavras-passe para essa aplicação utilizando a extensão do navegador My Apps.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capture manualmente campos de inscrição para uma aplicação

Para capturar manualmente os campos de inscrição, tem de ter a extensão do navegador My Apps instalada. Além disso, o seu navegador não pode estar a funcionar *em ModoPrivate,* *incógnito* ou *privado.*

Para configurar o SSO baseado em palavra-passe para uma aplicação utilizando a captura manual de campo de entrada, siga estes passos:
1. Abra o [portal do Azure](https://portal.azure.com/). Inscreva-se como administrador global ou coadministrador.
2. No painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão AD AZure.
3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.
4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.
5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.
   > [!NOTE] 
   > Se não vir a aplicação que pretende, utilize o controlo **filter** no topo da lista **de Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".
6. Selecione a aplicação que pretende configurar para SSO.
7. Depois de carregar a aplicação, selecione **Single sign-on no** painel de navegação do lado esquerdo.
8. Selecione o modo **de acesso baseado em palavras-passe.**
9. Introduza o **URL de entrada de inscrição,** que é a página onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sposição. *Certifique-se de que os campos de inscrição estão visíveis na página para o URL que fornece*.
10. Selecione **Configurar *&lt; &gt;* configurações únicas de sinalização de assinatura**.
11. Selecione **os campos de inscrição de deteção manual**.
14. Selecione **OK**.
15. Selecione **Guardar**.
16. Siga as instruções para utilizar as Minhas Apps.


## <a name="troubleshoot-problems"></a>Resolução de problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Eu tenho um erro de "Não conseguimos encontrar nenhum campo de inscrição naquele ERRO URL"

Obtém-se esta mensagem de erro quando a deteção automática dos campos de inscrição falha. Para resolver o problema, experimente a deteção manual de campo de inscrição. Consulte os [campos de inscrição de captura manual para uma](#manually-capture-sign-in-fields-for-an-app) secção de aplicação deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Recebo um erro de "Não conseguir salvar uma única configuração de sinal de inscrição"

Raramente, a atualização da configuração SSO falha. Para resolver este problema, tente guardar novamente a configuração.

Se continuar a cometer o erro, abra um caso de apoio. Inclua as informações descritas nos [dados](#view-portal-notification-details) de notificação do portal Ver e Envie por email o artigo Enviar por email o artigo Informação enviada por email o artigo Para [obter secções](#send-notification-details-to-a-support-engineer-to-get-help) de ajuda deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Não consigo detetar manualmente campos de inscrição para a minha aplicação.

Pode observar os seguintes comportamentos quando a deteção manual não estiver a funcionar:
- O processo de captura manual parecia funcionar, mas os campos capturados não estão corretos.
- Os campos corretos não são destacados quando o processo de captura é executado.
- O processo de captura leva-o à página de sinstentação da app como esperado, mas nada acontece.
- A captura manual parece funcionar, mas o SSO não acontece quando os utilizadores navegam para a aplicação a partir das Minhas Apps.

Se sentir algum destes problemas, faça as seguintes coisas:
- Certifique-se de que tem a versão mais recente da extensão do navegador My Apps *instalada e ativada.*
- Certifique-se de que o seu navegador não está *incógnito,* *incógnito, no modo InPrivate* ou *Private* durante o processo de captura. A extensão My Apps não é suportada nestes modos.
- Certifique-se de que os seus utilizadores não estão a tentar entrar na aplicação a partir das Minhas Apps enquanto estiverem *incógnitos,* *incógnitos, no* modo Privado ou *Privado.*
- Tente novamente o processo de captura manual. Certifique-se de que os marcadores vermelhos estão sobre os campos corretos.
- Se o processo de captura manual parece parar de responder ou a página de inscrição não responder, tente novamente o processo de captura manual. Mas desta vez, depois de concluir o processo, pressione a tecla F12 para abrir a consola de desenvolvimento do seu navegador. Selecione o separador **de consola.** **Digite a janela.location="*&lt; o &gt; URL de entrada de entrada que especificou ao configurar a aplicação*"** e, em seguida, prima Enter. Isto força um redirecionamento de página que termina o processo de captura e armazena os campos que foram capturados.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Não posso adicionar outro utilizador à minha aplicação SSO baseada em palavra-passe

A aplicação SSO baseada em palavras-passe tem um limite de 48 utilizadores. Assim, tem um limite de 48 chaves para par de nome de utilizador/palavra-passe por aplicação.
Se quiser adicionar utilizadores adicionais, pode:
-   Adicionar instância adicional da app
-   Remova os utilizadores que já não estão a usar a app primeiro

## <a name="request-support"></a>Solicitar apoio 
Se receber uma mensagem de erro quando configurar sSO e atribuir utilizadores, abra um bilhete de apoio. Incluir o máximo possível de informações:

-   ID de erro de correlação
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de browser
-   Fuso horário e tempo/período quando ocorreu o erro
-   Traços de violino

### <a name="view-portal-notification-details"></a>Ver detalhes da notificação do portal

Para ver os detalhes de qualquer notificação do portal, siga estes passos:
1. Selecione o ícone **notificações** (a campainha) no canto superior direito do portal Azure.
2. Selecione qualquer notificação que mostre um estado *de Erro.* (Eles têm um vermelho "!".)
   > [!NOTE]
   > Não é possível selecionar notificações que se encontrem no estado *de Sucesso* ou *Em Progresso.*
3. O painel de detalhes da **notificação** abre. Leia a informação para saber sobre o problema.
5. Se ainda precisar de ajuda, partilhe a informação com um engenheiro de suporte ou com o grupo de produtos. Selecione o ícone de **cópia** para o direito da caixa de **erro de Cópia** para copiar os dados de notificação para partilhar.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Envie detalhes de notificação a um engenheiro de suporte para obter ajuda

É importante que partilhe *todos os* detalhes listados nesta secção com suporte para que possam ajudá-lo rapidamente. Para gravar, pode tirar uma imagem ou selecionar **o erro de cópia**.

As seguintes informações explicam o que cada item de notificação significa e fornecem exemplos.

#### <a name="essential-notification-items"></a>Itens essenciais de notificação

- **Denominação**: título descritivo da notificação.

   Exemplo: *Configurações de procuração de aplicação*

- **Descrição:** o que ocorreu em resultado da operação.

   Exemplo: *O URL interno introduzido já está a ser utilizado por outra aplicação.*

- **ID de notificação:** o ID único da notificação.

    Exemplo: *clienteNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID de pedido do cliente:** o ID de pedido específico que o seu navegador esou.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Carimbo de tempo UTC**: o carimbo de tempo de quando ocorreu a notificação, na UTC.

    Exemplo: *2017-03-23T19:50:43.7583681Z*

- **ID de transação interna:** o ID interno que é usado para procurar o erro nos nossos sistemas.

    Exemplo: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: O utilizador que dirigiu a operação.

    Exemplo: *tperkins \@ f128.info*

- **ID do inquilino:** a identificação única do inquilino que o utilizador que dirigia a operação é membro.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID do objeto do utilizador**: O ID único do utilizador que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Artigos de notificação detalhados

- **Nome do visor**: (pode estar vazio) um nome de visualização mais detalhado para o erro.

    Exemplo: *Configurações de procuração de aplicação*

- **Estado**: o estado específico da notificação.

    Exemplo: *Falhado*

- **ID do objeto:**(pode estar vazio) o ID do objeto contra o qual a operação foi executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Pormenores**: descrição detalhada do que ocorreu em resultado da operação.

    Exemplo: *O url interno ' é <https://bing.com/> inválido uma vez que já está em uso.*

- **Erro de cópia**: Permite-lhe selecionar o ícone de **cópia** à direita da caixa de texto de erro **de cópia** para copiar os dados da notificação para ajudar no suporte.

    Exemplo:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Passos seguintes
* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
* [Planear uma implementação de As Minhas Aplicações](my-apps-deployment-plan.md)
