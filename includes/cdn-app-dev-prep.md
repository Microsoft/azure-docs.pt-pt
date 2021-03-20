---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993386"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de escrever o código de gestão cdn, deve fazer alguma preparação para permitir que o código interaja com o Gestor de Recursos Azure. Para fazer esta preparação, você precisa:

* Crie um grupo de recursos para conter o perfil CDN criado neste tutorial
* Configure Azure Ative Directy para fornecer autenticação para a aplicação
* Aplique permissões ao grupo de recursos para que apenas os utilizadores autorizados do seu inquilino AZURE AD possam interagir com o perfil cdN

### <a name="creating-the-resource-group"></a>Criar o grupo de recursos
1. Inscreva-se no [Portal Azure](https://portal.azure.com).
2. Clique **em Criar um recurso.**
3. Procure por **grupo de recursos** e no painel de grupo de recursos, clique em **Criar**.

    ![Criação de um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nomeie o seu grupo de recursos *CdnConsoleTutorial*.  Selecione a sua subscrição e escolha um local perto de si.  Se desejar, pode clicar no Pin para a caixa **de verificação do painel** de instrumentos para fixar o grupo de recursos no painel de instrumentos do portal.  Pinning torna mais fácil de encontrar mais tarde.  Depois de ter feito as suas seleções, clique em **Criar**.

    ![Screenshot da caixa de diálogo do grupo de recursos.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Após a criação do grupo de recursos, se não o fixar no painel de **instrumentos,** poderá encontrá-lo clicando em Procurar, em seguida, **Grupos de Recursos**.  Para abri-la, clique no grupo de recursos.  Tome nota do seu ID de **assinatura.** Precisamos mais tarde.

    ![Screenshot da secção tutorial da consola C D N.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criar a aplicação AZure AD e aplicar permissões
Existem duas abordagens para a autenticação de aplicações com o Azure Ative Directory: Utilizadores individuais ou um diretor de serviço. Um diretor de serviço é semelhante a uma conta de serviço no Windows.  Em vez de conceder a um determinado utilizador permissões para interagir com os perfis da CDN, as permissões são concedidas ao principal do serviço.  Os princípios de serviço são normalmente utilizados para processos automatizados e não interativos.  Mesmo que este tutorial esteja a escrever uma aplicação de consola interativa, vamos focar-nos na abordagem principal do serviço.

A criação de um principal serviço consiste em várias etapas, incluindo a criação de uma aplicação Azure Ative Directory.  Para criá-lo, vamos [seguir este tutorial.](../articles/active-directory/develop/howto-create-service-principal-portal.md)

> [!IMPORTANT]
> Certifique-se de seguir todos os passos no [tutorial ligado.](../articles/active-directory/develop/howto-create-service-principal-portal.md)  É *importante* que o complete exatamente como descrito.  Certifique-se de que nota o seu **ID** do **inquilino, nome de domínio do inquilino** (geralmente um domínio *.onmicrosoft.com* a menos que tenha especificado um domínio personalizado), **ID do cliente,** e **chave de autenticação do cliente,** pois precisamos desta informação mais tarde.  Tenha cuidado para guardar a **identificação** do seu cliente e **a chave de autenticação** do cliente, uma vez que estas credenciais podem ser utilizadas por qualquer pessoa para executar operações como diretor de serviço.
>
> Quando chegar ao degrau denominado Configure multi-inusitário, selecione **Nº**.
>
> Quando chegar ao passo [Atribua a aplicação a uma função,](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)utilize o grupo de recursos criado anteriormente, *CdnConsoleTutorial*, mas em vez da função **Reader,** atribua o papel **de Contribuidor de Perfil CDN.**  Depois de atribuir a aplicação, a **função de Contribuinte de Perfil CDN** no seu grupo de recursos, volte a este tutorial. 
>
>

Uma vez criado o seu principal de serviço e atribuído a função **de Contribuinte de Perfil CDN,** a lâmina dos **Utilizadores** para o seu grupo de recursos deve ser semelhante à seguinte imagem.

![Lâmina de utilizadores](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Autenticação interativa do utilizador
Se, em vez de um principal de serviço, preferir a autenticação individual do utilizador interativo, o processo é semelhante ao de um diretor de serviço.  Na verdade, tens de seguir o mesmo procedimento, mas fazer algumas pequenas alterações.

> [!IMPORTANT]
> Siga apenas estes próximos passos se optar por utilizar a autenticação individual do utilizador em vez de um principal de serviço.
>
>

1. Ao criar a sua aplicação, em vez de **Aplicação Web,** escolha **a aplicação Native**.

    ![Aplicação nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na página seguinte, é solicitado um **URI de redirecionamento**.  O URI não será validado, mas lembre-se do que entrou. Precisa mais tarde.
3. Não há necessidade de criar uma chave de **autenticação do cliente.**
4. Em vez de atribuir um principal de serviço à **função de Contribuinte de Perfil CDN,** vamos atribuir utilizadores ou grupos individuais.  Neste exemplo, pode ver que atribuí o *Utilizador cdn de demonstração* para o papel **de Contribuinte de Perfil CDN.**  

    ![Acesso individual do utilizador](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
