---
title: Azure VMware Solution by CloudSimple - Otimize a sua CloudSimple Private Cloud para Oracle RAC
description: Descreve como implantar um novo cluster e otimizar um VM para instalação e configuração de Clusters de Aplicação Real oracle (RAC)
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899274"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Otimize a cloudSimple Private Cloud para instalar o Oracle RAC

Pode implementar Clusters de Aplicações Reais oracle (RAC) no seu ambiente CloudSimple Private Cloud. Este guia descreve como implementar um novo cluster e otimizar um VM para a solução Oracle RAC. Depois de completar os passos neste tópico, pode instalar e configurar o Oracle RAC.

## <a name="storage-policy"></a>Política de Armazenamento

A implementação bem sucedida do Oracle RAC requer um número adequado de nós no cluster.  Na política de armazenamento vSAN, as falhas de toleração (FTT) são aplicadas aos discos de dados utilizados para armazenar a base de dados, registar e refazer discos.  O número necessário de nós para tolerar eficazmente falhas é 2N+1 onde N é o valor de FTT.

Exemplo: Se o FTT desejado for 2, então o número total de nós no cluster deve ser de 2*2+1 = 5.

## <a name="overview-of-deployment"></a>Visão geral da implantação

As secções seguintes descrevem como configurar o seu ambiente CloudSimple Private Cloud para o Oracle RAC.

1. Melhores práticas para configuração de disco
2. Implementar cloudSimple Nuvem privada vSphere Cluster
3. Configurar networking para Oracle RAC
4. Configuração de políticas de armazenamento vSAN
5. Crie VMs Oracle e crie discos VM partilhados
6. Configurar regras de afinidade VM-para-anfitrião

## <a name="best-practices-for-disk-configuration"></a>Melhores práticas para configuração de disco

As máquinas virtuais Oracle RAC têm vários discos, que são usados para funções específicas.  Os discos partilhados são montados em todas as máquinas virtuais, que são usadas pelo Oracle RAC Cluster.  Os discos de instalação do sistema operativo e do software são montados apenas nas máquinas virtuais individuais.  

![Visão geral dos discos de máquinas virtuais oracle RAC](media/oracle-vm-disks-overview.png)

O exemplo seguinte utiliza os discos definidos na tabela abaixo.

| Disco                                      | Objetivo                                       | Disco Partilhado |
|-------------------------------------------|-----------------------------------------------|-------------|
| SO                                        | Disco do sistema operativo                         | Não          |
| REDE                                      | Instalar localização para o software Oracle Grid     | Não          |
| BASE DE DADOs                                  | Instalar localização para o software de base de dados da Oracle | Não          |
| ORAHOME                                   | Localização base para binários de base de dados oracle    | Não          |
| DATA1, DATA2, DATA3, DATA4                | Disco onde os ficheiros de base de dados da Oracle são armazenados   | Sim         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Redo discos de log                                | Sim         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Discos de votação                                  | Sim         |
| FRA1                                | Discos de área de recuperação rápida                      | Sim         |

