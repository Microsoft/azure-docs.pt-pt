---
title: Configure MPIO no anfitrião StorSimple Linux
description: Configure MPIO no StorSimple ligado a um hospedeiro Linux executando CentOS 6.6
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 5dadd231335e93839e947077168f32dbfe96eb45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278368"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>Configure MPIO em um anfitrião StorSimple executando CentOS
Este artigo explica os passos necessários para configurar o Multipathing IO (MPIO) no seu servidor anfitrião Centos 6.6. O servidor de anfitriões está ligado ao seu dispositivo Microsoft Azure StorSimple para uma elevada disponibilidade através de iniciadores iSCSI. Descreve em detalhe a descoberta automática de dispositivos multipatas e a configuração específica apenas para volumes StorSimple.

Este procedimento é aplicável a todos os modelos de dispositivos da série StorSimple 8000.

> [!NOTE]
> Este procedimento não pode ser utilizado para um Aparelho de Nuvem StorSimple. Para mais informações, consulte como configurar os servidores de hospedar para o seu aparelho em nuvem.


## <a name="about-multipathing"></a>Sobre multipatia
A função multipatizadora permite configurar múltiplos caminhos de I/S entre um servidor de anfitriões e um dispositivo de armazenamento. Estes caminhos de I/O são ligações Físicas SAN que podem incluir cabos separados, interruptores, interfaces de rede e controladores. Multipatizando agrega os caminhos de I/S, para configurar um novo dispositivo que está associado a todos os caminhos agregados.

O objetivo do multipatismo é duplo:

* **Alta disponibilidade**: Proporciona um caminho alternativo se qualquer elemento do caminho De/S (como um cabo, interruptor, interface de rede ou controlador) falhar.
* **Equilíbrio de carga**: Dependendo da configuração do seu dispositivo de armazenamento, pode melhorar o desempenho detetando cargas nos caminhos de I/S e reequilibrando dinamicamente essas cargas.

### <a name="about-multipathing-components"></a>Sobre componentes multipatados
A multipatia em Linux consiste em componentes kernel e componentes espaço-utilizador como tabulado abaixo.

* **Kernel**: O componente principal é o *mapper* do dispositivo que redireciona o I/O e suporta a falha para caminhos e grupos de caminhos.

* **Espaço de utilizador**: Estas são *ferramentas multipatas* que gerem dispositivos multipatas, instruindo o módulo multipata do dispositivo o que fazer. As ferramentas consistem em:
   
   * **Multipath**: listas e configuradispositivos multipatas.
   * **Multipatado**: daemon que executa o multipata e monitoriza os caminhos.
   * **Nome de Vmap**: fornece um nome significativo de dispositivo para udev para mapas devmaps.
   * **Kpartx**: mapeie mapas lineares para divisórias de dispositivos para tornar os mapas multipatas divididos.
   * **Multipath.conf**: ficheiro de configuração para daemon multipata que é usado para substituir a tabela de configuração incorporada.

### <a name="about-the-multipathconf-configuration-file"></a>Sobre o ficheiro de configuração multipath.conf
O ficheiro `/etc/multipath.conf` de configuração torna muitas das funcionalidades multipatas configuráveis pelo utilizador. O `multipath` comando e o daemon `multipathd` do kernel usam as informações encontradas neste ficheiro. O ficheiro só é consultado durante a configuração dos dispositivos multipatas. Certifique-se de que todas as `multipath` alterações são feitas antes de executar o comando. Se modificar o ficheiro posteriormente, terá de parar e voltar a ser multipatado para que as alterações entrem em vigor.

O multipath.conf tem cinco secções:

