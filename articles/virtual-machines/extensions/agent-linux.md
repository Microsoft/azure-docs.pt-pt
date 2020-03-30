---
title: Visão geral do agente Azure Linux VM
description: Aprenda a instalar e configurar o Agente Linux (waagent) para gerir a interação da sua máquina virtual com o Controlador de Tecido Sinuoso.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073857"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Compreender e usar o Agente Azure Linux

O Agente Microsoft Azure Linux (waagent) gere o fornecimento de Linux & FreeBSD e a interação VM com o Controlador de TecidoS Azure. Além do Agente Linux que fornece a funcionalidade de provisionamento, o Azure também oferece a opção de usar cloud-init para alguns OSes Linux. O Agente Linux fornece a seguinte funcionalidade para implementações de IaaS Linux e FreeBSD:

> [!NOTE]
> Para mais informações, consulte a [README.](https://github.com/Azure/WALinuxAgent/blob/master/README.md)
> 
> 

* **Fornecimento de Imagem**
  
  * Criação de uma conta de utilizador
  * Configurar tipos de autenticação SSH
  * Implantação de chaves públicas SSH e pares de chaves
  * Definição do nome do anfitrião
  * Publicação do nome do anfitrião na plataforma DNS
  * Reportar a chave de impressão digital do SSH para a plataforma
  * Gestão de Discos de Recursos
  * Formatação e montagem do disco de recursos
  * Configurar o espaço de troca
* **Redes**
  
  * Gere rotas para melhorar a compatibilidade com os servidores DHCP da plataforma
  * Garante a estabilidade do nome da interface da rede
* **Kernel**
  
  * Configures o NUM virtual (desativar para <`2.6.37`de kernel)
  * Consome entropia Hiper-V para /dev/random
  * Configures os intervalos de tempo SCSI para o dispositivo radicular (que pode ser remoto)
* **Diagnóstico**
  
  * Reorientação da consola para a porta de série
* **Implantações SCVMM**
  
  * Deteta e bootstraps o agente VMM para Linux ao correr em um ambiente De Máquina Virtual Do System Center 2012 R2
* **Extensão VM**
  
  * Injetar componente da autoria da Microsoft e parceiros no Linux VM (IaaS) para permitir automatização de software e configuração
  * Implementação de referência de extensão VM em[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicação
A informação flui da plataforma para o agente através de dois canais:

* Um DVD em anexo para implantações IaaS. Este DVD inclui um ficheiro de configuração compatível com OVF que inclui todas as informações de provisionamento que não os pares de chaves SSH reais.
* Um ponto final do TCP expondo uma API REST usada para obter configuração de implantação e topologia.

## <a name="requirements"></a>Requisitos
Os seguintes sistemas foram testados e são conhecidos por funcionar com o Agente Azure Linux:

> [!NOTE]
> Esta lista pode diferir da lista oficial de sistemas suportados na Plataforma Microsoft Azure, conforme descrito aqui:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* Centos 6.3+
* Red Hat Enterprise Linux 6.7+
* Debiano 7.0+
* Ubuntu 12.04+
* aberturaSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Outros Sistemas Suportados:

* FreeBSD 10+ (Agente Azure Linux v2.0.10+)

O agente Linux depende de alguns pacotes do sistema para funcionar corretamente:

* Python 2.6+
* Abre 1.0+
* Abre 5.3+
* Utilitários do sistema de ficheiros: sfdisk, fdisk, mkfs, separados
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: ip-route
* Suporte kernel para a montagem de sistemas de ficheiros UDF.

## <a name="installation"></a>Instalação
A instalação utilizando um pacote RPM ou um PACOTE DEB do repositório de pacotes da sua distribuição é o método preferido para instalar e atualizar o Agente Azure Linux. Todos os fornecedores de [distribuição endossados](../linux/endorsed-distros.md) integram o pacote de agentes Azure Linux nas suas imagens e repositórios.

Consulte a documentação no representante do [Agente Azure Linux no GitHub](https://github.com/Azure/WALinuxAgent) para opções avançadas de instalação, tais como a instalação a partir de fonte ou para locais ou prefixos personalizados.

## <a name="command-line-options"></a>Opções de linha de comando
### <a name="flags"></a>Sinalizadores
* verboso: Aumentar a verbosidade do comando especificado
* força: Saltar a confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos
* ajuda: Lista os comandos e bandeiras suportados.
* disprovisionamento: Tente limpar o sistema e torná-lo adequado para o reprovisionamento. A seguinte operação elimina:
  
  * Todas as teclas hospedeiras sSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no ficheiro de configuração)
  * Configuração do servidor de nomes em /etc/resolve.conf
  * Palavra-passe de raiz de /etc/sombra (se provisionamento.DeleteRootPassword é 'y' no ficheiro de configuração)
  * Arrendamentos de clientes DHCP em cache
  * Repõe o nome do anfitrião para localhost.localdomain

> [!WARNING]
> A desprovisionamento não garante que a imagem seja desmarcada de todas as informações sensíveis e adequadas para redistribuição.
> 
> 

* deprovisionamento+utilizador: Executa tudo em -deprovisionamento (acima) e também elimina a última conta de utilizador provisionada (obtida a partir de /var/lib/waagent) e dados associados. Este parâmetro é quando desaprovisiona uma imagem que previamente aprovisionava em Azure para que pudesse ser capturada e reutilizada.
* versão: Exibe a versão do waagent
* serialconsole: Configures GRUB para marcar ttyS0 (a primeira porta série) como a consola de arranque. Isto garante que os registos de arranque de kernel são enviados para a porta de série e disponibilizados para depuração.
* daemon: Executar waagent como um daemon para gerir a interação com a plataforma. Este argumento é especificado para waagent no roteiro init waagent.
* início: Executar waagent como um processo de fundo

## <a name="configuration"></a>Configuração
Um ficheiro de configuração (/etc/waagent.conf) controla as ações do agente. O seguinte mostra um ficheiro de configuração de amostra:

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

São descritas as seguintes várias opções de configuração. As opções de configuração são de três tipos; Boolean, String ou Integer. As opções de configuração booleanas podem ser especificadas como "y" ou "n". A palavra-chave especial "Nenhuma" pode ser utilizada para algumas entradas de configuração do tipo de corda como os seguintes detalhes:

**Provisionamento.Habilitado:**  
```
Type: Boolean  
Default: y
```
Isto permite ao utilizador ativar ou desativar a funcionalidade de provisionamento no agente. Os valores válidos são "y" ou "n". Se o fornecimento estiver desativado, o hospedeiro SSH e as chaves do utilizador na imagem são preservados e qualquer configuração especificada na API de fornecimento Azure é ignorada.

> [!NOTE]
> O `Provisioning.Enabled` parâmetro falha em "n" em Ubuntu Cloud Images que usam cloud-init para o provisionamento.
> 
> 

**Provisionamento.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Se definido, a palavra-passe de raiz no ficheiro /etc/sombra é apagada durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Se definido, todos os pares de chaves de hospedeiro SSH (ecdsa, dsa e rsa) são eliminados durante o processo de provisionamento a partir de /etc/ssh/. E um único par de chaves frescas é gerado.

O tipo de encriptação do novo par de chaves é configurável pela entrada Provisioning.SshHostKeyPairType. Algumas distribuições recriam pares de chaves SSH para quaisquer tipos de encriptação em falta quando o daemon SSH é reiniciado (por exemplo, após um reboot).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Isto pode ser definido para um tipo de algoritmo de encriptação que é suportado pelo daemon SSH na máquina virtual. Os valores tipicamente apoiados são "rsa", "dsa" e "ecdsa". "putty.exe" no Windows não suporta "ecdsa". Assim, se pretender utilizar putty.exe no Windows para se ligar a uma implementação do Linux, utilize "rsa" ou "dsa".

**Provisionamento.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Se definido, o waagent monitoriza a máquina virtual Linux para alterações no nome do anfitrião (conforme devolvido pelo comando "nome de anfitrião" ) e atualiza automaticamente a configuração de rede na imagem para refletir a alteração. Para empurrar a mudança de nome para os servidores DNS, a rede é reiniciada na máquina virtual. Isto resulta numa breve perda da conectividade da Internet.

**Provisionamento.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Se estiver definido, o waagent descodifica o CustomData a partir do Base64.

**Provisionamento.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Se definido, o waagent executa o CustomData após o fornecimento.

**Provisionamento.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Esta opção permite que a palavra-passe para o utilizador sys seja redefinida; predefinição é desativado.

**Provisionamento.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritmo usado pela cripta ao gerar hash de senha.  
 1 - MD5  
 2a - Peixe-balão  
 5 - SHA-256  
 6 - SHA-512  

**Provisionamento.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Comprimento do sal aleatório utilizado ao gerar hash de senha.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Se definido, o disco de recursos fornecido pela plataforma for formatado e montado por waagent se o tipo de sistema de ficheiros solicitado pelo utilizador em "ResourceDisk.Filesystem" for outra coisa que não "ntfs". Uma única partição do tipo Linux (83) é disponibilizada no disco. Esta partição não é formatada se puder ser montada com sucesso.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Isto especifica o tipo de sistema de ficheiros para o disco de recursos. Os valores suportados variam em função da distribuição do Linux. Se a corda é X, então mkfs. X deve estar presente na imagem linux. As imagens SLES 11 devem normalmente usar 'ext3'. As imagens FreeBSD devem usar 'ufs2' aqui.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Isto especifica o caminho em que o disco de recursos é montado. O disco de recursos é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado.

**Resourcedisk.MountOptions**  
```
Type: String  
Default: None
```
Especifica as opções de montagem do disco a passar para o comando de montagem-o. Esta é uma lista separada de valores, ex. "nodev, nosuid". Consulte o monte (8) para obter mais detalhes.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Se definido, um ficheiro de permuta (/swapfile) é criado no disco de recursos e adicionado ao espaço de permuta do sistema.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
O tamanho do ficheiro de troca em megabytes.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Se estiver definido, a verbosidade do registo é aumentada. O Waagent regista para /var/log/waagent.log e utiliza a funcionalidade de logrotatdo do sistema para rodar os registos.

**OS. EnableRDMA**  
```
Type: Boolean  
Default: n
```
Se estiver definido, o agente tenta instalar e, em seguida, carregar um controlador de kernel RDMA que corresponda à versão do firmware no hardware subjacente.

**OS. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Esta definição configura o tempo de tempo SCSI em segundos no disco OS e unidades de dados. Se não estiver definido, são utilizadas as predefinições do sistema.

**OS. OpensslPath:**  
```
Type: String  
Default: None
```
Esta definição pode ser utilizada para especificar um caminho alternativo para o binário aberto a utilizar para operações criptográficas.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Se definido, o agente utiliza este servidor proxy para aceder à internet. 

**AutoUpdate.Ativado**
```
Type: Boolean
Default: y
```
Ativar ou desativar a atualização automática para o processamento do estado objetivo; predefinição está ativada.



## <a name="ubuntu-cloud-images"></a>Imagens da nuvem de Ubuntu
A Ubuntu Cloud Images utiliza o [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) para executar muitas tarefas de configuração que de outra forma seriam geridas pelo Agente Azure Linux. Aplicam-se as seguintes diferenças:

* **Provisionamento.Habilitar os** incumprimentos para "n" em Ubuntu Cloud Images que usam cloud-init para executar tarefas de provisionamento.
* Os seguintes parâmetros de configuração não têm qualquer efeito nas Imagens cloud ubuntu que usam cloud-init para gerir o disco de recursos e trocar espaço:
  
  * **ResourceDisk.Formato**
  * **ResourceDisk.Filesystem**
  * **Resourcedisk.MountPoint**
  * **Resourcedisk.Enableswap**
  * **Resourcedisk.SwapSizeMB**

* Para mais informações, consulte os seguintes recursos para configurar o ponto de montagem do disco de recursos e trocar espaço nas Imagens cloud ubuntu durante o fornecimento:
  
  * [Ubuntu Wiki: Configure Swap Partitions](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injetar dados personalizados numa máquina virtual azure](../windows/classic/inject-custom-data.md)

