---
title: Alta disponibilidade de ESCALA SAP HANA com ANF em | RHEL Microsoft Docs
description: Estabelecer uma elevada disponibilidade de SAP HANA com ANF em máquinas virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: b90d703a44cf89961eb0dca02abb1b8ea9cff166
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259207"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Alta disponibilidade de ESCALA SAP HANA com ficheiros Azure NetApp no Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como configurar a replicação do sistema SAP HANA na implementação de escala, quando os sistemas de ficheiros HANA são montados através de NFS, utilizando ficheiros Azure NetApp (ANF). Nas configurações e comandos de instalação do exemplo, são utilizados os termos **03** e o ID **HN1** do sistema HANA. A replicação DE SAP HANA consiste num nó primário e pelo menos um nó secundário.

Quando os passos neste documento são marcados com os seguintes prefixos, o significado é o seguinte:

- **[A]**: O passo aplica-se a todos os nós
- **[1]**: O passo aplica-se apenas ao nó1
- **[2]**: O passo aplica-se apenas ao nó2
 
Leia primeiro as seguintes notas e artigos SAP:

- Nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533), que tem:
    - A lista de tamanhos Azure VM que são suportados para a implementação de software SAP.
    - Informações importantes sobre a capacidade dos tamanhos Azure VM.
    - O software SAP suportado, e as combinações de sistema operativo (OS) e base de dados.
    - A versão necessária para o kernel SAP para Windows e Linux no Microsoft Azure.
