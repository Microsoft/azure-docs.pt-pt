---
title: Alta disponibilidade criada com STONITH para SAP HANA em Azure (Grandes Instâncias)[ Microsoft Docs
description: Estabelecer uma elevada disponibilidade para o SAP HANA em Azure (Grandes Instâncias) em SUSE utilizando o STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4060dbe936af8ff1f9dd8c958f64834cb06525de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77615092"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>High availability set up in SUSE using the STONITH (Utilizar STONITH para configurar a elevada disponibilidade em SUSE)
Este documento fornece as instruções detalhadas passo a passo para configurar o sistema operativo De Alta Disponibilidade no Sistema Operativo SUSE utilizando o dispositivo STONITH.

**Isenção** de responsabilidade: *Este guia é derivado testando a configuração no ambiente Microsoft HANA Large Instances, que funciona com sucesso. Uma vez que a equipa de Gestão de Serviços da Microsoft para as grandes instâncias hana não suporta o sistema operativo, poderá ter de contactar a SUSE para mais resolução de problemas ou esclarecimentos sobre a camada do sistema operativo. A equipa de gestão de serviços da Microsoft configura o dispositivo STONITH e suporta totalmente e pode estar envolvida para resolução de problemas para problemas com dispositivos STONITH.*
## <a name="overview"></a>Descrição geral
Para configurar a alta disponibilidade utilizando o agrupamento SUSE, os seguintes pré-requisitos devem satisfazer.
### <a name="pre-requisites"></a>Pré-requisitos
- HANA Grandes Instâncias são provisionadas
- Sistema operativo está registado
- Os servidores HANA Large Instances estão ligados ao servidor SMT para obter patches/pacotes
- O sistema operativo tem as últimas correções instaladas
- NTP (servidor de tempo) está configurado
- Leia e compreenda a mais recente versão da documentação suse na configuração ha

### <a name="setup-details"></a>Detalhes da configuração
Este guia utiliza a seguinte configuração:
- Sistema Operativo: SLES 12 SP1 para SAP
- GRANDES Instâncias HANA: 2xS192 (quatro tomadas, 2 TB)
- Versão HANA: HANA 2.0 SP1
- Nomes do servidor: sapprdhdb95 (nó1) e sapprdhdb96 (nó2)
- Dispositivo STONITH: dispositivo STONITH baseado em iSCSI
- NTP configurado em um dos nódosos de grande instância HANA

Quando configurar as grandes instâncias HANA com hSR, pode solicitar à equipa de Gestão de Serviços da Microsoft que configurar a STONITH. Se já é um cliente existente que tem as grandes instâncias HANA aprovisionadas e precisa de dispositivo STONITH configurado para as suas lâminas existentes, precisa fornecer as seguintes informações à equipa de Gestão de Serviços da Microsoft no formulário de pedido de serviço (SRF). Pode solicitar o formulário SRF através do Gestor de Conta Técnica ou do seu Contacto Microsoft para o embarque de big instância HANA. Os novos clientes podem solicitar o dispositivo STONITH no momento do fornecimento. Os inputs estão disponíveis no formulário de pedido de provisionamento.

- Nome do servidor e endereço IP do servidor (por exemplo, myhanaserver1, 10.35.0.1)
- Localização (por exemplo, US East)
- Nome do Cliente (por exemplo, Microsoft)
- SID - Identificador de sistema HANA (por exemplo, H11)

Uma vez configurado o dispositivo STONITH, a equipa de Gestão de Serviços da Microsoft fornece-lhe o nome do dispositivo SBD e o endereço IP do armazenamento iSCSI, que pode utilizar para configurar a configuração do STONITH. 

Para configurar o fim até ao fim do HA utilizando o STONITH, devem ser seguidos os seguintes passos:

1.  Identificar o dispositivo SBD
2.  Inicializar o dispositivo SBD
3.  Configurar o Cluster
4.  Configurar o Cão de Guarda Softdog
5.  Junte-se ao nó ao aglomerado
6.  Validar o cluster
7.  Configure os recursos para o cluster
8.  Testar o processo de failover

## <a name="1---identify-the-sbd-device"></a>1. Identificar o dispositivo SBD
Esta secção descreve como determinar o dispositivo SBD para a sua configuração depois de a equipa de gestão de serviços da Microsoft ter configurado o STONITH. **Esta secção aplica-se apenas ao cliente existente.** Se for um novo cliente, a equipa de gestão de serviços da Microsoft fornece-lhe o nome do dispositivo SBD e pode ignorar esta secção.

1.1 Modificar */etc/iscsi/initiatornae.isci* para 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

A gestão do serviço da Microsoft fornece esta corda. Modifique o ficheiro em **ambos os** nós, no entanto o número do nó é diferente em cada nó.

![initiatornae.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Modificar */etc/iscsi/iscsid.conf*: Definir *o nó.session.timeo.replacement_timeout=5* e *nó.startup = automático*. Modifique o ficheiro em **ambos os** nós.

1.3 Execute o comando de descoberta, mostra quatro sessões. Passa-o em ambos os nós.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Execute o comando para iniciar sessão no dispositivo iSCSI, mostra quatro sessões. Passa-o em **ambos os** nós.

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Executar o guião rescano: *rescan-scsi-bus.sh*.  Este guião mostra-lhe os novos discos criados para si.  Passa-o em ambos os nós. Deve ver um número LUN superior a zero (por exemplo: 1, 2 etc.)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Para que o nome do dispositivo seja executado o disco de comando *(l*. ). Passa-o em ambos os nós. Escolha o dispositivo com o tamanho de **178 MiB**.

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Inicializar o dispositivo SBD

2.1 Inicializar o dispositivo SBD em **ambos os** nós

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Verifique o que foi escrito no dispositivo. Faça-o em **ambos os** nós

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Configurar o Cluster
Esta secção descreve os passos para configurar o cluster SUSE HA.
### <a name="31-package-installation"></a>3.1 Instalação de pacotes
3.1.1 Verifique se estão instalados ha_sles e os padrões SAPHanaSR-doc. Se não estiver instalado, instale-os. Instale-o em **ambos os** nós.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Configuração do cluster
3.2.1 Pode utilizar o comando *ha-cluster-init* ou utilizar o assistente yast2 para configurar o cluster. Neste caso, o feiticeiro yast2 é usado. Você executa este passo **apenas no nó primário.**

Siga yast2> Alta ![Disponibilidade > Cluster yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Clique **em cancelar** uma vez que o pacote halk2 já está instalado.

![yast-falcão-continuar.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Clique em **Continuar**

Valor esperado=Número de nós implantados (neste ![caso 2)](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) yast-Cluster-Security.png Clique **Próximo**
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) Adicione nomes de nó e clique em "Adicionar ficheiros sugeridos"

Clique em "Ligar csync2 ON"

Clique em "Generate Pre-Shared-Keys", mostra abaixo popup

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Clique **OK**

A autenticação é realizada utilizando os endereços IP e as teclas pré-partilhadas em Csync2. O ficheiro chave é gerado com csync2 -k /etc/csync2/key_hagroup. O ficheiro key_hagroup deve ser copiado para todos os membros do cluster manualmente após a sua criação. **Certifique-se de copiar o ficheiro do nó 1 ao nó2**.

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Clique em **Next**
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Na opção padrão, o Booting estava desligado, mude-o para "on" para que o pacemaker seja iniciado no arranque. Pode fazer a escolha com base nos seus requisitos de configuração.
Clique em **Seguinte** e a configuração do cluster está completa.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Configurar o Cão de Guarda Softdog
Esta secção descreve a configuração do cão de guarda (softdog).

4.1 Adicione a seguinte linha a */etc/init.d/boot.local* em **ambos os** nós.
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Atualize o ficheiro */etc/sysconfig/sbd* em **ambos os** nós como seguintes:
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Carregue o módulo kernel em **ambos os** nós, executando o seguinte comando
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Verifique e certifique-se de que o softdog está a correr como seguindo **em ambos os** nós:
```
lsmod | grep dog
```
![Lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Inicie o dispositivo SBD em **ambos os** nós
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Teste o daemon SBD em **ambos os** nós. Vê-se duas entradas depois de configurá-la em **ambos os** nós
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Envie uma mensagem de teste a **um** dos seus nódosos
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 No **segundo** nó (nó2) pode verificar o estado da mensagem
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Para adotar a config dsíbe, atualize o ficheiro */etc/sysconfig/sbd* como seguinte. Atualize o ficheiro em **ambos os** nós
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Inicie o serviço de pacemaker no **nó primário** (nó1)
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se o serviço pacemaker *falhar,* consulte o *Cenário 5:* O serviço Pacemaker falha

## <a name="5---joining-the-cluster"></a>5. Aderir ao cluster
Esta secção descreve como juntar o nó ao cluster.

### <a name="51-add-the-node"></a>5.1 Adicione o nó
Executar o seguinte comando no **nó2** para deixar o nó2 juntar-se ao cluster.
```
ha-cluster-join
```
Se receber um *erro* durante a junção do cluster, consulte o *Cenário 6: Nó 2 incapaz de se juntar ao cluster*.

## <a name="6---validating-the-cluster"></a>6. Validação do cluster

### <a name="61-start-the-cluster-service"></a>6.1 Inicie o serviço de cluster
Para verificar e iniciar opcionalmente o cluster pela primeira vez em **ambos os** nós.
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Monitorizar o estado
Execute o comando *crm_mon* para garantir que **ambos** os nós estão on-line. Pode executá-lo em **qualquer um dos nós** do cluster
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) Também pode iniciar sessão no hawk para verificar o estado do cluster *https://\<nó IP>:7630*. O utilizador predefinido é hacluster e a palavra-passe é linux. Se necessário, pode alterar a palavra-passe utilizando o comando *passwd.*

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configurar propriedades e recursos de cluster 
Esta secção descreve os passos para configurar os recursos do cluster.
Neste exemplo, configurar o seguinte recurso, o resto pode ser configurado (se necessário) fazendo referência ao guia SUSE HA. Execute o config apenas **num dos nódosos.** Faça no nó principal.

- Armadilha de botas cluster
- Dispositivo STONITH
- O endereço IP virtual


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Aprisionação de botas cluster e muito mais
Adicione a armadilha de botas de cluster. Crie o ficheiro e adicione o texto como seguinte:
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

### <a name="72-stonith-device"></a>7.2 Dispositivo STONITH
Adicione recurso STONITH. Crie o ficheiro e adicione o texto como seguinte.
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

### <a name="73-the-virtual-ip-address"></a>7.3 O endereço IP virtual
Adicione o IP virtual de recursos. Crie o ficheiro e adicione o texto como abaixo.
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

### <a name="74-validate-the-resources"></a>7.4 Validar os recursos

Quando se dirige o comando *crm_mon,* pode-se ver os dois recursos.
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Além disso, pode ver o estado no *endereço IP do nó https://\<>:7630/cib/live/state*

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testar o processo de failover
Para testar o processo de failover, pare o serviço de pacemaker no nó1 e os recursos falham no nó2.
```
Service pacemaker stop
```
Agora, pare o serviço de pacemaker no **nó2** e os recursos falharam no **nó1**

**Antes do fracasso**  
![Antes do fracasso.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Depois do fracasso**  
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Resolução de problemas
Esta secção descreve os poucos cenários de falha, que podem ser encontrados durante a configuração. Pode não necessariamente encarar estas questões.

### <a name="scenario-1-cluster-node-not-online"></a>Cenário 1: Nó de cluster não online
Se algum dos nós não aparecer online no cluster manager, pode tentar segui-lo online.

Inicie o serviço iSCSI
```
service iscsid start
```

E agora você deve ser capaz de iniciar sessão naquele nó iSCSI
```
iscsiadm -m node -l
```
A saída esperada parece seguir
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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Cenário 2: yast2 não mostra vista gráfica
O ecrã gráfico yast2 é usado para configurar o cluster de alta disponibilidade neste documento. Se o yast2 não abrir com a janela gráfica como mostrado e lançar erro Qt, execute os passos como se seguisse. Se abrir com a janela gráfica, pode saltar os degraus.

**Erro**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Saída esperada**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se o yast2 não abrir com a vista gráfica, siga os passos seguintes.

Instale os pacotes necessários. Tem de ser registado como "raiz" do utilizador e ter SMT configurado para descarregar/instalar as embalagens.

Para instalar os pacotes, utilize o yast>Software>Software Management>Dependencies> opção "Instalar pacotes recomendados...". A imagem que se segue ilustra os ecrãs esperados.
>[!NOTE]
>Você precisa realizar os passos em ambos os nós, para que você possa aceder à vista gráfica yast2 de ambos os nós.

![gestão yast-sofware.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Sob Dependências, selecione "Instalar pacotes recomendados" ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Reveja as alterações e bata OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

A instalação ![do pacote prossegue yast-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Clique em Seguinte

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Clique em Terminar

Também precisa de instalar os pacotes libqt4 e libyui-qt.
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) Yast2 deve ser capaz de abrir a vista gráfica agora, como mostrado aqui.
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Cenário 3: yast2 não tem opção de Alta Disponibilidade
Para que a opção de Alta Disponibilidade seja visível no centro de controlo yast2, é necessário instalar as embalagens adicionais.

Utilização do Yast2>software>gestão de software>Selecione os seguintes padrões

- Base de servidor SAP HANA
- Compilador C/C++
- Elevada disponibilidade
- Base do servidor de aplicação SAP

O ecrã seguinte mostra os passos para instalar os padrões.

Utilização de yast2 > Software > Software Management

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecione os padrões

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Clique em **Aceitar**

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Clique em **Continuar**

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Clique em **seguida** quando a instalação estiver concluída

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Cenário 4: Instalação HANA falha com erro de conjuntos gcc
A instalação HANA falha com o seguinte erro.

![Hana-instalação-erro.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corrigir o problema, é necessário instalar bibliotecas (libgcc_sl e libstdc++6) como a seguir.

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Cenário 5: Falha no serviço pacemaker

O seguinte problema ocorreu durante o início do serviço do pacemaker.

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

Para corrigi-lo, elimine a seguinte linha do ficheiro */usr/lib/systemd/system/fstrim.timer*

```
Persistent=true
```

![Persistente.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Cenário 6: Nó 2 incapaz de aderir ao cluster

Ao juntar o nó2 ao aglomerado existente utilizando o comando de *junção ha-cluster,* ocorreu o seguinte erro.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corrigir, executar o seguinte em ambos os nós

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2. PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Após a correção anterior, o nó2 deve ser adicionado ao cluster

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentação Geral
Pode encontrar mais informações sobre a configuração da SUSE HA nos seguintes artigos: 

- [Cenário otimizado de desempenho sAP HANA SR](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [Esgrima baseada em armazenamento](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Utilização do Cluster Pacemaker para SAP HANA- Parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Usando o Cluster Pacemaker para SAP HANA- Parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
