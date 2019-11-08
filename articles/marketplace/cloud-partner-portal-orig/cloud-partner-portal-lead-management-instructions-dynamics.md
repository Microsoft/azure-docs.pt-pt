---
title: Dynamics CRM | Azure Marketplace
description: Configure o gerenciamento de leads para o Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 4ccc8b85e72a4da3b0e640abcc70d24b7cdc54af
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825264"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configurar o gerenciamento de leads para o Dynamics CRM Online

Este artigo descreve como configurar o Dynamics CRM Online para processar leads de vendas.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes permissões de usuário são necessárias para concluir as etapas neste artigo:
- Você precisa ser um administrador em sua instância do Dynamics CRM Online para instalar uma solução.
- Você precisa ser um administrador de locatário para criar uma nova conta de serviço para o serviço de Lead.

<a name="install-the-solution"></a>Instalar a solução
--------------------

1.  Baixe a [solução de gravador de Microsoft Marketplace Lead](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente.

2.  Abra o Dynamics CRM Online e vá para configurações.
    >[!NOTE]
    >Se você não vir as opções na próxima captura de tela, não terá as permissões necessárias.
 
       ![Exibição de configuração do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecione **importar**e, em seguida, selecione a solução que você baixou na etapa 1.
 
    ![Opção de importação do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Conclua a instalação da solução.

## <a name="configure-user-permissions"></a>Configurar permissões de usuário

Para gravar clientes potenciais em sua instância do Dynamics CRM, você precisa compartilhar uma conta de serviço conosco e configurar permissões para a conta.

Use as etapas a seguir para criar a conta de serviço e atribuir permissões. Você pode usar o **Azure Active Directory** ou o **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos essa opção porque você tem o benefício adicional de nunca precisar atualizar seu nome de usuário/senha para continuar a obter clientes potenciais. Para usar a opção Azure Active Directory, forneça a ID do aplicativo, a chave do aplicativo e a ID do diretório do seu aplicativo Active Directory.

Use as etapas a seguir para configurar o Azure Active Directory para o Dynamics CRM.

1.  Entre no [portal do Azure](https://portal.azure.com/) e, em seguida, selecione o serviço Azure Active Directory.

2.  Selecione **Propriedades** e copie a **ID do diretório**. Essa é a identificação de sua conta de locatário que você precisa usar na Portal do Cloud Partner.

    ![Obter ID do diretório](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecione **registros de aplicativo**e, em seguida, selecione **novo registro de aplicativo**.
4.  Insira o nome do aplicativo.
5.  Para tipo, selecione **aplicativo Web/API**.
6.  Forneça uma URL. Esse campo não é necessário para clientes potenciais, mas é necessário para criar um aplicativo.
7. Selecione **Criar**.
8.  Agora que seu aplicativo está registrado, selecione **Propriedades** e, em seguida, selecione **copiar a ID do aplicativo**. Você usará essas informações de conexão no Portal do Cloud Partner.
9.  Em Propriedades, defina o aplicativo como multilocatário e, em seguida, selecione **salvar**.

10. Selecione **chaves** e crie uma nova chave com a duração definida para *nunca expirar*. Selecione **salvar** para criar a chave. 
11. No menu chaves, selecione **copiar o valor da chave.** Salve uma cópia desse valor porque você precisará dele para o Portal do Cloud Partner.
    
    ![Chave de obtenção de Dynamics registrada](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecione **as permissões necessárias** e, em seguida, selecione **Adicionar**. 
13. Selecione **Dynamics CRM Online** como a nova API e verifique a permissão para *acessar o CRM Online como usuários da organização*.

14. No Dynamics CRM, vá para usuários e selecione a lista suspensa "usuários habilitados" para alternar para **os usuários do aplicativo**.
    
    ![Usuários do aplicativo](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecione **novo** para criar um novo usuário. Selecione o menu suspenso usuário **: aplicativo** .
    
    ![Adicionar novo usuário do aplicativo](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Em **novo usuário**, forneça o nome e o email que você deseja usar com essa conexão. Cole a **ID do aplicativo** para o aplicativo que você criou no portal do Azure.

     ![Configurar novo usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Vá para "configurações de segurança" neste artigo para concluir a configuração da conexão para esse usuário.

### <a name="office-365"></a>Office 365

Se você não quiser usar Azure Active Directory, poderá registrar um novo usuário no *centro de administração do Microsoft 365*. Você será solicitado a atualizar seu nome de usuário/senha a cada 90 dias para continuar a obter clientes potenciais.

Use as etapas a seguir para configurar o Office 365 para Dynamics CRM.

1. Entre no centro de [Administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione o bloco **admin** .

    ![Administrador do Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecione **Adicionar um usuário**.

    ![Adicionar um utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Crie um novo usuário para o serviço de gravador de cliente potencial. Defina as seguintes configurações:

    -   Forneça uma senha e desmarque a opção "tornar este usuário alterar sua senha ao entrar pela primeira vez".
    -   Selecione "usuário (sem acesso de administrador)" como a função para o usuário.
    -   Selecione a licença de produto mostrada na próxima captura de tela. Você será cobrado pela licença que escolher. A solução também funcionará com a licença do Dynamics CRM Online Basic.
    
    ![Configurar permissões e licença de usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Configurações de segurança

A etapa final é habilitar o usuário que você criou para escrever os leads.

1.  Entre no Dynamics CRM Online.
2.  Em **configurações**, selecione **segurança**.
    
    ![Configurações de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecione o usuário que você criou em **permissões de usuário**e, em seguida, selecione **gerenciar funções de usuário**. Marque **Microsoft Marketplace gravador de cliente potencial** para atribuir a função.

    ![Atribuir função de usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Essa função é criada pela solução que você importou e tem permissões para gravar os leads e para acompanhar a versão da solução para garantir a compatibilidade.

4.  Em segurança, selecione **funções de segurança** e localize a função para Microsoft Marketplace gravador de cliente potencial.
    
    ![Configurar o gravador de Lead de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecione a guia **registros de núcleo** . Habilite criar/ler/gravar para a interface do usuário da entidade.

    ![Habilitar criação/leitura/gravação para o usuário](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Conclusão

Conclua a configuração do Dynamics CRM para o gerenciamento de leads adicionando as informações de conta geradas ao Portal do Cloud Partner. Por exemplo:

-   **Azure Active Directory** - **ID do aplicativo** (exemplo *: 23456052-aaaa-bbbb-8662-1234Df56788f*), **ID do diretório** (exemplo: *12345678-8Af1-4Asf-1234-12234d01db47*) e **chave do aplicativo** ( exemplo: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =* ).
-   **URL** **do - do Office 365** (exemplo: *https://contoso.crm4.dynamics.com* ), **nome de usuário** (exemplo: *contoso\@contoso.onmicrosoft.com*) e **senha** (por exemplo: *P\@ssw0rd*).
