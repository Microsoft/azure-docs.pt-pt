---
title: Elevada disponibilidade que configurar com STONITH para SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Estabelecer elevada disponibilidade para SAP HANA no Azure (instâncias grandes) no SUSE usando o STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66973ce78004d0f29d08264869f166202aaaf109
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011846"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Elevada disponibilidade no SUSE usando o STONITH
Este documento fornece instruções passo a passo detalhadas para configurar a elevada disponibilidade no sistema operativo SUSE a utilizar o dispositivo STONITH.

**Exclusão de responsabilidade:** *Este guia é obtido ao testar a configuração no ambiente Microsoft HANA nas instâncias grandes, que funciona com êxito. Como a equipa de gestão de serviço da Microsoft para instâncias grandes do HANA não suporta o sistema operacional, poderá ter de contactar o SUSE para qualquer resolução ou esclarecimentos adicionais sobre a camada do sistema operativo. Equipe de gerenciamento de serviço do Microsoft configurar o dispositivo STONITH e totalmente suporta e pode estar envolvido para resolução de problemas para problemas de dispositivos STONITH.*
## <a name="overview"></a>Descrição geral
Para configurar a elevada disponibilidade com o clustering de SUSE, tem de cumprir os seguintes pré-requisitos.
### <a name="pre-requisites"></a>Pré-requisitos
- Instâncias grandes do HANA aprovisionadas
- Sistema operativo está registado
- Servidores de instâncias grandes do HANA estão ligados ao servidor SMT obter patches/pacotes
- Sistema operativo tem mais recentes patches instalados
- Configuração do NTP (servidor de horas)
- Leia e compreenda a versão mais recente da documentação de SUSE no programa de configuração HA

### <a name="setup-details"></a>Detalhes de configuração
Este guia utiliza a seguinte configuração:
- Sistema operativo: SP1 de 12 do SLES para SAP
- Instâncias grandes do HANA: 2xS192 (quatro soquetes, 2 TB)
- Versão do HANA: HANA 2.0 SP1
- Nomes de servidor: sapprdhdb95 (node1) e sapprdhdb96 (node2)
- : STONITH iSCSI com base no dispositivo dispositivo STONITH
- NTP configurar num de nós de instâncias grandes do HANA

Quando configurar instâncias grandes do HANA com HSR, pode pedir uma equipe de gerenciamento de serviços da Microsoft para configurar a STONITH. Se já for um cliente existente que tenha de instâncias grandes do HANA aprovisionado e a necessidade de dispositivo STONITH definida para seus painéis existentes, terá de fornecer as seguintes informações para a equipe de gerenciamento de serviços da Microsoft no formulário de pedido de serviço (SRF). Pode solicitar formulário SRF através do gestor técnico de conta ou o seu contacto da Microsoft para a integração de instância grande do HANA. Os novos clientes podem solicitar dispositivo STONITH ao tempo de aprovisionamento. As entradas estão disponíveis no formulário de pedido de aprovisionamento.

- Nome do servidor e servidor o endereço IP (por exemplo, 10.35.0.1, myhanaserver1)
- Localização (por exemplo, E.u.a. Leste)
- Nome do cliente (por exemplo, Microsoft)
- SID - Identificador de sistema HANA (por exemplo, H11)

Depois do dispositivo STONITH estiver configurado, a equipe de gerenciamento de serviços da Microsoft fornecem o nome do dispositivo SBD e o endereço IP do armazenamento de iSCSI, o que pode utilizar para configurar o programa de configuração STONITH. 

Para configurar a HA de ponto a ponto com STONITH, os seguintes passos tem de ser seguidas:

1.  Identificar o dispositivo SBD
2.  Inicializar o dispositivo SBD
3.  Configurar o Cluster
4.  Como configurar o Watchdog Softdog
5.  Junte-se o nó ao cluster
6.  Validar o cluster
7.  Configurar os recursos para o cluster
8.  Testar o processo de ativação pós-falha

## <a name="1---identify-the-sbd-device"></a>1.   Identificar o dispositivo SBD
Esta secção descreve como determinar se o dispositivo SBD para a sua configuração equipa de gestão do serviço Microsoft configurou o STONITH. **Esta secção aplica-se apenas para o cliente existente**. Se for um novo cliente, a equipe de gerenciamento de serviço do Microsoft fornecem SBD nome do dispositivo para e pode ignorar esta secção.

1.1 modificar */etc/iscsi/initiatorname.isci* para 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Gestão de serviço da Microsoft fornecem essa cadeia de caracteres. Modificar o ficheiro nos **ambos** os nós, no entanto o número de nó é diferente em cada nó.

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 modificar */etc/iscsi/iscsid.conf*: Definir *node.session.timeo.replacement_timeout=5* e *node.startup = automática*. Modificar o ficheiro nos **ambos** os nós.

