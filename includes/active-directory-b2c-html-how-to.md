---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 9b660bf20c90a84780175e70573c96a0ce1b0b7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377414"
---
## <a name="use-custom-page-content"></a>Use conteúdo de página personalizado

Ao utilizar a funcionalidade de personalização da UI da página, pode personalizar o aspeto e a sensação de qualquer política personalizada. Também pode manter a consistência visual e de marca entre a sua aplicação e o Azure AD B2C.

### <a name="how-it-works"></a>Como funciona

O Azure AD B2C executa código no navegador do seu cliente utilizando [a Partilha de Recursos de Origem Cruzada (CORS)](https://www.w3.org/TR/cors/). No tempo de execução, o conteúdo é carregado a partir de um URL que especifica no fluxo do utilizador ou na política personalizada. Cada página na experiência do utilizador carrega o seu conteúdo a partir do URL que especifica para essa página. Depois de o conteúdo ser carregado a partir do seu URL, é fundido com um fragmento HTML inserido pelo Azure AD B2C e, em seguida, a página é exibida ao seu cliente.

![Margem de conteúdo de página personalizada](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Conteúdo personalizado da página HTML

Crie uma página HTML com a sua própria marca para servir o conteúdo da sua página personalizada. Esta página pode ser uma `*.html` página estática, ou uma página dinâmica como .NET, Node.js ou PHP.

O conteúdo da sua página personalizada pode conter quaisquer elementos HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como os iframes. O único elemento necessário é um elemento de div com `id` definido para , tal como este dentro da sua página `api` `<div id="api"></div>` HTML.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalize as páginas AZURE AD B2C padrão

Em vez de criar o conteúdo da sua página personalizada do zero, pode personalizar o conteúdo padrão da página do Azure AD B2C.

A tabela que se segue lista o conteúdo da página predefinida fornecido pelo Azure AD B2C. Descarregue os ficheiros e use-os como ponto de partida para criar as suas próprias páginas personalizadas.

| Página padrão | Descrição | ID de definição de conteúdo<br/>(apenas política personalizada) |
|:-----------------------|:--------|-------------|
| [exception.htm](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Esta página é apresentada quando se encontra uma exceção ou um erro. | *api.erro* |
| [selfasserted.htm](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página auto-assertada.** Utilize este ficheiro como conteúdo de página personalizada para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de inscrição de conta local, redefinição de senha e muito mais. O formulário pode conter vários controlos de entrada, tais como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de rádio, caixas de entrega de uma única seleção e caixas de verificação multi-selecionadas. | *api.localaccountsignin,* *api.localaccountsignup,* *api.localaccountpasswordreset,* *api.selfasserted* |
| [multifactor-1.0.0.htm](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multi-factor**. Nesta página, os utilizadores podem verificar os seus números de telefone (utilizando texto ou voz) durante a inscrição ou a inscrição. | *api.phonefactor* |
| [updateprofile.htm](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfis**. Esta página contém um formulário que os utilizadores podem aceder para atualizar o seu perfil. Esta página é semelhante à página de inscrição de conta social, exceto para os campos de entrada de senha. | *api.selfasserted.profileupdate* |
| [unified.htm](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou inscrição**unificada . Esta página trata do processo de inscrição e inscrição do utilizador. Os utilizadores podem usar fornecedores de identidade empresarial, fornecedores de identidade social, como facebook ou Google+, ou contas locais. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hospedar o conteúdo da página

Ao utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI, hospede o seu conteúdo de UI em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App Services,](/azure/app-service/)servidores web, CDNs, AWS S3 ou sistemas de partilha de ficheiros.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para a utilização de conteúdo de página personalizado

- Utilize um URL absoluto quando incluir recursos externos como suportes, ficheiros CSS e JavaScript no seu ficheiro HTML.
- Utilizando [a versão de layout](../articles/active-directory-b2c/page-layout.md) da página 1.2.0 ou superior, pode adicionar o atributo nas suas `data-preload="true"` tags HTML para controlar a ordem de carga para CSS e JavaScript. Com `data-preload=true` , a página é construída antes de ser mostrada ao utilizador. Este atributo ajuda a evitar que a página "cintile" pré-carregando o ficheiro CSS, sem que o HTML não-estilo seja mostrado ao utilizador. O seguinte corte de código HTML mostra a utilização da `data-preload` etiqueta.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Recomendamos que comece com o conteúdo da página predefinido e construa em cima dele.
- Pode incluir o JavaScript no seu conteúdo personalizado tanto para fluxos de utilizador como para políticas [personalizadas.](../articles/active-directory-b2c/user-flow-javascript-overview.md) [custom policies](../articles/active-directory-b2c/javascript-samples.md)
- As versões de navegador suportadas são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Suporte limitado para Internet Explorer 9 e 8
  - Google Chrome 42.0 e acima
  - Mozilla Firefox 38.0 e acima
  - Safari para iOS e macOS, versão 12 e superior
- Devido a restrições de segurança, o Azure AD B2C não suporta `frame` `iframe` elementos , ou `form` HTML.

## <a name="custom-page-content-walkthrough"></a>Walkthrough de conteúdo de página personalizada

Aqui está uma visão geral do processo:

1. Prepare uma localização para hospedar o seu conteúdo de página personalizado (um ponto final HTTPS acessível ao público, ativado pelo CORS).
1. Faça o download e personalize um ficheiro de conteúdo de página predefinido, por `unified.html` exemplo.
1. Publique o seu conteúdo de página personalizado o seu ponto final HTTPS disponível publicamente.
1. Desaprove a partilha de recursos de origem cruzada (CORS) para a sua aplicação web.
1. Aponte a sua política para o seu conteúdo de política personalizada URI.

### <a name="1-create-your-html-content"></a>1. Crie o seu conteúdo HTML

Crie um conteúdo de página personalizado com a marca do seu produto no título.

1. Copie o seguinte corte HTML. É html5 bem formado com um elemento vazio chamado *\<div id="api"\>\</div\>* localizado dentro das *\<body\>* etiquetas. Este elemento indica onde deve ser inserido o conteúdo Azure AD B2C.

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

1. Cole o corte copiado num editor de texto e, em seguida, guarde o ficheiro como *customize-ui.html*.

> [!NOTE]
> Os elementos do formulário HTML serão removidos devido a restrições de segurança se utilizar login.microsoftonline.com. Se pretender utilizar elementos de forma HTML no seu conteúdo HTML personalizado, [utilize b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Criar uma conta de armazenamento Azure Blob

Neste artigo, utilizamos o armazenamento Azure Blob para hospedar o nosso conteúdo. Pode optar por hospedar o seu conteúdo num servidor web, mas tem de [ativar o CORS no seu servidor web.](https://enable-cors.org/server.html)

Para hospedar o seu conteúdo HTML no armazenamento Blob, execute os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu **Hub,** selecione **Nova**conta de Armazenamento de  >  **Storage**  >  **Armazenamento.**
1. Selecione uma **Subscrição** para a sua conta de armazenamento.
1. Crie um **grupo de Recursos** ou selecione um existente.
1. Insira um **Nome** único para a sua conta de armazenamento.
1. Selecione a **localização Geográfica** para a sua conta de armazenamento.
1. **O modelo de implementação** pode continuar a ser **o Gestor de Recursos.**
1. **O desempenho** pode continuar a ser **Standard**.
1. Alterar **Conta Tipo** para **Blob armazenamento**.
1. **A replicação** pode permanecer **RA-GRS**.
1. **O nível de acesso** pode permanecer **quente**.
1. Selecione **Review + criar** para criar a conta de armazenamento.
    Após a implementação concluída, a página **da conta de Armazenamento** abre-se automaticamente.

#### <a name="21-create-a-container"></a>2.1 Criar um recipiente

Para criar um recipiente público no armazenamento blob, execute os seguintes passos:

1. No **serviço Blob** no menu da esquerda, selecione **Blobs**.
1. Selecione **+Recipiente**.
1. Para **nome,** *introduza raiz*. O nome pode ser um nome à sua escolha, por exemplo, *contoso,* mas usamos *raiz* neste exemplo para simplicidade.
1. Para **o nível de acesso público**, selecione **Blob,** em **seguida, OK**.
1. Selecione **raiz** para abrir o novo recipiente.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Faça upload dos seus ficheiros de conteúdo de página personalizada

1. Selecione **Carregar**.
1. Selecione o ícone de pasta ao lado **de Selecionar um ficheiro**.
1. Navegue e selecione **customize-ui.html**, que criou anteriormente na secção de personalização da Página UI.
1. Se pretender fazer o upload para uma sub-dobragem, expanda **o Advanced** e introduza um nome de pasta no Upload para **a pasta**.
1. Selecione **Carregar**.
1. Selecione a ** bolha decustomize-ui.html** que fez o upload.
1. À direita da caixa de texto **URL,** selecione o ícone **copy to clipboard** para copiar o URL para a sua área de transferência.
1. No navegador web, navegue para o URL que copiou para verificar se a bolha que carregou está acessível. Se for inacessível, por exemplo, se encontrar um `ResourceNotFound` erro, certifique-se de que o tipo de acesso ao recipiente está definido para **borbulhar**.

### <a name="3-configure-cors"></a>3. Configure CORS

Configure o armazenamento blob para partilha de recursos de origem cruzada executando os seguintes passos:

1. No menu, selecione **CORS**.
1. Para **origens permitidas,** insira `https://your-tenant-name.b2clogin.com` . `your-tenant-name`Substitua-o pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do seu inquilino.
1. Para **Métodos Permitidos**, selecione ambos `GET` e `OPTIONS` .
1. Para **cabeçalhos permitidos,** introduza um asterisco (*).
1. Para **cabeçalhos expostos,** introduza um asterisco (*).
1. Para **a idade máxima,** insira 200.
1. Selecione **Guardar**.

#### <a name="31-test-cors"></a>3.1 Ensaio CORS

Valide que está pronto executando os seguintes passos:

1. Repita o passo DE CONFIGURAÇÃO CORS. Para **origens permitidas,** insira `https://www.test-cors.org`
1. Navegue até [www.test-cors.org](https://www.test-cors.org/) 
1. Para a caixa **de URL remota,** cole o URL do seu ficheiro HTML. Por exemplo, `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selecione **Enviar Pedido**.
    O resultado deve ser `XHR status: 200` . 
    Se receber um erro, certifique-se de que as definições do CORS estão corretas. Também poderá ter de limpar a cache do seu navegador ou abrir uma sessão de navegação privada pressionando ctrl+Shift+P.
