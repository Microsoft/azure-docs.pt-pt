---
title: Instalar o agente do aprovisionamento na cloud do Azure AD Connect
description: Este artigo descreve como instalar o agente de provisão de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb322805ac3368dd6ed8e193875e083b27195e1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695287"
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
1. Inscreva-se no portal Azure e, em seguida, vá para **o Diretório Ativo Azure**.
1. No menu esquerdo, selecione **Azure AD Connect**.
1. **Selecione Gerir o provisionamento (pré-visualização)**  >  **Rever todos os agentes**.
1. Descarregue o agente de provisionamento Azure AD Connect a partir do portal Azure.

   ![Baixar agente no local](media/how-to-install/install-9.png)</br>
1. Executar o instalador de provisionamento AZure AD Connect (AADConnectProvisioningAgent.Installer).
1. No ecrã do Pacote de **Agente de Provisionamento AD AD da Microsoft Azure,** aceite os termos de licenciamento e selecione **Instalar**.

   ![Microsoft Azure AD Connect Provisioning Agent Package Screen](media/how-to-install/install-1.png)</br>

1. Após o fim desta operação, o assistente de configuração começa. Inscreva-se na sua conta de administrador global Azure AD.
1. No ecrã **do Diretório Ativo Connect,** selecione **Add Directory**. Em seguida, inscreva-se na sua conta de administrador ative directory. Esta operação adiciona o seu diretório no local. Selecione **Seguinte**.

   ![Ligue o ecrã do Diretório Ativo](media/how-to-install/install-3.png)</br>

1. No ecrã completo da **Configuração,** **selecione Confirmar**. Esta operação regista e reinicia o agente.

   ![Ecrã completo de configuração](media/how-to-install/install-4a.png)</br>

1. Após o fim desta operação, deverá ver o aviso de que a configuração do **seu agente foi verificada com sucesso.** Selecione **Saída**.

   ![Botão de sair](media/how-to-install/install-5.png)</br>
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



## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
 
