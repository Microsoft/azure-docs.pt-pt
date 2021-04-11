---
title: Alta disponibilidade configurada com STONITH para SAP HANA em Azure (Grandes Instâncias)| Microsoft Docs
description: Estabelecer alta disponibilidade para SAP HANA em Azure (Grandes Instâncias) em SUSE usando o STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3dd2a618f22036fd0826a99207d83a3add390c7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645322"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>High availability set up in SUSE using the STONITH (Utilizar STONITH para configurar a elevada disponibilidade em SUSE)
Este documento fornece as instruções detalhadas passo a passo para configurar a Alta Disponibilidade no sistema operativo SUSE utilizando o dispositivo STONITH.

**Aviso:** *Este guia é derivado através do teste da configuração no ambiente Microsoft HANA Large Instances, que funciona com sucesso. Dado que a equipa de Gestão de Serviços da Microsoft para HANA Large Instances não suporta o sistema operativo, poderá ter de contactar a SUSE para qualquer resolução de problemas ou esclarecimentos sobre a camada do sistema operativo. A equipa de gestão de serviços da Microsoft cria dispositivoS STONITH e suporta totalmente e pode estar envolvida na resolução de problemas para problemas com dispositivos STONITH.*
## <a name="overview"></a>Descrição Geral
Para configurar a alta disponibilidade utilizando o agrupamento SUSE, os seguintes pré-requisitos devem reunir-se.
### <a name="pre-requisites"></a>Pré-requisitos
- HANA Grandes Instâncias são a provisionadas
- O sistema operativo está registado
- Os servidores HANA Large Instances estão ligados ao servidor SMT para obter patches/pacotes
- O sistema operativo tem as mais recentes correções instaladas
- NTP (servidor de tempo) é configurado
- Leia e compreenda a mais recente versão da documentação da SUSE sobre a configuração do HA

### <a name="setup-details"></a>Detalhes da configuração
Este guia utiliza a seguinte configuração:
- Sistema Operativo: SLES 12 SP1 para SAP
- HANA Grandes Instâncias: 2xS192 (quatro tomadas, 2 TB)
- Versão HANA: HANA 2.0 SP1
- Nomes do servidor: sapprdhdb95 (nó1) e sapprdhdb96 (nó2)
- Dispositivo STONITH: dispositivo STONITH baseado em iSCSI
- NTP configurado em um dos acenos HANA Large Instance

Quando configurar HANA Large Instances com HSR, pode solicitar à equipa de Gestão de Serviços da Microsoft para configurar o STONITH. Se já é um cliente existente que tem HANA Large Instances a fornecê-lo, e precisa de dispositivo STONITH configurado para as suas lâminas existentes, precisa de fornecer as seguintes informações à equipa de Gestão de Serviços da Microsoft no formulário de pedido de serviço (SRF). Pode solicitar o formulário SRF através do Gestor de Conta Técnica ou do seu Contacto Microsoft para o embarque em grande instância da HANA. Os novos clientes podem solicitar o dispositivo STONITH no momento do fornecimento. Os inputs estão disponíveis no formulário de pedido de provisionamento.

- Nome do servidor e endereço IP do servidor (por exemplo, myhanaserver1, 10.35.0.1)
- Localização (por exemplo, EUA Leste)
- Nome do Cliente (por exemplo, Microsoft)
- SID - Identificador do Sistema HANA (por exemplo, H11)

Uma vez configurado o dispositivo STONITH, a equipa de Gestão de Serviços da Microsoft fornece-lhe o nome do dispositivo SBD e o endereço IP do armazenamento iSCSI, que pode utilizar para configurar a configuração STONITH. 

Para estabelecer o fim para o HA com recurso ao STONITH, é necessário seguir os seguintes passos:

1.  Identificar o dispositivo SBD
2.  Inicializar o dispositivo SBD
3.  Configurar o Cluster
4.  Configurar o Cão de Guarda Softdog
5.  Junte o nó ao cluster
6.  Validar o cluster
7.  Configure os recursos para o cluster
8.  Testar o processo de failover

