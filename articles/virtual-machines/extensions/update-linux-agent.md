---
title: Atualize o Agente Azure Linux do GitHub
description: Saiba como atualizar o Agente Azure Linux para o seu VM Linux em Azure
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
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
ms.openlocfilehash: e4489f7c810799ca8e89565fe698f398f942b089
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251711"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o Agente Azure Linux num VM

Para atualizar o seu [Agente Azure Linux](https://github.com/Azure/WALinuxAgent) num VM Linux em Azure, já deve ter:

- Um Linux VM em Azure.
- Uma ligação com o Linux VM usando SSH.

Deve sempre verificar se há um pacote no repositório de distro Linux primeiro. É possível que o pacote disponível não seja a versão mais recente, no entanto, permitir a atualização automática irá garantir que o Agente Linux receberá sempre a mais recente atualização. Caso tenha problemas de instalação dos gestores de pacotes, deverá procurar apoio do fornecedor de distro.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Suporte mínimo de agente de máquinavirtual em Azure
Verifique o suporte mínimo [para os agentes de máquinas virtuais em Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) antes de prosseguir.

## <a name="updating-the-azure-linux-agent"></a>Atualizar o Agente Azure Linux

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Atualizar cache pacote

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

#### <a name="restart-agent-for-1404"></a>Reinício do agente para 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Reinício do agente para 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Chapéu Vermelho / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

A saída acima mostra-lhe se o pacote está atualizado.

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verifique as atualizações disponíveis

Na saída a partir do acima, isto irá mostrar-lhe se o pacote está atualizado.

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Reiniciar o serviço waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/ Debian 7 "Stretch"

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Atualizar cache pacote

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Ativar a atualização automática do agente
Esta versão da Debian não tem uma versão >= 2.0.16, pelo que o AutoUpdate não está disponível para o mesmo. A saída do comando acima mostrar-lhe-á se o pacote estiver atualizado.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Verifique a sua versão atual do pacote

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Atualizar cache pacote

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instale a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada
Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 e Oracle Linux 7

Para a Oracle Linux, certifique-se de que o repositório `Addons` está ativado. Opte por editar o ficheiro `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e alterar a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste ficheiro.

Em seguida, para instalar a versão mais recente do Agente Azure Linux, escreva:

```bash
sudo yum install WALinuxAgent
```

Se não encontrar o repositório de complemento, pode simplesmente adicionar estas linhas no final do seu ficheiro .repo de acordo com o seu lançamento Oracle Linux:

Para o Oracle Linux 6 máquinas virtuais:

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

Normalmente isto é tudo o que você precisa, mas se por alguma razão você precisa instalá-lo a partir de https://github.com diretamente, use os seguintes passos.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Atualize o Agente Linux quando não existir nenhum pacote de agente para distribuição

Instale wget (existem alguns distros que não o instalam por padrão, como as versões Red Hat, CentOS e Oracle Linux 6.4 e 6.5) digitando `sudo yum install wget` na linha de comando.

### <a name="1-download-the-latest-version"></a>1. Descarregue a versão mais recente
Abra [o lançamento do Agente Azure Linux no GitHub](https://github.com/Azure/WALinuxAgent/releases) numa página web e descubra o número mais recente da versão. (Pode localizar a sua versão atual digitando `waagent --version`.)

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

### <a name="2-install-the-azure-linux-agent"></a>2. Instale o Agente Azure Linux

#### <a name="for-version-22x-use"></a>Para a versão 2.2.x, utilize:
Pode ser necessário instalar a embalagem `setuptools` primeira vista [aqui.](https://pypi.python.org/pypi/setuptools) Em seguida, execute:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Certifique-se de que a atualização automática está ativada

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Enabled'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para permitir a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reiniciar o serviço de waagent
Para a maioria dos distros linux:

```bash
sudo service waagent restart
```

Para Ubuntu, use:

```bash
sudo service walinuxagent restart
```

Para o CoreOS, utilize:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirme a versão do Agente Azure Linux
    
```bash
waagent -version
```

Para o CoreOS, o comando acima pode não funcionar.

Verá que a versão do Agente Azure Linux foi atualizada para a nova versão.

Para mais informações sobre o Agente Azure Linux, consulte [o Agente Azure Linux README](https://github.com/Azure/WALinuxAgent).
