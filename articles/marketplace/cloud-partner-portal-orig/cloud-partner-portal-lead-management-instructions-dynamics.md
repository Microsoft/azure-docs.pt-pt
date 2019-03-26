---
title: Dynamics CRM | Documentos da Microsoft
description: Configure a gestão de oportunidades potenciais no Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: a1398d172a5c578ec3c0f16627eadd1da3fd1e45
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437625"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configurar a gestão de oportunidades potenciais para o Dynamics CRM online

Este artigo descreve como configurar o Dynamics CRM Online para processar as oportunidades potenciais de venda.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes permissões de utilizador são a necessidade de concluir os passos neste artigo:
- Tem de ser um administrador na sua instância do Dynamics CRM Online para instalar uma solução.
- Tem de ser um administrador de inquilino para criar uma nova conta de serviço para serviço de oportunidades potenciais.

<a name="install-the-solution"></a>Instalar a solução
--------------------

1.  Transfira o [solução do Microsoft Marketplace levar escritor](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e guarde-o localmente.

2.  Abra o Dynamics CRM Online e aceda às definições.
    >[!NOTE]
    >Se não vir as opções na captura de ecrã seguinte, em seguida, não tem as permissões que necessárias.
 
       ![Vista de configuração do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Selecione **importação**e, em seguida, selecione a solução que transferiu no passo 1.
 
    ![Opção de importar do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Conclua a instalação da solução.

## <a name="configure-user-permissions"></a>Configurar as permissões de utilizador

Escrever leva em sua instância do Dynamics CRM que tem de partilhar uma conta de serviço-nos e configurar permissões para a conta.

Utilize os seguintes passos para criar a conta de serviço e atribuir as permissões. Pode usar **do Azure Active Directory** ou **do Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Recomendamos esta opção, porque tem o benefício adicional de nunca necessidade de atualizar o seu nome de utilizador/palavra-passe para poder para continuar a receber oportunidades potenciais. Para utilizar a opção do Azure Active Directory é fornecer o Id da aplicação, a chave da aplicação e o Id de diretório da sua aplicação do Active Directory.

Utilize os seguintes passos para configurar o Azure Active Directory para o Dynamics CRM.

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/) e, em seguida, selecione o serviço do Azure Active Directory.

2.  Selecione **propriedades** e, em seguida, copie a **Id do diretório**. Esta é sua identificação de conta de inquilino que terá de utilizar o Portal de parceiros da Cloud.

    ![Obter ID de diretório](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Selecione **registos de aplicações**e, em seguida, selecione **novo registo de aplicação**.
4.  Introduza o nome da aplicação.
5.  Para o tipo, selecione **aplicação Web / API**.
6.  Forneça um URL. Este campo não é necessária para oportunidades potenciais, mas é necessário para criar uma aplicação.
7. Selecione **Criar**.
8.  Agora que a aplicação fica registada, selecione **propriedades** e, em seguida, selecione **copiar o Id da aplicação**. Usará essas informações de ligação no Portal de parceiros de nuvem.
9.  Nas propriedades, defina o aplicativo como vários inquilinos e, em seguida, selecione **guardar**.

10. Selecione **chaves** e crie uma nova chave com a duração definida como *nunca expira*. Selecione **guardar** para criar a chave. 
11. No menu de chaves, selecione **copiar o valor da chave.** Guarde uma cópia deste valor de uma vez que precisará das mesmas para o Portal de parceiros da Cloud.
    
    ![Dynamics obter chave registrada](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Selecione **permissões obrigatórias** e, em seguida, selecione **Add**. 
13. Selecione **Dynamics CRM Online** como a nova API e verificar a permissão para *acesso CRM Online como utilizadores da organização*.

14. No Dynamics CRM, vá para os utilizadores e selecione o menu "Utilizadores ativada" pendente para alternar para **utilizadores da aplicação**.
    
    ![Utilizadores da aplicação](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Selecione **New** para criar um novo utilizador. Selecione o **utilizador: UTILIZADOR de aplicação** lista pendente.
    
    ![Adicionar novo utilizador da aplicação](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Na **novo utilizador**, forneça o nome e e-mail que pretende utilizar com esta ligação. Cole o **Id da aplicação** para a aplicação que criou no portal do Azure.

     ![Configurar o novo utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Aceda a "Definições de segurança" neste artigo para concluir a configuração da ligação para este utilizador.

### <a name="office-365"></a>Office 365

Se não quiser utilizar o Azure Active Directory, pode registrar um novo utilizador do *Centro de administração do Microsoft 365*. Será solicitado a atualizar o seu nome de utilizador/palavra-passe todos os 90 dias para continuar a obter oportunidades potenciais.

Utilize os seguintes passos para configurar o Office 365 para o Dynamics CRM.

1. Inicie sessão para o [Centro de administração do Microsoft 365](https://admin.microsoft.com).

2. Selecione o **administrador** mosaico.

    ![Office Online Admin](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Selecione **adicionar um utilizador**.

    ![Adicionar um utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Crie um novo utilizador para o serviço de escritor líder. Configure as seguintes definições:

    -   Forneça uma palavra-passe e desmarque a opção "Tornar este utilizador alterar a palavra-passe quando iniciam sessão pela primeira vez".
    -   Selecione "Utilizador (sem acesso de administrador)" como a função do utilizador.
    -   Selecione a licença de produto, mostrada na captura de ecrã seguinte. Será cobrado para a licença que escolher. A solução também irão funcionar com a licença do Dynamics CRM Online Basic.
    
    ![Configurar as permissões de utilizador e a licença](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Definições de segurança

A etapa final é permitir que o utilizador que criou para escrever as oportunidades potenciais.

1.  Iniciar sessão no Dynamics CRM online.
2.  No **configurações**, selecione **segurança**.
    
    ![Definições de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Selecione o utilizador que criou no **permissões de utilizador**e, em seguida, selecione **gerir funções de utilizador**. Verifique **escritor de levar do Microsoft Marketplace** para atribuir a função.

    ![Atribuir a função de utilizador](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Esta função é criada pela solução que importou e apenas tiver permissões para escrever as oportunidades potenciais e para controlar a versão de solução para garantir a compatibilidade.

4.  Na segurança, selecione **funções de segurança** e localize a função do escritor de levar do Microsoft Marketplace.
    
    ![Configurar o escritor de oportunidades potenciais de segurança](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Selecione o **registos centrais** separador. Ative criar/leitura/escrita para a entidade de utilizador da interface do Usuário.

    ![Criar/leitura/escrita para o utilizador de ativar](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Conclusão

Conclua a configuração Dynamics CRM para gestão de oportunidades potenciais, adicionando as informações da conta gerada para o Portal de parceiro de Cloud. Por exemplo:

-   **O Azure Active Directory** - **Id de aplicação** (exemplo: *23456052-aaaa-bbbb-8662-1234df56788f*), **Id do diretório** (exemplo: *12345678-8af1-4asf-1234-12234d01db47*), e **chave da aplicação** (exemplo: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **Url** (exemplo: *https://contoso.crm4.dynamics.com*), **nome de utilizador** (exemplo: *contoso\@ contoso.onmicrosoft.com*), e **palavra-passe** (exemplo: *P\@ssw0rd*).
