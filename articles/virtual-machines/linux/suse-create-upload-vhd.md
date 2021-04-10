---
title: Criar e carregar um SUSE Linux VHD em Azure
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo SUSE Linux.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 276f5f4542ecea42c665764b8c4e5f66f2531126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552716"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Prepare a SLES or openSUSE virtual machine for Azure (Preparar uma máquina virtual SLES ou openSUSE para o Azure)


Este artigo pressupõe que já instalou um sistema operativo SUSE ou openSUSE Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros .vhd, por exemplo uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [instalar a função Hiper-V e configurar uma máquina virtual.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

## <a name="sles--opensuse-installation-notes"></a>Notas de instalação SLES / openSUSE
* Consulte também [as notas de instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet converte-vhd.
* Ao instalar o sistema Linux recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome LVM com VMs clonados, especialmente se um disco de SO precisar de ser ligado a outro VM para resolução de problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) podem ser usados em discos de dados, se preferir.
* Não configuure uma partição de troca no disco OS. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte [as notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="use-suse-studio"></a>Use estúdio SUSE
[O SUSE Studio](https://studioexpress.opensuse.org/) pode facilmente criar e gerir o seu SLES e abrir imagens SUSE para Azure e Hyper-V. Esta é a abordagem recomendada para personalizar o seu próprio SLES e abrir imagens SUSE.

Como alternativa à construção do seu próprio VHD, a SUSE também publica imagens BYOS (Bring Your Own Subscription) para SLES na [VM Depot](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Prepare o servidor de empresa SUSE Linux para o Azure
1. No painel central do Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Ligar** para abrir a janela para a máquina virtual.
3. Registe o seu sistema SUSE Linux Enterprise para permitir que descarregue atualizações e instale pacotes.
4. Atualize o sistema com as mais recentes correções:

    ```console
    # sudo zypper update
    ```
    
5. Instale o agente Azure Linux e o cloud-init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Permitir que o waagent & cloud-init comece no arranque

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Atualizar a configuração waagent e cloud-init

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Editar /etc/predefinição/ficheiro grub para garantir que os registos das consolas são enviados para a porta de série e, em seguida, atualizar o ficheiro de configuração principal com grub2-mkconfig -o /boot/grub2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Isto irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.
    
9. Certifique-se de que o disco com o seu UUID (by-uuid)
         
10. Modifique as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hiper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Recomenda-se a edição do ficheiro "/etc/sysconfig/network/dhcp" e alterar o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. Em "/etc/sudoers", comentar ou remover as seguintes linhas se existirem:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque. Este é geralmente o padrão.

14. Configuração de troca
 
    Não crie espaço de troca no disco do sistema operativo.

    Anteriormente, o Agente Azure Linux foi utilizado automaticamente para configurar o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a colocação da máquina virtual no Azure. No entanto, isto é agora tratado por cloud-init, **não deve** utilizar o Agente Linux para formatar o disco de recursos criar o ficheiro swap, modificar os seguintes parâmetros de `/etc/waagent.conf` forma adequada:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Se quiser montar, formatar e criar troca, pode:
    * Passe isto como um config de nuvem cada vez que criar um VM.
    * Utilize uma diretiva em nuvem cozida na imagem que o fará sempre que o VM for criado:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Clique em **Ação -> Desligar** em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.

---
## <a name="prepare-opensuse-131"></a>Preparar abrirSUSE 13.1+
1. No painel central do Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Ligar** para abrir a janela para a máquina virtual.
3. Na concha, executar o `zypper lr` comando' Se este comando devolver a saída semelhante à seguinte, então os repositórios são configurados como esperado-- não são necessários ajustamentos (note que os números da versão podem variar):

   | # | Alias                 | Name                  | Ativado | Atualizar
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Nuvem:Tools_13.1      | Nuvem:Tools_13.1      | Yes     | Yes
   | 2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
   | 3 | openSUSE_13.1_Atualizações | openSUSE_13.1_Atualizações | Yes     | Yes

    Se o comando retornar "Não há repositórios definidos..." em seguida, utilizar os seguintes comandos para adicionar estes repos:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Em seguida, pode verificar se os repositórios foram adicionados executando o comando `zypper lr` ' novamente. Caso não esteja ativado um dos repositórios de atualização relevantes, ative-o com o seguinte comando:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Atualize o núcleo para a versão mais recente disponível:

    ```console
    # sudo zypper up kernel-default
    ```

    Ou para atualizar o sistema com todas as correções mais recentes:

    ```console
    # sudo zypper update
    ```

5. Instale o Agente Azure Linux.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para isso, abra "/boot/grub/menu.lst" num editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Isto irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Além disso, retire os seguintes parâmetros da linha de arranque do núcleo, se existirem:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Recomenda-se a edição do ficheiro "/etc/sysconfig/network/dhcp" e alterar o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Importante:** Em "/etc/sudoers", comentar ou remover as seguintes linhas se existirem:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque.  Este é geralmente o padrão.
10. Não crie espaço de troca no disco SO.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado ao VM após o provisionamento no Azure. Note que o disco de recursos local é um disco *temporário* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo anterior), modificar os seguintes parâmetros em /etc/waagent.conf adequadamente:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Certifique-se de que o Agente Azure Linux funciona no arranque:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Clique em **Ação -> Desligar** em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto a usar o seu disco rígido virtual SUSE Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).