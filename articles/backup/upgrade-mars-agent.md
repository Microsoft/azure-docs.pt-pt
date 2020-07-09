---
title: Atualize o agente microsoft Azure Recovery Services (MARS)
description: Saiba como atualizar o agente Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78672923"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Atualize o agente microsoft Azure Recovery Services (MARS)

Neste artigo você vai aprender a:

* Identificar servidores com versões anteriores do agente MARS
* Atualizar as instalações do MARS nesses servidores

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identificar servidores com versões anteriores do agente MARS

Para instalações do agente de backup Azure e do servidor de backup Azure:

1. Navegue para o Cofre de Serviços de Recuperação onde tem servidores registados que estão potencialmente a ser apoiados por versões mais antigas do agente. Pode encontrar uma lista representativa de cofres com agentes de backup Azure mais antigos nos alertas de Atualização de Backup Azure do Azure.
1. Na secção definições do lado esquerdo do cofre de **serviços** de recuperação, selecione **Infraestruturas de backup** na secção **Gestão.**
1. Para descobrir os agentes de backup da Azure instalados como parte das instalações do servidor backup do Azure, vá a **Servidores de Gestão de Backup** em **Servidores de Gestão**. Isto irá listar os servidores que têm instalações do servidor Azure Backup juntamente com o número de versão do agente Azure Backup associado.

    ![Lista de agentes MARS instalados como parte das instalações do servidor backup do Azure](./media/upgrade-mars-agent/backup-management-servers.png)

1. Para verificar as versões de agentes para as instalações do agente do Microsoft Azure Recovery Services (MARS) ou para o agente de backup Azure, vá a **Servidores Protegidos** em **Servidores de Gestão**. Em seguida, selecione **o agente de backup Azure** sob o Tipo de Gestão de Cópias de Segurança. Isto irá listar os servidores que têm instalações de agente Azure Backup juntamente com o número de versão para a instalação.

    ![Lista de servidores com o agente MARS instalado](./media/upgrade-mars-agent/protected-servers.png)

1. Serdene a coluna da versão Azure Backup Agent clicando na coluna **versão do agente** para instalações do Agente MARS ou na coluna versão do agente de backup **Azure** para instalações de servidor de backup Azure.

1. O passo anterior irá dar-lhe a lista de servidores com agentes Azure Backup que têm versões inferiores a 2.0.9083.0 ou versões de agente listadas como espaços em branco. Estes são os servidores onde os agentes de backup do Azure precisam de ser atualizados.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Atualize a instalação do agente MARS no servidor

Depois de identificar os servidores que necessitam de uma atualização do agente de backup Azure, execute os seguintes passos para cada servidor identificado (utilizando o servidor de backup Azure ou o Agente MARS). [Descarregue a versão mais recente do agente de backup Azure](https://aka.ms/azurebackup_agent) antes de seguir os passos abaixo.

1. Clique numa linha que tenha o agente Azure Backup inferior a 2.0.9083.0 ou em branco. Isto abrirá o ecrã de detalhes do servidor.

    ![Servidores protegidos com versões de agente desatualizadas](./media/upgrade-mars-agent/old-agent-version.png)

    ![Servidores de backup Azure com versões de agente desatualizadas](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Clique em **Connect** para receber um ficheiro de ligação ao ambiente de trabalho remoto para se ligar ao servidor ou ligar-se diretamente ao servidor através da ligação Remote Desktop no seu servidor.

    ![Conecte-se ao servidor através de ligação remota ao ambiente de trabalho](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Se o servidor listado não existir ou tiver sido desativado, pode ignorar os passos restantes abaixo e saltar para o próximo servidor.

1. Insira os seus dados de login administrativo e inicie sessão.

1. Se o servidor ou o representante do servidor tiver acesso limitado à Internet, certifique-se de que as definições de firewall no servidor/procuração estão configuradas para permitir o URL adequado à nuvem Azure que está a usar:

    Nuvem de Azure | URL
    -- | ---
    Nuvem de Azure (Público) |   `https://login.windows.net`
    Nuvem Azure China 21Vianet   | `https://login.chinacloudapi.cn`
    Nuvem do Governo dos EUA de Azure |   `https://login.microsoftonline.us`
    Nuvem Alemã Azure  |  `https://login.microsoftonline.de`

1. Copie o instalador de atualização do agente de backup Azure para o servidor.

    ![Copy Azure backup agent updateer to server](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Execute o instalador. O assistente de atualização do agente de recuperação do Microsoft Azure é aberto.

    ![O assistente de atualização do agente de recuperação do Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Clique em **Seguinte**.

1. Clique em **Atualizar**.

    ![Instalação do Agente de Serviços de Recuperação do Microsoft Azure](./media/upgrade-mars-agent/upgrade-installation.png)

1. O ecrã de confirmação final indica que o agente Azure Backup foi atualizado com sucesso.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Para clientes Gestor de Proteção de Dados do Centro de Sistema (SC DPM)

Se instalou agentes de backup do Azure nos servidores Do Gestor de Proteção de Dados do Centro de Sistema (SC DPM), então tem de seguir os passos abaixo para identificar se os seus servidores DPM precisam de uma atualização do agente de backup Azure:

1. Inscreva-se no seu servidor SC DPM como Administrador.
2. Abra a Consola DPM.
3. Clique em **Gestão** na navegação inferior esquerda da consola.
4. Dentro das informações que aparecem na navegação à esquerda, procure as informações da versão do agente Azure Backup.
5. Se a versão for inferior a 2.0.9083.0, descarregue o mais recente instalador de backup do Azure e execute o instalador no servidor DPM para atualizar o agente Azure Backup.

Repita os passos acima para todos os servidores DPM no seu ambiente.

## <a name="next-steps"></a>Próximos passos

Saiba como [fazer backup das máquinas do Windows utilizando o agente Azure Backup MARS](backup-windows-with-mars-agent.md)
