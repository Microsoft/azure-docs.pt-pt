---
title: Problemas de resolução de problemas inscrevem-se numa aplicação da Azure AD My Apps
description: Problemas de resolução de problemas inscrevem-se numa aplicação da Azure AD My Apps
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 14b49c531ae11c056f9b6970e1ac00adcd68b296
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937173"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Problemas de resolução de problemas inscrevem-se numa aplicação da Azure AD My Apps

My Apps é um portal baseado na web que permite a um utilizador com uma conta de trabalho ou escola em Azure Ative Direy (Azure AD) visualizar e iniciar aplicações baseadas na nuvem a que o administrador da AD Azure lhes concedeu acesso. As minhas Apps são acedidas através de um navegador web em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Para saber mais sobre a utilização do Azure AD como fornecedor de identidade para uma aplicação, consulte o [What is Application Management in AD.](what-is-application-management.md) Para se atualizar rapidamente, confira a [Série Quickstart em Gestão de Aplicações](view-applications-portal.md).

Estas aplicações estão configuradas em nome do utilizador no portal AZure AD. A aplicação deve ser configurada corretamente e atribuída ao utilizador ou a um grupo do qual o utilizador é membro para ver a aplicação nas Minhas Apps. 

O tipo de aplicações que um utilizador pode estar a ver cair nas seguintes categorias:
-   Aplicações Microsoft 365
-   Aplicações da Microsoft e de terceiros configuradas com SSO baseado na federação
-   Aplicações SSO baseadas em palavras-passe
-   Aplicações com soluções SSO existentes

Aqui ficam algumas coisas para verificar se uma aplicação está a aparecer ou não.
- Certifique-se de que a aplicação é adicionada ao AZure AD e certifique-se de que o utilizador está atribuído. Para saber mais, consulte a [Série Quickstart em Gestão de Aplicações.](add-application-portal.md)
- Se uma aplicação foi recentemente adicionada, tenha o utilizador a assinar e de volta. 
- Se a aplicação necessitar de uma licença, como o Office, certifique-se de que o utilizador é atribuído a licença apropriada.
- O tempo que leva para alterações de licenciamento pode variar dependendo do tamanho e complexidade do grupo.

## <a name="general-issues-to-check-first"></a>Questões gerais para verificar primeiro

-   Certifique-se de que o navegador da Web cumpre os requisitos, consulte os [navegadores suportados pelas Minhas Apps.](../user-help/my-apps-portal-end-user-access.md)
-   Certifique-se de que o navegador do utilizador adicionou o URL da aplicação aos seus **sites fidedignos.**
-   Certifique-se de que a aplicação está **configurada** corretamente.
-   Certifique-se de que a conta do utilizador está **ativada** para iniciar sposições.
-   Certifique-se de que a conta do utilizador não está **bloqueada.**
-   Certifique-se de que a palavra-passe do utilizador **não está caducada ou esquecida.**
-   Certifique-se de que **a autenticação multi-factor** não bloqueia o acesso do utilizador.
-   Certifique-se de que uma **política de acesso condicional** ou uma política de **proteção de identidade** não estão a bloquear o acesso do utilizador.
-   Certifique-se de que as informações de contacto de **autenticação** de um utilizador estão atualizadas para permitir a aplicação das políticas de Autenticação Multi-Factor ou acesso condicional.
-   Certifique-se de também tentar limpar os cookies do seu navegador e tentar entrar novamente.

