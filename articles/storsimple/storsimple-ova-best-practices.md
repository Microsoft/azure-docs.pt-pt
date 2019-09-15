---
title: Práticas recomendadas para o StorSimple virtual array | Microsoft Docs
description: Descreve as práticas recomendadas para implantar e gerenciar o StorSimple virtual array.
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
ms.openlocfilehash: a8aed646f03b777722518152354cfe80cea043a0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002807"
---
# <a name="storsimple-virtual-array-best-practices"></a>Práticas recomendadas do StorSimple virtual array

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple matriz virtual é uma solução de armazenamento integrada que gerencia tarefas de armazenamento entre um dispositivo virtual local em execução em um hipervisor e Microsoft Azure armazenamento em nuvem. O StorSimple virtual array é uma alternativa eficiente e econômica para a matriz física da série 8000. A matriz virtual pode ser executada em sua infraestrutura de hipervisor existente, dá suporte aos protocolos iSCSI e SMB e é adequada para cenários de filial/escritório remotos. Para obter mais informações sobre as soluções do StorSimple, acesse [Microsoft Azure StorSimple visão geral](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo aborda as práticas recomendadas implementadas durante a instalação inicial, a implantação e o gerenciamento da matriz virtual StorSimple. Essas práticas recomendadas fornecem diretrizes validadas para a instalação e o gerenciamento de sua matriz virtual. Este artigo destina-se aos administradores de ti que implantam e gerenciam as matrizes virtuais em seus data centers.

Recomendamos uma revisão periódica das práticas recomendadas para ajudar a garantir que o dispositivo ainda esteja em conformidade quando forem feitas alterações no fluxo de instalação ou operação. Se você encontrar problemas ao implementar essas práticas recomendadas em sua matriz virtual, [entre em contato com suporte da Microsoft](storsimple-virtual-array-log-support-ticket.md) para obter assistência.

## <a name="configuration-best-practices"></a>Práticas recomendadas de configuração
Essas práticas recomendadas abrangem as diretrizes que precisam ser seguidas durante a instalação inicial e a implantação das matrizes virtuais. Essas práticas recomendadas incluem aquelas relacionadas ao provisionamento da máquina virtual, configurações de política de grupo, dimensionamento, configuração de rede, configuração de contas de armazenamento e criação de compartilhamentos e volumes para a matriz virtual. 

### <a name="provisioning"></a>Aprovisionamento
O StorSimple virtual array é uma VM (máquina virtual) provisionada no hipervisor (Hyper-V ou VMware) do seu servidor host. Ao provisionar a máquina virtual, verifique se o host é capaz de dedicar recursos suficientes. Para obter mais informações, acesse [requisitos mínimos de recursos](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) para provisionar uma matriz.

Implemente as seguintes práticas recomendadas ao provisionar a matriz virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**Geração 2** VM para uso com o Windows Server 2012 ou posterior e uma imagem *. vhdx* . <br></br> **Geração 1** VM para uso com um Windows Server 2008 ou posterior e uma imagem *. vhd* . |Use a máquina virtual versão 8 ao usar a imagem *. vmdk* . |
| **Tipo de memória** |Configure como **memória estática**. <br></br> Não use a opção **memória dinâmica** . | |
| **Tipo de disco de dados** |Provisionar como **expansão dinâmica**.<br></br> O **tamanho fixo** leva muito tempo. <br></br> Não use a opção de **diferenciação** . |Use a opção **provisionamento dinâmico** . |
| **Modificação do disco de dados** |A expansão ou a redução não são permitidas. Uma tentativa de fazer isso resulta na perda de todos os dados locais no dispositivo. |A expansão ou a redução não são permitidas. Uma tentativa de fazer isso resulta na perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Dimensionamento
Ao dimensionar sua matriz virtual StorSimple, considere os seguintes fatores:

* Reserva local para volumes ou compartilhamentos. Aproximadamente 12% do espaço são reservados na camada local para cada volume ou compartilhamento em camadas provisionado. Aproximadamente 10% do espaço também é reservado para um volume fixado localmente para o sistema de arquivos.
* Sobrecarga de instantâneo. Aproximadamente 15% de espaço na camada local são reservados para instantâneos.
* Necessidade de restaurações. Se você fizer a restauração como uma nova operação, o dimensionamento deverá considerar o espaço necessário para a restauração. A restauração é feita para um compartilhamento ou volume do mesmo tamanho.
* Um buffer deve ser alocado para qualquer crescimento inesperado.

Com base nos fatores anteriores, os requisitos de dimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Os exemplos a seguir ilustram como você pode dimensionar uma matriz virtual com base em seus requisitos.

#### <a name="example-1"></a>Exemplo 1:
Em sua matriz virtual, você deseja ser capaz de

* provisione um volume ou compartilhamento em camadas de 2 TB.
* provisione um volume ou compartilhamento em camadas de 1 TB.
* provisionar um volume ou compartilhamento localmente afixado de 300 GB.

Nos volumes ou compartilhamentos anteriores, vamos calcular os requisitos de espaço na camada local.

Primeiro, para cada volume/compartilhamento em camadas, a reserva local seria igual a 12% do tamanho do volume/compartilhamento. Para o volume/compartilhamento fixado localmente, a reserva local é de 10% do tamanho do volume/compartilhamento fixado localmente (além do tamanho provisionado). Neste exemplo, você precisa de

* Reserva local de 240 GB (para um volume/compartilhamento em camadas de 2 TB)
* Reserva local de 120 GB (para um volume/compartilhamento em camadas de 1 TB)
* 330-GB para volume ou compartilhamento localmente afixados (adicionando 10% da reserva local para o tamanho de 300 GB provisionado)

O espaço total necessário na camada local até agora é: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, precisamos de pelo menos espaço na camada local como a maior reserva única. Esse valor extra é usado caso você precise restaurar de um instantâneo de nuvem. Neste exemplo, a maior reserva local é 330 GB (incluindo a reserva para o sistema de arquivos), portanto, você adicionaria isso ao 690 GB: 690 GB + 330 GB = 1020 GB.
Se executarmos restaurações adicionais subsequentes, sempre podemos liberar o espaço da operação de restauração anterior.

Terceiro, precisamos de 15% do espaço total local até o momento para armazenar instantâneos locais, de forma que apenas 85% deles estejam disponíveis. Neste exemplo, isso seria cerca de 1020 GB = 0,85&ast;dados provisionados disco TB. Portanto, o disco de dados provisionado seria (1020&ast;(1/0,85)) = 1200 GB = 1,20 TB ~ 1,25 TB (arredondamento para o quartil mais próximo)

Levando em volta um crescimento inesperado e novas restaurações, você deve provisionar um disco local de cerca de 1,25 a 1,5 TB.

> [!NOTE]
> Também recomendamos que o disco local seja provisionado de thin. Essa recomendação é porque o espaço de restauração só é necessário quando você deseja restaurar dados com mais de cinco dias. A recuperação em nível de item permite restaurar dados dos últimos cinco dias sem a necessidade de espaço extra para restauração.


#### <a name="example-2"></a>Exemplo 2:
Em sua matriz virtual, você deseja ser capaz de

* provisionar um volume em camadas de 2 TB
* provisionar um volume fixado localmente de 300 GB

Com base em 12% da reserva de espaço local para volumes/compartilhamentos em camadas e 10% para volumes/compartilhamentos localmente afixados, precisamos

* Reserva local de 240 GB (para volume/compartilhamento em camadas de 2 TB)
* 330-GB para volume ou compartilhamento localmente afixados (adicionando 10% da reserva local para o espaço provisionado de 300 GB)

O espaço total necessário na camada local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para a restauração é de 330 GB.

15% de seu disco total é usado para armazenar instantâneos para que apenas 0,85 esteja disponível. Portanto, o tamanho do disco é (&ast;900 (1/0,85)) = 1, 6 TB ~ 1,25 TB (arredondando para o quartil mais próximo)

Levando em qualquer crescimento inesperado, você pode provisionar um disco local de 1,25 a 1,5 TB.

### <a name="group-policy"></a>Política de grupo
Política de Grupo é uma infraestrutura que permite implementar configurações específicas para usuários e computadores. Política de Grupo configurações estão contidas em objetos Política de Grupo (GPOs), que estão vinculados aos seguintes contêineres de Active Directory Domain Services (AD DS): sites, domínios ou UOs (unidades organizacionais). 

Se sua matriz virtual estiver ingressada no domínio, os GPOs poderão ser aplicados a ela. Esses GPOs podem instalar aplicativos como um software antivírus que pode afetar negativamente a operação da matriz virtual StorSimple.

Portanto, recomendamos que você:

* Verifique se sua matriz virtual está em sua própria UO (unidade organizacional) para Active Directory.
* Certifique-se de que nenhum objeto de política de grupo (GPOs) seja aplicado à sua matriz virtual. Você pode bloquear a herança para garantir que a matriz virtual (nó filho) não herde automaticamente nenhum GPO do pai. Para obter mais informações, vá para [Bloquear herança](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Redes
A configuração de rede para sua matriz virtual é feita por meio da interface do usuário da Web local. Uma interface de rede virtual é habilitada por meio do hipervisor no qual a matriz virtual é provisionada. Use a página [configurações de rede](storsimple-virtual-array-deploy3-fs-setup.md) para configurar o endereço IP, a sub-rede e o gateway da interface de rede virtual.  Você também pode configurar o servidor DNS primário e secundário, as configurações de hora e as configurações de proxy opcionais para seu dispositivo. A maior parte da configuração de rede é uma configuração única. Examine os [requisitos de rede do StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implantar a matriz virtual.

Ao implantar sua matriz virtual, recomendamos que você siga estas práticas recomendadas:

* Verifique se a rede na qual a matriz virtual está implantada sempre tem a capacidade de dedicar largura de banda de Internet de 5 Mbps (ou mais).
  
  * A largura de banda necessária da Internet varia dependendo de suas características de carga de trabalho e da taxa de alteração de dados.
  * A alteração de dados que pode ser tratada é diretamente proporcional à sua largura de banda de Internet. Como exemplo ao fazer um backup, uma largura de banda de 5 Mbps pode acomodar uma alteração de dados de cerca de 18 GB em 8 horas. Com quatro vezes mais largura de banda (20 Mbps), você pode lidar com quatro vezes mais alterações de dados (72 GB).
* Verifique se a conectividade com a Internet está sempre disponível. Conexões de Internet esporádicas ou não confiáveis para os dispositivos podem resultar em perda de acesso aos dados na nuvem e podem resultar em uma configuração sem suporte.
* Se você planeja implantar seu dispositivo como um servidor iSCSI:
  
  * Recomendamos que você desabilite a opção **obter endereço IP automaticamente** (DHCP).
  * Configurar endereços IP estáticos. Você deve configurar um servidor DNS primário e um secundário.
  * Se definir várias interfaces de rede em sua matriz virtual, somente a primeira interface de rede (por padrão, essa interface é **Ethernet**) pode alcançar a nuvem. Para controlar o tipo de tráfego, você pode criar várias interfaces de rede virtual em sua matriz virtual (configurada como um servidor iSCSI) e conectar essas interfaces a sub-redes diferentes.
* Para limitar apenas a largura de banda da nuvem (usada pela matriz virtual), configure a limitação no roteador ou no firewall. Se você definir a limitação em seu hipervisor, ele limitará todos os protocolos, incluindo iSCSI e SMB, em vez de apenas a largura de banda da nuvem.
* Verifique se a sincronização de horário para hipervisores está habilitada. Se estiver usando o Hyper-v, selecione sua matriz virtual no Gerenciador do Hyper-v, vá para **configurações &gt; Integration Services**e verifique se a **sincronização de horário** está marcada.

### <a name="storage-accounts"></a>Contas de armazenamento
A matriz virtual StorSimple pode ser associada a uma única conta de armazenamento. Essa conta de armazenamento pode ser uma conta de armazenamento gerada automaticamente, uma conta na mesma assinatura que o serviço ou uma conta de armazenamento relacionada a outra assinatura. Para obter mais informações, consulte como [gerenciar contas de armazenamento para sua matriz virtual](storsimple-virtual-array-manage-storage-accounts.md).

Use as seguintes recomendações para contas de armazenamento associadas à sua matriz virtual.

* Ao vincular várias matrizes virtuais a uma única conta de armazenamento, o fator na capacidade máxima (64 TB) para uma matriz virtual e o tamanho máximo (500 TB) para uma conta de armazenamento. Isso limita o número de matrizes virtuais de tamanho completo que podem ser associadas a essa conta de armazenamento para cerca de 7.
* Ao criar uma nova conta de armazenamento
  
  * Recomendamos que você o crie na região mais próxima do escritório remoto/filial em que a matriz virtual StorSimple é implantada para minimizar as latências.
  * Tenha em mente que não é possível mover uma conta de armazenamento entre regiões diferentes. Além disso, não é possível mover um serviço entre assinaturas.
  * Use uma conta de armazenamento que implemente a redundância entre os data centers. O GRS (armazenamento com redundância geográfica), o ZRS (armazenamento com redundância de zona) e o LRS (armazenamento com redundância local) têm suporte para uso com sua matriz virtual. Para obter mais informações sobre os diferentes tipos de contas de armazenamento, vá para [replicação de armazenamento do Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Compartilhamentos e volumes
Para a matriz virtual StorSimple, você pode provisionar compartilhamentos quando ele estiver configurado como um servidor de arquivos e volumes quando configurados como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho e ao tipo configurado.

#### <a name="volumeshare-size"></a>Tamanho do volume/compartilhamento
Em sua matriz virtual, você pode provisionar compartilhamentos quando ele estiver configurado como um servidor de arquivos e volumes quando configurados como um servidor iSCSI. As práticas recomendadas para a criação de compartilhamentos e volumes estão relacionadas ao tamanho e ao tipo configurado. 

Tenha em mente as práticas recomendadas a seguir ao provisionar compartilhamentos ou volumes em seu dispositivo virtual.

* Os tamanhos de arquivo relativos ao tamanho provisionado de um compartilhamento em camadas podem afetar o desempenho de camadas. Trabalhar com arquivos grandes pode resultar em uma camada lenta. Ao trabalhar com arquivos grandes, recomendamos que o maior arquivo seja menor que 3% do tamanho do compartilhamento.
* Um máximo de 16 volumes/compartilhamentos pode ser criado na matriz virtual. Para os limites de tamanho dos volumes/compartilhamentos fixados localmente e em camadas, sempre consulte os [limites da matriz virtual StorSimple](storsimple-ova-limits.md).
* Ao criar um volume, o fator no consumo de dados esperado, bem como no futuro crescimento. O volume não pode ser expandido posteriormente.
* Depois que o volume tiver sido criado, você não poderá reduzir o tamanho do volume no StorSimple.
* Ao gravar em um volume em camadas no StorSimple, quando os dados do volume atingem um determinado limite (em relação ao espaço local reservado para o volume), a e/s é limitada. Continuar a gravar nesse volume reduz a e/s de forma significativa. Embora você possa gravar em um volume em camadas além de sua capacidade provisionada (não interrompemos ativamente o usuário de escrever além da capacidade provisionada), você vê uma notificação de alerta para o efeito que você acabou de assinar. Depois de ver o alerta, é imperativo que você tome medidas corretivas, como excluir os dados do volume (atualmente, não há suporte para a expansão do volume).
* Para casos de uso de recuperação de desastre, como o número de compartilhamentos/volumes permitidos é 16 e o número máximo de compartilhamentos/volumes que podem ser processados em paralelo também é 16, o número de compartilhamentos/volumes não tem uma influência sobre o RPO e os RTOs.

#### <a name="volumeshare-type"></a>Tipo de volume/compartilhamento
O StorSimple dá suporte a dois tipos de volume/compartilhamento com base no uso: localmente fixado e em camadas. Os volumes/compartilhamentos localmente afixados são provisionados de forma densa, enquanto os volumes/compartilhamentos em camadas são provisionados de forma estreita. Não é possível converter um volume/compartilhamento fixado localmente em camadas ou *vice-versa* após a criação.

Recomendamos que você implemente as seguintes práticas recomendadas ao configurar compartilhamentos/volumes do StorSimple:

* Identifique o tipo de volume com base nas cargas de trabalho que você pretende implantar antes de criar um volume. Use volumes localmente afixados para cargas de trabalho que exigem garantias locais de dados (mesmo durante uma interrupção de nuvem) e que exigem latências de nuvem baixas. Depois de criar um volume em sua matriz virtual, você não pode alterar o tipo de volume de fixado localmente para em camadas ou *vice-versa*. Por exemplo, crie volumes fixados localmente ao implantar cargas de trabalho ou cargas de trabalho SQL que hospedam VMs (máquinas virtuais); Use volumes em camadas para cargas de trabalho de compartilhamento de arquivos.


#### <a name="volume-format"></a>Formato do volume
Depois de criar volumes do StorSimple no servidor iSCSI, você precisará inicializar, montar e formatar os volumes. Esta operação é executada no host conectado ao seu dispositivo StorSimple. As práticas recomendadas a seguir são recomendadas ao montar e formatar volumes no host StorSimple.

* Execute uma formatação rápida em todos os volumes do StorSimple.
* Ao Formatar um volume do StorSimple, use um tamanho de unidade de alocação (AUS) de 64 KB (o padrão é 4 KB). A AUS 64 KB é baseada em testes feitos internamente para cargas de trabalho comuns do StorSimple e outras cargas de trabalho.
* Ao usar a matriz virtual StorSimple configurada como um servidor iSCSI, não use volumes estendidos ou discos dinâmicos, pois esses volumes ou discos não têm suporte do StorSimple.

#### <a name="share-access"></a>Acesso de compartilhamento
Ao criar compartilhamentos em seu servidor de arquivos de matriz virtual, siga estas diretrizes:

* Ao criar um compartilhamento, atribua um grupo de usuários como um administrador de compartilhamento em vez de um único usuário.
* Você pode gerenciar as permissões NTFS depois que o compartilhamento é criado editando os compartilhamentos por meio do Windows Explorer.

#### <a name="volume-access"></a>Acesso ao volume
Ao configurar os volumes iSCSI em sua matriz virtual StorSimple, é importante controlar o acesso sempre que necessário. Para determinar quais servidores host podem acessar volumes, crie e associe ACRs (registros de controle de acesso) a volumes do StorSimple.

Use as seguintes práticas recomendadas ao configurar o ACRs para volumes do StorSimple:

* Sempre associe pelo menos um ACR a um volume.

* Ao atribuir mais de um ACR a um volume, verifique se o volume não está exposto de uma maneira em que possa ser acessado simultaneamente por mais de um host não clusterizado. Se você tiver atribuído vários ACRs a um volume, uma mensagem de aviso será exibida para que você examine sua configuração.

### <a name="data-security-and-encryption"></a>Segurança e encriptação de dados
A matriz virtual StorSimple tem recursos de segurança e criptografia de dados que garantem a confidencialidade e a integridade de seus dados. Ao usar esses recursos, é recomendável que você siga estas práticas recomendadas: 

* Defina uma chave de criptografia de armazenamento em nuvem para gerar a criptografia AES-256 antes que os dados sejam enviados de sua matriz virtual para a nuvem. Essa chave não será necessária se os dados forem criptografados para começar. A chave pode ser gerada e mantida segura usando um sistema de gerenciamento de chaves, como o [Azure Key Vault](../key-vault/key-vault-overview.md).
* Ao configurar a conta de armazenamento por meio do serviço de StorSimple Manager, certifique-se de habilitar o modo SSL para criar um canal seguro para a comunicação de rede entre o dispositivo StorSimple e a nuvem.
* Gere novamente as chaves para suas contas de armazenamento (acessando o serviço de armazenamento do Azure) periodicamente para considerar qualquer alteração no acesso com base na lista de administradores alterada.
* Os dados em sua matriz virtual são compactados e deduplicados antes de serem enviados para o Azure. Não é recomendável usar o serviço de função de eliminação de duplicação de dados no host do Windows Server.

## <a name="operational-best-practices"></a>Práticas recomendadas operacionais
As práticas recomendadas operacionais são diretrizes que devem ser seguidas durante o gerenciamento diário ou a operação da matriz virtual. Essas práticas abrangem tarefas de gerenciamento específicas, como fazer backups, restaurar de um conjunto de backup, executar um failover, desativar e excluir a matriz, monitorar o uso e a integridade do sistema e executar verificações de vírus em sua matriz virtual.

### <a name="backups"></a>Cópias de segurança
Os dados em sua matriz virtual são submetidos a backup na nuvem de duas maneiras, um backup diário automatizado padrão de todo o dispositivo, começando em 22:30 ou por meio de um backup manual sob demanda. Por padrão, o dispositivo cria automaticamente instantâneos de nuvem diários de todos os dados que residem nele. Para obter mais informações, acesse [fazer backup da matriz virtual StorSimple](storsimple-virtual-array-backup.md).

A frequência e a retenção associadas aos backups padrão não podem ser alteradas, mas você pode configurar a hora em que os backups diários são iniciados todos os dias. Ao configurar a hora de início para os backups automatizados, recomendamos que:

* Agende seus backups fora do horário de pico. A hora de início do backup não deve coincidir com várias e/s do host.
* Inicie um backup manual sob demanda ao planejar a execução de um failover de dispositivo ou antes da janela de manutenção para proteger os dados em sua matriz virtual.

### <a name="restore"></a>Restaurar
Você pode restaurar de um conjunto de backup de duas maneiras: restaurar para outro volume ou compartilhar ou executar uma recuperação em nível de item (disponível somente em uma matriz virtual configurada como um servidor de arquivos). A recuperação em nível de item permite que você faça uma recuperação granular de arquivos e pastas de um backup em nuvem de todos os compartilhamentos no dispositivo StorSimple. Para obter mais informações, acesse [restaurar de um backup](storsimple-virtual-array-clone.md).

Ao executar uma restauração, tenha em mente as seguintes diretrizes:

* A matriz virtual StorSimple não oferece suporte à restauração in-loco. No entanto, isso pode ser feito prontamente por um processo de duas etapas: Libere espaço na matriz virtual e, em seguida, restaure para outro volume/compartilhamento.
* Ao restaurar de um volume local, tenha em mente que a restauração será uma operação de execução demorada. Embora o volume possa ficar online rapidamente, os dados continuam a ser alimentados em segundo plano.
* O tipo de volume permanece o mesmo durante o processo de restauração. Um volume em camadas é restaurado para outro volume em camadas e um volume fixado localmente para outro volume fixado localmente.
* Ao tentar restaurar um volume ou um compartilhamento de um conjunto de backup, se o trabalho de restauração falhar, um volume ou compartilhamento de destino ainda poderá ser criado no Portal. É importante que você exclua esse volume de destino não utilizado ou compartilhe no portal para minimizar quaisquer problemas futuros que surjam deste elemento.

### <a name="failover-and-disaster-recovery"></a>Failover e recuperação de desastre
Um failover de dispositivo permite que você migre seus dados de um dispositivo de *origem* no datacenter para outro dispositivo de *destino* localizado na mesma localização geográfica ou em um local diferente. O failover do dispositivo é para todo o dispositivo. Durante o failover, os dados de nuvem para o dispositivo de origem alteram a propriedade para o do dispositivo de destino.

Para a matriz virtual StorSimple, você só pode fazer failover para outra matriz virtual gerenciada pelo mesmo serviço de StorSimple Manager. Um failover para um dispositivo da série 8000 ou uma matriz gerenciada por um serviço de StorSimple Manager diferente (diferente do do dispositivo de origem) não é permitido. Para saber mais sobre as considerações sobre failover, acesse [os pré-requisitos para o failover do dispositivo](storsimple-virtual-array-failover-dr.md).

Ao executar um failover para sua matriz virtual, tenha em mente o seguinte:

* Para um failover planejado, é uma prática recomendada colocar todos os volumes/compartilhamentos offline antes de iniciar o failover. Siga as instruções específicas do sistema operacional para colocar os volumes/compartilhamentos offline no host primeiro e, em seguida, coloque-os offline em seu dispositivo virtual.
* Para uma DR (recuperação de desastre) do servidor de arquivos, recomendamos que você ingresse o dispositivo de destino no mesmo domínio que a origem para que as permissões de compartilhamento sejam resolvidas automaticamente. Somente o failover para um dispositivo de destino no mesmo domínio tem suporte nesta versão.
* Depois que a recuperação de desastre for concluída com êxito, o dispositivo de origem será excluído automaticamente. Embora o dispositivo não esteja mais disponível, a máquina virtual que você provisionou no sistema host ainda está consumindo recursos. Recomendamos que você exclua essa máquina virtual do seu sistema host para impedir que os encargos sejam acumulados.
* Observe que, mesmo que o failover não seja bem-sucedido, **os dados são sempre seguros na nuvem**. Considere os três cenários a seguir em que o failover não foi concluído com êxito:
  
  * Ocorreu uma falha nos estágios iniciais do failover, como quando as verificações prévias de DR estão sendo executadas. Nessa situação, o dispositivo de destino ainda é utilizável. Você pode repetir o failover no mesmo dispositivo de destino.
  * Ocorreu uma falha durante o processo de failover real. Nesse caso, o dispositivo de destino é marcado como inutilizável. Você deve provisionar e configurar outra matriz virtual de destino e usá-la para failover.
  * O failover foi concluído após a exclusão do dispositivo de origem, mas o dispositivo de destino tem problemas e você não pode acessar nenhum dado. Os dados ainda são seguros na nuvem e podem ser recuperados facilmente criando outra matriz virtual e, em seguida, usando-o como um dispositivo de destino para a recuperação de desastre.

### <a name="deactivate"></a>Desativar
Ao desativar uma matriz virtual StorSimple, você separa a conexão entre o dispositivo e o serviço de StorSimple Manager correspondente. A desativação é uma operação **permanente** e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço de StorSimple Manager novamente. Para obter mais informações, vá para [desativar e excluir sua matriz virtual StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Tenha em mente as seguintes práticas recomendadas ao desativar sua matriz virtual:

* Tire um instantâneo de nuvem de todos os dados antes de desativar um dispositivo virtual. Quando você desativa uma matriz virtual, todos os dados do dispositivo local são perdidos. Fazer um instantâneo de nuvem permitirá que você recupere os dados em um estágio posterior.
* Antes de desativar uma matriz virtual StorSimple, pare ou exclua os clientes e hosts que dependem desse dispositivo.
* Exclua um dispositivo desativado se você não estiver mais usando para que ele não acumule os encargos.

### <a name="monitoring"></a>Monitorização
Para garantir que a matriz virtual StorSimple esteja em um estado íntegro contínuo, você precisa monitorar a matriz e garantir que você receba informações do sistema, incluindo alertas. Para monitorar a integridade geral da matriz virtual, implemente as seguintes práticas recomendadas:

* Configure o monitoramento para acompanhar o uso do disco de seu disco de dados da matriz virtual, bem como o disco do sistema operacional. Se estiver executando o Hyper-V, você poderá usar uma combinação de System Center Virtual Machine Manager (SCVMM) e System Center Operations Manager para monitorar seus hosts de virtualização.
* Configure notificações por email em sua matriz virtual para enviar alertas em determinados níveis de uso.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Pesquisa de índice e aplicativos de verificação de vírus
Uma matriz virtual StorSimple pode hierarquizar automaticamente os dados da camada local para a nuvem Microsoft Azure. Quando um aplicativo como uma pesquisa de índice ou uma verificação de vírus é usado para verificar os dados armazenados no StorSimple, você precisa cuidar de que os dados da nuvem não são acessados e retirados para a camada local.

Recomendamos que você implemente as seguintes práticas recomendadas ao configurar a pesquisa de índice ou a verificação de vírus em sua matriz virtual:

* Desabilite todas as operações de verificação completa configuradas automaticamente.
* Para volumes em camadas, configure a pesquisa de índice ou o aplicativo de verificação de vírus para executar uma verificação incremental. Isso examinaria apenas os novos dados que provavelmente residiam na camada local. Os dados que estão em camadas para a nuvem não são acessados durante uma operação incremental.
* Verifique se as configurações e os filtros de pesquisa corretos estão configurados para que apenas os tipos de arquivos pretendidos sejam verificados. Por exemplo, arquivos de imagem (JPEG, GIF e TIFF) e desenhos de engenharia não devem ser verificados durante a recompilação incremental ou completa do índice.

Se estiver usando o processo de indexação do Windows, siga estas diretrizes:

* Não use o indexador do Windows para volumes em camadas, pois ele rechamará grandes quantidades de dados (TBs) da nuvem se o índice precisar ser recriado com frequência. A recriação do índice recuperaria todos os tipos de arquivo para indexar o conteúdo.
* Use o processo de indexação do Windows para volumes fixados localmente, pois isso só acessaria dados nas camadas locais para criar o índice (os dados da nuvem não serão acessados).

### <a name="byte-range-locking"></a>Bloqueio de intervalo de bytes
Os aplicativos podem bloquear um intervalo especificado de bytes dentro dos arquivos. Se o bloqueio de intervalo de bytes estiver habilitado nos aplicativos que estão gravando no StorSimple, a disposição em camadas não funcionará em sua matriz virtual. Para que a camada funcione, todas as áreas dos arquivos acessados devem ser desbloqueadas. Não há suporte para o bloqueio de intervalo de bytes com volumes em camadas em sua matriz virtual.

As medidas recomendadas para aliviar o bloqueio de intervalo de bytes incluem:

* Desative o bloqueio de intervalo de bytes na lógica do aplicativo.
* Use volumes localmente afixados (em vez de em camadas) para os dados associados a esse aplicativo. Os volumes fixados localmente não são nivelados na nuvem.
* Ao usar volumes fixados localmente com o bloqueio de intervalo de bytes habilitado, o volume pode ficar online antes da conclusão da restauração. Nesses casos, você deve aguardar a conclusão da restauração.

## <a name="multiple-arrays"></a>Várias matrizes
Várias matrizes virtuais podem precisar ser implantadas para considerar um conjunto de trabalho crescente de dados que poderiam ser despejados na nuvem, afetando o desempenho do dispositivo. Nesses casos, é melhor dimensionar dispositivos à medida que o conjunto de trabalho cresce. Isso requer que um ou mais dispositivos sejam adicionados no data center local. Ao adicionar os dispositivos, você pode:

* Dividir o conjunto de dados atual.
* Implante novas cargas de trabalho para novos dispositivos.
* Se estiver implantando várias matrizes virtuais, recomendamos que, da perspectiva do balanceamento de carga, distribua a matriz entre diferentes hosts de hipervisor.
* Várias matrizes virtuais (quando configuradas como um servidor de arquivos ou um servidor iSCSI) podem ser implantadas em um namespace Sistema de Arquivos Distribuído. Para obter etapas detalhadas, acesse [sistema de arquivos distribuído solução de namespace com o guia de implantação de armazenamento em nuvem híbrida](https://www.microsoft.com/download/details.aspx?id=45507). Atualmente, a replicação do Sistema de Arquivos Distribuído não é recomendada para uso com a matriz virtual. 

## <a name="see-also"></a>Consulte também
Saiba como [administrar sua matriz virtual StorSimple](storsimple-virtual-array-manager-service-administration.md) por meio do serviço de StorSimple Manager.

