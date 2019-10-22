---
title: Configuração de alta disponibilidade com o STONITH para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Estabelecer alta disponibilidade para SAP HANA no Azure (instâncias grandes) no SUSE usando o STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f23fe2aa17934b967e7aecf41687cc555b9552c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71212526"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Configuração de alta disponibilidade no SUSE usando o STONITH
Este documento fornece as instruções passo a passo detalhadas para configurar a alta disponibilidade no sistema operacional SUSE usando o dispositivo STONITH.

**Isenção de responsabilidade:** *este guia é derivado testando a configuração no ambiente de instâncias grandes do Microsoft Hana, que funciona com êxito. Como a equipe de gerenciamento de serviços da Microsoft para instâncias grandes do HANA não oferece suporte ao sistema operacional, talvez seja necessário entrar em contato com o SUSE para obter mais informações ou esclarecimentos sobre a camada do sistema operacional. A equipe de gerenciamento de serviços da Microsoft configura o dispositivo STONITH e oferece suporte total e pode ser envolvida para solução de problemas de STONITH dispositivos.*
## <a name="overview"></a>Visão geral
Para configurar a alta disponibilidade usando o clustering SUSE, os pré-requisitos a seguir devem atender.
### <a name="pre-requisites"></a>Pré-requisitos
- Instâncias grandes HANA são provisionadas
- O sistema operacional está registrado
- Os servidores do HANA em instâncias grandes estão conectados ao servidor SMT para obter patches/pacotes
- O sistema operacional tem os patches mais recentes instalados
- O NTP (servidor de horário) está configurado
- Leia e entenda a versão mais recente da documentação do SUSE na configuração de HA

### <a name="setup-details"></a>Detalhes da instalação
Este guia usa a seguinte configuração:
- Sistema operacional: SLES 12 SP1 para SAP
- Instâncias grandes do HANA: 2xS192 (quatro soquetes, 2 TB)
- Versão do HANA: HANA 2,0 SP1
- Nomes de servidor: sapprdhdb95 (Node1) e sapprdhdb96 (NODE2)
- Dispositivo STONITH: dispositivo STONITH baseado em iSCSI
- Configuração de NTP em um dos nós de instância grande do HANA

Ao configurar instâncias grandes do HANA com o HSR, você pode solicitar que a equipe de gerenciamento de serviços da Microsoft configure o STONITH. Se você já é um cliente existente que tem o HANA em instâncias grandes provisionado e precisa configurar o dispositivo STONITH para suas folhas existentes, você precisa fornecer as seguintes informações para a equipe de gerenciamento de serviços da Microsoft no formulário de solicitação de serviço (SRF). Você pode solicitar o formulário SRF por meio do gerente técnico de conta ou seu contato da Microsoft para integração de instância grande do HANA. Os novos clientes podem solicitar o dispositivo STONITH no momento do provisionamento. As entradas estão disponíveis no formulário de solicitação de provisionamento.

- Nome do servidor e endereço IP do servidor (por exemplo, myhanaserver1, 10.35.0.1)
- Local (por exemplo, leste dos EUA)
- Nome do cliente (por exemplo, Microsoft)
- Identificador do sistema SID-HANA (por exemplo, H11)

Depois que o dispositivo STONITH estiver configurado, a equipe de gerenciamento de serviços da Microsoft fornecerá o nome do dispositivo SBD e o endereço IP do armazenamento iSCSI, que você pode usar para configurar a instalação do STONITH. 

Para configurar a alta disponibilidade de ponta a ponta usando o STONITH, as etapas a seguir precisam ser seguidas:

1.  Identificar o dispositivo SBD
2.  Inicializar o dispositivo SBD
3.  Configurando o cluster
4.  Configurando o Watchdog do SoftDog
5.  Ingressar o nó no cluster
6.  Validar o cluster
7.  Configurar os recursos para o cluster
8.  Testar o processo de failover

## <a name="1---identify-the-sbd-device"></a>1. identificar o dispositivo SBD
Esta seção descreve como determinar o dispositivo SBD para a sua configuração após a equipe de gerenciamento de serviços da Microsoft ter configurado o STONITH. **Esta seção se aplica somente ao cliente existente**. Se você for um novo cliente, a equipe de gerenciamento de serviços da Microsoft fornecerá o nome do dispositivo SBD para você e você poderá ignorar esta seção.