## <a name="1---identify-the-sbd-device"></a>1. Identificar o dispositivo SBD
Esta secção descreve como determinar o dispositivo SBD para a sua configuração depois da equipa de gestão de serviços da Microsoft ter configurado o STONITH. **Esta secção aplica-se apenas ao cliente existente.** Se for um novo cliente, a equipa de gestão de serviços da Microsoft fornece o nome do dispositivo SBD e pode saltar esta secção.

1.1 Modificar */etc/iscsi/initiatorname.isci* para 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

A gestão do serviço da Microsoft fornece esta cadeia. Modifique o ficheiro em **ambos os** nós, no entanto o número do nó é diferente em cada nó.

![A screenshot mostra um ficheiro de iniciação com valores de Nome iniciador para um nó.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Modificar */etc/iscsi/iscsid.conf*: set *node.session.timeo.replacement_timeout=5* e *node.startup = automático*. Modifique o ficheiro em **ambos os** nós.

1.3 Execute o comando de descoberta, mostra quatro sessões. Passa-o nos dois nós.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![A screenshot mostra uma janela da consola com resultados do comando de descoberta isciadm.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Execute o comando para iniciar sessão no dispositivo iSCSI, mostrando quatro sessões. Passa-o nos **dois** nós.

```
iscsiadm -m node -l
```
![A screenshot mostra uma janela da consola com os resultados do comando do nó iscsiadm.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Execute o roteiro do rescan: *rescan-scsi-bus.sh*.  Este script mostra-lhe os novos discos criados para si.  Passa-o nos dois nós. Deve ver um número LUN superior a zero (por exemplo: 1, 2 etc.)

```
rescan-scsi-bus.sh
```
![A screenshot mostra uma janela da consola com os resultados do script.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Para que o nome do dispositivo seja executado o comando *fdisk -l*. Passa-o nos dois nós. Escolha o dispositivo com o tamanho de **178 MiB**.

```
  fdisk –l
```

![A screenshot mostra uma janela da consola com resultados do comando do disco F.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. Inicializar o dispositivo SBD

2.1 Inicialize o dispositivo SBD em **ambos os** nós

```
sbd -d <SBD Device Name> create
```
![A screenshot mostra uma janela da consola com o resultado do comando s b d criar.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Verifique o que foi escrito ao aparelho. Faça-o em **ambos os** nós

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. Configurar o Cluster
Esta secção descreve os passos para configurar o cluster SUSE HA.
### <a name="31-package-installation"></a>3.1 Instalação de pacotes
3.1.1 Verifique se estão instalados padrões de ha_sles e SAPHanaSR-doc. Se não estiver instalado, instale-os. Instale-o em **ambos os** nós.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![A screenshot mostra uma janela da consola com o resultado do comando padrão. ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![ A screenshot mostra uma janela da consola com o resultado do comando SAPHanaSR-doc.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Configuração do cluster
3.2.1 Pode utilizar o comando *ha-cluster-init* ou utilizar o assistente yast2 para configurar o cluster. Neste caso, o feiticeiro yast2 é usado. Executa este passo **apenas no nó primário.**

Siga yast2> Alta Disponibilidade > Cluster ![ Screenshot mostra o Centro de Controlo YaST com Alta Disponibilidade e Cluster selecionados. ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![ A screenshot mostra uma caixa de diálogo com opções de Instalação e Cancelamento.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Clique **em cancelar** uma vez que o pacote halk2 já está instalado.

![A screenshot mostra uma mensagem sobre a sua opção de cancelamento.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Clique **em Continuar**

Valor esperado=Número de nós implantados (neste caso 2) ![ Screenshot mostra a Segurança do Cluster com uma caixa de verificação Enable Security Auth.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
**Clique em 'Next** 
 ![ Screenshot' mostra a janela de configuração do Cluster com listas de sync host e Sync File.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Adicione nomes de nó e, em seguida, clique em "Adicionar ficheiros sugeridos"

Clique em "Turn csync2 ON"

Clique em "Gerar Chaves Pré-Partilhadas", mostra abaixo popup

![A screenshot mostra uma mensagem de que a sua chave foi gerada.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Clique **em OK**

A autenticação é realizada utilizando os endereços IP e as teclas pré-partilhadas no Csync2. O ficheiro chave é gerado com csync2 -k /etc/csync2/key_hagroup. O ficheiro key_hagroup deve ser copiado manualmente para todos os membros do cluster após a sua criação. **Certifique-se de copiar o ficheiro do nó 1 ao nó2**.

![A screenshot mostra uma caixa de diálogo cluster configure com opções necessárias para copiar a chave para todos os membros do cluster.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

**Clique em 'Next** 
 ![ Screenshot' mostra a janela do Serviço cluster.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Na opção padrão, booting estava desligado, alterá-lo para "on" para que o pacemaker seja iniciado no arranque. Pode fazer a escolha com base nos seus requisitos de configuração.
Clique **em Seguinte** e a configuração do cluster está completa.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Configuração do Cão de Guarda Softdog
Esta secção descreve a configuração do cão de guarda (softdog).

4.1 Adicione a seguinte linha a */etc/init.d/boot.local* em **ambos os** nós.
```
modprobe softdog
```
![A screenshot mostra um ficheiro de arranque com a linha softdog adicionada.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Atualizar o ficheiro */etc/sysconfig/sbd* em **ambos os** nós como seguinte:
```
SBD_DEVICE="<SBD Device Name>"
```
![A screenshot mostra o ficheiro s b d com o valor acrescentado de D_DEVICE S B.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Carregue o módulo de núcleo em **ambos os** nós executando o seguinte comando
```
modprobe softdog
```
![A screenshot mostra parte de uma janela da consola com o softdog modprobe de comando.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Verifique e certifique-se de que o softdog está a funcionar como se segue em **ambos os** nós:
```
lsmod | grep dog
```
![A screenshot mostra parte de uma janela da consola com o resultado de executar o comando l s mod.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Inicie o dispositivo SBD em **ambos os** nós
```
/usr/share/sbd/sbd.sh start
```
![A screenshot mostra parte de uma janela da consola com o comando de partida.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Teste o daemon SBD em **ambos os** nós. Vê duas entradas depois de configurar em **ambos os** nós
```
sbd -d <SBD Device Name> list
```
![A screenshot mostra parte de uma janela da consola que apresenta duas entradas.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Envie uma mensagem de teste a **um** dos seus nós
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![A screenshot mostra parte de uma janela da consola que apresenta duas entradas.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 No **segundo** nó (nó2) pode verificar o estado da mensagem
```
sbd  -d <SBD Device Name> list
```
![A screenshot mostra parte de uma janela da consola com um dos membros a apresentar um valor de teste para o outro membro.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Para adotar o sbd config, atualize o ficheiro */etc/sysconfig/sbd* como seguinte. Atualize o ficheiro em **ambos os** nós
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Inicie o serviço de marca-passo no **nó primário** (nó 1)
```
systemctl start pacemaker
```
![A screenshot mostra uma janela da consola que mostra o estado após o início do pacemaker.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Se o serviço pacemaker *falhar,* consulte o *Cenário 5: O serviço pacemaker falha*

## <a name="5---joining-the-cluster"></a>5. Juntar-se ao cluster
Esta secção descreve como juntar o nó ao cluster.

### <a name="51-add-the-node"></a>5.1 Adicione o nó
Executar o seguinte comando no **nó2** para deixar o nó2 juntar-se ao cluster.
```
ha-cluster-join
```
Se receber um *erro* durante a junção do cluster, consulte o *Cenário 6: Nó 2 incapaz de se juntar ao cluster*.

## <a name="6---validating-the-cluster"></a>6. Validação do cluster

### <a name="61-start-the-cluster-service"></a>6.1 Iniciar o serviço de cluster
Para verificar e iniciar opcionalmente o cluster pela primeira vez em **ambos os** nós.
```
systemctl status pacemaker
systemctl start pacemaker
```
![A screenshot mostra uma janela da consola com o estado do pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Monitorizar o estado
Executar o *comando crm_mon* para garantir que ambos **os** nós estão on-line. Pode executá-lo em **qualquer um dos nóns** do cluster
```
crm_mon
```
![A screenshot mostra uma janela da consola com os resultados de c r m_mon.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
Também pode iniciar sessão no falcão para verificar o estado do cluster *https:// \<node IP> :7630*. O utilizador predefinido é hacluster e a palavra-passe é linux. Se necessário, pode alterar a palavra-passe utilizando o comando *passwd.*

## <a name="7-configure-cluster-properties-and-resources"></a>7. Configure propriedades e recursos do cluster 
Esta secção descreve os passos para configurar os recursos do cluster.
Neste exemplo, configurar o seguinte recurso, o resto pode ser configurado (se necessário) fazendo referência ao guia SUSE HA. Execute o config apenas num **dos nós.** Faça no nó primário.

- Botas de cluster
- Dispositivo STONITH
- O Endereço IP Virtual


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Botão de botins cluster e muito mais
Adicione a bateria de botas de cluster. Crie o ficheiro e adicione o texto como seguinte:
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
![A screenshot mostra parte de uma janela da consola a executar o comando c r m.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

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
Adicione IP virtual de recurso. Crie o ficheiro e adicione o texto como abaixo.
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

Quando se dirige o comando *crm_mon,* pode-se ver os dois recursos lá.
![A screenshot mostra uma janela da consola com dois recursos.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Além disso, pode ver o estado em *https:// \<node IP address> :7630/cib/live/state*

![A imagem mostra o estado dos dois recursos.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testar o processo de failover
Para testar o processo de failover, pare o serviço de pacemaker no nó1 e os recursos não além do nó2.
```
Service pacemaker stop
```
Agora, pare o serviço de pacemaker no **nó2** e os recursos falharam para **nó1**

**Antes do failover**  
![A screenshot mostra o estado dos dois recursos antes do fracasso.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Depois do failover**  
![A screenshot mostra o estado dos dois recursos após o fracasso.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![A screenshot mostra uma janela da consola com o estado dos recursos após o failover.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Resolução de problemas
Esta secção descreve os poucos cenários de falha, que podem ser encontrados durante a configuração. Pode não enfrentar necessariamente estas questões.

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
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Cenário 2: yast2 não mostra visão gráfica
O ecrã gráfico yast2 é usado para configurar o cluster de Alta Disponibilidade neste documento. Se yast2 não abrir com a janela gráfica como mostrado e lançar erro Qt, execute os passos como seguinte. Se abrir com a janela gráfica, pode saltar os degraus.

**Erro**

![A screenshot mostra parte de uma janela da consola com uma mensagem de erro.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Saída prevista**

![O Screenshot mostra o Centro de Controlo YaST com Alta Disponibilidade e Cluster em destaque.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Se o yast2 não abrir com a vista gráfica, siga os passos seguintes.

Instale os pacotes necessários. Tem de iniciar sessão como "raiz" do utilizador e ter sMT configurado para descarregar/instalar as embalagens.

Para instalar os pacotes, utilize a opção yast>software>De gestão de software>Dependencies> opção "Instalar pacotes recomendados...". A imagem que se segue ilustra os ecrãs esperados.
>[!NOTE]
>Você precisa executar os passos em ambos os nós, para que você possa acessar a vista gráfica yast2 de ambos os nós.

![A imagem mostra uma janela da consola que exibe o Centro de Controlo YaST.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Em Dependências, selecione "Instalar pacotes recomendados" ![ Screenshot mostra uma janela da consola com pacotes recomendados de instalação selecionados.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Reveja as alterações e acerte OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

A instalação do pacote prossegue ![ O Screenshot mostra uma janela da consola que mostra o progresso da instalação.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Clique em Seguinte

![A screenshot mostra uma janela da consola com uma mensagem de sucesso.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Clique em Terminar

Também precisa de instalar os pacotes libqt4 e libyui-qt.
```
zypper -n install libqt4
```
![A screenshot mostra uma janela de consola que instala o pacote libqt4.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![A screenshot mostra uma janela de consola que instala o pacote libyui-qt. ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![ A screenshot mostra uma janela de consola que instala o pacote libyui-qt, continuada.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
Yast2 deve ser capaz de abrir a vista gráfica agora como mostrado aqui.
![A screenshot mostra o Centro de Controlo YaST com software e atualização on-line selecionados.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Cenário 3: yast2 não tem opção de Alta Disponibilidade
Para que a opção de Alta Disponibilidade seja visível no centro de controlo yast2, é necessário instalar os pacotes adicionais.

Utilizando>de gestão de software>>software yast2>selecione os seguintes padrões

- Base do servidor SAP HANA
- Compilador C/C++ e ferramentas
- Elevada disponibilidade
- Base do servidor de aplicações SAP

O ecrã seguinte mostra os passos para instalar os padrões.

Utilização de software yast2 > > software > Gestão de Software

![O Screenshot mostra o Centro de Controlo YaST com software e atualização on-line selecionado para iniciar a instalação.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Selecione os padrões

![A screenshot mostra selecionar o primeiro padrão no item C/C++ Compiler e Ferramentas. ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![ A screenshot mostra selecionar o segundo padrão no item C/C++ Compiler e Tools.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Clique **em Aceitar**

![O Screenshot mostra a caixa de diálogo De Pacotes Alterados com pacotes alterados para resolver dependências.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Clique **em Continuar**

![A screenshot mostra a página de estado da instalação de performing.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Clique **em seguida** quando a instalação estiver concluída

![A imagem mostra o relatório de instalação.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Cenário 4: Instalação HANA falha com erro de montagem do CCG
A instalação HANA falha com o seguinte erro.

![A screenshot mostra uma mensagem de erro de que o sistema operativo não está pronto para executar conjuntos g c c 5.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Para corrigir o problema, é necessário instalar bibliotecas (libgcc_sl e libstdc++6) como seguintes.

![A screenshot mostra uma janela da consola a instalar bibliotecas necessárias.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Cenário 5: Serviço pacemaker falha

O seguinte problema ocorreu durante o início do serviço pacemaker.

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

Para corrigi-lo, elimine a seguinte linha do ficheiro */usr/lib/system/fstrim.timer*

```
Persistent=true
```

![A screenshot mostra o ficheiro de guarnição f com o valor de Persistent=verdadeiro a ser eliminado.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Cenário 6: Nó 2 incapaz de aderir ao cluster

Ao juntar o nó2 ao cluster existente utilizando o comando *ha-cluster-join,* ocorreu o seguinte erro.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![A screenshot mostra uma janela da consola com a mensagem de erro Não pode recuperar as teclas S S H de um endereço I P.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Para corrigir, executar o seguinte em ambos os nós

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![A screenshot mostra parte de uma janela da consola a executar o comando no primeiro nó.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![A screenshot mostra parte de uma janela da consola a executar o comando no segundo nó.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Após a correção anterior, o nó2 deve ser adicionado ao cluster

![A screenshot mostra uma janela de consola com um comando de união ha-cluster bem-sucedido.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Documentação Geral
Pode encontrar mais informações sobre a configuração do SUSE HA nos seguintes artigos: 

- [Cenário otimizado do desempenho do SAP HANA SR](https://www.suse.com/support/kb/doc/?id=000019450 )
- [Esgrima baseada em armazenamento](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog - Utilização do Pacemaker Cluster para SAP HANA- Parte 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog - Utilização do Pacemaker Cluster para SAP HANA- Parte 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
