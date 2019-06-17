---
title: Melhores práticas para a matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve as melhores práticas para implementar e gerir a matriz Virtual StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: alkohli
ms.openlocfilehash: b8e9f12a549f71971c2da3b9865f6a74dad58f61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630143"
---
# <a name="storsimple-virtual-array-best-practices"></a>Práticas recomendadas da matriz Virtual StorSimple
## <a name="overview"></a>Descrição geral
Microsoft Azure StorSimple Virtual Array é uma solução de armazenamento integrada que gere tarefas de armazenamento entre um dispositivo virtual de no local em execução num hipervisor e o armazenamento do Microsoft Azure na cloud. Matriz Virtual StorSimple é uma alternativa eficiente e econômica para a matriz de físicos da 8000 série. A matriz virtual pode ser executado em sua infraestrutura existente do hipervisor, suporta o iSCSI e os protocolos SMB e é bem adequada para cenários de sucursais/escritórios remotos. Para obter mais informações sobre as soluções StorSimple, aceda a [descrição geral do Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo aborda as práticas recomendadas implementadas durante a configuração inicial, a implementação e a gestão da matriz Virtual StorSimple. Essas práticas recomendadas fornecem diretrizes validadas para a configuração e gestão de sua matriz virtual. Este artigo é voltado para os administradores de TI que implantam e gerenciam as matrizes virtuais em seus data Centers.

Recomendamos uma revisão periódica das práticas recomendadas para ajudar a garantir o que seu dispositivo ainda está em conformidade quando forem feitas alterações para o fluxo de configuração ou de operação. Deve encontrar algum problema ao implementar estas melhores práticas em sua matriz virtual, [contacte o Microsoft Support](storsimple-virtual-array-log-support-ticket.md) para obter assistência.

## <a name="configuration-best-practices"></a>Melhores práticas de configuração
Estas melhores práticas sobre as diretrizes que precisam ser seguidas durante a configuração inicial e a implementação das matrizes virtuais. Essas práticas recomendadas incluem os relacionados com o aprovisionamento da máquina virtual, configurações de diretiva de grupo, o dimensionamento, configurar o funcionamento em rede, configurar contas do storage e criação de compartilhamentos e volumes para a matriz virtual. 

### <a name="provisioning"></a>Aprovisionamento
Matriz Virtual StorSimple é uma máquina virtual (VM) aprovisionada no hipervisor (Hyper-V ou VMware) do seu servidor de anfitrião. Quando aprovisionar a máquina virtual, certifique-se de que o seu anfitrião é capaz de dediquem recursos suficientes. Para obter mais informações, aceda a [requisitos de recursos mínimo](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) para Aprovisionar uma matriz.

Implemente as seguintes melhores práticas quando aprovisionar a matriz virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**Geração 2** VM para utilização com o Windows Server 2012 ou posterior e um *vhdx* imagem. <br></br> **Geração 1** VM para utilização com o Windows Server 2008 ou posterior e um *VHD* imagem. |Utilizar a máquina virtual versão 8 ao usar *. vmdk* imagem. |
| **Tipo de memória** |Configurar como **memória estática**. <br></br> Não utilize o **memória dinâmica** opção. | |
| **Tipo de disco de dados** |Aprovisionar como **expansão dinâmica**.<br></br> **Foi corrigido o tamanho** demora muito tempo. <br></br> Não utilize o **diferenciação** opção. |Utilize o **dinâmico aprovisionar** opção. |
| **Modificação do disco de dados** |Expansão ou encolher não é permitida. Uma tentativa de fazê-lo resulta na perda de todos os dados locais no dispositivo. |Expansão ou encolher não é permitida. Uma tentativa de fazê-lo resulta na perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Dimensionamento
Ao dimensionar a sua matriz Virtual StorSimple, considere os seguintes fatores:

* Reserva local para volumes ou compartilhamentos. Aproximadamente 12% do espaço é reservado no escalão local para cada volume em camadas aprovisionado ou partilha. Aproximadamente 10% do espaço também está reservado para um volume afixado localmente para o sistema de ficheiros.
* Instantâneo de sobrecarga. Aproximadamente 15% de espaço no escalão local está reservado para instantâneos.
* É necessário para restauros. Se a fazer a restauração como uma operação de novo, de dimensionamento deve conta para o espaço necessário para restauro. É efetuar um restauro para um compartilhamento ou volume do mesmo tamanho.
* Algum buffer deve ser alocado para qualquer crescimento inesperado.

Com base nos fatores anteriores, os requisitos de dimensionamento podem ser representados por seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Os exemplos seguintes mostram como dimensionar uma matriz virtual com base nos seus requisitos.

#### <a name="example-1"></a>Exemplo 1:
Em sua matriz virtual, pretende ser capaz de

* Aprovisione um volume em camadas de 2 TB ou uma partilha.
* Aprovisione um volume em camadas de 1 TB ou uma partilha.
* Aprovisione um 300 GB do volume afixado localmente ou partilha.

Para o anterior volumes ou compartilhamentos, vamos calcule os requisitos de espaço no escalão local.

Em primeiro lugar, para cada volume/partilha em camadas, reserva local seria igual a 12% do tamanho do volume/partilha. Para a partilha/volume afixada localmente, reserva local é de 10% do tamanho da partilha/volume afixado localmente (para além do tamanho aprovisionado). Neste exemplo, precisa

* GB de 240 reserva local (para um volume em camadas 2 TB/partilha)
* Reserva local de 120 GB (para um volume em camadas 1 TB/partilha)
* 330 GB para volume afixado localmente ou partilha (aumento de 10% de reserva local para o tamanho de 300 GB aprovisionado)

O espaço total obrigatório no escalão local até agora é: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, é necessário, pelo menos, todo o espaço no escalão local que a reserva de única maior. Este é o valor extra é utilizado caso seja necessário restaurar a partir de um instantâneo de cloud. Neste exemplo, a maior reserva local é 330 GB (incluindo a reserva para o sistema de ficheiros), portanto, adicionaria isso para o GB 690: 690 GB + 330 GB = 1020 GB.
Se efetuámos restauros adicionais subsequentes, pode sempre libertamos o espaço de operação de restauro anterior.

Em terceiro lugar, precisamos 15% de seu total de espaço local até o momento para armazenar os instantâneos locais, para que apenas 85% dele está disponível. Neste exemplo, o que deve ocorrer em torno 1020 GB = 0.85&ast;TB de disco de dados aprovisionados. Assim, seria o disco de dados aprovisionados (1020&ast;(1/0.85)) = 1200 GB = 1.20 TB ~ 1.25 TB (levantada para quartile mais próximo)

Tendo em crescimento inesperado e restaurações de novo, deve aprovisionar um disco local de em torno 1,25-1,5 TB.

> [!NOTE]
> Recomendamos também que o disco local é fracamente aprovisionado. Esta recomendação é porque o espaço de restauro só é necessário quando deseja restaurar os dados que está com mais de cinco dias. Recuperação ao nível do item permite que restaure dados dos últimos cinco dias sem a necessidade do espaço extra para restauro.


#### <a name="example-2"></a>Exemplo 2:
Em sua matriz virtual, pretende ser capaz de

* Aprovisionar um volume em camadas de 2 TB
* Aprovisionar um volume localmente afixado de 300 GB

Com base na % de 12 de reserva de espaço local para volumes/partilhas em camadas e de 10% para localmente afixado de volumes/partilhas, precisamos

* Reserva local de 240 GB (2 TB em camadas volume/partilha)
* 330 GB para volume afixado localmente ou partilha (aumento de 10% de reserva local para o espaço de 300 GB aprovisionado)

Total de espaço necessária no escalão local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para restauro é 330 GB.

15% de seu disco total é usado para armazenar instantâneos, de forma que 0.85 só está disponível. Por isso, é o tamanho do disco (900&ast;(1/0.85)) = TB 1,06 ~ 1.25 TB (levantada para quartile mais próximo)

Tendo em qualquer crescimento inesperado, pode aprovisionar um disco local 1,25-1,5 TB.

### <a name="group-policy"></a>Política de grupo
Política de grupo é uma infraestrutura que permite implementar configurações específicas para utilizadores e computadores. Definições de política de grupo estão contidas em objetos de política de grupo (GPOs), quais são vinculados aos seguintes contentores de serviços de domínio do Active Directory (AD DS): sites, domínios ou unidades organizacionais (UOs). 

Se a sua matriz virtual está associado a um domínio, os GPOs podem ser aplicadas ao mesmo. Estes GPOs podem instalar aplicativos, como um software antivírus, que pode afetar negativamente a operação da matriz Virtual StorSimple.

Por conseguinte, recomendamos que:

* Certifique-se de que a sua matriz virtual está em sua própria unidade organizacional (UO) para o Active Directory.
* Certifique-se de que não existem objetos de política de grupo (GPOs) são aplicados a sua matriz virtual. Pode bloquear a herança para se certificar de que a matriz virtual (nó filho) não herda automaticamente quaisquer GPOs do pai. Para obter mais informações, aceda a [bloquear herança](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Redes
A configuração de rede para a sua matriz virtual é feita através da IU da web local. Uma interface de rede virtual é ativada através do hipervisor em que a matriz virtual está aprovisionada. Utilize o [definições de rede](storsimple-virtual-array-deploy3-fs-setup.md) página para configurar o endereço IP de interface de rede virtual, sub-rede e gateway.  Também pode configurar o servidor DNS primário e secundário, as definições de hora e as definições de proxy opcional para o seu dispositivo. A maioria da configuração da rede é uma configuração única. Reveja os [StorSimple requisitos de rede](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar a matriz virtual.

Ao implementar a sua matriz virtual, recomendamos que siga estas práticas recomendadas:

* Certifique-se de que a rede no qual a matriz virtual é implementada, sempre tem a capacidade de dedicar 5 Mbps largura de banda de Internet (ou mais).
  
  * Necessidade de largura de banda de Internet varia consoante as características da sua carga de trabalho e a taxa de alteração de dados.
  * A alteração de dados que pode ser manipulada é diretamente proporcional ao seu largura de banda de Internet. Por exemplo quando fazer uma cópia de segurança, uma largura de banda 5 Mbps capaz de acomodar uma alteração de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), pode manipular quatro vezes mais de alteração de dados (72 GB).
* Certifique-se de que a conectividade à Internet está sempre disponível. Ligações de Internet esporádicos ou pouco fiáveis para os dispositivos podem resultar em perda de acesso a dados na cloud e podem resultar numa configuração não suportada.
* Se planeia implementar o dispositivo como um servidor de iSCSI:
  
  * Recomendamos que desative o **obtém o endereço IP automaticamente** opção (DHCP).
  * Configure endereços IP estáticos. Tem de configurar um site primário e um servidor DNS secundário.
  * Se definir várias interfaces de rede no seu virtual matriz, apenas a primeira interface de rede (por predefinição, esta interface está **Ethernet**) pode entrar na cloud. Para controlar o tipo de tráfego, pode criar várias interfaces de rede virtual na sua matriz virtual (configurado como um servidor de iSCSI) e ligar essas interfaces a sub-redes diferentes.
* Para limitar a largura de banda do cloud apenas (utilizado pela matriz virtual), configurar a limitação no roteador ou firewall. Se definir a limitação no seu hipervisor, irá limitar a todos os protocolos, incluindo o iSCSI e SMB em vez de apenas a largura de banda na cloud.
* Certifique-se que a mesma hora para hipervisores é ativado. Se utilizar o Hyper-V, selecione a sua matriz virtual no Gestor de Hyper-V, aceda a **configurações &gt; serviços de integração**e certifique-se de que o **sincronização de hora** está marcada.

### <a name="storage-accounts"></a>Contas de armazenamento
Matriz Virtual StorSimple pode ser associado uma única conta de armazenamento. Esta conta de armazenamento pode ser uma conta de armazenamento gerado automaticamente, uma conta na mesma subscrição que o serviço, ou relacionados com uma conta de armazenamento para outra subscrição. Para obter mais informações, consulte como [gerir contas de armazenamento para a sua matriz virtual](storsimple-virtual-array-manage-storage-accounts.md).

Utilize as seguintes recomendações para contas de armazenamento associadas à sua matriz virtual.

* Quando ligar várias matrizes virtuais com uma única conta de armazenamento, avaliar a capacidade máxima (64 TB) para uma matriz virtual e o tamanho máximo (500 TB) de uma conta de armazenamento. Isso limita o número de matrizes virtuais normal que pode ser associado essa conta de armazenamento para cerca de 7.
* Ao criar uma nova conta de armazenamento
  
  * Recomendamos que criá-la na região mais próxima ao escritório em que a sua matriz Virtual StorSimple é implementada para minimizar as latências sucursais/escritórios remotos.
  * Lembre-se de que não é possível mover uma conta de armazenamento em regiões diferentes. Também não é possível mover um serviço em várias subscrições.
  * Utilize uma conta de armazenamento que implementa a redundância entre os datacenters. Armazenamento Georredundante (GRS), o armazenamento com redundância de zona (ZRS) e o armazenamento localmente redundante (LRS) são todas suportadas para utilização com a sua matriz virtual. Para obter mais informações sobre os diferentes tipos de contas de armazenamento, aceda a [replicação de armazenamento do Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Compartilhamentos e volumes
Para a sua matriz Virtual StorSimple, pode aprovisionar partilhas quando está configurado como um servidor de ficheiros e os volumes quando configurado como um servidor de iSCSI. As melhores práticas para a criação de compartilhamentos e volumes estão relacionadas com o tamanho e o tipo de configurado.

#### <a name="volumeshare-size"></a>Tamanho do volume/partilha
Na sua matriz virtual, pode aprovisionar partilhas quando está configurado como um servidor de ficheiros e os volumes quando configurado como um servidor de iSCSI. As melhores práticas para a criação de compartilhamentos e volumes estão relacionadas com o tamanho e o tipo de configurado. 

Tenha em atenção as seguintes melhores práticas quando são aprovisionados partilhas ou volumes no dispositivo virtual.

* Os tamanhos de ficheiro relativo ao tamanho aprovisionado de uma partilha em camadas podem afetar o desempenho de camadas. Trabalhar com ficheiros grandes pode resultar numa camada lenta horizontalmente. Ao trabalhar com ficheiros grandes, recomendamos que o maior arquivo for menor que 3% do tamanho da partilha.
* Um máximo de 16 volumes/partilhas pode ser criado na matriz virtual. Para os limites de tamanho de volumes/partilhas localmente afixados e em camadas, sempre consulte a [limites da matriz Virtual do StorSimple](storsimple-ova-limits.md).
* Ao criar um volume, avaliar o consumo de dados esperados, bem como crescimento futuro. O volume não é possível expandir mais tarde.
* Quando o volume tiver sido criado, não é possível reduzir o tamanho do volume no StorSimple.
* Ao escrever um volume em camadas no StorSimple, quando os dados de volume atingirem um determinado limiar (relativo ao espaço local reservado para o volume), está limitada a e/s. Continuar a escrever para este volume pode atrasar a e/s significativamente. Embora seja possível escrever para um volume em camadas para além de sua capacidade aprovisionada (não ativamente impedir que o utilizador de escrita para lá da capacidade aprovisionada), verá uma notificação de alerta para o efeito que tenha sobre-subscrita. Quando vir o alerta, é imperativo que tomar medidas corretivas, como eliminar os dados de volume (expansão do volume não é atualmente suportado).
* Para casos de utilização de recuperação após desastre, como o número de volumes/partilhas permitidas é 16 e o número máximo de partilhas/volumes que podem ser processados em paralelo também é 16, o número de volumes/partilhas não tem um efeito nos seus RPO e Rto.

#### <a name="volumeshare-type"></a>Tipo de volume/partilha
O StorSimple suporta dois tipos de volume/partilha com base na utilização: localmente afixado e em camadas. Afixado localmente volumes/partilhas são amplamente aprovisionadas, ao passo que os volumes/partilhas em camadas são com aprovisionamento dinâmico. Não é possível converter uma partilha/volume afixada localmente para em camadas ou *vice-versa* depois de criada.

Recomendamos que implemente as seguintes práticas recomendadas ao configurar o StorSimple volumes/partilhas:

* Identifica o tipo de volume com base nas cargas de trabalho que pretende implementar antes de criar um volume. Utilize volumes afixados localmente para cargas de trabalho que necessitam de garantias locais de dados (mesmo durante uma falha na cloud) e que requerem latências de cloud de baixa. Depois de criar um volume na sua matriz virtual, não é possível alterar o tipo de volume do localmente afixado a em camadas ou *vice versa*. Por exemplo, criar volumes localmente afixados ao implementar cargas de trabalho do SQL ou cargas de trabalho que aloja máquinas virtuais (VMs); Utilize volumes em camadas para cargas de trabalho de partilha de ficheiros.


#### <a name="volume-format"></a>Formato de volume
Depois de criar volumes do StorSimple no seu servidor de iSCSI, é preciso inicializar, montar e formatar os volumes. Esta operação é executada no anfitrião ligado ao seu dispositivo StorSimple. Recomenda-se seguir as melhores práticas quando montar e formatar volumes do anfitrião do StorSimple.

* Efetue uma formatação rápida em todos os volumes do StorSimple.
* Ao formatar um volume StorSimple, utilize um tamanho de unidade de alocação (UA) de 64 KB (a predefinição é 4 KB). A 64 KB UAS baseia-se no teste executado internamente para cargas de trabalho comuns do StorSimple e outras cargas de trabalho.
* Quando utilizar o StorSimple Virtual Array configurado como um servidor de iSCSI, não utilize volumes expandidos ou discos dinâmicos como estes volumes ou discos não são suportados pelo StorSimple.

#### <a name="share-access"></a>Acesso de partilha
Ao criar partilhas no servidor de ficheiros de matriz virtual, siga estas diretrizes:

* Ao criar uma partilha, atribua um grupo de utilizadores como um administrador de partilha, em vez de um único utilizador.
* Pode gerir as permissões de NTFS depois de criar a partilha ao editar as partilhas através do Explorador do Windows.

#### <a name="volume-access"></a>Acesso ao volume
Ao configurar os volumes de iSCSI na sua matriz Virtual StorSimple, é importante controlar o acesso sempre que necessário. Para determinar quais servidores de anfitrião podem aceder aos volumes, criar e associar os registos de controlo de acesso (ACRs) com volumes do StorSimple.

Utilize as seguintes práticas recomendadas ao configurar ACRs para volumes do StorSimple:

* Associe sempre pelo menos um ACR com um volume.

* Ao atribuir mais de um ACR para um volume, certifique-se de que o volume não é exposto de forma em que pode ser simultaneamente acedido por mais de um anfitrião não agrupado. Se tiver atribuído ACRs vários para um volume, uma mensagem de aviso aparece para rever a sua configuração.

### <a name="data-security-and-encryption"></a>Segurança e encriptação de dados
A sua matriz Virtual StorSimple tem funcionalidades de segurança e encriptação de dados que garantir a confidencialidade e integridade dos seus dados. Ao usar esses recursos, recomenda-se que siga estas práticas recomendadas: 

* Defina uma chave de encriptação de armazenamento na nuvem para gerar a encriptação AES-256 antes dos dados são enviados a partir da sua matriz virtual para a cloud. Esta chave não é necessária se os dados são encriptados para começar. A chave pode ser gerada e seguros, tais como a utilizar um sistema de gestão de chaves [do Azure key vault](../key-vault/key-vault-whatis.md).
* Ao configurar a conta de armazenamento através do serviço StorSimple Manager, certifique-se de que ativa o modo SSL para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a cloud.
* Voltar a gerar as chaves para as suas contas de armazenamento (acessando o serviço de armazenamento do Azure) periodicamente para a conta para todas as alterações aceder com base na lista de alterações de administradores.
* Os dados na sua matriz virtual são compactados e eliminação dos duplicados efetuados antes do envio para o Azure. Não recomendamos utilizar o serviço de função de eliminação de duplicados de dados no seu anfitrião do Windows Server.

## <a name="operational-best-practices"></a>Melhores práticas operacionais
As melhores práticas operacionais são diretrizes que devem ser seguidas durante a operação da matriz virtual ou o gerenciamento diário. Essas práticas cobrem as tarefas de gestão específicos, como fazer cópias de segurança, restaurar a partir de um conjunto de backup, realizar uma ativação pós-falha, desativar e eliminar a matriz, monitorizar a utilização de sistema e o estado de funcionamento e execução de vírus analisa em sua matriz virtual.

### <a name="backups"></a>Cópias de segurança
Os dados na sua matriz virtual é uma cópia de segurança para a cloud de duas formas, um padrão automatizada de cópia de segurança diária de todo o dispositivo, começando às 22:30 ou através de uma cópia de segurança a pedido manual. Por predefinição, o dispositivo cria automaticamente os instantâneos de cloud diários de todos os dados que residem no mesmo. Para obter mais informações, aceda a [cópia de segurança a sua matriz Virtual StorSimple](storsimple-virtual-array-backup.md).

Não não possível alterar a frequência e a retenção associado com as cópias de segurança padrão, mas pode configurar o tempo em que as cópias de segurança diárias são iniciadas todos os dias. Ao configurar a hora de início para cópias de segurança automáticas, recomendamos que:

* Agende as cópias de segurança para horas de ponta. Hora de início de cópia de segurança não deve coincidir com o anfitrião várias e/s.
* Inicie uma cópia de segurança a pedido manual quando planear efetuar uma ativação pós-falha do dispositivo ou antes da janela de manutenção, para proteger os dados na sua matriz virtual.

### <a name="restore"></a>Restauro
Pode restaurar a partir de uma cópia de segurança definida de duas formas: Restaurar para outro volume ou partilha ou efetuar uma recuperação ao nível do item (disponível apenas numa matriz virtual configurada como um servidor de ficheiros). Recuperação ao nível do item permite-lhe efetuar uma recuperação granular de ficheiros e pastas a partir de uma cópia de segurança de cloud de todas as partilhas no dispositivo StorSimple. Para obter mais informações, aceda a [restaurar a partir de uma cópia de segurança](storsimple-virtual-array-clone.md).

Ao realizar uma restauração, mantenha as seguintes diretrizes em mente:

* A sua matriz Virtual StorSimple não suporta o restauro no local. No entanto pode prontamente ser alcançado por um processo de dois passos: Liberte espaço virtual na matriz e, em seguida, restaure para outro volume/partilha.
* Quando restaurar a partir de um volume local, tenha em atenção o restauro será uma operação de longa execução. Embora o volume pode rapidamente ficar online, os dados continuam a ser alimentado em segundo plano.
* O tipo de volume permanece igual durante o processo de restauração. Um volume em camadas é restaurado para outro volume em camadas e um volume afixado localmente para outro localmente afixado de volume.
* Quando tentar restaurar um volume ou uma partilha de um conjunto de cópia de segurança, se falhar a tarefa de restauro, um volume de destino ou partilha ainda pode ser criada no portal. É importante que eliminar o volume de destino não utilizadas ou partilhar no portal para minimizar qualquer futuros problemas resultantes desse elemento.

### <a name="failover-and-disaster-recovery"></a>Ativação pós-falha e recuperação após desastre
Uma ativação pós-falha de dispositivo permite-lhe migrar seus dados a partir de um *origem* dispositivo no Centro de dados para outro *destino* dispositivo localizado no mesmo ou outra localização geográfica. A ativação pós-falha destina-se a todo o dispositivo. Durante a ativação pós-falha, os dados de cloud para o dispositivo de origem forem alterados propriedade para que o dispositivo de destino.

Para a sua matriz Virtual StorSimple, pode apenas a ativação pós-falha para outra matriz virtual gerenciada pelo mesmo serviço StorSimple Manager. Não é permitida uma ativação pós-falha para um dispositivo da 8000 série ou uma matriz gerido por um serviço StorSimple Manager diferentes (que aquele para o dispositivo de origem). Para saber mais sobre as considerações de ativação pós-falha, aceda a [pré-requisitos para a ativação pós-falha](storsimple-virtual-array-failover-dr.md).

Quando realizar uma ativação pós-falha sua matriz virtual, tenha em atenção o seguinte:

* Para uma ativação pós-falha planeada, é uma prática recomendada para todos as volumes/partilhas offline antes de iniciar a ativação pós-falha. Siga as instruções específicas do sistema operativo para colocar as volumes/partilhas offline no anfitrião primeiro e, em seguida, tirar aqueles offline no seu dispositivo virtual.
* Para uma ficheiro server recuperação após desastre (DR), recomendamos que se associe o dispositivo de destino ao mesmo domínio como origem para que as permissões de partilha são automaticamente resolvidas. Apenas a ativação pós-falha para um dispositivo de destino no mesmo domínio é suportada nesta versão.
* Assim que a DR é concluída com êxito, o dispositivo de origem é eliminado automaticamente. Apesar do dispositivo já não está disponível, a máquina virtual que aprovisionou no sistema host ainda está a consumir recursos. Recomendamos que elimine esta máquina virtual do sistema anfitrião para impedir que os eventuais encargos a acumular.
* Tenha em atenção que, mesmo se não for bem-sucedida, a ativação pós-falha **os dados estão sempre seguros na cloud**. Considere os seguintes três cenários em que a ativação pós-falha não foi concluída com êxito:
  
  * Ocorreu uma falha nas fases iniciais da ativação pós-falha, como quando estão a ser efetuadas as pré-verificações de DR. Nesta situação, o dispositivo de destino ainda será possível usar. Pode tentar novamente a ativação pós-falha no mesmo dispositivo de destino.
  * Ocorreu uma falha durante o processo de ativação pós-falha real. Neste caso, o dispositivo de destino é marcado inutilizável. Tem de aprovisionar e configurar outra matriz virtual de destino e utilizá-la para ativação pós-falha.
  * A ativação pós-falha foi concluída após o qual o dispositivo de origem foi eliminado, mas o dispositivo de destino tem problemas e não pode aceder a quaisquer dados. Os dados ainda seja seguros na cloud e podem ser facilmente obtidos através da criação de uma matriz e, em seguida, utilizá-lo como um dispositivo de destino para a DR.

### <a name="deactivate"></a>desativar
Ao desativar uma StorSimple Virtual Array, servidor de ligação entre o dispositivo e o serviço StorSimple Manager correspondente. Desativação é um **permanente** operação e não pode ser anulada. Um dispositivo desativado não pode ser registado com o serviço StorSimple Manager novamente. Para obter mais informações, aceda a [desativar e eliminar a sua matriz Virtual StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Mantenha as seguintes práticas recomendadas em mente quando desativar a sua matriz virtual:

* Tire um instantâneo de cloud de todos os dados antes de desativar um dispositivo virtual. Ao desativar uma matriz virtual, todos os dados do dispositivo local é perdido. Um instantâneo de cloud, poderá recuperar os dados num estágio posterior.
* Antes de desativar uma StorSimple Virtual Array, certifique-se para ou elimina os clientes e anfitriões que dependem desse dispositivo.
* Elimine um dispositivo desativado se já não estiver a utilizar, para que ele não acumular despesas.

### <a name="monitoring"></a>Monitorização
Para garantir que a sua matriz Virtual StorSimple está em bom estado de funcionamento contínuo, terá de monitorizar a matriz e certifique-se de que recebe informações do sistema, incluindo alertas. Para monitorizar o estado de funcionamento geral da matriz virtual, implemente as seguintes práticas recomendadas:

* Configure a monitorização para controlar a utilização do disco de seu disco de dados da matriz virtual, bem como o disco do SO. Se executar o Hyper-V, pode utilizar uma combinação de System Center Virtual Machine Manager (SCVMM) e o System Center Operations Manager para monitorizar os anfitriões de virtualização.
* Configure notificações de e-mail na sua matriz virtual para enviar alertas ter determinados níveis de utilização.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicações de análise de pesquisa do índice e vírus
Uma matriz Virtual StorSimple pode camadas automaticamente os dados de escalão local para a cloud do Microsoft Azure. Quando um aplicativo como uma pesquisa de índice ou uma verificação de vírus é utilizado para analisar os dados armazenados no StorSimple, terá de tomar conta que os dados na cloud não obter acedidos e obtidos para a camada de local.

Recomendamos que implemente as seguintes práticas recomendadas ao configurar a análise de pesquisa ou vírus do índice em sua matriz virtual:

* Desative quaisquer operações automaticamente configurado da análise completa.
* Para volumes em camadas, configure a aplicação de análise de índice pesquisa ou vírus, para efetuar uma análise incremental. Isso seria analisar apenas os novos dados provável que reside no escalão local. Os dados que estão em camadas para a cloud não são acedidos durante uma operação incremental.
* Certifique-se de que os filtros de pesquisa correto e as configurações estão configuradas para que apenas os tipos de ficheiros pretendidos obterem analisados. Por exemplo, os ficheiros (JPEG, GIF e TIFF) de imagem e desenhos de engenharia não deve ser analisado durante a recriação de índice completo ou incrementais.

Se utilizar o processo de indexação do Windows, siga estas diretrizes:

* Não utilize o indexador do Windows para volumes em camadas como solicitar grandes quantidades de dados (TB) da cloud, se o índice tem de ser recriados com frequência. Reconstrua o índice teria de obter todos os tipos de ficheiro para indexar seu conteúdo.
* Utilize o Windows a indexação de processo para volumes afixados localmente como isso seria apenas acesso a dados nas camadas locais para criar o índice (não serão possível aceder os dados na cloud).

### <a name="byte-range-locking"></a>Bloqueio de intervalo de bytes
Aplicativos podem bloquear um intervalo especificado de bytes dos ficheiros. Se o bloqueio de intervalo de bytes estiver ativada nas aplicações que estiver a escrever para o seu StorSimple, em seguida, disposição em camadas não funciona em sua matriz virtual. Para a disposição em camadas para trabalhar, todas as áreas de arquivos acessados devem ser desbloqueadas. Bloqueio de intervalo de bytes não é suportado com volumes em camadas na sua matriz virtual.

Medidas recomendadas para minimizar o bloqueio de intervalo de bytes incluem:

* Desative bloqueio em sua lógica de aplicativo de intervalo de bytes.
* Volumes afixados de utilização local (em vez de em camadas) para os dados associados esta aplicação. Os volumes afixados localmente não camada para a cloud.
* Quando utilizar volumes afixados localmente com bloqueio de intervalo de bytes ativado, o volume pode ficar online antes do restauro está concluído. Nesses casos, tem de aguardar o restauro ser concluído.

## <a name="multiple-arrays"></a>Várias matrizes
Várias matrizes de virtual poderão ter de ser implementado para a conta para um conjunto de trabalho cada vez maior de dados que foi transbordam para a cloud, afetando assim o desempenho do dispositivo. Nesses casos, é melhor para os dispositivos de dimensionamento à medida que aumenta o conjunto de trabalho. Isto requer um ou mais dispositivos a adicionar no Centro de dados no local. Ao adicionar os dispositivos, pode:

* Divida o conjunto atual de dados.
* Implemente novas cargas de trabalho para o novo dispositivo (s).
* Se implementar várias matrizes virtuais, recomendamos que do balanceamento de carga de perspectiva, distribuir a matriz em hosts de hipervisor diferentes.
* Várias matrizes virtuais (quando configurado como um servidor de ficheiros ou um servidor de iSCSI) podem ser implementadas num Namespace de sistema de ficheiros distribuído. Para obter passos detalhados, consulte [distribuído sistema espaço de nomes de solução de ficheiros com o guia de implementação de armazenamento de Cloud híbrida](https://www.microsoft.com/download/details.aspx?id=45507). Replicação de sistema de ficheiros distribuído atualmente não é recomendada para utilização com a matriz virtual. 

## <a name="see-also"></a>Consulte também
Saiba como [administrar a sua matriz Virtual StorSimple](storsimple-virtual-array-manager-service-administration.md) através do serviço StorSimple Manager.

