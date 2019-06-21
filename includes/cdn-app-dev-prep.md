---
author: zhangmanling
ms.service: cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 7f80c8f1773cfeb8ddfb222d068a5c6571c2e5c7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184605"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de escrever o código de gerenciamento de CDN, tem de fazer uma preparação para permitir que o código interagir com o Azure Resource Manager. Para fazer esta preparação, terá de:

* Criar um grupo de recursos para conter o perfil CDN que criou neste tutorial
* Configurar o Azure Active Directory para fornecer autenticação para a aplicação
* Aplicar permissões ao grupo de recursos para que apenas os utilizadores autorizados no seu inquilino do Azure AD possam interagir com o perfil CDN

### <a name="creating-the-resource-group"></a>Criar o grupo de recursos
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso**.
3. Procure **grupo de recursos** e no painel do grupo de recursos, clique em **criar**.

    ![Criar um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Nome do seu grupo de recursos *CdnConsoleTutorial*.  Selecione a sua subscrição e escolha uma localização perto de si.  Se desejar, pode clicar a **afixar ao dashboard** caixa de seleção para afixar o grupo de recursos para o dashboard no portal.  Afixar torna mais fácil encontrar mais tarde.  Depois de fazer as suas seleções, clique em **criar**.

    ![O grupo de recursos de atribuição de nomes](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Depois de criar o grupo de recursos, se ele não tiver afixado ao dashboard, pode encontrá-lo ao clicar em **navegue**, em seguida, **grupos de recursos**.  Para abri-lo, clique no grupo de recursos.  Tome nota da sua **ID de subscrição**. Vamos precisar dele mais tarde.

    ![O grupo de recursos de atribuição de nomes](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criar aplicação do Azure AD e aplicar permissões
Existem duas abordagens para autenticação de aplicação com o Azure Active Directory: Utilizadores individuais ou um principal de serviço. Um principal de serviço é semelhante a uma conta de serviço no Windows.  Em vez de conceder permissões para interagir com os perfis CDN de um utilizador específico, em vez disso, são concedidas permissões ao principal de serviço.  Principais de serviço são normalmente utilizados para processos automatizados, não interativo.  Embora este tutorial é escrever uma aplicação de consola interativa, vamos nos concentrar na abordagem de principal de serviço.

Criar um principal de serviço consiste em várias etapas, incluindo a criação de uma aplicação do Azure Active Directory.  Para criá-lo, vamos [siga este tutorial](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Certifique-se de que siga todos os passos a [tutorial ligado](../articles/active-directory/develop/howto-create-service-principal-portal.md).  É *importante* que conclua-lo exatamente como descrito.  Certifique-se de observar seu **ID do inquilino**, **nome de domínio de inquilino** (normalmente um *. onmicrosoft.com* domínio, a menos que tiver especificado um domínio personalizado), **ID de cliente** , e **chave de autenticação de cliente**, porque temos essas informações mais tarde.  Tenha o cuidado de proteger sua **ID de cliente** e **chave de autenticação de cliente**, como estas credenciais podem ser utilizadas por qualquer pessoa para executar operações como o principal de serviço.
>
> Quando para o passo com o nome configurar aplicações de multi-inquilino, selecione **não**.
>
> Quando vai para o passo [atribuir a aplicação a uma função](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), utilize o grupo de recursos criado anteriormente, *CdnConsoleTutorial*, mas em vez do **leitor** função, atribuir a **Contribuidor de perfil de CDN** função.  Depois de atribuir a aplicação do **Contribuidor de perfil de CDN** função no seu grupo de recursos, retorno para este tutorial. 
>
>

Depois de criado o seu principal de serviço e atribuído a **Contribuidor de perfil de CDN** função, o **utilizadores** painel para o grupo de recursos deve ter um aspeto semelhante à seguinte imagem.

![Painel de utilizadores](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Autenticação de utilizador interativa
Se, em vez de um principal de serviço, preferiria ter autenticação de utilizador individuais interativa, o processo é semelhante de um principal de serviço.  Na verdade, terá de seguir o mesmo procedimento, mas fazer algumas pequenas alterações.

> [!IMPORTANT]
> Apenas se, siga estes passos são a escolha utilizar a autenticação de utilizador individuais em vez de um principal de serviço.
>
>

1. Ao criar a sua aplicação, em vez de **aplicação Web**, escolha **aplicativo nativo**.

    ![Aplicação nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na página seguinte, lhe for pedido para um **URI de redirecionamento**.  O URI não validados, mas lembre-se de que introduziu. Precisa dele mais tarde.
3. Não é necessário para criar uma **chave de autenticação de cliente**.
4. Em vez de atribuir um principal de serviço para o **Contribuidor de perfil de CDN** função, vamos atribuir utilizadores individuais ou grupos.  Neste exemplo, pode ver que que lhe *demonstração de utilizador de CDN* para o **Contribuidor de perfil de CDN** função.  

    ![Acesso de utilizador individuais](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
