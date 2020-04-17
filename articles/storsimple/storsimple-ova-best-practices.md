---
title: Boas práticas para StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve as melhores práticas para implementar e gerir o StorSimple Virtual Array.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: bdf69a9ff7b3260b47042f296a47826e3c52387b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460652"
---
# <a name="storsimple-virtual-array-best-practices"></a>Práticas recomendadas da Matriz Virtual do StorSimple

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O Microsoft Azure StorSimple Virtual Array é uma solução de armazenamento integrada que gere tarefas de armazenamento entre um dispositivo virtual no local que funciona num hipervisor e armazenamento em nuvem do Microsoft Azure. StorSimple Virtual Array é uma alternativa eficiente e rentável à matriz física da série 8000. A matriz virtual pode funcionar na sua infraestrutura hipervisor existente, suporta tanto o iSCSI como os protocolos SMB, e é adequado para cenários remotos de escritório/filial. Para obter mais informações sobre as soluções StorSimple, vá ao [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo abrange as melhores práticas implementadas durante a configuração, implantação e gestão inicial do StorSimple Virtual Array. Estas boas práticas fornecem diretrizes validadas para a configuração e gestão da sua matriz virtual. Este artigo é direcionado para os administradores de TI que implementam e gerem as matrizes virtuais nos seus datacenters.

Recomendamos uma revisão periódica das melhores práticas para ajudar a garantir que o seu dispositivo ainda está em conformidade quando são feitas alterações ao fluxo de configuração ou operação. Caso encontre algum problema durante a implementação destas melhores práticas na sua matriz virtual, [contacte](storsimple-virtual-array-log-support-ticket.md) o Microsoft Support para obter assistência.

## <a name="configuration-best-practices"></a>Configuração das melhores práticas
Estas boas práticas abrangem as diretrizes que devem ser seguidas durante a configuração e implantação inicial das matrizes virtuais. Estas boas práticas incluem as relacionadas com o fornecimento da máquina virtual, configurações de políticas de grupo, dimensionamento, configuração da rede, configuração de contas de armazenamento e criação de partilhas e volumes para a matriz virtual. 

### <a name="provisioning"></a>Aprovisionamento
StorSimple Virtual Array é uma máquina virtual (VM) aprovisionada no hipervisor (Hyper-V ou VMware) do seu servidor anfitrião. Ao fornecer a máquina virtual, certifique-se de que o seu anfitrião é capaz de dedicar recursos suficientes. Para mais informações, vá aos [requisitos mínimos](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) de recursos para fornecer uma matriz.

Implementar as seguintes melhores práticas ao fornecer a matriz virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**Geração 2** VM para utilização com o Windows Server 2012 ou posterior mente e uma imagem *.vhdx.* <br></br> **Geração 1** VM para utilização com um Windows Server 2008 ou mais tarde e uma imagem *.vhd.* |Utilize a versão 8 da máquina virtual quando utilizar a imagem *.vmdk.* |
| **Tipo de memória** |Configure como **memória estática.** <br></br> Não utilize a opção **de memória dinâmica.** | |
| **Tipo de disco de dados** |Disposição como **expansão dinâmica.**<br></br> **O tamanho fixo** leva muito tempo. <br></br> Não utilize a opção de **dismissão.** |Use a **opção de provisão fina.** |
| **Modificação do disco de dados** |A expansão ou a redução não são permitidas. Uma tentativa de fazê-lo resulta na perda de todos os dados locais no dispositivo. |A expansão ou a redução não são permitidas. Uma tentativa de fazê-lo resulta na perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Dimensionamento
Ao dimensionar o seu StorSimple Virtual Array, considere os seguintes fatores:

* Reserva local para volumes ou ações. Aproximadamente 12% do espaço é reservado no nível local para cada volume ou partilha hierárquico. Cerca de 10% do espaço também é reservado para um volume fixado localmente para sistema de ficheiros.
* Snapshot sobrecarga. Cerca de 15% de espaço no nível local é reservado para instantâneos.
* Necessidade de restauros. Se fizer a restauração como uma nova operação, o dimensionamento deve explicar o espaço necessário para restaurar. Restaurar é feito a uma parte ou volume do mesmo tamanho.
* Algum tampão deve ser atribuído para qualquer crescimento inesperado.

Com base nos fatores anteriores, os requisitos de dimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Os exemplos que se seguem ilustram como pode dimensionar uma matriz virtual com base nas suas necessidades.

#### <a name="example-1"></a>Exemplo 1:
Na sua matriz virtual, você quer ser capaz de

* prever um volume ou partilha de 2 TB.
* prever um volume ou partilha de 1 TB.
* prever um volume ou partilha de 300-GB fixado localmente.

Para os volumes ou ações anteriores, vamos calcular os requisitos de espaço no nível local.

Em primeiro lugar, para cada volume/partilha hierárquico, a reserva local seria igual a 12% do volume/tamanho da partilha. Para o volume/parte fixado localmente, a reserva local é de 10 % do volume/tamanho de quota fixado localmente (para além do tamanho previsto). Neste exemplo, você precisa

* Reserva local de 240-GB (para um volume/ação tiered 2 TB)
* Reserva local de 120 GB (para um volume/ação tiered 1-TB)
* 330-GB para volume ou partilha localmente fixado (adicionando 10 % da reserva local ao tamanho previsto de 300 GB)

O espaço total necessário no nível local até agora é: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, precisamos de pelo menos tanto espaço no nível local como a maior reserva única. Esta quantidade extra é usada no caso de precisar de restaurar a partir de um instantâneo em nuvem. Neste exemplo, a maior reserva local é de 330 GB (incluindo reserva para sistema de ficheiros), por isso acrescenta-se isso aos 690 GB: 690 GB + 330 GB = 1020 GB.
Se efetuarmos restauros adicionais subsequentes, podemos sempre libertar o espaço da operação de restauro anterior.

Em terceiro lugar, precisamos de 15 % do seu espaço local total até agora para armazenar instantâneos locais, de modo que apenas 85% dele está disponível. Neste exemplo, seria cerca de 1020 GB&ast;= 0,85 tb de disco de dados provisionado. Assim, o disco de dados provisionado&ast;seria (1020 (1/0,85)= 1200 GB = 1,20 TB ~ 1,25 TB (arredondamento para quartil mais próximo)

Tendo em conta o crescimento inesperado e novos restauros, deverá fornecer um disco local de cerca de 1,25 - 1,5 TB.

> [!NOTE]
> Recomendamos também que o disco local seja pouco provisionado. Esta recomendação é porque o espaço de restauro só é necessário quando se quer restaurar dados com mais de cinco dias. A recuperação ao nível do item permite restaurar os dados nos últimos cinco dias sem necessitar de espaço extra para restauro.


#### <a name="example-2"></a>Exemplo 2:
Na sua matriz virtual, você quer ser capaz de

* fornecer um volume de 2 TB tiered
* provisão de um volume de 300-GB fixado localmente

Com base em 12 % da reserva espacial local para volumes/ações hierárquicos e 10 % para volumes/ações locais fixados, precisamos

* Reserva local de 240-GB (para 2 TB volume/partilha)
* 330-GB para volume ou partilha localmente fixado (adicionando 10% da reserva local ao espaço aprovisionado de 300 GB)

O espaço total necessário no nível local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para restaurar é de 330 GB.

15 % do seu disco total é utilizado para armazenar instantâneos de modo a que apenas 0,85 esteja disponível. Assim, o tamanho do disco&ast;é (900 (1/0,85)) = 1,06 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Tendo em conta qualquer crescimento inesperado, pode fornecer um disco local de 1,25 - 1,5 TB.

### <a name="group-policy"></a>Política de grupo
A Política de Grupo é uma infraestrutura que lhe permite implementar configurações específicas para utilizadores e computadores. As definições de Política de Grupo estão contidas em objetos de Política de Grupo (GPOs), que estão ligados aos seguintes contentores ative directory Domain Services (AD DS): sites, domínios ou unidades organizacionais (OUs). 

Se a sua matriz virtual for unida ao domínio, os GPOs podem ser aplicados a ela. Estes GPOs podem instalar aplicações como um software antivírus que pode afetar negativamente o funcionamento do StorSimple Virtual Array.

Por isso, recomendamos que:

* Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para diretório ativo.
* Certifique-se de que não são aplicados objetos de política de grupo (GPOs) à sua matriz virtual. Pode bloquear a herança para garantir que a matriz virtual (nó infantil) não herda automaticamente quaisquer GPOs do progenitor. Para mais informações, vá bloquear a [herança.](https://technet.microsoft.com/library/cc731076.aspx)

### <a name="networking"></a>Redes
A configuração da rede para a sua matriz virtual é feita através da UI web local. Uma interface de rede virtual é ativada através do hipervisor no qual a matriz virtual é aprovisionada. Utilize a página [Definições](storsimple-virtual-array-deploy3-fs-setup.md) de Rede para configurar o endereço IP, subnet e gateway da interface de rede virtual.  Também pode configurar o servidor DNS primário e secundário, as definições de tempo e as definições de procuração opcionais para o seu dispositivo. A maior parte da configuração da rede é uma configuração única. Reveja os requisitos de [rede StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar a matriz virtual.

Ao implementar a sua matriz virtual, recomendamos que siga estas boas práticas:

* Certifique-se de que a rede em que a matriz virtual é implantada tem sempre a capacidade de dedicar largura de banda de 5 Mbps à Internet (ou mais).
  
  * A largura de banda da Internet varia consoante as suas características de carga de trabalho e a taxa de variação de dados.
  * A alteração de dados que pode ser tratada é diretamente proporcional à largura de banda da Internet. Como exemplo ao receber uma cópia de segurança, uma largura de banda de 5 Mbps pode acomodar uma mudança de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), pode lidar com quatro vezes mais alterações de dados (72 GB).
* Certifique-se de que a conectividade com a Internet está sempre disponível. Ligações esporádicas ou pouco fiáveis à Internet aos dispositivos podem resultar numa perda de acesso aos dados na nuvem e podem resultar numa configuração não suportada.
* Se planeia implementar o seu dispositivo como servidor iSCSI:
  
  * Recomendamos que desative automaticamente a opção **Get IP** (DHCP).
  * Configure endereços IP estáticos. Deve configurar um servidor Primário e Secundário de DNS.
  * Se definir várias interfaces de rede na sua matriz virtual, apenas a primeira interface de rede (por padrão, esta interface é **Ethernet)** pode chegar à nuvem. Para controlar o tipo de tráfego, pode criar múltiplas interfaces de rede virtual na sua matriz virtual (configurada como servidor iSCSI) e ligar essas interfaces a diferentes subredes.
* Para acelerar apenas a largura de banda da nuvem (utilizada pela matriz virtual), configure o estrangulamento no router ou na firewall. Se definir o estrangulamento no seu hipervisor, irá acelerar todos os protocolos, incluindo iSCSI e SMB em vez de apenas a largura de banda da nuvem.
* Certifique-se de que a sincronização do tempo para os hipervisores está ativada. Se utilizar o Hyper-V, selecione a sua matriz virtual no Gestor De Hiper-V, vá aos Serviços de Integração de **Definições, &gt; **e certifique-se de que a **sincronização** do tempo é verificada.

### <a name="storage-accounts"></a>Contas de armazenamento
StorSimple Virtual Array pode ser associado a uma única conta de armazenamento. Esta conta de armazenamento pode ser uma conta de armazenamento gerada automaticamente, uma conta na mesma subscrição que o serviço, ou uma conta de armazenamento relacionada com outra subscrição. Para mais informações, consulte como gerir as contas de [armazenamento da sua matriz virtual](storsimple-virtual-array-manage-storage-accounts.md).

Utilize as seguintes recomendações para contas de armazenamento associadas à sua matriz virtual.

* Ao ligar várias matrizes virtuais a uma única conta de armazenamento, fator na capacidade máxima (64 TB) para uma matriz virtual e o tamanho máximo (500 TB) para uma conta de armazenamento. Isto limita o número de matrizes virtuais de tamanho completo que podem ser associadas a essa conta de armazenamento a cerca de 7.
* Ao criar uma nova conta de armazenamento
  
  * Recomendamos que o crie na região mais próxima do escritório remoto/filial onde o seu StorSimple Virtual Array é implantado para minimizar as tardios.
  * Tenha em mente que não pode mover uma conta de armazenamento em diferentes regiões. Também não é possível mover um serviço através de subscrições.
  * Utilize uma conta de armazenamento que implemente o despedimento entre os datacenters. Armazenamento Geo-Redundante (GRS), Armazenamento Redundante da Zona (ZRS) e Armazenamento Redundante Local (LRS) são todos suportados para uso com a sua matriz virtual. Para obter mais informações sobre os diferentes tipos de contas de armazenamento, vá à replicação de [armazenamento Azure.](../storage/common/storage-redundancy.md)

### <a name="shares-and-volumes"></a>Ações e volumes
Para o seu StorSimple Virtual Array, pode fornecer ações quando estiver configurado como servidor de ficheiros e volumes quando configurado como servidor iSCSI. As melhores práticas para a criação de partilhas e volumes estão relacionadas com o tamanho e o tipo configurado.

#### <a name="volumeshare-size"></a>Volume/Tamanho de partilha
Na sua matriz virtual, pode fornecer ações quando estiver configurada como servidor de ficheiros e volumes quando configurado como servidor iSCSI. As melhores práticas para a criação de partilhas e volumes relacionam-se com o tamanho e o tipo configurado. 

Tenha em mente as seguintes boas práticas ao fornecer ações ou volumes no seu dispositivo virtual.

* Os tamanhos dos ficheiros relativos ao tamanho provisionado de uma parte tiered podem ter impacto no desempenho do tiering. Trabalhar com ficheiros grandes pode resultar numa saída lenta. Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação.
* Pode ser criado um máximo de 16 volumes/ações na matriz virtual. Para os limites de tamanho dos volumes/ações fixas e nitentes locais, consulte sempre os [limites de Matriz Virtual StorSimple](storsimple-ova-limits.md).
* Ao criar um volume, fator no consumo de dados esperado, bem como no crescimento futuro. O volume ou a partilha não podem ser expandidos posteriormente.
* Uma vez criado o volume/partilha, não é possível reduzir o tamanho do volume/parte no StorSimple.
* Ao escrever para um volume hierárquico no StorSimple, quando os dados de volume atingem um determinado limiar (em relação ao espaço local reservado para o volume), a IO é estrangulada. Continuar a escrever para este volume atrasa significativamente a OI. Embora possa escrever para um volume hierárquico para além da sua capacidade provisionada (não impedimos ativamente o utilizador de escrever para além da capacidade prevista), vê uma notificação de alerta no sentido de ter sobrestimado. Uma vez que veja o alerta, é imperativo que tome medidas corretivas como eliminar os dados de volume (a expansão do volume não é suportada atualmente).
* Para casos de utilização de recuperação de desastres, uma vez que o número de ações/volumes admissíveis é de 16 e o número máximo de ações/volumes que podem ser processados paralelamente é também de 16, o número de ações/volumes não tem influência nos seus RPO e RTOs.

#### <a name="volumeshare-type"></a>Tipo de volume/partilha
O StorSimple suporta dois tipos de volume/partilha com base na utilização: fixado localmente e nitente. Os volumes/ações fixados localmente são densamente provisionados, enquanto os volumes/ações hierárquicos são finamente provisionados. Não é possível converter um volume/partilha fixado localmente em camadas ou *vice-versa* uma vez criado.

Recomendamos que implemente as seguintes boas práticas ao configurar volumes/partilhas StorSimple:

* Identifique o tipo de volume com base nas cargas de trabalho que pretende implementar antes de criar um volume. Utilize volumes fixados localmente para cargas de trabalho que requerem garantias locais de dados (mesmo durante uma paragem na nuvem) e que requerem baixas llácturas em nuvem. Uma vez criado um volume na sua matriz virtual, não pode alterar o tipo de volume de fixado localmente para hierárquico ou *vice-versa*. Como exemplo, crie volumes fixados localmente ao implementar cargas de trabalho SQL ou cargas de trabalho que hospedam máquinas virtuais (VMs); utilizar volumes hierárquicos para cargas de trabalho de partilha de ficheiros.


#### <a name="volume-format"></a>Formato de volume
Depois de criar volumes StorSimple no seu servidor iSCSI, precisa de inicializar, montar e formatar os volumes. Esta operação é realizada no hospedeiro ligado ao seu dispositivo StorSimple. Recomenda-se as melhores práticas ao montar e fortar volumes no hospedeiro StorSimple.

* Execute um formato rápido em todos os volumes StorSimple.
* Ao formar um volume StorSimple, utilize uma unidade de alocação (AUS) de 64 KB (predefinição é de 4 KB). O 64 KB AUS baseia-se em testes feitos internamente para cargas de trabalho storSimple comuns e outras cargas de trabalho.
* Ao utilizar o StorSimple Virtual Array configurado como servidor iSCSI, não utilize volumes ou discos dinâmicos, uma vez que estes volumes ou discos não são suportados pelo StorSimple.

#### <a name="share-access"></a>Acesso a partilha
Ao criar ações no seu servidor de ficheiros de matriz virtual, siga estas orientações:

* Ao criar uma ação, designe um grupo de utilizadores como administrador de ações em vez de um único utilizador.
* Pode gerir as permissões NTFS após a criação da partilha editando as ações através do Windows Explorer.

#### <a name="volume-access"></a>Acesso ao volume
Ao configurar os volumes iSCSI no seu StorSimple Virtual Array, é importante controlar o acesso sempre que necessário. Para determinar quais os servidores anfitriões que podem aceder a volumes, criar e associar registos de controlo de acesso (ACRs) com volumes StorSimple.

Utilize as seguintes melhores práticas ao configurar ACRs para volumes StorSimple:

* Associe sempre pelo menos um ACR com um volume.

* Ao atribuir mais de um ACR a um volume, certifique-se de que o volume não é exposto de uma forma em que possa ser simultaneamente acedido por mais de um hospedeiro não agrupado. Se atribuiu vários ACRs a um volume, aparece uma mensagem de aviso para que reveja a sua configuração.

### <a name="data-security-and-encryption"></a>Segurança e encriptação de dados
O Seu StorSimple Virtual Array possui funcionalidades de segurança e encriptação de dados que garantem a confidencialidade e integridade dos seus dados. Ao utilizar estas funcionalidades, recomenda-se que siga estas boas práticas: 

* Defina uma chave de encriptação de armazenamento em nuvem para gerar encriptação AES-256 antes de os dados forem enviados da sua matriz virtual para a nuvem. Esta chave não é necessária se os seus dados forem encriptados para começar. A chave pode ser gerada e mantida segura usando um sistema de gestão chave, como [o cofre de chaves Azure](../key-vault/general/overview.md).
* Ao configurar a conta de armazenamento através do serviço StorSimple Manager, certifique-se de que ativa o modo TLS para criar um canal seguro para a comunicação de rede entre o seu dispositivo StorSimple e a nuvem.
* Regenerar periodicamente as chaves das suas contas de armazenamento (acedendo ao serviço de Armazenamento Azure) para contabilizar periodicamente quaisquer alterações de acesso com base na lista alterada de administradores.
* Os dados da sua matriz virtual são comprimidos e desduplicados antes de ser enviado para o Azure. Não recomendamos a utilização do serviço de função Deduplicação de Dados no seu anfitrião do Windows Server.

## <a name="operational-best-practices"></a>Melhores práticas operacionais
As melhores práticas operacionais são orientações que devem ser seguidas durante a gestão ou operação diária da matriz virtual. Estas práticas abrangem tarefas de gestão específicas, tais como a obtenção de backups, o restabelecimento de um conjunto de backup, a realização de uma falha, a desativação e a desativação da matriz, a monitorização do uso e saúde do sistema, e a execução de varreduras de vírus na sua matriz virtual.

### <a name="backups"></a>Cópias de segurança
Os dados da sua matriz virtual são apoiados até à nuvem de duas formas, uma cópia de segurança diária automatizada de todo o dispositivo a partir das 22:30 ou através de uma cópia de segurança manual a pedido. Por padrão, o dispositivo cria automaticamente imagens diárias em nuvem de todos os dados que residem nele. Para mais informações, vá [fazer o seu StorSimple Virtual Array](storsimple-virtual-array-backup.md).

A frequência e retenção associadas às cópias de segurança predefinidas não podem ser alteradas, mas pode configurar o tempo em que as cópias de segurança diárias são iniciadas todos os dias. Ao configurar a hora de início das cópias de segurança automatizadas, recomendamos que:

* Agende os seus reforços para horas fora do pico. A hora de início de reserva não deve coincidir com numerosos IO hospedeiros.
* Inicie uma cópia de segurança manual a pedido quando planeia executar uma falha no dispositivo ou antes da janela de manutenção, para proteger os dados da sua matriz virtual.

### <a name="restore"></a>Restauro
Pode restaurar a partir de um conjunto de backup de duas formas: restaurar para outro volume ou partilhar ou executar uma recuperação de nível de item (disponível apenas numa matriz virtual configurada como servidor de ficheiros). A recuperação ao nível do item permite-lhe fazer uma recuperação granular de ficheiros e pastas a partir de uma cópia de segurança em nuvem de todas as ações do dispositivo StorSimple. Para mais informações, vá [restaurar a partir de um backup](storsimple-virtual-array-clone.md).

Ao efetuar uma restauração, tenha em mente as seguintes orientações:

* O seu StorSimple Virtual Array não suporta restauro no local. No entanto, isto pode ser facilmente conseguido através de um processo em duas etapas: fazer espaço na matriz virtual e, em seguida, restaurar para outro volume/partilha.
* Ao restaurar de um volume local, lembre-se que a restauração será uma operação de longa duração. Embora o volume possa rapidamente estar online, os dados continuam a ser hidratados em segundo plano.
* O tipo de volume permanece o mesmo durante o processo de restauro. Um volume hierárquico é restaurado para outro volume hierárquico e um volume fixado localmente para outro volume fixado localmente.
* Ao tentar restaurar um volume ou uma parte de um conjunto de backup, se o trabalho de restauro falhar, um volume de destino ou partilha ainda pode ser criado no portal. É importante que apague este volume de destino não utilizado ou partilhe no portal para minimizar quaisquer problemas futuros decorrentes deste elemento.

### <a name="failover-and-disaster-recovery"></a>Falha e recuperação de desastres
Um failover do dispositivo permite-lhe migrar os seus dados de um dispositivo *de origem* no datacenter para outro *dispositivo-alvo* localizado no mesmo ou numa localização geográfica diferente. A falha do dispositivo é para todo o dispositivo. Durante o failover, os dados da nuvem para o dispositivo de origem alteram a propriedade para a do dispositivo alvo.

Para o seu StorSimple Virtual Array, só pode falhar com outra matriz virtual gerida pelo mesmo serviço StorSimple Manager. Não é permitida uma falha num dispositivo da série 8000 ou numa matriz gerida por um serviço StorSimple Manager diferente (do que o do dispositivo de origem). Para saber mais sobre as considerações de failover, vá aos [pré-requisitos para o failover do dispositivo](storsimple-virtual-array-failover-dr.md).

Ao realizar uma falha na sua matriz virtual, tenha em mente o seguinte:

* Para uma falha planeada, é uma melhor prática recomendada para desligar todos os volumes/ações antes de dar início à falha. Siga as instruções específicas do sistema operativo para desligar primeiro os volumes/partilhas no hospedeiro e, em seguida, desligá-los no seu dispositivo virtual.
* Para uma recuperação de desastrede servidor de ficheiros (DR), recomendamos que se junte ao dispositivo-alvo no mesmo domínio que a fonte, de modo a que as permissões de partilha sejam automaticamente resolvidas. Apenas a falha de um dispositivo-alvo no mesmo domínio é suportada nesta versão.
* Uma vez concluído o DR com sucesso, o dispositivo de origem é automaticamente eliminado. Embora o dispositivo já não esteja disponível, a máquina virtual que disponibilizou no sistema de acolhimento ainda está a consumir recursos. Recomendamos que elimine esta máquina virtual do seu sistema de acolhimento para evitar que quaisquer encargos se acumulem.
* Note que mesmo que a falha não seja bem sucedida, **os dados são sempre seguros na nuvem**. Considere os três cenários seguintes em que a failover não completou com sucesso:
  
  * Uma falha ocorreu nas fases iniciais da falha, como quando os pré-controlos dr estão sendo realizados. Nesta situação, o seu dispositivo alvo ainda é utilizável. Pode voltar a tentar a falha no mesmo dispositivo-alvo.
  * Uma falha ocorreu durante o processo de failover real. Neste caso, o dispositivo-alvo está marcado inutilizável. Deve fornecer e configurar outra matriz virtual alvo e usá-lo para falhar.
  * A falha foi completa da seguinte a qual o dispositivo de origem foi eliminado, mas o dispositivo-alvo tem problemas e não é possível aceder a quaisquer dados. Os dados ainda são seguros na nuvem e podem ser facilmente recuperados criando outra matriz virtual e, em seguida, usando-os como um dispositivo-alvo para o DR.

### <a name="deactivate"></a>Desativar
Quando desativa um StorSimple Virtual Array, corta a ligação entre o dispositivo e o serviço StorSimple Manager correspondente. A desativação é uma operação **permanente** e não pode ser desfeita. Um dispositivo desativado não pode ser registado novamente no serviço StorSimple Manager. Para mais informações, vá [desativar e eliminar o seu StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Tenha em mente as seguintes boas práticas ao desativar a sua matriz virtual:

* Tire uma imagem em nuvem de todos os dados antes de desativar um dispositivo virtual. Quando desactivauma matriz virtual, todos os dados do dispositivo local são perdidos. Tirar uma imagem de nuvem permitirá recuperar dados numa fase posterior.
* Antes de desativar um StorSimple Virtual Array, certifique-se de parar ou eliminar clientes e anfitriões que dependem desse dispositivo.
* Elimine um dispositivo desativado se já não estiver a utilizar para que não acumule cargas.

### <a name="monitoring"></a>Monitorização
Para garantir que o seu StorSimple Virtual Array está em estado de saúde contínua, precisa monitorizar a matriz e garantir que recebe informações do sistema, incluindo alertas. Para monitorizar a saúde geral da matriz virtual, implemente as seguintes boas práticas:

* Configure a monitorização para monitorizar a utilização do disco do seu disco de dados de matriz virtual, bem como o disco OS. Se executar hyper-V, pode utilizar uma combinação de System Center Virtual Machine Manager (SCVMM) e System Center Operations Manager para monitorizar os seus anfitriões de virtualização.
* Configure notificações de e-mail na sua matriz virtual para enviar alertas a determinados níveis de utilização.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicações de pesquisa de índices e varredura de vírus
Um StorSimple Virtual Array pode automaticamente tierizar dados do nível local para a nuvem Microsoft Azure. Quando uma aplicação como uma pesquisa de índice ou uma varredura de vírus é usada para digitalizar os dados armazenados no StorSimple, você precisa ter cuidado para que os dados da nuvem não sejam acedidos e puxados para o nível local.

Recomendamos que implemente as seguintes boas práticas ao configurar a pesquisa de índices ou a varredura de vírus na sua matriz virtual:

* Desative quaisquer operações de digitalização completa configuradas automaticamente.
* Para volumes hierárquicos, configure a aplicação de pesquisa de índices ou de varredura de vírus para realizar uma varredura incremental. Isto só analisaria os novos dados que provavelmente residem no nível local. Os dados que são nividados para a nuvem não são acedidos durante uma operação incremental.
* Certifique-se de que os filtros e definições de pesquisa corretos estão configurados para que apenas os tipos de ficheiros pretendidos sejam digitalizados. Por exemplo, os ficheiros de imagem (JPEG, GIF e TIFF) e os desenhos de engenharia não devem ser digitalizados durante a reconstrução incremental ou completa do índice.

Se utilizar o processo de indexação do Windows, siga estas orientações:

* Não utilize o Indexer do Windows para volumes hierárquicos, uma vez que recorda grandes quantidades de dados (TBs) da nuvem se o índice precisar de ser reconstruído com frequência. A reconstrução do índice recuperaria todos os tipos de ficheiros para indexar o seu conteúdo.
* Utilize o processo de indexação do Windows para volumes localizados localmente, uma vez que isso apenas acederia a dados nos níveis locais para construir o índice (os dados da nuvem não serão acedidos).

### <a name="byte-range-locking"></a>Bloqueio de alcance byte
As aplicações podem bloquear uma gama especificada de bytes dentro dos ficheiros. Se o bloqueio de alcance byte estiver ativado nas aplicações que estão a escrever para o seu StorSimple, então o tiering não funciona na sua matriz virtual. Para que o tiering funcione, todas as áreas dos ficheiros acedidos devem ser desbloqueadas. O bloqueio de alcance byte não é suportado com volumes hierárquicos na sua matriz virtual.

As medidas recomendadas para aliviar o bloqueio do alcance dos bytes incluem:

* Desligue o alcance do byte na sua lógica de aplicação.
* Utilize volumes fixados localmente (em vez de hierárquicos) para os dados associados a esta aplicação. Os volumes fixados localmente não se traduzem na nuvem.
* Ao utilizar volumes fixados localmente com bloqueio de gama byte ativado, o volume pode ficar on-line antes de o restauro estar completo. Nestes casos, deve esperar que o restauro esteja completo.

## <a name="multiple-arrays"></a>Múltiplas matrizes
Várias matrizes virtuais podem ter de ser implementadas para explicar um conjunto crescente de dados de trabalho que podem derramar na nuvem, afetando assim o desempenho do dispositivo. Nestes casos, o melhor é escalar os dispositivos à medida que o conjunto de trabalho cresce. Isto requer que um ou mais dispositivos sejam adicionados no centro de dados no local. Ao adicionar os dispositivos, pode:

* Divida o conjunto atual de dados.
* Implementar novas cargas de trabalho para novos dispositivos.
* Se implementar várias matrizes virtuais, recomendamos que, do ponto de vista de equilíbrio de carga, distribua a matriz através de diferentes hospedeiros hipervisores.
* Várias matrizes virtuais (quando configuradas como um servidor de ficheiros ou um servidor iSCSI) podem ser implementadas num espaço de nome do sistema de ficheiros distribuído. Para passos detalhados, vá à [solução nomespace](https://www.microsoft.com/download/details.aspx?id=45507)do sistema de ficheiros distribuído com guia híbrido de implantação de armazenamento em nuvem . A replicação do sistema de ficheiros distribuídonão é atualmente recomendada para utilização com a matriz virtual. 

## <a name="see-also"></a>Consulte também
Aprenda a administrar o [seu StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) através do serviço StorSimple Manager.

