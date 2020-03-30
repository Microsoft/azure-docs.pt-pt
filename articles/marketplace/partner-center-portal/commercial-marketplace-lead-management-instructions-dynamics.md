---
title: Gestão de chumbo para a Dynamics 365 para envolvimento com o cliente Mercado Azure
description: Configure a gestão de chumbo para a Dynamics 365 para envolvimento com o cliente.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: 8af6b3a451d20bcc9cab3fa4adb9643f82b85e49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288823"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Configure a gestão de chumbo para a Dynamics 365 para envolvimento com o cliente

Este artigo descreve como configurar a Dynamics 365 para o Envolvimento com o Cliente (anteriormente Dynamics CRM Online), ler mais sobre a mudança [aqui](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) para processar os leads de vendas da sua oferta de mercado. 

>[!Note]
>Estas instruções são específicas para a Microsoft hospedada na nuvem Dynamics 365 para ambiente de envolvimento com o cliente. A ligação direta a um ambiente Dynamics on-prem não é suportada atualmente, existem outras opções para receber pistas como configurar um [ponto final https](./commercial-marketplace-lead-management-instructions-https.md) ou uma tabela [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber leads.

## <a name="prerequisites"></a>Pré-requisitos

São necessárias as seguintes permissões de utilizador para completar os passos deste artigo:

* É necessário ser administrador na sua Dinâmica 365 para que o envolvimento do cliente possa instalar uma solução e seguir estas instruções.
* Você precisa ser um administrador inquilino para criar uma nova conta de serviço para o serviço de chumbo usado para enviar pistas de ofertas de mercado.
* Precisa de ter acesso ao portal de administração do Office 365.
* Precisa ter acesso ao portal Azure.

## <a name="install-the-solution"></a>Instalar a solução

1.  Descarregue a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e guarde-a localmente para o seu computador.

2.  Open Dynamics 365 para envolvimento com o cliente navegando `https://tenant.crm.dynamics.com`para o URL para a sua instância De Dinâmica (como).

3.  Ajustes de acesso selecionando o ícone da engrenagem e **definições avançadas** na barra de navegação superior.
 
    ![Dinâmica - Definições Avançadas](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Uma vez na página Definições, aceda ao menu Definição a partir da barra de navegação superior e selecione **Soluções**.

    >[!Note]
    >Se não vir as opções na próxima captura do ecrã, então não tem as permissões necessárias. Contacte um administrador na sua Dinâmica 365 para a instância de Envolvimento com o Cliente.

    ![Dinâmica 365 - Soluções](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Uma vez na página Solutions, selecione **Import** e navegue até onde guardou a solução *Microsoft Marketplace Lead Writer* que descarregou no passo 1.

    ![Dinâmica 365 para Envolvimento com Clientes - Importação](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Importar a solução completa seguindo o assistente de solução import.

## <a name="configure-user-permissions"></a>Configurar permissões de utilizador

Para escrever pistas na sua Dinâmica 365 para a instância de Envolvimento do Cliente, você deve partilhar uma conta de serviço connosco e configurar permissões para a conta.

Utilize os seguintes passos para criar a conta de serviço e atribuir permissões. Pode utilizar **o Diretório Ativo Azure** ou o **Office 365**.

>[!Note]
>Com base na opção de autenticação selecionada, pode saltar para as instruções correspondentes com base na sua escolha. Consulte [o Diretório Ativo Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) ou [o Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos esta opção porque obtém o benefício adicional de nunca precisar de atualizar o seu nome de utilizador/palavra-passe para continuar a obter leads. Para utilizar a opção Diretório Ativo Azure, fornece o ID da aplicação, chave de aplicação e ID do Diretório da sua aplicação Ative Directory.

Utilize os seguintes passos para configurar o Diretório Ativo Azure para a Dinâmica 365 para o Envolvimento do Cliente.

1. Inscreva-se no [portal Azure](https://portal.azure.com/)e, em seguida, selecione o serviço de Diretório Ativo Azure a partir da navegação à esquerda.

2. Selecione **Propriedades** do Diretório Ativo Azure deixou a navegação e copie o valor de ID do **Diretório** nessa página. Poupe este valor, pois é o valor de ID do *Diretório* que precisa fornecer no portal editorial para receber pistas para a sua oferta de mercado.

    ![Diretório Ativo Azure - Propriedades](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selecione registos de **Aplicativos** do Diretório Ativo Azure e, em seguida, selecione **Novo registo** nessa página.
4. Insira um nome para o nome da candidatura. Forneça um nome significativo de candidatura.
5. Nos tipos de conta suportada, selecione **Contas em qualquer diretório organizacional**.
6. Em Redirecione o URI, selecione **Web** e forneça um URI (como). `https://contosoapp1/auth` 
7. Selecione **Registar**.

    ![Registar uma aplicação](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Agora que a sua aplicação está registada, aceda à página de visão geral da aplicação e copie o valor de ID da **Aplicação (cliente)** nessa página. Poupe este valor, uma vez que é o valor de ID da *Aplicação (cliente)* que precisa fornecer no portal editorial e na Dynamics para receber pistas para a sua oferta de marketplace.

    ![ID da Aplicação (cliente)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Selecione **Certificados e segredos** da navegação esquerda da aplicação e selecione **novo segredo** de cliente nessa página. Introduza uma descrição significativa para o segredo do cliente e selecione a opção **Nunca** em Expira. Selecione **Adicionar** para criar o segredo do cliente.

    ![Aplicação - Certificação e Segredos](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Assim que o segredo do cliente for criado com sucesso, **copie o valor secreto do cliente.** Não conseguirá recuperar o valor depois de navegar para longe da página. Poupe este valor, pois é o valor secreto do *Cliente* que precisa fornecer no portal editorial para receber pistas para a sua oferta de mercado. 
11. Selecione **permissões API** da navegação esquerda das aplicações e, em seguida, selecione **Adicionar uma permissão**.
12. Selecione APIs da Microsoft e, em seguida, selecione **Dynamics CRM** como API.
13. De acordo com *o tipo de permissões que a sua aplicação requer,* certifique-se de que as **permissões delegadas são selecionadas.** Verifique a permissão para **user_impersonation** *Access Common Data Service como utilizadores da organização.* **Selecione Adicionar permissões**.

    ![Adicionar permissões](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Depois de completar os passos 1-13 no portal Azure, navegue até à sua Dinâmica `https://tenant.crm.dynamics.com`365 para a instância de envolvimento do cliente navegando para o URL (como).
15. Ajustes de acesso selecionando o ícone da engrenagem e **Definições Avançadas** na barra de navegação superior.
16. Uma vez na página Definições, aceda ao menu Definição a partir da barra de navegação superior e selecione **Security**.
17. Uma vez na página de Segurança, selecione **Utilizadores**.  Na página Utilizadores, selecione a desistência de "Utilizadores Ativados" para mudar para utilizadores de **aplicações**.
18. Selecione **Novo** para criar um novo utilizador. 

    ![Criar um novo utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Em **New User,** certifique-se de que o UTILIZADOR: UTILIZADOR DE APLICAÇÃO é selecionado. Forneça um nome de utilizador, nome completo e endereço de e-mail para o utilizador que pretende utilizar com esta ligação. Além disso, cola no ID de **aplicação** para a app que criou no portal Azure a partir do passo 8. Selecione **Guardar e Fechar** para completar a adição do utilizador.

    ![Novo utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Vá a "Definições de Segurança" neste artigo para terminar de configurar a ligação para este utilizador.

### <a name="office-365"></a>Office 365

Se não quiser utilizar o Azure Ative Directory, pode registar um novo utilizador no centro de administração da *Microsoft 365*. Será obrigado a atualizar o seu nome de utilizador/senha a cada 90 dias para continuar a obter pistas.

Utilize os seguintes passos para configurar o Office 365 para a Dynamics 365 para o Envolvimento do Cliente.

1. Inicie sessão no [centro de administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione **Adicionar um utilizador**.

    ![Microsoft 365 centro de administração - adicione um utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Crie um novo utilizador para o serviço de escritores principais. Configure as seguintes definições:

    * Fornecer um nome de utilizador
    * Forneça uma palavra-passe e desmarque a opção "Faça com que este utilizador altere a sua palavra-passe quando iniciar o seu início de sessão".
    * Selecione "Utilizador (sem acesso a administrador)" como função para o utilizador.
    * Selecione "Dynamics 365 Customer Engagement plan" como licença de produto mostrada na captura do próximo ecrã. Será cobrado pela licença que escolher. 

Guarde estes valores uma vez que são os valores de *Username e Password* que precisa fornecer no portal de publicação para receber pistas para a sua oferta de mercado.

![Microsoft 365 centro de administração - novo utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Definições de segurança

O passo final é permitir ao Utilizador que criou escrever as pistas.

1. Open Dynamics 365 para envolvimento com o cliente navegando `https://tenant.crm.dynamics.com`para o URL para a sua instância De Dinâmica (como).
2. Ajustes de acesso selecionando o ícone da engrenagem e **definições avançadas** na barra de navegação superior.
3. Uma vez na página Definições, aceda ao menu Definição a partir da barra de navegação superior e selecione **Security**.
4. Uma vez na página 'Segurança', selecione **Utilizadores** e selecione o utilizador que criou na secção de permissões do Utilizador Configurar deste documento e, em seguida, selecione **'Gerir funções'.** 

    ![Gerir funções](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Procure o nome de papel "Microsoft Marketplace Lead Writer" e selecione-o para atribuir ao utilizador o papel.

    ![Gerir funções de utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Este papel é criado pela solução que importaste e só tem permissões para escrever os leads e para rastrear a versão da solução para garantir a compatibilidade.

6. Navegue de volta para a página de Segurança e selecione **Funções**de Segurança . Procure o papel "Microsoft Marketplace Lead Writer" e selecione-o.

    ![Funções de segurança](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Uma vez na função de segurança, selecione o separador **Core Records.** Procure a entidade "Definições ui da entidade utilizadora" e ative as permissões De Criar, Ler e Escrever ao Utilizador (1/4 círculo amarelo) para essa entidade clicando uma vez em cada um dos círculos correspondentes.

    ![Microsoft Marketplace Lead Writer - Core Records](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Agora navegue para o separador **de personalização.** Pesquisar tor a entidade "System Job" e permitir as permissões de Leitura, Escrita e Apêndice para a Organização (verde sólido) para essa entidade clicando quatro vezes em cada um dos círculos correspondentes.

    ![Microsoft Marketplace Lead Writer - personalização](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Poupe e feche.**

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configure a sua oferta para enviar pistas para a Dynamics 365 Para Envolvimento com o Cliente

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga os passos abaixo:

1. Navegue na página de **configuração** da Oferta para a sua oferta.
2. Selecione **Ligar** sob a secção de Gestão de Chumbo.

    ![Ligar à Gestão de Chumbo](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Na janela pop-up de detalhes da Ligação, selecione **Dynamics 365 para o envolvimento do cliente** para o destino principal

    ![Detalhes da ligação - destino de chumbo](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Fornecer o URL de instância `https://contoso.crm4.dynamics.com`Dynamics **365,** tais como .
5. Selecione o método de **Autenticação,** Diretório Ativo Azure ou Office 365. 
6. Se selecionou o Azure Ative Directory, forneça o `23456052-aaaa-bbbb-8662-1234df56788f` **ID (cliente) da Aplicação (exemplo:** ), ID do **Diretório** (exemplo: `12345678-8af1-4asf-1234-12234d01db47`) e **segredo do Cliente** (exemplo: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Detalhes da ligação - Diretório Ativo Azure](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Se selecionou o Office 365, forneça `contoso@contoso.onmicrosoft.com`o nome de `P@ssw0rd` **utilizador** (exemplo: ) e palavra-passe (exemplo: ).

    ![Detalhes da ligação - Nome do utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.
