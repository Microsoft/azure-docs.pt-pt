---
title: StorSimple 8000 solução geral da solução Microsoft Docs
description: Descreve o tiering StorSimple, o dispositivo, dispositivo virtual, serviços e gestão de armazenamento, e introduz termos-chave usados no StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: timlt
ms.openlocfilehash: f9c7a2df83e9d630ce2b4635a830fb941180c758
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021844"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série StorSimple 8000: uma solução híbrida de armazenamento em nuvem

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrada que gere tarefas de armazenamento entre dispositivos no local e armazenamento em nuvem Microsoft Azure. A StorSimple é uma solução eficiente, rentável e facilmente gerível da rede de área de armazenamento (SAN) que elimina muitas das questões e despesas associadas ao armazenamento e proteção de dados da empresa. Utiliza o dispositivo de série StorSimple 8000 patenteado, está integrado com serviços cloud e fornece um conjunto de ferramentas de gestão para uma visão totalmente integrada de todo o armazenamento empresarial, incluindo o armazenamento na cloud. (As informações de implementação StorSimple publicadas no website da Microsoft Azure aplicam-se apenas aos dispositivos da série StorSimple 8000. Se estiver a utilizar um dispositivo da série StorSimple 5000/7000, vá à [StorSimple Help](http://onlinehelp.storsimple.com/).)

A StorSimple utiliza [o tiering de armazenamento](#automatic-storage-tiering) para gerir dados armazenados em vários meios de armazenamento. O conjunto de trabalho atual é armazenado no local em unidades de estado sólido (SSDs), dados que são usados com menos frequência são armazenados em discos rígidos (HDDs) e os dados de arquivo são empurrados para a nuvem. Além disso, a StorSimple utiliza a deduplicação e a compressão para reduzir a quantidade de armazenamento que os dados consomem. Para mais informações, aceda à [Deduplicação e compressão.](#deduplication-and-compression) Para definições de outros termos e conceitos-chave utilizados na documentação da série StorSimple 8000, vá à [terminologia StorSimple](#storsimple-terminology) no final deste artigo.

Além da gestão de armazenamento, as funcionalidades de proteção de dados StorSimple permitem-lhe criar backups a pedido e agendados e, em seguida, armazená-los localmente ou na nuvem. As cópias de segurança são tomadas sob a forma de instantâneos incrementais, o que significa que podem ser criadas e restauradas rapidamente. As imagens em nuvem podem ser criticamente importantes em cenários de recuperação de desastres porque substituem sistemas de armazenamento secundário (como cópia de segurança de fita), e permitem restaurar os dados no seu datacenter ou em sites alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Veja o vídeo para uma rápida introdução ao Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Porquê usar o StorSimple?
A tabela seguinte descreve alguns dos principais benefícios que o Microsoft Azure StorSimple fornece.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |Utiliza o protocolo iSCSI para ligar invisivelmente as instalações de armazenamento de dados. Isto garante que os dados armazenados na nuvem, no datacenter ou em servidores remotos parecem ser armazenados num único local. |
| Custos de armazenamento reduzidos |Aloca armazenamento local ou em nuvem suficiente para satisfazer as exigências atuais e prolonga o armazenamento em nuvem apenas quando necessário. Reduz ainda mais os requisitos de armazenamento e as despesas, eliminando versões redundantes dos mesmos dados (deduplicação) e utilizando a compressão. |
| Gestão simplificada do armazenamento |Fornece ferramentas de administração do sistema para configurar e gerir dados armazenados no local, num servidor remoto e na nuvem. Além disso, pode gerir as funções de backup e restaurar a partir de um snap-in da Microsoft Management Console (MMC).|
| Melhoria da recuperação e da conformidade com as catástrofes |Não requer um tempo de recuperação prolongado. Em vez disso, restaura os dados como são necessários. Isto significa que as operações normais podem continuar com o mínimo de perturbação. Além disso, pode configurar políticas para especificar horários de backup e retenção de dados. |
| Mobilidade de dados |Os dados enviados para os serviços de cloud do Microsoft Azure podem ser acedidos a partir de outros sites para fins de recuperação e migração. Além disso, pode utilizar o StorSimple para configurar aparelhos de nuvem StorSimple em máquinas virtuais (VMs) em funcionamento no Microsoft Azure. Os VMs podem então utilizar dispositivos virtuais para aceder a dados armazenados para fins de teste ou recuperação. |
| Continuidade do negócio |Permite que os utilizadores da série StorSimple 5000-7000 migram os seus dados para um dispositivo da série StorSimple 8000. |
| Disponibilidade no Portal do Governo azul |StorSimple está disponível no Portal do Governo de Azure. Para mais informações, consulte [implementar o seu dispositivo StorSimple no portal do Governo.](storsimple-8000-deployment-walkthrough-gov-u2.md) |
| Proteção e disponibilidade de dados |A série StorSimple 8000 suporta o armazenamento redundante da zona (ZRS), além do armazenamento localmente redundante (LRS) e do armazenamento geo-redundante (GRS). Consulte [este artigo sobre as opções de redundância do Azure Storage](../storage/common/storage-redundancy.md) para detalhes do ZRS. |
| Apoio a aplicações críticas |O StorSimple permite identificar volumes apropriados como fixados localmente, o que, por sua vez, garante que os dados exigidos por aplicações críticas não são hierárquicos na nuvem. Os volumes fixados localmente não estão sujeitos a latências em nuvem ou problemas de conectividade. Para obter mais informações sobre volumes fixados localmente, consulte [utilize o serviço StorSimple Device Manager para gerir volumes](storsimple-8000-manage-volumes-u2.md). |
| Baixa latência e alto desempenho |Pode criar aparelhos em nuvem que tirem partido das características de alta performance e baixa latência do armazenamento premium Azure. Para obter mais informações sobre os aparelhos de nuvem premium StorSimple, consulte [implementar e gerir um aparelho de nuvem StorSimple em Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Componentes StorSimple
A solução Microsoft Azure StorSimple inclui os seguintes componentes:

* **Microsoft Azure StorSimple dispositivo** – um conjunto de armazenamento híbrido no local que contém SSDs e HDDs, juntamente com controladores redundantes e capacidades automáticas de failover. Os controladores gerem o tiering de armazenamento, colocando dados atualmente utilizados (ou quentes) no armazenamento local (no dispositivo ou nos servidores no local), enquanto movem dados menos utilizados para a nuvem.
* **StorSimple Cloud Appliance** – também conhecido como StorSimple Virtual Appliance, esta é uma versão de software do dispositivo StorSimple que replica a arquitetura e a maioria das capacidades do dispositivo de armazenamento híbrido físico. O StorSimple Cloud Appliance funciona num único nó numa máquina virtual Azure. Dispositivos virtuais premium, que aproveitam o armazenamento premium Azure, estão disponíveis no Update 2 e posteriormente.
* **Serviço StorSimple Device Manager** – uma extensão do portal Azure que permite gerir um dispositivo StorSimple ou um Aparelho cloud StorSimple a partir de uma única interface web. Pode utilizar o serviço StorSimple Device Manager para criar e gerir serviços, visualizar e gerir dispositivos, ver alertas, gerir volumes e ver e gerir políticas de backup e o catálogo de backup.
* **Windows PowerShell para StorSimple** – uma interface de linha de comando que pode utilizar para gerir o dispositivo StorSimple. O Windows PowerShell para StorSimple possui funcionalidades que lhe permitem registar o seu dispositivo StorSimple, configurar a interface de rede no seu dispositivo, instalar certos tipos de atualizações, resolver problemas com o seu dispositivo acedendo à sessão de suporte e alterar o estado do dispositivo. Pode aceder ao Windows PowerShell para StorSimple ligando-o à consola em série ou utilizando o remoting Do Windows PowerShell.
* **Azure PowerShell StorSimple cmdlets** – uma coleção de cmdlets Windows PowerShell que lhe permitem automatizar tarefas de nível de serviço e migração a partir da linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, consulte a [referência cmdlet](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – um snap-in MMC que utiliza grupos de volume e o Windows Volume Shadow Copy Service para gerar cópias de segurança consistentes com aplicações. Além disso, pode utilizar o StorSimple Snapshot Manager para criar horários de backup e clonar ou restaurar volumes.
* **Adaptador StorSimple para o SharePoint** – uma ferramenta que alarga de forma transparente o armazenamento e proteção de dados do Microsoft Azure StorSimple às explorações do SharePoint Server, ao mesmo tempo que torna o armazenamento StorSimple visível e manejável a partir do portal da Administração Central do SharePoint.

O diagrama abaixo proporciona uma visão de alto nível da arquitetura e componentes microsoft Azure StorSimple.

![Arquitetura StorSimple](./media/storsimple-overview/overview-big-picture.png)

As seguintes secções descrevem cada um destes componentes com maior detalhe e explicam como a solução organiza dados, atribui armazenamento e facilita a gestão do armazenamento e proteção de dados. A última secção fornece definições para alguns dos termos e conceitos importantes relacionados com os componentes StorSimple e sua gestão.

## <a name="storsimple-device"></a>Dispositivo StorSimple
O dispositivo Microsoft Azure StorSimple é um conjunto de armazenamento híbrido no local que fornece armazenamento primário e acesso iSCSI aos dados armazenados no mesmo. Gere a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e confidencialidade de todos os dados armazenados na solução Microsoft Azure StorSimple.

O dispositivo StorSimple inclui SSDs e discos rígidos que conduzem HDDs, bem como suporte para clustering e falha automática. Contém um processador partilhado, armazenamento partilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

* Ligação a um computador anfitrião
* Até seis portas de rede para ligar à rede local (LAN)
* Monitorização de hardware
* Memória de acesso aleatório não volátil (NVRAM), que retém informação mesmo que a energia seja interrompida
* Atualização consciente do cluster para gerir atualizações de software em servidores num cluster de falhade modo que as atualizações tenham um efeito mínimo ou nenhum na disponibilidade do serviço
* Serviço de cluster, que funciona como um cluster back-end, proporcionando alta disponibilidade e minimizando quaisquer efeitos adversos que possam ocorrer se um HDD ou SSD falhar ou for desligado

Apenas um controlador está ativo a qualquer momento. Se o controlador ativo falhar, o segundo controlador torna-se ativo automaticamente.

Para mais informações, aceda aos [componentes e estado de hardware StorSimple](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Pode utilizar o StorSimple para criar um aparelho em nuvem que replica a arquitetura e as capacidades do dispositivo de armazenamento híbrido físico. O StorSimple Cloud Appliance (também conhecido como StorSimple Virtual Appliance) funciona num único nó numa máquina virtual Azure. (Um aparelho em nuvem só pode ser criado numa máquina virtual Azure. Não é possível criar um num dispositivo StorSimple ou num servidor no local.)

O aparelho em nuvem tem as seguintes características:

* Comporta-se como um aparelho físico e pode oferecer uma interface iSCSI a máquinas virtuais na nuvem.
* Pode criar um número ilimitado de aparelhos em nuvem na nuvem e ligá-los e desligá-los conforme necessário.
* Pode ajudar a simular ambientes no local em cenários de recuperação, desenvolvimento e teste de desastres, e pode ajudar na recuperação do nível de item a partir de backups.

O StorSimple Cloud Appliance está disponível em dois modelos: o dispositivo 8010 (anteriormente conhecido como modelo 1100) e o dispositivo 8020. O dispositivo 8010 tem uma capacidade máxima de 30 TB. O dispositivo 8020, que tira partido do armazenamento premium Azure, tem uma capacidade máxima de 64 TB. (Nos níveis locais, o armazenamento premium Azure armazena dados sobre SSDs, enquanto o armazenamento padrão armazena dados em HDDs.) Note que deve ter uma conta de armazenamento premium Azure para usar armazenamento premium.

Para obter mais informações sobre o StorSimple Cloud Appliance, vá ao [Implementar e gere um aparelho de nuvem StorSimple em Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple
O Microsoft Azure StorSimple fornece uma interface de utilizador baseada na Web (o serviço StorSimple Device Manager) que lhe permite gerir centralmente o datacenter e o armazenamento em nuvem. Pode utilizar o serviço StorSimple Device Manager para executar as seguintes tarefas:

* Configurar as definições do sistema para dispositivos StorSimple.
* Configure e gere as definições de segurança para dispositivos StorSimple.
* Configure credenciais e propriedades em nuvem.
* Configure e gere volumes num servidor.
* Configurar grupos de volume.
* Fazer o back up e restaurar os dados.
* Monitorize o desempenho.
* Rever as definições do sistema e identificar possíveis problemas.

Pode utilizar o serviço StorSimple Device Manager para executar todas as tarefas de administração, exceto aquelas que requerem tempo de inativo do sistema, como a configuração inicial e a instalação de atualizações.

Para obter mais informações, [aceda ao serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple
O Windows PowerShell para StorSimple fornece uma interface de linha de comando que pode utilizar para criar e gerir o serviço Microsoft Azure StorSimple e configurar e monitorizar dispositivos StorSimple. Trata-se de uma interface de linha de comando baseada no Windows PowerShell que inclui cmdlets dedicados para gerir o seu dispositivo StorSimple. O Windows PowerShell para StorSimple tem funcionalidades que lhe permitem:

* Registar um dispositivo.
* Configure a interface de rede num dispositivo.
* Instale certos tipos de atualizações.
* Resolva o seu dispositivo acedendo à sessão de suporte.
* Mude o estado do dispositivo.

Pode aceder ao Windows PowerShell para StorSimple a partir de uma consola em série (num computador anfitrião ligado diretamente ao dispositivo) ou remotamente utilizando o remoting do Windows PowerShell. Note que algumas tarefas do Windows PowerShell para tarefas StorSimple, como o registo inicial do dispositivo, só podem ser feitas na consola em série.

Para obter mais informações, aceda ao [Windows PowerShell para administrar o seu dispositivo](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple cmdlets
Os cmdlets Azure PowerShell StorSimple são uma coleção de cmdlets Windows PowerShell que permitem automatizar tarefas de nível de serviço e migração a partir da linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, consulte a [referência cmdlet](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>Snapshot Manager do StorSimple
StorSimple Snapshot Manager é um snap-in da Microsoft Management Console (MMC) que pode utilizar para criar cópias de backup consistentes e pontuais de dados locais e em nuvem. O snap-in é executado num anfitrião baseado no Windows Server. Pode utilizar o StorSimple Snapshot Manager para:

* Configure, recue e elimine volumes.
* Configure grupos de volume para garantir que os dados com armazenamento são consistentes com a aplicação.
* Gerencie as políticas de backup para que os dados sejam cópias de segurança num horário pré-determinado e armazenados num local designado (localmente ou na nuvem).
* Restaurar volumes e ficheiros individuais.

As cópias de segurança são capturadas como instantâneos, que registam apenas as alterações desde que a última fotografia foi tirada e requerem muito menos espaço de armazenamento do que cópias de segurança completas. Pode criar horários de backup ou fazer backups imediatos conforme necessário. Além disso, pode utilizar o StorSimple Snapshot Manager para estabelecer políticas de retenção que controlem quantas imagens serão guardadas. Se posteriormente necessitar de restaurar os dados a partir de uma cópia de segurança, o StorSimple Snapshot Manager permite-lhe selecionar a partir do catálogo de instantâneos locais ou em nuvem. 

Se ocorrer uma catástrofe ou se precisar de restaurar os dados por outra razão, o StorSimple Snapshot Manager restaura-os gradualmente conforme necessário. A restauração de dados não requer que desligue todo o sistema enquanto restaura um ficheiro, substitua o equipamento ou mude as operações para outro site.

Para mais informações, aceda ao [Que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador do StorSimple para o SharePoint
O Microsoft Azure StorSimple inclui o Adaptador StorSimple para o SharePoint, um componente opcional que alarga de forma transparente as funcionalidades de armazenamento e proteção de dados StorSimple às explorações de servidores SharePoint. O adaptador funciona com um fornecedor de armazenamento de blob remoto (RBS) e com a função RBS do SQL Server, permitindo-lhe mover BLOBs para um servidor apoiado pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple armazena os dados BLOB localmente ou na nuvem, com base na utilização.

O Adaptador StorSimple para SharePoint é gerido a partir do portal da Administração Central do SharePoint. Consequentemente, a gestão do SharePoint permanece centralizada, e todo o armazenamento parece estar na quinta SharePoint.

Para mais informações, aceda ao [Adaptador StorSimple para SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento
Para além do dispositivo dedicado StorSimple, dispositivo virtual e outros componentes, o Microsoft Azure StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido aos dados e reduzir o consumo de armazenamento:

* [Tiering automático de armazenamento](#automatic-storage-tiering) 
* [Aprovisionamento dinâmico](#thin-provisioning) 
* [Deduplicação e compressão](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Tiering automático de armazenamento
O Microsoft Azure StorSimple organiza automaticamente dados em níveis lógicos baseados no uso, idade e relação atuais com outros dados. Os dados mais ativos são armazenados localmente, enquanto dados menos ativos e inativos são automaticamente migrados para a nuvem. O diagrama a seguir ilustra esta abordagem de armazenamento.

![Camadas de armazenamento StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para permitir o acesso rápido, a StorSimple armazena dados muito ativos (dados quentes) em SSDs no dispositivo StorSimple. Armazena dados que são utilizados ocasionalmente (dados quentes) em HDDs no dispositivo ou em servidores no datacenter. Move dados inativos, dados de backup e dados retidos para fins de arquivo ou conformidade para a nuvem. 

> [!NOTE]
> Na Atualização 2 ou posterior, pode especificar um volume como fixado localmente, caso em que os dados permanecem no dispositivo local e não estão nivelados para a nuvem. 


StorSimple ajusta e reorganiza as atribuições de dados e armazenamento à medida que os padrões de utilização mudam. Por exemplo, algumas informações podem tornar-se menos ativas com o tempo. À medida que se torna progressivamente menos ativo, é migrado de SSDs para HDDs e, em seguida, para a nuvem. Se esses mesmos dados voltarem a funcionar, é migrado de volta para o dispositivo de armazenamento.

O processo de tiering de armazenamento ocorre da seguinte forma:

1. Um administrador de sistema cria uma conta de armazenamento em nuvem Microsoft Azure.
2. O administrador utiliza a consola em série e o serviço StorSimple Device Manager (em execução no portal Azure) para configurar o dispositivo e o servidor de ficheiros, criando volumes e políticas de proteção de dados. As máquinas no local (como servidores de ficheiros) utilizam a Interface do Sistema de Computadores Pequenos da Internet (iSCSI) para aceder ao dispositivo StorSimple.
3. Inicialmente, a StorSimple armazena dados sobre o nível SSD rápido do dispositivo.
4. À medida que o nível SSD se aproxima da capacidade, o StorSimple desduplica e comprime os blocos de dados mais antigos, e move-os para o nível HDD.
5. À medida que o nível HDD se aproxima da capacidade, o StorSimple encripta os blocos de dados mais antigos e envia-os de forma segura para a conta de armazenamento do Microsoft Azure via HTTPS.
6. O Microsoft Azure cria múltiplas réplicas dos dados no seu datacenter e num datacenter remoto, garantindo que os dados podem ser recuperados em caso de desastre.
7. Quando o servidor de ficheiros solicita dados armazenados na nuvem, o StorSimple devolve-os sem problemas e armazena uma cópia no nível SSD do dispositivo StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Como o StorSimple gere dados em nuvem

O StorSimple desdulica os dados dos clientes em todos os instantâneos e nos dados primários (dados escritos pelos anfitriões). Embora a desduplicação seja ótima para a eficiência do armazenamento, torna complicada a questão do "o que está na nuvem". Os dados primários hierárquicos e os dados instantâneos sobrepõem-se uns aos outros. Um único pedaço de dados na nuvem poderia ser usado como dados primários hierárquicos e também ser referenciado por vários instantâneos. Cada instantâneo em nuvem garante que uma cópia de todos os dados pontuais é bloqueado na nuvem até que esse instantâneo seja eliminado.

Os dados só são eliminados da nuvem quando não há referências a esses dados. Por exemplo, se tirarmos uma imagem em nuvem de todos os dados que estão no dispositivo StorSimple e depois apagarmos alguns dados primários, veremos os _dados primários_ cair imediatamente. Os _dados em nuvem_ que incluem os dados hierárquicos e as cópias de segurança, permanecem os mesmos. Isto porque ainda existe um instantâneo que refere os dados da nuvem. Após a visualização da nuvem ser eliminada (e qualquer outra imagem que tenha referenciado os mesmos dados), o consumo em nuvem diminui. Antes de removermos os dados da nuvem, verificamos se ainda não há instantâneos que refiram esses dados. Este processo chama-se _recolha de lixo_ e é um serviço de fundo em execução no dispositivo. A remoção de dados em nuvem não é imediata, uma vez que o serviço de recolha de lixo verifica outras referências a esses dados antes da eliminação. A velocidade da recolha de lixo depende do número total de instantâneos e do total de dados. Normalmente, os dados da nuvem são limpos em menos de uma semana.


### <a name="thin-provisioning"></a>Aprovisionamento dinâmico
O fornecimento fino é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, a StorSimple utiliza um fornecimento fino para alocar espaço suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento em nuvem facilita esta abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para satisfazer as exigências em mudança.

> [!NOTE]
> Os volumes fixados localmente não são pouco abastetados. O armazenamento atribuído a um volume apenas local é a provisionado na sua totalidade quando o volume é criado.


### <a name="deduplication-and-compression"></a>Deduplicação e compressão
O Microsoft Azure StorSimple utiliza a desduplicação e a compressão de dados para reduzir ainda mais os requisitos de armazenamento.

A deduplica reduz a quantidade global de dados armazenados eliminando a redundância no conjunto de dados armazenados. À medida que a informação muda, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, a StorSimple reduz a quantidade de dados armazenados identificando e removendo informações desnecessárias. 

> [!NOTE]
> Os dados relativos aos volumes fixados localmente não são desduplicados ou comprimidos. No entanto, os backups de volumes fixados localmente são desduplicados e comprimidos.


## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho StorSimple
Um resumo das cargas de trabalho StorSimple suportadas é tabulado abaixo.

| Cenário | Carga de trabalho | Suportado | Restrições | Versão |
| --- | --- | --- | --- | --- |
| Colaboração |Partilha de ficheiros |Sim | |Todas as versões |
| Colaboração |Partilha de ficheiros distribuídos |Sim | |Todas as versões |
| Colaboração |SharePoint |Sim* |Suportado apenas com volumes fixados localmente |Atualização 2 e mais tarde |
| Arquivo |Arquivo simples de ficheiros |Sim | |Todas as versões |
| Virtualização |Máquinas virtuais |Sim* |Suportado apenas com volumes fixados localmente |Atualização 2 e mais tarde |
| Base de Dados |SQL |Sim* |Suportado apenas com volumes fixados localmente |Atualização 2 e mais tarde |
| Videovigilância |Videovigilância |Sim* |Suportado quando o dispositivo StorSimple é dedicado apenas a esta carga de trabalho |Atualização 2 e mais tarde |
| Cópia de segurança |Backup do alvo primário |Sim* |Suportado quando o dispositivo StorSimple é dedicado apenas a esta carga de trabalho |Atualização 3 e mais tarde |
| Cópia de segurança |Backup de alvo secundário |Sim* |Suportado quando o dispositivo StorSimple é dedicado apenas a esta carga de trabalho |Atualização 3 e mais tarde |

*Sim&#42; - Devem ser aplicadas diretrizes e restrições de solução.*

As seguintes cargas de trabalho não são suportadas por dispositivos da série StorSimple 8000. Se forem implantadas no StorSimple, estas cargas de trabalho resultarão numa configuração não suportada.

* Imagem médica
* Troca
* VDI
* Oracle
* SAP
* Macrodados
* Distribuição de conteúdos
* Arranque da SCSI

Segue-se uma lista dos componentes de infraestrutura suportados StorSimple.

| Cenário | Carga de trabalho | Suportado | Restrições | Versão |
| --- | --- | --- | --- | --- |
| Geral |ExpressRoute |Sim | |Todas as versões |
| Geral |DataCore FC |Sim* |Suportado com DataCore SANsymphony |Todas as versões |
| Geral |DFSR |Sim* |Suportado apenas com volumes fixados localmente |Todas as versões |
| Geral |Indexação |Sim* |Para volumes hierárquicos, apenas a indexação de metadados é suportada (sem dados).<br>Para volumes fixados localmente, a indexação completa é suportada. |Todas as versões |
| Geral |Antivírus |Sim* |Para volumes hierárquicos, apenas é suportada a verificação em aberto e próximo.<br> Para volumes fixados localmente, a varredura completa é suportada. |Todas as versões |

*Sim&#42; - Devem ser aplicadas diretrizes e restrições de solução.*

Segue-se uma lista de outros softwares que são utilizados com o StorSimple para construir soluções.

| Tipo de carga de trabalho | Software utilizado com StorSimple | Versões suportadas|Link para guia de solução| 
| --- | --- | --- | --- |
| Alvo de backup |Veeam |Veeam v 9 e mais tarde |[StorSimple como alvo de backup com Veaam](storsimple-configure-backup-target-veeam.md)|
| Alvo de backup |Executivo de Backup veritas |Backup Exec 16 e mais tarde |[StorSimple como alvo de backup com Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Alvo de backup |Veritas NetBackup |NetBackup 7.7.x e mais tarde  |[StorSimple como alvo de backup com NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Partilha global de ficheiros <br></br> Colaboração |Rio Talon  |[StorSimple com Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia StorSimple
Antes de implementar a sua solução Microsoft Azure StorSimple, recomendamos que reveja os seguintes termos e definições.

### <a name="key-terms-and-definitions"></a>Termos e definições-chave
| Termo (sigla ou abreviatura) | Descrição |
| --- | --- |
| registo de controlo de acesso (ACR) |Um registo associado a um volume no seu dispositivo Microsoft Azure StorSimple que determina quais os anfitriões que podem ligar-se ao mesmo. A determinação baseia-se no nome qualificado iSCSI (IQN) dos anfitriões (contidos no ACR) que estão a ligar-se ao seu dispositivo StorSimple. |
| AES-256 |Um algoritmo avançado de encriptação de 256 bits (AES) para encriptar dados à medida que se move de e para a nuvem. |
| tamanho da unidade de atribuição (AUS) |A menor quantidade de espaço em disco que pode ser atribuída para conter um ficheiro nos seus sistemas de ficheiros Windows. Se o tamanho de um ficheiro não for um múltiplo do tamanho do cluster, deve ser utilizado espaço extra para segurar o ficheiro (até ao próximo múltiplo do tamanho do cluster) resultando em espaço perdido e fragmentação do disco rígido. <br>O AUS recomendado para volumes Azure StorSimple é de 64 KB porque funciona bem com os algoritmos de deduplicação. |
| tiering de armazenamento automatizado |Movendo automaticamente dados menos ativos de SSDs para HDDs e, em seguida, para um nível na nuvem, e, em seguida, permitindo a gestão de todo o armazenamento a partir de uma interface central do utilizador. |
| catálogo de backup |Uma coleção de backups, geralmente relacionadas com o tipo de aplicação que foi usado. Esta coleção é apresentada na lâmina do Catálogo de Cópias de Segurança da UI do Serviço de Gestor de Dispositivos StorSimple. |
| arquivo de catálogo de backup |Um ficheiro que contém uma lista de instantâneos disponíveis atualmente armazenados na base de dados de backup do StorSimple Snapshot Manager. |
| política de backup |Uma seleção de volumes, tipo de backup e um calendário que lhe permite criar cópias de segurança num horário predefinido. |
| objetos grandes binários (BLOBs) |Uma recolha de dados binários armazenados como uma única entidade num sistema de gestão de bases de dados. Blobs são tipicamente imagens, áudio ou outros objetos multimédia, embora por vezes o código binário executável seja armazenado como BLOB. |
| CHAP (Challenge Handshake Authentication Protocol) |Um protocolo usado para autenticar o par de uma ligação, com base no peer que partilha uma palavra-passe ou segredo. Chap pode ser unidirecciona ou mútuo. Com chap de ida, o alvo autentica um iniciador. A Mutual CHAP exige que o alvo autua o iniciador e que o iniciador autentique o alvo. |
| clone |Uma cópia duplicada de um volume. |
| Nuvem como um Tier (CaaT) |Armazenamento em nuvem integrado como um nível dentro da arquitetura de armazenamento para que todo o armazenamento pareça fazer parte de uma rede de armazenamento empresarial. |
| prestador de serviços em nuvem (CSP) |Um fornecedor de serviços de computação em nuvem. |
| instantâneo em nuvem |Uma cópia pontual dos dados de volume que é armazenado na nuvem. Um instantâneo em nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferente e fora do local. As imagens em nuvem são particularmente úteis em cenários de recuperação de desastres. |
| chave de encriptação de armazenamento em nuvem |Uma palavra-passe ou uma chave utilizada pelo seu dispositivo StorSimple para aceder aos dados encriptados enviados pelo seu dispositivo para a nuvem. |
| atualização consciente do cluster |Gerir as atualizações de software nos servidores num cluster de falhade forma que as atualizações tenham um efeito mínimo ou nenhum na disponibilidade do serviço. |
| datapath |Uma recolha de unidades funcionais que realizam operações interligadas de processamento de dados. |
| desativar |Uma ação permanente que rompa a ligação entre o dispositivo StorSimple e o serviço de nuvem associado. As imagens em nuvem do dispositivo permanecem após este processo e podem ser clonadas ou usadas para a recuperação de desastres. |
| espelhamento do disco |Replicação de volumes de discos lógicos em discos rígidos separados em tempo real para garantir a disponibilidade contínua. |
| espelhamento dinâmico do disco |Replicação de volumes de discos lógicos em discos dinâmicos. |
| discos dinâmicos |Um formato de volume de disco que utiliza o Gestor de Discos Lógicos (LDM) para armazenar e gerir dados em vários discos físicos. Os discos dinâmicos podem ser ampliados para proporcionar mais espaço livre. |
| Recinto alargado de discos (EBOD) |Um recinto secundário do seu dispositivo Microsoft Azure StorSimple que contém discos de disco rígido extra para armazenamento adicional. |
| provisão de gordura |Um fornecimento convencional de armazenamento em que o espaço de armazenamento é atribuído com base nas necessidades previstas (e geralmente está fora da necessidade atual). Consulte também *o fornecimento fino*. |
| disco rígido (HDD) |Uma unidade que utiliza bandejas rotativas para armazenar dados. |
| armazenamento de nuvem híbrida |Uma arquitetura de armazenamento que usa recursos locais e fora do local, incluindo armazenamento em nuvem. |
| Interface do sistema informático internet (iSCSI) |Um padrão de rede de armazenamento baseado em protocolo de Internet (IP) para a ligação de equipamentos ou instalações de armazenamento de dados. |
| Iniciador iSCSI |Um componente de software que permite a um computador anfitrião executar o Windows ligar-se a uma rede externa de armazenamento baseada no iSCSI. |
| nome qualificado iSCSI (IQN) |Um nome único que identifica um alvo ou iniciador iSCSI. |
| Alvo iSCSI |Um componente de software que fornece subsistemas de disco iSCSI centralizados em redes de área de armazenamento. |
| arquivo ao vivo |Uma abordagem de armazenamento em que os dados de arquivo estão sempre acessíveis (não é armazenado fora do local em fita, por exemplo). O Microsoft Azure StorSimple utiliza arquivamento ao vivo. |
| volume fixado localmente |um volume que reside no dispositivo e nunca é hierárquico para a nuvem. |
| instantâneo local |Uma cópia pontual dos dados de volume que é armazenada no dispositivo Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Uma solução poderosa que consiste num aparelho de armazenamento de datacenter e software que permite que as organizações de TI aproveitem o armazenamento em nuvem como se fosse armazenamento de datacenter. A StorSimple simplifica a proteção de dados e a gestão de dados, reduzindo ao mesmo tempo os custos. A solução consolida o armazenamento primário, o arquivo, o backup e a recuperação de desastres (DR) através da integração perfeita com a nuvem. Ao combinar a gestão de dados de armazenamento e nuvem da SAN numa plataforma de classe empresarial, os dispositivos StorSimple permitem velocidade, simplicidade e fiabilidade para todas as necessidades relacionadas com o armazenamento. |
| Módulo de alimentação e arrefecimento (PCM) |Componentes de hardware do seu dispositivo StorSimple constituídos pelas fontes de alimentação e pelo ventilador de arrefecimento, daí o nome Power and Cooling module. O recinto primário do dispositivo tem dois PCMs de 764W, enquanto o recinto EBOD tem dois PCMs de 580W. |
| recinto primário |Caixa principal do seu dispositivo StorSimple que contém os controladores da plataforma de aplicação. |
| objetivo do tempo de recuperação (RTO) |O tempo máximo que deve ser gasto antes de um processo ou sistema de negócio é totalmente restaurado após um desastre. |
| SCSI em série anexado (SAS) |Um tipo de disco rígido (HDD). |
| chave de encriptação de dados de serviço |Uma chave disponibilizada a qualquer novo dispositivo StorSimple que se registe com o serviço StorSimple Device Manager. Os dados de configuração transferidos entre o serviço StorSimple Device Manager e o dispositivo são encriptados utilizando uma chave pública e podem ser desencriptados apenas no dispositivo utilizando uma chave privada. A chave de encriptação de dados de serviço permite que o serviço obtenha esta chave privada para desencriptação. |
| chave de registo de serviço |Uma chave que ajuda a registar o dispositivo StorSimple com o serviço StorSimple Device Manager para que apareça no portal Azure para mais ações de gestão. |
| Interface de sistema de computador pequeno (SCSI) |Um conjunto de normas para ligar fisicamente computadores e passar dados entre eles. |
| unidade de estado sólido (SSD) |Um disco que não contém peças móveis; por exemplo, uma pen drive. |
| conta de armazenamento |Um conjunto de credenciais de acesso ligadas à sua conta de armazenamento para um determinado fornecedor de serviços na nuvem. |
| Adaptador do StorSimple para o SharePoint |Um componente Microsoft Azure StorSimple que alarga de forma transparente o armazenamento e proteção de dados StorSimple às explorações de servidores SharePoint. |
| Serviço de Gestor de Dispositivos do StorSimple |Uma extensão do portal Azure que lhe permite gerir o seu Azure StorSimple no local e dispositivos virtuais. |
| Snapshot Manager do StorSimple |Um snap-in da Microsoft Management Console (MMC) para gerir operações de backup e restauro no Microsoft Azure StorSimple. |
| tomar backup |Uma funcionalidade que permite ao utilizador fazer uma cópia de segurança interativa de um volume. É uma forma alternativa de obter uma cópia de segurança manual de um volume em vez de fazer uma cópia de segurança automatizada através de uma política definida. |
| provisões finas |Um método para otimizar a eficiência com que o espaço de armazenamento disponível é usado em sistemas de armazenamento. No fornecimento fino, o armazenamento é atribuído entre vários utilizadores com base no espaço mínimo exigido por cada utilizador a qualquer momento. Consulte também *o fornecimento de gordura.* |
| nivelamento |Organização de dados em agrupamentos lógicos com base no uso atual, idade e relação com outros dados. O StorSimple organiza automaticamente dados em níveis. |
| volume |Áreas de armazenamento lógica apresentadas sob a forma de unidades. Os volumes StorSimple correspondem aos volumes montados pelo hospedeiro, incluindo os descobertos através da utilização do iSCSI e de um dispositivo StorSimple. |
| recipiente de volume |Um agrupamento de volumes e as definições que se aplicam a eles. Todos os volumes do seu dispositivo StorSimple estão agrupados em recipientes de volume. As configurações do contentor de volume incluem contas de armazenamento, definições de encriptação para dados enviados para cloud com chaves de encriptação associadas e largura de banda consumida para operações que envolvam a nuvem. |
| grupo de volume |No StorSimple Snapshot Manager, um grupo de volume é uma coleção de volumes configurados para facilitar o processamento de backup. |
| Serviço de cópia de sombra de volume (VSS) |Um serviço de sistema operativo Windows Server que facilita a consistência da aplicação comunicando com aplicações conscientes do VSS para coordenar a criação de instantâneos incrementais. A VSS garante que as aplicações são temporariamente inativas quando as imagens são tiradas. |
| Windows PowerShell para StorSimple |Uma interface de linha de comando baseada no Windows PowerShell utilizada para operar e gerir o seu dispositivo StorSimple. Apesar de manter algumas das capacidades básicas do Windows PowerShell, esta interface possui cmdlets dedicados adicionais que se destinam à gestão de um dispositivo StorSimple. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a segurança StorSimple](storsimple-8000-security.md).