- O SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553) lista pré-requisitos para implementações de software SAP suportadas pelo SAP em Azure.
- O SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) lista o sistema de ficheiros recomendado para o ambiente HANA.
- O SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) recomendou as definições de SO para Red Hat Enterprise Linux.
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) tem diretrizes SAP HANA para Red Hat Enterprise Linux.
- A Nota [SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632) tem informações detalhadas sobre todas as métricas de monitorização reportadas para o SAP em Azure.
- Sap Note [2191498](https://launchpad.support.sap.com/#/notes/2191498) tem a versão necessária do Agente anfitrião SAP para Linux em Azure.
- Sap Nota [2243692](https://launchpad.support.sap.com/#/notes/2243692) tem informações sobre o licenciamento SAP em Linux em Azure.
- Sap Nota [1999351](https://launchpad.support.sap.com/#/notes/1999351) tem informações adicionais de resolução de problemas para a extensão de monitorização melhorada de Azure para o SAP.
- [A SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) exigiu todas as notas SAP para o Linux.
- [Azure Virtual Machines planejamento e implementação para SAP em Linux][planning-guide]
- [Implementação de Máquinas Virtuais Azure para SAP em Linux][deployment-guide]
- [Azure Virtual Machines DBMS implantação para SAP em Linux][dbms-guide]
- [Replicação do sistema SAP HANA no cluster pacemaker.](https://access.redhat.com/articles/3004101)
- Documentação geral do RHEL
    - [Visão geral Add-On de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Alta Disponibilidade Add-On Administração.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referência de Add-On de Alta Disponibilidade.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Configure a replicação do sistema SAP HANA em Scale-Up num cluster pacemaker quando os sistemas de ficheiros HANA estão em ações NFS](https://access.redhat.com/solutions/5156571)
- Documentação RHEL específica do Azure:
    - [Políticas de suporte para clusters de alta disponibilidade RHEL - Microsoft Azure Virtual Machines como Membros do Cluster.](https://access.redhat.com/articles/3131341)
    - [Instalar e Configurar um Red Hat Enterprise Linux 7.4 (e mais tarde) High-Availability Cluster no Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Instale o SAP HANA no Red Hat Enterprise Linux para utilização no Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Configurar a replicação do sistema de escala SAP HANA quando os sistemas de ficheiros HANA estiverem em ações NFS](https://access.redhat.com/solutions/5156571)
- [Aplicações NETApp SAP no Microsoft Azure utilizando ficheiros Azure NetApp](https://www.netapp.com/us/media/tr-4746.pdf)
- [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)

## <a name="overview"></a>Descrição Geral

Tradicionalmente em ambiente de escala, todos os sistemas de ficheiros para SAP HANA são montados a partir do armazenamento local. A criação de alta disponibilidade de replicação do sistema SAP HANA no Red Hat Enterprise Linux é publicada no guia [Configurar a replicação do sistema SAP HANA no RHEL](./sap-hana-high-availability-rhel.md)

Para conseguir a alta disponibilidade do sistema de escala SAP HANA nas ações NFS dos [Ficheiros Azure NetApp,](../../../azure-netapp-files/index.yml) precisamos de alguma configuração adicional de recursos no cluster, para que os recursos da HANA recuperem, quando um nó perde acesso às ações da NFS na ANF.  O cluster gere os suportes da NFS, permitindo-lhe monitorizar a saúde dos recursos. As dependências entre os suportes do sistema de ficheiros e os recursos SAP HANA são aplicadas.  

![Escala DE SAP HANA HA na ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

Os sistemas de ficheiros SAP HANA são montados em ações NFS utilizando ficheiros Azure NetApp em cada nó. Os sistemas de ficheiros /hana/dados, /hana/log e /hana/shared são exclusivos de cada nó. 

Montado no nó1 **(hanadb1)**

- 10.32.2.4:/**hanadb1**-data-mnt00001 em /hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 em /hana/log
- 10.32.2.4:/**hanadb1**-shared-mnt00001 on /hana/shared

Montado no nó2 **(hanadb2)**

- 10.32.2.4:/**hanadb2**-data-mnt00001 em /hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 em /hana/log
- 10.32.2.4:/**hanadb2**-shared-mnt00001 on /hana/shared

> [!NOTE]
> Os sistemas de ficheiros /hana/shared, /hana/data e /hana/log não são partilhados entre os dois nós. Cada nó de cluster tem os seus próprios sistemas de ficheiros separados.   

A configuração de replicação do sistema SAP HANA utiliza um nome de hospedeiro virtual dedicado e endereços IP virtuais. No Azure, é necessário um equilibrador de carga para utilizar um endereço IP virtual. A lista a seguir mostra a configuração do equilibrador de carga:

- Configuração frontal: endereço IP 10.32.0.10 para hn1-db
- Configuração traseira: Ligadas a interfaces de rede primárias de todas as máquinas virtuais que devem fazer parte da Replicação do Sistema HANA
- Porta sonda: Porto 62503
- Regras de equilíbrio de cargas: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (se utilizar o balançador de carga básica)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Configurar a infraestrutura de ficheiros Azure NetApp

Antes de prosseguir com a configuração para a infraestrutura Azure NetApp Files, familiarize-se com a documentação do Azure [NetApp Files](../../../azure-netapp-files/index.yml).

O Azure NetApp Files está disponível em várias [regiões do Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verifique se a região de Azure selecionada oferece ficheiros Azure NetApp.

Para obter informações sobre a disponibilidade de Ficheiros Azure NetApp por região do Azure, consulte [Azure NetApp Files Availability by Azure Region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Antes de implementar ficheiros Azure NetApp, solicite a bordo para ficheiros Azure NetApp indo para [registar instruções de Arquivos Azure NetApp](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Implementar recursos de ficheiros Azure NetApp

As seguintes instruções pressupõem que já implementou a sua [rede virtual Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos e VMs do Azure NetApp, onde serão montados os recursos do Azure NetApp Files, devem ser implantados na mesma rede virtual Azure ou em redes virtuais Azure.

1. Se ainda não implementou os recursos, solicite [a bordo para os Ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Crie uma conta NetApp na região Azure selecionada seguindo as instruções na [Criação de uma conta NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3.  Crie um pool de capacidade do Azure NetApp Files seguindo as instruções em [Configurar um conjunto de capacidades Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

    A arquitetura HANA apresentada neste artigo usa um único pool de capacidade Azure NetApp Files ao nível do *Serviço Ultra.* Para cargas de trabalho HANA em Azure, recomendamos a utilização de um [nível](../../../azure-netapp-files/azure-netapp-files-service-levels.md)de serviço Azure NetApp *Files Ultra* ou *Premium* .

4.  Delege uma sub-rede para ficheiros Azure NetApp, conforme descrito nas instruções em [Delegado uma sub-rede para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Implementar volumes de ficheiros Azure NetApp seguindo as instruções na [Criar um volume NFS para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    Ao implementar os volumes, não se esqueça de selecionar a versão NFSv4.1. Implemente os volumes na sub-rede designada Azure NetApp Files. Os endereços IP dos volumes Azure NetApp são atribuídos automaticamente.

    Tenha em mente que os recursos do Azure NetApp Files e os VMs Azure devem estar na mesma rede virtual Azure ou em redes virtuais Azure. Por exemplo, hanadb1-data-mnt00001, hanadb1-log-mnt00001, e assim por diante, são os nomes de volume e nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001, e assim por diante, são os caminhos de arquivo para os volumes dos Ficheiros Azure NetApp.
    
    Em **hanadb1**

    - Volume hanadb1-dados-mnt0001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt0001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-shared-mnt0001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    Em **hanadb2**

    - Volume hanadb2-dados-mnt0001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt0001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-shared-mnt0001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Considerações importantes

Como está a criar os seus Ficheiros Azure NetApp para sistemas SAP HANA Scale-up, esteja ciente da seguinte consideração:

- A capacidade mínima é de 4 tebibytes (TiB).
- O tamanho mínimo do volume é de 100 gibibytes (GiB).
- Os ficheiros Azure NetApp e todas as máquinas virtuais onde os volumes dos Ficheiros Azure NetApp serão montados devem estar na mesma rede virtual Azure ou em [redes virtuais na](../../../virtual-network/virtual-network-peering-overview.md) mesma região.
- A rede virtual selecionada deve ter uma sub-rede delegada nos Ficheiros Azure NetApp.
- O rendimento de um volume de Ficheiros Azure NetApp é uma função da quota de volume e do nível de serviço, conforme documentado no [nível de Serviço para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Quando estiver a dimensionar os volumes HANA Azure NetApp, certifique-se de que a produção resultante satisfaz os requisitos do sistema HANA.
- Com a política de [exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)do Azure NetApp Files, pode controlar os clientes autorizados, o tipo de acesso (ler-escrever, ler apenas, e assim por diante).
- A funcionalidade Azure NetApp Files ainda não está ciente da zona. Atualmente, a funcionalidade não está implantada em todas as zonas de disponibilidade numa região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure.

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é fundamental. Trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes dos Ficheiros Azure NetApp são implantados nas proximidades.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Dimensionamento da base de dados HANA em Ficheiros Azure NetApp

O resultado de um volume de Ficheiros Azure NetApp é uma função do tamanho e do nível de serviço do volume, conforme documentado no [nível de Serviço para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

Ao conceber a infraestrutura para SAP em Azure, esteja ciente de alguns requisitos mínimos de armazenamento por SAP, que se traduzem em características mínimas de produção:

- Leia-escreva em /hana/log de 250 megabytes por segundo (MB/s) com tamanhos de I/O de 1-MB.
- Leia a atividade de pelo menos 400 MB/s para /hana/data para tamanhos de 16-MB e 64-MB I/O.
- Escreva atividade de, pelo menos, 250 MB/s para /hana/dados com tamanhos de 16-MB e 64-MB I/O.

Os [limites de produção dos ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md) por 1 TiB da quota de volume são:

- Nível de armazenamento premium - 64 MiB/s.
- Nível de armazenamento ultra - 128 MiB/s.

Para satisfazer os requisitos mínimos de produção do SAP para /hana/dados e /hana/log, e as diretrizes para /hana/shared, os tamanhos recomendados seriam:

|    Volume    | Tamanho do nível de armazenamento premium | Tamanho do nível de armazenamento ultra | Protocolo NFS apoiado |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/dados  |           6.3 TiB            |          3.2 TiB           |          v4.1          |
| /hana/compartilhado |           1 x RAM            |          1 x RAM           |          v3 ou v4.1    |


> [!NOTE]
> As recomendações de dimensionamento dos Ficheiros Azure NetApp aqui declaradas são direcionadas para cumprir os requisitos mínimos que a SAP recomenda para os seus fornecedores de infraestruturas. Em situações reais de colocação de clientes e cenários de carga de trabalho, estes tamanhos podem não ser suficientes. Utilize estas recomendações como ponto de partida e adapte-se, com base nos requisitos da sua carga de trabalho específica.

> [!TIP]
> Pode redimensionar os volumes Azure NetApp Files dinamicamente, sem ter de *desmontar* os volumes, parar as máquinas virtuais ou parar o SAP HANA. Esta abordagem permite flexibilidade para satisfazer as exigências de produção esperadas e imprevistas da sua aplicação.

> [!NOTE]
> Todos os comandos para montar /hana/partilhado neste artigo são apresentados para volumes NFSv4.1 /hana/shared.
> Se tiver implantado os volumes /hana/shared como volumes NFSv3, não se esqueça de ajustar os comandos de montagem para /hana/partilhado para NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Implementar máquina virtual Linux através do portal Azure 

Primeiro, tem de criar os volumes dos Ficheiros Azure NetApp. Em seguida, faça os seguintes passos:

1.  Crie um grupo de recursos.
2.  Crie uma rede virtual
3.  Crie um conjunto de disponibilidade. Desave o domínio de atualização máxima.
4.  Criar um equilibrador de carga (interno). Recomendamos o balanceador de carga padrão.
    Selecione a rede virtual criada no passo 2.
5.  Criar Máquina Virtual 1 **(hanadb1).** 
6.  Criar Máquina Virtual 2 **(hanadb2).**  
7.  Ao criar uma máquina virtual, não adicionaremos nenhum disco, uma vez que todos os nossos pontos de montagem estarão nas ações da NFS a partir de Ficheiros Azure NetApp. 

> [!IMPORTANT]
> O IP flutuante não é suportado numa configuração IP secundária do NIC em cenários de equilíbrio de carga. Para mais detalhes consulte [as limitações do balançador de carga Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se precisar de um endereço IP adicional para o VM, implante um segundo NIC.    

> [!NOTE] 
> Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos. Para obter detalhes sobre como alcançar a conectividade de saída, consulte [a conectividade do ponto final público para máquinas virtuais utilizando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).

8.  Se utilizar o balanceador de carga padrão, siga estes passos de configuração:
    1.  Primeiro, crie um pool IP frontal:
        1.  Abra o balançador de carga, selecione **o pool IP frontend** e selecione **Adicionar**.
        1.  Insira o nome do novo pool IP frontal (por exemplo, **hana-frontend).**
        1.  Desaponda a **Estática** e introduza o endereço IP (por exemplo, **10.32.0.10**). 
        1.  Selecione **OK**.
        1.  Após a criação do novo pool IP frontal, note o endereço IP do pool.
    1.  Em seguida, crie uma piscina traseira:
        1.  Abra o balançador de carga, selecione **piscinas de backend** e selecione **Adicionar**.
        1.  Insira o nome da nova piscina traseira (por exemplo, **hana-backend).**
        1.  **Selecione Adicionar uma máquina virtual**.
        1.  Selecione ** Máquina virtual**.
        1.  Selecione as máquinas virtuais do cluster SAP HANA e os seus endereços IP.
        1.  Selecione **Adicionar**.
    1.  Em seguida, criar uma sonda de saúde:
        1.  Abra o equilibrador de carga, selecione **sondas de saúde** e selecione **Adicionar**.
        1.  Insira o nome da nova sonda de saúde (por exemplo, **hana-hp).**
        1.  Selecione TCP como protocolo e porta 625 **03**. Mantenha o valor **de intervalo** definido para 5, e o valor do **limiar insalubre** definido para 2.
        1.  Selecione **OK**.
    1.  Em seguida, crie as regras de equilíbrio de carga:
        1.  Abra o balançador de carga, selecione **as regras de equilíbrio de carga** e selecione **Adicionar**.
        1.  Introduza o nome da nova regra do balançador de carga (por exemplo, **hana-lb**).
        1.  Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend,** **hana-backend** e **hana-hp).**
        1.  Selecione **portas HA**.
        1.  Aumente o **tempo de 30** minutos.
        1.  Certifique-se de que ativa o **IP flutuante**.
        1.  Selecione **OK**.


9. Em alternativa, se o seu cenário ditar a utilização do balanceador de carga básico, siga estes passos de configuração:
    1.  Configure o equilibrador de carga. Primeiro, crie um pool IP frontal:
        1.  Abra o balançador de carga, selecione **o pool IP frontend** e selecione **Adicionar**.
        1.  Insira o nome do novo pool IP frontal (por exemplo, **hana-frontend).**
        1.  Desaponda a **Estática** e introduza o endereço IP (por exemplo, **10.32.0.10**). 
        1.  Selecione **OK**.
        1.  Após a criação do novo pool IP frontal, note o endereço IP do pool.
    1.  Em seguida, crie uma piscina traseira:
        1.  Abra o balançador de carga, selecione **piscinas de backend** e selecione **Adicionar**.
        1.  Insira o nome da nova piscina traseira (por exemplo, **hana-backend).**
        1.  **Selecione Adicionar uma máquina virtual**.
        1.  Selecione o conjunto de disponibilidade criado no passo 3.
        1.  Selecione as máquinas virtuais do cluster SAP HANA.
        1.  Selecione **OK**.
    1.  Em seguida, criar uma sonda de saúde:
        1.  Abra o equilibrador de carga, selecione **sondas de saúde** e selecione **Adicionar**.
        1.  Insira o nome da nova sonda de saúde (por exemplo, **hana-hp).**
        1.  Selecione **TCP** como protocolo e porta 625 **03**. Mantenha o valor **de intervalo** definido para 5, e o valor do **limiar insalubre** definido para 2.
        1.  Selecione **OK**.
    1.  Para SAP HANA 1.0, crie as regras de equilíbrio de carga:
        1.  Abra o balançador de carga, selecione **as regras de equilíbrio de carga** e selecione **Adicionar**.
        1.  Introduza o nome da nova regra do balançador de carga (por exemplo, hana-lb-3 **03** 15).
        1.  Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
        1.  Mantenha o **Protocolo** definido para **TCP,** e entre na porta **3 03** 15.
        1.  Aumente o **tempo de 30** minutos.
        1.  Certifique-se de que ativa o **IP flutuante**.
        1.  Selecione **OK**.
        1.  Repita estes passos para a porta **3 03** 17.
    1.  Para o SAP HANA 2.0, crie as regras de equilíbrio de carga para a base de dados do sistema:
        1.  Abra o balançador de carga, selecione **as regras de equilíbrio de carga** e selecione **Adicionar**.
        1.  Introduza o nome da nova regra do balançador de carga (por exemplo, hana-lb-3 **03** 13).
        1.  Selecione o endereço IP frontal, o pool traseiro e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
        1.  Mantenha o **Protocolo** definido para **TCP,** e entre na porta **3 03** 13.
        1.  Aumente o **tempo de 30** minutos.
        1.  Certifique-se de que ativa o **IP flutuante**.
        1.  Selecione **OK**.
        1.  Repita estes passos para a porta **3 03** 14.
    1.  Para o SAP HANA 2.0, primeiro crie as regras de equilíbrio de carga para a base de dados do arrendatário:
        1.  Abra o balançador de carga, selecione **as regras de equilíbrio de carga** e selecione **Adicionar**.
        1.  Introduza o nome da nova regra do balançador de carga (por exemplo, hana-lb-3 **03** 40).
        1.  Selecione o endereço IP frontend, o backend pool e a sonda de saúde que criou anteriormente (por exemplo, **hana-frontend**).
        1.  Mantenha o **Protocolo** definido para **TCP,** e entre na porta **3 03** 40.
        1.  Aumente o **tempo de 30** minutos.
        1.  Certifique-se de que ativa o **IP flutuante**.
        1.  Selecione **OK**.
        1.  Repita estes passos para as portas **3 03** 41 e 3 **03** 42.

Para obter mais informações sobre as portas necessárias para o SAP HANA, leia o capítulo [Ligações às Bases de Dados de Inquilinos](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) no guia [sap hana de bases de dados](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) de inquilinos ou sap Note [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Não ative os cartas temporais TCP em VMs Azure colocados atrás do Balançador de Carga Azure. Permitir os tempos de TCP fará com que as sondas de saúde falhem. Definir o parâmetro **net.ipv4.tcp_timestamps** a **0**. Para mais detalhes consulte [as sondas de saúde load balancer](../../../load-balancer/load-balancer-custom-probe-overview.md). Consulte também a nota [SAP 2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Monte o volume de ficheiros Azure NetApp

1. **[A]** Criar pontos de montagem para os volumes de base de dados HANA. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Verifique a definição de domínio NFS. Certifique-se de que o domínio está configurado como o domínio padrão dos Ficheiros Azure NetApp, ou seja, **defaultv4iddomain.com** e o mapeamento não é definido para **ninguém**.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Certifique-se de que define o domínio NFS em /etc/idmapd.conf no VM para corresponder à configuração de domínio predefinido nos ficheiros Azure NetApp: **defaultv4iddomain.com**. Se houver um desfasamento entre a configuração de domínio no cliente NFS (ou seja, o VM) e o servidor NFS, ou seja, a configuração do Azure NetApp, então as permissões para ficheiros nos volumes Azure NetApp que são montados nos VMs serão apresentadas como ninguém.
    

3. **[1]** Monte os volumes específicos do nó no nó1 **(hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** Monte os volumes específicos do nó no nó2 **(hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Verifique se todos os volumes HANA são montados com a versão NFSv4 do protocolo NFSV4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** Verificar **nfs4_disable_idmapping**. Deve ser definido para **Y.** Para criar a estrutura do diretório onde está localizada **nfs4_disable_idmapping,** execute o comando de montagem. Não poderá criar manualmente o diretório em /sys/módulos, porque o acesso é reservado para o núcleo/condutores.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Para obter mais detalhes sobre como alterar **nfs_disable_idmapping** parâmetro, consulte [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Instalação SAP HANA

1. **[A]** Configurar a resolução do nome do anfitrião para todos os anfitriões.

   Pode utilizar um servidor DNS ou modificar o ficheiro /etc/hosts em todos os nós. Este exemplo mostra-lhe como utilizar o ficheiro /etc/hosts. Substitua o endereço IP e o nome de anfitrião nos seguintes comandos:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL para configuração HANA

   Configure o RHEL conforme descrito abaixo na NOTA SAP com base na sua versão RHEL

   - [2292690 - SAP HANA DB: Definições recomendadas de SO para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: Definições recomendadas de OS para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: Aplicações SAP em execução compiladas com GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: Aplicações SAP em execução compiladas com GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: Aplicações SAP em execução compiladas com GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** Instalar o SAP HANA

   Iniciado com HANA 2.0 SPS 01, MDC é a opção padrão. Quando instalar o sistema HANA, o SYSTEMDB e um inquilino com o mesmo SID serão criados em conjunto. Em alguns casos, você não quer o inquilino em incumprimento. No caso de não querer criar inquilino inicial juntamente com a instalação, pode seguir a Nota [SAP 2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Executar o programa **hdblcm** a partir do DVD HANA. Introduza os seguintes valores à medida:  
    Escolha a instalação: Introduza **1** (para instalação)  
    Selecione componentes adicionais para a instalação: Introduza **1**.  
    Insira caminho de instalação [/hana/shared]: prima Entrar para aceitar o padrão  
    Insira o nome do anfitrião local [..]: Pressione Insira para aceitar o padrão  
    Deseja adicionar anfitriões adicionais ao sistema? (y/n) [n]: **n**  
    Insira o ID do sistema SAP HANA: Insira **HN1**.  
    Inserir número de instância [00]: Insira **03**  
    Selecione modo de base de dados / Insira Índice [1]: prima Introduzir para aceitar o padrão  
    Selecione o Sistema De utilização / Insira índice [4]: insira **4** (para costume)  
    Introdução Localização de Volumes de Dados [/hana/dados]: prima Insira para aceitar o padrão  
    Insira a localização dos volumes de registo [/hana/log]: prima Insira para aceitar o padrão  
    Restringir a atribuição máxima de memória? [n]: pressione Entrar para aceitar o padrão  
    Insira o nome do anfitrião para o anfitrião '...' [...]: pressione Entrar para aceitar o padrão  
    Insira o utilizador do agente anfitrião SAP (sapadm) Palavra-passe: Introduza a palavra-passe do utilizador do agente anfitrião  
    Confirme o utilizador do agente anfitrião SAP (sapadm) Palavra-passe: Introduza novamente a palavra-passe do utilizador do agente anfitrião para confirmar  
    Inserir Palavra-passe do Administrador do Sistema (hn1adm): Introduza a palavra-passe do administrador do sistema  
    Confirmação do Administrador do Sistema (hn1adm) Palavra-passe: Introduza novamente a palavra-passe do administrador do sistema para confirmar  
    Insira o Diretório doméstico do Administrador do Sistema [/usr/sap/HN1/home]: prima Insira para aceitar o padrão  
    Insira a Shell de Login do Administrador do Sistema [/bin/sh]: prima Insira para aceitar o padrão  
    Insira iD do utilizador do administrador do sistema [1001]: prima Insira para aceitar o padrão  
    Introduza iD do Grupo de Utilizador (sapsys) [79]: prima Enter para aceitar o padrão  
    Inserir Palavra-passe do Utilizador (SISTEMA) Da base de dados: Introduza a senha de utilizador da base de dados  
    Confirmar Palavra-passe do Utilizador (SYSTEM) do Dado-Base: Introduza novamente a palavra-passe do utilizador da base de dados para confirmar  
    Reiniciar o sistema após o reinício da máquina? [n]: pressione Entrar para aceitar o padrão  
    Quer continuar? (y/n): Validar o resumo. Entrar **y** para continuar  

4. **[A]** Atualizar agente anfitrião DA SAP

   Descarregue o mais recente arquivo do Agente Anfitrião SAP do Centro de [Software SAP](https://launchpad.support.sap.com/#/softwarecenter) e execute o seguinte comando para atualizar o agente. Substitua o caminho para o arquivo para apontar para o ficheiro que descarregou:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** Firewall Configure

   Crie a regra de firewall para a porta da sonda de esquilibrador de carga Azure.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Configurar a replicação do sistema SAP HANA

Siga os passos na Configuração [da Replicação do Sistema SAP HANA](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) para configurar a replicação do sistema SAP HANA. 

## <a name="cluster-configuration"></a>Configuração do cluster

Esta secção descreve os passos necessários para que o cluster funcione perfeitamente quando o SAP HANA é instalado em ações NFS utilizando ficheiros Azure NetApp. 

### <a name="create-a-pacemaker-cluster"></a>Criar um cluster pacemaker

Siga os passos na configuração do [Pacemaker no Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) em Azure para criar um cluster pacemaker básico para este servidor HANA.

### <a name="configure-filesystem-resources"></a>Configurar recursos do sistema de ficheiros

Neste exemplo, cada nó de cluster tem os seus próprios sistemas de ficheiros HANA NFS /hana/shared, /hana/data, e /hana/log.   

1. **[1]** Coloque o cluster no modo de manutenção.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Criar os recursos do sistema de ficheiros para os suportes **hanadb1.**

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Criar os recursos do sistema de ficheiros para os suportes **hanadb2.**

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` o atributo é adicionado à operação do monitor de modo a que cada monitor efetue um teste de leitura/escrita no sistema de ficheiros. Sem este atributo, a operação do monitor apenas verifica se o sistema de ficheiros está montado. Isto pode ser um problema porque quando a conectividade é perdida, o sistema de ficheiros pode permanecer montado apesar de ser inacessível.

    `on-fail=fence` o atributo também é adicionado à operação do monitor. Com esta opção, se a operação do monitor falhar num nó, esse nó é imediatamente vedado. Sem esta opção, o comportamento padrão é parar todos os recursos que dependem do recurso falhado, em seguida, reiniciar o recurso falhado, em seguida, iniciar todos os recursos que dependem do recurso falhado. Este comportamento não só pode demorar muito tempo quando um recurso SAPHana depende do recurso falhado, como também pode falhar completamente. O recurso SAPHana não pode parar com sucesso se o servidor NFS que mantém os executáveis HANA estiver inacessível.

4. **[1]** Configurar restrições de localização

   Configure os constrangimentos de localização para garantir que os recursos que gerem os suportes exclusivos hanadb1 nunca podem funcionar em hanadb2, e vice-versa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    A `resource-discovery=never` opção é definida porque os suportes únicos para cada nó partilham o mesmo ponto de montagem. Por exemplo, `hana_data1` usa o ponto de montagem , e também utiliza o ponto de montagem `/hana/data` `hana_data2` `/hana/data` . Isto pode causar um falso positivo para uma operação de sonda, quando o estado de recursos é verificado no cluster startup, e isso pode, por sua vez, causar comportamentos de recuperação desnecessários. Isto pode ser evitado através da definição `resource-discovery=never`

5. **[1]** Configurar recursos de atributos

   Configurar recursos de atributo. Estes atributos serão definidos como verdadeiros se todos os suportes NFS de um nó (/hana/dados, /hana/log, e /hana/dados) forem montados e forem definidos como falsos de outra forma.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Configurar restrições de localização

   Configure as restrições de localização para garantir que o recurso de atributo hanadb1 nunca funciona em hanadb2, e vice-versa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** Criação de restrições de encomenda

   Configurar restrições de ordenação para que os recursos de atributos de um nó comecem apenas depois de todos os suportes NFS do nó serem montados.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Se a sua configuração incluir sistemas de ficheiros, fora do grupo `hanadb1_nfs` ou `hanadb2_nfs` , então inclua a `sequential=false` opção, de modo a que não existam dependências de encomenda entre os sistemas de ficheiros. Todos os sistemas de ficheiros devem começar antes `hana_nfs1_active` , mas não precisam de começar em qualquer ordem em relação uns aos outros. Para mais [detalhes, veja como configurar a replicação do sistema SAP HANA em Scale-Up num cluster pacemaker quando os sistemas de ficheiros HANA estão em ações NFS](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Configurar recursos de cluster SAP HANA

1. Siga os passos na [Criação de recursos de cluster SAP HANA](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) para criar os Recursos SAP HANA no cluster. Uma vez criados os recursos SAP HANA, precisamos de criar uma restrição de regra de localização entre os recursos SAP HANA e os Sistemas de Ficheiros (NFS Mounts)

2. **[1]** Restrições de configuração entre os recursos SAP HANA e os montes NFS

   As restrições à regra de localização serão definidas de modo a que os recursos SAP HANA só possam funcionar num nó se todos os suportes NFS do nó estiverem montados.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Verifique o estado do cluster e todos os recursos
   > [!NOTE]
   > Este artigo contém referências ao termo *escravo*, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta secção descreve como pode testar a sua configuração. 

1. Antes de iniciar um teste, certifique-se de que o Pacemaker não tem qualquer ação falhada (através do estado do PCS), não existem constrangimentos de localização inesperados (por exemplo, restos de um teste de migração) e que a replicação do sistema HANA é um estado de sincronização, por exemplo com o sistemaReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Verifique a configuração do cluster para um cenário de falha quando um nó perde o acesso à partilha NFS (/hana/shared)

   Os agentes de recursos SAP HANA dependem de binários, armazenados `/hana/shared` para executar operações durante o failover. O sistema de  `/hana/shared` ficheiros é montado sobre NFS no cenário apresentado.  
   É difícil simular uma falha, onde um dos servidores perde acesso à partilha NFS. Um teste que pode ser realizado é re montar o sistema de ficheiros apenas para leitura.
   Esta abordagem valida que o cluster será capaz de falhar, se o acesso `/hana/shared` se perder no nó ativo.     


   **Resultado esperado:** Ao fazer `/hana/shared` como sistema de ficheiros apenas de leitura, o `OCF_CHECK_LEVEL` atributo do recurso que executa a operação de `hana_shared1` leitura/escrita no sistema de ficheiros falhará, uma vez que não é capaz de escrever nada no sistema de ficheiros e executará o failover de recursos HANA.  O mesmo resultado é esperado quando o seu nó HANA perde acesso às ações da NFS. 

   Estado dos recursos antes de iniciar o teste:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   Pode colocar /hana/partilhado no modo apenas de leitura no nó de cluster ativo, utilizando abaixo o comando:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 reiniciará ou desligará com base na ação definida em stonith `pcs property show stonith-action` ().  Uma vez que o servidor (hanadb1) está em baixo, o recurso HANA move-se para hanadb2. Pode verificar o estado do aglomerado de hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Recomendamos testar cuidadosamente a configuração do cluster SAP HANA, realizando também os testes descritos na [Replicação do Sistema SAP HANA da Configuração EM RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).

## <a name="next-steps"></a>Passos seguintes

* [Azure Virtual Machines planejamento e implementação para SAP][planning-guide]
* [Implantação de máquinas virtuais Azure para SAP][deployment-guide]
* [Implantação DBMS de máquinas virtuais Azure para SAP][dbms-guide]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)