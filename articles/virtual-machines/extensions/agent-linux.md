---
title: Descrição geral do agente de VM do Linux do Azure | Documentos da Microsoft
description: Saiba como instalar e configurar o agente do Linux (waagent) para gerir a interação da sua máquina virtual com o Azure Fabric Controller.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1defa08b0eb9ede2adec3b7ac12c873522dd6c37
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011605"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Compreendendo e usando o agente Linux do Azure

O agente de Linux do Microsoft Azure (waagent) gere o Linux e FreeBSD aprovisionamento e interação de VM com o Azure Fabric Controller. Além do agente Linux fornecendo funcionalidade de aprovisionamento, o Azure também fornece a opção de utilizar o cloud-init para alguns sistemas operacionais de Linux. O agente Linux fornece as seguintes funcionalidades para Linux e FreeBSD IaaS implementações:

> [!NOTE]
> Para obter mais informações, consulte a [Leiame](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Aprovisionamento de imagens**
  
  * Criação de uma conta de utilizador
  * Configurar tipos de autenticação SSH
  * Implementação de chaves públicas SSH e pares de chaves
  * Definir o nome de anfitrião
  * O nome de anfitrião de publicação para a plataforma de DNS
  * Relatório de impressão digital de chave de anfitrião do SSH para a plataforma
  * Gestão de recursos do disco
  * Formatação e a montagem do disco de recurso
  * Configurando o espaço de comutação
* **Redes**
  
  * Gere as rotas para melhorar a compatibilidade com servidores para plataforma de DHCP
  * Garante a estabilidade do nome de interface de rede
* **Kernel**
  
  * Configura o virtual NUMA (desativar o kernel <`2.6.37`)
  * Consumes Hyper-V entropy for /dev/random
  * Configura os tempos limite de SCSI para o dispositivo de raiz (o que poderia ser remoto)
* **Diagnóstico**
  
  * Redirecionamento da consola para a porta serial
* **Implementações do SCVMM**
  
  * Deteta e inicializa o agente do VMM para Linux quando em execução num ambiente do System Center Virtual Machine Manager 2012 R2
* **Extensão de VM**
  
  * Injetar componentes criados pela Microsoft e parceiros em VM do Linux (IaaS) para ativar o software e automatização de configuração
  * Implementação de referência de extensão de VM no [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicação
O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um tempo de inicialização anexados DVD para implementações de IaaS. Esse DVD inclui um ficheiro de configuração compatível com OVF, que inclui todas as informações de aprovisionamento que não sejam os pares de chaves SSH real.
* Um ponto final TCP expor uma API REST utilizada para obter a implementação e configuração da topologia.

## <a name="requirements"></a>Requisitos
Os seguintes sistemas foram testados e funcionam com o agente Linux do Azure:

> [!NOTE]
> Esta lista poderá ser diferente da lista oficial de sistemas suportados na plataforma Microsoft Azure, conforme descrito aqui: [https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4 +

Outros sistemas suportados:

* FreeBSD 10 + (o agente Linux do Azure v2.0.10 +)

O agente do Linux depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.6 +
* OpenSSL 1.0+
* OpenSSH 5.3 +
* Utilitários de sistema de ficheiros: parted sfdisk fdisk, mkfs,
* Ferramentas de palavra-passe: chpasswd, sudo
* Ferramentas de processamento de texto: PO, grep
* Ferramentas de rede: rotas de ip
* Suporte a kernel de montagem de sistemas de ficheiros do UDF.

## <a name="installation"></a>Instalação
A instalação a utilizar um RPM ou um pacote DEB no repositório de pacotes de sua distribuição é o método preferencial de instalação e atualização do agente Linux do Azure. Todas as a [apoiadas pelo fornecedores de distribuição](../linux/endorsed-distros.md) integrar o pacote do agente Linux do Azure em suas imagens e repositórios.

Consulte a documentação sobre o [repositório do agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent) para opções de instalação avançada, como a instalação de origem ou para localizações personalizadas ou prefixos.

## <a name="command-line-options"></a>Opções da linha de comandos
### <a name="flags"></a>Sinalizadores
* verboso: Aumentar a verbosidade do comando especificado
* força: Ignorar confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos
* help: Lista os comandos com suporte e sinalizadores.
* desaprovisionar: Tente limpar o sistema e torná-la adequada para reprovisionamento. Eliminações de operação seguinte:
  
  * Todas as chaves de anfitrião SSH (se Provisioning. regeneratesshhostkeypair 'y' no ficheiro de configuração)
  * Configuração de nomes em /etc/resolv.conf
  * Palavra-passe de raiz de /etc/shadow (se Provisioning. deleterootpassword 'y' no ficheiro de configuração)
  * Concessões de cliente DHCP em cache
  * Repõe o nome do anfitrião para localhost.localdomain

> [!WARNING]
> Desaprovisionamento não garante que a imagem é limpa de todas as informações confidenciais e adequada para redistribuição.
> 
> 

* desaprovisionamento + utilizador: Executa tudo no - desaprovisionamento (acima) e também elimina a última conta de utilizador aprovisionado (obtida a partir do /var/lib/waagent) e dados associados. Este parâmetro é quando Desconfiguração de uma imagem que foi anteriormente aprovisionamento no Azure, pelo que pode ser capturado e reutilizado.
* Versão: Apresenta a versão do waagent
* serialconsole: Configura o GRUB para marcar ttyS0 (a primeira porta serial) como a consola de arranque. Isto garante que os registos de arranque de kernel são enviados para a porta serial e disponibilizados para depuração.
* daemon: Execute waagent como um daemon para gerir a interação com a plataforma. Este argumento é especificado para waagent no script de init waagent.
* Introdução: Executar waagent como um processo em segundo plano

## <a name="configuration"></a>Configuração
Um ficheiro de configuração (/ etc/waagent.conf) controla as ações de waagent. O código a seguir mostra um ficheiro de configuração de exemplo:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

O seguinte que várias opções de configuração estão descritas. Opções de configuração são de três tipos; Booleano, cadeia de caracteres ou número inteiro. As opções de configuração booleano podem ser especificadas como "y" ou "n". A especial palavra-chave "None" podem ser utilizados para algumas entradas de configuração do tipo de cadeia de caracteres como os seguintes detalhes:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Isto permite ao utilizador ativar ou desativar a funcionalidade de aprovisionamento do agente. Valores válidos são "y" ou "n". Se o aprovisionamento estiver desativado, chaves de anfitrião e o utilizador SSH na imagem são preservadas e qualquer configuração especificada no Azure API de aprovisionamento é ignorada.

> [!NOTE]
> O `Provisioning.Enabled` parâmetro a predefinição é "n" nas imagens do Ubuntu em nuvem que utilizar o cloud-init para o aprovisionamento.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Se o conjunto, a palavra-passe de raiz no arquivo de cópias de sombra/etc/é apagado durante o processo de aprovisionamento.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Se o conjunto, todos os SSH anfitrião pares de chaves (ecdsa, dsa e rsa) é eliminado durante o processo de aprovisionamento do /etc/ssh /. E é gerado um par de chaves de raiz único.

O tipo de encriptação para o novo par de chaves é configurável pela entrada Provisioning.SshHostKeyPairType. Algumas distribuições recriar os pares de chaves SSH para todos os tipos de encriptação em falta quando o daemon de SSH é reiniciado (por exemplo, após um reinício).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Isso pode ser definido como um tipo de algoritmo de encriptação que é suportado pelo daemon de SSH na máquina virtual. Os valores normalmente suportados são "rsa", "dsa" e "ecdsa". "putty.exe" no Windows não suporta "ecdsa". Então, se pretender utilizar putty.exe no Windows para ligar a uma implementação do Linux, utilize "rsa" ou "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Se o conjunto de waagent monitoriza a máquina virtual do Linux para que as alterações de nome de anfitrião (conforme devolvido pelo comando "hostname") e automaticamente Atualize a configuração de rede na imagem para refletir a alteração. Para enviar a alteração de nome para os servidores DNS, o sistema de rede é reiniciado na máquina virtual. Em resumo, isso resulta de perda de conectividade de Internet.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Se o conjunto de waagent descodifica CustomData a partir de Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Se o conjunto de waagent executa CustomData após o aprovisionamento.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Esta opção permite que a palavra-passe para o utilizador de sys ser redefinidas; desativado por predefinição.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritmo utilizado pela crypt ao gerar o hash de palavra-passe.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Comprimento de salt aleatório utilizado ao gerar o hash de palavra-passe.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Se definido, o disco de recurso fornecido pela plataforma é formatado e montado pela waagent se o tipo de sistema de ficheiros solicitado pelo utilizador em "ResourceDisk.Filesystem" é diferente de "ntfs". Uma única partição de tipo (83) do Linux é disponibilizada no disco. Esta partição não está formatada se podem ser montados com êxito.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Esta ação Especifica o tipo de sistema de ficheiros para o disco de recurso. Valores suportados variam consoante a distribuição de Linux. Se a cadeia de caracteres é X, em seguida, mkfs. X deve estar presente na imagem do Linux. Imagens de 11 de SLES, normalmente, devem utilizar 'ext3'. Imagens de FreeBSD devem usar 'ufs2' aqui.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Esta ação Especifica o caminho em que o disco de recurso é montado. O disco de recurso é um *temporária* disco e poderá ser esvaziada quando a VM é desaprovisionada.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Especifica opções de montagem do disco a serem passados para o comando de -o de montagem. Esta é uma lista separada por vírgulas de valores, por ex. 'nodev, nosuid'. Ver mount(8) para obter detalhes.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Se definir um ficheiro de comutação (/ swapfile) é criado no disco de recurso e adicionado ao espaço de comutação do sistema.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
O tamanho do ficheiro de comutação em megabytes.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Se o conjunto de verbosidade de log é aumentado. Waagent regista /var/log/waagent.log e utiliza a funcionalidade de logrotate de sistema para girar os registos.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Se o conjunto, o agente tentar instalar e, em seguida, carregar um driver do kernel RDMA que corresponde à versão do firmware no hardware subjacente.

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Esta definição configura o tempo limite de SCSI em segundos em que as unidades de disco e os dados do sistema operacional. Se não for definido, o sistema são utilizadas predefinições.

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
Esta definição pode ser utilizada para especificar um caminho alternativo para openssl binário a utilizar operações criptográficas.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Se o conjunto, o agente utiliza este servidor proxy para aceder à internet. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Ativar ou desativar atualização automática para o estado do objetivo de processamento; a predefinição está ativada.



## <a name="ubuntu-cloud-images"></a>Imagens de nuvem do Ubuntu
Utilizar imagens de nuvem do Ubuntu [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) para executar muitas tarefas de configuração que caso contrário, teriam de ser gerenciadas pelo agente Linux do Azure. Aplicam as seguintes diferenças:

* **Provisioning.Enabled** a predefinição é "n" nas imagens do Ubuntu na Cloud que utilize o cloud-init para executar tarefas de aprovisionamento.
* Os seguintes parâmetros de configuração não têm qualquer efeito nas imagens do Ubuntu na Cloud que utilize o cloud-init para gerir o disco de recurso e espaço de comutação:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Para obter mais informações, consulte os seguintes recursos para configurar o ponto de montagem do disco de recurso e trocar espaço nas imagens do Ubuntu Cloud durante o aprovisionamento:
  
  * [Ubuntu Wiki: Configurar partições de troca](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injetar dados personalizados numa máquina Virtual do Azure](../windows/classic/inject-custom-data.md)

