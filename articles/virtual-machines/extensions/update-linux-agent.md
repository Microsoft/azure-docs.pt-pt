---
title: Atualizar o agente Linux do Azure do GitHub | Documentos da Microsoft
description: Saiba como atualizar o agente Linux do Azure para a sua VM do Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: 5d53f34ea6b0983d0687cdaf6ec6271c703bb055
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998525"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o agente do Linux do Azure numa VM

Para atualizar seus [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) numa VM do Linux no Azure, já tem de ter:

- Uma VM do Linux em execução no Azure.
- Uma ligação para essa VM do Linux através de SSH.

Deve sempre verificar um pacote no repositório de distribuição do Linux pela primeira vez. É possível que o pacote disponível pode não ser a versão mais recente, no entanto, permitindo autoupdate irá garantir que o agente Linux sempre receberá a atualização mais recente. Deve ter problemas de instalar a partir de gestores de pacotes, deve buscar suporte do fornecedor de distribuição.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Suporte a agentes mínimo da máquina virtual no Azure
Verifique se o [suporte para a versão mínima para os agentes de máquina virtual no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) antes de continuar.

## <a name="updating-the-azure-linux-agent"></a>A atualizar o agente Linux do Azure

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

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

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

#### <a name="enable-agent-auto-update"></a>Ativar atualização automática do agente
Esta versão do Debian não tem uma versão > = 2.0.16, portanto a AutoUpdate não está disponível para o mesmo. A saída do comando acima mostra se o pacote está atualizado.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

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
#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

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

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SLES DE SUSE

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Verificar a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

A saída acima mostra se o pacote é atualizado.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

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

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

No resultado de acima, este mostrará se o pacote está atualizado.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 e 7

Para Oracle Linux, certifique-se de que o `Addons` repositório está ativado. Optar por editar o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` sob **[ol6_addons]** ou **[ol7_addons]** desta ficheiro.

Em seguida, para instalar a versão mais recente do agente Linux do Azure, escreva:

```bash
sudo yum install WALinuxAgent
```

Se não encontrar o repositório de suplemento pode simplesmente adicionar estas linhas no final do seu ficheiro .repo, de acordo com sua versão do Oracle Linux:

Para máquinas de virtuais de Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Para máquinas de virtuais de Oracle Linux 7:

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

Normalmente, isso é tudo o que precisa, mas se por algum motivo quiser instalá-lo a partir https://github.com diretamente, utilize os seguintes passos.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Atualizar o agente Linux quando não existe nenhum pacote de agente para distribuição

Instalar o wget (existem algumas distribuições que não instalação-lo por padrão, como as versões de Red Hat, CentOS e Oracle Linux 6.4 e 6.5), escrevendo `sudo yum install wget` na linha de comandos.

### <a name="1-download-the-latest-version"></a>1. Baixe a versão mais recente
Open [a versão do agente do Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) numa página da web e saiba de que o número da versão mais recente. (Pode localizar sua versão atual, escrevendo `waagent --version`.)

#### <a name="for-version-22x-or-later-type"></a>Para a versão 2.2.x ou posterior, escreva:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A seguinte linha utiliza versão 2.2.0, por exemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Instale o agente Linux do Azure

#### <a name="for-version-22x-use"></a>Para a versão 2.2.x, utilize:
Poderá ter de instalar o pacote `setuptools` pela primeira vez, consulte [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada

Em primeiro lugar, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir este resultado, é habilitado:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reinicie o serviço de waagent
Para a maioria das distribuições de Linux:

```bash
sudo service waagent restart
```

Para o Ubuntu, utilize:

```bash
sudo service walinuxagent restart
```

Para o CoreOS, utilize:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirmar a versão do agente Linux do Azure
    
```bash
waagent -version
```

Para CoreOS, o comando acima pode não funcionar.

Verá que a versão do agente Linux do Azure foi atualizada para a nova versão.

Para obter mais informações sobre o agente Linux do Azure, consulte [Leiame de agente do Linux do Azure](https://github.com/Azure/WALinuxAgent).
