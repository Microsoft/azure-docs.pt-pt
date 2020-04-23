---
title: Solução Azure VMware by CloudSimple - Otimize a sua Nuvem Privada CloudSimple para o Oracle RAC
description: Descreve como implementar um novo cluster e otimizar um VM para a instalação e configuração de Clusters de Aplicações Reais oracle (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b945beaa7497e1ad19315bacf1284dd0cbc24d6a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868082"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Otimize a sua CloudSimple Private Cloud para instalar o Oracle RAC

Você pode implementar Clusters de Aplicação Real Oracle (RAC) no seu ambiente CloudSimple Private Cloud. Este guia descreve como implantar um novo cluster e otimizar um VM para a solução Oracle RAC. Depois de completar os passos neste tópico, pode instalar e configurar o Oracle RAC.

## <a name="storage-policy"></a>Política de Armazenamento

A implementação bem sucedida do Oracle RAC requer um número adequado de nós no cluster.  Na política de armazenamento vSAN, as falhas na tolerar (FTT) são aplicadas aos discos de dados utilizados para armazenar a base de dados, o registo e os discos de redoção.  O número necessário de nós para tolerar efetivamente falhas é 2N+1 onde N é o valor de FTT.

Exemplo: Se o FTT desejado for 2, então o número total de nós no cluster deve ser 2*2+1 = 5.

## <a name="overview-of-deployment"></a>Visão geral da implantação

As seguintes secções descrevem como configurar o seu ambiente CloudSimple Private Cloud para o Oracle RAC.

1. Boas práticas para configuração de disco
2. Implementar cloudsimple private cloud vSphere Cluster
3. Configurar networking para o Oracle RAC
4. Configurar políticas de armazenamento vSAN
5. Crie VMs Oracle e crie discos VM partilhados
6. Criar regras de afinidade VM-a-anfitriã

## <a name="best-practices-for-disk-configuration"></a>Boas práticas para configuração de disco

As máquinas virtuais Oracle RAC têm vários discos, que são usados para função específica.  Os discos partilhados são montados em todas as máquinas virtuais, que são usadas pelo Cluster RAC oracle.  Os discos de instalação do sistema operativo e do software são montados apenas nas máquinas virtuais individuais.  

![Visão geral dos discos virtuais da Oracle RAC](media/oracle-vm-disks-overview.png)

Seguindo o exemplo utiliza os discos definidos na tabela abaixo.

| Disco                                      | Objetivo                                       | Disco Partilhado |
|-------------------------------------------|-----------------------------------------------|-------------|
| SO                                        | Disco do sistema operativo                         | Não          |
| GRELHA                                      | Instale a localização para o software Oracle Grid     | Não          |
| BASE DE DADOS                                  | Instale a localização para o software de base de dados Oracle | Não          |
| ORAHOME                                   | Localização base para binários de base de dados Oracle    | Não          |
| DATA1, DATA2, DATA3, DATA4                | Disco onde os ficheiros da base de dados da Oracle são armazenados   | Sim         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Redote slog disks                                | Sim         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Discos de votação                                  | Sim         |
| FRA1, FRA2                                | Discos de área de recuperação rápida                      | Sim         |

![Configuração do disco virtual oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configuração da máquina virtual

* Cada máquina virtual é configurada com quatro controladores SCSI.
* O tipo de controlador SCSI está definido para vMware paravirtual.
* São criados vários discos virtuais (.vmdk).
* Os discos são montados em diferentes controladores SCSI.
* O tipo de partilha de vários escritores está definido para discos de cluster partilhados.
* a política de armazenamento vSAN é definida para garantir uma elevada disponibilidade de discos.

### <a name="operating-system-and-software-disk-configuration"></a>Configuração do sistema operativo e do disco de software

Cada máquina virtual Oracle é configurada com vários discos para o sistema operativo do anfitrião, swap, instalação de software e outras funções de OS.  Estes discos não são partilhados entre as máquinas virtuais.  

* Três discos para cada máquina virtual são configurados como discos virtuais e montados em máquinas virtuais Oracle RAC.
    * Disco do SO
    * Disco para armazenar oracle Grid instala ficheiros
    * Disco para armazenar ficheiros de instalação de base de dados Oracle
* Os discos podem ser configurados como **Finoprovisionado**.
* Cada disco é montado no primeiro controlador SCSI (SCSI0).  
* A partilha está definida para **não partilhar.**
* O despedimento é definido no armazenamento utilizando políticas vSAN.  

![Configuração do grupo de discos de dados Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configuração do disco de dados

Os discos de dados são usados principalmente para armazenar ficheiros de base de dados.  

* Quatro discos são configurados como discos virtuais e montados em todas as máquinas virtuais oracle RAC.
* Cada disco é montado num controlador SCSI diferente.
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.  
* A partilha está definida para **o multi-escritor.**  
* Os discos devem ser configurados como um grupo de disco de Gestão automática de Armazenamento (ASM).  
* O despedimento é definido no armazenamento utilizando políticas vSAN.  
* O despedimento da ASM está definido para o despedimento **externo.**

![Configuração do grupo de discos de dados Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Configuração do disco de registo redo

Os ficheiros de registo redosão são utilizados para armazenar uma cópia das alterações feitas na base de dados.  Os ficheiros de registo são utilizados quando os dados precisam de ser recuperados após eventuais falhas.

* Os discos de registo redomados devem ser configurados como múltiplos grupos de discos.  
* Seis discos são criados e montados em todas as máquinas virtuais oracle RAC.
* Os discos são montados em diferentes controladores SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* A partilha está definida para **o multi-escritor.**  
* Os discos devem ser configurados como dois grupos de discos ASM.
* Cada grupo de discos ASM contém três discos, que estão em diferentes controladores SCSI.  
* A redundância da ASM está definida para a redundância **normal.**
* Cinco ficheiros de registo de redo são criados em ambos os grupos de registo ASM Redo

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

![Configuração do grupo de discos de log Oracle RAC redo](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configuração do disco de votação oracle

Os discos de votação fornecem a funcionalidade do disco quórum como um canal de comunicação adicional para evitar qualquer situação entre o cérebro.

* Cinco discos são criados e montados em todas as máquinas virtuais oracle RAC.
* Os discos são montados num controlador SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* A partilha está definida para **o multi-escritor.**  
* Os discos devem ser configurados como um grupo de discos ASM.  
* A redundância da ASM está marcada para **o alto** despedimento.

![Configuração do grupo de grupo de votação Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Configuração do disco de área de recuperação rápida oracle (opcional)

A área de recuperação rápida (FRA) é o sistema de ficheiros gerido pelo grupo de discos Oracle ASM.  A FRA fornece um local de armazenamento partilhado para ficheiros de backup e recuperação. A Oracle cria registos arquivados e registos de flashback na área de recuperação rápida. O Oracle Recovery Manager (RMAN) pode armazenar opcionalmente os seus conjuntos de backup e cópias de imagem na área de recuperação rápida, e usa-o ao restaurar ficheiros durante a recuperação dos meios de comunicação.

* Dois discos são criados e montados em todas as máquinas virtuais Oracle RAC.
* Os discos são montados em diferentes controladores SCSI
* Cada disco virtual é configurado como **Thick Provision Eager Zeroed**.
* A partilha está definida para **o multi-escritor.**  
* Os discos devem ser configurados como um grupo de discos ASM.  
* O despedimento da ASM está definido para o despedimento **externo.**

![Configuração do grupo de grupo de votação Oracle RAC](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Implementar cloudsimple private cloud vSphere cluster

Para implantar um cluster vSphere na sua Nuvem Privada, siga este processo:

1. A partir do portal CloudSimple, [crie uma Nuvem Privada.](create-private-cloud.md) A CloudSimple cria um utilizador vCenter padrão chamado 'cloudowner' na recém-criada Cloud Privada. Para mais detalhes sobre o modelo de permissão e utilizador e permissão do Private Cloud, consulte O modelo de [permissão](learn-private-cloud-permissions.md)Da Nuvem Privada .  Este passo cria o cluster de gestão primária para a sua Nuvem Privada.

2. A partir do portal CloudSimple, [expanda a Nuvem Privada](expand-private-cloud.md) com um novo cluster.  Este aglomerado será usado para implantar o Oracle RAC.  Selecione o número de nós com base na tolerância à falha desejada (mínimo de três nós).

## <a name="set-up-networking-for-oracle-rac"></a>Configurar a rede para o Oracle RAC

1. Na sua Nuvem Privada, [crie dois VLANs,](create-vlan-subnet.md)um para a rede pública Oracle e outro para a rede privada Oracle e atribua CIDRs de sub-rede apropriados.
2. Depois de criados os VLANs, crie os [grupos portuários distribuídos no vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)da Nuvem Privada .
3. Instale uma [máquina virtual de servidor DHCP e DNS](dns-dhcp-setup.md) no seu cluster de gestão para o ambiente Oracle.
4. [Configure o reencaminhado DNS no servidor DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) instalado na Cloud Privada.

## <a name="set-up-vsan-storage-policies"></a>Criar políticas de armazenamento vSAN

as políticas vSAN definem as falhas de tolerar e de despir discos para os dados armazenados nos discos VM.  A política de armazenamento criada deve ser aplicada nos discos VM enquanto cria o VM.

1. [Inscreva-se no cliente vSphere](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access) da sua Nuvem Privada.
2. A partir do menu superior, selecione **Políticas e Perfis.**
3. A partir do menu esquerdo, selecione Políticas de **Armazenamento VM** e, em seguida, selecione Criar uma Política de **armazenamento VM**.
4. Introduza um nome significativo para a apólice e clique em **NEXT**.
5. Na secção **estrutura política,** selecione **As regras de ativação para armazenamento vSAN** e clique em **NEXT**.
6. Na secção de**Disponibilidade** **vSAN,** > selecione Nenhum para tolerância a **desastres** do Site. Para falhas a tolerar, selecione a opção **RAID - Espelhamento** para o FTT desejado.
    ![definições](media/oracle-rac-storage-wizard-vsan.png)vSAN .
7. Na secção **Avançada,** selecione o número de riscas de disco por objeto. Para reserva de espaço objeto, **selecione Thick Provisioned**. **Selecione Desativar a verificação**do objeto . Clique **em seguida**.
8. Siga as instruções no ecrã para visualizar a lista de lojas de dados vSAN compatíveis, reveja as definições e termine a configuração.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Crie VMs Oracle e crie discos VM partilhados para a Oracle

Para criar um VM para a Oracle, clone um VM existente ou crie um novo.  Esta secção descreve como criar um novo VM e, em seguida, cloná-lo para criar um segundo após a instalação do sistema operativo base.  Após a criação dos VMs, pode criar-lhes um add disks.  O cluster Oracle utiliza discos partilhados para armazenar, dados, registos e registos de refazer.

### <a name="create-vms"></a>Criar VMs

1. No vCenter, clique no ícone **Anfitriões e Clusters.** Selecione o cluster que criou para a Oracle.
2. Clique no cluster e selecione **Nova Máquina Virtual**.
3. Selecione **Criar uma nova máquina virtual** e clique em **Next**.
4. Nomeie a máquina, selecione a localização do Oráculo VM e clique **em Next**.
5. Selecione o recurso do cluster e clique **em Seguinte**.
6. Selecione a loja de dados vSAN para o cluster e clique **em Next**.
7. Mantenha a seleção de compatibilidade ESXi 6.5 padrão e clique **em Seguinte**.
8. Selecione o oss o de hóspedes do ISO para o VM que está a criar e clique **em Next**.
9. Selecione o tamanho do disco rígido necessário para instalar o SISTEMA.
10. Para instalar a aplicação num dispositivo diferente, clique em **Adicionar novo dispositivo**.
11. Selecione opções de rede e atribua o grupo portuário distribuído criado para a rede pública.
12. Para adicionar interfaces de rede adicionais, clique **em Adicionar novo dispositivo** e selecione o grupo portuário distribuído criado para a rede privada.
13. Para a Nova Unidade DC/DVD, selecione o ficheiro ISO da datastore que contém o ISO para a instalação do sistema operativo preferido. Selecione o ficheiro que já fez o upload para a pasta ISOs e Templates e clique em **OK**.
14. Reveja as definições e clique **em OK** para criar o novo VM.
15. Energia no VM. Instale o sistema operativo e quaisquer atualizações necessárias

Depois de instalado o sistema operativo, pode clonar um segundo VM. Clique na entrada VM e selecione a opção e clone.

### <a name="create-shared-disks-for-vms"></a>Criar discos partilhados para VMs

A Oracle utiliza o disco partilhado para armazenar os dados, registar e refazer ficheiros de registo.  Pode criar um disco partilhado no vCenter e montá-lo em ambos os VMs.  Para um desempenho mais elevado, coloque os discos de dados em diferentes controladores SCSI Passos abaixo, mostrar como criar um disco partilhado no vCenter e, em seguida, anexá-lo a uma máquina virtual. o cliente vCenter Flash é utilizado para modificar as propriedades VM.

#### <a name="create-disks-on-the-first-vm"></a>Criar discos no primeiro VM

1. No vCenter, clique num dos VMs oracle e selecione **definições de Edição**.
2. Na nova secção do dispositivo, selecione **o controlador SCSI** e clique em **Adicionar**.
3. Na nova secção do dispositivo, selecione **new hard disk** e clique em **Adicionar**.
4. Expanda as propriedades do disco New Hard.
5. Especifique o tamanho do disco rígido.
6. Especifique a política de armazenamento VM como a política de armazenamento vSAN que definiu anteriormente.
7. Selecione a localização como pasta na loja de dados vSAN. A localização ajuda a navegar e a fixar os discos a um segundo VM.
8. Para o fornecimento de disco, selecione **Thick provision ansioso zeroed**.
9. Para partilha, especifique **multi-escritor**.
10. Para o nó virtual do dispositivo, selecione o novo controlador SCSI que foi criado no passo 2.

    ![Criar discos no primeiro VM](media/oracle-rac-new-hard-disk.png)

Repita os passos 2 - 10 para todos os novos discos necessários para os dados, registos e refazer ficheiros de registo da Oracle.

#### <a name="attach-disks-to-second-vm"></a>Anexar discos ao segundo VM

1. No vCenter, clique num dos VMs oracle e selecione **definições de Edição**.
2. Na nova secção do dispositivo, selecione **o controlador SCSI** e clique em **Adicionar**.
3. Na nova secção do dispositivo, selecione disco **Hard existente** e clique em **Adicionar**.
4. Navegue no local onde o disco foi criado para o primeiro VM e selecione o ficheiro VMDK.
5. Especifique a política de armazenamento VM como a política de armazenamento vSAN que definiu anteriormente.
6. Para o fornecimento de disco, selecione **Thick provision ansioso zeroed**.
7. Para partilha, especifique **multi-escritor**.
8. Para o nó virtual do dispositivo, selecione o novo controlador SCSI que foi criado no passo 2.

    ![Criar discos no primeiro VM](media/oracle-rac-existing-hard-disk.png)

Repita os passos 2 - 7 para todos os novos discos necessários para os dados oracle, registos e refazer ficheiros de registo.

## <a name="set-up-vm-host-affinity-rules"></a>Configurar regras de afinidade anfitriãs vM

As regras de afinidade VM-to-host asseguram que o VM funciona com o hospedeiro desejado.  Pode definir regras no vCenter para garantir que o VM oracle funciona no hospedeiro com recursos adequados e para satisfazer quaisquer requisitos específicos de licenciamento.

1. No portal CloudSimple, [aumente os privilégios](escalate-private-cloud-privileges.md) do utilizador do cloudowner.
2. [Faça login no cliente vSphere](https://docs.azure.cloudsimple.com/vsphere-access) da sua Nuvem Privada.
3. No cliente vSphere, selecione o cluster onde os VMs oracle são implantados e clique em **Configurar**.
4. Em Configurar, selecione **Grupos VM/Host**.
5. Clique **+** em .
6. Adicione um grupo VM. Selecione **o grupo VM** como o tipo. Insira o nome do grupo. Selecione os VMs e, em seguida, clique em **OK** para criar o grupo.
6. Adicione um grupo de anfitriões. Selecione **Host Group** como o tipo. Insira o nome do grupo. Selecione os anfitriões onde os VMs irão correr e, em seguida, clique EM **OK** para criar o grupo.
7. Para criar uma regra, clique nas **regras VM/Anfitrião**.
8. Clique **+** em .
9. Introduza um nome para a regra e verifique **Ativar**.
10. Para o tipo de regra, selecione **Máquinas Virtuais para Hospedar**.
11. Selecione o grupo VM que contém os VMs oracle.
12. Selecione **Deve correr em anfitriões deste grupo**.
13. Selecione o grupo anfitrião que criou.
14. Clique em **OK** para criar a regra.

## <a name="references"></a>Referências

* [Sobre as Políticas vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Atributo Multi-Escritor VMware para VMDKs partilhados](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
