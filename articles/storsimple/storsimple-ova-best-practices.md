---
title: Boas práticas para storSimple Virtual Array | Microsoft Docs
description: Descreve as melhores práticas para implantar e gerir o Array Virtual StorSimple.
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
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94964988"
---
# <a name="storsimple-virtual-array-best-practices"></a>Práticas recomendadas da Matriz Virtual do StorSimple

## <a name="overview"></a>Descrição Geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O Microsoft Azure StorSimple Virtual Array é uma solução de armazenamento integrada que gere tarefas de armazenamento entre um dispositivo virtual no local que funciona num hipervisor e o armazenamento em nuvem microsoft Azure. StorSimple Virtual Array é uma alternativa eficiente e rentável à matriz física da série 8000. A matriz virtual pode funcionar na sua infraestrutura de hipervisor existente, suporta tanto os protocolos iSCSI como os protocolos SMB, e é bem adaptado para cenários remotos de escritório/filial. Para obter mais informações sobre as soluções StorSimple, aceda à [visão geral do Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo abrange as melhores práticas implementadas durante a configuração, implementação e gestão iniciais do StorSimple Virtual Array. Estas boas práticas fornecem diretrizes validadas para a configuração e gestão do seu conjunto virtual. Este artigo destina-se aos administradores de TI que implementam e gerem as matrizes virtuais nos seus centros de dados.

Recomendamos uma revisão periódica das melhores práticas para ajudar a garantir que o seu dispositivo ainda está em conformidade quando são feitas alterações ao fluxo de configuração ou funcionamento. Caso encontre algum problema ao implementar estas boas práticas no seu conjunto virtual, [contacte o Microsoft Support](storsimple-virtual-array-log-support-ticket.md) para obter assistência.

## <a name="configuration-best-practices"></a>Configuração das melhores práticas
Estas boas práticas abrangem as diretrizes que devem ser seguidas durante a configuração inicial e a implantação das matrizes virtuais. Estas boas práticas incluem as relacionadas com o fornecimento da máquina virtual, as definições de políticas de grupo, o dimensionamento, a criação da rede, a configuração das contas de armazenamento e a criação de ações e volumes para a matriz virtual. 

### <a name="provisioning"></a>Aprovisionamento
StorSimple Virtual Array é uma máquina virtual (VM) ateada no hipervisor (Hyper-V ou VMware) do seu servidor anfitrião. Ao a provisionar a máquina virtual, certifique-se de que o seu anfitrião é capaz de dedicar recursos suficientes. Para mais informações, aceda aos [requisitos mínimos de recursos](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) para obter um conjunto.

Implementar as seguintes boas práticas ao a provisionar o conjunto virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**Geração 2** VM para utilização com o Windows Server 2012 ou mais tarde e uma imagem *.vhdx.* <br></br> **Geração 1** VM para utilização com um Windows Server 2008 ou mais tarde e uma imagem *.vhd.* |Utilize a versão da máquina virtual 8 quando utilizar a imagem *.vmdk.* |
| **Tipo de memória** |Configurar como **memória estática.** <br></br> Não utilize a opção **de memória dinâmica.** | |
| **Tipo de disco de dados** |Provisão como **expansão dinâmica.**<br></br> **O tamanho fixo** leva muito tempo. <br></br> Não utilize a opção **de divergência.** |Utilize a opção **de provisão fina.** |
| **Modificação do disco de dados** |Não é permitida a expansão ou redução. Uma tentativa de o fazer resulta na perda de todos os dados locais sobre o dispositivo. |Não é permitida a expansão ou redução. Uma tentativa de o fazer resulta na perda de todos os dados locais sobre o dispositivo. |

### <a name="sizing"></a>Dimensionamento
Ao dimensionar o seu StorSimple Virtual Array, considere os seguintes fatores:

* Reserva local para volumes ou ações. Aproximadamente 12% do espaço é reservado no nível local para cada volume ou parte hierárquico a provisionado. Cerca de 10% do espaço também é reservado para um volume fixado localmente para o sistema de ficheiros.
* Foto snapshot sobrecarga. Cerca de 15% de espaço no nível local é reservado para instantâneos.
* Necessidade de restauros. Se restabelecer como uma nova operação, o dimensionamento deve explicar o espaço necessário para a restauração. A restauração é feita a uma parte ou volume do mesmo tamanho.
* Deve ser atribuído um tampão para qualquer crescimento inesperado.

Com base nos fatores anteriores, os requisitos de dimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Os exemplos a seguir ilustram como pode dimensioná-lo com base nas suas necessidades.

#### <a name="example-1"></a>Exemplo 1:
Na sua matriz virtual, quer ser capaz de

* prever um volume ou uma ação hierárquico de 2 TB.
* prever um volume ou uma quota de 1-TB.
* provisão de 300 GB de volume ou parte fixados localmente.

Para os volumes ou ações anteriores, vamos calcular os requisitos de espaço no nível local.

Em primeiro lugar, para cada volume/ação tiered, a reserva local seria igual a 12% do volume/share. Para o volume/parte fixado localmente, a reserva local é de 10 % do volume/parte fixado localmente (para além da dimensão prevista). Neste exemplo, você precisa

* Reserva local de 240 GB (para um volume/partilha tiered de 2 TB)
* Reserva local de 120-GB (para um volume/partilha tiered de 1-TB)
* 330 GB para volume ou quota fixados localmente (adicionando 10 % da reserva local ao tamanho previsto de 300 GB)

O espaço total necessário no nível local até agora é: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, precisamos de pelo menos tanto espaço no nível local como a maior reserva única. Esta quantidade extra é usada no caso de precisar de ser restaurado a partir de uma imagem em nuvem. Neste exemplo, a maior reserva local é de 330 GB (incluindo reserva para sistema de ficheiros), pelo que adicionaria isso aos 690 GB: 690 GB + 330 GB = 1020 GB.
Se realizarmos restauros adicionais subsequentes, podemos sempre libertar o espaço da operação de restauro anterior.

Em terceiro lugar, precisamos de 15 % do seu espaço local total até agora para armazenar instantâneos locais, de modo que apenas 85% do seu espaço está disponível. Neste exemplo, seria cerca de 1020 GB = 0,85 &ast; TB de disco de dados forerado. Assim, o disco de dados provisionado seria (1020 &ast; (1/0,85)= 1200 GB = 1,20 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Tendo em conta o crescimento inesperado e as novas restaurações, deverá providenciar um disco local de cerca de 1,25 - 1,5 TB.

> [!NOTE]
> Recomendamos também que o disco local seja pouco abastetado. Esta recomendação é porque o espaço de restauração só é necessário quando se pretende restaurar dados com mais de cinco dias. A recuperação do nível do item permite-lhe restaurar os dados dos últimos cinco dias sem necessitar de espaço extra para restauro.


#### <a name="example-2"></a>Exemplo 2:
Na sua matriz virtual, quer ser capaz de

* provisão de um volume tiered de 2-TB
* provisão de um volume 300-GB fixado localmente

Com base em 12 % da reserva espacial local para volumes/ações tiered e 10 % para volumes/ações fixados localmente, precisamos

* Reserva local de 240 GB (para 2 volume/partilha tiered TB)
* 330-GB para volume ou quota fixado localmente (adicionando 10% da reserva local ao espaço provisionado de 300 GB)

O espaço total necessário no nível local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para a restauração é de 330 GB.

15 % do seu disco total é usado para armazenar instantâneos de modo a que apenas 0,85 esteja disponível. Assim, o tamanho do disco é (900 &ast; (1/0,85)) = 1,06 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Tendo em conta qualquer crescimento inesperado, pode providenciar um disco local de 1,25 - 1,5 TB.

### <a name="group-policy"></a>Política de grupo
A Política de Grupo é uma infraestrutura que permite implementar configurações específicas para utilizadores e computadores. As definições de Política de Grupo estão contidas em objetos de Política de Grupo (GPOs), que estão ligados aos seguintes contentores ative directory domain services (AD DS): sites, domínios ou unidades organizacionais (OUs). 

Se a sua matriz virtual estiver unida ao domínio, os GPOs podem ser aplicados a ele. Estes GPOs podem instalar aplicações como um software antivírus que pode afetar negativamente o funcionamento do StorSimple Virtual Array.

Por isso, recomendamos que:

* Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory.
* Certifique-se de que nenhum objeto de política de grupo (GPOs) é aplicado na sua matriz virtual. Pode bloquear a herança para garantir que a matriz virtual (nó de criança) não herda automaticamente quaisquer GPOs do progenitor. Para mais informações, vá para bloquear a [herança.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11))

