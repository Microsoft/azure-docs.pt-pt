---
title: Configurar o servidor de configuração para a recuperação de desastres de servidores físicos para o Azure usando a Recuperação do Site Azure Microsoft Docs'
description: Este artigo descreve como configurar o servidor de configuração no local para a recuperação de desastres de servidores físicos no local para Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: d2cc3627222bcd8de5a5cb1b5c83b04c5d4ef880
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130206"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Configurar o servidor de configuração para recuperação após desastre dos servidores físicos para Azure

Este artigo descreve como configurar o ambiente no local para começar a replicar servidores físicos que executam o Windows ou o Linux em Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume que já tem:
- Um cofre dos Serviços de Recuperação no [portal Azure.](https://portal.azure.com "Portal do Azure")
- Um computador físico para instalar o servidor de configuração.
- Se tiver desativado o TLS 1.0 na máquina em que está a instalar o servidor de configuração, certifique-se de que os TLs 1.2 estão ativados e que a versão 4.6 ou posterior do Quadro .NET é instalada na máquina (com uma criptografia forte ativada). [Saiba mais](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela seguinte lista os requisitos mínimos de hardware, software e rede para um servidor de configuração.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Os servidores proxy baseados em HTTPS não são suportados pelo servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolha os seus objetivos de proteção

1. No portal Azure, vá à lâmina dos cofres dos **Serviços de Recuperação** e selecione o seu cofre.
2. No menu **de recursos** do cofre, clique em **Iniciar o**Passo de  >  **Recuperação do Local**  >  **1: Prepare o**objetivo de  >  **proteção da**infraestrutura .

    ![Selecione os objetivos](./media/physical-azure-set-up-source/choose-goals.png)
3. Na **meta proteção**, selecione **Para Azure** e **Não virtualizado/Outros**, e clique em **OK**.

    ![Selecione os objetivos](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

1. Na **fonte Preparar**, se não tiver um servidor de configuração, clique em **+Servidor de Configuração** para adicionar um.

   ![Configurar a origem](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Na lâmina **do Servidor de Adicionar,** verifique se o **Servidor de Configuração** aparece no **tipo servidor**.
4. Descarregue o ficheiro de instalação de configuração unificada de recuperação do site.
5. Transfira a chave de registo do cofre. Precisa da chave de registo quando executar a Configuração Unificada. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/physical-azure-set-up-source/set-source2.png)
6. Na máquina que está a utilizar como servidor de configuração, executar **a Configuração Unificada de Recuperação do Site Azure** para instalar o servidor de configuração, o servidor de processo e o servidor alvo principal.

#### <a name="run-azure-site-recovery-unified-setup"></a>Executar Azure Site Recovery Unificado Configuração

> [!TIP]
> O registo do servidor de configuração falha se a hora no relógio do sistema do computador estiver a mais de cinco minutos da hora local. Sincronizar o relógio do sistema com um [servidor de tempo](/windows-server/networking/windows-time-service/windows-time-service-top) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado através de uma linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Problemas comuns

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passos seguintes

O próximo passo consiste em [configurar o seu ambiente alvo](physical-azure-set-up-target.md) em Azure.
