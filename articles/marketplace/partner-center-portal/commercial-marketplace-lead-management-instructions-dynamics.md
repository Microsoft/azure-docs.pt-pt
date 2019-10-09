---
title: Gerenciamento de leads do Dynamics 365 for Customer Engagement | Azure Marketplace
description: Configure o gerenciamento de leads do Dynamics 365 para o compromisso com o cliente.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 181a3f3a8d3cabb2fdf6caf79cef16201fab0c68
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177813"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Configurar o gerenciamento de Lead para o Dynamics 365 for Customer Engagement

Este artigo descreve como configurar o Dynamics 365 para o envolvimento do cliente (anteriormente Dynamics CRM Online), leia mais sobre a alteração [aqui](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) para processar os leads de vendas de sua oferta do Marketplace. 

>[!Note]
>Essas instruções são específicas para o ambiente de envolvimento do cliente do Microsoft Hosted Cloud Dynamics 365 para clientes. Não há suporte para a conexão direta com um ambiente local do Dynamics no momento, há outras opções para receber clientes potenciais, como configurar um [ponto de extremidade https](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber clientes potenciais.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes permissões de usuário são necessárias para concluir as etapas neste artigo:

* Você precisa ser um administrador em sua instância do Dynamics 365 for Customer Engagement para poder instalar uma solução e seguir estas instruções.
* Você precisa ser um administrador de locatário para criar uma nova conta de serviço para o serviço de Lead usado para enviar clientes potenciais das ofertas do Marketplace.
* Você precisa ter acesso ao portal de administração do Office 365.
* Você precisa ter acesso ao portal do Azure.

## <a name="install-the-solution"></a>Instalar a solução

1.  Baixe a [solução de gravador de Microsoft Marketplace Lead](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente no computador.

2.  Abra o Dynamics 365 para o compromisso com o cliente navegando até a URL da instância do Dynamics (como `https://tenant.crm.dynamics.com`).

3.  Acesse as configurações selecionando o ícone de engrenagem e **as configurações avançadas** na barra de navegação superior.
 
    ![Dynamics – configurações avançadas](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Uma vez na página configurações, acesse o menu configuração na barra de navegação superior e selecione **soluções**.

    >[!Note]
    >Se você não vir as opções na próxima captura de tela, não terá as permissões necessárias para continuar. Entrar em contato com um administrador em sua instância do Dynamics 365 for Customer Engagement.

    ![Dynamics 365 – soluções](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Uma vez na página soluções, selecione **importar** e navegue até o local em que você salvou a solução de *gravador de Microsoft Marketplace Lead* que você baixou na etapa 1.

    ![Dynamics 365 para compromisso com o cliente – importação](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Conclua a importação da solução seguindo o assistente de importação de solução.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar clientes potenciais em seu Dynamics 365 para a instância do engajamento do cliente, você deve compartilhar uma conta de serviço conosco e configurar permissões para a conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o **Azure Active Directory** ou o **Office 365**.

>[!Note]
>Com base na opção de autenticação selecionada, você pode pular para as instruções correspondentes com base em sua escolha. Consulte [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) ou [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos essa opção porque você tem o benefício adicional de nunca precisar atualizar seu nome de usuário/senha para continuar a obter clientes potenciais. Para usar a opção Azure Active Directory, forneça a ID do aplicativo, a chave do aplicativo e a ID do diretório do seu aplicativo Active Directory.

Use as etapas a seguir para configurar o Azure Active Directory do Dynamics 365 para o compromisso com o cliente.

1. Entre no [portal do Azure](https://portal.azure.com/)e, em seguida, selecione o serviço Azure Active Directory na navegação à esquerda.

2. Selecione **Propriedades** na barra de navegação Azure Active Directory à esquerda e copie o valor da **ID de diretório** nessa página. Salve esse valor, pois é o valor da *ID de diretório* que você precisa fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

    ![Azure Active Directory-Propriedades](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selecione **registros de aplicativo** na barra de navegação do Azure Active Directory esquerdo e, em seguida, selecione **novo registro** nessa página.
4. Insira um nome para o nome do aplicativo. Forneça um nome de aplicativo significativo.
5. Em tipos de conta com suporte, selecione **contas em qualquer diretório organizacional**.
6. Em URI de redirecionamento, selecione **Web** e forneça um URI (como `https://contosoapp1/auth`). 
7. Selecione **Registar**.

    ![Registar uma aplicação](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Agora que seu aplicativo está registrado, acesse a página de visão geral do aplicativo e copie o valor da **ID do aplicativo (cliente)** nessa página. Salve esse valor, pois ele é o valor de *ID do aplicativo (cliente)* que você precisa fornecer no portal de publicação e no Dynamics para receber clientes potenciais para sua oferta do Marketplace.

    ![ID do aplicativo (cliente)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Selecione **certificados e segredos** na navegação à esquerda do aplicativo e selecione **novo segredo do cliente** nessa página. Insira uma descrição significativa para o segredo do cliente e selecione a opção **nunca** em expirar. Selecione **Adicionar** para criar o segredo do cliente.

    ![Aplicativos-certificação e segredos](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Assim que o segredo do cliente for criado com êxito, **Copie o valor do segredo do cliente**. Você não poderá recuperar o valor depois de sair da página. Salve esse valor, pois é o valor do *segredo do cliente* que você precisa fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace. 
11. Selecione **permissões de API** na navegação à esquerda dos aplicativos e, em seguida, selecione **Adicionar uma permissão**.
12. Selecione APIs da Microsoft e, em seguida, selecione **Dynamics CRM** como a API.
13. Em *que tipo de permissões seu aplicativo exige*, verifique se **permissões delegadas** está selecionada. Verifique a permissão para acesso ao **user_impersonation** *Common Data Service como usuários da organização*. Selecione **adicionar permissões**.

    ![Adicionar permissões](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Depois de concluir as etapas de 1 a 1-13 no portal do Azure, navegue até o Dynamics 365 para a instância do engajamento do cliente navegando até a URL (como `https://tenant.crm.dynamics.com`).
15. Acesse as configurações selecionando o ícone de engrenagem e **Configurações avançadas** na barra de navegação superior.
16. Uma vez na página configurações, acesse o menu configuração na barra de navegação superior e selecione **segurança**.
17. Na página segurança, selecione **usuários**.  Na página usuários, selecione a lista suspensa "usuários habilitados" para alternar para **os usuários do aplicativo**.
18. Selecione **novo** para criar um novo usuário. 

    ![Criar um novo utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Em **novo usuário**, verifique se o usuário: O usuário do aplicativo está selecionado. Forneça um nome de usuário, um nomes completo e um endereço de email para aquele que você deseja usar com essa conexão. Além disso, Cole a **ID do aplicativo** para o aplicativo que você criou no portal do Azure da etapa 8. Selecione **salvar e fechar** para concluir a adição do usuário.

    ![Novo usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Vá para "configurações de segurança" neste artigo para concluir a configuração da conexão para esse usuário.

### <a name="office-365"></a>Office 365

Se você não quiser usar Azure Active Directory, poderá registrar um novo usuário no *centro de administração do Microsoft 365*. Você será solicitado a atualizar seu nome de usuário/senha a cada 90 dias para continuar a obter clientes potenciais.

Use as etapas a seguir para configurar o Office 365 para Dynamics 365 para o envolvimento do cliente.

1. Entre no centro de [Administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione **Adicionar um usuário**.

    ![Centro de administração do Microsoft 365 – adicionar um usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Crie um novo usuário para o serviço de gravador de cliente potencial. Defina as seguintes configurações:

    * Fornecer um nome de usuário
    * Forneça uma senha e desmarque a opção "tornar este usuário alterar sua senha ao entrar pela primeira vez".
    * Selecione "usuário (sem acesso de administrador)" como a função para o usuário.
    * Selecione "plano do engajamento do cliente do Dynamics 365" como licença de produto mostrada na próxima captura de tela. Você será cobrado pela licença que escolher. 

Salve esses valores, pois eles são os valores de *nome de usuário e senha* que você precisa fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

![Centro de administração do Microsoft 365 – novo usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Definições de segurança

A etapa final é habilitar o usuário que você criou para escrever os leads.

1. Abra o Dynamics 365 para o compromisso com o cliente navegando até a URL da instância do Dynamics (como `https://tenant.crm.dynamics.com`).
2. Acesse as configurações selecionando o ícone de engrenagem e **as configurações avançadas** na barra de navegação superior.
3. Uma vez na página configurações, acesse o menu configuração na barra de navegação superior e selecione **segurança**.
4. Na página segurança, selecione **usuários** e selecione o usuário que você criou na seção configurar permissões de usuário deste documento e, em seguida, selecione **gerenciar funções**. 

    ![Gerenciar funções](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Procure o nome da função "Microsoft Marketplace gravador de Lead" e selecione-o para atribuir ao usuário a função.

    ![Gerir funções de utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Essa função é criada pela solução que você importou e tem permissões para gravar os leads e para acompanhar a versão da solução para garantir a compatibilidade.

6. Navegue de volta para a página de segurança e selecione **funções de segurança**. Pesquise a função "Microsoft Marketplace o gravador de Lead" e selecione-a.

    ![Funções de segurança](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Uma vez na função de segurança, selecione a guia **registros principais** . Pesquise a entidade "configurações de interface do usuário da entidade de usuários" e habilite as permissões criar, ler e gravar para o usuário (1/4 círculo amarelo) para essa entidade clicando uma vez em cada um dos círculos correspondentes.

    ![Microsoft Marketplace gravador de Lead-registros principais](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Agora, navegue até a guia **personalização** . Pesquise Tor a entidade "trabalho do sistema" e habilite as permissões de leitura, gravação e acréscimo para a organização (verde estável) para essa entidade clicando quatro vezes em cada um dos círculos correspondentes.

    ![Gravador de Lead Microsoft Marketplace-personalização](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Salve e feche**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configure sua oferta para enviar clientes potenciais ao Dynamics 365 para o compromisso com o cliente

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página de **instalação da oferta** para sua oferta.
2. Selecione **conectar** na seção Gerenciamento de leads.

    ![Conectar-se ao gerenciamento de leads](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Na janela pop-up detalhes da conexão, selecione **Dynamics 365 para compromisso** com o cliente para o destino do cliente potencial

    ![Detalhes da conexão-destino do cliente potencial](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Forneça a **URL da instância do Dynamics 365** , como `https://contoso.crm4.dynamics.com`.
5. Selecione o método de **autenticação**, Azure Active Directory ou Office 365. 
6. Se você selecionou Azure Active Directory, forneça a **ID do aplicativo (cliente)** (exemplo: `23456052-aaaa-bbbb-8662-1234df56788f`), a **ID do diretório** (exemplo: `12345678-8af1-4asf-1234-12234d01db47`) e o **segredo do cliente** (exemplo: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Detalhes da conexão-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Se você selecionou Office 365, forneça o **nome de usuário** (exemplo: `contoso@contoso.onmicrosoft.com`) e a senha (exemplo: `P@ssw0rd`).

    ![Detalhes da conexão-nome de usuário](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.
