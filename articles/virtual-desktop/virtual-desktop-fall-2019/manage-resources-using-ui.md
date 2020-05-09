---
title: Implementar ferramenta de gestão com um modelo de Gestor de Recursos Azure - Azure
description: Como instalar uma ferramenta de interface de utilizador com um modelo de Gestor de Recursos Azure para gerir os recursos do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: abe9b060793983e42ab432924ca5d6d7f43d307d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615243"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Implementar uma ferramenta de gestão com um modelo de Gestor de Recursos Azure

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure.

As instruções deste artigo dir-lhe-ão como implementar o UI utilizando um modelo de Gestor de Recursos Azure.

## <a name="important-considerations"></a>Considerações importantes

Uma vez que a aplicação requer consentimento para interagir com o Windows Virtual Desktop, esta ferramenta não suporta cenários Business-to-Business (B2B). A subscrição de cada inquilino do Azure Ative Directory (AAD) necessitará da sua própria implantação separada da ferramenta de gestão.

Esta ferramenta de gestão é uma amostra. A Microsoft fornecerá importantes atualizações de segurança e qualidade. O [código fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Clientes e parceiros são encorajados a personalizar a ferramenta de acordo com as suas necessidades de negócio.

Para seguir os navegadores são compatíveis com a ferramenta de gestão:
- Google Chrome 68 ou mais tarde
- Microsoft Edge 40.15063 ou mais tarde
- Mozilla Firefox 52.0 ou mais tarde
- Safari 10 ou mais tarde (apenas macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que precisa para implementar a ferramenta de gestão

Antes de implementar a ferramenta de gestão, vai precisar de um utilizador do Azure Ative Directory (Azure AD) para criar um registo de aplicações e implementar a UI de gestão. Este utilizador deve:

- Ter a autenticação multi-factor Azure (MFA) desativada
- Tenha permissão para criar recursos na sua subscrição Azure
- Tenha permissão para criar uma aplicação Azure AD. Siga estes passos para verificar se o utilizador tem as permissões necessárias seguindo as instruções em [permissões requeridas](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Depois de implementar e configurar a ferramenta de gestão, recomendamos que peça a um utilizador que lance o UI de gestão para garantir que tudo funcione. O utilizador que lançar a UI de gestão deve ter uma atribuição de funções que lhes permita visualizar ou editar o inquilino do Windows Virtual Desktop.

## <a name="deploy-the-management-tool"></a>Implementar a ferramenta de gestão

Antes de começar, certifique-se de que as aplicações do servidor e do cliente têm consentimento visitando a página de consentimento virtual do [Windows](https://rdweb.wvd.microsoft.com) Para o Diretório Ativo Azure (AAD) representado.

Siga estas instruções para implementar o modelo de Gestão de Recursos Azure:

1. Vá à [página GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Desloque o modelo para Azure.
    - Se estiver a implementar uma subscrição da Enterprise, desloque-se e selecione **Deploy para Azure**. 
    - Se estiver a implementar uma subscrição do Cloud Solution Provider, siga estas instruções para implementar para o Azure:
        1. Desloque-se para baixo e clique à **direita, desloque-se para O Azure**e, em seguida, selecione **Copy Link Location**.
        2. Abra um editor de texto como o Notepad e colhe o link lá.
        3. Logo <https://portal.azure.com/> a seguir e antes da hashtag (#), insira um sinal de at sign (@) seguido pelo nome de domínio do inquilino. Aqui está um exemplo do <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>formato: .
        4. Inscreva-se no portal Azure como utilizador com permissões Admin/Contributor para a subscrição do Cloud Solution Provider.
        5. Colhe o link que copiou para o editor de texto na barra de endereços.
3. Ao introduzir os parâmetros, faça o seguinte:
    - Para o parâmetro **isServicePrincipal,** selecione **falso**.
    - Para as credenciais, introduza as suas credenciais Azure AD com a autenticação multifactor desativada. Estas credenciais serão usadas para criar a aplicação Azure AD e os recursos Azure. Para saber mais, veja o [Que precisa para implementar a ferramenta de gestão.](#what-you-need-to-deploy-the-management-tool)
    - Para o nome da **aplicação,** utilize um nome único para a sua aplicação que será registada no seu Diretório Ativo Azure. Este nome também será usado para o URL da aplicação web. Por exemplo, pode usar um nome como "Apr3UX".
4. Assim que fornecer os parâmetros, aceite os termos e condições e selecione **Comprar**.

## <a name="provide-consent-for-the-management-tool"></a>Dar consentimento para a ferramenta de gestão

Após o modelo gitHub Azure Resource Manager completo, você encontrará um grupo de recursos contendo dois serviços de aplicações juntamente com um plano de serviço de aplicações no portal Azure.

Antes de iniciar sessão e utilizar a ferramenta de gestão, deve dar consentimento para a nova aplicação Azure AD associada à ferramenta de gestão. O consentimento permite que a ferramenta de gestão faça com que as chamadas de gestão do Windows Virtual Desktop sejam chamadas em nome do utilizador atualmente inscrito na ferramenta.

![Uma imagem que mostra as permissões que estão a ser fornecidas quando consentir com a ferramenta de gestão da UI.](../media/management-ui-delegated-permissions.png)

Para determinar que utilizador pode utilizar para iniciar sessão na ferramenta, aceda à página de definições de utilizador do [Diretório Ativo do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e tome nota do valor para os Utilizadores pode consentir em aplicações que **acedam aos dados**da empresa em seu nome.

![Uma imagem que mostra se os utilizadores podem conceder consentimento a aplicações apenas para o seu utilizador.](../media/management-ui-user-consent-allowed.png)

- Se o valor for definido para **Sim,** pode iniciar sessão com qualquer conta de utilizador no Diretório Ativo Do Azure e dar consentimento apenas a esse utilizador. No entanto, se iniciar sessão na ferramenta de gestão com um utilizador diferente mais tarde, terá de realizar o mesmo consentimento novamente.
- Se o valor estiver definido para **Não,** deve inscrever-se como Administrador Global no Diretório Ativo Azure e fornecer consentimento administrativo para todos os utilizadores do diretório. Nenhum outro utilizador enfrentará um pedido de consentimento.


Assim que decidir qual utilizador utilizará para dar consentimento, siga estas instruções para dar o consentimento à ferramenta:

1. Vá aos seus recursos Azure, selecione o recurso Azure App Services com o nome que forneceu no modelo (por exemplo, Apr3UX) e navegue para o URL associado ao mesmo; por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Inscreva-se na utilização da conta de utilizador do Diretório Ativo Azure apropriado.
3. Se autenticado com um Administrador Global, pode agora selecionar a caixa de verificação para **Consent em nome da sua organização**. Selecione **Aceitar** para dar consentimento.
   
   ![Uma imagem mostrando a página completa de consentimento que o utilizador ou administrador irá ver.](../media/management-ui-consent-page.png)

Isto irá agora levá-lo à ferramenta de gestão.

## <a name="use-the-management-tool"></a>Use a ferramenta de gestão

Depois de fornecer o consentimento para a organização ou para um utilizador especificado, pode aceder à ferramenta de gestão a qualquer momento.

Siga estas instruções para lançar a ferramenta:

1. Selecione o recurso Azure App Services com o nome que forneceu no modelo (por exemplo, Apr3UX) e navegue para o URL associado ao mesmo; por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Inscreva-se utilizando as suas credenciais de ambiente de trabalho virtual do Windows.
3. Quando for solicitado a escolher um Grupo de Inquilinos, selecione **Default Tenant Group** da lista de drop-down.
4. Ao selecionar O **Grupo de Inquilinos Padrão,** deve aparecer um menu no lado esquerdo da sua janela. Neste menu, encontre o nome do seu grupo de inquilinos e selecione-o.
  
  > [!NOTE]
  > Se tiver um grupo de inquilinos personalizados, insira o nome manualmente em vez de escolher a partir da lista de lançamentos.

## <a name="report-issues"></a>Comunicar problemas

Se encontrar algum problema com a ferramenta de gestão ou outras ferramentas de ambiente de trabalho virtual do Windows, siga as instruções nos modelos do Gestor de [Recursos Azure para serviços](https://github.com/Azure/RDS-Templates/blob/master/README.md) de ambiente de trabalho remotos para os reportar no GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implementar e conectar-se com a ferramenta de gestão, pode aprender a usar o Serviço Azure para monitorizar problemas de serviço e aconselhamento de saúde. Para saber mais, consulte o tutorial de alertas de [serviço Configuração.](set-up-service-alerts-2019.md)