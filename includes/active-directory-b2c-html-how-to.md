---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116933"
---
## <a name="use-custom-page-content"></a>Use conteúdo de página personalizado

Ao utilizar a funcionalidade de personalização de UI da página, pode personalizar o aspeto e a sensação de qualquer política personalizada. Também pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C.

### <a name="how-it-works"></a>Como funciona

O Azure AD B2C executa o código no navegador do seu cliente utilizando a [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). No prazo de execução, o conteúdo é carregado a partir de um URL que especifica no fluxo do utilizador ou na política personalizada. Cada página na experiência do utilizador carrega o seu conteúdo a partir do URL que especifica para essa página. Depois de o conteúdo ser carregado a partir do seu URL, é fundido com um fragmento HTML inserido pelo Azure AD B2C, e depois a página é exibida ao seu cliente.

![Margem de conteúdo de página personalizada](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Conteúdo personalizado da página HTML

Crie uma página HTML com a sua própria marca para servir o conteúdo da página personalizada. Esta página pode `*.html` ser uma página estática, ou uma página dinâmica como .NET, Node.js ou PHP.

O conteúdo da página personalizada pode conter quaisquer elementos HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como iframes. O único elemento necessário é `id` um `api`elemento de mergulho `<div id="api"></div>` com conjunto para, como este dentro da sua página HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalize as páginas padrão do Azure AD B2C

Em vez de criar o conteúdo da página personalizada do zero, pode personalizar o conteúdo de página padrão do Azure AD B2C.

A tabela seguinte lista o conteúdo da página predefinida fornecido pelo Azure AD B2C. Descarregue os ficheiros e use-os como ponto de partida para criar as suas próprias páginas personalizadas.

| Página padrão | Descrição | ID de definição de conteúdo<br/>(apenas política personalizada) |
|:-----------------------|:--------|-------------|
| [exceção.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Esta página é apresentada quando uma exceção ou um erro é encontrado. | *api.error* |
| [autoafirmado.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página autoafirmada.** Utilize este ficheiro como conteúdo de página personalizada para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de registo de conta local, reset de palavra-passe e muito mais. O formulário pode conter vários controlos de entrada, tais como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de rádio, caixas de entrega únicas e caixas de verificação multi-selecionadas. | *api.localaccountsignin,* *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.self-afirmou* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação de vários fatores.** Nesta página, os utilizadores podem verificar os seus números de telefone (utilizando texto ou voz) durante o registo ou o início de sessão. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os utilizadores podem aceder para atualizar o seu perfil. Esta página é semelhante à página de inscrição da conta social, com exceção dos campos de entrada de palavras-passe. | *api.self-asserted.profileupdate* |
| [unificado.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição unificada ou inscrição.** Esta página trata do processo de inscrição e de inscrição do utilizador. Os utilizadores podem utilizar fornecedores de identidade empresarial, fornecedores de identidade social, como facebook ou Google+, ou contas locais. | *api.signuporsignina* |

## <a name="hosting-the-page-content"></a>Hospedar o conteúdo da página

Ao utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI, apresente o seu conteúdo uI em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob,](../articles/storage/blobs/storage-blobs-introduction.md) [Serviços de Aplicações Azure,](/azure/app-service/)servidores web, CDNs, AWS S3 ou sistemas de partilha de ficheiros.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para usar conteúdo de página personalizada

- Utilize um URL absoluto quando incluir recursos externos como ficheiros de mídia, CSS e JavaScript no seu ficheiro HTML.
- Utilizando a versão de layout da [página](../articles/active-directory-b2c/page-layout.md) 1.2.0 ou superior, pode adicionar o `data-preload="true"` atributo nas suas tags HTML para controlar a ordem de carga para CSS e JavaScript. Com `data-preload=true`, a página é construída antes de ser mostrada ao utilizador. Este atributo ajuda a evitar que a página "cintile" pré-carregando o ficheiro CSS, sem que o HTML não-estilo seja mostrado ao utilizador. O seguinte código HTML mostra a `data-preload` utilização da etiqueta.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Recomendamos que comece com o conteúdo da página padrão e construa em cima dele.
- Pode incluir o JavaScript no seu conteúdo personalizado tanto para [fluxos](../articles/active-directory-b2c/user-flow-javascript-overview.md) de utilizadores como [para políticas personalizadas.](../articles/active-directory-b2c/javascript-samples.md)
- As versões de navegador suportadas são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Suporte limitado para Internet Explorer 9 e 8
  - Google Chrome 42.0 e acima
  - Mozilla Firefox 38.0 e acima
  - Safari para iOS e macOS, versão 12 e superior
- Devido a restrições de segurança, o Azure `frame`AD B2C não suporta elementos , `iframe`ou `form` HTML.

## <a name="custom-page-content-walkthrough"></a>Walkthrough de conteúdo de página personalizada

Aqui está uma visão geral do processo:

1. Prepare um local para hospedar o conteúdo da página personalizada (um ponto final HTTPS acessível ao público, ativado pelo CORS).
1. Descarregue e personalize um `unified.html`ficheiro de conteúdo de página predefinido, por exemplo.
1. Publique o conteúdo da sua página personalizada o seu ponto final HTTPS disponível publicamente.
1. Detete a partilha de recursos de origem cruzada (CORS) para a sua aplicação web.
1. Aponte a sua política para o seu conteúdo de política personalizado URI.

### <a name="1-create-your-html-content"></a>1. Crie o seu conteúdo HTML

Crie um conteúdo de página personalizado com a marca do seu produto no título.

1. Copie o seguinte corte HTML. É bem formado HTML5 com um elemento vazio chamado * \<div\>\<id="api"\> /div* localizado dentro das etiquetas do * \<corpo.\> * Este elemento indica onde o conteúdo da AD B2C azure deve ser inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Colhe o corte copiado num editor de texto e, em seguida, guarde o ficheiro como *customize-ui.html*.

> [!NOTE]
> Os elementos de formulário HTML serão removidos devido a restrições de segurança se utilizar login.microsoftonline.com. Se pretender utilizar elementos de formulário HTML no seu conteúdo HTML personalizado, [utilize b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Criar uma conta de armazenamento Azure Blob

Neste artigo, utilizamos o armazenamento Azure Blob para hospedar os nossos conteúdos. Pode optar por hospedar o seu conteúdo num servidor web, mas tem de ativar o [CORS no seu servidor web](https://enable-cors.org/server.html).

Para alojar o seu conteúdo HTML no armazenamento blob, execute os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No menu **Hub,** selecione a conta **new** > **storage** > **Storage**.
1. Selecione uma **Subscrição** para a sua conta de armazenamento.
1. Crie um **grupo de Recursos** ou selecione um existente.
1. Insira um **nome** único para a sua conta de armazenamento.
1. Selecione a **localização Geográfica** para a sua conta de armazenamento.
1. **O modelo** de implantação pode continuar a ser **Gestor de Recursos.**
1. **O desempenho** pode permanecer **Standard**.
1. Alterar **o tipo de conta** para o armazenamento **blob.**
1. **A replicação** pode permanecer **RA-GRS**.
1. **O nível de acesso** pode permanecer **quente.**
1. Selecione **Review + crie** para criar a conta de armazenamento.
    Após a conclusão da implementação, a página da **conta de armazenamento** abre automaticamente.

#### <a name="21-create-a-container"></a>2.1 Criar um recipiente

Para criar um contentor público no armazenamento blob, execute os seguintes passos:

1. Sob o **serviço Blob** no menu à esquerda, selecione **Blobs**.
1. **Selecione +Recipiente**.
1. Para **Nome,** introduza *raiz.* O nome pode ser um nome à sua escolha, por exemplo, *contoso,* mas usamos *raiz* neste exemplo para a simplicidade.
1. Para o nível de **acesso público,** selecione **Blob**, em **seguida, OK**.
1. Selecione **raiz** para abrir o novo recipiente.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Faça upload dos ficheiros de conteúdo da página personalizada

1. Selecione **Upload**.
1. Selecione o ícone da pasta ao lado **de Selecionar um ficheiro**.
1. Navegue para e selecione **customize-ui.html,** que criou anteriormente na secção de personalização da Página UI.
1. Se quiser fazer o upload para uma subpasta, expanda **O Avançado** e introduza um nome de pasta no Upload **para pasta**.
1. Selecione **Upload**.
1. Selecione a bolha **customize-ui.html** que carregou.
1. À direita da caixa de texto **URL,** selecione o ícone **Copy para clipboard** para copiar o URL para a sua área de cópia.
1. No navegador web, navegue para o URL que copiou para verificar se a bolha que carregou está acessível. Se estiver inacessível, por exemplo, se encontrar um `ResourceNotFound` erro, certifique-se de que o tipo de acesso do recipiente está definido para a **bolha**.

### <a name="3-configure-cors"></a>3. Configure cors

Configure o armazenamento blob para partilha de recursos de origem cruzada executando os seguintes passos:

1. No menu, selecione **CORS**.
1. Para **origens permitidas,** entre . `https://your-tenant-name.b2clogin.com` Substitua `your-tenant-name` pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao introduzir o nome do seu inquilino.
1. Para **métodos permitidos,** selecione ambos `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos,** introduza um asterisco (*).
1. Para **cabeçalhos expostos,** introduza um asterisco (*).
1. Para **a idade máxima,** insira 200.
1. Selecione **Guardar**.

#### <a name="31-test-cors"></a>3.1 Teste CORS

Valide que está pronto executando os seguintes passos:

1. Repita o passo CORS configurado. Para **origens permitidas,** entre`https://www.test-cors.org`
1. Navegue para [www.test-cors.org](https://www.test-cors.org/) 
1. Para a caixa **DEURL Remota,** colhe o URL do seu ficheiro HTML. Por exemplo, `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Selecione **Enviar Pedido**.
    O resultado `XHR status: 200`deve ser . 
    Se receber um erro, certifique-se de que as definições cors estão corretas. Você também pode precisar limpar o seu cache de navegador ou abrir uma sessão de navegação in-private pressionando Ctrl+Shift+P.
