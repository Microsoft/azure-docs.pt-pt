---
title: Visão geral do agente Azure Linux VM
description: Aprenda a instalar e configurar o Agente Linux (waagent) para gerir a interação da sua máquina virtual com o Controlador de TecidoS Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 10/17/2016
ms.openlocfilehash: e8851ddd5211536394614727d990a2b52d32bfcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565382"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Compreensão e utilização do Agente Azure Linux

O Agente Linux (waagent) da Microsoft Azure gere o fornecimento de & Livre e a interação VM com o Controlador de Tecidos Azure. Além do Agente Linux fornecer funcionalidade de provisionamento, o Azure também oferece a opção de usar cloud-init para alguns Linux OSes. O Agente Linux fornece a seguinte funcionalidade para implementações de IaaS Linux e FreeBSD:

> [!NOTE]
> Para mais informações, consulte a [README.](https://github.com/Azure/WALinuxAgent/blob/master/README.md)
> 
> 

* **Provisão de imagem**
  
  * Criação de uma conta de utilizador
  * Configurar tipos de autenticação SSH
  * Implantação de chaves públicas SSH e pares de chaves
  * Definição do nome do anfitrião
  * Publicar o nome de anfitrião para a plataforma DNS
  * Relatório SSH anfitrião chave impressão digital chave para a plataforma
  * Gestão de Discos de Recursos
  * Formatação e montagem do disco de recursos
  * Espaço de troca de configuração
* **Rede**
  
  * Gere rotas para melhorar a compatibilidade com os servidores DHCP da plataforma
  * Garante a estabilidade do nome da interface de rede
* **Kernel**
  
  * Configura UMA virtual (desativação para <de kernel `2.6.37` )
  * Consome entropia Hiper-V para /dev/random
  * Configura os intervalos de tempo SCSI para o dispositivo radicial (que pode ser remoto)
* **Diagnóstico**
  
  * Redirecionamento da consola para a porta em série
* **Implementações SCVMM**
  
  * Deteta e arranca o agente VMM para Linux ao correr num ambiente de R2 do System Center Virtual Machine Manager 2012
* **Extensão VM**
  
  * Componente injetado da microsoft e parceiros no Linux VM (IaaS) para permitir automatização de software e configuração
  * Implementação de referência de extensão VM [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicação
O fluxo de informação da plataforma para o agente ocorre através de dois canais:

* Um DVD anexado para implantações iaaS. Este DVD inclui um ficheiro de configuração compatível com OVF que inclui todas as informações de fornecimento que não os elementos-chave SSH reais.
* Um ponto final TCP expondo uma API REST usada para obter configuração de implantação e topologia.

## <a name="requirements"></a>Requisitos
Os seguintes sistemas foram testados e são conhecidos por trabalhar com o Agente Azure Linux:

> [!NOTE]
> Esta lista pode diferir da lista oficial de [distros suportados](../linux/endorsed-distros.md).
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Outros sistemas suportados:

* FreeBSD 10+ (Agente Azure Linux v2.0.10+)

O agente Linux depende de alguns pacotes do sistema para funcionar corretamente:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Utilitários de sistema de ficheiros: sfdisk, fdisk, mkfs, partedos
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: via ip
* Suporte kernel para a montagem de sistemas de ficheiros UDF.

Confirme que a VM tem acesso ao endereço IP 168.63.129.16. Para mais informações, consulte [o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="installation"></a>Instalação
A instalação utilizando um RPM ou uma embalagem DEB do repositório de pacotes da sua distribuição é o método preferido para instalar e atualizar o Agente Azure Linux. Todos os [fornecedores de distribuição endossados](../linux/endorsed-distros.md) integram o pacote de agente Azure Linux nas suas imagens e repositórios.

Consulte a documentação no repo do [Agente Azure Linux no GitHub](https://github.com/Azure/WALinuxAgent) para opções avançadas de instalação, tais como instalar a partir de fonte ou para locais ou prefixos personalizados.

## <a name="command-line-options"></a>Opções Command-Line
### <a name="flags"></a>Sinalizadores
* verbose: Aumentar a verbosidade do comando especificado
* força: Saltar a confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos
* ajuda: Lista os comandos e bandeiras suportados.
* desprovisionamento: Tente limpar o sistema e torná-lo adequado para a reprovisionamento. A seguinte operação elimina:
  
  * Todas as teclas de anfitrião SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no ficheiro de configuração)
  * Configuração nameserver em /etc/resolve.conf
  * Palavra-passe raiz de /etc/sombra (se Provisioning.DeleteRootPassword é 'y' no ficheiro de configuração)
  * Arrendamentos de clientes dhcp em cache
  * Repõe o nome do hospedeiro para localhost.localdomain

> [!WARNING]
> A desprovisionamento não garante que a imagem seja limpa de toda a informação sensível e adequada para redistribuição.
> 
> 

* deprovision+utilizador: Executa tudo em -deprovision (acima) e também elimina a última conta de utilizador aprovisionada (obtida a partir de /var/lib/waagent) e dados associados. Este parâmetro é quando desa provisionando uma imagem que estava previamente a provisionar em Azure para que possa ser capturada e reutilizada.
* versão: Exibe a versão do waagent
* serialconsole: Configura o GRUB para marcar o ttyS0 (a primeira porta em série) como a consola de arranque. Isto garante que os registos de arranque de kernel são enviados para a porta de série e disponibilizados para depurar.
* Daemon: Corra waagent como um daemon para gerir a interação com a plataforma. Este argumento é especificado para waagent no roteiro waagent init.
* início: Executar waagent como um processo de fundo

## <a name="configuration"></a>Configuração
Um ficheiro de configuração (/etc/waagent.conf) controla as ações do waagent. O seguinte mostra um ficheiro de configuração da amostra:

```config
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

São descritas as seguintes opções de configuração. As opções de configuração são de três tipos; Boolean, String ou Inteiro. As opções de configuração booleanas podem ser especificadas como "y" ou "n". A palavra-chave especial "Nenhuma" pode ser utilizada para algumas entradas de configuração do tipo de corda como os seguintes detalhes:

**Provisioning.Habilitado:**  
```txt
Type: Boolean  
Default: y
```
Isto permite ao utilizador ativar ou desativar a funcionalidade de provisionamento no agente. Valores válidos são "y" ou "n". Se o provisionamento for desativado, o hospedeiro SSH e as chaves do utilizador na imagem são preservadas e qualquer configuração especificada na API de provisionamento Azure é ignorada.

> [!NOTE]
> O `Provisioning.Enabled` parâmetro predefine para "n" em Ubuntu Cloud Images que usam cloud-init para provisões.
> 
> 

**Provisioning.DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Se for definida, a palavra-passe de raiz no ficheiro /etc/sombra é apagada durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Se estiver definido, todos os pares de chaves de anfitrião SSH (ecdsa, dsa e rsa) são eliminados durante o processo de provisionamento de /etc/ssh/. E um único par de chaves fresco é gerado.

O tipo de encriptação para o par de chaves frescos é configurável pela entrada Provisioning.SshHostKeyPairType. Algumas distribuições recriam os pares de chaves SSH para quaisquer tipos de encriptação em falta quando o daemon SSH é reiniciado (por exemplo, após um reboot).

**Provisioning.SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
Isto pode ser definido para um tipo de algoritmo de encriptação que é suportado pelo daemon SSH na máquina virtual. Os valores tipicamente suportados são "rsa", "dsa" e "ecdsa". "putty.exe" no Windows não suporta "ecdsa". Por isso, se pretender utilizar putty.exe no Windows para se ligar a uma implementação Linux, utilize "rsa" ou "dsa".

**Provisioning.MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Se estiver definido, o waagent monitoriza a máquina virtual Linux para alterações no nome hospedeiro (como devolvido pelo comando "nome de anfitrião") e atualiza automaticamente a configuração de rede na imagem para refletir a alteração. Para empurrar a alteração de nome para os servidores DNS, a rede é reiniciada na máquina virtual. Isto resulta numa breve perda de conectividade na Internet.

**Provisioning.DecodeCustomData**  
```txt
Type: Boolean  
Default: n
```
Se definido, waagent descodifica CustomData da Base64.

**Provisioning.ExebonitoCustomData**  
```txt
Type: Boolean  
Default: n
```
Se definido, waagent executa CustomData após provisão.

**Provisioning.AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Esta opção permite que a palavra-passe para o utilizador sys seja reposta; o padrão é desativado.

**Provisioning.PasswordCryptId**  
```txt
Type: String  
Default: 6
```
Algoritmo usado pela cript ao gerar haxixe de senha.  
 1 - MD5  
 2a - Peixe-balão  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
Comprimento do sal aleatório utilizado ao gerar haxixe de senha.

**ResourceDisk.Formato:**  
```txt
Type: Boolean  
Default: y
```
Se for definido, o disco de recursos fornecido pela plataforma é formatado e montado por waagent se o tipo de sistema de ficheiros solicitado pelo utilizador em "ResourceDisk.Filesystem" for outra coisa que não "ntfs". Uma única partição do tipo Linux (83) é disponibilizada no disco. Esta partição não é formatada se puder ser montada com sucesso.

**ResourceDisk.Filesystem:**  
```txt
Type: String  
Default: ext4
```
Isto especifica o tipo de sistema de ficheiros para o disco de recursos. Os valores suportados variam consoante a distribuição do Linux. Se a corda é X, então mkfs. X deve estar presente na imagem linux. As imagens SLES 11 devem normalmente utilizar 'ext3'. As imagens FreeBSD devem utilizar 'ufs2' aqui.

**ResourceDisk.MountPoint:**  
```txt
Type: String  
Default: /mnt/resource 
```
Isto especifica o caminho em que o disco de recursos é montado. O disco de recursos é um disco *temporário* e pode ser esvaziado quando o VM é desprovisionado.

**RecursosDisk.MountOptions**  
```txt
Type: String  
Default: None
```
Especifica as opções de montagem do disco a passar para o comando montagem -o. Esta é uma lista de valores separada por vírgula, ex. "nódev, nosuid". Consulte o suporte(8) para mais detalhes.

**ResourceDisk.EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Se for definido, é criado um ficheiro swap (/swapfile) no disco de recursos e adicionado ao espaço de troca do sistema.

**ResourceDisk.SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
O tamanho do ficheiro de troca em megabytes.

**Logs.Verbose:**  
```txt
Type: Boolean  
Default: n
```
Se estiver definido, a verbosidade do log é reforçada. Os registos Waagent para /var/log/waagent.log e utiliza a funcionalidade logrotate do sistema para rodar registos.

**OS. Ativar oRDMA**  
```txt
Type: Boolean  
Default: n
```
Se estiver definido, o agente tenta instalar e, em seguida, carregar um controlador de kernel RDMA que corresponda à versão do firmware no hardware subjacente.

**OS. RootDeviceSiTimeout:**  
```txt
Type: Integer  
Default: 300
```
Esta definição configura o intervalo de tempo SCSI em segundos no disco de so e unidades de dados. Se não estiver definido, os predefinidos do sistema são utilizados.

**OS. OpensslPath:**  
```txt
Type: String  
Default: None
```
Esta definição pode ser usada para especificar um caminho alternativo para o binário openssl para usar para operações criptográficas.

**HttpProxy.Host, HttpProxy.Port**  
```txt
Type: String  
Default: None
```
Se for definido, o agente utiliza este servidor proxy para aceder à internet. 

**AutoUpdate.Habilidoso**
```txt
Type: Boolean
Default: y
```
Ative ou desative a atualização automática para o processamento do estado de objetivo; o padrão está ativado.



## <a name="ubuntu-cloud-images"></a>Imagens da nuvem de Ubuntu
Ubuntu Cloud Images utiliza [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) para executar muitas tarefas de configuração que de outra forma seriam geridas pelo Agente Azure Linux. Aplicam-se as seguintes diferenças:

* **Provisioning.Habilitado** para "n" em Ubuntu Cloud Images que usam cloud-init para executar tarefas de provisionamento.
* Os seguintes parâmetros de configuração não têm qualquer efeito nas Imagens cloud Ubuntu que usam a cloud-init para gerir o disco de recursos e trocar o espaço:
  
  * **ResourceDisk.Formato**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Para obter mais informações, consulte os seguintes recursos para configurar o ponto de montagem do disco de recursos e trocar espaço em Ubuntu Cloud Images durante o provisionamento:
  
  * [Ubuntu Wiki: Configure Swap Partitions](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injetar dados personalizados numa máquina virtual Azure](../windows/tutorial-automate-vm-deployment.md)