- **Predefinições do nível do sistema** *(predefinições)*: Pode substituir as falhas do nível do sistema.
- **Dispositivos na lista negra** *(lista negra)*: Pode especificar a lista de dispositivos que não devem ser controlados pelo mapper do dispositivo.
- **Exceções à lista negra** *(blacklist_exceptions)*: Pode identificar dispositivos específicos a serem tratados como dispositivos multipatas, mesmo que listados na lista negra.
- **Definições específicas** do controlador de armazenamento *(dispositivos)*: Pode especificar as definições de configuração que serão aplicadas a dispositivos que possuam informações sobre o Fornecedor e o Produto.
- **Definições específicas** do dispositivo *(multipatas)*: Pode utilizar esta secção para afinar as definições de configuração para LUNs individuais.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Configure multipatas no StorSimple ligado ao hospedeiro Linux
Um dispositivo StorSimple ligado a um hospedeiro Linux pode ser configurado para alta disponibilidade e equilíbrio de carga. Por exemplo, se o hospedeiro Linux tiver duas interfaces ligadas ao SAN e o dispositivo tiver duas interfaces ligadas ao SAN de tal forma que estas interfaces estão na mesma sub-rede, então haverá 4 caminhos disponíveis. No entanto, se cada interface DATA no dispositivo e interface do hospedeiro estiver em uma subnet IP diferente (e não resaída), então apenas 2 caminhos estarão disponíveis. Pode configurar multipatas para descobrir automaticamente todos os caminhos disponíveis, escolher um algoritmo de equilíbrio de carga para esses caminhos, aplicar configurações de configuração específicas para volumes storSimple-only e, em seguida, ativar e verificar multipatas.

O procedimento seguinte descreve como configurar multipatas quando um dispositivo StorSimple com duas interfaces de rede está ligado a um hospedeiro com duas interfaces de rede.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção detalha os pré-requisitos de configuração para o servidor CentOS e para o seu dispositivo StorSimple.

### <a name="on-centos-host"></a>No anfitrião do CentOS
1. Certifique-se de que o seu anfitrião CentOS tem 2 interfaces de rede ativadas. Escreva:
   
    `ifconfig`
   
    O exemplo seguinte mostra a saída`eth0` quando `eth1`duas interfaces de rede (e) estão presentes no hospedeiro.
   
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
1. Instale os *utilitários iSCSI-iniciador* no seu servidor CentOS. Execute os seguintes passos para instalar os dispositivos de *instalação iSCSI-iniciador .*
   
   1. Inicie sessão como `root` no seu anfitrião CentOS.
   1. Instale os *iSCSI-iniciador-utilitários*. Escreva:
      
       `yum install iscsi-initiator-utils`
   1. Depois de instalado com sucesso os *iSCSI-Initiator-utils,* inicie o serviço iSCSI. Escreva:
      
       `service iscsid start`
      
       Em ocasiões, `iscsid` pode realmente `--force` não começar e a opção pode ser necessária
   1. Para garantir que o seu iniciador iSCSI está ativado durante o tempo de arranque, utilize o `chkconfig` comando para ativar o serviço.
      
       `chkconfig iscsi on`
   1. Para verificar se foi corretamente configurado, execute o comando:
      
       `chkconfig --list | grep iscsi`
      
       É apresentada abaixo uma saída de exemplo.
      
           iscsi   0:off   1:off   2:on3:on4:on5:on6:off
           iscsid  0:off   1:off   2:on3:on4:on5:on6:off
      
       Pelo exemplo acima, pode ver que o seu ambiente iSCSI funcionará no tempo de arranque nos níveis 2, 3, 4 e 5.
1. Instale o *multipata do dispositivo.* Escreva:
   
    `yum install device-mapper-multipath`
   
    A instalação começará. Tipo **Y** para continuar quando solicitado para confirmação.

### <a name="on-storsimple-device"></a>No dispositivo StorSimple
O seu dispositivo StorSimple deve ter:

* Um mínimo de duas interfaces ativadas para iSCSI. Para verificar se duas interfaces estão ativadas por iSCSI no seu dispositivo StorSimple, execute os seguintes passos no portal clássico Azure para o seu dispositivo StorSimple:
  
  1. Inicie sessão no portal clássico do seu dispositivo StorSimple.
  1. Selecione o seu serviço StorSimple Manager, clique em **Dispositivos** e escolha o dispositivo StorSimple específico. Clique em **Configurar** e verificar as definições de interface de rede. Uma imagem com duas interfaces de rede ativadas por iSCSI é mostrada abaixo. Aqui DATA 2 e DATA 3, ambas as interfaces de 10 GbE estão ativadas para iSCSI.
     
      ![MPIO StorsDados Simples DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 Config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Na página **Configure**
     
     1. Certifique-se de que ambas as interfaces de rede estão ativadas por iSCSI. O campo **iSCSI ativado** deve ser definido para **Sim**.
     1. Certifique-se de que as interfaces de rede têm a mesma velocidade, ambas devem ser de 1 GbE ou 10 GbE.
     1. Note os endereços IPv4 das interfaces ativadas pelo iSCSI e guarde para posterior utilização no hospedeiro.
* As interfaces iSCSI do seu dispositivo StorSimple devem ser acessíveis a partir do servidor CentOS.
      Para verificar isto, tem de fornecer os endereços IP das interfaces de rede ativadas pelo StorSimple iSCSI no seu servidor de anfitriões. Os comandos utilizados e a saída correspondente com DATA2 (10.126.162.25) e DATA3 (10.126.162.26) são apresentados abaixo:
  
        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target

### <a name="hardware-configuration"></a>Configuração de hardware
Recomendamos que ligue as duas interfaces de rede iSCSI em caminhos separados para despedimento. A figura abaixo mostra a configuração de hardware recomendada para alta disponibilidade e multipatagem de equilíbrio de carga para o seu servidor CentOS e dispositivo StorSimple.

![Config de hardware MPIO para StorSimple para linux anfitrião](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Como mostra a figura anterior:

* O seu dispositivo StorSimple encontra-se numa configuração activa-passiva com dois controladores.
* Dois interruptores SAN estão ligados aos controladores do seu dispositivo.
* Dois iniciadores iSCSI estão ativados no seu dispositivo StorSimple.
* Duas interfaces de rede estão ativadas no seu anfitrião CentOS.

A configuração acima produzirá 4 caminhos separados entre o seu dispositivo e o anfitrião se o hospedeiro e as interfaces de dados forem enredados.

> [!IMPORTANT]
> * Recomendamos que não misture interfaces de rede de 1 GbE e 10 GbE para multipatia. Ao utilizar duas interfaces de rede, ambas as interfaces devem ser do mesmo tipo.
> * No seu dispositivo StorSimple, DATA0, DATA1, DATA4 e DATA5 estão 1 interfaces GbE, enquanto DATA2 e DATA3 são interfaces de rede de 10 GbE.[
> 
> 

## <a name="configuration-steps"></a>Passos de configuração
Os passos de configuração para multipatas envolvem configurar os caminhos disponíveis para a descoberta automática, especificando o algoritmo de equilíbrio de carga a utilizar, permitindo multipatiae e verificando finalmente a configuração. Cada um destes passos é discutido em detalhe nas seguintes secções.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Passo 1: Configure multipatas para a descoberta automática
Os dispositivos multipatas suportados podem ser automaticamente descobertos e configurados.

1. Inicializar `/etc/multipath.conf` o ficheiro. Escreva:
   
     `mpathconf --enable`
   
    O comando acima `sample/etc/multipath.conf` criará um ficheiro.
1. Inicie o serviço multi-path. Escreva:
   
    `service multipathd start`
   
    Verá a saída seguinte:
   
    `Starting multipathd daemon:`
1. Permitir a descoberta automática de múltiplos caminhos. Escreva:
   
    `mpathconf --find_multipaths y`
   
    Isto modificará a secção `multipath.conf` de predefinições da sua, como mostrado abaixo:
   
        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Passo 2: Configure multipatia para volumes StorSimple
Por predefinição, todos os dispositivos são listados em preto no ficheiro multipath.conf e serão ignorados. Terá de criar exceções na lista negra para permitir a multipatia para volumes a partir de dispositivos StorSimple.

1. Editar `/etc/mulitpath.conf` o ficheiro. Escreva:
   
    `vi /etc/multipath.conf`
1. Localize a secção blacklist_exceptions no ficheiro multipath.conf. O seu dispositivo StorSimple precisa de ser listado como uma exceção à lista negra nesta secção. Pode não comentar linhas relevantes neste ficheiro para modificá-lo como mostrado abaixo (utilize apenas o modelo específico do dispositivo que está a utilizar):
   
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

### <a name="step-3-configure-round-robin-multipathing"></a>Passo 3: Configure multipatagem de robin redondo
Este algoritmo de equilíbrio de carga usa todos os multipatas disponíveis para o controlador ativo de uma forma equilibrada e redonda.

1. Editar `/etc/multipath.conf` o ficheiro. Escreva:
   
    `vi /etc/multipath.conf`
1. Por `defaults` baixo da `path_grouping_policy` secção, coloque o `multibus`. A `path_grouping_policy` especifica a política de agrupamento de caminhos padrão para aplicar a multipatas não especificadas. A secção de incumprimentos será vista como mostrado abaixo.
   
        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }

> [!NOTE]
> Os valores `path_grouping_policy` mais comuns incluem:
> 
> * failover = 1 caminho por grupo prioritário
> * multibus = todos os caminhos válidos em 1 grupo prioritário
> 
> 

### <a name="step-4-enable-multipathing"></a>Passo 4: Ativar o multipata
1. Reinicie `multipathd` o daemon. Escreva:
   
    `service multipathd restart`
1. A saída será como mostrado abaixo:
   
        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]

### <a name="step-5-verify-multipathing"></a>Passo 5: Verificar multipatia
1. Primeiro certifique-se de que a ligação iSCSI está estabelecida com o dispositivo StorSimple da seguinte forma:
   
   a. Descubra o seu dispositivo StorSimple. Escreva:
      
    ```
    iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>
    ```
    
    A saída quando o endereço IP para DATA0 é 10.126.162.25 e a porta 3260 é aberta no dispositivo StorSimple para tráfego iSCSI de saída é como mostrado abaixo:
    
    ```
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Copie o IQN do seu `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`dispositivo StorSimple, a partir da saída anterior.

   b. Ligue-se ao dispositivo utilizando o target IQN. O dispositivo StorSimple é o alvo iSCSI aqui. Escreva:

    ```
    iscsiadm -m node --login -T <IQN of iSCSI target>
    ```

    O exemplo seguinte mostra a saída `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`com um QIN alvo de . A saída indica que ligou com sucesso às duas interfaces de rede ativadas pelo iSCSI no seu dispositivo.

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

    Se vir apenas uma interface de anfitrião e dois caminhos aqui, então precisa de ativar ambas as interfaces no anfitrião para iSCSI. Pode seguir as [instruções detalhadas na documentação do Linux.](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)

1. Um volume é exposto ao servidor CentOS a partir do dispositivo StorSimple. Para mais informações, consulte passo [6: Crie um volume](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) através do portal Azure no seu dispositivo StorSimple.

1. Verifique os caminhos disponíveis. Escreva:

      ```
      multipath -l
      ```

      O exemplo seguinte mostra a saída de duas interfaces de rede num dispositivo StorSimple ligado a uma única interface de rede de hospedeiros com dois caminhos disponíveis.

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

## <a name="troubleshoot-multipathing"></a>Multipatagem de resolução de problemas
Esta secção fornece algumas dicas úteis se tiver algum problema durante a configuração multipata.

P. Não vejo as alterações nos `multipath.conf` ficheiros a produzirem efeito.

R. Se tiver feito alterações `multipath.conf` no ficheiro, terá de reiniciar o serviço multi-pathing. Escreva o seguinte comando:

    service multipathd restart

P. Ativei duas interfaces de rede no dispositivo StorSimple e duas interfaces de rede no hospedeiro. Quando listar os caminhos disponíveis, vejo apenas dois caminhos. Esperava ver quatro caminhos disponíveis.

R. Certifique-se de que os dois caminhos estão na mesma sub-rede e encaminhados. Se as interfaces de rede estiverem em vLANs diferentes e não forem resaídas, verá apenas dois caminhos. Uma forma de verificar isto é certificar-se de que pode chegar a ambas as interfaces de hospedar a partir de uma interface de rede no dispositivo StorSimple. Terá de contactar o [Microsoft Support,](storsimple-8000-contact-microsoft-support.md) uma vez que esta verificação só pode ser feita através de uma sessão de suporte.

P. Quando listar os caminhos disponíveis, não vejo qualquer saída.

R. Normalmente, não ver nenhum caminho multipata sugere um problema com o daemon multipatizador, e `multipath.conf` é mais provável que qualquer problema aqui esteja no ficheiro.

Também valeria a pena verificar se pode realmente ver alguns discos depois de ligar ao alvo, uma vez que nenhuma resposta das listas multipatas também pode significar que você não tem nenhum disco.

* Utilize o seguinte comando para redigitalizar o autocarro SCSI:
  
    `$ rescan-scsi-bus.sh`(parte do pacote sg3_utils)
* Digite os seguintes comandos:
  
    `$ dmesg | grep sd*`
     
     Ou
  
    `$ fdisk -l`
  
    Estes devolverão detalhes de discos recentemente adicionados.
* Para determinar se se trata de um disco StorSimple, utilize os seguintes comandos:
  
    `cat /sys/block/<DISK>/device/model`
  
    Isto devolverá uma corda, que determinará se é um disco StorSimple.

Uma causa menos provável, mas possível, também pode ser iscsid pid. Utilize o seguinte comando para sair das sessões iSCSI:

    iscsiadm -m node --logout -p <Target_IP>

Repita este comando para todas as interfaces de rede conectadas no alvo iSCSI, que é o seu dispositivo StorSimple. Depois de ter iniciado o registo de todas as sessões iSCSI, utilize o qin-alvo iSCSI para restabelecer a sessão iSCSI. Escreva o seguinte comando:

    iscsiadm -m node --login -T <TARGET_IQN>


P. Não sei se o meu dispositivo está na lista branca.

R. Para verificar se o seu dispositivo está na lista branca, utilize o seguinte comando interativo de resolução de problemas:

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


Para mais informações, vá a resolução de [problemas para multipatia.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot)

## <a name="list-of-useful-commands"></a>Lista de comandos úteis
| Tipo | Comando | Descrição |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |Iniciar o serviço iSCSI |
| &nbsp; |`service iscsid stop` |Parar o serviço iSCSI |
| &nbsp; |`service iscsid restart` |Reiniciar o serviço iSCSI |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Descubra os alvos disponíveis no endereço especificado |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Inicie sessão no alvo iSCSI |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Saque do alvo iSCSI |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Imprimir nome de iniciador iSCSI |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Verifique o estado da sessão iSCSI e o volume descoberto no hospedeiro |
| &nbsp; |`iscsi -m session` |Mostra todas as sessões iSCSI estabelecidas entre o anfitrião e o dispositivo StorSimple |
|  | | |
| **Multipathing** |`service multipathd start` |Iniciar daemon multi-path |
| &nbsp; |`service multipathd stop` |Pare o daemon multi-path |
| &nbsp; |`service multipathd restart` |Reiniciar o daemon multipata |
| &nbsp; |`chkconfig multipathd on` </br> OU </br> `mpathconf -with_chkconfig y` |Ativar o daemon multipata para começar no tempo de arranque |
| &nbsp; |`multipathd -k` |Inicie a consola interativa para resolução de problemas |
| &nbsp; |`multipath -l` |Listar ligações e dispositivos multipatas |
| &nbsp; |`mpathconf --enable` |Criar um ficheiro mulitpath.conf de amostra em`/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Passos seguintes
Como está a configurar mpio no anfitrião Linux, também pode ter de se referir aos seguintes documentos CentoS 6.6:

* [Criação de MPIO no CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Guia de treino linux](http://linux-training.be/linuxsys.pdf)

