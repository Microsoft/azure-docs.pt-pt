---
title: Visão geral do agente de VM Linux do Azure | Microsoft Docs
description: Saiba como instalar e configurar o agente do Linux (waagent) para gerenciar a interação da sua máquina virtual com o controlador de malha do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acb6e14845beb4c947992e63f1984c072ba9f59f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084827"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Compreendendo e usando o agente Linux do Azure

O agente do Microsoft Azure Linux (waagent) gerencia o provisionamento do Linux & FreeBSD e a interação da VM com o controlador de malha do Azure. Além do agente do Linux que fornece a funcionalidade de provisionamento, o Azure também fornece a opção de usar o Cloud-init para alguns sistemas operacionais Linux. O agente do Linux fornece a seguinte funcionalidade para implantações IaaS do Linux e FreeBSD:

> [!NOTE]
> Para obter mais informações, consulte [](https://github.com/Azure/WALinuxAgent/blob/master/README.md)o Leiame.
> 
> 

* **Provisionamento de imagem**
  
  * Criação de uma conta de usuário
  * Configurando tipos de autenticação SSH
  * Implantação de chaves públicas SSH e pares de chaves
  * Configurando o nome do host
  * Publicando o nome do host no DNS da plataforma
  * Relatando a impressão digital da chave de host SSH para a plataforma
  * Gerenciamento de disco de recursos
  * Formatando e montando o disco de recursos
  * Configurando o espaço de permuta
* **Redes**
  
  * Gerencia rotas para melhorar a compatibilidade com servidores DHCP de plataforma
  * Garante a estabilidade do nome da interface de rede
* **Kernel**
  
  * Configura NUMA virtual (Disable for kernel <`2.6.37`)
  * Consome a entropia do Hyper-V para/dev/random
  * Configura os tempos limite de SCSI para o dispositivo raiz (que pode ser remoto)
* **Diagnóstico**
  
  * Redirecionamento de console para a porta serial
* **Implantações do SCVMM**
  
  * Detecta e inicializa o agente do VMM para Linux quando executado em um ambiente System Center Virtual Machine Manager 2012 R2
* **Extensão de VM**
  
  * Injetar componente criado pela Microsoft e por parceiros na VM do Linux (IaaS) para habilitar a automação de software e configuração
  * Implementação de referência de extensão de VM em[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicação
O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um DVD anexado em tempo de inicialização para implantações de IaaS. Esse DVD inclui um arquivo de configuração compatível com OVF que inclui todas as informações de provisionamento que não sejam os pares de chaves SSH reais.
* Um ponto de extremidade TCP expondo uma API REST usada para obter a implantação e a configuração de topologia.

## <a name="requirements"></a>Requisitos
Os sistemas a seguir foram testados e são conhecidos por trabalhar com o agente Linux do Azure:

> [!NOTE]
> Essa lista pode ser diferente da lista oficial de sistemas com suporte na plataforma Microsoft Azure, conforme descrito aqui:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3+
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Outros sistemas com suporte:

* FreeBSD 10 + (agente Linux do Azure v 2.0.10 +)

O agente do Linux depende de alguns pacotes do sistema para funcionar corretamente:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Utilitários FileSystem: sfdisk, fdisk, mkfs, parte
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: IP-rota
* Suporte de kernel para montagem de sistemas de fileudf.

## <a name="installation"></a>Instalação
A instalação usando um RPM ou um pacote de DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o agente Linux do Azure. Todos os [provedores de distribuição endossados](../linux/endorsed-distros.md) integram o pacote do agente Linux do Azure em suas imagens e repositórios.

Consulte a documentação no repositório do [agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent) para obter opções de instalação avançada, como instalar de origem ou de locais ou prefixos personalizados.

## <a name="command-line-options"></a>Opções de linha de comando
### <a name="flags"></a>Sinalizadores
* extensa Aumentar o detalhamento do comando especificado
* aplicação Ignorar a confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos
* Ajuda: Lista os comandos e sinalizadores com suporte.
* desprovisionar Tente limpar o sistema e torná-lo adequado para reprovisionamento. A seguinte operação exclui:
  
  * Todas as chaves de host SSH (se o provisionamento. RegenerateSshHostKeyPair for ' y ' no arquivo de configuração)
  * Configuração de nameserver em/etc/resolv.conf
  * A senha raiz de/etc/shadow (se provisione. DeleteRootPassword for ' y ' no arquivo de configuração)
  * Concessões de cliente DHCP em cache
  * Repõe o nome do anfitrião para localhost.localdomain

> [!WARNING]
> O desprovisionamento não garante que a imagem seja desmarcada de todas as informações confidenciais e adequada para redistribuição.
> 
> 

* desprovisionar + usuário: Executa tudo em-desprovision (acima) e também exclui a última conta de usuário provisionada (Obtida de/var/lib/waagent) e dados associados. Esse parâmetro é ao desprovisionar uma imagem que foi provisionada anteriormente no Azure para que possa ser capturada e reutilizada.
* Versão: Exibe a versão do waagent
* serialconsole: Configura o GRUB para marcar ttyS0 (a primeira porta serial) como o console de inicialização. Isso garante que os logs de inicialização do kernel sejam enviados para a porta serial e disponibilizados para depuração.
* demonstração Execute waagent como um daemon para gerenciar a interação com a plataforma. Esse argumento é especificado para waagent no script de inicialização waagent.
* Comece Executar waagent como um processo em segundo plano

## <a name="configuration"></a>Configuração
Um arquivo de configuração (/etc/waagent.conf) controla as ações de waagent. O seguinte mostra um exemplo de arquivo de configuração:

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

As várias opções de configuração a seguir são descritas. As opções de configuração são de três tipos; Booliano, Cadeia de caracteres ou inteiro. As opções de configuração boolianas podem ser especificadas como "y" ou "n". A palavra-chave especial "None" pode ser usada para algumas entradas de configuração de tipo de cadeia de caracteres como os seguintes detalhes:

**Provisionamento. habilitado:**  
```
Type: Boolean  
Default: y
```
Isso permite que o usuário habilite ou desabilite a funcionalidade de provisionamento no agente. Os valores válidos são "y" ou "n". Se o provisionamento estiver desabilitado, as chaves de usuário e o host SSH na imagem serão preservados e qualquer configuração especificada na API de provisionamento do Azure será ignorada.

> [!NOTE]
> O `Provisioning.Enabled` parâmetro assume como padrão "n" imagens de nuvem do Ubuntu que usam Cloud-init para provisionamento.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Se definido, a senha raiz no arquivo/etc/shadow será apagada durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Se definido, todos os pares de chaves de host SSH (ECDSA, DSA e RSA) serão excluídos durante o processo de provisionamento do/etc/ssh/. E um único par de chaves é gerado.

O tipo de criptografia para o par de chaves atualizado é configurável pela entrada Provisioning. SshHostKeyPairType. Algumas distribuições recriam pares de chaves SSH para quaisquer tipos de criptografia ausentes quando o daemon SSH é reiniciado (por exemplo, após uma reinicialização).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Isso pode ser definido como um tipo de algoritmo de criptografia com suporte pelo daemon SSH na máquina virtual. Os valores normalmente com suporte são "RSA", "DSA" e "ECDSA". o ". exe" no Windows não oferece suporte a "ECDSA". Portanto, se você pretende usar o. exe no Windows para se conectar a uma implantação do Linux, use "RSA" ou "DSA".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Se definido, waagent monitorará a máquina virtual Linux em busca de alterações de nome de host (como retornado pelo comando "hostname") e atualizará automaticamente a configuração de rede na imagem para refletir a alteração. Para enviar por push a alteração de nome para os servidores DNS, a rede é reiniciada na máquina virtual. Isso resulta em uma breve perda de conectividade com a Internet.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Se definido, waagent decodifica CustomData a partir de Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Se definido, waagent executará CustomData após o provisionamento.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Essa opção permite que a senha do usuário sys seja redefinida; o padrão é desabilitado.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritmo usado por cript. ao gerar o hash de senha.  
 1 - MD5  
 2a-Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Comprimento de Salt aleatório usado ao gerar o hash de senha.

**ResourceDisk. Format:**  
```
Type: Boolean  
Default: y
```
Se definido, o disco de recursos fornecido pela plataforma será formatado e montado pelo waagent se o tipo de sistema de arquivos solicitado pelo usuário em "ResourceDisk. FileSystem" for algo diferente de "NTFS". Uma única partição do tipo Linux (83) é disponibilizada no disco. Essa partição não será formatada se puder ser montada com êxito.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Isso especifica o tipo de sistema de arquivos para o disco de recursos. Os valores com suporte variam de acordo com a distribuição do Linux. Se a cadeia de caracteres for X, então mkfs. X deve estar presente na imagem do Linux. As imagens do SLES 11 normalmente devem usar ' ext3 '. As imagens do FreeBSD devem usar ' UFS2 ' aqui.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Isso especifica o caminho no qual o disco de recursos é montado. O disco de recursos é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Especifica as opções de montagem de disco a serem passadas para o comando Mount-o. Essa é uma lista de valores separados por vírgula, por exemplo, 'nodev,nosuid'. Consulte Mount (8) para obter detalhes.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Se definido, um arquivo de permuta (/Swapfile) é criado no disco de recursos e adicionado ao espaço de permuta do sistema.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
O tamanho do arquivo de permuta em megabytes.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Se definido, o detalhamento de log será aumentado. Waagent registra em/var/log/waagent.log e utiliza a funcionalidade de logrotate do sistema para girar os logs.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Se definido, o agente tenta instalar e, em seguida, carregar um driver de kernel RDMA que corresponde à versão do firmware no hardware subjacente.

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Essa configuração define o tempo limite de SCSI em segundos no disco do sistema operacional e nas unidades de dados. Se não estiver definido, os padrões do sistema serão usados.

**Sistemas. OpensslPath:**  
```
Type: String  
Default: None
```
Essa configuração pode ser usada para especificar um caminho alternativo para o OpenSSL Binary a ser usado para operações criptográficas.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Se definido, o agente usará esse servidor proxy para acessar a Internet. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Habilitar ou desabilitar a atualização automática para o processamento do estado de meta; o padrão é habilitado.



## <a name="ubuntu-cloud-images"></a>Imagens de nuvem do Ubuntu
As imagens de nuvem do Ubuntu utilizam [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) para executar muitas tarefas de configuração que, de outra forma, seriam gerenciadas pelo agente Linux do Azure. As seguintes diferenças se aplicam:

* **Provisionamento.** os padrões habilitados para "n" em imagens de nuvem do Ubuntu que usam Cloud-init para executar tarefas de provisionamento.
* Os parâmetros de configuração a seguir não têm nenhum efeito nas imagens de nuvem do Ubuntu que usam Cloud-init para gerenciar o disco de recursos e o espaço de permuta:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Para obter mais informações, consulte os seguintes recursos para configurar o ponto de montagem de disco do recurso e o espaço de permuta nas imagens de nuvem do Ubuntu durante o provisionamento:
  
  * [Ubuntu Wiki: Configurar partições de permuta](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injetando dados personalizados em uma máquina virtual do Azure](../windows/classic/inject-custom-data.md)

