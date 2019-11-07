---
title: Definir configurações de origem para a recuperação de desastres do VMware no Azure com Azure Site Recovery
description: Este artigo descreve como configurar seu ambiente local para replicar VMs VMware para o Azure com Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622216"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurar o ambiente de origem para replicação do VMware para o Azure

Este artigo descreve como configurar seu ambiente local de origem, para replicar VMs do VMware no Azure. O artigo inclui etapas para selecionar o cenário de replicação, configurar um computador local como o Site Recovery servidor de configuração e descobrir automaticamente as VMs locais.

## <a name="prerequisites"></a>Pré-requisitos

O artigo pressupõe que você já tenha:

- Planejou sua implantação com a ajuda de [planejador de implantações do Azure site Recovery](site-recovery-deployment-planner.md). Isso ajuda a alocar largura de banda suficiente, com base em sua taxa diária de alteração de dados, para atender ao RPO (objetivo de ponto de recuperação) desejado.
- [Configurar recursos](tutorial-prepare-azure.md) no [portal do Azure](https://portal.azure.com).
- [Configure o VMware local](vmware-azure-tutorial-prepare-on-premises.md), incluindo uma conta dedicada para a descoberta automática.

## <a name="choose-your-protection-goals"></a>Escolher suas metas de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Neste cenário, estamos a utilizar **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde estão localizadas as máquinas virtuais**, selecione **No local**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Em **As suas máquinas estão virtualizadas**, selecione **Sim, com o VMware vSphere Hypervisor**. Em seguida, selecione **OK**.

## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Você pode configurar o servidor de configuração como uma VM do VMware local por meio de um modelo de OVA (aplicativo de virtualização aberta). [Saiba mais](concepts-vmware-to-azure-architecture.md) sobre os componentes que serão instalados na VM VMware.

1. Saiba mais sobre os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) para a implantação do servidor de configuração.
2. [Verifique os números de capacidade](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) para implantação.
3. [Baixe](vmware-azure-deploy-configuration-server.md#download-the-template) e [importe](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) o modelo OVA para configurar uma VM VMware local que executa o servidor de configuração. A licença fornecida com o modelo é uma licença de avaliação e é válida por 180 dias. Poste esse período, o cliente precisa ativar as janelas com uma licença adquirida.
4. Ative a VM do VMware e [Registre-](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) a no cofre dos serviços de recuperação.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery exclusões de pastas do programa antivírus

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Se o software antivírus estiver ativo na máquina de origem

Se o computador de origem tiver um software antivírus ativo, a pasta de instalação deverá ser excluída. Portanto, exclua a pasta *C:\ProgramData\ASR\agent* para replicação suave.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Se o software antivírus estiver ativo no servidor de configuração

Exclua as pastas a seguir do software antivírus para replicação suave e para evitar problemas de conectividade

- C:\Arquivos de Programas\microsoft Azure Recovery Services Agent.
- C:\Arquivos de Programas\microsoft Azure Site Recovery Provider
- C:\Arquivos de Programas\microsoft Azure Site Recovery Configuration Manager 
- C:\Arquivos de Programas\microsoft Azure Site Recovery ferramenta de coleta de erros 
  - C:\thirdparty
  - C:\temp.
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Arquivos de programas (x86) \MySQL
  - C:\ProgramData\ASR
  - Azure Site Recovery C:\ProgramData\Microsoft
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Diretório de instalação do Site Recovery Server. Por exemplo: E:\Arquivos Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Se o software antivírus estiver ativo no servidor de processo de expansão/destino mestre

Exclua as pastas a seguir do software antivírus

1. C:\Arquivos de Programas\microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. Azure Site Recovery C:\ProgramData\Microsoft
7. Azure Site Recovery diretório de instalação do servidor de processo com balanceamento de carga, exemplo: C:\Arquivos de programas (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Passos seguintes
[Configurar seu ambiente de destino](./vmware-azure-set-up-target.md) 
