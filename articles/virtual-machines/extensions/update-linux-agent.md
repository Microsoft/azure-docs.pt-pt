---
title: Atualizar o agente Linux do Azure do GitHub
description: Saiba como atualizar o agente Linux do Azure para sua VM Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 03e1689ca495d3fd3c8efce6b039386711a49472
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904912"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o agente Linux do Azure em uma VM

Para atualizar o [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) em uma VM Linux no Azure, você já deve ter:

- Uma VM do Linux em execução no Azure.
- Uma conexão com essa VM do Linux usando SSH.

Você sempre deve verificar um pacote no repositório distribuição do Linux primeiro. É possível que o pacote disponível não seja a versão mais recente, no entanto, habilitar a atualização automática garantirá que o agente do Linux sempre obterá a atualização mais recente. Se você tiver problemas para instalar a partir dos gerenciadores de pacotes, deverá buscar o suporte do fornecedor distribuição.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Suporte mínimo ao agente de máquina virtual no Azure
Verifique o [suporte mínimo de versão para agentes de máquina virtual no Azure antes de](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) continuar.

## <a name="updating-the-azure-linux-agent"></a>Atualizando o agente Linux do Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Verifique a versão atual do pacote

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Atualizar cache do pacote

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada

Primeiro, verifique se ele está habilitado:

```bash
cat /etc/waagent.conf
```

Localize ' AutoUpdate. Enabled '. Se você vir essa saída, ela será habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

#### <a name="restart-agent-for-1404"></a>Reiniciar agente para 14, 4

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Reiniciar agente para 16, 4/17, 4

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Verifique a versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificar atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

Primeiro, verifique se ele está habilitado:

```bash
cat /etc/waagent.conf
```

Localize ' AutoUpdate. Enabled '. Se você vir essa saída, ela será habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Verifique a versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificar atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

Primeiro, verifique se ele está habilitado:

```bash
cat /etc/waagent.conf
```

Localize ' AutoUpdate. Enabled '. Se você vir essa saída, ela será habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Verifique a versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificar atualizações disponíveis

A saída acima mostrará se o pacote está atualizado.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

Primeiro, verifique se ele está habilitado:

```bash
cat /etc/waagent.conf
```

Localize ' AutoUpdate. Enabled '. Se você vir essa saída, ela será habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Verifique a versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificar atualizações disponíveis

Na saída do acima, isso mostrará se o pacote está atualizado,.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

Primeiro, verifique se ele está habilitado:

```bash
cat /etc/waagent.conf
```

Localize ' AutoUpdate. Enabled '. Se você vir essa saída, ela será habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 e Oracle Linux 7

Para Oracle Linux, verifique se o repositório de `Addons` está habilitado. Escolha Editar o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a `enabled=0` de linha para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste arquivo.

Em seguida, para instalar a versão mais recente do agente Linux do Azure, digite:

```bash
sudo yum install WALinuxAgent
```

Se você não encontrar o repositório do complemento, poderá simplesmente adicionar essas linhas ao final do seu arquivo. Repository de acordo com sua versão de Oracle Linux:

Para máquinas virtuais Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Para máquinas virtuais Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Em seguida, escreva:

```bash
sudo yum update WALinuxAgent
```

Normalmente, isso é tudo o que você precisa, mas se, por algum motivo, você precisar instalá-lo de https://github.com diretamente, use as etapas a seguir.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Atualizar o agente do Linux quando não existir nenhum pacote de agente para distribuição

Instale o wget (há alguns distribuições que não o instalam por padrão, como Red Hat, CentOS e Oracle Linux versões 6,4 e 6,5) digitando `sudo yum install wget` na linha de comando.

### <a name="1-download-the-latest-version"></a>1. Baixe a versão mais recente
Abra [a versão do agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) em uma página da Web e descubra o número de versão mais recente. (Você pode localizar sua versão atual digitando `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Para a versão 2.2. x ou posterior, digite:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A linha a seguir usa a versão 2.2.0 como exemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. instalar o agente Linux do Azure

#### <a name="for-version-22x-use"></a>Para a versão 2.2. x, use:
Talvez seja necessário instalar o pacote `setuptools` primeiro – consulte [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada

Primeiro, verifique se ele está habilitado:

```bash
cat /etc/waagent.conf
```

Localize ' AutoUpdate. Enabled '. Se você vir essa saída, ela será habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reinicie o serviço waagent
Para a maioria dos distribuições do Linux:

```bash
sudo service waagent restart
```

Para o Ubuntu, use:

```bash
sudo service walinuxagent restart
```

Para CoreOS, use:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirme a versão do agente Linux do Azure
    
```bash
waagent -version
```

Para CoreOS, o comando acima pode não funcionar.

Você verá que a versão do agente Linux do Azure foi atualizada para a nova versão.

Para obter mais informações sobre o agente Linux do Azure, consulte [Leiame do agente Linux do Azure](https://github.com/Azure/WALinuxAgent).
