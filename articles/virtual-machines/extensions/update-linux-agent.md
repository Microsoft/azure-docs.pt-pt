---
title: Atualizar o agente Linux do Azure a partir do GitHub | Microsoft Docs
description: Saiba como atualizar o agente Linux do Azure para a VM com Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: danis
ms.openlocfilehash: b5a482ef6d15f1c6b1942a6128a807d7c6189918
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o agente Linux do Azure numa VM

Ao atualizar a sua [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) numa VM com Linux no Azure, já tem de ter:

- Uma VM com Linux em execução no Azure.
- Uma ligação para essa VM com Linux através do SSH.

Deve sempre procurar um pacote no Linux distro repositório pela primeira vez. É possível que o pacote disponível não pode ser a versão mais recente, no entanto, ativar autoupdate irá garantir que o agente Linux sempre irá obter a atualização mais recente. Deve ter problemas de instalação de gestores de pacote, deve procurar o ressarcimento de suporte do fornecedor distro.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Suporte de agente mínimo da máquina virtual no Azure
Certifique-se a [suporte para a versão mínima para agentes de máquina virtual no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) antes de continuar.

## <a name="updating-the-azure-linux-agent"></a>Atualizar o agente Linux do Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

#### <a name="restart-agent-for-1404"></a>Reinicie o agente para 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Reinicie o agente para 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Ativar a atualização automática do agente
Esta versão do Debian não tem uma versão de > = 2.0.16, por conseguinte AutoUpdate não está disponível para o mesmo. O resultado do comando acima mostra se o pacote está atualizado.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch Database"

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="redhat--centos"></a>VM de Redhat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

A saída acima mostra se o pacote é atualizado.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

No resultado de acima, este irá mostrar o pacote estiver até Data.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 e 7

Para Oracle Linux, certifique-se de que o `Addons` repositório está ativado. Escolha Editar o ficheiro `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** deste ficheiro.

Em seguida, para instalar a versão mais recente do agente Linux do Azure, escreva:

```bash
sudo yum install WALinuxAgent
```

Se não encontrar o repositório de suplemento pode simplesmente adicionar estas linhas no fim do ficheiro .repo, de acordo com seu lançamento Oracle Linux:

Para máquinas de virtuais Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Para máquinas de virtuais Oracle Linux 7:

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

Normalmente, isto é tudo o que precisa, mas se por alguma razão tem de instalar a https://github.com diretamente, utilize os seguintes passos.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Atualizar o agente Linux quando não existe nenhum pacote de agente para distribuição

Instalar wget (existem alguns distros que não instale por predefinição, tais como a VM de Redhat, CentOS, Oracle Linux versões e 6.4 e 6.5) escrevendo `sudo yum install wget` na linha de comandos.

### <a name="1-download-the-latest-version"></a>1. Transferir a versão mais recente
Abra [a versão do agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) numa página web e a descobrir o número da versão mais recente. (Pode localizar a versão atual, escrevendo `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Para a versão 2.2.x ou posterior, escreva:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A linha seguinte utiliza a versão 2.2.0 como exemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Instale o agente Linux do Azure

#### <a name="for-version-22x-use"></a>Para a versão 2.2.x, utilize:
Poderá ter de instalar o pacote `setuptools` em primeiro lugar – consulte [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se a atualização automática está ativada

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se vir este resultado, está ativado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a executar:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reinicie o serviço de waagent
Para a maioria Linux distros:

```bash
sudo service waagent restart
```

Ubuntu, utilize:

```bash
sudo service walinuxagent restart
```

Para CoreOS, utilize:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirmar a versão do agente Linux do Azure
    
```bash
waagent -version
```

Para CoreOS, o comando acima pode não funcionar.

Verá que a versão do agente Linux do Azure foi atualizada para a nova versão.

Para obter mais informações sobre o agente Linux do Azure, consulte [Leia-me de agente do Azure Linux](https://github.com/Azure/WALinuxAgent).
