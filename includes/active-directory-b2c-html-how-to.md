---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/26/2020
ms.author: marsma
ms.openlocfilehash: 4cf8eba9632c51ce9b5bcc42feae2446348890c2
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77651735"
---
## <a name="use-custom-page-content"></a>Use conteúdo de página personalizado

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C.

### <a name="how-it-works"></a>Como funciona

O Azure AD B2C executa o código no navegador do seu cliente utilizando a [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/). No prazo de execução, o conteúdo é carregado a partir de um URL que especifica no fluxo do utilizador ou na política personalizada. Cada página na experiência do utilizador carrega o seu conteúdo a partir do URL que especifica para essa página. Depois de o conteúdo ser carregado a partir do seu URL, é fundido com um fragmento HTML inserido pelo Azure AD B2C, e depois a página é exibida ao seu cliente.

![Margem de conteúdo de página personalizada](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Conteúdo personalizado da página HTML

Crie uma página HTML com a sua própria marca para servir o conteúdo da página personalizada. Esta página pode ser uma página estática `*.html`, ou uma página dinâmica como .NET, Node.js ou PHP.

O conteúdo da página personalizada pode conter quaisquer elementos HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como iframes. O único elemento necessário é um elemento de mergulho com `id` definido para `api`, como este `<div id="api"></div>` dentro da sua página HTML.

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
| [exceção.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. | *api.error* |
| [autoafirmado.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página autoafirmada.** Utilize este ficheiro como conteúdo de página personalizada para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de registo de conta local, reset de palavra-passe e muito mais. O formulário pode conter vários controlos de entrada, tais como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de rádio, caixas de entrega únicas e caixas de verificação multi-selecionadas. | *api.localaccountsignin,* *api.localaccountsignup* , *api.localaccountpasswordreset*, *api.self-afirmou* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação de vários fatores.** Nessa página, os usuários podem verificar seus números de telefone (usando texto ou voz) durante a inscrição ou entrada. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os utilizadores podem aceder para atualizar o seu perfil. Esta página é semelhante à página de inscrição de conta social, exceto para os campos de entrada de senha. | *api.self-asserted.profileupdate* |
| [unificado.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição unificada ou inscrição.** Esta página trata do processo de inscrição e de inscrição do utilizador. Os utilizadores podem utilizar fornecedores de identidade empresarial, fornecedores de identidade social, como facebook ou Google+, ou contas locais. | *api.signuporsignina* |

## <a name="hosting-the-page-content"></a>Hospedar o conteúdo da página

Ao utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI, apresente o seu conteúdo uI em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob,](../articles/storage/blobs/storage-blobs-introduction.md) [Serviços de Aplicações Azure,](/azure/app-service/)servidores web, CDNs, AWS S3 ou sistemas de partilha de ficheiros.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para usar conteúdo de página personalizada

- Utilize um URL absoluto quando incluir recursos externos como ficheiros de mídia, CSS e JavaScript no seu ficheiro HTML.
- Utilizando a versão de layout da [página](../articles/active-directory-b2c/page-layout.md) 1.2.0 ou superior, pode adicionar o `data-preload="true"` atributo nas suas tags HTML para controlar a ordem de carga para CSS e JavaScript. Com `data-preload=true`, a página é construída antes de ser mostrada ao utilizador. Este atributo ajuda a evitar que a página "cintile" pré-carregando o ficheiro CSS, sem que o HTML não-estilo seja mostrado ao utilizador. O seguinte código HTML mostra a utilização da etiqueta `data-preload`.
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
- Devido a restrições de segurança, o Azure AD B2C não suporta elementos `frame`, `iframe`ou `form` HTML.

## <a name="custom-page-content-walkthrough"></a>Walkthrough de conteúdo de página personalizada

Aqui está uma visão geral do processo:

1. Prepare um local para hospedar o conteúdo da página personalizada (um ponto final HTTPS acessível ao público, ativado pelo CORS).
1. Descarregue e personalize um ficheiro de conteúdo de página predefinido, por exemplo, `unified.html`.
1. Publique o conteúdo da sua página personalizada o seu ponto final HTTPS disponível publicamente.
1. Detete a partilha de recursos de origem cruzada (CORS) para a sua aplicação web.
1. Aponte a sua política para o seu conteúdo de política personalizado URI.

### <a name="1-create-your-html-content"></a>1. Crie o seu conteúdo HTML

Crie um conteúdo de página personalizado com a marca do seu produto no título.

1. Copie o trecho de código HTML a seguir. É bem formado HTML5 com um elemento vazio chamado *\<div id="api"\>\</div\>* localizado dentro do corpo\< *\>* tags. Esse elemento indica onde Azure AD B2C conteúdo deve ser inserido.

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
> Os elementos de formulário HTML serão removidos devido a restrições de segurança se você usar login.microsoftonline.com. Se pretender utilizar elementos de formulário HTML no seu conteúdo HTML personalizado, [utilize b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Criar uma conta de armazenamento Azure Blob

Neste artigo, usamos o armazenamento de BLOBs do Azure para hospedar nosso conteúdo. Pode optar por hospedar o seu conteúdo num servidor web, mas tem de ativar o [CORS no seu servidor web](https://enable-cors.org/server.html).

Para alojar o seu conteúdo HTML no armazenamento blob, execute os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu **Hub,** selecione **new** > **Storage** > **Storage .**
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

Para criar um contêiner público no armazenamento de BLOBs, execute as seguintes etapas:

1. Sob o **serviço Blob** no menu à esquerda, selecione **Blobs**.
1. **Selecione +Recipiente**.
1. Para **Nome,** introduza *raiz.* Este pode ser um nome à sua escolha, por *exemplo, wingtiptoys*, mas usamos *raiz* neste exemplo para a simplicidade.
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
1. No navegador da Web, navegue até a URL que você copiou para verificar se o blob que você carregou está acessível. Se estiver inacessível, por exemplo, se encontrar um erro `ResourceNotFound`, certifique-se de que o tipo de acesso do recipiente está definido para **a bolha**.

### <a name="3-configure-cors"></a>3. Configure cors

Configure o armazenamento de BLOB para compartilhamento de recursos entre origens executando as seguintes etapas:

1. No menu, selecione **CORS**.
1. Para **origens permitidas,** introduza `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao introduzir o nome do seu inquilino.
1. Para **métodos permitidos,** selecione `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos,** introduza um asterisco (*).
1. Para **cabeçalhos expostos,** introduza um asterisco (*).
1. Para **a idade máxima,** insira 200.
1. Selecione **Guardar**.

#### <a name="31-test-cors"></a>3.1 Teste CORS

Valide se você está pronto executando as seguintes etapas:

1. Navegue para [www.test-cors.org](https://www.test-cors.org/) e colhe o URL na caixa **de URL remota.**
1. Selecione **Enviar Pedido**.
    Se receber um erro, certifique-se de que as definições cors estão corretas. Talvez você também precise limpar o cache do navegador ou abrir uma sessão de navegação em particular pressionando Ctrl + Shift + P.
