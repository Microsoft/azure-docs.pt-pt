---
title: Atualize o agente dos Serviços de Recuperação do Microsoft Azure (MARS)
description: Saiba como atualizar o agente dos Serviços de Recuperação do Microsoft Azure (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672923"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Atualize o agente dos Serviços de Recuperação do Microsoft Azure (MARS)

Neste artigo aprenderá a:

* Identifique servidores com versões anteriores do agente MARS
* Atualizar instalações mars nesses servidores

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identifique servidores com versões anteriores do agente MARS

Para instalações do agente de backup Azure e do servidor de backup Azure:

1. Navegue para o Cofre de Serviços de Recuperação onde tem servidores registados que estão potencialmente a ser apoiados por versões mais antigas do agente. Pode encontrar uma lista representativa de cofres com agentes de backup azure mais antigos nos alertas azure backup update do Azure.
1. Na secção **definições** do lado esquerdo do Cofre de Serviços de Recuperação, selecione **Infraestrutura de backup** sob a secção **Gerir.**
1. Para descobrir os agentes de backup do Azure instalados como parte das instalações do servidor de backup Do Azure, vá a Servidores de **Gestão** de Backup em Servidores de **Gestão**. Isto listará os servidores que possuem instalações de servidores de backup Azure juntamente com o número da versão para o agente de backup Azure associado.

    ![Lista de agentes mars instalados como parte das instalações do servidor de backup Azure](./media/upgrade-mars-agent/backup-management-servers.png)

1. Para verificar as versões do agente para instalações de agentes do Microsoft Azure Recovery Services (MARS) ou para o agente de backup Azure, vá a **Servidores Protegidos** sob servidores de **gestão**. Em seguida, selecione **o agente de backup Azure** sob o tipo de gestão de backup. Isto listará os servidores que possuem instalações do agente Debackup Azure juntamente com o número da versão para a instalação.

    ![Lista de servidores com o agente MARS instalado](./media/upgrade-mars-agent/protected-servers.png)

1. Orse a coluna da versão do Agente de Backup Azure clicando na coluna **Versão do Agente** para instalações do Agente MARS ou na coluna versão do agente de backup **Azure** para instalações do servidor de backup Azure.

1. O passo anterior irá dar-lhe a lista de servidores com agentes de backup Azure que têm versões inferiores a 2.0.9083.0 ou versões de agente listadas como espaços em branco. Estes são os servidores onde os agentes de backup do Azure precisam de ser atualizados.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Atualize a instalação do agente MARS no servidor

Depois de identificar os servidores que necessitam de uma atualização do agente Debackup Azure, execute os seguintes passos para cada servidor identificado (utilizando o servidor de backup Azure ou o Agente MARS). [Faça o download da versão mais recente do agente de reserva Azure](https://aka.ms/azurebackup_agent) antes de seguir os passos abaixo.

1. Clique numa linha que tenha o agente de backup Azure inferior a 2.0.9083.0 ou em branco. Isto abrirá o ecrã de detalhes do servidor.

    ![Servidores protegidos com versões de agente desatualizadas](./media/upgrade-mars-agent/old-agent-version.png)

    ![Servidores de backup Azure com versões de agente desatualizadas](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Clique no **Connect** para receber um ficheiro de ligação remota para o ambiente de trabalho para se ligar ao servidor ou ligar-se diretamente ao servidor através da ligação Remote Desktop no seu servidor.

    ![Ligue-se ao servidor através de uma ligação remota de ambiente de trabalho](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Se o servidor listado não existir ou tiver sido desativado, pode ignorar os passos restantes abaixo e saltar para o servidor seguinte.

1. Insira os seus dados administrativos de login e inicie sessão.

1. Se o seu servidor ou proxy do servidor tiver acesso limitado à Internet, certifique-se de que as definições de firewall no servidor/proxy estão configuradas para permitir o URL apropriado à nuvem Azure que está a utilizar:

    Nuvem Azure | do IdP
    -- | ---
    Nuvem Azure (Pública) |   `https://login.windows.net`
    Nuvem Azure China 21Vianet   | `https://login.chinacloudapi.cn`
    Nuvem do Governo dos EUA Azure |   `https://login.microsoftonline.us`
    Nuvem Alemã Azul  |  `https://login.microsoftonline.de`

1. Copie o instalador de atualização do agente de reserva Azure para o servidor.

    ![Copy Azure backup agent update installer para servidor](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Execute o instalador. O assistente de atualização do agente de recuperação do Microsoft Azure abre.

    ![O Assistente de Upgrade do Agente de Recuperação do Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Clique em **Seguinte**.

1. Clique em **Atualizar**.

    ![Instalação do Agente de Serviços de Recuperação do Microsoft Azure](./media/upgrade-mars-agent/upgrade-installation.png)

1. O ecrã de confirmação final indica que o agente De reserva Azure foi atualizado com sucesso.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Para clientes do System Center Data Protection Manager (SC DPM)

Se instalou agentes de backup Azure nos servidores do System Center Data Protection Manager (SC DPM), então tem de seguir os passos abaixo para identificar se os seus servidores DPM precisam de uma atualização do agente de backup Azure:

1. Inscreva-se no seu servidor SC DPM como Administrador.
2. Abra a Consola DPM.
3. Clique em **Gestão** na navegação inferior esquerda da consola.
4. Dentro da informação que aparece na navegação à esquerda, procure informações sobre a versão do agente de backup Azure.
5. Se a versão for inferior a 2.0.9083.0, descarregue o mais recente instalador de agente de reserva Azure e execute o instalador no servidor DPM para atualizar o agente de backup Azure.

Repita os passos acima para todos os servidores DPM no seu ambiente.

## <a name="next-steps"></a>Passos seguintes

Saiba como fazer backup das [máquinas windows utilizando o agente Azure Backup MARS](backup-windows-with-mars-agent.md)
