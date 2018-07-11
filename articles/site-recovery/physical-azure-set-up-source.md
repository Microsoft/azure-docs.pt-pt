---
title: Configurar o ambiente de origem (servidores físicos no Azure) | Documentos da Microsoft
description: Este artigo descreve como configurar o seu ambiente no local para começar a replicar servidores físicos que executem Windows ou Linux no Azure.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: anoopkv
ms.openlocfilehash: 00b09db97e597521de5c73eeefab77b0dfa1304d
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916103"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Configurar o ambiente de origem (servidor físico para o Azure)

Este artigo descreve como configurar o seu ambiente no local para começar a replicar servidores físicos que executem Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo pressupõe que já tem:
1. O Cofre dos serviços de recuperação no [portal do Azure](http://portal.azure.com "portal do Azure").
3. Um computador físico em que pretende instalar o servidor de configuração.

### <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela seguinte lista os mínimos de hardware, software e requisitos de rede para um servidor de configuração.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Servidores proxy baseado em HTTPS não são suportadas pelo servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolha os seus objetivos de proteção

1. No portal do Azure, vá para o **serviços de recuperação** cofres dos painel e selecione o cofre.
2. Na **Resource** menu do cofre, clique em **introdução** > **Site Recovery** > **passo 1: preparar infraestrutura**   >  **Objetivo de proteção**.

    ![Selecione os objetivos](./media/physical-azure-set-up-source/choose-goals.png)
3. Na **objetivo de proteção**, selecione **para o Azure** e **não virtualizado/outro**e, em seguida, clique em **OK**.

    ![Selecione os objetivos](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

1. Na **preparar origem**, se não tiver um servidor de configuração, clique em **+ servidor de configuração** para adicionar um.

  ![Configurar a origem](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Na **Adicionar servidor** painel, verifique se **servidor de configuração** aparece no **tipo de servidor**.
4. Transfira o ficheiro de instalação do Site Recovery Unified Setup.
5. Transfira a chave de registo do cofre. Terá da chave de registo ao executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

    ![Configurar a origem](./media/physical-azure-set-up-source/set-source2.png)
6. No computador que está a utilizar como o servidor de configuração, execute **do Azure Site Recovery Unified Setup** para instalar o servidor de configuração, o servidor de processos e o servidor de destino mestre.

#### <a name="run-azure-site-recovery-unified-setup"></a>Configuração unificada de execução do Azure Site Recovery

> [!TIP]
> Registo do servidor de configuração falha se a hora no relógio do sistema do seu computador estiver mais de cinco minutos da hora local. Sincronizar o seu relógio do sistema com um [servidor de horas](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado através de uma linha de comando. Para obter mais informações, consulte [instalando o servidor de configuração com as ferramentas da linha de comandos](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Problemas comuns

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passos Seguintes

Próxima etapa envolve [como configurar o ambiente de destino](physical-azure-set-up-target.md) no Azure.
