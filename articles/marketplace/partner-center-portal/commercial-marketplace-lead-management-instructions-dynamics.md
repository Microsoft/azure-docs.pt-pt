---
title: Gestão de chumbo para Dynamics 365 Customer Engagement - Microsoft commercial marketplace
description: Saiba como configurar o Dynamics 365 Customer Engagement para gerir os leads do Microsoft AppSource e do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5d07dda82361ff59a43aa6753669bf38f4463059
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491142"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Configure a gestão de chumbo para a Dynamics 365 Customer Engagement

Este artigo descreve como configurar o Envolvimento do Cliente Dynamics 365 (anteriormente denominado Dynamics CRM Online). Leia mais sobre a alteração da autenticação baseada no [servidor Configure com o Customer Engagement e SharePoint Online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) para processar os leads de vendas da sua oferta de mercado comercial.

>[!NOTE]
>Estas instruções são específicas para o ambiente em nuvem hospedado na Microsoft para o Envolvimento do Cliente Dinâmico 365. A ligação direta a um ambiente dinâmico no local não é suportada atualmente. Existem outras opções para receber leads, tais como configurar um [ponto final HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Pré-requisitos

São necessárias as seguintes permissões do utilizador para completar os passos deste artigo:

* Direitos de administrador na sua instância de Envolvimento ao Cliente Dynamics 365 para poder instalar uma solução.
* Direitos de administração de inquilinos para criar uma nova conta de serviço para o serviço de chumbo usado para enviar pistas de ofertas de mercado comercial.
* Acesso ao portal de administração.
* Acesso ao portal Azure.

## <a name="install-the-solution"></a>Instale a solução

1. Descarregue a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)e guarde-a localmente para o seu computador.

1. Open Dynamics 365 Customer Engagement indo ao URL para a sua instância Dynamics, como `https://tenant.crm.dynamics.com` .

