---
title: Configurar definições de origem para a recuperação de desastres VMware para Azure com a recuperação do site Azure
description: Este artigo descreve como configurar o seu ambiente no local para replicar VMware VMs para Azure com Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88189732"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurar o ambiente de origem para a replicação da VMware para a Azure

Este artigo descreve como configurar o ambiente de origem no local, para replicar VMware VMs para Azure. O artigo inclui passos para selecionar o seu cenário de replicação, configurar uma máquina no local como servidor de configuração de recuperação do site e descobrir automaticamente VMs no local.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume que já tem:

- Planeou a sua implantação com a ajuda do [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Isto ajuda-o a alocar largura de banda suficiente, com base na sua taxa diária de alteração de dados, para satisfazer o objetivo pretendido do ponto de recuperação (RPO).
- [Criar recursos](tutorial-prepare-azure.md) no [portal Azure.](https://portal.azure.com)
- [Configurar no local VMware,](vmware-azure-tutorial-prepare-on-premises.md)incluindo uma conta dedicada para a descoberta automática.

## <a name="choose-your-protection-goals"></a>Escolha os seus objetivos de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Neste cenário, estamos a utilizar **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Em seguida, selecione **Preparar Infraestrutura**.
3. Em **Objetivo de Proteção**  >  **Onde estão as suas máquinas,** selecione **On-ins**.
4. Em **Para onde pretende replicar as máquinas**, selecione **Para o Azure**.
5. Em **As suas máquinas estão virtualizadas**, selecione **Sim, com o VMware vSphere Hypervisor**. Em seguida, selecione **OK**.

## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Pode configurar o servidor de configuração como um VMware VM no local através de um modelo de Aplicação de Virtualização Aberta (OVA). [Saiba mais](./vmware-azure-architecture.md) sobre os componentes que serão instalados no VMware VM.

1. Saiba mais sobre os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) para a implementação do servidor de configuração.
2. [Verifique os números de capacidade](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) para a implantação.
3. [Descarregue](vmware-azure-deploy-configuration-server.md#download-the-template) e [importe](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) o modelo OVA para configurar um VMware VMware no local que executa o servidor de configuração. A licença fornecida com o modelo é uma licença de avaliação e é válida por 180 dias. Após este período, o cliente precisa de ativar as janelas com uma licença adquirida.
4. Ligue o VMware VM e [registe-o](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) no cofre dos Serviços de Recuperação.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Exclusões de pasta de recuperação do site Azure do programa Antivírus

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Se o software Antivírus estiver ativo na máquina Source

Se a máquina de origem tiver um software Antivírus ativo, a pasta de instalação deve ser excluída. Assim, exclua a pasta *C:\ProgramData\ASR\agente* para uma replicação suave.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Se o Software Antivírus estiver ativo no servidor de configuração

Exclua as pastas seguintes do software Antivírus para uma replicação suave e para evitar problemas de conectividade

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Ficheiros de programa\Microsoft Azure Site Recovery Provider
- C:\Ficheiros de programa\Microsoft Azure Site Recovery Processd Manager 
- C:\Ficheiros de programa\Microsoft Azure Site Recovery Error Collection Tool 
  - C:\terceira parte
  - C:\Temperatura
  - C:\morango
  - C:\ProgramData\MySQL
  - C:\Ficheiros do programa (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Diretório de instalação do servidor de recuperação do local. Por exemplo: E:\Ficheiros de programa (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Se o Software Antivírus estiver ativo no servidor de processo de escala/alvo principal

Excluir pastas seguintes do software Antivírus

1. C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Diretório de instalação de servidor de processo equilibrado da Azure Site Recovery, Exemplo: C:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>Se o Software Antivírus estiver ativo no Linux Master Target

Excluir pastas seguintes do software Antivírus

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>Passos seguintes
[Configurar o seu ambiente alvo](./vmware-azure-set-up-target.md) 