### <a name="networking"></a>Rede
A configuração de rede para a sua matriz virtual é feita através da UI web local. Uma interface de rede virtual é ativada através do hipervisor no qual a matriz virtual é a provisionada. Utilize a página [Definições de Rede](storsimple-virtual-array-deploy3-fs-setup.md) para configurar o endereço IP, sub-rede e gateway de interface de rede virtual.  Também pode configurar o servidor dSNS primário e secundário, as definições de tempo e as definições de procuração opcionais para o seu dispositivo. A maior parte da configuração da rede é uma configuração única. Reveja os [requisitos de rede StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar o array virtual.

Ao implementar a sua matriz virtual, recomendamos que siga estas boas práticas:

* Certifique-se de que a rede em que a matriz virtual é implantada tem sempre a capacidade de dedicar largura de banda de 5 Mbps (ou mais).
  
  * A necessidade de largura de banda da Internet varia consoante as suas características de carga de trabalho e a taxa de alteração de dados.
  * A alteração de dados que pode ser tratada é diretamente proporcional à largura de banda da Internet. Como exemplo ao fazer uma cópia de segurança, uma largura de banda de 5 Mbps pode acomodar uma mudança de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), pode lidar com quatro vezes mais alterações de dados (72 GB).
* Certifique-se de que a conectividade com a Internet está sempre disponível. As ligações esporádicas ou não confiáveis da Internet aos dispositivos podem resultar numa perda de acesso aos dados na nuvem e podem resultar numa configuração não apoiada.
* Se planeia implantar o seu dispositivo como servidor iSCSI:
  
  * Recomendamos que desative automaticamente a opção **do endereço Get IP** (DHCP).
  * Configure endereços IP estáticos. Tem de configurar um servidor primário e secundário de DNS.
  * Se definir várias interfaces de rede no seu array virtual, apenas a primeira interface de rede (por padrão, esta interface é **Ethernet)** pode chegar à nuvem. Para controlar o tipo de tráfego, pode criar múltiplas interfaces de rede virtuais na sua matriz virtual (configurada como servidor iSCSI) e ligar essas interfaces a diferentes sub-redes.
* Para acelerar apenas a largura de banda da nuvem (utilizada pela matriz virtual), configurar o estrangulamento no router ou na firewall. Se definir o estrangulamento no seu hipervisor, irá acelerar todos os protocolos, incluindo iSCSI e SMB, em vez de apenas a largura de banda em nuvem.
* Certifique-se de que a sincronização temporal dos hipervisores está ativada. Se utilizar o Hyper-V, selecione a sua matriz virtual no Hyper-V Manager, vá aos Serviços de **&gt; Integração de Definições** e certifique-se de que a **sincronização** do tempo é verificada.

### <a name="storage-accounts"></a>Contas de armazenamento
O StorSimple Virtual Array pode ser associado a uma única conta de armazenamento. Esta conta de armazenamento pode ser uma conta de armazenamento gerada automaticamente, uma conta na mesma subscrição que o serviço, ou uma conta de armazenamento relacionada com outra subscrição. Para obter mais informações, consulte como [gerir as contas de armazenamento da sua matriz virtual.](storsimple-virtual-array-manage-storage-accounts.md)