1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Definições Avançadas**.
 
    ![Dinâmica 365 Item de menu definições avançadas](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Na página **Definições,** abra o menu **Definições** na barra superior e selecione **Soluções**.

    >[!NOTE]
    >Se não vir as opções no ecrã seguinte, não tem as permissões necessárias para prosseguir. Contacte um administrador sobre a sua instância de Envolvimento com o Cliente Dynamics 365.

    ![Opção Dinâmica 365 Soluções](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Na página **Solutions,** selecione **Import** e vá para onde guardou a solução **Microsoft Marketplace Lead Writer** que descarregou no passo 1.

    ![Botão de importação](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Importar completamente a solução seguindo o assistente de solução Import.

## <a name="configure-user-permissions"></a>Configure permissões de utilizador

Para escrever pistas na sua instância de Envolvimento ao Cliente Dynamics 365, tem de partilhar uma conta de serviço com a Microsoft e configurar permissões para a conta.

Utilize os seguintes passos para criar a conta de serviço e atribuir permissões. Pode utilizar o Azure Ative Directory ou o Office 365.

>[!NOTE]
>Passar para as instruções correspondentes com base na opção de autenticação selecionada. Ver [Diretório Ativo Azure](#azure-active-directory) ou [Escritório 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos esta opção porque nunca precisa de atualizar o seu nome de utilizador ou palavra-passe para continuar a obter pistas. Para utilizar a opção Azure Ative Directory, fornece o ID da aplicação, a chave de aplicação e o ID do Diretório ativo a partir da sua aplicação Ative Directory.

Para configurar o Azure Ative Directory for Dynamics 365 Customer Engagement:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**.

1. Selecione **Propriedades** e copie o valor de **ID** do Diretório na página **propriedades do Diretório.** Guarde este valor porque terá de o fornecer no portal de publicação para receber pistas para a sua oferta de mercado.

    ![Item do menu Azure Ative Directory Properties](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selecione **as inscrições** da App no Painel de Esquerda Azure Ative e, em seguida, selecione **Novo registo** nessa página.
1. Introduza um nome significativo para o nome da aplicação.
1. Nos **tipos de conta suportada** , selecione Contas em qualquer **diretório organizacional**.
1. Em **Redirecionar URI (opcional)** , selecione **Web** e introduza um URI, tal como `https://contosoapp1/auth` . 
1. Selecione **Registar**.

    ![Registar uma página de inscrição](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Agora que a sua candidatura está registada, aceda à página geral da candidatura. Copie o valor **de ID da Aplicação (cliente)** nessa página. Guarde este valor porque terá de o fornecer no portal de publicação e na Dynamics 365 para receber leads para a sua oferta de mercado.

    ![Caixa de ID de aplicação (cliente)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selecione **Certificados & segredos** do painel esquerdo da aplicação e selecione o botão secreto do **novo cliente.** Introduza uma descrição significativa para o segredo do cliente e selecione a opção **Nunca** em **Expira**. **Selecione Adicionar** para criar o segredo do cliente.

    ![Certificados & segredos item do menu](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Assim que o segredo do cliente for criado com sucesso, copie o valor secreto do **Cliente.** Não poderá recuperar o valor depois de sair da página. Guarde este valor porque terá de o fornecer no portal de publicação para receber pistas para a sua oferta de mercado. 
1. Selecione **permissões API** a partir do painel esquerdo da aplicação e, em seguida, selecione **+ Adicione uma permissão**.
1. Selecione **apis da Microsoft** e, em seguida, selecione **Dynamics CRM** como API.
1. Em **Que tipo de permissões a sua aplicação requer?** **Delegated permissions** 
1. Sob **Permissão** , selecione a caixa de verificação **user_impersonation** para o Access Common Data Service **como utilizadores da organização**. Em seguida, **selecione Adicionar permissões**.

    ![Adicionar botão de permissões](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Depois de completar os passos 1 a 14 no portal Azure, vá à sua instância de Envolvimento com o Cliente Dynamics 365 indo para o URL, como `https://tenant.crm.dynamics.com` .
1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Definições Avançadas**.
1. Na página **Definições,** abra o menu **Definições** na barra superior e selecione **Segurança**.
1. Na página **'Segurança',** selecione **Utilizadores.** Na página **dos Utilizadores,** selecione o drop-down **dos Utilizadores Ativados** e, em seguida, selecione **Utilizadores de Aplicações**.
1. Selecione **Novo** para criar um novo utilizador. 

    ![Criar um novo utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. No **painel novo do utilizador,** certifique-se de que **o UTILIZADOR: USER APPLICATION ESTÁ** selecionado. Forneça um nome de utilizador, nome completo e endereço de e-mail para o utilizador que pretende utilizar com esta ligação. Além disso, cole no ID da **aplicação** a aplicação que criou no portal Azure a partir do passo 8. **Selecione Guardar & Perto** de terminar de adicionar o utilizador.

    ![Novo painel de utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Aceda à secção "Definições de segurança" deste artigo para terminar a configuração da ligação para este utilizador.

### <a name="office-365"></a>Office 365

Se não quiser utilizar o Azure Ative Directory, pode registar um novo utilizador no centro de administração microsoft 365. Será necessário atualizar o seu nome de utilizador e senha a cada 90 dias para continuar a obter pistas.

Para configurar o Office 365 para a Dynamics 365 Customer Engagement:

1. Inicie sessão no [centro de administração do Microsoft 365](https://admin.microsoft.com).

1. **Selecione Adicionar um utilizador**.

    ![Microsoft 365 centro de administração Adicionar uma opção de utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Crie um novo utilizador para o serviço de escritores principal. Configure as seguintes definições:

    * Introduza um nome de utilizador.
    * Introduza uma palavra-passe e limpe o **utilizador Altere a sua palavra-passe quando iniciar a sua primeira** stenção na opção.
    * Selecione **o Utilizador (sem acesso ao administrador)** como função para o utilizador.
    * Selecione **Dynamics 365 Customer Engagement Plan** como a licença do produto, como mostrado no ecrã seguinte. Será cobrado pela licença que escolher. 

Guarde estes valores porque terá de fornecer os valores do Nome de **Utilizador** e **palavra-passe** no portal de publicação para receber leads para a sua oferta de mercado.

![Microsoft 365 centro de administração Novo painel de utilizadores](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Definições de segurança

O passo final é permitir que o utilizador que criou escreva os leads.

1. Open Dynamics 365 Customer Engagement indo ao URL para a sua instância Dynamics, como `https://tenant.crm.dynamics.com` .
1. Selecione o ícone de engrenagem na barra superior e, em seguida, selecione **Definições Avançadas**.
1. Na página **Definições,** abra o menu **Definições** na barra superior e selecione **Segurança**.
1. Na página **'Segurança',** selecione **Utilizadores** e selecione o utilizador que criou na secção "Configurar permissões de utilizador" deste documento. Em seguida, **selecione Gerir Funções**. 

    ![Gerir o separador De Papéis](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Procure o nome de função **Microsoft Marketplace Lead Writer** , e selecione-o para atribuir ao utilizador o papel.

    ![Gerir o painel de funções do utilizador](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Esta função é criada pela solução que importou e só tem permissões para escrever os leads e acompanhar a versão da solução para garantir a compatibilidade.

1. Volte para a página **de Segurança** e selecione **Funções de Segurança**. Procure o papel **Microsoft Marketplace Lead Writer** e selecione-o.

    ![Painel de Papéis de Segurança](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Na função de segurança, selecione o **separador Core Records.** Procure o item de **Configurações UI da Entidade utilizadora.** Ativar as permissões de Criar, Ler e Escrever ao Utilizador (1/4 círculo amarelo) para essa entidade clicando uma vez em cada um dos círculos correspondentes.

    ![Separador Core Records do Escritor Líder do Microsoft Marketplace](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. No separador **Personalização,** procure o item **'Trabalho do Sistema'.** Ativar as permissões de Ler, Escrever e Apêndice à Organização (círculos verdes sólidos) para essa entidade clicando quatro vezes em cada um dos círculos correspondentes.

    ![Separador de personalização do escritor líder do Microsoft Marketplace](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selecione **Guardar e fechar**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Configure a sua oferta de envio leva à Dynamics 365 Customer Engagement 

Para configurar a informação de gestão de chumbo para a sua oferta no portal de publicação:

1. Aceda à página **de configuração da Oferta** para a sua oferta.
1. Na secção **de comandos do Cliente,** selecione **Connect**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Ligações ao cliente":::

1. Na janela pop-up de detalhes da Ligação, selecione **Dynamics 365 Customer Engagement** para o destino principal.

    ![Caixa de destino de chumbo](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Introduza o **URL** para a instância Dynamics 365, tal como `https://contoso.crm4.dynamics.com` .

1. Selecione o método de **Autenticação** , ou Azure Ative Directory ou Office 365. 
1. Se selecionou **o Azure Ative Directory,** insira o **ID de Aplicação (cliente)** (por exemplo, `23456052-aaaa-bbbb-8662-1234df56788f` o ID do **Diretório** (por exemplo,) `12345678-8af1-4asf-1234-12234d01db47` e o Segredo do **Cliente** (por exemplo, `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ).

    ![Autenticação com Diretório Ativo Azure selecionado](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Se selecionar **o Office 365,** insira o **nome de utilizador** (por exemplo,) e a `contoso@contoso.onmicrosoft.com` **Palavra-passe** (por exemplo, `P@ssw0rd` ).

    ![Caixa de nome do utilizador office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Para **e-mail de contato,** insira endereços de e-mail para pessoas da sua empresa que devem receber notificações de e-mail quando um novo chumbo é recebido. Pode introduzir vários endereços de e-mail separando-os com pontos-e-vírguis.
1. Selecione **OK**.

Para se certificar de que está ligado com sucesso a um destino de chumbo, selecione o botão **Validate.** Se for bem sucedido, terá uma pista de teste no destino principal.

![Caixa de e-mail de contato](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Tem de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.