---
title: Visão geral da solução da série StorSimple 8000 [ StorSimple 8000 ] Microsoft Docs
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
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68965123"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série StorSimple 8000: uma solução híbrida de armazenamento em nuvem

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrada que gere tarefas de armazenamento entre dispositivos no local e armazenamento em nuvem microsoft Azure. O StorSimple é uma solução eficiente, rentável e facilmente gerível de área de armazenamento (SAN) que elimina muitas das questões e despesas associadas ao armazenamento e proteção de dados da empresa. Utiliza o dispositivo de série StorSimple 8000 patenteado, está integrado com serviços cloud e fornece um conjunto de ferramentas de gestão para uma visão totalmente integrada de todo o armazenamento empresarial, incluindo o armazenamento na cloud. (As informações de implementação StorSimple publicadas no website do Microsoft Azure aplicam-se apenas aos dispositivos da série StorSimple 8000. Se estiver a utilizar um dispositivo da série StorSimple 5000/7000, vá ao [StorSimple Help](http://onlinehelp.storsimple.com/).)

O StorSimple utiliza [o tiering](#automatic-storage-tiering) de armazenamento para gerir os dados armazenados em vários meios de armazenamento. O conjunto de trabalho atual é armazenado no local em unidades de estado sólido (SSDs), os dados que são utilizados com menos frequência são armazenados em discos rígidos (HDDs), e os dados de arquivo são empurrados para a nuvem. Além disso, a StorSimple utiliza a desduplicação e a compressão para reduzir a quantidade de armazenamento que os dados consomem. Para mais informações, vá à [Deduplicação e compressão.](#deduplication-and-compression) Para definições de outros termos e conceitos-chave utilizados na documentação da série StorSimple 8000, vá à [terminologia StorSimple](#storsimple-terminology) no final deste artigo.

Além da gestão de armazenamento, as funcionalidades de proteção de dados StorSimple permitem criar backups a pedido e regulares e, em seguida, armazená-las localmente ou na nuvem. As cópias de segurança são tomadas sob a forma de instantâneos incrementais, o que significa que podem ser criadas e restauradas rapidamente. Os instantâneos em nuvem podem ser criticamente importantes em cenários de recuperação de desastres porque substituem sistemas de armazenamento secundário (como cópia de segurança da fita), e permitem restaurar os dados no seu datacenter ou para sites alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Veja o vídeo para uma rápida introdução ao Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Porquê usar o StorSimple?
A tabela que se segue descreve alguns dos principais benefícios que o Microsoft Azure StorSimple proporciona.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |Utiliza o protocolo iSCSI para ligar invisivelmente as instalações de armazenamento de dados. Isto garante que os dados armazenados na nuvem, no datacenter ou em servidores remotos parecem ser armazenados num único local. |
| Custos reduzidos de armazenamento |Aloca armazenamento local ou em nuvem suficiente para satisfazer as exigências atuais e alarga o armazenamento em nuvem apenas quando necessário. Reduz ainda mais os requisitos de armazenamento e as despesas, eliminando versões redundantes dos mesmos dados (deduplicação) e utilizando a compressão. |
| Gestão simplificada de armazenamento |Fornece ferramentas de administração do sistema para configurar e gerir dados armazenados no local, num servidor remoto e na nuvem. Além disso, pode gerir as funções de backup e restaurar funções a partir de um snap-in da Consola de Gestão da Microsoft (MMC).|
| Melhor recuperação e conformidade de desastres |Não requer um tempo de recuperação prolongado. Em vez disso, restaura os dados conforme necessário. Isto significa que as operações normais podem continuar com o mínimo de perturbação. Além disso, pode configurar políticas para especificar os horários de backup e a retenção de dados. |
| Mobilidade de dados |Os dados enviados para os serviços de nuvem do Microsoft Azure podem ser acedidos a partir de outros sites para fins de recuperação e migração. Além disso, pode utilizar o StorSimple para configurar os Eletrodomésticos StorSimple Cloud em máquinas virtuais (VMs) em execução no Microsoft Azure. Os VMs podem então utilizar dispositivos virtuais para aceder a dados armazenados para fins de teste ou recuperação. |
| Continuidade do negócio |Permite que os utilizadores da série StorSimple 5000-7000 migram os seus dados para um dispositivo da série StorSimple 8000. |
| Disponibilidade no Portal do Governo do Azure |A StorSimple está disponível no Portal do Governo Azure. Para mais informações, consulte [A implantação do seu dispositivo StorSimple no portal do Governo](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Proteção e disponibilidade de dados |A série StorSimple 8000 suporta o Armazenamento Redundante da Zona (ZRS), além do Armazenamento Redundante Local (LRS) e armazenamento geo-redundante (GRS). Consulte [este artigo sobre as opções de redundância](https://azure.microsoft.com/documentation/articles/storage-redundancy/) do Armazenamento Azure para detalhes do ZRS. |
| Apoio a aplicações críticas |O StorSimple permite identificar volumes apropriados como fixados localmente, o que, por sua vez, garante que os dados exigidos por aplicações críticas não são nividamente para a nuvem. Os volumes fixados localmente não estão sujeitos a tardios em nuvem ou problemas de conectividade. Para obter mais informações sobre volumes fixados localmente, consulte [Use o serviço StorSimple Device Manager para gerir volumes](storsimple-8000-manage-volumes-u2.md). |
| Baixa latência e alto desempenho |Pode criar aparelhos em nuvem que aproveitem o alto desempenho, características de baixa latência do armazenamento premium Azure. Para obter mais informações sobre os eletrodomésticos storSimple premium, consulte [O utilizar e gerencie um StorSimple Cloud Appliance em Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Componentes StorSimple
A solução Microsoft Azure StorSimple inclui os seguintes componentes:

* **Dispositivo Microsoft Azure StorSimple** – um conjunto de armazenamento híbrido no local que contém SSDs e HDDs, juntamente com controladores redundantes e capacidades automáticas de failover. Os controladores gerem o tiering de armazenamento, colocando dados atualmente utilizados (ou quentes) no armazenamento local (no dispositivo ou nos servidores no local), enquanto movem dados menos utilizados para a nuvem.
* **StorSimple Cloud Appliance** – também conhecido como StorSimple Virtual Appliance, esta é uma versão de software do dispositivo StorSimple que replica a arquitetura e a maioria das capacidades do dispositivo de armazenamento híbrido físico. O StorSimple Cloud Appliance funciona num único nó numa máquina virtual Azure. Os dispositivos virtuais premium, que aproveitam o armazenamento premium do Azure, estão disponíveis no Update 2 e posteriormente.
* **Serviço StorSimple Device Manager** – uma extensão do portal Azure que permite gerir um dispositivo StorSimple ou Um Aparelho Cloud StorSimple a partir de uma única interface web. Pode utilizar o serviço StorSimple Device Manager para criar e gerir serviços, visualizar e gerir dispositivos, visualizar alertas, gerir volumes e visualizar e gerir políticas de backup e o catálogo de backup.
* **Windows PowerShell para StorSimple** – uma interface de linha de comando que pode utilizar para gerir o dispositivo StorSimple. O Windows PowerShell para o StorSimple possui funcionalidades que permitem registar o seu dispositivo StorSimple, configurar a interface de rede no seu dispositivo, instalar certos tipos de atualizações, resolver problemas ao aceder à sessão de suporte e alterar o estado do dispositivo. Pode aceder ao Windows PowerShell para StorSimple conectando-se à consola em série ou utilizando o remo do Windows PowerShell.
* **StorSimple StorLets Azure PowerShell** – uma coleção de cmdlets Windows PowerShell que lhe permitem automatizar tarefas de nível de serviço e migração a partir da linha de comando. Para mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá à [referência cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – um snap-in MMC que utiliza grupos de volume e o Serviço de Cópia sonante do Volume windows para gerar backups consistentes com aplicações. Além disso, pode utilizar o StorSimple Snapshot Manager para criar horários de backup e clonar ou restaurar volumes.
* **StorSimple Adapter para SharePoint** – uma ferramenta que alarga de forma transparente a proteção de armazenamento e dados do Microsoft Azure StorSimple às explorações do SharePoint Server, ao mesmo tempo que faz com que o armazenamento StorSimple seja visível e gerível a partir do portal da Administração Central sharePoint.

O diagrama abaixo fornece uma visão de alto nível da arquitetura e componentes Microsoft Azure StorSimple.

![Arquitetura StorSimple](./media/storsimple-overview/overview-big-picture.png)

As seguintes secções descrevem cada um destes componentes com maior detalhe e explicam como a solução organiza dados, aloca armazenamento e facilita a gestão de armazenamento e proteção de dados. A última secção fornece definições para alguns dos termos e conceitos importantes relacionados com os componentes StorSimple e sua gestão.

## <a name="storsimple-device"></a>Dispositivo StorSimple
O dispositivo Microsoft Azure StorSimple é um conjunto de armazenamento híbrido no local que fornece armazenamento primário e acesso iSCSI aos dados armazenados no mesmo. Gere a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e confidencialidade de todos os dados que são armazenados na solução Microsoft Azure StorSimple.

O dispositivo StorSimple inclui SSDs e discos rígidos HDDs, bem como suporte para clustering e falha automática. Contém um processador partilhado, armazenamento partilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

* Ligação a um computador de acolhimento
* Até seis portas de rede para ligar à rede local (LAN)
* Monitorização de hardware
* Memória de acesso aleatório não volátil (NVRAM), que retém informação mesmo que a energia seja interrompida
* Atualização consciente do cluster para gerir atualizações de software em servidores num cluster failover para que as atualizações tenham um efeito mínimo ou nenhum na disponibilidade do serviço
* Serviço de cluster, que funciona como um cluster back-end, proporcionando alta disponibilidade e minimizando quaisquer efeitos adversos que possam ocorrer se um HDD ou SSD falhar ou for desligado

Apenas um controlador está ativo a qualquer momento. Se o controlador ativo falhar, o segundo controlador ativa fica ativa automaticamente.

Para mais informações, vá aos [componentes de hardware StorSimple e ao estado](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Pode utilizar o StorSimple para criar um aparelho em nuvem que replica a arquitetura e as capacidades do dispositivo de armazenamento híbrido físico. O StorSimple Cloud Appliance (também conhecido como StorSimple Virtual Appliance) funciona num único nó numa máquina virtual Azure. (Um aparelho em nuvem só pode ser criado numa máquina virtual Azure. Não é possível criar um num dispositivo StorSimple ou num servidor no local.)

O aparelho em nuvem tem as seguintes características:

* Comporta-se como um aparelho físico e pode oferecer uma interface iSCSI a máquinas virtuais na nuvem.
* Pode criar um número ilimitado de aparelhos em nuvem na nuvem e ligá-los e desligar-se conforme necessário.
* Pode ajudar a simular ambientes no local em cenários de recuperação, desenvolvimento e teste de desastres, e pode ajudar na recuperação ao nível do item a partir de backups.

O StorSimple Cloud Appliance está disponível em dois modelos: o dispositivo 8010 (anteriormente conhecido como modelo 1100) e o dispositivo 8020. O dispositivo 8010 tem uma capacidade máxima de 30 TB. O dispositivo 8020, que aproveita o armazenamento premium Azure, tem uma capacidade máxima de 64 TB. (Nos níveis locais, o Armazenamento Premium Azure armazena dados sobre SSDs, enquanto os dados padrão das lojas de armazenamento em HDDs.) Note que deve ter uma conta de armazenamento premium Azure para utilizar armazenamento premium.

Para mais informações sobre o StorSimple Cloud Appliance, vá [acionar e gerencie um StorSimple Cloud Appliance em Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple
O Microsoft Azure StorSimple fornece uma interface de utilizador baseada na Web (o serviço StorSimple Device Manager) que lhe permite gerir centralmente o datacenter e o armazenamento em nuvem. Pode utilizar o serviço StorSimple Device Manager para executar as seguintes tarefas:

* Configure as definições do sistema para dispositivos StorSimple.
* Configure e gerencie as definições de segurança para dispositivos StorSimple.
* Configure credenciais e propriedades em nuvem.
* Configure e gereos volumes num servidor.
* Configure grupos de volume.
* Volte e restaure os dados.
* Monitor desempenho.
* Reveja as definições do sistema e identifique possíveis problemas.

Pode utilizar o serviço StorSimple Device Manager para executar todas as tarefas de administração, exceto as que requerem tempo de paragem do sistema, como configuração inicial e instalação de atualizações.

Para mais informações, vá ao [serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple
O Windows PowerShell para o StorSimple fornece uma interface de linha de comando que pode utilizar para criar e gerir o serviço Microsoft Azure StorSimple e configurar e monitorizar dispositivos StorSimple. Trata-se de uma interface de linha de comando baseada no Windows PowerShell que inclui cmdlets dedicados para gerir o seu dispositivo StorSimple. O Windows PowerShell para o StorSimple tem funcionalidades que lhe permitem:

* Registe um dispositivo.
* Configure a interface de rede num dispositivo.
* Instale certos tipos de atualizações.
* Problemas de suposição do seu dispositivo acedendo à sessão de suporte.
* Mude o estado do dispositivo.

Pode aceder ao Windows PowerShell para StorSimple a partir de uma consola em série (num computador de acolhimento ligado diretamente ao dispositivo) ou remotamente utilizando o remo do Windows PowerShell. Note que algumas tarefas Windows PowerShell para StorSimple, como o registo inicial do dispositivo, só podem ser feitas na consola em série.

Para mais informações, vá ao [Windows PowerShell para o StorSimple administrar o seu dispositivo](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>StorLets Azure PowerShellSimple
Os cmdlets Azure PowerShell StorSimple são uma coleção de cmdlets Windows PowerShell que lhe permitem automatizar tarefas de nível de serviço e migração a partir da linha de comando. Para mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá à [referência cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>Snapshot Manager do StorSimple
StorSimple Snapshot Manager é um snap-in da Consola de Gestão da Microsoft (MMC) que pode utilizar para criar cópias de backup consistentes e ponto-a-tempo de dados locais e em nuvem. O snap-in é executado num anfitrião baseado no Windows Server. Pode utilizar o StorSimple Snapshot Manager para:

* Configure, recue e elimine volumes.
* Configure grupos de volume para garantir que os dados apoiados são consistentes com aplicações.
* Gerir as políticas de backup para que os dados sejam apoiados num horário pré-determinado e armazenados num local designado (localmente ou na nuvem).
* Restaurar volumes e ficheiros individuais.

As cópias de segurança são capturadas como instantâneos, que registam apenas as alterações desde que a última fotografia foi tirada e requerem muito menos espaço de armazenamento do que cópias de segurança completas. Pode criar horários de backup ou receber cópias de segurança imediatas, se necessário. Além disso, pode utilizar o StorSimple Snapshot Manager para estabelecer políticas de retenção que controlam quantas imagens serão guardadas. Se posteriormente necessitar de restaurar os dados de uma cópia de segurança, o StorSimple Snapshot Manager permite selecionar a partir do catálogo de instantâneos locais ou em nuvem. 

Se ocorrer um desastre ou se precisar restaurar os dados por outra razão, o StorSimple Snapshot Manager restaura-os gradualmente conforme necessário. A restauração de dados não requer que desligue todo o sistema enquanto restaura um ficheiro, substitui o equipamento ou transfere as operações para outro local.

Para mais informações, vá ao [StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador do StorSimple para o SharePoint
O Microsoft Azure StorSimple inclui o Adaptador StorSimple para o SharePoint, um componente opcional que alarga de forma transparente as funcionalidades de armazenamento e proteção de dados StorSimple às explorações de servidores SharePoint. O adaptador funciona com um fornecedor de Armazenamento Remoto Blob (RBS) e com a funcionalidade SQL Server RBS, permitindo-lhe mover bloBs para um servidor apoiado pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple armazena os dados BLOB localmente ou na nuvem, com base na utilização.

O Adaptador StorSimple para SharePoint é gerido a partir do portal da Administração Central sharePoint. Consequentemente, a gestão do SharePoint permanece centralizada, e todo o armazenamento parece estar na quinta do SharePoint.

Para mais informações, vá ao [Adaptador StorSimple para o SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento
Além do dispositivo Dedicado StorSimple, dispositivo virtual e outros componentes, o Microsoft Azure StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido aos dados e reduzir o consumo de armazenamento:

* [Tiering automático de armazenamento](#automatic-storage-tiering) 
* [Aprovisionamento dinâmico](#thin-provisioning) 
* [Desduplicação e compressão](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Tiering automático de armazenamento
O Microsoft Azure StorSimple organiza automaticamente dados em níveis lógicos com base no uso, idade e relação atuais com outros dados. Os dados mais ativos são armazenados localmente, enquanto os dados menos ativos e inativos são automaticamente migrados para a nuvem. O diagrama que se segue ilustra esta abordagem de armazenamento.

![Níveis de armazenamento StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para permitir um acesso rápido, o StorSimple armazena dados muito ativos (dados quentes) em SSDs no dispositivo StorSimple. Armazena dados que são usados ocasionalmente (dados quentes) em HDDs no dispositivo ou em servidores no datacenter. Move dados inativos, dados de backup e dados retidos para fins de arquivo ou conformidade para a nuvem. 

> [!NOTE]
> No Update 2 ou posterior, pode especificar um volume como fixado localmente, caso em que os dados permanecem no dispositivo local e não estão nividados para a nuvem. 


O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento à medida que os padrões de utilização mudam. Por exemplo, algumas informações podem tornar-se menos ativas ao longo do tempo. À medida que se torna progressivamente menos ativo, é migrado de SSDs para HDDs e, em seguida, para a nuvem. Se esses mesmos dados voltarem a estar ativos, este é migrado de volta para o dispositivo de armazenamento.

O processo de nidamento de armazenamento ocorre da seguinte forma:

1. Um administrador do sistema cria uma conta de armazenamento em nuvem Do Microsoft Azure.
2. O administrador utiliza a consola em série e o serviço StorSimple Device Manager (em execução no portal Azure) para configurar o dispositivo e o servidor de ficheiros, criando volumes e políticas de proteção de dados. As máquinas no local (como servidores de ficheiros) utilizam a Interface do Sistema de Computadores Pequenos (iSCSI) para aceder ao dispositivo StorSimple.
3. Inicialmente, a StorSimple armazena dados sobre o nível SSD rápido do dispositivo.
4. À medida que o nível SSD se aproxima da capacidade, o StorSimple desduplica e comprime os blocos de dados mais antigos e transfere-os para o nível HDD.
5. À medida que o nível HDD se aproxima da capacidade, o StorSimple encripta os blocos de dados mais antigos e envia-os de forma segura para a conta de armazenamento do Microsoft Azure via HTTPS.
6. O Microsoft Azure cria múltiplas réplicas dos dados no seu datacenter e num centro de dados remoto, garantindo que os dados podem ser recuperados se ocorrer um desastre.
7. Quando o servidor de ficheiros solicita dados armazenados na nuvem, o StorSimple devolve-os sem problemas e armazena uma cópia no nível SSD do dispositivo StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Como o StorSimple gere os dados da nuvem

O StorSimple desduplica os dados dos clientes em todas as imagens e dados primários (dados escritos pelos anfitriões). Embora a desduplicação seja ótima para a eficiência do armazenamento, torna complicada a questão do "que está na nuvem". Os dados primários hierárquicos e os dados instantâneos sobrepõem-se uns aos outros. Um único pedaço de dados na nuvem poderia ser usado como dados primários hierárquicos e também ser referenciado por vários instantâneos. Cada instantâneo em nuvem garante que uma cópia de todos os dados ponto-em-tempo é bloqueada na nuvem até que esse instantâneo seja eliminado.

Os dados só são eliminados da nuvem quando não há referências a esses dados. Por exemplo, se tirarmos uma imagem em nuvem de todos os dados que estão no dispositivo StorSimple e depois eliminarmos alguns dados primários, veríamos os _dados primários_ cair imediatamente. Os _dados em nuvem_ que incluem os dados hierárquicos e as cópias de segurança permanecem os mesmos. Isto porque ainda há um instantâneo a fazer referência aos dados da nuvem. Após o instantâneo da nuvem ser eliminado (e qualquer outro instantâneo que referenciasse os mesmos dados), o consumo em nuvem diminui. Antes de removermos os dados da nuvem, verificamos que ainda não há instantâneos a referenciar esses dados. Este processo chama-se recolha de _lixo_ e é um serviço de fundo em execução no dispositivo. A remoção de dados em nuvem não é imediata, uma vez que o serviço de recolha de lixo verifica outras referências a esses dados antes da eliminação. A velocidade da recolha de lixo depende do número total de instantâneos e do total de dados. Normalmente, os dados da nuvem são limpos em menos de uma semana.


### <a name="thin-provisioning"></a>Aprovisionamento dinâmico
O fornecimento fino é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, a StorSimple utiliza um fornecimento fino para alocar espaço suficiente para satisfazer os requisitos atuais. A natureza elástica do armazenamento em nuvem facilita esta abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para satisfazer as exigências em mudança.

> [!NOTE]
> Os volumes fixados localmente não são pouco provisionados. O armazenamento atribuído a um volume apenas local é aprovisionado na sua totalidade quando o volume é criado.


### <a name="deduplication-and-compression"></a>Desduplicação e compressão
O Microsoft Azure StorSimple utiliza a desduplicação e a compressão de dados para reduzir ainda mais os requisitos de armazenamento.

A duplicação reduz a quantidade global de dados armazenados eliminando o despedimento no conjunto de dados armazenado. À medida que a informação muda, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados identificando e removendo informações desnecessárias. 

> [!NOTE]
> Os dados relativos a volumes fixados localmente não são desduplicados ou comprimidos. No entanto, as cópias de segurança dos volumes fixados localmente são desduplicadas e comprimidos.


## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho StorSimple
Um resumo das cargas de trabalho storSimple suportadas é tabulado abaixo.

| Cenário | Carga de trabalho | Suportado | Restrições | Versão |
| --- | --- | --- | --- | --- |
| Colaboração |Partilha de ficheiros |Sim | |Todas as versões |
| Colaboração |Partilha de ficheiros distribuídos |Sim | |Todas as versões |
| Colaboração |SharePoint |Sim* |Suportado apenas com volumes fixados localmente |Atualização 2 e mais tarde |
| Arquivo |Arquivamento simples de ficheiros |Sim | |Todas as versões |
| Virtualização |Máquinas virtuais |Sim* |Suportado apenas com volumes fixados localmente |Atualização 2 e mais tarde |
| Base de Dados |SQL |Sim* |Suportado apenas com volumes fixados localmente |Atualização 2 e mais tarde |
| Videovigilância |Videovigilância |Sim* |Suportado quando o dispositivo StorSimple é dedicado apenas a esta carga de trabalho |Atualização 2 e mais tarde |
| Cópia de segurança |Backup do alvo primário |Sim* |Suportado quando o dispositivo StorSimple é dedicado apenas a esta carga de trabalho |Atualização 3 e mais tarde |
| Cópia de segurança |Backup de alvo secundário |Sim* |Suportado quando o dispositivo StorSimple é dedicado apenas a esta carga de trabalho |Atualização 3 e mais tarde |

*Sim&#42; - Devem ser aplicadas diretrizes e restrições de solução.*

As seguintes cargas de trabalho não são suportadas por dispositivos da série StorSimple 8000. Se forem implantados no StorSimple, estas cargas de trabalho resultarão numa configuração não suportada.

* Imagem médica
* Troca
* VDI
* Oracle
* SAP
* Macrodados
* Distribuição de conteúdos
* Arranque do SCSI

Segue-se uma lista dos componentes de infraestrutura suportados storSimple.

| Cenário | Carga de trabalho | Suportado | Restrições | Versão |
| --- | --- | --- | --- | --- |
| Geral |ExpressRoute |Sim | |Todas as versões |
| Geral |DataCore Football Club |Sim* |Suportado com DataCore SANsymphony |Todas as versões |
| Geral |DFSR |Sim* |Suportado apenas com volumes fixados localmente |Todas as versões |
| Geral |Indexação |Sim* |Para volumes hierárquicos, apenas é suportada a indexação de metadados (sem dados).<br>Para volumes fixados localmente, a indexação completa é suportada. |Todas as versões |
| Geral |Antivírus |Sim* |Para volumes hierárquicos, apenas é suportado um digitalização aberto e próximo.<br> Para volumes fixados localmente, a varredura completa é suportada. |Todas as versões |

*Sim&#42; - Devem ser aplicadas diretrizes e restrições de solução.*

Segue-se uma lista de outros softwares que são usados com o StorSimple para construir soluções.

| Tipo de carga de trabalho | Software usado com StorSimple | Versões suportadas|Link para guia de solução| 
| --- | --- | --- | --- |
| Alvo de backup |Veeam |Veeam v 9 e mais tarde |[StorSimple como alvo de reserva com Veaam](storsimple-configure-backup-target-veeam.md)|
| Alvo de backup |Executivo de Backup Veritas |Backup Exec 16 e mais tarde |[StorSimple como alvo de backup com executivo de backup](storsimple-configure-backup-target-using-backup-exec.md)|
| Alvo de backup |Veritas NetBackup |NetBackup 7.7.x e mais tarde  |[StorSimple como alvo de backup com NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Partilha global de ficheiros <br></br> Colaboração |Talon  |[StorSimple com Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia StorSimple
Antes de implementar a sua solução Microsoft Azure StorSimple, recomendamos que reveja os seguintes termos e definições.

### <a name="key-terms-and-definitions"></a>Termos e definições-chave
| Termo (acrónimo ou abreviatura) | Descrição |
| --- | --- |
| registo de controlo de acesso (ACR) |Um registo associado a um volume no seu dispositivo Microsoft Azure StorSimple que determina quais os anfitriões que podem ligar-se ao mesmo. A determinação baseia-se no nome iSCSI Qualificado (IQN) dos anfitriões (contidos no ACR) que estão ligados ao seu dispositivo StorSimple. |
| AES-256 |Um algoritmo de 256 bits Advanced Encryption Standard (AES) para encriptar dados à medida que se move de e para a nuvem. |
| tamanho da unidade de atribuição (AUS) |A menor quantidade de espaço em disco que pode ser atribuído para conter um ficheiro nos seus sistemas de ficheiros Windows. Se um tamanho de ficheiro não for um mesmo múltiplo do tamanho do cluster, deve ser utilizado espaço extra para segurar o ficheiro (até ao próximo múltiplo do tamanho do cluster) resultando em espaço perdido e fragmentação do disco rígido. <br>O AUS recomendado para volumes Azure StorSimple é de 64 KB porque funciona bem com os algoritmos de desduplicação. |
| tiering de armazenamento automatizado |Movendo automaticamente dados menos ativos de SSDs para HDDs e, em seguida, para um nível na nuvem, e, em seguida, permitindo a gestão de todo o armazenamento a partir de uma interface central do utilizador. |
| catálogo de backup |Uma coleção de backups, geralmente relacionadas com o tipo de aplicação que foi usado. Esta coleção é apresentada na lâmina de catálogo de backup do serviço StorSimple Device Manager UI. |
| ficheiro de catálogo de backup |Um ficheiro que contém uma lista de instantâneos disponíveis atualmente armazenados na base de dados de backup do StorSimple Snapshot Manager. |
| política de backup |Uma seleção de volumes, tipo de backup e um calendário que lhe permite criar backups num horário predefinido. |
| objetos grandes binários (BLOBs) |Uma recolha de dados binários armazenados como uma única entidade num sistema de gestão de bases de dados. BloBs são tipicamente imagens, áudio ou outros objetos multimédia, embora às vezes o código executável binário seja armazenado como um BLOB. |
| CHAP (Challenge Handshake Authentication Protocol) |Um protocolo usado para autenticar o par de uma ligação, com base no par que partilha uma senha ou segredo. Chap pode ser de ida ou mútuo. Com chap de ida, o alvo autentica um iniciador. A Mutual CHAP exige que o alvo autenticao o iniciador e que o iniciador autentica o alvo. |
| clone |Uma cópia duplicada de um volume. |
| Nuvem como um Nível (CaaT) |Armazenamento em nuvem integrado como um nível dentro da arquitetura de armazenamento para que todo o armazenamento pareça fazer parte de uma rede de armazenamento empresarial. |
| provedor de serviço sinuoso (CSP) |Um fornecedor de serviços de computação em nuvem. |
| imagem de nuvem |Uma cópia ponto-a-tempo dos dados de volume que é armazenado na nuvem. Um instantâneo em nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferente, fora do local. Os instantâneos em nuvem são particularmente úteis em cenários de recuperação de desastres. |
| chave de encriptação de armazenamento em nuvem |Uma palavra-passe ou uma chave utilizada pelo seu dispositivo StorSimple para aceder aos dados encriptados enviados pelo seu dispositivo para a nuvem. |
| atualização de cluster-aware |Gerir atualizações de software em servidores num cluster failover para que as atualizações tenham um efeito mínimo ou nenhum na disponibilidade do serviço. |
| datapath |Uma recolha de unidades funcionais que realizam operações interligadas de processamento de dados. |
| desativar |Uma ação permanente que rompe a ligação entre o dispositivo StorSimple e o serviço de nuvem associado. As imagens em nuvem do dispositivo permanecem após este processo e podem ser clonadas ou usadas para a recuperação de desastres. |
| espelhamento de disco |Replicação de volumes de disco lógicos em discos rígidos separados em tempo real para garantir a disponibilidade contínua. |
| espelhamento dinâmico do disco |Replicação de volumes lógicos de discos em discos dinâmicos. |
| discos dinâmicos |Um formato de volume de disco que utiliza o Gestor lógico de Discos (LDM) para armazenar e gerir dados através de vários discos físicos. Os discos dinâmicos podem ser ampliados para proporcionar mais espaço livre. |
| Recinto alargado de discos (EBOD) |Um recinto secundário do seu dispositivo Microsoft Azure StorSimple que contém discos de disco rígido extra para armazenamento adicional. |
| fornecimento de gordura |Um fornecimento convencional de armazenamento em que o espaço de armazenamento é atribuído com base nas necessidades previstas (e geralmente está para além da necessidade atual). Consulte também o *fornecimento fino.* |
| disco rígido (HDD) |Uma unidade que utiliza pratos rotativos para armazenar dados. |
| armazenamento híbrido de nuvem |Uma arquitetura de armazenamento que utiliza recursos locais e fora do local, incluindo armazenamento em nuvem. |
| Interface do sistema de computador pequeno da Internet (iSCSI) |Uma norma de rede de armazenamento baseada em Protocolo sinuoso (IP) para ligar equipamentos ou instalações de armazenamento de dados. |
| iniciador iSCSI |Um componente de software que permite que um computador anfitrião que execute o Windows se conectem a uma rede de armazenamento baseada em iSCSI externa. |
| iSCSI Nome Qualificado (IQN) |Um nome único que identifica um alvo ou iniciador iSCSI. |
| alvo iSCSI |Um componente de software que fornece subsistemas de disco iSCSI centralizados em redes de área de armazenamento. |
| arquivo vivo |Uma abordagem de armazenamento em que os dados de arquivo são acessíveis a toda a hora (não é armazenado fora do local em fita, por exemplo). O Microsoft Azure StorSimple utiliza arquivamento sinuoso ao vivo. |
| volume localmente fixado |um volume que reside no dispositivo e nunca é hierárquico para a nuvem. |
| instantâneo local |Uma cópia ponto-a-tempo dos dados de volume que é armazenado no dispositivo Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Uma solução poderosa que consiste num aparelho de armazenamento e software de armazenamento de datacenter que permite às organizações de TI alavancar o armazenamento em nuvem como se fosse armazenamento de datacenter. O StorSimple simplifica a proteção de dados e a gestão de dados, reduzindo os custos. A solução consolida o armazenamento primário, arquivo, backup e recuperação de desastres (DR) através da integração perfeita com a nuvem. Ao combinar armazenamento SAN e gestão de dados em nuvem numa plataforma de classe empresarial, os dispositivos StorSimple permitem velocidade, simplicidade e fiabilidade para todas as necessidades relacionadas com o armazenamento. |
| Módulo de potência e arrefecimento (PCM) |Componentes de hardware do seu dispositivo StorSimple constituídos pelas fontes de alimentação e pelo ventilador de arrefecimento, daí o nome de módulo de potência e arrefecimento. O recinto primário do dispositivo tem dois PCMs 764W, enquanto o recinto EBOD tem dois PCMs de 580W. |
| recinto primário |O recinto principal do seu dispositivo StorSimple que contém os controladores da plataforma de aplicação. |
| objetivo do tempo de recuperação (RTO) |O tempo máximo que deve ser gasto antes de um processo ou sistema de negócios é totalmente restaurado após uma catástrofe. |
| Série anexada SCSI (SAS) |Um tipo de disco rígido (HDD). |
| chave de encriptação de dados de serviço |Uma chave disponibilizada para qualquer novo dispositivo StorSimple que se registe no serviço StorSimple Device Manager. Os dados de configuração transferidos entre o serviço StorSimple Device Manager e o dispositivo são encriptados utilizando uma chave pública e podem ser desencriptados apenas no dispositivo utilizando uma chave privada. A chave de encriptação de dados de serviço permite que o serviço obtenha esta chave privada para desencriptação. |
| chave de registo de serviços |Uma chave que ajuda a registar o dispositivo StorSimple com o serviço StorSimple Device Manager para que apareça no portal Azure para mais ações de gestão. |
| Interface de sistema de computador pequeno (SCSI) |Um conjunto de normas para ligar fisicamente os computadores e passar dados entre eles. |
| unidade de estado sólido (SSD) |Um disco que não contém peças móveis; por exemplo, uma pen. |
| conta de armazenamento |Um conjunto de credenciais de acesso ligadas à sua conta de armazenamento para um determinado fornecedor de serviços na nuvem. |
| Adaptador do StorSimple para o SharePoint |Um componente Microsoft Azure StorSimple que alarga de forma transparente o armazenamento e proteção de dados StorSimple às explorações de servidores SharePoint. |
| Serviço de Gestor de Dispositivos do StorSimple |Uma extensão do portal Azure que lhe permite gerir o seu Azure StorSimple no local e dispositivos virtuais. |
| Snapshot Manager do StorSimple |Um snap-in da Consola de Gestão da Microsoft (MMC) para gerir operações de backup e restauro no Microsoft Azure StorSimple. |
| tomar reforço |Uma funcionalidade que permite ao utilizador obter uma cópia de segurança interativa de um volume. É uma forma alternativa de obter uma cópia de segurança manual de um volume em vez de tomar uma cópia de segurança automatizada através de uma política definida. |
| fornecimento fino |Um método de otimização da eficiência com que o espaço de armazenamento disponível é utilizado em sistemas de armazenamento. No fornecimento fino, o armazenamento é atribuído entre vários utilizadores com base no espaço mínimo exigido por cada utilizador a qualquer momento. Consulte também *o fornecimento de gordura.* |
| tiering |Organizar dados em agrupamentos lógicos com base no uso atual, idade e relação com outros dados. O StorSimple organiza automaticamente dados em níveis. |
| volume |Áreas de armazenamento lógica apresentadas sob a forma de unidades. Os volumes StorSimple correspondem aos volumes montados pelo hospedeiro, incluindo os descobertos através da utilização do iSCSI e de um dispositivo StorSimple. |
| recipiente de volume |Um agrupamento de volumes e as definições que se aplicam a eles. Todos os volumes do seu dispositivo StorSimple estão agrupados em recipientes de volume. As definições do contentor de volume incluem contas de armazenamento, configurações de encriptação para dados enviados para a nuvem com chaves de encriptação associadas e largura de banda consumida para operações que envolvam a nuvem. |
| grupo de volume |No StorSimple Snapshot Manager, um grupo de volume é uma coleção de volumes configurados para facilitar o processamento de backup. |
| Serviço de cópia de sombra de volume (VSS) |Um sistema operativo Windows Server que facilita a consistência da aplicação comunicando com aplicações conscientes do VSS para coordenar a criação de instantâneos incrementais. A VSS garante que as aplicações estão temporariamente inativas quando são tiradas fotografias. |
| Windows PowerShell para StorSimple |Uma interface de linha de comando baseada no Windows PowerShell utilizada para operar e gerir o seu dispositivo StorSimple. Mantendo algumas das capacidades básicas do Windows PowerShell, esta interface tem cmdlets dedicados adicionais que estão orientados para a gestão de um dispositivo StorSimple. |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a segurança StorSimple](storsimple-8000-security.md).