1,1 modificar */etc/iSCSI/initiatorname.iSCI* para 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

O gerenciamento de serviços da Microsoft fornece essa cadeia de caracteres. Modifique o arquivo em **ambos** os nós, no entanto, o número do nó é diferente em cada nó.

![iniciador. png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 modificar */etc/iSCSI/iscsid.conf*: definir *node. Session. time. replacement_timeout = 5* e *node. Startup = Automatic*. Modifique o arquivo em **ambos** os nós.

1,3 executar o comando de descoberta, ele mostra quatro sessões. Execute-o em ambos os nós.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery. png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 Execute o comando para fazer logon no dispositivo iSCSI. ele mostra quatro sessões. Execute-o em **ambos** os nós.

```
iscsiadm -m node -l
```
![iSCSIadmLogin. png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 execute o script de nova verificação: *Rescan-SCSI-Bus.sh*.  Esse script mostra os novos discos criados para você.  Execute-o em ambos os nós. Você deverá ver um número de LUN maior que zero (por exemplo: 1, 2 etc.)

```
rescan-scsi-bus.sh
```
![rescanscsibus. png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 para obter o nome do dispositivo, execute o comando *fdisk – l*. Execute-o em ambos os nós. Escolha o dispositivo com o tamanho de **178 MIB**.

```
  fdisk –l
```

![fdisk-l. png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Inicialize o dispositivo SBD

2,1 inicializar o dispositivo SBD em **ambos** os nós

```
sbd -d <SBD Device Name> create
```
![sbdcreate. png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 Verifique o que foi gravado no dispositivo. Faça isso em **ambos** os nós

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Configurando o cluster
Esta seção descreve as etapas para configurar o cluster SUSE HA.
### <a name="31-package-installation"></a>instalação do pacote 3,1
3.1.1 Verifique se os padrões ha_sles e SAPHanaSR-Doc estão instalados. Se não estiver instalado, instale-os. Instale-o em **ambos** os nós.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles. png ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![zypperpatternSAPHANASR-doc. png ](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 Configurando o cluster
3.2.1 você pode usar o comando *ha-cluster-init* ou usar o assistente de yast2 para configurar o cluster. Nesse caso, o assistente de yast2 é usado. Você executa essa etapa **somente no nó primário**.

Siga yast2 > cluster de > de alta disponibilidade ![yast-Control-Center. png ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![yast-Hawk-install. png ](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Clique em **Cancelar** , pois o pacote halk2 já está instalado.

![YaST-Hawk-continue. png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Clique em **continuar**

Valor esperado = número de nós implantados (nesse caso, 2) ![yast-Cluster-Security. png ](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) clique em **avançar** 
 ![yast-cluster-configure-csync2. png ](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) adicionar nomes de nós e clique em "adicionar arquivos sugeridos"

Clique em "ativar csync2"

Clique em "gerar chaves pré-compartilhadas", que aparece abaixo do pop-up

![YaST-Key-File. png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Clique em **OK**

A autenticação é executada usando os endereços IP e as chaves pré-compartilhadas em Csync2. O arquivo de chave é gerado com csync2-k/etc/csync2/key_hagroup. O arquivo key_hagroup deve ser copiado para todos os membros do cluster manualmente após sua criação. **Certifique-se de copiar o arquivo do nó 1 para NODE2**.

![YaST-cluster-conntrackd. png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Clique em **avançar** 
 ![yast-Cluster-Service. png ](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Na opção padrão, a inicialização foi desativada, altere-a para "on" para que pacemaker seja iniciado na inicialização. Você pode fazer a escolha com base nos requisitos de configuração.
Clique em **Avançar** e a configuração do cluster estará concluída.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Configurando o Watchdog do SoftDog
Esta seção descreve a configuração do Watchdog (SoftDog).

4,1 Adicione a seguinte linha a */etc/init.d/boot.local* em **ambos** os nós.
```
modprobe softdog
```
![modprobe-SoftDog. png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 Atualize o arquivo */etc/sysconfig/SBD* em **ambos** os nós da seguinte maneira:
```
SBD_DEVICE="<SBD Device Name>"
```
![SBD-Device. png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 Carregue o módulo kernel em **ambos** os nós executando o comando a seguir
```
modprobe softdog
```
![modprobe-SoftDog-Command. png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Verifique e verifique se o SoftDog está em execução da seguinte forma em **ambos** os nós:
```
lsmod | grep dog
```
![lsmod-grep-Dog. png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 iniciar o dispositivo SBD em **ambos** os nós
```
/usr/share/sbd/sbd.sh start
```
![SBD-sh-start. png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 teste o daemon SBD em **ambos** os nós. Você vê duas entradas depois de configurá-las em **ambos** os nós
```
sbd -d <SBD Device Name> list
```
![SBD-List. png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 enviar uma mensagem de teste para **um** de seus nós
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![SBD-List. png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 no **segundo** nó (NODE2), você pode verificar o status da mensagem
```
sbd  -d <SBD Device Name> list
```
![SBD-List-Message. png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 para adotar a configuração do SBD, atualize o arquivo */etc/sysconfig/SBD* da seguinte maneira. Atualizar o arquivo em **ambos** os nós
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 iniciar o serviço pacemaker no **nó primário** (Node1)
```
systemctl start pacemaker
```
![Start-pacemaker. png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se o serviço pacemaker *falhar*, consulte o *cenário 5: falha do serviço pacemaker*

## <a name="5---joining-the-cluster"></a>5. ingressando no cluster
Esta seção descreve como unir o nó ao cluster.

### <a name="51-add-the-node"></a>5,1 adicionar o nó
Execute o comando a seguir em **NODE2** para permitir que o NODE2 ingresse no cluster.
```
ha-cluster-join
```
Se você receber um *erro* durante a junção do cluster, consulte o *cenário 6: o nó 2 não pode ingressar no cluster*.

## <a name="6---validating-the-cluster"></a>6. Validando o cluster

### <a name="61-start-the-cluster-service"></a>6,1 iniciar o serviço de cluster
Para verificar e, opcionalmente, iniciar o cluster pela primeira vez em **ambos os** nós.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker. png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 monitorar o status
Execute o comando *crm_mon* para garantir que **ambos** os nós estejam online. Você pode executá-lo em **qualquer um dos nós** do cluster
```
crm_mon
```
![crm-Mon. png ](media/HowToHLI/HASetupWithStonith/crm-mon.png) você também pode fazer logon no Hawk para verificar o status do cluster *https://\<node IP >: 7630*. O usuário padrão é hacluster e a senha é Linux. Se necessário, você pode alterar a senha usando o comando *passwd* .

## <a name="7-configure-cluster-properties-and-resources"></a>7. configurar as propriedades e os recursos do cluster 
Esta seção descreve as etapas para configurar os recursos de cluster.
Neste exemplo, configure o recurso a seguir, o restante pode ser configurado (se necessário) referenciando o guia de HA do SUSE. Execute a configuração em apenas **um dos nós** . Fazer no nó primário.

- Inicialização do cluster
- Dispositivo STONITH
- O endereço IP virtual


### <a name="71-cluster-bootstrap-and-more"></a>inicialização do cluster 7,1 e mais
Adicionar inicialização de cluster. Crie o arquivo e adicione o texto da seguinte maneira:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Adicione a configuração ao cluster.
```
crm configure load update crm-bs.txt
```
![CRM-configure-crmbs. png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 dispositivo STONITH
Adicione o recurso STONITH. Crie o arquivo e adicione o texto como a seguir.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Adicione a configuração ao cluster.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 o endereço IP virtual
Adicionar IP virtual de recurso. Crie o arquivo e adicione o texto como mostrado abaixo.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Adicione a configuração ao cluster.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 validar os recursos

Ao executar o comando *crm_mon*, você pode ver os dois recursos ali.
](media/HowToHLI/HASetupWithStonith/crm_mon_command.png) ![crm_mon_command. png

Além disso, você pode ver o status em *https://\<node endereço IP >: 7630/CIB/Live/State*

![hawlk-status-Page. png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. testando o processo de failover
Para testar o processo de failover, pare o serviço pacemaker no Node1 e os recursos failover para NODE2.
```
Service pacemaker stop
```
Agora, pare o serviço pacemaker no **NODE2** e os recursos com failover para **Node1**

**Antes do failover**  
![Before-failover. png ](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Após o failover**  
![after-failover. png ](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-Mon-After-failover. png ](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. solução de problemas
Esta seção descreve alguns cenários de falha que podem ser encontrados durante a instalação. Você não pode necessariamente enfrentar esses problemas.

### <a name="scenario-1-cluster-node-not-online"></a>Cenário 1: o nó de cluster não está online
Se qualquer um dos nós não aparecer online no Gerenciador de cluster, você poderá tentar seguir para colocá-lo online.

Iniciar o serviço iSCSI
```
service iscsid start
```

E agora você deve ser capaz de fazer logon nesse nó iSCSI
```
iscsiadm -m node -l
```
A saída esperada é parecida com a seguinte
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Cenário 2: o yast2 não mostra a exibição gráfica
A tela gráfica yast2 é usada para configurar o cluster de alta disponibilidade neste documento. Se yast2 não abrir com a janela gráfica, conforme mostrado e emitir o erro Qt, execute as etapas a seguir. Se ele for aberto com a janela gráfica, você poderá ignorar as etapas.

**Ao**

![yast2-Qt-GUI-Error. png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Saída esperada**

![YaST-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se o yast2 não abrir com a exibição gráfica, siga as etapas a seguir.

Instale os pacotes necessários. Você deve estar conectado como "raiz" do usuário e ter o SMT configurado para baixar/instalar os pacotes.

Para instalar os pacotes, use YaST > Software > gerenciamento de software > dependências > opção "instalar pacotes recomendados...". A captura de tela a seguir ilustra as telas esperadas.
>[!NOTE]
>Você precisa executar as etapas em ambos os nós, para que possa acessar a exibição gráfica do yast2 de ambos os nós.

![YaST-sofwaremanagement. png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Em dependências, selecione "instalar pacotes recomendados" ![yast-Dependencies. png ](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Revise as alterações e pressione OK

![YaST](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

A instalação do pacote prossegue ![yast-Performing-Installation. png ](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Clique em Seguinte

![YaST-Installation-Report. png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Clique em concluir

Você também precisa instalar os pacotes libqt4 e libyui-Qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4. png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui. png ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![zypper-install-ligyui_part2. png ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) yast2 deve ser capaz de abrir a exibição gráfica agora, como mostrado aqui.
![yast2-Control-Center. png ](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Cenário 3: o yast2 não é a opção de alta disponibilidade
Para que a opção de alta disponibilidade fique visível no centro de controle do yast2, você precisa instalar os pacotes adicionais.

Usando o yast2 > Software > gerenciamento de software > selecione os seguintes padrões

- Base do SAP HANA Server
- C/C++ compilador e ferramentas
- Elevada disponibilidade
- Base do servidor de aplicativos SAP

A tela a seguir mostra as etapas para instalar os padrões.

Usando o yast2 > Software > gerenciamento de software

![yast2-Control-Center. png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecionar os padrões

![yast-pattern1. png ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![yast-pattern2. png ](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Clique em **aceitar**

![YaST-Changed-Packages. png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Clique em **continuar**

![yast2-Performing-Installation. png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Clique em **Avançar** quando a instalação for concluída

![yast2-Installation-Report. png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Cenário 4: a instalação do HANA falha com erro de assemblies gcc
A instalação do HANA falha com o erro a seguir.

![Hana-Installation-Error. png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corrigir o problema, você precisa instalar bibliotecas (libgcc_sl e libstdc + + 6) da seguinte maneira.

![zypper-install-lib. png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Cenário 5: falha do serviço pacemaker

O problema a seguir ocorreu durante o início do serviço pacemaker.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Para corrigi-lo, exclua a seguinte linha do arquivo */usr/lib/systemd/System/fstrim.Timer*

```
Persistent=true
```

![Persistent. png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Cenário 6: o nó 2 não pode ingressar no cluster

Ao unir o NODE2 ao cluster existente usando o comando *ha-cluster-Join* , ocorreu o seguinte erro.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Ha-cluster-Join-Error. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corrigir, execute o seguinte em ambos os nós

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-Node1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-NODE2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Após a correção anterior, NODE2 deve ser adicionado ao cluster

![Ha-cluster-Join-Fix. png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentação geral
Você pode encontrar mais informações sobre a configuração do SUSE HA nos seguintes artigos: 

- [Cenário otimizado para desempenho do SAP HANA SR](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Isolamento baseado em armazenamento](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog-usando o cluster pacemaker para a SAP HANA-parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog-usando o cluster pacemaker para a SAP HANA-parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