1.3 executar o comando de deteção, mostra quatro sessões. Executá-lo em ambos os nós.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 executar o comando para iniciar sessão no dispositivo de iSCSI, mostra quatro sessões. Executá-lo no **ambos** os nós.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 execute o script de reanálise: *reanálise-scsi-bus.sh*.  Este script mostra novos discos criados para si.  Executá-lo em ambos os nós. Deverá ver um número do LUN que é maior que zero (por exemplo: 1, 2 etc.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 para obter o nome de dispositivo que execute o comando *fdisk – l*. Executá-lo em ambos os nós. Escolha o dispositivo com o tamanho de **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Inicializar o dispositivo SBD

2.1 inicializar o dispositivo SBD no **ambos** os nós

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Verifique o que foi escrito para o dispositivo. Fazê-lo **ambos** os nós

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Configurar o Cluster
Esta secção descreve os passos para configurar o cluster SUSE HA.
### <a name="31-package-installation"></a>3.1 instalação do pacote de
3.1.1 volte a verificar se ha_sles e padrões de documento SAPHanaSR estão instalados. Se não estiver instalado, instale-os. Instale-o num **ambos** os nós.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 como configurar o cluster
3.2.1 é possível usar *ha-cluster-init* comando ou utilize o Assistente de yast2 para configurar o cluster. Neste caso, o Assistente de yast2 é utilizado. Efetue este passo **apenas no nó principal**.

Siga yast2 > elevada disponibilidade > Cluster ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Clique em **Cancelar** , uma vez que o pacote de halk2 já está instalado.

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Clique em **continuar**

Valor esperado = número de nós implementados (no caso 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) clique em **próxima**
![yast-cluster-configurar-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) adicionar nomes de nó e clique em "adicionam ficheiros sugeridos"

Clique em "Ligar csync2"

Clique em "Gerar Pre-Shared-Keys", mostra abaixo pop-up

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Clique em **OK**

A autenticação é realizada usando os endereços IP e o pre-shared-chaves no Csync2. O ficheiro de chave é gerado com csync2 -k /etc/csync2/key_hagroup. Manualmente o ficheiro key_hagroup deve ser copiado para todos os membros do cluster depois de criado. **Certifique-se para copiar o ficheiro de nó 1 para node2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Click **Next**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

A opção predefinida, inicializando estivesse desativada, alterá-la como "ativado" para que pacemaker é iniciado no arranque. Pode fazer a escolha com base nos seus requisitos de configuração.
Clique em **seguinte** e a configuração de cluster está concluída.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Como configurar o Watchdog Softdog
Esta secção descreve a configuração da watchdog (softdog).

4.1 Adicione a seguinte linha ao */etc/init.d/boot.local* nos **ambos** os nós.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 atualizar o ficheiro */etc/sysconfig/sbd* nos **ambos** os nós do seguinte:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 carregar o módulo de kernel no **ambos** os nós ao executar o seguinte comando
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 verificar e certifique-se de que softdog está em execução como o seguinte procedimento no **ambos** os nós:
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 iniciar o dispositivo SBD **ambos** os nós
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 testar o daemon SBD **ambos** os nós. Verá duas entradas depois de configurar no **ambos** os nós
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 enviar uma mensagem de teste **um** de seus nós
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 diante da **segundo** nó (node2), pode verificar o estado de mensagem
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 para adotar a configuração de sbd, atualize o ficheiro */etc/sysconfig/sbd* seguinte. Atualizar o ficheiro no **ambos** os nós
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 iniciar o serviço de pacemaker no **nó primário** (node1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se o serviço de pacemaker *falhar*, consulte *cenário 5: Falha de serviço pacemaker*

## <a name="5---joining-the-cluster"></a>5.   Associar o cluster
Esta secção descreve como associar o nó ao cluster.

### <a name="51-add-the-node"></a>5.1 adicionar o nó
Execute o seguinte comando **node2** para permitir que node2 aderirem ao cluster.
```
ha-cluster-join
```
Se receber um *erro* durante a associação do cluster, consulte *cenário 6: 2 não é possível adicionar o cluster de nó*.

## <a name="6---validating-the-cluster"></a>6.   A validar o cluster

### <a name="61-start-the-cluster-service"></a>6.1 iniciar o serviço de cluster
Para verificar e, opcionalmente, inicie o cluster pela primeira vez no **ambos** nós.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 monitorizar o Estado
Execute o comando *crm_mon* para garantir **ambos** os nós estão online. Pode executá-lo **qualquer um de nós** do cluster
```
crm_mon
```
![CRM-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) também pode iniciar sessão para hawk para verificar o estado de cluster *https://<node IP>: 7630*. O utilizador predefinido é hacluster e a palavra-passe é o linux. Se for necessário, pode alterar a palavra-passe utilizando *passwd* comando.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configurar as propriedades do Cluster e recursos 
Esta secção descreve os passos para configurar os recursos do cluster.
Neste exemplo, configure o seguinte recurso, o restante pode ser configurado (se necessário) ao consultar o guia SUSE HA. Executar a configuração no **um de nós** apenas. Fazer no nó principal.

- Arranque de cluster
- Dispositivo STONITH
- O endereço Virtual IP


### <a name="71-cluster-bootstrap-and-more"></a>7.1 arranque de cluster e muito mais
Adicione o arranque de cluster. Criar o ficheiro e adicione o texto seguinte:
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
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 dispositivo STONITH
Adicione recurso STONITH. Criar o ficheiro e adicione o texto seguinte.
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

### <a name="73-the-virtual-ip-address"></a>7.3 o endereço IP virtual
Adicione o recurso IP virtual. Criar o ficheiro e adicione o texto tal como indicado abaixo.
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

### <a name="74-validate-the-resources"></a>7.4 validar os recursos

Quando executa o comando *crm_mon*, pode ver os dois recursos lá.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Além disso, pode ver o estado no *https://<node IP address>: 7630/cib/live/Estado*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. O processo de ativação pós-falha de teste
Para testar o processo de ativação pós-falha, pare o serviço de pacemaker no node1 e a ativação pós-falha de recursos para node2.
```
Service pacemaker stop
```
Agora, pare o serviço de pacemaker no **node2** e os recursos de ativação pós-falha **node1**

**Antes da ativação pós-falha**
![antes de failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)
**após a ativação pós-falha**
![após failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)
 ![ CRM segunda-feira após failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)


## <a name="9-troubleshooting"></a>9. Resolução de problemas
Esta secção descreve alguns cenários de falha, que podem ser encontrados durante a configuração. Não necessariamente poderá experimentar esses problemas.

### <a name="scenario-1-cluster-node-not-online"></a>Cenário 1: Nó de cluster não online
Se qualquer um de nós não mostra online no Gestor de clusters, pode tentar seguir para colocá-lo online.

Iniciar o serviço iSCSI
```
service iscsid start
```

E agora deve ser capaz de iniciar sessão para esse nó de iSCSI
```
iscsiadm -m node -l
```
O resultado esperado parece seguinte
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Cenário 2: yast2 não mostra a exibição gráfica
O ecrã de gráfico yast2 é utilizado para configurar o cluster de elevada disponibilidade neste documento. Se yast2 não aberto com a janela de gráfica, conforme mostrado e gerar Qt erro, execute os passos seguinte. Se for aberta com a janela de gráfica, pode ignorar os passos.

**Error**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Resultado esperado**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se o yast2 não for aberto com a exibição gráfica, siga os seguintes passos.

Instale os pacotes necessários. Deve fazer logon como utilizador "raiz" e ter SMT configurado para download/instalar os pacotes.

Para instalar os pacotes, utilize yast > Software > Gestão de Software > dependências > opção "Instalação recomendada pacotes...". Captura de ecrã seguinte ilustra os ecrãs esperados.
>[!NOTE]
>Terá de efetuar os passos em ambos os nós, para que possa acessar a exibição gráfica yast2 de ambos os nós de.

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Em dependências, selecione "Instalar pacotes recomendado" ![yast dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Reveja as alterações e clico em OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Pacote de instalação continua ![installation.png yast executar](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Clique em Seguinte

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Clique em Concluir

Também terá de instalar os pacotes libqt4 e libyui qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 deve ser capaz de abrir a vista de gráfica agora conforme mostrado aqui.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Cenário 3: yast2 faz não a opção de elevada disponibilidade
Para a opção de elevada disponibilidade para ser visível no Centro de controlo de yast2, terá de instalar os pacotes adicionais.

Usando Yast2 > Software > Gestão de Software > selecione os seguintes padrões

- Servidor SAP HANA base
- O compilador de C/C++ e ferramentas
- Elevada disponibilidade
- Servidor de aplicações SAP base

O ecrã seguinte mostra os passos para instalar os padrões.

Usando yast2 > Software > Gestão de Software

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecione os padrões de

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Clique em **aceite**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Clique em **continuar**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Clique em **seguinte** quando a instalação estiver concluída

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Cenário 4: Falha na instalação do HANA com erro de assemblies de gcc
A instalação de HANA falha com o seguinte erro.

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corrigir o problema, tem de instalar bibliotecas (libgcc_sl e libstdc + + 6) como o seguinte.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Cenário 5: Falha de serviço pacemaker

Ocorreu o seguinte problema durante o início do serviço pacemaker.

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

Para corrigi-lo, elimine a seguinte linha do arquivo */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Cenário 6: 2 não é possível adicionar o cluster de nó

Quando associar o node2 ao existente de cluster usando *ha-cluster-associação* de comando, Ocorreu o erro seguinte.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corrigir o problema, execute o seguinte nos dois nós

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Após a correção anterior, Nó2 deve obter adicionado ao cluster

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentação Geral
Pode encontrar mais informações sobre a configuração HA do SUSE nos seguintes artigos: 

- [SAP HANA SR desempenho optimizado do cenário](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Com base no armazenamento de bloqueio](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blogue - com o Cluster de Pacemaker para SAP HANA - parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blogue - com o Cluster de Pacemaker para SAP HANA - parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
