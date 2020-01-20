---
title: Configurar o MPIO no host Linux do StorSimple
description: Configurar o MPIO no StorSimple conectado a um host Linux executando o CentOS 6,6
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278368"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Configurar o MPIO em um host StorSimple que executa o CentOS
Este artigo explica as etapas necessárias para configurar a e/s de vários caminhos (MPIO) no servidor host CentOS 6,6. O servidor host está conectado ao seu dispositivo de Microsoft Azure StorSimple para alta disponibilidade por meio de iniciadores iSCSI. Ele descreve detalhadamente a descoberta automática de dispositivos de vários caminhos e a configuração específica somente para volumes do StorSimple.

Esse procedimento é aplicável a todos os modelos de dispositivos da série StorSimple 8000.

> [!NOTE]
> Este procedimento não pode ser usado para um dispositivo de nuvem StorSimple. Para obter mais informações, consulte como configurar servidores de host para seu dispositivo de nuvem.


## <a name="about-multipathing"></a>Sobre vários caminhos
O recurso de vários caminhos permite configurar múltiplos caminhos de e/s entre um servidor de host e um dispositivo de armazenamento. Esses caminhos de e/s são conexões físicas de SAN que podem incluir cabos, comutadores, interfaces de rede e controladores separados. Múltiplos caminhos agrega os caminhos de e/s, para configurar um novo dispositivo associado a todos os caminhos agregados.

A finalidade de vários caminhos é de duas dobras:

* **Alta disponibilidade**: fornece um caminho alternativo se qualquer elemento do caminho de e/s (como um cabo, comutador, interface de rede ou controlador) falhar.
* **Balanceamento de carga**: dependendo da configuração do seu dispositivo de armazenamento, ele pode melhorar o desempenho detectando cargas nos caminhos de e/s e rebalanceando dinamicamente essas cargas.

### <a name="about-multipathing-components"></a>Sobre os componentes de vários caminhos
Vários caminhos no Linux consistem em componentes de kernel e componentes de espaço do usuário, conforme tabulado abaixo.

* **Kernel**: o componente principal é o *Device-Mapper* que redireciona a e/s e dá suporte a failover para caminhos e grupos de caminho.

* **Espaço de usuário**: são *ferramentas de vários caminhos* que gerenciam dispositivos com vários caminhos instruindo o módulo de vários caminhos do mapeador do dispositivo o que fazer. As ferramentas consistem em:
   
   * **Vários caminhos**: lista e configura os dispositivos com vários caminhos.
   * **Vários caminhos**: daemon que executa múltiplos caminhos e monitora os caminhos.
   * **Devmap-Name**: fornece um nome de dispositivo significativo para udev para devmaps.
   * **Kpartx**: mapeia devmaps linear para partições de dispositivo para tornar os mapas de vários caminhos particionados.
   * **Vários Path. conf**: arquivo de configuração para o daemon de vários caminhos que é usado para substituir a tabela de configuração interna.

### <a name="about-the-multipathconf-configuration-file"></a>Sobre o arquivo de configuração de vários caminhos. conf
O arquivo de configuração `/etc/multipath.conf` torna muitos dos recursos de vários caminhos configuráveis pelo usuário. O comando `multipath` e o daemon do kernel `multipathd` usam as informações encontradas neste arquivo. O arquivo é consultado somente durante a configuração dos dispositivos de vários caminhos. Certifique-se de que todas as alterações sejam feitas antes de executar o comando `multipath`. Se você modificar o arquivo posteriormente, será necessário parar e iniciar vários caminhos novamente para que as alterações entrem em vigor.

O webpath. conf tem cinco seções:

- **Padrões de nível do sistema** *(padrões)* : você pode substituir os padrões de nível do sistema.
- **Dispositivos blacklists** *(Blacklist)* : você pode especificar a lista de dispositivos que não devem ser controlados pelo Device-Mapper.
- **Exceções de lista negra** *(blacklist_exceptions)* : você pode identificar dispositivos específicos a serem tratados como dispositivos de vários caminhos, mesmo se listados na lista de bloqueios.
- **Configurações específicas do controlador de armazenamento** *(dispositivos)* : você pode especificar as definições de configuração que serão aplicadas a dispositivos que têm informações de fornecedor e produto.
- **Configurações específicas do dispositivo** *(vários caminhos)* : você pode usar esta seção para ajustar as definições de configuração para LUNs individuais.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Configurar vários caminhos no StorSimple conectado ao host do Linux
Um dispositivo StorSimple conectado a um host Linux pode ser configurado para alta disponibilidade e balanceamento de carga. Por exemplo, se o host do Linux tiver duas interfaces conectadas à SAN e o dispositivo tiver duas interfaces conectadas à SAN, de modo que essas interfaces estejam na mesma sub-rede, haverá quatro caminhos disponíveis. No entanto, se cada interface de dados na interface do dispositivo e do host estiverem em uma sub-rede IP diferente (e não roteável), somente dois caminhos estarão disponíveis. Você pode configurar múltiplos caminhos para descobrir automaticamente todos os caminhos disponíveis, escolher um algoritmo de balanceamento de carga para esses caminhos, aplicar definições de configuração específicas para volumes somente StorSimple e, em seguida, habilitar e verificar vários caminhos.

O procedimento a seguir descreve como configurar vários caminhos quando um dispositivo StorSimple com duas interfaces de rede está conectado a um host com duas interfaces de rede.

## <a name="prerequisites"></a>Pré-requisitos
Esta seção detalha os pré-requisitos de configuração para o servidor CentOS e seu dispositivo StorSimple.

### <a name="on-centos-host"></a>No host CentOS
1. Verifique se o host CentOS tem duas interfaces de rede habilitadas. Tipo:
   
    `ifconfig`
   
    O exemplo a seguir mostra a saída quando duas interfaces de rede (`eth0` e `eth1`) estão presentes no host.
   
        [root@centosSS ~]# ifconfig
        eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
          inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
         RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
   
        eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
          inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
          inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
          inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
          TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
   
        loLink encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
1. Instale o *iSCSI-Initiator-utils* no servidor CentOS. Execute as etapas a seguir para instalar o *iSCSI-Initiator-utils*.
   
   1. Faça logon como `root` em seu host CentOS.
   1. Instale o *iSCSI-Initiator-utils*. Tipo:
      
       `yum install iscsi-initiator-utils`
   1. Depois que o *iSCSI-Initiator-utils* for instalado com êxito, inicie o serviço iSCSI. Tipo:
      
       `service iscsid start`
      
       Em ocasiões, `iscsid` pode não iniciar de fato e a opção de `--force` pode ser necessária
   1. Para garantir que o iniciador iSCSI esteja habilitado durante o tempo de inicialização, use o comando `chkconfig` para habilitar o serviço.
      
       `chkconfig iscsi on`
   1. Para verificar se ele foi configurado corretamente, execute o comando:
      
       `chkconfig --list | grep iscsi`
      
       É apresentada abaixo uma saída de exemplo.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       No exemplo acima, você pode ver que o ambiente iSCSI será executado no momento da inicialização nos níveis de execução 2, 3, 4 e 5.
1. Instalar o *Device-Mapper-vários caminhos*. Tipo:
   
    `yum install device-mapper-multipath`
   
    A instalação será iniciada. Digite **Y** para continuar quando a confirmação for solicitada.

### <a name="on-storsimple-device"></a>No dispositivo StorSimple
O dispositivo StorSimple deve ter:

* Um mínimo de duas interfaces habilitadas para iSCSI. Para verificar se duas interfaces estão habilitadas para iSCSI em seu dispositivo StorSimple, execute as seguintes etapas no portal clássico do Azure para seu dispositivo StorSimple:
  
  1. Faça logon no portal clássico para seu dispositivo StorSimple.
  1. Selecione seu serviço de StorSimple Manager, clique em **dispositivos** e escolha o dispositivo StorSimple específico. Clique em **Configurar** e verifique as configurações de interface de rede. Uma captura de tela com duas interfaces de rede habilitadas para iSCSI é mostrada abaixo. Aqui, DATA 2 e DATA 3, ambas as interfaces de 10 GbE estão habilitadas para iSCSI.
     
      ![Configuração do MPIO StorsSimple DATA 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![Configuração do MPIO StorSimple DATA 3](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na página **Configurar**
     
     1. Verifique se ambas as interfaces de rede estão habilitadas para iSCSI. O campo **habilitado para iSCSI** deve ser definido como **Sim**.
     1. Verifique se as interfaces de rede têm a mesma velocidade, ambas devem ser 1 GbE ou 10 GbE.
     1. Observe os endereços IPv4 das interfaces habilitadas para iSCSI e salve para uso posterior no host.
* As interfaces iSCSI em seu dispositivo StorSimple devem ser acessíveis do servidor CentOS.
      Para verificar isso, você precisa fornecer os endereços IP de suas interfaces de rede habilitadas para iSCSI do StorSimple no servidor host. Os comandos usados e a saída correspondente com DATA2 (10.126.162.25) e DATA3 (10.126.162.26) são mostrados abaixo:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Configuração de hardware
Recomendamos que você conecte as duas interfaces de rede iSCSI em caminhos separados para redundância. A figura a seguir mostra a configuração de hardware recomendada para alta disponibilidade e vários caminhos de balanceamento de carga para o servidor CentOS e o dispositivo StorSimple.

![Configuração de hardware do MPIO para StorSimple para host do Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Conforme mostrado na figura anterior:

* O dispositivo StorSimple está em uma configuração ativa-passiva com dois controladores.
* Dois comutadores de SAN estão conectados aos controladores de dispositivo.
* Dois iniciadores iSCSI estão habilitados em seu dispositivo StorSimple.
* Duas interfaces de rede estão habilitadas em seu host CentOS.

A configuração acima produzirá quatro caminhos separados entre o dispositivo e o host se as interfaces de host e de dados forem roteáveis.

> [!IMPORTANT]
> * Recomendamos que você não misture interfaces de rede de 1 GbE e 10 GbE para vários caminhos. Ao usar duas interfaces de rede, ambas as interfaces devem ser do tipo idêntico.
> * Em seu dispositivo StorSimple, DATA0, DATA1, DATA4 e DATA5 são interfaces de 1 GbE, enquanto DATA2 e DATA3 são interfaces de rede de 10 GbE. |
> 
> 

## <a name="configuration-steps"></a>Passos de configuração
As etapas de configuração para vários caminhos envolvem a configuração dos caminhos disponíveis para a descoberta automática, especificando o algoritmo de balanceamento de carga a ser usado, habilitando vários caminhos e, por fim, verificando a configuração. Cada uma dessas etapas é discutida detalhadamente nas seções a seguir.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Etapa 1: configurar vários caminhos para a descoberta automática
Os dispositivos com suporte a vários caminhos podem ser automaticamente descobertos e configurados.

1. Inicializar o arquivo de `/etc/multipath.conf`. Tipo:
   
     `mpathconf --enable`
   
    O comando acima criará um arquivo de `sample/etc/multipath.conf`.
1. Inicie o serviço de vários caminhos. Tipo:
   
    `service multipathd start`
   
    Você verá a seguinte saída:
   
    `Starting multipathd daemon:`
1. Habilitar a descoberta automática de vários caminhos. Tipo:
   
    `mpathconf --find_multipaths y`
   
    Isso modificará a seção de padrões de sua `multipath.conf`, conforme mostrado abaixo:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Etapa 2: configurar vários caminhos para volumes do StorSimple
Por padrão, todos os dispositivos são pretos listados no arquivo de vários caminhos. conf e serão ignorados. Você precisará criar exceções de lista negra para permitir vários caminhos para volumes de dispositivos StorSimple.

1. Edite o arquivo de `/etc/mulitpath.conf`. Tipo:
   
    `vi /etc/multipath.conf`
1. Localize a seção blacklist_exceptions no arquivo. conf de vários caminhos. Seu dispositivo StorSimple precisa ser listado como uma exceção de lista negra nesta seção. Você pode remover os comentários das linhas relevantes nesse arquivo para modificá-lo, conforme mostrado abaixo (Use apenas o modelo específico do dispositivo que você está usando):
   
        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }

### <a name="step-3-configure-round-robin-multipathing"></a>Etapa 3: configurar vários caminhos de Round Robin
Esse algoritmo de balanceamento de carga usa todos os vários caminhos disponíveis para o controlador ativo em um modo Round Robin equilibrado.

1. Edite o arquivo de `/etc/multipath.conf`. Tipo:
   
    `vi /etc/multipath.conf`
1. Na seção `defaults`, defina o `path_grouping_policy` como `multibus`. O `path_grouping_policy` especifica a política de agrupamento de caminho padrão a ser aplicada a vários caminhos não especificados. A seção padrões será semelhante à mostrada abaixo.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Os valores mais comuns de `path_grouping_policy` incluem:
> 
> * failover = 1 caminho por grupo de prioridade
> * Multibus = todos os caminhos válidos em 1 grupo de prioridade
> 
> 

### <a name="step-4-enable-multipathing"></a>Etapa 4: habilitar vários caminhos
1. Reinicie o daemon `multipathd`. Tipo:
   
    `service multipathd restart`
1. A saída será como mostrado abaixo:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Etapa 5: verificar vários caminhos
1. Primeiro, verifique se a conexão iSCSI foi estabelecida com o dispositivo StorSimple da seguinte maneira:
   
   a. Descubra seu dispositivo StorSimple. Tipo:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    A saída quando o endereço IP para DATA0 é 10.126.162.25 e a porta 3260 é aberta no dispositivo StorSimple para o tráfego iSCSI de saída, conforme mostrado abaixo:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Copie o IQN do seu dispositivo StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, da saída anterior.

   b. Conecte-se ao dispositivo usando o IQN de destino. O dispositivo StorSimple é o destino iSCSI aqui. Tipo:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    O exemplo a seguir mostra a saída com um IQN de destino de `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. A saída indica que você se conectou com êxito às duas interfaces de rede habilitadas para iSCSI em seu dispositivo.

    ```
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Se você vir apenas uma interface de host e dois caminhos aqui, precisará habilitar ambas as interfaces no host para iSCSI. Você pode seguir as [instruções detalhadas na documentação do Linux](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Um volume é exposto ao servidor CentOS do dispositivo StorSimple. Para obter mais informações, consulte [etapa 6: criar um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) por meio do portal do Azure em seu dispositivo StorSimple.

1. Verifique os caminhos disponíveis. Tipo:

      ```
      multipath -l
      ```

      O exemplo a seguir mostra a saída para duas interfaces de rede em um dispositivo StorSimple conectado a uma interface de rede de host único com dois caminhos disponíveis.

        ```
        mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 7:0:0:1 sdc 8:32 active undef running
        `- 6:0:0:1 sdd 8:48 active undef running
        ```

        The following example shows the output for two network interfaces on a StorSimple device connected to two host network interfaces with four available paths.

        ```
        mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
        size=100G features='0' hwhandler='0' wp=rw
        `-+- policy='round-robin 0' prio=0 status=active
        |- 17:0:0:0 sdb 8:16 active undef running
        |- 15:0:0:0 sdd 8:48 active undef running
        |- 14:0:0:0 sdc 8:32 active undef running
        `- 16:0:0:0 sde 8:64 active undef running
        ```

        After the paths are configured, refer to the specific instructions on your host operating system (Centos 6.6) to mount and format this volume.

## <a name="troubleshoot-multipathing"></a>Solucionar problemas de vários caminhos
Esta seção fornece algumas dicas úteis se você encontrar problemas durante a configuração de vários caminhos.

P. Não vejo as alterações no `multipath.conf` arquivo que está tendo efeito.

R. Se você tiver feito alterações no arquivo de `multipath.conf`, será necessário reiniciar o serviço de vários caminhos. Escreva o seguinte comando:

    service multipathd restart

P. Habilitei duas interfaces de rede no dispositivo StorSimple e duas interfaces de rede no host. Quando listo os caminhos disponíveis, vejo apenas dois caminhos. Eu esperava ver quatro caminhos disponíveis.

R. Verifique se os dois caminhos estão na mesma sub-rede e roteáveis. Se as interfaces de rede estiverem em VLANs diferentes e não roteáveis, você verá apenas dois caminhos. Uma maneira de verificar isso é certificar-se de que você pode acessar ambas as interfaces de host de uma interface de rede no dispositivo StorSimple. Você precisará [entrar em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) , pois essa verificação só pode ser feita por meio de uma sessão de suporte.

P. Quando listo caminhos disponíveis, não vejo nenhuma saída.

R. Normalmente, não ver nenhum caminho com vários caminhos sugere um problema com o daemon de vários caminhos, e é mais provável que qualquer problema aqui esteja no arquivo de `multipath.conf`.

Também vale a pena verificar se você realmente pode ver alguns discos depois de se conectar ao destino, pois nenhuma resposta das listagens de vários caminhos também pode significar que você não tem discos.

* Use o seguinte comando para examinar novamente o barramento SCSI:
  
    `$ rescan-scsi-bus.sh` (parte do pacote sg3_utils)
* Digite os seguintes comandos:
  
    `$ dmesg | grep sd*`
     
     Ou
  
    `$ fdisk -l`
  
    Eles retornarão os detalhes dos discos adicionados recentemente.
* Para determinar se ele é um disco StorSimple, use os seguintes comandos:
  
    `cat /sys/block/<DISK>/device/model`
  
    Isso retornará uma cadeia de caracteres, que determinará se é um disco StorSimple.

Uma causa menos provável, mas possível, também pode ser um PID iscsid obsoleto. Use o seguinte comando para fazer logoff das sessões iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Repita esse comando para todas as interfaces de rede conectadas no destino iSCSI, que é o dispositivo StorSimple. Depois de fazer logoff de todas as sessões iSCSI, use o IQN de destino iSCSI para restabelecer a sessão iSCSI. Escreva o seguinte comando:

    iscsiadm -m node --login -T <TARGET_IQN>


P. Não tenho certeza se meu dispositivo está na lista de permissões.

R. Para verificar se o dispositivo está na lista de permissões, use o seguinte comando interativo de solução de problemas:

    multipathd -k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Para obter mais informações, acesse [solução de problemas para vários caminhos](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Lista de comandos úteis
| Tipo | Comando | Descrição |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Iniciar serviço iSCSI |
| &nbsp; |`service iscsid stop` |Parar o serviço iSCSI |
| &nbsp; |`service iscsid restart` |Reiniciar o serviço iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Descobrir destinos disponíveis no endereço especificado |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Fazer logon no destino iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Fazer logoff do destino iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Imprimir nome do iniciador iSCSI |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Verifique o estado da sessão iSCSI e o volume descobertos no host |
| &nbsp; |`iscsi -m session` |Mostra todas as sessões iSCSI estabelecidas entre o host e o dispositivo StorSimple |
|  | | |
| **Vários caminhos** |`service multipathd start` |Iniciar o daemon de vários caminhos |
| &nbsp; |`service multipathd stop` |Parar o daemon de vários caminhos |
| &nbsp; |`service multipathd restart` |Reiniciar o daemon de vários caminhos |
| &nbsp; |`chkconfig multipathd on` </br> OU </br> `mpathconf -with_chkconfig y` |Habilitar o daemon de vários caminhos para iniciar no momento da inicialização |
| &nbsp; |`multipathd -k` |Iniciar o console interativo para solução de problemas |
| &nbsp; |`multipath -l` |Listar dispositivos e conexões de vários caminhos |
| &nbsp; |`mpathconf --enable` |Crie um arquivo mulitpath. conf de exemplo no `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Passos seguintes
Enquanto estiver configurando o MPIO no host do Linux, talvez você também precise consultar os seguintes documentos do CentoS 6,6:

* [Configurando o MPIO no CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Guia de treinamento do Linux](http://linux-training.be/linuxsys.pdf)