## <a name="problems-with-the-users-account"></a>Problemas na conta do utilizador
O acesso às Minhas Apps pode ser bloqueado devido a um problema na conta do utilizador. Seguem-se algumas formas de resolver problemas e resolver problemas com os utilizadores e as definições da sua conta:
-   [Verifique se existe uma conta de utilizador no Azure Ative Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Verifique o estado da conta de um utilizador](#check-a-users-account-status)
-   [Redefinir a senha de um utilizador](#reset-a-users-password)
-   [Ativar a reposição de palavras-passe self-service](#enable-self-service-password-reset)
-   [Verifique o estado de autenticação de vários fatores do utilizador](#check-a-users-multi-factor-authentication-status)
-   [Verifique as informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)
-   [Verifique os membros do grupo de um utilizador](#check-a-users-group-memberships)
-   [Verifique se um utilizador tem mais de 999 atribuições de funções de aplicações](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Verifique as licenças atribuídas a um utilizador](#check-a-users-assigned-licenses)
-   [Atribuir a um utilizador uma licença](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verifique se existe uma conta de utilizador no Azure Ative Directory
Para verificar se a conta de um utilizador está presente, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Verifique as propriedades do objeto do utilizador para ter certeza de que parecem como espera e não faltam dados.

### <a name="check-a-users-account-status"></a>Verifique o estado da conta de um utilizador
Para verificar o estado da conta de um utilizador, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Selecione **perfil**.
8.  Em **Definições,** certifique-se de que **o sinal de bloqueio** está definido para **nº**.

### <a name="reset-a-users-password"></a>Redefinir a senha de um utilizador
Para redefinir a palavra-passe de um utilizador, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Selecione o **botão de palavra-passe Reset** na parte superior do painel de utilização.
8.  Selecione o botão **de palavra-passe Reset** no painel **de palavras-passe Reset** que aparece.
9.  Copie a **palavra-passe temporária** ou **introduza uma nova palavra-passe** para o utilizador.
10. Comunique esta nova palavra-passe ao utilizador, sendo-lhes necessária a alteração desta palavra-passe durante a sua próxima entrada no Azure Ative Directory.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service
Para permitir o reset da palavra-passe de autosserviço, siga estes passos de implementação:
-   [Permitir que os utilizadores repusem as suas palavras-passe do Azure Ative Directory](../authentication/tutorial-enable-sspr.md)
-   [Permitir que os utilizadores reiniciem ou alterem as suas palavras-passe no Ative Directory no local](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verifique o estado de autenticação de vários fatores do utilizador
Para verificar o estado de autenticação de vários fatores do utilizador, siga estes passos:
1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2. Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4. Selecione **Utilizadores e grupos** no menu de navegação.
5. Selecione **Todos os utilizadores**.
6. Selecione o botão **de autenticação multi-factor** na parte superior do painel.
7. Assim que o Portal de **Administração de Autenticação Multi-Factor** estiver carregado, certifique-se de que está no separador **Utilizadores.**
8. Encontre o utilizador na lista de utilizadores pesquisando, filtrando ou classificando.
9. Selecione o utilizador da lista de utilizadores e **Ative,** **desativar** ou **impor a** autenticação de vários fatores conforme desejado.
   >[!NOTE]
   >Se um utilizador estiver num estado **forçado,** pode desativá-los **temporariamente** para os deixar voltar à sua conta. Uma vez que estejam de volta, pode então alterar o seu estado para **Ativado** novamente para exigir que voltem a registar os seus dados de contacto durante a sua próxima entrada. Em alternativa, pode seguir os passos na [informação de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.

### <a name="check-a-users-authentication-contact-info"></a>Verifique as informações de contacto de autenticação de um utilizador
Para verificar as informações de contacto de autenticação de um utilizador utilizadas para autenticação de vários fatores, acesso condicional, proteção de identidade e redefinição de palavras-passe, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Selecione **perfil**.
8.  Desloque-se para **informações de contacto de autenticação**.
9.  **Reveja** os dados registados para o utilizador e atualização conforme necessário.

### <a name="check-a-users-group-memberships"></a>Verifique os membros do grupo de um utilizador
Para verificar os membros do grupo de um utilizador, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Selecione **Grupos** para ver em que grupos o utilizador é membro.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Verifique se um utilizador tem mais de 999 atribuições de funções de aplicações
Se um utilizador tiver mais de 999 atribuições de funções de aplicação atribuídas a elas, então pode não ver todas as suas aplicações nas Minhas Apps.

Isto porque as Minhas Apps lêem atualmente até 999 atribuições de funções de aplicações para determinar as aplicações às quais os utilizadores estão atribuídos. Se um utilizador for designado para mais de 999 aplicações, não é possível controlar quais destas aplicações irão aparecer no portal My Apps.

Para verificar o número de atribuições de funções de aplicação concedidas a um utilizador, siga estes passos:
1. Instale o módulo [**Microsoft.Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell.
2. Executar `Connect-MgGraph -Scopes "Directory.Read.All"` e autenticar como **Administrador Global.**
3. Corra `$m = Get-MgUserAppRoleAssignment -UserId "<userId>" | Measure; $m.Count` para determinar o número de atribuições de funções de aplicação que o utilizador concedeu atualmente.

### <a name="check-a-users-assigned-licenses"></a>Verifique as licenças atribuídas a um utilizador
Para verificar as licenças atribuídas a um utilizador, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Selecione **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.

### <a name="assign-a-user-a-license"></a>Atribuir a um utilizador uma licença 
Para atribuir uma licença a um utilizador, siga estes passos:
1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**
2.  Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3.  Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4.  Selecione **Utilizadores e grupos** no menu de navegação.
5.  Selecione **Todos os utilizadores**.
6.  **Procure** o utilizador em que está interessado e **selecione a linha** para selecionar.
7.  Selecione **Licenças** para ver quais as licenças que o utilizador atualmente atribuiu.
8.  Selecione o botão **Atribuir.**
9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.
10. **Selecione opcional** o item **opções de atribuição** para atribuir granularmente produtos. Selecione **OK**.
11. Selecione o botão **Atribuir** para atribuir estas licenças a este utilizador.

## <a name="troubleshooting-deep-links"></a>Resolução de problemas em ligações profundas
Links profundos ou URLs de acesso ao utilizador são links que os seus utilizadores podem usar para aceder às suas aplicações SSO de palavra-passe diretamente das barras de URL dos seus navegadores. Ao navegar neste link, os utilizadores são automaticamente inscritos na aplicação sem terem de ir primeiro às Minhas Apps. O link é o mesmo que os utilizadores usam para aceder a estas aplicações a partir do lançador de aplicações Microsoft 365.

### <a name="checking-the-deep-link"></a>Verificando a ligação profunda

Para verificar se tem o elo de fundo correto, siga estes passos:
1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**
2. Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4. Selecione **Aplicações Empresariais** no menu de navegação esquerdo do Azure Active Directory.
5. Selecione **Todas as Aplicações** para ver uma lista de todas as aplicações.
   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**
6. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**
7. Abra a **Extensão do Diretório Ativo Azure** selecionando **todos os serviços** no topo do menu principal de navegação à esquerda.
8. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
9. Selecione **Aplicações Empresariais** no menu de navegação esquerdo do Azure Active Directory.
10. Selecione **Todas as Aplicações** para ver uma lista de todas as aplicações.
    * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**
11. Selecione a aplicação para a seguinte a verificação do link profundo.
12. Encontre o **URL de Acesso ao Utilizador da** etiqueta . A sua ligação profunda deve coincidir com esta URL.

## <a name="contact-support"></a>Contactar o suporte
Abra um bilhete de apoio com as seguintes informações, se disponível:
-   ID de erro de correlação
-   UPN (endereço de e-mail do utilizador)
-   TenantID
-   Tipo de browser
-   Fuso horário e tempo/prazo durante o erro ocorre
-   Traços de violino

## <a name="next-steps"></a>Passos seguintes
- [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
