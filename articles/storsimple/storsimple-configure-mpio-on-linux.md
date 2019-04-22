---
title: Configurar a MPIO no anfitrião Linux do StorSimple | Documentos da Microsoft
description: Configurar a MPIO no StorSimple ligado ao anfitrião Linux com o CentOS 6.6
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: tysonn
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bc1e8a5abc85af95448570497177030f17649d87
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877589"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Configurar o MPIO num anfitrião StorSimple em execução no CentOS
Este artigo explica os passos necessários para configurar o Multipathing e/s (MPIO) no seu servidor de anfitrião do Centos 6.6. O servidor de anfitrião está ligado ao seu dispositivo do Microsoft Azure StorSimple para elevada disponibilidade através de iniciadores iSCSI. Ele descreve detalhadamente a deteção automática de dispositivos multipath e o programa de configuração específico apenas para os volumes do StorSimple.

Este procedimento é aplicável a todos os modelos de dispositivos da série StorSimple 8000.

> [!NOTE]
> Este procedimento não pode ser utilizado para uma StorSimple Cloud Appliance. Para obter mais informações, consulte como configurar servidores de anfitrião para a sua aplicação da cloud.


## <a name="about-multipathing"></a>Sobre o multipathing
A funcionalidade de multipathing permite-lhe configurar vários caminhos de e/s entre um servidor de anfitrião e um dispositivo de armazenamento. Estes caminhos de e/s são ligações SAN físicas, que podem incluir cabos separados, comutadores, interfaces de rede e controladores. Multipathing agrega os caminhos de e/s, para configurar um novo dispositivo que está associado a todos os caminhos de agregados.

A finalidade de multipathing tem duas fases:

* **Elevada disponibilidade**: Ele fornece um caminho alternativo se falhar de qualquer elemento do caminho de e/s (por exemplo, um cabo, comutador, interface de rede ou controlador).
* **O balanceamento de carga**: Dependendo da configuração do seu dispositivo de armazenamento, ele pode melhorar o desempenho, detectando cargas nos caminhos de e/s e dinamicamente reequilibrar essas cargas.

### <a name="about-multipathing-components"></a>Sobre os componentes de multipathing
Multipathing no Linux é constituído por componentes de kernel e componentes de espaço do usuário como apresentadas abaixo.

* **Kernel**: O componente principal é o *mapeador de dispositivo* que redireciona a e/s e suporta a ativação pós-falha para caminhos e grupos de caminho.

* **Espaço do usuário**: Estes são *ferramentas de Multipath i* que gerenciam dispositivos multipathed por instruindo o módulo de Multipath i do mapeador de dispositivo, o que fazer. As ferramentas consistem em:
   
   * **Multipath i**: apresenta uma lista e configura os dispositivos multipathed.
   * **Multipathd**: daemon que executa o Multipath i e monitoriza os caminhos.
   * **Nome do Devmap**: Fornece um nome de dispositivo relevante para udev para devmaps.
   * **Kpartx**: linear devmaps é mapeado para as partições do dispositivo para tornar o multipath mapas de partições.
   * **Multipath.Conf**: ficheiro de configuração para multipath daemon que é utilizado para substituir a tabela de configuração incorporados.

### <a name="about-the-multipathconf-configuration-file"></a>Sobre o ficheiro de configuração multipath.conf
O ficheiro de configuração `/etc/multipath.conf` torna muitos dos recursos de multipathing configurável pelo utilizador. O `multipath` comando e o daemon de kernel `multipathd` utilizar informações encontradas neste ficheiro. O ficheiro consultei-se apenas durante a configuração dos dispositivos multipath i. Certifique-se de que todas as alterações são feitas antes de executar o `multipath` comando. Se modificar o ficheiro, posteriormente, terá de parar e iniciar multipathd novamente para que as alterações entrem em vigor.

O multipath.conf tem cinco seções:

- **Predefinições ao nível do sistema** *(predefinições)*: Pode substituir as predefinições ao nível do sistema.
- **Bloqueado de dispositivos** *(lista de bloqueios)*: Pode especificar a lista de dispositivos que não devem ser controlados por mapeador de dispositivo.
- **Lista de exceções de bloqueio** *(blacklist_exceptions)*: Pode identificar dispositivos específicos sejam tratados como dispositivos multipath, mesmo se listados na lista de bloqueios.
- **As definições específicas do controlador de armazenamento** *(dispositivos)*: Pode especificar definições de configuração que serão aplicadas a dispositivos que tenham informações do fornecedor e o produto.
- **As definições específicas do dispositivo** *(multipaths)*: Pode utilizar esta secção para ajustar as definições de configuração para os LUNs individuais.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Configurar o multipathing no StorSimple ligado ao anfitrião Linux
Um dispositivo StorSimple ligado ao anfitrião Linux pode ser configurado para elevada disponibilidade e balanceamento de carga. Por exemplo, se o anfitrião Linux possui duas interfaces ligados à SAN e o dispositivo tiver duas interfaces ligados à SAN que essas interfaces são na mesma sub-rede, em seguida, haverá 4 caminhos disponíveis. No entanto, se cada interface de dados na interface de dispositivo e o anfitrião estiver numa sub-rede IP diferente (e não encaminháveis), em seguida, apenas 2 caminhos estarão disponíveis. Pode configurar multipathing automaticamente detetar todos os caminhos disponíveis, escolher um algoritmo de balanceamento de carga para esses caminhos, aplicar definições de configuração específicas para volumes de só de StorSimple e, em seguida, ativar e certifique-se de multipathing.

O procedimento seguinte descreve como configurar o multipathing quando um dispositivo do StorSimple com duas interfaces de rede está ligado a um anfitrião com duas interfaces de rede.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção fornece detalhes sobre os pré-requisitos de configuração para o servidor do CentOS e do dispositivo StorSimple.

### <a name="on-centos-host"></a>No anfitrião do CentOS
1. Certifique-se de que o anfitrião de CentOS tem 2 interfaces de rede ativadas. Escreva:
   
    `ifconfig`
   
    O exemplo seguinte mostra a saída quando duas interfaces de rede (`eth0` e `eth1`) estão presentes no anfitrião.
   
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
1. Instale *iniciador de iSCSI-utils* no seu servidor de CentOS. Execute os seguintes passos para instalar *iniciador de iSCSI-utils*.
   
   1. Inicie sessão como `root` em seu host de CentOS.
   1. Instalar o *iniciador de iSCSI-utils*. Escreva:
      
       `yum install iscsi-initiator-utils`
   1. Depois do *iniciador de iSCSI-utils* com êxito é instalado, inicie o serviço de iSCSI. Escreva:
      
       `service iscsid start`
      
       Em ocasiões, `iscsid` , na verdade, não pode começar e a `--force` opção poderá ser necessária
   1. Para garantir que o iniciador iSCSI está ativado durante o tempo de arranque, utilize o `chkconfig` comando para ativar o serviço.
      
       `chkconfig iscsi on`
   1. Para verificar que foi corretamente configurada, execute o comando:
      
       `chkconfig --list | grep iscsi`
      
       É apresentada abaixo uma saída de exemplo.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       O exemplo acima, pode ver que o seu ambiente de iSCSI será executado no momento da inicialização em níveis de execução do 2, 3, 4 e 5.
1. Instale *dispositivo-mapeador-Multipath i*. Escreva:
   
    `yum install device-mapper-multipath`
   
    A instalação será iniciado. Tipo **Y** continuar quando lhe for pedida confirmação.

### <a name="on-storsimple-device"></a>No dispositivo StorSimple
Deve ter o dispositivo StorSimple:

* Um mínimo de duas interfaces ativadas para iSCSI. Para verificar que duas interfaces são habilitados para iSCSI no dispositivo StorSimple, execute os seguintes passos no portal clássico do Azure para o dispositivo StorSimple:
  
  1. Inicie sessão no portal clássico para o dispositivo StorSimple.
  1. Selecione o seu serviço StorSimple Manager, clique em **dispositivos** e selecione o dispositivo StorSimple específico. Clique em **configurar** e verifique as definições de interface de rede. Uma captura de ecrã com duas interfaces de rede com capacidade de iSCSI é mostrada abaixo. Aqui dados 2 e 3 de dados, ambos os 10 GbE interfaces estão ativadas para iSCSI.
     
      ![Configuração do MPIO StorsSimple dados 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![O StorSimple MPIO dados 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na **configurar** página
     
     1. Certifique-se de que ambas as interfaces de rede são habilitados para iSCSI. O **compatível com iSCSI** campo deve ser definido como **Sim**.
     1. Certifique-se de que as interfaces de rede tem a mesma velocidade, ambos devem ser de 1 GbE ou de 10 GbE.
     1. Tenha em atenção os endereços IPv4 das interfaces habilitados para iSCSI e guardar para utilização posterior no anfitrião.
* As interfaces de iSCSI no dispositivo StorSimple devem ser acessíveis a partir do servidor do CentOS.
      Para verificar isto, terá de fornecer os endereços IP das suas interfaces de rede com capacidade de iSCSI do StorSimple no seu servidor de anfitrião. Os comandos utilizados e o resultado correspondente com DATA2 (10.126.162.25) e DATA3 (10.126.162.26) é mostrado abaixo:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Configuração de hardware
Recomendamos que se conectar as duas interfaces de rede iSCSI em caminhos separados para redundância. A figura abaixo mostra a configuração de hardware recomendado para elevada disponibilidade e balanceamento de carga multipathing para o seu servidor do CentOS e o dispositivo StorSimple.

![Configuração de hardware do MPIO para o StorSimple para o anfitrião Linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Conforme mostrado na figura anterior:

* O dispositivo StorSimple é uma configuração ativa-passiva com dois controladores.
* Duas opções de SAN estão ligadas aos controladores de dispositivo.
* Dois iniciadores de iSCSI estão ativadas no dispositivo StorSimple.
* Duas interfaces de rede estão ativadas no anfitrião do CentOS.

A configuração acima resultará em 4 caminhos separados entre o dispositivo e o anfitrião se as interfaces de dados e do anfitrião são encaminháveis.

> [!IMPORTANT]
> * Recomendamos que não misture de 1 GbE e interfaces de rede de 10 GbE para multipathing. Ao usar duas interfaces de rede, ambas as interfaces devem ser do tipo idêntico.
> * No seu dispositivo StorSimple, DATA0, dados1, DATA4 e DATA5 são interfaces de 1 GbE enquanto DATA2 e DATA3 são interfaces de rede de 10 GbE. |
> 
> 

## <a name="configuration-steps"></a>Passos de configuração
Os passos de configuração para multipathing envolvem a configuração os caminhos disponíveis para a deteção automática, especificando o algoritmo de balanceamento de carga para utilizar, permitindo multipathing e, finalmente, verificar a configuração. Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Passo 1: Configurar o multipathing para a deteção automática
Os dispositivos multipath i com suporte podem ser automaticamente detetados e configurados.

1. Inicializar `/etc/multipath.conf` ficheiro. Escreva:
   
     `mpathconf --enable`
   
    O comando acima irá criar um `sample/etc/multipath.conf` ficheiro.
1. Inicie o serviço de Multipath i. Escreva:
   
    `service multipathd start`
   
    Verá a seguinte saída:
   
    `Starting multipathd daemon:`
1. Ative a deteção automática de multipaths. Escreva:
   
    `mpathconf --find_multipaths y`
   
    Isto irá modificar a seção de padrões da sua `multipath.conf` conforme mostrado abaixo:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Passo 2: Configurar o multipathing para volumes do StorSimple
Por predefinição, todos os dispositivos ficam em pretas listados no arquivo de multipath.conf e serão ignorados. Terá de criar exceções de lista de bloqueios para permitir que o multipathing para volumes a partir de dispositivos do StorSimple.

1. Editar o `/etc/mulitpath.conf` ficheiro. Escreva:
   
    `vi /etc/multipath.conf`
1. Localize a secção de blacklist_exceptions no ficheiro multipath.conf. O dispositivo StorSimple tem de ser listada como uma exceção de lista de bloqueios nesta secção. Pode remover os comentários de linhas relevantes neste ficheiro para modificá-lo conforme apresentado abaixo (utilize o modelo específico do dispositivo que está a utilizar):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Passo 3: Configurar o multipathing round robin
Esse algoritmo de balanceamento de carga utiliza todos os multipaths disponíveis para o controlador ativo, de forma equilibrada, round robin.

1. Editar o `/etc/multipath.conf` ficheiro. Escreva:
   
    `vi /etc/multipath.conf`
1. Sob o `defaults` secção, defina o `path_grouping_policy` para `multibus`. O `path_grouping_policy` Especifica o caminho predefinido da política se aplique multipaths não especificados de agrupamento. A seção de padrões terá um aspeto conforme mostrado abaixo.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Os valores mais comuns de `path_grouping_policy` incluem:
> 
> * ativação pós-falha = 1 caminho por grupo de prioridade
> * multibus = todos os caminhos válidos no grupo de 1 prioridade
> 
> 

### <a name="step-4-enable-multipathing"></a>Passo 4: Ativar multipathing
1. Reinicie o `multipathd` daemon. Escreva:
   
    `service multipathd restart`
1. O resultado será conforme mostrado abaixo:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Passo 5: Certifique-se de multipathing
1. Primeiro, certifique-se de que o iSCSI é estabelecer ligação com o dispositivo StorSimple da seguinte forma:
   
   a. Detete o dispositivo StorSimple. Escreva:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    O resultado quando o endereço IP DATA0 está 10.126.162.25 e porta 3260 é aberta no dispositivo StorSimple para o tráfego de saída iSCSI é conforme mostrado abaixo:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Copie o IQN de dispositivo do StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, a partir da saída anterior.

   b. Ligar ao dispositivo utilizar o IQN de destino. O dispositivo StorSimple é o destino de iSCSI aqui. Escreva:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    O exemplo seguinte mostra a saída com um destino IQN de `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. A saída indica que ligou com êxito para as duas interfaces de rede iSCSI ativada no seu dispositivo.

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

    Se vir a interface de anfitrião apenas um e dois caminhos aqui, em seguida, tem de ativar ambas as interfaces no anfitrião para iSCSI. Pode seguir a [instruções na documentação do Linux detalhadas](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

1. Um volume é exposto para o servidor de CentOS do dispositivo StorSimple. Para obter mais informações, consulte [passo 6: Criar um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) através do portal do Azure no seu dispositivo StorSimple.

1. Verifique se os caminhos disponíveis. Escreva:

      ```
      multipath –l
      ```

      O exemplo seguinte mostra a saída de duas interfaces de rede num dispositivo StorSimple ligado a uma interface de rede de anfitrião único com dois caminhos disponíveis.

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

## <a name="troubleshoot-multipathing"></a>Resolver problemas de multipathing
Esta seção fornece algumas dicas úteis, caso se depare com quaisquer problemas durante a configuração de multipathing.

P. Não vejo as alterações no `multipath.conf` ficheiros entrarem em vigor.

R. Se tiver efectuado alterações para o `multipath.conf` ficheiro, terá de reiniciar o serviço de multipathing. Escreva o seguinte comando:

    service multipathd restart

P. Eu tiver ativado a duas interfaces de rede no dispositivo StorSimple e duas interfaces de rede no anfitrião. Quando eu listar os caminhos disponíveis, vejo apenas dois caminhos. Eu esperava ver quatro caminhos disponíveis.

R. Certifique-se de que os dois caminhos estão na mesma sub-rede e encaminhável. Se as interfaces de rede estiverem em diferentes vLANs e não encaminháveis internos, verá apenas dois caminhos. Uma forma para verificar esta situação é certificar-se de que pode entrar ambas as interfaces de anfitrião de uma interface de rede no dispositivo StorSimple. Precisará [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) como esta verificação só pode ser feita por meio de uma sessão de suporte.

P. Quando eu listar os caminhos disponíveis, não vejo qualquer saída.

R. Normalmente, não a ver caminhos multipathed indica um problema com o daemon de multipathing e é mais provável que qualquer problema aqui está no `multipath.conf` ficheiro.

Também seria a pena verificar o que realmente pode ver alguns discos depois de ligar para o destino, como nenhuma resposta das listagens de Multipath i pode também significar que não tem quaisquer discos.

* Utilize o seguinte comando para reanalisar o barramento SCSI:
  
    `$ rescan-scsi-bus.sh` (parte do pacote de sg3_utils)
* Escreva os seguintes comandos:
  
    `$ dmesg | grep sd*`
     
     Ou
  
    `$ fdisk –l`
  
    Estes irão devolver detalhes de discos adicionados recentemente.
* Para determinar se é um disco do StorSimple, utilize os seguintes comandos:
  
    `cat /sys/block/<DISK>/device/model`
  
    Isto irá devolver uma cadeia de caracteres, que irá determinar se se trata de um disco do StorSimple.

A menos causa provável, mas é possível também poderia ser iscsid obsoleto pid. Utilize o seguinte comando para fazer logoff das sessões iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Repita este comando para todas as interfaces de rede ligada no destino iSCSI, que é o dispositivo StorSimple. Depois de iniciar de todas as sessões de iSCSI, utilize o IQN de destino iSCSI para restabelecer a sessão de iSCSI. Escreva o seguinte comando:

    iscsiadm -m node --login -T <TARGET_IQN>


P. Não tenho a certeza de que se meu dispositivo estiver na lista de permissões.

R. Para verificar se o seu dispositivo está na lista de permissões, utilize o seguinte comando interativo de resolução de problemas:

    multipathd –k
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


Para obter mais informações, aceda a [utilizar a resolução de problemas de comando interativo para multipathing](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html).

## <a name="list-of-useful-commands"></a>Lista de comandos úteis
| Type | Comando | Descrição |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Iniciar o serviço iSCSI |
| &nbsp; |`service iscsid stop` |Parar o serviço iSCSI |
| &nbsp; |`service iscsid restart` |Reinicie o serviço iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Detetar os destinos disponíveis no endereço especificado |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Inicie sessão no destino iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Terminar sessão proveniente do destino iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Nome do iniciador iSCSI de impressão |
| &nbsp; |`iscsiadm –m session –s <sessionid> -P 3` |Verifique o estado da sessão de iSCSI e volume detetados no anfitrião |
| &nbsp; |`iscsi –m session` |Mostra todas as sessões de iSCSI estabelecidas entre o anfitrião e o dispositivo StorSimple |
|  | | |
| **Multipathing** |`service multipathd start` |Iniciar o daemon de Multipath i |
| &nbsp; |`service multipathd stop` |Parar o daemon de Multipath i |
| &nbsp; |`service multipathd restart` |Reinicie-o Multipath i |
| &nbsp; |`chkconfig multipathd on` </br> OU </br> `mpathconf –with_chkconfig y` |Ativar o daemon de multipath iniciar no momento da inicialização |
| &nbsp; |`multipathd –k` |Inicie o console interativo para resolução de problemas |
| &nbsp; |`multipath –l` |Lista de ligações multipath e dispositivos |
| &nbsp; |`mpathconf --enable` |Criar um ficheiro de mulitpath.conf de exemplo no `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Passos Seguintes
Como está a configurar o MPIO no anfitrião Linux, também poderá ter de consultar os seguintes documentos de CentoS 6.6:

* [Configurar o MPIO no CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
* [Guia de treinamento do Linux](http://linux-training.be/linuxsys.pdf)