![Configuração do disco de máquina virtual Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

* Cada máquina virtual está configurada com quatro controladores SCSI.
* O tipo de controlador SCSI está definido para VMware paravirtual.
* Vários discos virtuais (.vmdk) são criados.
* Os discos são montados em diferentes controladores SCSI.
* O tipo de partilha de vários escritores está definido para discos de cluster compartilhados.
* a política de armazenamento vSAN é definida para garantir uma elevada disponibilidade de discos.

### <a name="operating-system-and-software-disk-configuration"></a>Configuração do sistema operativo e do disco de software

Cada máquina virtual Oracle está configurada com vários discos para o sistema operativo anfitrião, troca, instalação de software e outras funções de SO.  Estes discos não são partilhados entre as máquinas virtuais.  

* Três discos para cada máquina virtual são configurados como discos virtuais e montados em máquinas virtuais Oracle RAC.
    * Disco do SO
    * Disco para armazenar Oracle Grid instala ficheiros
    * Disco para armazenar ficheiros de instalação da base de dados oracle
* Os discos podem ser configurados como **Finos Provisionados**.
* Cada disco é montado no primeiro controlador SCSI (SCSI0).  
* A partilha está definida para **Não partilhar.**
* A redundância é definida no armazenamento utilizando políticas vSAN.  

![Diagrama que mostra a configuração física do disco Oracle RAC OS.](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configuração do disco de dados

Os discos de dados são usados principalmente para armazenar ficheiros de bases de dados.  

* Quatro discos são configurados como discos virtuais e montados em todas as máquinas virtuais do Oracle RAC.
* Cada disco é montado num controlador SCSI diferente.
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.  
* A partilha está definida para **multi-escritor.**  
* Os discos devem ser configurados como um grupo de discos de Gestão automática de Armazenamento (ASM).  
* A redundância é definida no armazenamento utilizando políticas vSAN.  
* O despedimento da ASM está definido para o despedimento **externo.**

![Configuração do grupo de discos de dados oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Redo configuração do disco de log

Os ficheiros de registo de redo são utilizados para armazenar uma cópia das alterações efetuadas na base de dados.  Os ficheiros de registo são utilizados quando os dados precisam de ser recuperados após eventuais falhas.

* Os discos de registo redo devem ser configurados como múltiplos grupos de discos.  
* Seis discos são criados e montados em todas as máquinas virtuais oracle RAC.
* Os discos são montados em diferentes controladores SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* A partilha está definida para **multi-escritor.**  
* Os discos devem ser configurados como dois grupos de discos ASM.
* Cada grupo de discos ASM contém três discos, que estão em diferentes controladores SCSI.  
* O despedimento da ASM está definido para o despedimento **normal.**
* Cinco ficheiros de registo de redo são criados tanto no grupo de registo ASM Redo

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC redo configuração do grupo de discos de log](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configuração do disco de voto do Oráculo

Os discos de votação fornecem a funcionalidade do disco quórum como um canal de comunicação adicional para evitar qualquer situação de cérebro dividido.

* Cinco discos são criados e montados em todas as máquinas virtuais oracle RAC.
* Os discos são montados num controlador SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* A partilha está definida para **multi-escritor.**  
* Os discos devem ser configurados como um grupo de discos ASM.  
* O despedimento da ASM está definido para **alta** redundância.

![Configuração do grupo de discos de voto Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Configuração do disco de recuperação rápida da oracle (opcional)

A área de recuperação rápida (FRA) é um sistema de ficheiros gerido pelo grupo de discos Oracle ASM.  A FRA fornece um local de armazenamento partilhado para ficheiros de backup e recuperação. A Oracle cria registos arquivados e registos de flashback na área de recuperação rápida. O Oracle Recovery Manager (RMAN) pode opcionalmente armazenar os seus conjuntos de backup e cópias de imagem na área de recuperação rápida, e usa-os ao restaurar ficheiros durante a recuperação dos meios de comunicação.

* Dois discos são criados e montados em todas as máquinas virtuais oracle RAC.
* Os discos são montados em diferentes controladores SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* A partilha está definida para **multi-escritor.**  
* Os discos devem ser configurados como um grupo de discos ASM.  
* O despedimento da ASM está definido para o despedimento **externo.**

![Diagrama que mostra a configuração do grupo de discos de voto Oracle RAC.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Implementar cloudSimple nuvem privada vSphere cluster

Para implementar um cluster vSphere na sua Nuvem Privada, siga este processo:

1. A partir do portal CloudSimple, [crie uma Nuvem Privada.](create-private-cloud.md) CloudSimple cria um utilizador vCenter padrão chamado 'cloudowner' na recém-criada Cloud Privada. Para obter mais detalhes sobre o utilizador e o modelo de permissão private Cloud predefinidos, consulte [o modelo de permissão Private Cloud](learn-private-cloud-permissions.md).  Este passo cria o cluster de gestão primária para a sua Nuvem Privada.

2. A partir do portal CloudSimple, [expanda a Nuvem Privada](expand-private-cloud.md) com um novo cluster.  Este cluster será usado para implantar o Oracle RAC.  Selecione o número de nós com base na tolerância à falha desejada (mínimo de três nós).

## <a name="set-up-networking-for-oracle-rac"></a>Configurar a rede para o Oracle RAC

1. Na sua Nuvem Privada, [crie dois VLANs](create-vlan-subnet.md), um para a rede pública Oracle e outro para a rede privada Oracle e atribua CIDRs de sub-rede apropriados.
2. Após a criação dos VLANs, crie os [grupos portuários distribuídos no VCenter Private Cloud](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Crie uma [máquina virtual de servidor DHCP e DNS](dns-dhcp-setup.md) no seu cluster de gestão para o ambiente Oráculo.
4. [Configurar o encaminhamento de DNS no servidor DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) instalado na Nuvem Privada.

## <a name="set-up-vsan-storage-policies"></a>Configurar políticas de armazenamento vSAN

as políticas vSAN definem as falhas de toleração e de tiragem de discos para os dados armazenados nos discos VM.  A política de armazenamento criada deve ser aplicada nos discos VM enquanto cria o VM.

1. [Inscreva-se no cliente vSphere](./vcenter-access.md) da sua Nuvem Privada.
2. A partir do menu superior, selecione **Políticas e Perfis.**
3. A partir do menu esquerdo, selecione **Políticas de Armazenamento VM** e, em seguida, selecione **Criar uma Política de armazenamento VM**.
4. Introduza um nome significativo para a apólice e clique em **NEXT**.
5. Na secção **estrutura política,** selecione **Ative as regras para o armazenamento vSAN** e clique em **SEGUINTE**.
6. Na secção **disponibilidade vSAN,**  >   selecione **Nenhum** para a tolerância a desastres do Site. Para falhas em tolerar, selecione a opção **RAID - Mirroring** para o FTT desejado.
    ![configurações vSAN ](media/oracle-rac-storage-wizard-vsan.png) .
7. Na secção **Avançado,** selecione o número de riscas de disco por objeto. Para a reserva de espaço object, selecione **Thick Provisioned**. Selecione **Desativar a sê-lo.** Clique **em SEGUINTE**.
8. Siga as instruções no ecrã para ver a lista de datastores vSAN compatíveis, reveja as definições e termine a configuração.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Crie VMs Oracle e crie discos VM partilhados para a Oracle

Para criar um VM para o Oráculo, clone um VM existente ou crie um novo.  Esta secção descreve como criar um novo VM e, em seguida, cloná-lo para criar um segundo após a instalação do sistema operativo base.  Depois de criados os VMs, pode criar-lhes um add disks.  O cluster Oracle utiliza discos partilhados para armazenar, dados, registos e redo registos.

### <a name="create-vms"></a>Criar VMs

1. No vCenter, clique no ícone **Anfitriões e Agrupamentos.** Selecione o cluster que criou para o Oráculo.
2. Clique com o botão direito no cluster e selecione **Nova Máquina Virtual.**
3. **Selecione Criar uma nova máquina virtual** e clique em **Seguinte**.
4. Nomeie a máquina, selecione a localização do Oracle VM e clique em **Seguinte**.
5. Selecione o recurso cluster e clique em **Seguinte**.
6. Selecione a loja de dados vSAN para o cluster e clique em **Seguinte**.
7. Mantenha a seleção de compatibilidade padrão ESXi 6.5 e clique em **Seguinte**.
8. Selecione o SO convidado do ISO para o VM que está a criar e clique em **Seguinte**.
9. Selecione o tamanho do disco rígido necessário para a instalação do SISTEMA.
10. Para instalar a aplicação num dispositivo diferente, clique em **Adicionar novo dispositivo**.
11. Selecione as opções de rede e atribua o grupo portuário distribuído criado para a rede pública.
12. Para adicionar interfaces de rede adicionais, clique em **Adicionar novo dispositivo** e selecione o grupo portuário distribuído criado para a rede privada.
13. Para nova unidade DC/DVD, selecione o ficheiro ISO da loja de dados que contém o ISO para a instalação do sistema operativo preferido. Selecione o ficheiro que já fez o upload para a pasta ISOs e Modelos e clique em **OK**.
14. Reveja as definições e clique **em OK** para criar o novo VM.
15. Ligue no VM. Instale o sistema operativo e quaisquer atualizações necessárias

Depois de instalado o sistema operativo, pode clonar um segundo VM. Clique com o botão direito na entrada VM e selecione a opção e clone.

### <a name="create-shared-disks-for-vms"></a>Criar discos partilhados para VMs

A Oracle utiliza o disco partilhado para armazenar os ficheiros de dados, registos e redo ficheiros de registo.  Pode criar um disco partilhado no vCenter e montá-lo em ambos os VMs.  Para um desempenho mais elevado, coloque os discos de dados em diferentes comandos SCSI Passos abaixo mostram como criar um disco partilhado no vCenter e, em seguida, anexá-lo a uma máquina virtual. vCenter Flash cliente é utilizado para modificar as propriedades VM.

#### <a name="create-disks-on-the-first-vm"></a>Criar discos no primeiro VM

1. Em vCenter, clique com o botão direito num dos VMs do Oracle e selecione **as definições de Edição**.
2. Na nova secção do dispositivo, selecione **o controlador SCSI** e clique em **Adicionar**.
3. Na nova secção do dispositivo, selecione **Novo Disco Rígido** e clique em **Adicionar**.
4. Expandir as propriedades do novo disco rígido.
5. Especifique o tamanho do disco rígido.
6. Especifique a política de armazenamento VM como a política de armazenamento vSAN que definiu anteriormente.
7. Selecione a localização como uma pasta na loja de dados vSAN. A localização ajuda na navegação e na fixação dos discos a um segundo VM.
8. Para o provisionamento do disco, selecione **Thick provision eager zeroed**.
9. Para partilha, especifique **multi-escritor.**
10. Para o nó do dispositivo virtual, selecione o novo controlador SCSI que foi criado no passo 2.

    ![Screenshot que realça os campos necessários para criar discos no primeiro VM.](media/oracle-rac-new-hard-disk.png)

Repita os passos 2 - 10 para todos os novos discos necessários para os ficheiros de dados, registos e redo ficheiros de registo da Oracle.

#### <a name="attach-disks-to-second-vm"></a>Anexar discos ao segundo VM

1. Em vCenter, clique com o botão direito num dos VMs do Oracle e selecione **as definições de Edição**.
2. Na nova secção do dispositivo, selecione **o controlador SCSI** e clique em **Adicionar**.
3. Na nova secção do dispositivo, selecione **o disco rígido existente** e clique em **Adicionar**.
4. Navegue no local onde o disco foi criado para o primeiro VM e selecione o ficheiro VMDK.
5. Especifique a política de armazenamento VM como a política de armazenamento vSAN que definiu anteriormente.
6. Para o provisionamento do disco, selecione **Thick provision eager zeroed**.
7. Para partilha, especifique **multi-escritor.**
8. Para o nó do dispositivo virtual, selecione o novo controlador SCSI que foi criado no passo 2.

    ![Criar discos no primeiro VM](media/oracle-rac-existing-hard-disk.png)

Repita os passos 2 - 7 para todos os novos discos necessários para os ficheiros de dados, registos e redo ficheiros de registo da Oracle.

## <a name="set-up-vm-host-affinity-rules"></a>Configurar regras de afinidade do anfitrião VM

As regras de afinidade VM-para-anfitrião asseguram que o VM funciona no hospedeiro pretendido.  Pode definir regras sobre o vCenter para garantir que o Oracle VM funciona no hospedeiro com recursos adequados e para satisfazer quaisquer requisitos específicos de licenciamento.

1. No portal CloudSimple, [aumente os privilégios](escalate-private-cloud-privileges.md) do utilizador de cloudowner.
2. Faça login no cliente vSphere da sua Nuvem Privada.
3. No cliente vSphere, selecione o cluster onde os VMs da Oracle são implantados e clique em **Configurar**.
4. Em Configuração, selecione **VM/Host Groups**.
5. Clique **+** em .
6. Adicione um grupo VM. Selecione **o grupo VM** como o tipo. Insira o nome do grupo. Selecione os VMs e, em seguida, clique **em OK** para criar o grupo.
6. Adicione um grupo de anfitriões. Selecione **o Grupo anfitrião** como o tipo. Insira o nome do grupo. Selecione os anfitriões onde os VMs serão executados e, em seguida, clique em **OK** para criar o grupo.
7. Para criar uma regra, clique nas **regras VM/Host**.
8. Clique **+** em .
9. Introduza um nome para a regra e verifique **Ativar**.
10. Para o tipo de regra, selecione **Máquinas Virtuais para Hospedar**.
11. Selecione o grupo VM que contém os VMs do Oráculo.
12. Selecione **Deve correr em anfitriões neste grupo**.
13. Selecione o grupo anfitrião que criou.
14. Clique em **OK** para criar a regra.

## <a name="references"></a>Referências

* [Sobre as políticas vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware Atributo Multi-Escritor para VMDKs compartilhados](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
