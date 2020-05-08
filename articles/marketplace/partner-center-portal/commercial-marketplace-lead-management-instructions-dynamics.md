---
title: Gestão de chumbo para O Envolvimento com clientes da Dynamics 365 - Mercado comercial da Microsoft
description: Saiba como configurar o Envolvimento do Cliente da Dynamics 365 para gerir os leads do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 985b3258eb0b957242d529945f32ed9704a91e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791005"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Configure a gestão de chumbo para a Dynamics 365 Customer Engagement

Este artigo descreve como configurar o Envolvimento do Cliente da Dynamics 365 (anteriormente denominado Dynamics CRM Online). Leia mais sobre a alteração na autenticação baseada em [servidores Configure com envolvimento](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) com o cliente e SharePoint Online para processar os leads de vendas da sua oferta de mercado comercial.

>[!NOTE]
>Estas instruções são específicas para o ambiente de nuvem hospedado pela Microsoft para o Envolvimento do Cliente Dynamics 365. A ligação diretamente a um ambiente de dinâmica no local não é suportada atualmente. Existem outras opções para receber pistas, como configurar um [ponto final HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma tabela [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Pré-requisitos

São necessárias as seguintes permissões de utilizador para completar os passos deste artigo:

* Direitos de administrador na sua instância de Envolvimento com o Cliente Dynamics 365 para poder instalar uma solução.
* O administrador dos inquilinos tem direito a criar uma nova conta de serviço para o serviço de chumbo utilizado para enviar pistas de ofertas de mercado comercial.
* Acesso ao portal de administração do Office 365.
* Acesso ao portal Azure.

## <a name="install-the-solution"></a>Instalar a solução

1. Descarregue a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)e guarde-a localmente para o seu computador.

1. Open Dynamics 365 Customer Engagement indo ao URL `https://tenant.crm.dynamics.com`para a sua instância De Dinâmica, como .

1. Selecione o ícone de engrenagem na barra superior e, em seguida, **selecione Definições Avançadas**.
 
    ![Ponto de menu De Definições Avançadas Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Na página **Definições,** abra o menu **Definições** na barra superior e selecione **Soluções**.

    >[!NOTE]
    >Se não vir as opções no ecrã seguinte, não tem as permissões necessárias. Contacte um administrador na sua instância de Envolvimento com o Cliente Dynamics 365.

    ![Opção Dynamics 365 Solutions](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Na página **Solutions,** selecione **Import** e vá até onde guardou a solução **Microsoft Marketplace Lead Writer** que descarregou no passo 1.

    ![Botão de importação](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Importar a solução completa seguindo o assistente de solução import.

## <a name="configure-user-permissions"></a>Configurar permissões de utilizador

Para escrever pistas na sua instância de Envolvimento com o Cliente Dynamics 365, deve partilhar uma conta de serviço com a Microsoft e configurar permissões para a conta.

Utilize os seguintes passos para criar a conta de serviço e atribuir permissões. Pode utilizar o Diretório Ativo Azure ou o Office 365.

>[!NOTE]
>Salte para as instruções correspondentes com base na opção de autenticação selecionada. Consulte [o Diretório Ativo Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) ou [o Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos esta opção porque nunca necessita de atualizar o seu nome de utilizador ou palavra-passe para continuar a obter leads. Para utilizar a opção Diretório Ativo Azure, fornece o ID da aplicação, chave de aplicação e ID do Diretório da sua aplicação Ative Directory.

Para configurar o Diretório Ativo azure para a Dinâmica 365 Envolvimento com o Cliente:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**.

1. Selecione **Propriedades**, e copie o valor de ID do **Diretório** na página de propriedades do **Diretório.** Guarde este valor porque terá de o fornecer no portal editorial para receber pistas para a sua oferta de marketplace.

    ![Item do menu Azure Ative Directory Properties](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selecione **as inscrições** da App do painel esquerdo do Azure Ative Directory e, em seguida, selecione **Nova inscrição** nessa página.
1. Introduza um nome significativo para o nome da aplicação.
1. Nos tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional**.
1. Em **Redirecione URI (opcional)**, selecione **Web** e introduza um URI, como `https://contosoapp1/auth`. 
1. Selecione **Registar**.

    ![Registar uma página de candidatura](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Agora que a sua candidatura está registada, aceda à página geral da aplicação. Copie o valor de ID da **Aplicação (cliente)** nessa página. Guarde este valor porque terá de o fornecer no portal editorial e na Dynamics 365 para receber pistas para a sua oferta de marketplace.

    ![Caixa de identificação de aplicação (cliente)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selecione **Certificados & segredos** do painel esquerdo da aplicação e selecione o botão secreto do **novo cliente.** Introduza uma descrição significativa para o segredo do cliente e selecione a opção **Nunca** em **Expira .** Selecione **Adicionar** para criar o segredo do cliente.

    ![Certificados & itens de menu de segredos](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Assim que o segredo do cliente for criado com sucesso, copie o valor secreto do **Cliente.** Não conseguirá recuperar o valor depois de deixar a página. Guarde este valor porque terá de o fornecer no portal editorial para receber pistas para a sua oferta de marketplace. 
1. Selecione **permissões API** do painel esquerdo da aplicação e, em seguida, selecione **+ Adicione uma permissão**.
1. Selecione **Microsoft APIs**, e, em seguida, selecione **Dynamics CRM** como API.
1. Sob **que tipo de permissões o seu pedido requer?** **Delegated permissions** 
1. Sob **Permissão**, selecione a caixa **de** verificação user_impersonation para Access Common Data Service como utilizadores **da organização**. Em seguida, selecione **Adicionar permissões**.

    ![Adicione o botão de permissões](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Depois de completar os passos 1 a 14 no portal Azure, vá à sua `https://tenant.crm.dynamics.com`instância de Envolvimento com o Cliente Dynamics 365 indo ao URL, como .
1. Selecione o ícone de engrenagem na barra superior e, em seguida, **selecione Definições Avançadas**.
1. Na página **Definições,** abra o menu **Definições** na barra superior e selecione **Segurança**.
1. Na página **de Segurança,** selecione **Utilizadores**. Na página **Utilizadores,** selecione o drop-down dos **Utilizadores Ativados** e, em seguida, selecione Utilizadores de **Aplicação**.
1. Selecione **Novo** para criar um novo utilizador. 

    ![Criar um novo utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. No painel **do Novo Utilizador,** certifique-se de que o **UTILIZADOR: UTILIZADOR** DE APLICAÇÃO é selecionado. Forneça um nome de utilizador, nome completo e endereço de e-mail para o utilizador que pretende utilizar com esta ligação. Além disso, cola no ID de **aplicação** para a app que criou no portal Azure a partir do passo 8. Selecione **Guardar & Perto** de terminar a adição do utilizador.

    ![Novo painel de utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Vá à secção "Definições de Segurança" neste artigo para terminar de configurar a ligação para este utilizador.

### <a name="office-365"></a>Office 365

Se não quiser utilizar o Azure Ative Directory, pode registar um novo utilizador no centro de administração da Microsoft 365. Será obrigado a atualizar o seu nome de utilizador e senha a cada 90 dias para continuar a obter pistas.

Para configurar o Office 365 para a Dynamics 365 Customer Engagement:

1. Inicie sessão no [centro de administração do Microsoft 365](https://admin.microsoft.com).

1. Selecione **Adicionar um utilizador**.

    ![Microsoft 365 centro de administração Adicione uma opção de utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Crie um novo utilizador para o serviço de escritores principais. Configure as seguintes definições:

    * Introduza um nome de utilizador.
    * Introduza uma palavra-passe e limpe o **utilizador Alterar a sua palavra-passe quando iniciar o seu início de sessão.**
    * Selecione **User (sem acesso** ao administrador) como a função para o utilizador.
    * Selecione O Plano de **Envolvimento do Cliente Da Dynamics 365** como licença de produto, como mostra o ecrã seguinte. Será cobrado pela licença que escolher. 

Guarde estes valores porque terá de fornecer os valores do **Username** e **Password** no portal de publicação para receber pistas para a sua oferta de marketplace.

![Microsoft 365 centro de administração Novo painel de utilizadores](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Definições de segurança

O passo final é permitir ao utilizador que criou escrever as pistas.

1. Open Dynamics 365 Customer Engagement indo ao URL `https://tenant.crm.dynamics.com`para a sua instância De Dinâmica, como .
1. Selecione o ícone de engrenagem na barra superior e, em seguida, **selecione Definições Avançadas**.
1. Na página **Definições,** abra o menu **Definições** na barra superior e selecione **Segurança**.
1. Na página **'Segurança',** selecione **Utilizadores** e selecione o utilizador que criou na secção "Configure user permissions" deste documento. Em seguida, selecione **'Gerir funções '**. 

    ![Gerir o separador Roles](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Procure o nome de papel **Microsoft Marketplace Lead Writer**, e selecione-o para atribuir ao utilizador o papel.

    ![Gerir o painel de funções do utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Este papel é criado pela solução que importaste e só tem permissões para escrever os leads e para rastrear a versão da solução para garantir a compatibilidade.

1. Volte para a página de **Segurança** e selecione **Funções**de Segurança . Procure o papel **Microsoft Marketplace Lead Writer**, e selecione-o.

    ![Painel de papéis de segurança](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Na função de segurança, selecione o separador **Core Records.** Procure o item de **Definições ui** da entidade utilizadora. Ative as permissões Criar, Ler e Escrever ao Utilizador (1/4 círculo amarelo) para essa entidade clicando uma vez em cada um dos círculos correspondentes.

    ![Separador Core Records do Microsoft Marketplace Lead Writer](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. No separador **Personalização,** procure o item **System Job.** Ativar as permissões de Leitura, Escrita e Apêndice para a Organização (círculos verdes sólidos) para essa entidade clicando quatro vezes em cada um dos círculos correspondentes.

    ![Separador de personalização de personalização do escritor líder do Microsoft Marketplace](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selecione **Guardar e fechar**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Configure a sua oferta para enviar pistas para o Envolvimento do Cliente da Dynamics 365 

Para configurar as informações de gestão de chumbo para a sua oferta no portal editorial:

1. Vá à página de **configuração** da Oferta para a sua oferta.
1. Selecione **Ligar** sob a secção de gestão de **chumbo.**

    ![Secção de gestão de chumbo Botão de ligação](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

1. Na janela pop-up de detalhes da Ligação, selecione **Dynamics 365 Customer Engagement** para o destino principal.

    ![Caixa de destino de chumbo](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Introduza o **URL** para a instância `https://contoso.crm4.dynamics.com`Dynamics 365, tais como .

1. Selecione o método de **Autenticação,** quer azure Ative Diretório ou Office 365. 
1. Se selecionou **o Diretório Ativo Azure,** introduza o `23456052-aaaa-bbbb-8662-1234df56788f`ID **(cliente) (por** exemplo, ) id do **Diretório** (por exemplo, `12345678-8af1-4asf-1234-12234d01db47`) e o segredo do **Cliente** (por exemplo, `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Autenticação com Diretório Ativo Azure selecionado](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Se selecionou o **Office 365**, introduza o nome de `P@ssw0rd` **utilizador** (por exemplo) `contoso@contoso.onmicrosoft.com`e a **palavra-passe** (por exemplo, ).

    ![Caixa de nome do utilizador do Office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Para **e-mail contacte**, insira endereços de e-mail para pessoas da sua empresa que devam receber notificações de e-mail quando um novo chumbo for recebido. Pode introduzir vários endereços de e-mail separando-os com pontos evíolos.
1. Selecione **OK**.

Para se certificar de que se ligou com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá um teste de chumbo no destino principal.

![Caixa de e-mail de contato](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.