Utilize as seguintes recomendações para contas de armazenamento associadas à sua matriz virtual.

* Ao ligar vários conjuntos virtuais a uma única conta de armazenamento, fator na capacidade máxima (64 TB) para uma matriz virtual e no tamanho máximo (500 TB) para uma conta de armazenamento. Isto limita o número de matrizes virtuais de tamanho completo que podem ser associadas a essa conta de armazenamento a cerca de 7.
* Ao criar uma nova conta de armazenamento
  
  * Recomendamos que o crie na região mais próxima da filial remota onde o seu StorSimple Virtual Array é implantado para minimizar as latências.
  * Tenha em mente que não pode mover uma conta de armazenamento em diferentes regiões. Além disso, não é possível mover um serviço através de subscrições.
  * Utilize uma conta de armazenamento que implemente a redundância entre os datacenters. Geo-Redundant Armazenamento (GRS), Armazenamento Redundante de Zona (ZRS) e Armazenamento LocalMente Redundante (LRS) são todos suportados para uso com a sua matriz virtual. Para obter mais informações sobre os diferentes tipos de contas de armazenamento, aceda à [replicação de armazenamento Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Ações e volumes
Para o seu StorSimple Virtual Array, pode provisturar ações quando esta é configurada como um servidor de ficheiros e volumes quando configurado como um servidor iSCSI. As melhores práticas para a criação de ações e volumes estão relacionadas com o tamanho e o tipo configurados.

#### <a name="volumeshare-size"></a>Tamanho do volume/partilha
Na sua matriz virtual, pode provisidade de ações quando esta é configurada como um servidor de ficheiros e volumes quando configurado como um servidor iSCSI. As melhores práticas para a criação de ações e volumes dizem respeito ao tamanho e ao tipo configurado. 

Tenha em mente as seguintes boas práticas ao aussar ações ou volumes no seu dispositivo virtual.

* Os tamanhos dos ficheiros relativos ao tamanho previsto de uma ação hierárquico podem ter impacto no desempenho do tiering. Trabalhar com ficheiros grandes pode resultar numa saída lenta. Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação.
* Um máximo de 16 volumes/ações pode ser criado na matriz virtual. Para os limites de tamanho dos volumes/ações fixados localmente e hierárquicos, consulte sempre os [limites de Matriz Virtual StorSimple](storsimple-ova-limits.md).
* Ao criar um volume, fator no consumo de dados esperado, bem como no crescimento futuro. O volume ou a quota não podem ser expandidos mais tarde.
* Uma vez criado o volume/partilha, não é possível diminuir o tamanho do volume/ação no StorSimple.
* Ao escrever para um volume hierárquico em StorSimple, quando os dados de volume atingem um determinado limiar (em relação ao espaço local reservado para o volume), o IO é estrangulado. Continuar a escrever para este volume diminui significativamente o IO. Embora possa escrever para um volume hierárquico para além da sua capacidade a provisionada (não impedimos ativamente o utilizador de escrever para além da capacidade prevista), vê uma notificação de alerta para o efeito de que tem sobrestimado. Uma vez visto o alerta, é imperativo que tome medidas corretivas como eliminar os dados de volume (a expansão do volume não está suportada atualmente).
* Para casos de utilização de recuperação de desastres, uma vez que o número de ações/volumes admissíveis é de 16 e o número máximo de ações/volumes que podem ser processados em paralelo é também de 16, o número de ações/volumes não tem influência nos seus RPO e RTOs.

#### <a name="volumeshare-type"></a>Tipo volume/partilha
A StorSimple suporta dois tipos de volume/partilha baseados na utilização: fixado localmente e hierarquizado. Os volumes/ações fixados localmente são fortemente abastetados, enquanto os volumes/ações hierárquicos são pouco provisionados. Não é possível converter um volume/ação fixado localmente para tiered ou *vice-versa* uma vez criado.

Recomendamos que implemente as seguintes boas práticas ao configurar volumes/ações StorSimple:

* Identifique o tipo de volume com base nas cargas de trabalho que pretende implementar antes de criar um volume. Utilize volumes fixados localmente para cargas de trabalho que exijam garantias locais de dados (mesmo durante uma paragem na nuvem) e que exijam baixas latências de nuvens. Uma vez criado um volume na sua matriz virtual, não é possível alterar o tipo de volume de fixado localmente para hierarquizado ou *vice-versa*. Como exemplo, criar volumes fixados localmente ao implementar cargas de trabalho SQL ou cargas de trabalho que hospedam máquinas virtuais (VMs); utilizar volumes hierárquicos para cargas de trabalho de partilha de ficheiros.


#### <a name="volume-format"></a>Formato de volume
Depois de criar volumes StorSimple no seu servidor iSCSI, tem de rubricar, montar e formatar os volumes. Esta operação é efetuada no hospedeiro ligado ao seu dispositivo StorSimple. Recomenda-se que se ressinta os melhores treinos ao montar e formatar volumes no hospedeiro StorSimple.

* Execute um formato rápido em todos os volumes StorSimple.
* Ao formatar um volume StorSimple, utilize um tamanho unitário de atribuição (AUS) de 64 KB (o padrão é de 4 KB). O 64 KB AUS baseia-se em testes feitos internamente para cargas de trabalho storsimple comuns e outras cargas de trabalho.
* Ao utilizar o Array Virtual StorSimple configurado como um servidor iSCSI, não utilize volumes ou discos dinâmicos, uma vez que estes volumes ou discos não são suportados pelo StorSimple.

#### <a name="share-access"></a>Partilhar acesso
Ao criar ações no seu servidor de ficheiros de matriz virtual, siga estas diretrizes:

* Ao criar uma partilha, atribua um grupo de utilizador como administrador de ações em vez de um único utilizador.
* Pode gerir as permissões NTFS após a criação da partilha editando as ações através do Windows Explorer.

#### <a name="volume-access"></a>Acesso ao volume
Ao configurar os volumes iSCSI no seu StorSimple Virtual Array, é importante controlar o acesso sempre que necessário. Para determinar quais os servidores anfitriões que podem aceder a volumes, criar e associar registos de controlo de acesso (ACRs) com volumes StorSimple.

Utilize as seguintes boas práticas ao configurar ACRs para volumes StorSimple:

* Associe sempre pelo menos um ACR a um volume.

* Ao atribuir mais de um ACR a um volume, certifique-se de que o volume não é exposto de forma a que possa ser acedido simultaneamente por mais de um hospedeiro não agrupado. Se atribuiu vários ACRs a um volume, aparece uma mensagem de aviso para que reveja a sua configuração.

### <a name="data-security-and-encryption"></a>Segurança e encriptação de dados
O seu StorSimple Virtual Array dispõe de funcionalidades de segurança e encriptação de dados que garantem a confidencialidade e integridade dos seus dados. Ao utilizar estas funcionalidades, recomenda-se que siga estas boas práticas: 

* Defina uma chave de encriptação de armazenamento em nuvem para gerar encriptação AES-256 antes de os dados serem enviados da sua matriz virtual para a nuvem. Esta chave não é necessária se os seus dados forem encriptados para começar. A chave pode ser gerada e mantida em segurança usando um sistema de gestão chave, como [o cofre de chaves Azure](../key-vault/general/overview.md).
* Ao configurar a conta de armazenamento através do serviço StorSimple Manager, certifique-se de que permite que o modo TLS crie um canal seguro para a comunicação de rede entre o seu dispositivo StorSimple e a nuvem.
* Regenerar as chaves das suas contas de armazenamento (acedendo periodicamente ao serviço de Armazenamento Azure) para ter em conta quaisquer alterações de acesso com base na lista alterada de administradores.
* Os dados da sua matriz virtual são comprimidos e desduplicados antes de serem enviados para Azure. Não recomendamos a utilização do serviço de função de desduplicação de dados no seu anfitrião Windows Server.

## <a name="operational-best-practices"></a>Melhores práticas operacionais
As melhores práticas operacionais são orientações que devem ser seguidas durante a gestão ou funcionamento do conjunto virtual. Estas práticas abrangem tarefas de gestão específicas, tais como fazer backups, restaurar a partir de um conjunto de backup, executar uma falha, desativar e eliminar a matriz, monitorizar o uso do sistema e a saúde, e executar verificações de vírus na sua matriz virtual.

### <a name="backups"></a>Cópias de segurança
Os dados da sua matriz virtual são apoiados até à nuvem de duas formas, uma cópia de segurança diária automatizada padrão de todo o dispositivo a partir das 22:30 ou através de uma cópia de segurança manual a pedido. Por predefinição, o dispositivo cria automaticamente imagens em nuvem diária de todos os dados que residem nele. Para mais informações, procure [fazer o back up do seu StorSimple Virtual Array](storsimple-virtual-array-backup.md).

A frequência e retenção associadas às cópias de segurança predefinidas não podem ser alteradas, mas pode configurar o momento em que as cópias de segurança diárias são iniciadas todos os dias. Ao configurar a hora de início para as cópias de segurança automatizadas, recomendamos que:

* Agende os seus backups para horas fora do pico. A hora de início do backup não deve coincidir com numerosos IO hospedeiros.
* Inicie uma cópia de segurança manual a pedido quando planeia executar um dispositivo que falhe por cima ou antes da janela de manutenção, para proteger os dados da sua matriz virtual.

### <a name="restore"></a>Restauro
Pode restaurar a partir de um conjunto de backup de duas formas: restaurar para outro volume ou partilhar ou executar uma recuperação de nível de item (disponível apenas num conjunto virtual configurado como um servidor de ficheiros). A recuperação do nível de item permite-lhe fazer uma recuperação granular de ficheiros e pastas a partir de uma cópia de segurança em nuvem de todas as ações do dispositivo StorSimple. Para mais informações, vá [para restaurar a partir de uma cópia de segurança.](storsimple-virtual-array-clone.md)

Ao realizar uma restauração, tenha em mente as seguintes diretrizes:

* O seu StorSimple Virtual Array não suporta a restauração no local. Isto pode, no entanto, ser facilmente conseguido através de um processo em duas etapas: fazer espaço na matriz virtual e, em seguida, restaurar para outro volume/partilha.
* Ao restaurar de um volume local, tenha em mente que a restauração será uma operação de longa duração. Embora o volume possa rapidamente ficar on-line, os dados continuam a ser hidratados em segundo plano.
* O tipo de volume permanece o mesmo durante o processo de restauro. Um volume hierárquico é restaurado para outro volume hierárquico e um volume fixado localmente para outro volume fixado localmente.
* Ao tentar restaurar um volume ou uma parte de um conjunto de backup, se o trabalho de restauro falhar, um volume ou partilha-alvo ainda pode ser criado no portal. É importante que elimine este volume de destino não reutilizado ou partilhe no portal para minimizar quaisquer problemas futuros decorrentes deste elemento.

### <a name="failover-and-disaster-recovery"></a>Recuperação de falhas e desastres
Uma falha no dispositivo permite-lhe migrar os seus dados de um dispositivo *de origem* no datacenter para outro *dispositivo-alvo* localizado na mesma localização geográfica ou numa localização geográfica diferente. A falha do dispositivo é para todo o dispositivo. Durante o failover, os dados da nuvem do dispositivo de origem alteram a propriedade do dispositivo alvo.

Para o seu StorSimple Virtual Array, só pode falhar em outro conjunto virtual gerido pelo mesmo serviço StorSimple Manager. Não é permitida uma falha num dispositivo da série 8000 ou numa matriz gerida por um serviço StorSimple Manager diferente (do que o do dispositivo de origem). Para saber mais sobre as considerações de failover, vá a [pré-requisitos para o dispositivo falhar](storsimple-virtual-array-failover-dr.md).

Ao executar uma falha para a sua matriz virtual, tenha em mente o seguinte:

* Para uma falha planeada, é uma melhor prática recomendada para desligar todos os volumes/ações offline antes de iniciar a falha. Siga as instruções específicas do sistema operativo para tirar primeiro os volumes/ações do hospedeiro e, em seguida, desativá-los no seu dispositivo virtual.
* Para uma recuperação de desastres do servidor de ficheiros (DR), recomendamos que se junte ao dispositivo alvo para o mesmo domínio que a fonte para que as permissões de partilha sejam automaticamente resolvidas. Apenas a falha de um dispositivo alvo no mesmo domínio é suportada nesta versão.
* Uma vez concluído o DR com sucesso, o dispositivo de origem é automaticamente eliminado. Apesar de o dispositivo já não se encontra disponível, a máquina virtual que atei no sistema de acolhimento ainda está a consumir recursos. Recomendamos que elimine esta máquina virtual do seu sistema de anfitrião para evitar que quaisquer encargos se acumulem.
* Note que mesmo que o failover não tenha sucesso, **os dados são sempre seguros na nuvem**. Considere os três cenários em que a falha não foi concluída com êxito:
  
  * Ocorreu uma falha nas fases iniciais do incumprimento, como quando estão a ser efetuados os pré-controlos DR. Nesta situação, o seu dispositivo alvo ainda é utilizável. Pode voltar a tentar a falha no mesmo dispositivo alvo.
  * Uma falha ocorreu durante o processo de incumprimento real. Neste caso, o dispositivo alvo está marcado inutilizável. Você deve providenciar e configurar outro conjunto virtual alvo e usá-lo para failover.
  * A falha foi completa na sequência da qual o dispositivo de origem foi eliminado, mas o dispositivo-alvo tem problemas e não é possível aceder a quaisquer dados. Os dados ainda são seguros na nuvem e podem ser facilmente recuperados criando outra matriz virtual e, em seguida, usando-os como um dispositivo alvo para o DR.

### <a name="deactivate"></a>Desativar
Quando desativa um Array Virtual StorSimple, corta-se a ligação entre o dispositivo e o serviço StorSimple Manager correspondente. A desativação é uma operação **permanente** e não pode ser desfeita. Um dispositivo desativado não pode ser registado novamente no serviço StorSimple Manager. Para mais informações, vá [desativar e elimine o seu StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Tenha em mente as seguintes boas práticas ao desativar a sua matriz virtual:

* Tire uma imagem em nuvem de todos os dados antes de desativar um dispositivo virtual. Quando desativa uma matriz virtual, todos os dados do dispositivo local perdem-se. Tirar uma fotografia em nuvem permitir-lhe-á recuperar dados numa fase posterior.
* Antes de desativar um StorSimple Virtual Array, certifique-se de parar ou eliminar clientes e anfitriões que dependem desse dispositivo.
* Elimine um dispositivo desativado se já não estiver a utilizar para que não acumule encargos.

### <a name="monitoring"></a>Monitorização
Para garantir que o seu StorSimple Virtual Array se encontra num estado contínuo e saudável, precisa de monitorizar a matriz e garantir que recebe informações do sistema, incluindo alertas. Para monitorizar a saúde geral da matriz virtual, implemente as seguintes boas práticas:

* Configure a monitorização para rastrear a utilização do disco de dados da sua matriz virtual, bem como o disco DE. Se estiver a executar o Hyper-V, pode utilizar uma combinação de System Center Virtual Machine Manager (SCVMM) e System Center Operations Manager para monitorizar os seus anfitriões de virtualização.
* Configure notificações de e-mail no seu conjunto virtual para enviar alertas em determinados níveis de utilização.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicações de pesquisa de índices e de verificação de vírus
Um Conjunto Virtual StorSimple pode eliminar automaticamente os dados do nível local para a nuvem Microsoft Azure. Quando uma aplicação como uma pesquisa de índice ou uma varredura de vírus é usada para digitalizar os dados armazenados no StorSimple, você precisa ter cuidado para que os dados da nuvem não são acedidos e puxados de volta para o nível local.

Recomendamos que implemente as seguintes boas práticas ao configurar a pesquisa de índice ou a varredura do vírus na sua matriz virtual:

* Desative todas as operações de digitalização completas configuradas automaticamente.
* Para volumes hierárquicos, configuure a aplicação de pesquisa de índice ou de verificação de vírus para realizar uma varredura incremental. Isto iria digitalizar apenas os novos dados que provavelmente residem no nível local. Os dados que são hierárquicos na nuvem não são acedidos durante uma operação incremental.
* Certifique-se de que os filtros e configurações de pesquisa corretos estão configurados de modo a que apenas os tipos de ficheiros pretendidos sejam digitalizados. Por exemplo, os ficheiros de imagem (JPEG, GIF e TIFF) e desenhos de engenharia não devem ser digitalizados durante a reconstrução incremental ou completa do índice.

Se utilizar o processo de indexação do Windows, siga estas diretrizes:

* Não utilize o Índice do Windows para volumes hierárquicos, uma vez que recorda grandes quantidades de dados (TBs) da nuvem se o índice precisar de ser reconstruído com frequência. A reconstrução do índice recuperaria todos os tipos de ficheiros para indexar o seu conteúdo.
* Utilize o processo de indexação do Windows para volumes fixados localmente, uma vez que tal só acederia a dados nos níveis locais para construir o índice (os dados da nuvem não serão acedidos).

### <a name="byte-range-locking"></a>Bloqueio de alcance byte
As aplicações podem bloquear um leque especificado de bytes dentro dos ficheiros. Se o bloqueio de alcance byte estiver ativado nas aplicações que estão a escrever para o seu StorSimple, então o tiering não funciona na sua matriz virtual. Para que o tiering funcione, todas as áreas dos ficheiros acedidos devem ser desbloqueadas. O bloqueio de alcance byte não é suportado com volumes hierárquicos na sua matriz virtual.

As medidas recomendadas para aliviar o bloqueio ao alcance do byte incluem:

* Desligue o bloqueio do alcance do byte na lógica da aplicação.
* Utilize volumes fixados localmente (em vez de hierarquizados) para os dados associados a esta aplicação. Os volumes fixados localmente não se encavem na nuvem.
* Quando utilizar volumes fixados localmente com fecho de alcance byte ativado, o volume pode ficar ligado antes de a restauração estar concluída. Nestes casos, deve esperar que o restauro esteja completo.

## <a name="multiple-arrays"></a>Múltiplas matrizes
Podem ser necessários vários conjuntos virtuais para dar conta de um conjunto crescente de dados que podem derramar para a nuvem, afetando assim o desempenho do dispositivo. Nestes casos, é melhor escalar os dispositivos à medida que o conjunto de trabalho cresce. Isto requer que um ou mais dispositivos sejam adicionados no centro de dados no local. Ao adicionar os dispositivos, pode:

* Divida o conjunto atual de dados.
* Implementar novas cargas de trabalho em novos dispositivos.
* Se implementar várias matrizes virtuais, recomendamos que, do ponto de vista do equilíbrio de carga, distribua a matriz por diferentes anfitriões hipervisores.
* Várias matrizes virtuais (quando configuradas como um servidor de ficheiros ou um servidor iSCSI) podem ser implantadas num espaço de nome do sistema de ficheiros distribuído. Para etapas detalhadas, aceda à [solução de espaço de nome do sistema de ficheiros distribuído com o Guia de Implementação de Armazenamento em Nuvem Híbrida](https://www.microsoft.com/download/details.aspx?id=45507). A replicação distribuída do sistema de ficheiros não é recomendada para utilização com a matriz virtual. 

## <a name="see-also"></a>Ver também
Saiba como [administrar o seu StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) através do serviço StorSimple Manager.