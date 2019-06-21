---
title: Implementar a ferramenta de gestão - Azure
description: Como instalar uma ferramenta de interface de utilizador para gerir os recursos de pré-visualização da área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 275fec5fb696a7e1352bbddccd288863e984b796
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304506"
---
# <a name="tutorial-deploy-a-management-tool"></a>Tutorial: Implementar uma ferramenta de gestão

A ferramenta de gestão fornece uma interface de utilizador (IU) para gerir recursos do Microsoft Virtual Desktop Preview. Neste tutorial, irá aprender como implantar e ligue-se à ferramenta de gestão.

>[!NOTE]
>Estas instruções destinam-se uma configuração específica de pré-visualização de área de Trabalho Virtual do Windows que pode ser utilizada com os processos existentes da sua organização.

## <a name="important-considerations"></a>Considerações importantes

Uma vez que a aplicação necessita de consentimento para interagir com a área de Trabalho Virtual do Windows, esta ferramenta não suporta cenários de empresa-empresa (B2B). Subscrição do cada inquilino Azure Active Directory (AAD) terá sua própria implantação à parte da ferramenta de gestão.

Essa ferramenta de gestão é um exemplo. Microsoft irá fornecer atualizações de qualidade e de segurança importante. O [código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Os clientes e parceiros são encorajados a personalizar a ferramenta de acordo com as suas necessidades de negócios.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que precisa para executar o modelo Azure Resource Manager

Antes de implementar o modelo Azure Resource Manager, terá de um utilizador do Azure Active Directory para implementar o gerenciamento da interface do Usuário. Este utilizador tem de:

- Tenha desativado os Azure multi-factor Authentication (MFA)
- Tem permissão para criar recursos na sua subscrição do Azure
- Tem permissão para criar uma aplicação do Azure AD. Siga estes passos para verificar se o seu utilizador tem o [permissões obrigatórias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Depois de implementar o modelo Azure Resource Manager, desejará iniciar a gestão da interface do Usuário para validar. Este utilizador tem de:
- Ter uma atribuição de função para ver ou editar o seu inquilino de área de Trabalho Virtual do Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Executar o modelo Azure Resource Manager para aprovisionar o gerenciamento da interface do Usuário

Antes de começar, certifique-se as aplicações de servidor e cliente têm consentimento, visitando a [Windows Virtual Desktop consentimento página](https://rdweb.wvd.microsoft.com) para o Azure Active Directory (AAD) representado.

Siga estas instruções para implementar o modelo de gestão de recursos do Azure:

1. Vá para o [página GitHub do Azure RDS-modelos](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implemente o modelo para o Azure.
    - Se estiver a implementar numa subscrição Enterprise, desloque para baixo e selecione **implementar no Azure**. Ver [documentação de orientação para os parâmetros de modelo](#guidance-for-template-parameters).
    - Se estiver a implementar uma subscrição do fornecedor de soluções Cloud, siga estas instruções para implementar no Azure:
        1. Desloque para baixo e clique com botão direito **implementar no Azure**, em seguida, selecione **localização da ligação de cópia**.
        2. Abra um editor de texto como o bloco de notas e cole a ligação existe.
        3. Logo após <https://portal.azure.com/> e antes da hashtag (#), introduza um arroba (@) seguido do nome de domínio do inquilino. Eis um exemplo do formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Inicie sessão no portal do Azure como um utilizador com permissões de administrador/contribuinte à subscrição do fornecedor de soluções Cloud.
        5. Cole a ligação que copiou para o editor de texto na barra de endereço.

### <a name="guidance-for-template-parameters"></a>Documentação de orientação para os parâmetros de modelo
Eis como introduzir parâmetros para configurar a ferramenta:

- Este é o URL de Mediador de área de trabalho remota:  <https://rdbroker.wvd.microsoft.com/>
- Este é o URL de recurso:  <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- Utilize as suas credenciais do AAD com a MFA desativada para iniciar sessão no Azure. Ver [o que precisa para executar o modelo Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Utilize um nome exclusivo para a aplicação que será registado no Azure Active Directory para a ferramenta de gestão; Por exemplo, Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Forneça o consentimento para a ferramenta de gestão

Depois do GitHub do Azure Resource Manager modelo for concluída, encontrará um grupo de recursos que contém dois serviços de aplicação, juntamente com um plano do serviço de aplicações no portal do Azure.

Antes de iniciar sessão e utilizar a ferramenta de gestão, terá de fornecer o consentimento para a nova aplicação do Azure Active Directory que estão associada com a ferramenta de gestão. Ao fornecer consentimento, está a permitir que a ferramenta de gerenciamento fazer chamadas de gerenciamento de área de Trabalho Virtual do Windows em nome do utilizador com sessão iniciada para a ferramenta.

![Uma captura de ecrã que mostra as permissões que está a ser fornecidas quando o utilizador está a autorizar a ferramenta de gerenciamento da interface do Usuário.](media/management-ui-delegated-permissions.png)

Para determinar quais o usuário pode utilizar para iniciar sessão para a ferramenta, aceda ao seu [página de definições de utilizador do Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e tome nota do valor para a **os utilizadores podem dar consentimento a aplicações acedam aos dados da empresa em nome deles** .

![Um captura de ecrã que mostra se os utilizadores podem conceder consentimento a aplicações para apenas de utilizadores.](media/management-ui-user-consent-allowed.png)

- Se o valor é definido como **Sim**, pode iniciar sessão com qualquer conta de utilizador no Azure Active Directory e fornecem consentimento para apenas esse utilizador. No entanto, se iniciar sessão para a ferramenta de gestão com um utilizador diferente mais tarde, tem de efetuar novamente o consentimento do mesmo.
- Se o valor é definido como **não**, tem de iniciar sessão como um Administrador Global no Azure Active Directory e fornecer o consentimento de administrador para todos os utilizadores no diretório. Nenhum outro usuário será enfrentam um pedido de consentimento.


Assim que decidir o que irá utilizar para fornecer o consentimento de utilizador, siga estas instruções para fornecer consentimento para a ferramenta:

1. Ir para os seus recursos do Azure, selecione o recurso de serviços aplicacionais do Azure com o nome que indicou no modelo (por exemplo, Apr3UX) e navegue para o URL associado-lo. Por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Inicie sessão com a conta de utilizador do Azure Active Directory adequada.
3. Se autenticar com um Administrador Global, agora, pode selecionar a caixa de verificação **consentir em nome da sua organização**. Selecione **Accept** para fornecer consentimento.
   
   ![Uma captura de ecrã que mostra a página de consentimento completa que o utilizador ou administrador de Verão.](media/management-ui-consent-page.png)

Isso levará agora para a ferramenta de gestão.

## <a name="use-the-management-tool"></a>Utilize a ferramenta de gestão

Depois de fornecer consentimento para a organização ou para um utilizador especificado, pode acessar a ferramenta de gestão em qualquer altura.

Siga estas instruções para iniciar a ferramenta:

1. Selecione o recurso de serviços aplicacionais do Azure com o nome que indicou no modelo (por exemplo, Apr3UX) e navegue para o URL associado-lo. Por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Inicie sessão com as credenciais da sua área de Trabalho Virtual do Windows.
3. Quando lhe for pedido para escolher um grupo de inquilino, selecione **grupo de inquilino predefinido** na lista pendente.

> [!NOTE]
> Se tiver um grupo personalizado de inquilino, introduza o nome manualmente em vez de selecionar a lista pendente.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como implementar e ligar-se à ferramenta de gestão, pode aprender a utilizar o Azure Service Health para monitorizar problemas de serviço e aconselhamentos sobre o estado de funcionamento.

> [!div class="nextstepaction"]
> [Configurar o tutorial de alertas do serviço](./set-up-service-alerts.md)
