---
title: Configure MPIO no anfitrião StorSimple Linux
description: Aprenda os passos necessários para configurar o IO Multipating (MPIO) no seu servidor de anfitrião StorSimple Linux (Centos 6.6).
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 75ccfe7a8e62e519b1df89792211433260a6abf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294718"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Configure mPIO em um anfitrião StorSimple executando CentOS
Este artigo explica os passos necessários para configurar o IO Multipating (MPIO) no servidor anfitrião Centos 6.6. O servidor anfitrião está ligado ao seu dispositivo Microsoft Azure StorSimple para uma elevada disponibilidade através de iniciadores iSCSI. Descreve em detalhe a descoberta automática de dispositivos multipatas e a configuração específica apenas para volumes StorSimple.

Este procedimento aplica-se a todos os modelos de dispositivos da série StorSimple 8000.

> [!NOTE]
> Este procedimento não pode ser utilizado para um aparelho de nuvem StorSimple. Para obter mais informações, consulte como configurar servidores anfitriões para o seu aparelho em nuvem.


## <a name="about-multipathing"></a>Sobre multipathing
A funcionalidade multipatar permite configurar vários caminhos de E/S entre um servidor anfitrião e um dispositivo de armazenamento. Estes caminhos de E/S são ligações FÍSICAS SAN que podem incluir cabos separados, interruptores, interfaces de rede e controladores. Multipathing agrega os caminhos de E/S, para configurar um novo dispositivo que está associado a todos os caminhos agregados.

O objetivo da multipatia é duplo:

* **Alta disponibilidade**: Fornece um caminho alternativo se qualquer elemento do caminho de E/S (como cabo, interruptor, interface de rede ou controlador) falhar.
* **Equilíbrio de carga**: Dependendo da configuração do seu dispositivo de armazenamento, pode melhorar o desempenho detetando cargas nos caminhos de E/S e reequilibrando dinamicamente essas cargas.

### <a name="about-multipathing-components"></a>Sobre componentes multipatar
A multipatia no Linux consiste em componentes de núcleo e componentes do espaço do utilizador, tal como tabulado abaixo.

* **Kernel**: O componente principal é o *mapeador do dispositivo* que reencaminho A E/S e suporta o failover para caminhos e grupos de caminhos.

* **Espaço-utilizador : Trata-se de** *ferramentas multipatas* que gerem dispositivos multipatas, instruindo o módulo multipata de dispositivo-mapper o que fazer. As ferramentas consistem em:
   
   * **Multipata**: lista e configura dispositivos multipatriados.
   * **Multipatas:** daemon que executa multipatas e monitoriza os caminhos.
   * **Nome devmap:** fornece um nome significativo do dispositivo para udev para devmaps.
   * **Kpartx**: mapeia devmaps lineares para adiamentos de dispositivos para tornar os mapas multipatas partitionáveis.
   * **Multipath.conf**: ficheiro de configuração para daemon multi-path que é usado para substituir a tabela de configuração incorporada.

### <a name="about-the-multipathconf-configuration-file"></a>Sobre o ficheiro de configuração multipath.conf
O ficheiro de configuração `/etc/multipath.conf` torna muitas das funcionalidades multipatar configuráveis. O `multipath` comando e o kernel daemon usam `multipathd` informações encontradas neste ficheiro. O ficheiro é consultado apenas durante a configuração dos dispositivos multipatas. Certifique-se de que todas as alterações são feitas antes de executar o `multipath` comando. Se modificar o ficheiro posteriormente, terá de parar e recomeçar em várias imposições para que as alterações produzam efeitos.

O multipata.conf tem cinco secções:

- **Predefinições no nível do sistema** *(predefinições)*: Pode substituir as predefinições do nível do sistema.
- **Dispositivos na lista negra** *(lista negra)*: Pode especificar a lista de dispositivos que não devem ser controlados pelo mapper do dispositivo.
- **Exceções na lista negra** *(blacklist_exceptions)*: Pode identificar dispositivos específicos para serem tratados como dispositivos multipatas, mesmo que listados na lista negra.
- **Definições específicas do controlador de armazenamento** *(dispositivos)*: Pode especificar as definições de configuração que serão aplicadas a dispositivos que tenham informações do Fornecedor e do Produto.
- **Definições específicas do dispositivo** *(multipatas)*: Pode utilizar esta secção para afinar as definições de configuração para ASNS individuais.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Configurar multipatriar em StorSimple ligado ao anfitrião Linux
Um dispositivo StorSimple ligado a um hospedeiro Linux pode ser configurado para uma elevada disponibilidade e equilíbrio de carga. Por exemplo, se o anfitrião Linux tiver duas interfaces ligadas ao SAN e o dispositivo tiver duas interfaces ligadas à SAN de modo a que estas interfaces estejam na mesma sub-rede, então haverá 4 caminhos disponíveis. No entanto, se cada interface DATA no dispositivo e interface de anfitrião estiver numa sub-rede IP diferente (e não roteable), apenas 2 caminhos estarão disponíveis. Pode configurar multipathing para descobrir automaticamente todos os caminhos disponíveis, escolher um algoritmo de equilíbrio de carga para esses caminhos, aplicar configurações de configuração específicas para volumes apenas StorSimple e, em seguida, ativar e verificar multipathing.

O procedimento a seguir descreve como configurar multipatriamento quando um dispositivo StorSimple com duas interfaces de rede está ligado a um hospedeiro com duas interfaces de rede.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção detalha os pré-requisitos de configuração para o servidor CentOS e o seu dispositivo StorSimple.

### <a name="on-centos-host"></a>No anfitrião CentOS
1. Certifique-se de que o seu anfitrião CentOS tem 2 interfaces de rede ativadas. Escreva:
   
    `ifconfig`
   
    O exemplo a seguir mostra a saída quando duas interfaces de rede ( `eth0` e `eth1` ) estão presentes no hospedeiro.
   
    ```output
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
    ```
1. Instale os *utilitários iSCSI no* seu servidor CentOS. Execute os seguintes passos para instalar os *utilitários iSCSI-iniciador*.
   
   1. Faça login como `root` no seu anfitrião CentOS.
   1. Instale os *utilitários iSCSI-iniciador*. Escreva:
      
       `yum install iscsi-initiator-utils`
   1. Depois de instalar com sucesso o *iSCSI-Iniciador,,* inicie o serviço iSCSI. Escreva:
      
       `service iscsid start`
      
       Em ocasiões, `iscsid` pode realmente não começar e a `--force` opção pode ser necessária
   1. Para garantir que o seu iniciador iSCSI está ativado durante o tempo de arranque, utilize o `chkconfig` comando para ativar o serviço.
      
       `chkconfig iscsi on`
   1. Para verificar se foi devidamente configurado, verifique o comando:
      
       `chkconfig --list | grep iscsi`
      
       É apresentada abaixo uma saída de exemplo.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       Pelo exemplo acima, pode ver que o seu ambiente iSCSI funcionará no tempo de arranque nos níveis de execução 2, 3, 4 e 5.
1. Instale *o dispositivo-mapper-multipata*. Escreva:
   
    `yum install device-mapper-multipath`
   
    A instalação vai começar. Tipo **Y** para continuar quando solicitado para confirmação.

### <a name="on-storsimple-device"></a>No dispositivo StorSimple
O seu dispositivo StorSimple deve ter:

* Um mínimo de duas interfaces ativadas para o iSCSI. Para verificar se duas interfaces estão ativadas pelo iSCSI no seu dispositivo StorSimple, execute os seguintes passos no portal clássico Azure para o seu dispositivo StorSimple:
  
  1. Inicie sessão no portal clássico para o seu dispositivo StorSimple.
  1. Selecione o seu serviço StorSimple Manager, clique em **Dispositivos** e escolha o dispositivo StorSimple específico. Clique **em Configurar** e verifique as definições da interface de rede. Uma imagem com duas interfaces de rede ativadas pelo iSCSI é mostrada abaixo. Aqui os DADOS 2 e DATA 3, ambas as interfaces de 10 GbE estão ativadas para o iSCSI.
     
      ![MPIO StorsSimple DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na página **Configure**
     
     1. Certifique-se de que ambas as interfaces de rede estão ativadas pelo iSCSI. O campo **ativado iSCSI** deve ser definido como **Sim**.
     1. Certifique-se de que as interfaces de rede têm a mesma velocidade, ambas devem ser de 1 GbE ou 10 GbE.
     1. Note os endereços IPv4 das interfaces ativadas pelo iSCSI e guarde para posterior utilização no anfitrião.
* As interfaces iSCSI do seu dispositivo StorSimple devem ser alcançáveis a partir do servidor CentOS.
      Para verificar isto, precisa de fornecer os endereços IP das interfaces de rede ativadas pelo StorSimple iSCSI no servidor anfitrião. Os comandos utilizados e a saída correspondente com DATA2 (10.126.162.25) e DATA3 (10.126.162.26) são apresentados abaixo:
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Configuração de hardware
Recomendamos que conecte as duas interfaces de rede iSCSI em caminhos separados para redundância. A figura abaixo mostra a configuração de hardware recomendada para alta disponibilidade e multipatia de equilíbrio de carga para o seu servidor CentOS e dispositivo StorSimple.

![Hardware MPIO config para StorSimple para anfitrião linux](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Como mostrado na figura anterior:

* O seu dispositivo StorSimple encontra-se numa configuração activa-passiva com dois controladores.
* Dois interruptores SAN estão ligados aos controladores do dispositivo.
* Dois iniciadores iSCSI estão ativados no seu dispositivo StorSimple.
* Duas interfaces de rede estão ativadas no seu anfitrião CentOS.

A configuração acima produzirá 4 caminhos separados entre o seu dispositivo e o hospedeiro se as interfaces de anfitrião e de dados forem encaminháveis.

> [!IMPORTANT]
> * Recomendamos que não misture interfaces de rede de 1 GbE e 10 GbE para multipatas. Ao utilizar duas interfaces de rede, ambas as interfaces devem ser do tipo idêntico.
> * No seu dispositivo StorSimple, DATA0, DATA1, DATA4 e DATA5 são 1 Interfaces GbE, enquanto data2 e DATA3 são interfaces de rede de 10 GbE./
> 
> 

## <a name="configuration-steps"></a>Passos de configuração
Os passos de configuração para multipatia envolvem configurar os caminhos disponíveis para a descoberta automática, especificar o algoritmo de equilíbrio de carga para usar, permitindo multipatas e, finalmente, verificar a configuração. Cada um destes passos é discutido em detalhe nas seguintes secções.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Passo 1: Configurar multipatriamento para a descoberta automática
Os dispositivos suportados por vários caminhos podem ser automaticamente descobertos e configurados.

1. Inicializar `/etc/multipath.conf` o ficheiro. Escreva:
   
     `mpathconf --enable`
   
    O comando acima irá criar um `sample/etc/multipath.conf` ficheiro.
1. Inicie o serviço multi-path. Escreva:
   
    `service multipathd start`
   
    Verá a saída seguinte:
   
    `Starting multipathd daemon:`
1. Permitir a descoberta automática de vários caminhos. Escreva:
   
    `mpathconf --find_multipaths y`
   
    Isto modificará a secção de predefinições da sua `multipath.conf` forma mostrada abaixo:
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Passo 2: Configurar multipatriamento para volumes StorSimple
Por predefinição, todos os dispositivos são listados em preto no ficheiro multipata.conf e serão ignorados. Terá de criar exceções na lista negra para permitir a multipatração para volumes a partir de dispositivos StorSimple.

1. Editar o ficheiro `/etc/mulitpath.conf`. Escreva:
   
    `vi /etc/multipath.conf`
1. Localize a secção blacklist_exceptions no ficheiro multipata.conf. O seu dispositivo StorSimple tem de ser listado como uma exceção à lista negra nesta secção. Pode descodê-lo de linhas relevantes neste ficheiro para modificá-lo conforme mostrado abaixo (utilize apenas o modelo específico do dispositivo que está a utilizar):
   
    ```config
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
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>Passo 3: Configurar multipathing round-robin
Este algoritmo de equilíbrio de carga usa todos os multipatas disponíveis para o controlador ativo de uma forma equilibrada e redonda.

1. Editar o ficheiro `/etc/multipath.conf`. Escreva:
   
    `vi /etc/multipath.conf`
1. Sob a `defaults` secção, desacione `path_grouping_policy` o `multibus` . O `path_grouping_policy` especificado a política de agrupamento de caminhos padrão para aplicar a multipatas não especificados. A secção de predefinições será a seguinte.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> Os valores mais comuns `path_grouping_policy` incluem:
> 
> * failover = 1 caminho por grupo prioritário
> * multibus = todos os caminhos válidos em 1 grupo prioritário
> 
> 

### <a name="step-4-enable-multipathing"></a>Passo 4: Ativar a multipata
1. Reinicie o `multipathd` daemon. Escreva:
   
    `service multipathd restart`
1. A saída será como mostrado abaixo:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>Passo 5: Verificar multipathing
1. Em primeiro lugar, certifique-se de que a ligação iSCSI é estabelecida com o dispositivo StorSimple da seguinte forma:
   
   a. Descubra o seu dispositivo StorSimple. Escreva:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    A saída quando o endereço IP para DATA0 é 10.126.162.25 e a porta 3260 é aberta no dispositivo StorSimple para tráfego iSCSI de saída é como mostrado abaixo:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Copie o IQN do seu dispositivo StorSimple, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` a partir da saída anterior.

   b. Ligue-se ao dispositivo utilizando o IQN alvo. O dispositivo StorSimple é o alvo iSCSI aqui. Escreva:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    O exemplo a seguir mostra a saída com um IQN-alvo de `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . A saída indica que ligou com sucesso as duas interfaces de rede ativadas pelo iSCSI no seu dispositivo.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Se vir apenas uma interface de anfitrião e dois caminhos aqui, então precisa ativar ambas as interfaces no anfitrião para iSCSI. Pode seguir as [instruções detalhadas na documentação do Linux.](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)

1. Um volume é exposto ao servidor CentOS a partir do dispositivo StorSimple. Para obter mais informações, consulte [o Passo 6: Crie um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) através do portal Azure no seu dispositivo StorSimple.

1. Verifique os caminhos disponíveis. Escreva:

    `multipath -l`

      O exemplo a seguir mostra a saída de duas interfaces de rede num dispositivo StorSimple ligado a uma interface de rede de anfitriões único com dois caminhos disponíveis.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    O exemplo a seguir mostra a saída de duas interfaces de rede num dispositivo StorSimple ligado a duas interfaces de rede hospedeiras com quatro caminhos disponíveis.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    Depois de configurados os caminhos, consulte as instruções específicas do seu sistema operativo anfitrião (Centos 6.6) para montar e formatar este volume.

## <a name="troubleshoot-multipathing"></a>Resolução de problemas multipatar
Esta secção fornece algumas dicas úteis se encontrar problemas durante a configuração multipatar.

P. Não vejo as alterações nos `multipath.conf` ficheiros a fazer efeito.

A. Se tiver escamado alguma alteração no `multipath.conf` ficheiro, terá de reiniciar o serviço multipatar. Escreva o seguinte comando:

`service multipathd restart`

P. Permiti duas interfaces de rede no dispositivo StorSimple e duas interfaces de rede no hospedeiro. Quando enusei os caminhos disponíveis, só vejo dois caminhos. Esperava ver quatro caminhos disponíveis.

A. Certifique-se de que os dois caminhos estão na mesma sub-rede e roteáveis. Se as interfaces de rede estiverem em vLANs diferentes e não forem encaminháveis, verá apenas dois caminhos. Uma forma de verificar isto é garantir que pode chegar a ambas as interfaces de anfitrião a partir de uma interface de rede no dispositivo StorSimple. Terá de [contactar](storsimple-8000-contact-microsoft-support.md) o Microsoft Support uma vez que esta verificação só pode ser feita através de uma sessão de suporte.

P. Quando enusei caminhos disponíveis, não vejo saída.

A. Normalmente, não ver nenhum caminho multipatar sugere um problema com o deemon multi-pathing, e é mais provável que qualquer problema aqui esteja no `multipath.conf` arquivo.

Também valeria a pena verificar se pode realmente ver alguns discos depois de se ligar ao alvo, uma vez que nenhuma resposta das listas multipatar também pode significar que não tem discos.

* Utilize o seguinte comando para rescanar o autocarro SCSI:
  
    `$ rescan-scsi-bus.sh` (parte do pacote sg3_utils)
* Digite os seguintes comandos:
  
    `$ dmesg | grep sd*`
     
     Ou
  
    `$ fdisk -l`
  
    Estes irão devolver detalhes de discos recentemente adicionados.
* Para determinar se se trata de um disco StorSimple, utilize os seguintes comandos:
  
    `cat /sys/block/<DISK>/device/model`
  
    Isto irá devolver uma corda, que determinará se é um disco StorSimple.

Uma causa menos provável, mas possível, também pode ser um pid iscsid velho. Utilize o seguinte comando para iniciar sessão nas sessões iSCSI:

`iscsiadm -m node --logout -p <Target_IP>`

Repita este comando para todas as interfaces de rede ligadas no alvo iSCSI, que é o seu dispositivo StorSimple. Uma vez iniciadas a partir de todas as sessões iSCSI, utilize o IQN alvo do iSCSI para restabelecer a sessão iSCSI. Escreva o seguinte comando:

`iscsiadm -m node --login -T <TARGET_IQN>`


P. Não sei se o meu dispositivo é permitido.

A. Para verificar se o seu dispositivo é permitido, utilize o seguinte comando interativo de resolução de problemas:

```console
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
```


Para mais informações, vá à [resolução de problemas para multipatar.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)

## <a name="list-of-useful-commands"></a>Lista de comandos úteis
| Tipo | Comando | Descrição |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Iniciar o serviço iSCSI |
| &nbsp; |`service iscsid stop` |Parar o serviço iSCSI |
| &nbsp; |`service iscsid restart` |Reiniciar o serviço iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Descubra os alvos disponíveis no endereço especificado |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Faça login no alvo iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Faça login a partir do alvo iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Imprimir nome de iniciador iSCSI |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Verifique o estado da sessão do iSCSI e o volume descoberto no hospedeiro |
| &nbsp; |`iscsi -m session` |Mostra todas as sessões iSCSI estabelecidas entre o anfitrião e o dispositivo StorSimple |
|  | | |
| **Multipathing** |`service multipathd start` |Inicie o daemon multi-path |
| &nbsp; |`service multipathd stop` |Pare o daemon multi-path |
| &nbsp; |`service multipathd restart` |Reiniciar o daemon multi-path |
| &nbsp; |`chkconfig multipathd on` </br> OU </br> `mpathconf -with_chkconfig y` |Permitir que o daemon multi-path comece na hora do arranque |
| &nbsp; |`multipathd -k` |Inicie a consola interativa para resolução de problemas |
| &nbsp; |`multipath -l` |Listar ligações e dispositivos multipatas |
| &nbsp; |`mpathconf --enable` |Criar um ficheiro mulitpath.conf de amostra em `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Passos seguintes
Como está a configurar o MPIO no anfitrião Linux, também poderá ter de consultar os seguintes documentos CentoS 6.6:

* [Criação do MPIO no CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Guia de Treino linux](http://linux-training.be/linuxsys.pdf)
