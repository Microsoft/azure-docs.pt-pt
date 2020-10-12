---
title: Implementar ferramenta de gestão com um modelo de Gestor de Recursos Azure - Azure
description: Como instalar uma ferramenta de interface de utilizador com um modelo de Gestor de Recursos Azure para gerir os recursos do Windows Virtual Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 09e0b3bbac0bdc1d268aa7f24741aeb12a7d366d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462586"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Implemente uma ferramenta de gestão virtual do Windows Desktop (clássico) com um modelo de Gestor de Recursos Azure

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

As instruções deste artigo dir-lhe-ão como implantar a UI utilizando um modelo de Gestor de Recursos Azure.

## <a name="important-considerations"></a>Considerações importantes

Uma vez que a aplicação requer consentimento para interagir com o Windows Virtual Desktop, esta ferramenta não suporta cenários Business-to-Business (B2B). Cada subscrição do Azure Ative Directory (AAD) precisará da sua própria implementação separada da ferramenta de gestão.

Esta ferramenta de gestão é uma amostra. A Microsoft fornecerá importantes atualizações de segurança e qualidade. O [código fonte está disponível no GitHub.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) Os clientes e parceiros são encorajados a personalizar a ferramenta para se adaptarem às suas necessidades de negócio.

Os seguintes navegadores são compatíveis com a ferramenta de gestão:
- Google Chrome 68 ou mais tarde
- Microsoft Edge 40.15063 ou mais tarde
- Mozilla Firefox 52.0 ou mais tarde
- Safari 10 ou mais tarde (apenas para macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que precisa para implementar a ferramenta de gestão

Antes de implementar a ferramenta de gestão, precisará de um utilizador do Azure Ative Directory (Azure AD) para criar um registo de aplicações e implementar a UI de gestão. Este utilizador deve:

- Desativar a autenticação multi-factor Azure (MFA)
- Tenha permissão para criar recursos na sua subscrição Azure
- Tenha permissão para criar uma aplicação AD Azure. Siga estes passos para verificar se o seu utilizador tem as permissões necessárias seguindo as instruções nas [permissões Requeridas](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Depois de implementar e configurar a ferramenta de gestão, recomendamos que peça a um utilizador que lance a UI de gestão para se certificar de que tudo funciona. O utilizador que lançar o UI de gestão deve ter uma atribuição de função que lhes permite ver ou editar o inquilino virtual do Windows Desktop.

## <a name="deploy-the-management-tool"></a>Implementar a ferramenta de gestão

Antes de iniciar, certifique-se de que o servidor e as aplicações do cliente têm o consentimento visitando a [Página de Consentimento Virtual do Windows](https://rdweb.wvd.microsoft.com) para o Diretório Ativo Azure (AAD) representado.

Siga estas instruções para implementar o modelo de Gestão de Recursos Azure:

1. Vá à [página de RDS-Templates GitHub Azure](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implemente o modelo no Azure.
    - Se estiver a implementar uma subscrição da Enterprise, desloque-se para baixo e selecione **Implementar para Azure**.
    - Se estiver a implementar uma subscrição do Cloud Solution Provider, siga estas instruções para implementar no Azure:
        1. Desloque-se para baixo e clique no botão direito **Implementar para Azure**e, em seguida, selecione **Copy Link Location**.
        2. Abra um editor de texto como o Bloco de Notas e cole o link lá.
        3. Logo a seguir <https://portal.azure.com/> e antes da hashtag (#), insira um sinal (@) seguido pelo nome de domínio do inquilino. Aqui está um exemplo do formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/> .
        4. Inscreva-se no portal Azure como utilizador com permissões Admin/Contribuinte para a subscrição do Cloud Solution Provider.
        5. Cole o link que copiou para o editor de texto na barra de endereços.
3. Ao introduzir os parâmetros, faça o seguinte:
    - Para o parâmetro **isServicePrincipal,** selecione **falso**.
    - Para as credenciais, insira as suas credenciais AZure AD com autenticação multi-factor desativada. Estas credenciais serão usadas para criar a aplicação AD Azure e os recursos Azure. Para saber mais, consulte o [que precisa para implementar a ferramenta de gestão.](#what-you-need-to-deploy-the-management-tool)
    - Para a **aplicaçãoName,** use um nome único para a sua aplicação que estará registada no seu Azure Ative Diretório. Este nome também será usado para o URL da aplicação web. Por exemplo, pode usar um nome como "Apr3UX".
4. Assim que fornecer os parâmetros, aceite os termos e condições e selecione **Comprar**.

## <a name="provide-consent-for-the-management-tool"></a>Fornecer consentimento para a ferramenta de gestão

Depois de o modelo gitHub Azure Resource Manager estar concluído, encontrará um grupo de recursos contendo dois serviços de aplicações, juntamente com um plano de serviço de aplicações no portal Azure.

Antes de iniciar sedível e utilizar a ferramenta de gestão, tem de fornecer consentimento para a nova aplicação AD Azure associada à ferramenta de gestão. O consentimento permite que a ferramenta de gestão faça chamadas de gestão virtual do Windows desktop em nome do utilizador que se encontra atualmente inscrito na ferramenta.

> [!div class="mx-imgBorder"]
> ![Uma imagem que mostra as permissões fornecidas quando concorda com a ferramenta de gestão da UI.](../media/management-ui-delegated-permissions.png)

Para determinar que utilizador pode utilizar para iniciar singagem na ferramenta, aceda à página de [definições do utilizador do Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e tome nota do valor para **os Utilizadores poderem consentir em aplicações que acedam aos dados da empresa em seu nome**.

> [!div class="mx-imgBorder"]
> ![Uma imagem de imagem que mostra se os utilizadores podem conceder consentimento a aplicações apenas para o seu utilizador.](../media/management-ui-user-consent-allowed.png)

- Se o valor for definido para **Sim,** pode iniciar scontabilidade com qualquer conta de utilizador no Diretório Ativo Azure e fornecer o consentimento apenas para esse utilizador. No entanto, se iniciar susta na ferramenta de gestão com um utilizador diferente mais tarde, deve voltar a efetuar o mesmo consentimento.
- Se o valor for definido como **Nº,** deve inscrever-se como Administrador Global no Diretório Ativo Azure e fornecer consentimento administrativo para todos os utilizadores do diretório. Nenhum outro utilizadores terá de enfrentar um pedido de consentimento.


Assim que decidir qual o utilizador que utilizará para fornecer o seu consentimento, siga estas instruções para fornecer o consentimento à ferramenta:

1. Aceda aos seus recursos Azure, selecione o recurso Azure App Services com o nome que forneceu no modelo (por exemplo, Apr3UX) e navegue para o URL associado a ele; por exemplo,  `https://rdmimgmtweb-210520190304.azurewebsites.net` . .
2. Inscreva-se utilizando a conta de utilizador Azure Ative Directory apropriada.
3. Se autenticar com um Administrador Global, pode agora selecionar a caixa de verificação para **Consent em nome da sua organização.** Selecione **Aceitar** para fornecer consentimento.

   > [!div class="mx-imgBorder"]
   > ![Uma imagem mostrando a página de consentimento completa que o utilizador ou administrador verá.](../media/management-ui-consent-page.png)

Isto irá agora levá-lo à ferramenta de gestão.

## <a name="use-the-management-tool"></a>Utilize a ferramenta de gestão

Depois de fornecer o consentimento para a organização ou para um utilizador especificado, pode aceder à ferramenta de gestão a qualquer momento.

Siga estas instruções para lançar a ferramenta:

1. Selecione o recurso Azure App Services com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue para o URL associado ao mesmo; por exemplo,  `https://rdmimgmtweb-210520190304.azurewebsites.net` . .
2. Inscreva-se usando as suas credenciais de ambiente de trabalho virtual do Windows.
3. Quando solicitado para escolher um Grupo de Inquilinos, selecione **O Grupo de Inquilinos Predefinidos** na lista de suspensos.
4. Quando selecionar **o Grupo inquilino predefinido,** deve aparecer um menu no lado esquerdo da janela. Neste menu, encontre o nome do seu grupo de inquilinos e selecione-o.

  > [!NOTE]
  > Se tiver um grupo de inquilinos personalizados, insira o nome manualmente em vez de escolher a partir da lista de drop-down.

## <a name="report-issues"></a>Comunicar problemas

Se encontrar problemas com a ferramenta de gestão ou outras ferramentas de ambiente de trabalho virtual do Windows, siga as instruções nos [modelos do Gestor de Recursos Azure para serviços de ambiente de trabalho remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) para os reportar no GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar e conectar-se à ferramenta de gestão, pode aprender a usar o Serviço Azure para monitorizar problemas de serviço e avisos de saúde. Para saber mais, consulte o nosso [serviço de alertas de serviço tutorial.](set-up-service-alerts-2019.md)