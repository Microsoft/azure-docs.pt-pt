---
title: Implantar ferramenta de gerenciamento – Azure
description: Como instalar uma ferramenta de interface do usuário para gerenciar recursos da área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: helohr
ms.openlocfilehash: f07403d8d0b2c6d0dd7a6b851a87b47b0c32501a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679492"
---
# <a name="tutorial-deploy-a-management-tool"></a>Tutorial: Implementar uma ferramenta de gestão

A ferramenta de gerenciamento fornece uma interface do usuário para gerenciar recursos da área de trabalho virtual da Microsoft. Neste tutorial, você aprenderá a implantar e conectar-se à ferramenta de gerenciamento do.

>[!NOTE]
>Essas instruções são para uma configuração específica da área de trabalho virtual do Windows que pode ser usada com os processos existentes da sua organização.

## <a name="important-considerations"></a>Considerações importantes

Como o aplicativo requer consentimento para interagir com a área de trabalho virtual do Windows, essa ferramenta não dá suporte a cenários B2B (entre empresas). Cada assinatura de locatário do Azure Active Directory (AAD) precisará de sua própria implantação separada da ferramenta de gerenciamento.

Essa ferramenta de gerenciamento é um exemplo. A Microsoft fornecerá atualizações importantes de segurança e qualidade. O [código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Os clientes e parceiros são incentivados a personalizar a ferramenta para atender às suas necessidades de negócios.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que você precisa para executar o modelo de Azure Resource Manager

Antes de implantar o modelo de Azure Resource Manager, você precisará de um usuário Azure Active Directory para implantar a interface do usuário de gerenciamento. Esse usuário deve:

- Ter a autenticação multifator do Azure (MFA) desabilitada
- Ter permissão para criar recursos em sua assinatura do Azure
- Ter permissão para criar um aplicativo do Azure AD. Siga estas etapas para verificar se o usuário tem as [permissões necessárias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Depois de implantar o modelo de Azure Resource Manager, você desejará iniciar a interface do usuário de gerenciamento para validar. Esse usuário deve:
- Ter uma atribuição de função para exibir ou editar seu locatário de área de trabalho virtual do Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Executar o modelo de Azure Resource Manager para provisionar a interface do usuário de gerenciamento

Antes de começar, verifique se os aplicativos cliente e servidor têm consentimento visitando a [página de consentimento da área de trabalho virtual do Windows](https://rdweb.wvd.microsoft.com) para o Azure Active Directory (AAD) representado.

Siga estas instruções para implantar o modelo de gerenciamento de recursos do Azure:

1. Vá para a [página do Azure RDS-templates do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implante o modelo no Azure.
    - Se você estiver implantando em uma assinatura do Enterprise, role para baixo e selecione **implantar no Azure**. Consulte as [diretrizes para parâmetros de modelo](#guidance-for-template-parameters).
    - Se você estiver implantando em uma assinatura do provedor de soluções na nuvem, siga estas instruções para implantar no Azure:
        1. Role para baixo e clique com o botão direito do mouse em **implantar no Azure**e selecione **Copiar local do link**.
        2. Abra um editor de texto como o bloco de notas e cole o link lá.
        3. Logo após <https://portal.azure.com/> e antes da hashtag (#), insira um sinal de arroba (@) seguido pelo nome de domínio do locatário. Aqui está um exemplo do formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Entre no portal do Azure como um usuário com permissões de administrador/colaborador para a assinatura do provedor de soluções na nuvem.
        5. Cole o link que você copiou para o editor de texto na barra de endereços.

### <a name="guidance-for-template-parameters"></a>Diretrizes para parâmetros de modelo
Aqui está como inserir parâmetros para configurar a ferramenta:

- Esta é a URL do agente RD: https: \//rdbroker. wvd. Microsoft. com/
- Esta é a URL do recurso: https: \//Mrs-prod. ame. GBL/Sra-RDInfra-prod
- Use suas credenciais do AAD com o MFA desabilitado para entrar no Azure. Veja o [que você precisa para executar o modelo de Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Use um nome exclusivo para o aplicativo que será registrado em seu Azure Active Directory para a ferramenta de gerenciamento; por exemplo, Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Fornecer consentimento para a ferramenta de gerenciamento

Depois que o modelo de Azure Resource Manager do GitHub for concluído, você encontrará um grupo de recursos que contém dois serviços de aplicativo junto com um plano do serviço de aplicativo na portal do Azure.

Antes de entrar e usar a ferramenta de gerenciamento, você precisará fornecer consentimento para o novo aplicativo Azure Active Directory associado à ferramenta de gerenciamento. Ao fornecer consentimento, você permite que a ferramenta de gerenciamento faça chamadas de gerenciamento de área de trabalho virtual do Windows em nome do usuário que está conectado à ferramenta.

![Uma captura de tela mostrando as permissões fornecidas quando você concorda com a ferramenta de gerenciamento de interface do usuário.](media/management-ui-delegated-permissions.png)

Para determinar qual usuário você pode usar para entrar na ferramenta, acesse a [página Azure Active Directory configurações do usuário](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e anote o valor para **que os usuários possam dar consentimento aos aplicativos que acessam os dados da empresa em seu nome**.

![Uma captura de tela mostrando se os usuários podem conceder consentimento aos aplicativos apenas para o usuário.](media/management-ui-user-consent-allowed.png)

- Se o valor for definido como **Sim**, você poderá entrar com qualquer conta de usuário no Azure Active Directory e fornecer consentimento somente para esse usuário. No entanto, se você entrar na ferramenta de gerenciamento com um usuário diferente mais tarde, deverá executar o mesmo consentimento novamente.
- Se o valor for definido como **não**, você deverá entrar como um administrador Global na Azure Active Directory e fornecer consentimento de administrador para todos os usuários no diretório. Nenhum outro usuário enfrentará um prompt de consentimento.


Depois de decidir qual usuário será usado para fornecer consentimento, siga estas instruções para fornecer consentimento para a ferramenta:

1. Vá para os recursos do Azure, selecione o recurso serviços de Azure App com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue até a URL associada a ele; por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Entre usando a conta de usuário do Azure Active Directory apropriada.
3. Se você tiver autenticado com um administrador global, agora poderá selecionar a caixa de seleção para **consentir em nome da sua organização**. Selecione **aceitar** para fornecer consentimento.
   
   ![Uma captura de tela mostrando a página de consentimento completo que o usuário ou o administrador verá.](media/management-ui-consent-page.png)

Agora, isso levará você à ferramenta de gerenciamento.

## <a name="use-the-management-tool"></a>Usar a ferramenta de gerenciamento

Depois de fornecer consentimento para a organização ou para um usuário especificado, você pode acessar a ferramenta de gerenciamento a qualquer momento.

Siga estas instruções para iniciar a ferramenta:

1. Selecione o recurso serviços de Azure App com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue até a URL associada a ele; por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Entre usando suas credenciais de área de trabalho virtual do Windows.
3. Quando for solicitado a escolher um grupo de locatários, selecione **grupo de locatários padrão** na lista suspensa.

> [!NOTE]
> Se você tiver um grupo de locatários personalizado, insira o nome manualmente em vez de escolher na lista suspensa.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como implantar e se conectar à ferramenta de gerenciamento, você pode aprender a usar a integridade de serviço do Azure para monitorar problemas de serviço e comunicados de integridade.

> [!div class="nextstepaction"]
> [Tutorial de configuração de alertas de serviço](./set-up-service-alerts.md)
