---
title: Atualize um Azure VM com SUSE Linux Enterprise Server para SUSE 15 SP1 [ Microsoft Docs
description: Este artigo fornece etapas gerais sobre como usar o Sistema de Migração de Distribuição SUSE para atualizar o servidor SUSE Linux Enterprise para SUSE 15 SP1 para uma máquina virtual Azure.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359516"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Upgrade Azure VM com SLES 12 a SLES 15 SP1

Este artigo fornece etapas gerais sobre como atualizar o servidor SUSE Linux Enterprise (SLES) 12 a SLES 15 SP1 para uma máquina virtual Azure (VM). Para obter mais informações, consulte [utilizando o Sistema de Migração da Distribuição SUSE](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) e o Guia de [Atualização SUSE Linux Enterprise Server 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Caminhos de atualização suportados
A versão atual do SLES deve ser SLES 12 SP4 ou 12 SP5 antes de poder avançar para o SLES 15 SP1.

![A imagem sobre o caminho de upgrade suportado](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Pré-requisitos

- Planeie a atividade de migração de acordo com a janela de tempo de inatividade aprovada. Isto porque o VM reinicia durante a migração.
- Antes da atividade de migração, faça uma cópia de segurança completa do VM.
- Se a cópia de segurança não estiver configurada, faça uma cópia de segurança do disco SO.
- [Verifique se o VM é de geração V1 ou v2 de geração](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Upgrade de SUSE 12 SP4 ou SP5 para SUSE 15 SP1

1. Instale o mais recente pacote para o VM:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. Depois de concluída a instalação, reinicie o VM.

3. Verifique a versão kernel e SO. Certifique-se de que a versão é SUSE 12 SP4 ou SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Instale a **ativação suse-migração-sle15**. Quando o **pacote-migração-sle15-ativação** for instalado, outro pacote **SLES15-Migração** será automaticamente instalado como um pacote de dependência. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. Depois de concluída a instalação, executar o `reboot` comando para reiniciar o VM.

6. Aceda ao [portal Azure,](https://portal.azure.com)selecione o VM e, em seguida, selecione **a consola Serial**. Verá que o sistema para em "reboot: Reiniciar o sistema". Este processo deve demorar cerca de 15 a 45 minutos. Para a Geração 2 VM, pode estar preso no ecrã "reboot: Restarting system". Neste caso, espere 45 minutos. Se ainda não progredir mais, vá à página **geral** do VM no portal Azure, pare o VM e, em seguida, reinicie-o.

     ![A imagem sobre as mensagens na consola em série.](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. Depois de o sistema ser reiniciado com um novo núcleo, verá a seguinte mensagem.

     ![A imagem sobre as mensagens na consola em série depois do sistema ser reiniciado com o novo núcleo.](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Verifique se a versão kernel e SO para verificar se o sistema foi atualizado com sucesso.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Verifique a versão de geração para um VM

Pode utilizar um dos seguintes métodos para verificar a versão geração:

- No terminal SLES, executar o `dmidecode | grep -i hyper` comando. Se for uma geração V1 VM, não há saída devolvida. Para as gerações V2 VMs, verá a seguinte saída:

     ![A imagem sobre a saída para a geração 2 vm](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- No [portal Azure,](https://portal.azure.com)vá às **Propriedades** do VM e, em seguida, verifique o campo de **geração VM.**
