---
title: Dinâmica CRM [ CRM ] Mercado Azure
description: Configure a gestão de chumbo para a Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416309"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configure a gestão de chumbo para A Dinâmica CRM online

Este artigo descreve como configurar o Dynamics CRM Online para processar os leads de vendas.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes permissões de utilizador são necessárias para completar os passos deste artigo:
- É necessário ser administrador na sua instância Dynamics CRM Online para instalar uma solução.
- Você precisa ser um administrador inquilino para criar uma nova conta de serviço para o serviço principal.

<a name="install-the-solution"></a>Instalar a solução
--------------------

1.  Descarregue a [solução Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e guarde-a localmente.

2.  Abra a Dinâmica CRM Online e vá a Definições.
    >[!NOTE]
    >Se não vir as opções na próxima captura do ecrã, então não tem as permissões de que precisa.
 
       ![Visão de configuração de dinâmica](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecione **Import**e, em seguida, selecione a solução que descarregou no passo 1.
 
    ![Opção de importação de dinâmicas](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Termine de instalar a solução.

## <a name="configure-user-permissions"></a>Configurar permissões de utilizador

Para escrever pistas na sua instância de CRM Dynamics tem de partilhar uma conta de serviço connosco e configurar permissões para a conta.

Utilize os seguintes passos para criar a conta de serviço e atribuir permissões. Pode utilizar **o Diretório Ativo Azure** ou o **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos esta opção porque obtém o benefício adicional de nunca precisar de atualizar o seu nome de utilizador/palavra-passe para continuar a obter leads. Para utilizar a opção Diretório Ativo Azure, fornece o Id app, a chave de aplicação e o Id do diretório a partir da sua aplicação Ative Directory.

Utilize os seguintes passos para configurar o Diretório Ativo Azure para o CRM Dynamics.

1.  Inscreva-se no [portal Azure](https://portal.azure.com/) e, em seguida, selecione o serviço de Diretório Ativo Azure.

2.  Selecione **Propriedades** e, em seguida, copie o **Id do Diretório**. Esta é a sua identificação de conta de inquilino que precisa de ser utilizada no Portal do Parceiro cloud.

    ![Obter ID do Diretório](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecione registos de **aplicativos**e, em seguida, selecione novo registo de **aplicação**.
4.  Introduza o nome da aplicação.
5.  Para Tipo, selecione **web app / API**.
6.  Forneça uma URL. Este campo não é necessário para pistas, mas é necessário para criar uma aplicação.
7. Selecione **Criar**.
8.  Agora que a sua aplicação está registada, selecione **Propriedades** e, em seguida, selecione **copiar o Id da Aplicação**. Utilizará esta informação de ligação no Portal do Parceiro cloud.
9.  Em Propriedades, detete a aplicação como Multi-arrendatária e, em seguida, selecione **Guardar**.

10. Selecione **Keys** e crie uma nova tecla com o conjunto duração para *nunca expirar*. Selecione **Guardar** para criar a chave. 
11. No menu Chaves, selecione **Copiar o valor-chave.** Guarde uma cópia deste valor porque vai precisar para o Portal do Parceiro cloud.
    
    ![A dinâmica obtém a chave registada](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecione **as permissões necessárias** e, em seguida, selecione **Adicionar**. 
13. Selecione **Dynamics CRM Online** como a nova API, e verifique a permissão para *Access CRM Online como utilizadores da organização.*

14. No CRM da Dynamics, vá aos Utilizadores e selecione a queda de "Utilizadores Ativados" para mudar para **utilizadores de aplicações**.
    
    ![Utilizadores de aplicações](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecione **Novo** para criar um novo utilizador. Selecione o **UTILIZADOR: DROPDown DO UTILIZADOR DA APLICAÇÃO.**
    
    ![Adicionar novo utilizador de aplicações](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Em **Novo Utilizador,** forneça o nome e o e-mail que pretende utilizar com esta ligação. Colar no Id de **aplicação** para a app que criou no portal Azure.

     ![Configurar novo utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Vá a "Definições de Segurança" neste artigo para terminar de configurar a ligação para este utilizador.

### <a name="office-365"></a>Office 365

Se não quiser utilizar o Azure Ative Directory, pode registar um novo utilizador no centro de administração da *Microsoft 365*. Será obrigado a atualizar o seu nome de utilizador/senha a cada 90 dias para continuar a obter pistas.

Utilize os seguintes passos para configurar o Office 365 para a Dynamics CRM.

1. Inicie sessão no [centro de administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione o azulejo **do Administrador.**

    ![Administrador online do escritório](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecione **Adicionar um utilizador**.

    ![Adicionar um utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Crie um novo utilizador para o serviço de escritores principais. Configure as seguintes definições:

    -   Forneça uma palavra-passe e desmarque a opção "Faça com que este utilizador altere a sua palavra-passe quando iniciar o seu início de sessão".
    -   Selecione "Utilizador (sem acesso a administrador)" como função para o utilizador.
    -   Selecione a licença do produto mostrada na captura do ecrã seguinte. Será cobrado pela licença que escolher. A solução também funcionará com a licença Base Online Dynamics CRM.
    
    ![Configure permissões e licenças de utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Definições de segurança

O passo final é permitir ao Utilizador que criou escrever as pistas.

1.  Inscreva-se na Dynamics CRM online.
2.  Em **Definições,** selecione **Security**.
    
    ![Definições de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecione o utilizador que criou nas **permissões**do Utilizador e, em seguida, selecione **Gerir as funções do utilizador**. Consulte o **Microsoft Marketplace Lead Writer** para atribuir o papel.

    ![Atribuir a função do utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Este papel é criado pela solução que importaste e só tem permissões para escrever os leads e para rastrear a versão da solução para garantir a compatibilidade.

4.  Em Security, selecione **Security Roles** e encontre o papel para Microsoft Marketplace Lead Writer.
    
    ![Configure o escritor principal de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecione o separador **Core Records.** Ativar Criar/Ler/Escrever para a UI da Entidade utilizadora.

    ![Ativar criar/ler/escrever para o utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Embrulhar

Termine de configurar o Dynamics CRM para gestão de chumbo adicionando a informação de conta gerada ao Portal do Parceiro cloud. Por exemplo:

-   **Id de aplicação** de **diretório** - ativo Azure (exemplo: *23456052-aaaa-bbbb-8662-1234df56788f),* **Id do diretório** (exemplo: *12345678-8af1-4asf-1234-12234d01db47*), e Chave de **Aplicação** (exemplo: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Url do Office 365** - **(exemplo:** **`https://contoso.crm4.dynamics.com`** **`contoso\@contoso.onmicrosoft.com`**), Nome do **utilizador** (exemplo: ) e **Palavra-passe** (exemplo: *P\@ssw0rd*).
