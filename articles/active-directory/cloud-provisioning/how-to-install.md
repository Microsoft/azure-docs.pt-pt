---
title: Instalar o agente do aprovisionamento na cloud do Azure AD Connect
description: Este artigo descreve como instalar o agente de fornecimento de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263351"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalar o agente do aprovisionamento na cloud do Azure AD Connect
Este documento acompanha-o através do processo de instalação do Diretório Ativo Azure (Azure AD) Connect agent de provisionamento e como configurá-lo inicialmente no portal Azure.

>[!IMPORTANT]
>As seguintes instruções de instalação pressupõem que todos os [pré-requisitos foram cumpridos.](how-to-prerequisites.md)

A instalação e configuração do fornecimento de Ligação Azure AD é realizada nos seguintes passos:
    
- [Instale o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estes passos.

1. Inscreva-se no servidor que utilizará com permissões de administração da empresa.
1. Aceda ao portal do Azure. À esquerda, selecione **Azure Ative Directory**.
1. Selecione **Gerir o fornecimento (pré-visualização)**  > **Rever todos os agentes**.
1. Descarregue o agente de provisionamento Azure AD Connect a partir do portal Azure.

   ![Descarregue o agente no local](media/how-to-install/install9.png)</br>
1. Executar o instalador de provisionamento Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. No ecrã do pacote do pacote do pacote do Pacote de Fornecimento de **Ligação AD da Microsoft Azure,** aceite os termos de licenciamento e selecione **Instalar**.

   ![Microsoft Azure AD Connect Provisioning Agent Screen](media/how-to-install/install1.png)</br>

1. Após esta operação terminar, o assistente de configuração começa. Inscreva-se na sua conta de administrador global da AD Azure.
1. No ecrã **Connect Ative Directy,** selecione **Adicionar Diretório**. Em seguida, inscreva-se na sua conta de administrador de Diretório Ativo. Esta operação adiciona o seu diretório no local. Selecione **Seguinte**.

   ![Conecte ecrã de Diretório Ativo](media/how-to-install/install3.png)</br>

1. No ecrã completo da **Configuração,** selecione **Confirmar**. Esta operação regista e reinicia o agente.

   ![Ecrã de configuração concluída](media/how-to-install/install4.png)</br>

1. Após esta operação terminar, deverá ver o aviso De que a configuração do **seu agente foi verificada com sucesso.** Selecione **Saída**.

   ![Botão de saída](media/how-to-install/install5.png)</br>
1. Se ainda vir o ecrã inicial do pacote de pacote de fornecimento de dispositivos de **ligação ad ad da Microsoft Azure,** selecione **Close**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação de agente do portal Azure
Para verificar se o agente está a ser visto pelo Azure, siga estes passos.

1. Inicie sessão no Portal do Azure.
1. À esquerda, selecione **Azure Ative Directory** > **Azure AD Connect**. No centro, selecione **Gerir o provisionamento (pré-visualização)** .

   ![Portal do Azure](media/how-to-install/install6.png)</br>

1.  No ecrã **de provisionamento de AD Azure (pré-visualização),** selecione **Rever todos os agentes**.

    ![Reveja todos os agentes opção](media/how-to-install/install7.png)</br>
 
1. No ecrã dos agentes de **provisionamento no local,** vê-se os agentes instalados. Verifique se o agente em questão está lá e está marcado *ativo*.

   ![Rastreio de agentes de provisionamento no local](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verifique a porta
Para verificar se o Azure está a ouvir na porta 443 e que o seu agente pode comunicar com ele, siga estes passos.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Este teste verifica que os seus agentes podem comunicar com azure sobre a porta 443. Abra um navegador e vá para o URL anterior a partir do servidor onde o agente está instalado.

![Verificação da viabilidade portuária](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está a funcionar, siga estes passos.

1.  Inscreva-se no servidor com uma conta de administrador.
1.  Serviços **abertos** navegando até ele ou **indo iniciar** > **Executar** > **Services.msc**.
1.  No âmbito **dos Serviços,** certifique-se de que o **Microsoft Azure AD Connect Agent Updater** e o **Microsoft Azure AD Connect Provisioning Agent** estão lá e o seu estado está a *funcionar*.

    ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e ativado antes de começar a sincronizar os utilizadores. Para configurar um novo agente, consulte Criar uma nova configuração para o [fornecimento baseado em nuvem Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Passos seguintes 

- [O que é o provisionamento?](what-is-provisioning.md)
- [O que é o fornecimento de nuvem Azure AD Connect?](what-is-cloud-provisioning.md)
 
