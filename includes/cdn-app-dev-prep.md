---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77608717"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de escrever o código de gestão da CDN, deve fazer alguma preparação para permitir que o código interaja com o Gestor de Recursos Azure. Para fazer esta preparação, precisa de:

* Crie um grupo de recursos para conter o perfil CDN criado neste tutorial
* Configure Diretório Ativo Azure para fornecer autenticação para a aplicação
* Aplicar permissões ao grupo de recursos para que apenas utilizadores autorizados do seu inquilino Azure AD possam interagir com o perfil de CDN

### <a name="creating-the-resource-group"></a>Criar o grupo de recursos
1. Inscreva-se no [Portal Azure.](https://portal.azure.com)
2. Clique em **Criar um recurso**.
3. Procure o **grupo Derecursos** e no painel do grupo Recursos, clique em **Criar**.

    ![Criação de um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nomeie o seu grupo de recursos *CdnConsoleTutorial*.  Selecione a sua subscrição e escolha um local perto de si.  Se desejar, pode clicar no **Pin para o painel de** verificação para fixar o grupo de recursos no painel de instrumentos do portal.  A fixação torna mais fácil encontrar mais tarde.  Depois de ter feito as suas seleções, clique em **Criar**.

    ![Nomear o grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Depois de criado o grupo de recursos, se não o tiver agarrado ao seu painel de instrumentos, pode encontrá-lo clicando em **Browse**, em seguida, **Grupos de Recursos**.  Para abri-la, clique no grupo de recursos.  Tome nota do seu ID de **subscrição**. Precisamos mais tarde.

    ![Nomear o grupo de recursos](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criação do pedido de AD Azure e aplicação de permissões
Existem duas abordagens para a autenticação de apps com o Diretório Ativo Azure: utilizadores individuais ou um diretor de serviço. Um diretor de serviço é semelhante a uma conta de serviço no Windows.  Em vez de conceder permissões específicas a um utilizador específico para interagir com os perfis da CDN, as permissões são concedidas ao diretor de serviço.  Os diretores de serviço são normalmente utilizados para processos automatizados e não interativos.  Mesmo que este tutorial esteja a escrever uma aplicação de consola interativa, vamos focar-nos na abordagem principal do serviço.

A criação de um diretor de serviço consiste em várias etapas, incluindo a criação de uma aplicação azure Ative Directory.  Para criá-lo, vamos [seguir este tutorial.](../articles/active-directory/develop/howto-create-service-principal-portal.md)

> [!IMPORTANT]
> Certifique-se de seguir todos os passos do [tutorial ligado.](../articles/active-directory/develop/howto-create-service-principal-portal.md)  É *importante* que o complete exatamente como descrito.  Certifique-se de que nota o seu ID do **seu inquilino,** nome de **domínio do inquilino** (geralmente um domínio *.onmicrosoft.com,* a menos que tenha especificado um domínio personalizado), ID do **cliente,** e chave de autenticação do **cliente,** pois precisamos desta informação mais tarde.  Tenha cuidado para guardar o ID do **cliente** e a chave de autenticação do **cliente,** uma vez que estas credenciais podem ser usadas por qualquer pessoa para executar operações como diretor de serviço.
>
> Quando chegar ao passo denominado Configure aplicação multi-inquilino, selecione **No**.
>
> Quando chegar ao passo [Atribuir a aplicação a uma função](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), use o grupo de recursos criado anteriormente, *CdnConsoleTutorial,* mas em vez da função **de Leitor,** atribua a função de Colaborador de **Perfil CDN.**  Depois de atribuir a aplicação a função de Colaborador de **Perfil CDN** no seu grupo de recursos, volte a este tutorial. 
>
>

Uma vez criado o seu diretor de serviço e atribuído a função de Colaborador de **Perfil CDN,** a lâmina **utilizadores** para o seu grupo de recursos deve ser semelhante à imagem seguinte.

![Lâmina de utilizadores](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Autenticação interativa do utilizador
Se, em vez de um diretor de serviço, preferir ter a autenticação individual interativa do utilizador, o processo é semelhante ao de um diretor de serviço.  Na verdade, é preciso seguir o mesmo procedimento, mas fazer algumas pequenas alterações.

> [!IMPORTANT]
> Siga apenas estes próximos passos se optar por utilizar a autenticação individual do utilizador em vez de um diretor de serviço.
>
>

1. Ao criar a sua aplicação, em vez de **Aplicação Web,** escolha **a aplicação Nativa.**

    ![Aplicação nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na página seguinte, é solicitado um **URI redirecionado.**  O URI não será validado, mas lembre-se do que entrou. Precisa mais tarde.
3. Não há necessidade de criar uma chave de **autenticação do cliente.**
4. Em vez de atribuir um principal de serviço à função de Colaborador de **Perfil CDN,** vamos atribuir utilizadores ou grupos individuais.  Neste exemplo, pode ver que atribuí o *CDN Demo User* à função de Colaborador de **Perfil CDN.**  

    ![Acesso individual ao utilizador](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
