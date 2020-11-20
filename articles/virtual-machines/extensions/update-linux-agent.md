---
title: Atualize o agente Azure Linux do GitHub
description: Saiba como atualizar o Agente Azure Linux para o seu Linux VM em Azure
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 9de866faeb706893101020c23bbba353b77148f6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964903"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o Agente Azure Linux num VM

Para atualizar o seu [Agente Azure Linux](https://github.com/Azure/WALinuxAgent) num Linux VM em Azure, já deve ter:

- Um Linux VM em Azure.
- Uma ligação com o Linux VM usando SSH.

Deve sempre verificar se há um pacote no repositório de distro Linux primeiro. É possível que o pacote disponível possa não ser a versão mais recente, no entanto, permitir a data de auto-data garantirá que o Agente Linux receberá sempre a mais recente atualização. Caso tenha problemas instalados nos gestores de pacotes, deverá procurar apoio do fornecedor de distro.

> [!NOTE]
> Para mais informações consulte [as distribuições de Linux endossadas no Azure](../linux/endorsed-distros.md)

Verifique o [suporte mínimo de versão para agentes de máquinas virtuais em Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) antes de prosseguir.


## <a name="ubuntu"></a>Ubuntu

Verifique a sua versão atual do pacote

```bash
apt list --installed | grep walinuxagent
```

Atualizar cache de pacote

```bash
sudo apt-get -qq update
```

Instale a versão mais recente do pacote

```bash
sudo apt-get install walinuxagent
```

Certifique-se de que a atualização automática está ativada. Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reiniciar o serviço waagengt para 14.04

```bash
initctl restart walinuxagent
```

Reinicie o serviço waagent para 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Chapéu Vermelho / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

Verifique a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

Ver atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

Instale a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço waagent

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

Verifique a sua versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

Ver atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

Instale a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

Certifique-se de que a atualização automática está ativada. Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

Verifique a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

Verifique as atualizações disponíveis. A saída acima irá mostrar-lhe se o pacote está atualizado.

Instale a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

Verifique a sua versão atual do pacote

```bash
zypper info python-azure-agent
```

Ver atualizações disponíveis

Na saída a partir do acima, isto irá mostrar-lhe se o pacote está atualizado.

Instale a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

Certifique-se de que a atualização automática está ativada 

Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/ Debian 7 "Stretch"

Verifique a sua versão atual do pacote

```bash
dpkg -l | grep waagent
```

Atualizar cache de pacote

```bash
sudo apt-get -qq update
```

Instale a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

Ativar a atualização automática do agente Esta versão da Debian não tem uma versão >= 2.0.16, pelo que o AutoUpdate não está disponível para o mesmo. A saída do comando acima irá mostrar-lhe se o pacote está atualizado.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

Verifique a sua versão atual do pacote

```bash
apt list --installed | grep waagent
```

Atualizar cache de pacote

```bash
sudo apt-get -qq update
```

Instale a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

Certifique-se de que a atualização automática está ativada Primeiro, verifique se está ativada:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 e Oracle Linux 7

Para a Oracle Linux, certifique-se de que o `Addons` repositório está ativado. Opte por editar o ficheiro `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux) e altere a linha `enabled=0` para `enabled=1` **abaixo de [ol6_addons]** ou **[ol7_addons]** neste ficheiro.

Em seguida, para instalar a versão mais recente do Agente Azure Linux, escreva:

```bash
sudo yum install WALinuxAgent
```

Se não encontrar o repositório adicional, pode simplesmente adicionar estas linhas no final do seu ficheiro .repo de acordo com a sua versão Oracle Linux:

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

Normalmente, isto é tudo o que precisa, mas se por alguma razão precisar de o instalar https://github.com diretamente, use os seguintes passos.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Atualize o Agente Linux quando não existir nenhum pacote de agente para distribuição

Instale o wget (existem alguns distros que não o instalam por padrão, como o Red Hat, CentOS e as versões Oracle Linux 6.4 e 6.5) digitando `sudo yum install wget` na linha de comando.

### <a name="1-download-the-latest-version"></a>1. Descarregue a versão mais recente
Abra [o lançamento do Agente Azure Linux no GitHub](https://github.com/Azure/WALinuxAgent/releases) numa página web e descubra o número de versão mais recente. (Pode localizar a sua versão atual digitando `waagent --version` .)

Para a versão 2.2.x ou posterior, escreva:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A seguinte linha utiliza a versão 2.2.0 como exemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Instalar o Agente Azure Linux

Para a versão 2.2.x, use: Pode ter de instalar a embalagem `setuptools` em primeiro lugar-- consulte [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

Certifique-se de que a atualização automática está ativada. Primeiro, verifique se está ativado:

```bash
cat /etc/waagent.conf
```

Encontre 'AutoUpdate.Activado'. Se vir esta saída, está ativada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para ativar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reiniciar o serviço waagent
Para a maioria dos distros Linux:

```bash
sudo service waagent restart
```

Para Ubuntu, utilize:

```bash
sudo service walinuxagent restart
```

Para CoreOS, utilize:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirmar a versão do Agente Azure Linux
    
```bash
waagent -version
```

Para o CoreOS, o comando acima pode não funcionar.

Verá que a versão Azure Linux Agent foi atualizada para a nova versão.

Para mais informações sobre o Agente Azure Linux, consulte [o Agente Azure Linux README](https://github.com/Azure/WALinuxAgent).
