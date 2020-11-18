---
title: Instalar o agente do aprovisionamento na cloud do Azure AD Connect
description: Este artigo descreve como instalar o agente de provisão de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cc26d9490a4bff7a7b3ca0853d4cd668f700a06
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651420"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalar o agente do aprovisionamento na cloud do Azure AD Connect
Este documento acompanha-o através do processo de instalação do Azure Ative Directory (Azure AD) Connect provisioning agent e como inicialmente configumentá-lo no portal Azure.

>[!IMPORTANT]
>As seguintes instruções de instalação pressupõem que todos os [Pré-requisitos foram cumpridos.](how-to-prerequisites.md)

A instalação e configuração do provisionamento Azure AD Connect é realizada nos seguintes passos:
    
- [Instalar o agente](#install-the-agent)
- [Verificar instalação do agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estes passos.

 1. Inscreva-se no servidor que utilizará com permissões de administração da empresa.
 2. Inscreva-se no portal Azure e, em seguida, vá para **o Diretório Ativo Azure**.
 3. No menu esquerdo, selecione **Azure AD Connect**.
 4. **Selecione Gerir o provisionamento (pré-visualização)**  >  **Rever todos os agentes**.
 5. Descarregue o agente de provisionamento Azure AD Connect a partir do portal Azure.
   ![Baixar agente no local](media/how-to-install/install-9.png)</br>
 6. Executar o instalador de provisionamento AZure AD Connect AADConnectProvisioningAgent.msi.
 7. No ecrã do Pacote de **Agente de Provisionamento AD AD da Microsoft Azure,** aceite os termos de licenciamento e selecione **Instalar**.
   ![Microsoft Azure AD Connect Provisioning Agent Package Screen](media/how-to-install/install-1.png)</br>
 8. Após o fim desta operação, o assistente de configuração começa. Inscreva-se na sua conta de administrador global Azure AD.
 9. No ecrã **do Diretório Ativo Connect,** selecione **Add Directory**. Em seguida, inscreva-se na sua conta de administrador ative directory. Esta operação adiciona o seu diretório no local. 
 10. Opcionalmente, pode gerir a preferência dos controladores de domínio que o agente utilizará selecionando a **prioridade do controlador de domínio Select** e encomendando a lista de controladores de domínio.   Clique em **OK**.
  ![Encomendar controlllers de domínio](media/how-to-install/install-2a.png)</br>
 11. Selecione **Seguinte**.
  ![Ligue o ecrã do Diretório Ativo](media/how-to-install/install-3.png)</br>
 12. No ecrã da **Conta de Serviço configurar** insira as credenciais de administração de domínio para criar a conta de Serviço Gerido do grupo que será usada para executar o serviço de agente.  Esta conta será nomeada provAgentgMSA$.
   ![Criar gMSA](media/how-to-install/install-10.png)</br>
 13.  No ecrã de **instalação** do agente confirme as definições e a conta que será criada e clique em **Confirmar**.
  ![Confirmar settngs](media/how-to-install/install-11.png)</br>
 14. Depois de terminar esta operação, deverá ver **se a instalação do seu agente está completa.** Selecione **Saída**.
  ![Ecrã completo de configuração](media/how-to-install/install-4a.png)</br>
1. Se ainda vir o ecrã inicial do **Pacote de Agente de Provisionamento do Microsoft Azure AD Connect,** selecione **Close**.

## <a name="verify-agent-installation"></a>Verificar instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente do portal Azure
Para verificar se o agente está a ser visto pelo Azure, siga estes passos.

1. Inicie sessão no portal do Azure.
1. À esquerda, selecione **Azure Ative Directory**  >  **Azure AD Connect**. No centro, **selecione Gerir o provisionamento (pré-visualização)**.

   ![Portal do Azure](media/how-to-install/install-6.png)</br>

1.  No ecrã **Azure AD Provisioning (pré-visualização),** selecione **Reveja todos os agentes**.

    ![Rever todas as opções de agentes](media/how-to-install/install-7.png)</br>
 
1. No **ecrã dos agentes de provisionamento no local, vê-se** os agentes que instalou. Verifique se o agente em questão está lá e está marcado *ativo*.

   ![Tela de agentes de provisionamento no local](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está a correr, siga estes passos.

1.  Inscreva-se no servidor com uma conta de administrador.
1.  **Serviços abertos** navegando para ele ou indo para **Start**  >  **Run**  >  **Services.msc**.
1.  Em **Serviços**, certifique-se de que **o Microsoft Azure AD Connect Agent Updater** e o Microsoft **Azure AD Connect Provisioning Agent** estão lá e o seu estado está a *funcionar*.

    ![Tela de serviços](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>O agente foi instalado, mas tem de ser configurado e ativado antes de começar a sincronizar os utilizadores. Para configurar um novo agente, consulte [Criar uma nova configuração para o provisionamento baseado na nuvem AZure AD Connect](how-to-configure.md).

## <a name="group-managed-service-accounts"></a>Contas de Serviço Geridas de Grupo
Um grupo Managed Service Account é uma conta de domínio gerida que fornece gestão automática de passwords, gestão de nome principal de serviço simplificado (SPN), a capacidade de delegar a gestão a outros administradores, e também estende esta funcionalidade a vários servidores.  O Azure AD Connect Cloud Sync suporta e recomenda a utilização de uma conta de serviço gerida por grupo para executar o agente.  Para obter mais informações sobre um gMSA, consulte [contas de serviço geridas pelo grupo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Modernização de um agente existente para utilizar a conta gMSA
Para atualizar um agente existente para utilizar a conta gMSA criada durante a instalação, basta atualizar o serviço de agente para a versão mais recente executando o AADConnectProvisioningAgent.msi.  Isto irá atualizar o serviço para a versão mais recente.  Agora passe novamente pelo assistente de instalação e forneça as credenciais para criar a conta quando solicitado.




## <a name="next-steps"></a>Próximos passos 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
 
