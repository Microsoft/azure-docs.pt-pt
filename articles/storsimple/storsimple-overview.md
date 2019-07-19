---
title: Visão geral da solução StorSimple série 8000 | Microsoft Docs
description: Descreve a camada do StorSimple, o dispositivo, o dispositivo virtual, os serviços e o gerenciamento de armazenamento e apresenta os principais termos usados no StorSimple.
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
ms.openlocfilehash: 5a3800cfdf36bdc2e042eec860310a508204ba11
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876439"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série StorSimple 8000: uma solução de armazenamento de nuvem híbrida
## <a name="overview"></a>Descrição geral
Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrada que gerencia tarefas de armazenamento entre dispositivos locais e Microsoft Azure armazenamento em nuvem. O StorSimple é uma solução de SAN (rede de área de armazenamento) eficiente, econômica e facilmente gerenciável que elimina muitos dos problemas e despesas associados ao armazenamento corporativo e à proteção de dados. Ele usa o dispositivo proprietário da série 8000 do StorSimple, integra-se aos serviços de nuvem e fornece um conjunto de ferramentas de gerenciamento para uma exibição direta de todo o armazenamento corporativo, incluindo o armazenamento em nuvem. (As informações de implantação do StorSimple publicadas no site Microsoft Azure aplicam-se somente a dispositivos StorSimple da série 8000. Se você estiver usando um dispositivo StorSimple da série 5000/7000, vá para a [ajuda do storsimple](http://onlinehelp.storsimple.com/).)

O StorSimple usa [camadas de armazenamento](#automatic-storage-tiering) para gerenciar dados armazenados em várias mídias de armazenamento. O conjunto de trabalho atual é armazenado localmente em SSDs (unidades de estado sólido), os dados usados com menos frequência são armazenados em HDDs (unidades de disco rígido) e os dados de arquivamento são enviados para a nuvem. Além disso, o StorSimple usa eliminação de duplicação e compactação para reduzir a quantidade de armazenamento que os dados consomem. Para obter mais informações, acesse [eliminação de duplicação e compactação](#deduplication-and-compression). Para obter definições de outros termos e conceitos principais usados na documentação da série StorSimple 8000, vá para a [terminologia do storsimple](#storsimple-terminology) no final deste artigo.

Além do gerenciamento de armazenamento, os recursos de proteção de dados do StorSimple permitem criar backups sob demanda e agendados e, em seguida, armazená-los localmente ou na nuvem. Os backups são feitos na forma de instantâneos incrementais, o que significa que eles podem ser criados e restaurados rapidamente. Os instantâneos de nuvem podem ser extremamente importantes em cenários de recuperação de desastres, pois substituem os sistemas de armazenamento secundários (como backup em fita) e permitem que você restaure dados para seu datacenter ou para sites alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista ao vídeo para obter uma breve introdução ao Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Por que usar o StorSimple?
A tabela a seguir descreve alguns dos principais benefícios que o Microsoft Azure StorSimple fornece.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |Usa o protocolo iSCSI para vincular invisivelmente instalações de armazenamento de dados. Isso garante que os dados armazenados na nuvem, no Datacenter ou em servidores remotos pareçam estar armazenados em um único local. |
| Custos de armazenamento reduzidos |Aloca armazenamento suficiente local ou na nuvem para atender às demandas atuais e estende o armazenamento em nuvem somente quando necessário. Ele reduz ainda mais os requisitos de armazenamento e as despesas eliminando as versões redundantes dos mesmos dados (eliminação de duplicação) e usando a compactação. |
| Gerenciamento de armazenamento simplificado |Fornece ferramentas de administração do sistema para configurar e gerenciar dados armazenados localmente, em um servidor remoto e na nuvem. Além disso, você pode gerenciar funções de backup e restauração de um snap-in do MMC (console de gerenciamento Microsoft).|
| Melhor recuperação de desastres e conformidade |Não requer tempo de recuperação estendido. Em vez disso, ele restaura os dados conforme necessário. Isso significa que as operações normais podem continuar com o mínimo de interrupção. Além disso, você pode configurar políticas para especificar agendamentos de backup e retenção de dados. |
| Mobilidade de dados |Os dados carregados no Microsoft Azure serviços de nuvem podem ser acessados de outros sites para fins de recuperação e migração. Além disso, você pode usar o StorSimple para configurar dispositivos de nuvem StorSimple em máquinas virtuais (VMs) em execução no Microsoft Azure. As VMs podem então usar dispositivos virtuais para acessar dados armazenados para fins de teste ou recuperação. |
| Continuidade do negócio |Permite que os usuários da série StorSimple 5000-7000 migrem seus dados para um dispositivo StorSimple da série 8000. |
| Disponibilidade no portal do Azure governamental |O StorSimple está disponível no portal do Azure governamental. Para obter mais informações, consulte [implantar seu dispositivo StorSimple local no portal do governo](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Proteção e disponibilidade de dados |A série StorSimple 8000 dá suporte ao ZRS (armazenamento com redundância de zona), além de LRS (armazenamento com redundância local) e GRS (armazenamento com redundância geográfica). Consulte [Este artigo sobre as opções de redundância de armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para obter detalhes do ZRS. |
| Suporte para aplicativos críticos |O StorSimple permite que você identifique os volumes apropriados como fixados localmente, o que, por sua vez, garante que os dados exigidos por aplicativos críticos não sejam enfileirados na nuvem. Os volumes fixados localmente não estão sujeitos a problemas de conectividade ou latências de nuvem. Para obter mais informações sobre volumes fixados localmente, consulte [usar o serviço de Device Manager do StorSimple para gerenciar volumes](storsimple-8000-manage-volumes-u2.md). |
| Baixa latência e alto desempenho |Você pode criar dispositivos de nuvem que tiram proveito dos recursos de alto desempenho e baixa latência do armazenamento Premium do Azure. Para obter mais informações sobre os dispositivos de nuvem Premium do StorSimple, consulte [implantar e gerenciar um dispositivo de nuvem storsimple no Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Componentes do StorSimple
A solução Microsoft Azure StorSimple inclui os seguintes componentes:

* **Dispositivo Microsoft Azure StorSimple** – uma matriz de armazenamento híbrido local que contém SSDs e HDDs, juntamente com controladores redundantes e recursos de failover automático. Os controladores gerenciam camadas de armazenamento, colocando dados atualmente usados (ou ativos) no armazenamento local (no dispositivo ou servidores locais), ao mesmo tempo em que movem os dados usados com menos frequência para a nuvem.
* Dispositivo de **nuvem storsimple** – também conhecido como dispositivo virtual storsimple, esta é uma versão de software do dispositivo storsimple que Replica a arquitetura e a maioria dos recursos do dispositivo de armazenamento híbrido físico. O dispositivo de nuvem StorSimple é executado em um único nó em uma máquina virtual do Azure. Os dispositivos virtuais Premium, que aproveitam o armazenamento Premium do Azure, estão disponíveis na atualização 2 e posterior.
* **Serviço de Device Manager do storsimple** – uma extensão do portal do Azure que permite que você gerencie um dispositivo storsimple ou um dispositivo de nuvem StorSimple de uma única interface da Web. Você pode usar o serviço de Device Manager do StorSimple para criar e gerenciar serviços, exibir e gerenciar dispositivos, exibir alertas, gerenciar volumes e exibir e gerenciar políticas de backup e o catálogo de backup.
* **Windows PowerShell para StorSimple** – uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem registrar seu dispositivo StorSimple, configurar a interface de rede em seu dispositivo, instalar determinados tipos de atualizações, solucionar problemas de seu dispositivo acessando a sessão de suporte e alterar o dispositivo status. Você pode acessar Windows PowerShell para StorSimple conectando-se ao console serial ou usando a comunicação remota do Windows PowerShell.
* **Azure PowerShell cmdlets do StorSimple** – uma coleção de cmdlets do Windows PowerShell que permitem automatizar tarefas de nível de serviço e migração na linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá para a [referência de cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **Snapshot Manager StorSimple** – um snap-in do MMC que usa grupos de volumes e o serviço de cópias de sombra de volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o StorSimple Snapshot Manager para criar agendamentos de backup e clonar ou restaurar volumes.
* **Adaptador StorSimple para SharePoint** – uma ferramenta que estende de forma transparente Microsoft Azure StorSimple armazenamento e proteção de dados para farms de servidores do SharePoint, ao mesmo tempo em que torna o armazenamento do StorSimple visível e gerenciável a partir do SharePoint Central Portal de administração.

O diagrama a seguir fornece uma exibição de alto nível da arquitetura e dos componentes do Microsoft Azure StorSimple.

![Arquitetura do StorSimple](./media/storsimple-overview/overview-big-picture.png)

As seções a seguir descrevem cada um desses componentes com mais detalhes e explicam como a solução organiza os dados, aloca o armazenamento e facilita o gerenciamento de armazenamento e a proteção de dados. A última seção fornece definições para alguns dos termos e conceitos importantes relacionados aos componentes do StorSimple e seu gerenciamento.

## <a name="storsimple-device"></a>Dispositivo StorSimple
O dispositivo Microsoft Azure StorSimple é uma matriz de armazenamento híbrido local que fornece armazenamento primário e acesso iSCSI aos dados armazenados nele. Ele gerencia a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados na solução de Microsoft Azure StorSimple.

O dispositivo StorSimple inclui SSDs e unidades de disco rígido HDDs, bem como suporte para clustering e failover automático. Ele contém um processador compartilhado, um armazenamento compartilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

* Conexão com um computador host
* Até seis portas de rede para se conectar à rede local (LAN)
* Monitoramento de hardware
* A memória de acesso aleatório não volátil (NVRAM), que retém informações mesmo se a energia for interrompida
* Atualização com suporte a cluster para gerenciar atualizações de software em servidores em um cluster de failover para que as atualizações tenham mínimo ou nenhum efeito sobre a disponibilidade do serviço
* Serviço de cluster, que funciona como um cluster de back-end, fornecendo alta disponibilidade e minimizando quaisquer efeitos adversos que possam ocorrer se um HDD ou SSD falhar ou ficar offline

Somente um controlador está ativo em qualquer ponto no tempo. Se o controlador ativo falhar, o segundo controlador se tornará ativo automaticamente.

Para obter mais informações, vá para [componentes de hardware do StorSimple e status](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Você pode usar o StorSimple para criar um dispositivo de nuvem que Replica a arquitetura e os recursos do dispositivo de armazenamento híbrido físico. O dispositivo de nuvem StorSimple (também conhecido como o dispositivo virtual StorSimple) é executado em um único nó em uma máquina virtual do Azure. (Um dispositivo de nuvem só pode ser criado em uma máquina virtual do Azure. Você não pode criar um em um dispositivo StorSimple ou em um servidor local.)

O dispositivo de nuvem tem os seguintes recursos:

* Ele se comporta como um dispositivo físico e pode oferecer uma interface iSCSI para máquinas virtuais na nuvem.
* Você pode criar um número ilimitado de dispositivos de nuvem na nuvem e ativá-los e desligá-los conforme necessário.
* Ele pode ajudar a simular ambientes locais em cenários de recuperação de desastres, desenvolvimento e teste e pode ajudar com a recuperação em nível de item de backups.

O dispositivo de nuvem StorSimple está disponível em dois modelos: o dispositivo 8010 (anteriormente conhecido como o modelo 1100) e o dispositivo 8020. O dispositivo 8010 tem uma capacidade máxima de 30 TB. O dispositivo 8020, que aproveita o armazenamento Premium do Azure, tem uma capacidade máxima de 64 TB. (Em camadas locais, o armazenamento Premium do Azure armazena dados no SSDs, enquanto o armazenamento Standard armazena dados em HDDs.) Observe que você deve ter uma conta de armazenamento Premium do Azure para usar o armazenamento Premium.

Para obter mais informações sobre o dispositivo de nuvem StorSimple, acesse [implantar e gerenciar um dispositivo de nuvem storsimple no Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple
O Microsoft Azure StorSimple fornece uma interface do usuário baseada na Web (o serviço StorSimple Device Manager) que permite que você gerencie centralmente o datacenter e o armazenamento em nuvem. Você pode usar o serviço de Device Manager do StorSimple para executar as seguintes tarefas:

* Definir configurações do sistema para dispositivos StorSimple.
* Configurar e gerenciar configurações de segurança para dispositivos StorSimple.
* Configurar credenciais e propriedades de nuvem.
* Configurar e gerenciar volumes em um servidor.
* Configurar grupos de volumes.
* Fazer backup e restaurar dados.
* Monitore o desempenho.
* Examine as configurações do sistema e identifique possíveis problemas.

Você pode usar o serviço de Device Manager do StorSimple para executar todas as tarefas de administração, exceto aquelas que exigem o tempo de inatividade do sistema, como instalação inicial e instalação de atualizações.

Para obter mais informações, acesse [usar o serviço storsimple Device Manager para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple
O Windows PowerShell para StorSimple fornece uma interface de linha de comando que você pode usar para criar e gerenciar o serviço de Microsoft Azure StorSimple e configurar e monitorar dispositivos StorSimple. É uma interface de linha de comando baseada no Windows PowerShell que inclui cmdlets dedicados para gerenciar seu dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem:

* Registrar um dispositivo.
* Configure a interface de rede em um dispositivo.
* Instale determinados tipos de atualizações.
* Solucione o problema do seu dispositivo acessando a sessão de suporte.
* Alterar o estado do dispositivo.

Você pode acessar Windows PowerShell para StorSimple de um console serial (em um computador host conectado diretamente ao dispositivo) ou remotamente usando a comunicação remota do Windows PowerShell. Observe que algumas tarefas de Windows PowerShell para StorSimple, como o registro de dispositivo inicial, só podem ser feitas no console serial.

Para obter mais informações, acesse [usar Windows PowerShell para StorSimple para administrar seu dispositivo](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell cmdlets do StorSimple
Os cmdlets Azure PowerShell StorSimple são uma coleção de cmdlets do Windows PowerShell que permitem automatizar tarefas de nível de serviço e migração na linha de comando. Para obter mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá para a [referência de cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>Snapshot Manager do StorSimple
O StorSimple Snapshot Manager é um snap-in do MMC (console de gerenciamento Microsoft) que você pode usar para criar cópias de backup consistentes e pontuais de dados locais e na nuvem. O snap-in é executado em um host baseado no Windows Server. Você pode usar o Snapshot Manager do StorSimple para:

* Configurar, fazer backup e excluir volumes.
* Configure grupos de volumes para garantir que os dados de backup sejam consistentes com o aplicativo.
* Gerencie políticas de backup para que os dados sejam copiados em um agendamento predeterminado e armazenados em um local designado (localmente ou na nuvem).
* Restaurar volumes e arquivos individuais.

Os backups são capturados como instantâneos, que registram apenas as alterações desde que o último instantâneo foi tirado e exigem muito menos espaço de armazenamento do que os backups completos. Você pode criar agendamentos de backup ou fazer backups imediatos, conforme necessário. Além disso, você pode usar a Snapshot Manager do StorSimple para estabelecer políticas de retenção que controlam quantos instantâneos serão salvos. Se, posteriormente, você precisar restaurar dados de um backup, o StorSimple Snapshot Manager permite que você selecione no catálogo de instantâneos locais ou na nuvem. 

Se ocorrer um desastre ou se você precisar restaurar dados por outro motivo, o StorSimple Snapshot Manager restaura-o incrementalmente conforme necessário. A restauração de dados não exige que você desligue todo o sistema enquanto restaura um arquivo, substitui equipamentos ou move operações para outro site.

Para obter mais informações, acesse [o que é StorSimple snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador do StorSimple para o SharePoint
O Microsoft Azure StorSimple inclui o adaptador StorSimple para SharePoint, um componente opcional que estende de forma transparente os recursos de armazenamento e de proteção de dados do StorSimple para farms de servidores do SharePoint. O adaptador funciona com um provedor de armazenamento de BLOB Remoto (RBS) e o recurso SQL Server RBS, permitindo que você mova os BLOBs para um servidor de backup pelo sistema Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os dados de BLOB localmente ou na nuvem, com base no uso.

O adaptador StorSimple para SharePoint é gerenciado no portal de administração central do SharePoint. Consequentemente, o gerenciamento do SharePoint permanece centralizado e todo o armazenamento parece estar no farm do SharePoint.

Para obter mais informações, acesse [adaptador StorSimple para SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento
Além do dispositivo StorSimple dedicado, dispositivo virtual e outros componentes, o Microsoft Azure StorSimple usa as seguintes tecnologias de software para fornecer acesso rápido aos dados e para reduzir o consumo de armazenamento:

* [Camadas de armazenamento automático](#automatic-storage-tiering) 
* [Provisionamento dinâmico](#thin-provisioning) 
* [Eliminação de duplicação e compactação](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Camadas de armazenamento automático
Microsoft Azure StorSimple organiza automaticamente os dados em camadas lógicas com base no uso atual, na idade e no relacionamento com outros dados. Os dados que são mais ativos são armazenados localmente, enquanto os dados menos ativos e inativos são migrados automaticamente para a nuvem. O diagrama a seguir ilustra essa abordagem de armazenamento.

![Camadas de armazenamento do StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para habilitar o acesso rápido, o StorSimple armazena dados muito ativos (dados ativos) no SSDs no dispositivo StorSimple. Ele armazena dados que são usados ocasionalmente (dados quentes) em HDDs no dispositivo ou em servidores no datacenter. Ele move dados inativos, dados de backup e dados retidos para fins de arquivamento ou conformidade para a nuvem. 

> [!NOTE]
> Na atualização 2 ou posterior, você pode especificar um volume como fixado localmente, caso em que os dados permanecem no dispositivo local e não são em camadas para a nuvem. 


O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento conforme os padrões de uso mudam. Por exemplo, algumas informações podem se tornar menos ativas ao longo do tempo. Como ele se torna progressivamente menos ativo, ele é migrado do SSDs para HDDs e, em seguida, para a nuvem. Se esses mesmos dados se tornarem ativos novamente, eles serão migrados de volta para o dispositivo de armazenamento.

O processo de camadas de armazenamento ocorre da seguinte maneira:

1. Um administrador do sistema configura um Microsoft Azure conta de armazenamento em nuvem.
2. O administrador usa o console serial e o serviço StorSimple Device Manager (em execução no portal do Azure) para configurar o dispositivo e o servidor de arquivos, criando políticas de proteção de volumes e de dados. Os computadores locais (como servidores de arquivos) usam a Internet Small Computer System interface (iSCSI) para acessar o dispositivo StorSimple.
3. Inicialmente, o StorSimple armazena dados na camada de SSD rápida do dispositivo.
4. À medida que a camada SSD se aproxima da capacidade, o StorSimple elimina e compacta os blocos de dados mais antigos e os move para a camada HDD.
5. À medida que a camada HDD se aproxima da capacidade, o StorSimple criptografa os blocos de dados mais antigos e os envia com segurança para a conta de armazenamento Microsoft Azure via HTTPS.
6. Microsoft Azure cria várias réplicas dos dados em seu datacenter e em um datacenter remoto, garantindo que os dados possam ser recuperados se ocorrer um desastre.
7. Quando o servidor de arquivos solicita dados armazenados na nuvem, o StorSimple o retorna diretamente e armazena uma cópia na camada SSD do dispositivo StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Como o StorSimple gerencia dados na nuvem

O StorSimple elimina a duplicação de dados do cliente em todos os instantâneos e nos dados primários (dados gravados por hosts). Embora a eliminação de duplicação seja ótima para a eficiência do armazenamento, ela faz a pergunta "o que está na nuvem" é complicado. Os dados primários em camadas e os dados de instantâneo se sobrepõem. Um único bloco de dados na nuvem poderia ser usado como dados primários em camadas e também ser referenciado por vários instantâneos. Cada instantâneo de nuvem garante que uma cópia de todos os dados pontuais esteja bloqueada na nuvem até que esse instantâneo seja excluído.

Os dados só são excluídos da nuvem quando não há nenhuma referência a esses dados. Por exemplo, se pegarmos um instantâneo de nuvem de todos os dados que estão no dispositivo StorSimple e, em seguida, excluirmos alguns dados primários, veremos os _dados primários_ imediatamente. Os _dados de nuvem_ que incluem os dados em camadas e os backups permanecem os mesmos. Isso ocorre porque há um instantâneo que ainda faz referência aos dados da nuvem. Depois que o instantâneo de nuvem é excluído (e qualquer outro instantâneo que referenciou os mesmos dados), o consumo de nuvem cai. Antes de removermos dados de nuvem, verificamos se nenhum instantâneo ainda faz referência a esses dados. Esse processo é chamado de _coleta de lixo_ e é um serviço em segundo plano em execução no dispositivo. A remoção de dados de nuvem não é imediata, pois o serviço de coleta de lixo verifica se há outras referências a esses dados antes da exclusão. A velocidade da coleta de lixo depende do número total de instantâneos e do total de dados. Normalmente, os dados de nuvem são limpos em menos de uma semana.


### <a name="thin-provisioning"></a>Provisionamento dinâmico
O provisionamento dinâmico é uma tecnologia de virtualização na qual o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o StorSimple usa provisionamento dinâmico para alocar espaço suficiente para atender aos requisitos atuais. A natureza elástica do armazenamento em nuvem facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às demandas em constante mudança.

> [!NOTE]
> Os volumes fixados localmente não são provisionados de thin. O armazenamento alocado para um volume somente local é provisionado em sua totalidade quando o volume é criado.


### <a name="deduplication-and-compression"></a>Eliminação de duplicação e compactação
Microsoft Azure StorSimple usa eliminação de duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento.

A eliminação de duplicação reduz a quantidade geral de dados armazenados eliminando a redundância no conjunto de dados armazenado. À medida que as informações são alteradas, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados identificando e removendo informações desnecessárias. 

> [!NOTE]
> Os dados em volumes fixados localmente não têm eliminação de duplicação ou compactação. No entanto, os backups de volumes localmente afixados são eliminados e compactados.


## <a name="storsimple-workload-summary"></a>Resumo da carga de trabalho do StorSimple
Um resumo das cargas de trabalho do StorSimple com suporte é tabulado abaixo.

| Cenário | Carga de trabalho | Suportadas | Restrições | Version |
| --- | --- | --- | --- | --- |
| Colaboração |Compartilhamento de arquivos |Sim | |Todas as versões |
| Colaboração |Compartilhamento de arquivos distribuídos |Sim | |Todas as versões |
| Colaboração |SharePoint |Sim* |Com suporte apenas com volumes localmente afixados |Atualização 2 e posterior |
| Arquivamento |Arquivamento de arquivo simples |Sim | |Todas as versões |
| Virtualização |Máquinas virtuais |Sim* |Com suporte apenas com volumes localmente afixados |Atualização 2 e posterior |
| Base de Dados |SQL |Sim* |Com suporte apenas com volumes localmente afixados |Atualização 2 e posterior |
| Vigilância por vídeo |Vigilância por vídeo |Sim* |Com suporte quando o dispositivo StorSimple é dedicado somente a essa carga de trabalho |Atualização 2 e posterior |
| Cópia de segurança |Backup de destino primário |Sim* |Com suporte quando o dispositivo StorSimple é dedicado somente a essa carga de trabalho |Atualização 3 e posterior |
| Cópia de segurança |Backup de destino secundário |Sim* |Com suporte quando o dispositivo StorSimple é dedicado somente a essa carga de trabalho |Atualização 3 e posterior |

*Sim&#42; -as diretrizes e restrições da solução devem ser aplicadas.*

As cargas de trabalho a seguir não têm suporte dos dispositivos da série StorSimple 8000. Se implantado no StorSimple, essas cargas de trabalho resultarão em uma configuração sem suporte.

* Imagens médicas
* Exchange
* VDI
* Oracle
* SAP
* Macrodados
* Distribuição de conteúdo
* Inicializar do SCSI

A seguir está uma lista dos componentes de infraestrutura com suporte do StorSimple.

| Cenário | Carga de trabalho | Suportadas | Restrições | Version |
| --- | --- | --- | --- | --- |
| Geral |ExpressRoute |Sim | |Todas as versões |
| Geral |FC do DataCore |Sim* |Com suporte com DataCore SANsymphony |Todas as versões |
| Geral |SYSVOL |Sim* |Com suporte apenas com volumes localmente afixados |Todas as versões |
| Geral |Indexação |Sim* |Para volumes em camadas, somente a indexação de metadados tem suporte (sem dados).<br>Para volumes fixados localmente, há suporte para a indexação completa. |Todas as versões |
| Geral |Antivírus |Sim* |Para volumes em camadas, há suporte apenas para verificação em abrir e fechar.<br> Para volumes fixados localmente, há suporte para verificação completa. |Todas as versões |

*Sim&#42; -as diretrizes e restrições da solução devem ser aplicadas.*

Veja a seguir uma lista de outros softwares usados com o StorSimple para criar soluções.

| Tipo de carga de trabalho | Software usado com o StorSimple | Versões suportadas|Link para o guia de solução| 
| --- | --- | --- | --- |
| Destino de backup |Veeam |Veeam v 9 e posterior |[StorSimple como um destino de backup com Veaam](storsimple-configure-backup-target-veeam.md)|
| Destino de backup |Veritas Backup Exec |Backup Exec 16 e posterior |[StorSimple como um destino de backup com o Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Destino de backup |Veritas NetBackup |NetBackup 7.7. x e posterior  |[StorSimple como um destino de backup com o NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Compartilhamento de arquivos global <br></br> Colaboração |Talon  |[StorSimple com Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia do StorSimple
Antes de implantar sua solução de Microsoft Azure StorSimple, recomendamos que você revise os seguintes termos e definições.

### <a name="key-terms-and-definitions"></a>Principais termos e definições
| Termo (acrônimo ou abreviação) | Descrição |
| --- | --- |
| registro de controle de acesso (ACR) |Um registro associado a um volume em seu Microsoft Azure StorSimple dispositivo que determina quais hosts podem se conectar a ele. A determinação é baseada no nome qualificado iSCSI (IQN) dos hosts (contidos no ACR) que estão se conectando ao seu dispositivo StorSimple. |
| AES-256 |Um algoritmo de criptografia AES de 256 bits (AES) para criptografar dados à medida que eles se movem de e para a nuvem. |
| tamanho da unidade de alocação (AUS) |A menor quantidade de espaço em disco que pode ser alocada para manter um arquivo em seus sistemas de arquivos do Windows. Se um tamanho de arquivo não for um múltiplo par do tamanho do cluster, o espaço extra deverá ser usado para manter o arquivo (até o próximo múltiplo do tamanho do cluster), resultando em espaço perdido e na fragmentação do disco rígido. <br>A AUS recomendada para os volumes do Azure StorSimple é 64 KB, pois funciona bem com os algoritmos de eliminação de duplicação. |
| camadas de armazenamento automatizado |Mover automaticamente menos dados ativos do SSDs para HDDs e, em seguida, para uma camada na nuvem e, em seguida, habilitar o gerenciamento de todo o armazenamento de uma interface do usuário central. |
| Catálogo de backup |Uma coleção de backups, geralmente relacionada ao tipo de aplicativo que foi usado. Essa coleção é exibida na folha catálogo de backup da interface do usuário do serviço StorSimple Device Manager. |
| arquivo de catálogo de backup |Um arquivo que contém uma lista de instantâneos disponíveis atualmente armazenados no banco de dados de backup do StorSimple Snapshot Manager. |
| política de backup |Uma seleção de volumes, tipo de backup e um timetable que permite criar backups em um agendamento predefinido. |
| BLOBs (objetos binários grandes) |Uma coleção de dados binários armazenados como uma única entidade em um sistema de gerenciamento de banco de dados. Os BLOBs normalmente são imagens, áudio ou outros objetos de multimídia, embora, às vezes, o código executável binário seja armazenado como um BLOB. |
| Protocolo CHAP (Challenge Handshake Authentication Protocol) |Um protocolo usado para autenticar o par de uma conexão, com base no ponto de compartilhamento de uma senha ou segredo. O CHAP pode ser unidirecional ou mútuo. Com o CHAP unidirecional, o destino autentica um iniciador. O CHAP mútuo requer que o destino autentique o iniciador e que o iniciador autentique o destino. |
| 8i |Uma cópia duplicada de um volume. |
| Nuvem como camada (CaaT) |Armazenamento em nuvem integrado como uma camada dentro da arquitetura de armazenamento para que todo o armazenamento pareça fazer parte de uma rede de armazenamento empresarial. |
| provedor de serviços de nuvem (CSP) |Um provedor de serviços de computação em nuvem. |
| instantâneo de nuvem |Uma cópia pontual dos dados de volume que são armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo replicado em um sistema de armazenamento diferente, fora do local. Os instantâneos de nuvem são particularmente úteis em cenários de recuperação de desastres. |
| chave de criptografia de armazenamento em nuvem |Uma senha ou uma chave usada pelo seu dispositivo StorSimple para acessar os dados criptografados enviados pelo seu dispositivo para a nuvem. |
| atualização com suporte a cluster |Gerenciamento de atualizações de software em servidores em um cluster de failover para que as atualizações tenham mínimo ou nenhum efeito sobre a disponibilidade do serviço. |
| caminho |Uma coleção de unidades funcionais que executam operações de processamento de dados entre as conectadas. |
| ativar |Uma ação permanente que interrompe a conexão entre o dispositivo StorSimple e o serviço de nuvem associado. Os instantâneos de nuvem do dispositivo permanecem após esse processo e podem ser clonados ou usados para recuperação de desastres. |
| espelhamento de disco |Replicação de volumes de disco lógico em discos rígidos separados em tempo real para garantir a disponibilidade contínua. |
| espelhamento de disco dinâmico |Replicação de volumes de disco lógico em discos dinâmicos. |
| discos dinâmicos |Um formato de volume de disco que usa o Gerenciador de discos lógicos (LDM) para armazenar e gerenciar dados em vários discos físicos. Discos dinâmicos podem ser ampliados para fornecer mais espaço livre. |
| Compartimento de EBOD (vários discos estendidos) |Um compartimento secundário do seu dispositivo Microsoft Azure StorSimple que contém discos rígidos adicionais para armazenamento adicional. |
| provisionamento de Fat |Um provisionamento de armazenamento convencional no qual o espaço de armazenamento é alocado com base nas necessidades antecipadas (e geralmente está além da necessidade atual). Consulte também *provisionamento dinâmico*. |
| unidade de disco rígido (HDD) |Uma unidade que usa Platters em rotação para armazenar dados. |
| armazenamento em nuvem híbrida |Uma arquitetura de armazenamento que usa recursos locais e fora do local, incluindo o armazenamento em nuvem. |
| Internet Small Computer System interface (iSCSI) |Um padrão de rede de armazenamento baseado no protocolo IP para vincular equipamentos ou instalações de armazenamento de dados. |
| iniciador iSCSI |Um componente de software que permite que um computador host executando o Windows se conecte a uma rede de armazenamento baseada em iSCSI externa. |
| IQN (nome qualificado do iSCSI) |Um nome exclusivo que identifica um destino iSCSI ou iniciador. |
| destino iSCSI |Um componente de software que fornece subsistemas de disco iSCSI centralizados em redes de área de armazenamento. |
| arquivamento dinâmico |Uma abordagem de armazenamento na qual os dados de arquivamento estão acessíveis o tempo todo (não é armazenado fora do local em fita, por exemplo). O Microsoft Azure StorSimple usa arquivamento ao vivo. |
| volume fixado localmente |um volume que reside no dispositivo e que nunca é colocado em camadas na nuvem. |
| instantâneo local |Uma cópia pontual dos dados de volume armazenados no dispositivo de Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Uma solução poderosa que consiste em um software e um dispositivo de armazenamento de datacenter que permite que as organizações de ti aproveitem o armazenamento em nuvem como se fosse um armazenamento de datacenter. O StorSimple simplifica a proteção de dados e o gerenciamento de dados, reduzindo os custos. A solução consolida o armazenamento primário, o arquivamento, o backup e a recuperação de desastres (DR) por meio da integração direta com a nuvem. Ao combinar o armazenamento SAN e o gerenciamento de dados na nuvem em uma plataforma de classe empresarial, os dispositivos StorSimple permitem velocidade, simplicidade e confiabilidade para todas as necessidades relacionadas ao armazenamento. |
| Módulo de energia e resfriamento (PCM) |Componentes de hardware do seu dispositivo StorSimple que consistem em fontes de alimentação e ventilador de resfriamento, portanto, o nome módulo de energia e resfriamento. O compartimento principal do dispositivo tem duas 764W PCMs, enquanto o compartimento EBOD tem dois 580W PCMs. |
| compartimento principal |O compartimento principal do seu dispositivo StorSimple que contém os controladores de plataforma de aplicativo. |
| objetivo de tempo de recuperação (RTO) |A quantidade máxima de tempo que deve ser gasta antes que um processo de negócios ou sistema seja totalmente restaurado após um desastre. |
| SAS (Serial Attached SCSI) |Um tipo de unidade de disco rígido (HDD). |
| chave de criptografia de dados de serviço |Uma chave disponibilizada para qualquer novo dispositivo StorSimple que se registra com o serviço StorSimple Device Manager. Os dados de configuração transferidos entre o serviço de Device Manager do StorSimple e o dispositivo são criptografados usando uma chave pública e, em seguida, podem ser descriptografados somente no dispositivo usando uma chave privada. A chave de criptografia de dados de serviço permite que o serviço obtenha essa chave privada para descriptografia. |
| chave de registro do serviço |Uma chave que ajuda a registrar o dispositivo StorSimple com o serviço de Device Manager do StorSimple para que ele apareça no portal do Azure para ações de gerenciamento adicionais. |
| Interface de sistema de computador pequeno (SCSI) |Um conjunto de padrões para conectar computadores fisicamente e passar dados entre eles. |
| unidade de estado sólido (SSD) |Um disco que não contém partes móveis; por exemplo, uma unidade flash. |
| conta de armazenamento |Um conjunto de credenciais de acesso vinculadas à sua conta de armazenamento para um determinado provedor de serviços de nuvem. |
| Adaptador do StorSimple para o SharePoint |Um componente Microsoft Azure StorSimple que estende de forma transparente o armazenamento do StorSimple e a proteção de dados para farms de servidores do SharePoint. |
| Serviço de Gestor de Dispositivos do StorSimple |Uma extensão do portal do Azure que permite que você gerencie seus dispositivos locais e virtuais do Azure StorSimple. |
| Snapshot Manager do StorSimple |Um snap-in do MMC (console de gerenciamento Microsoft) para gerenciar operações de backup e restauração no Microsoft Azure StorSimple. |
| fazer backup |Um recurso que permite ao usuário fazer um backup interativo de um volume. É uma maneira alternativa de fazer um backup manual de um volume em vez de fazer um backup automatizado por meio de uma política definida. |
| provisionamento dinâmico |Um método de otimizar a eficiência com a qual o espaço de armazenamento disponível é usado em sistemas de armazenamento. No provisionamento dinâmico, o armazenamento é alocado entre vários usuários com base no espaço mínimo exigido por cada usuário em um determinado momento. Consulte também *provisionamento de Fat*. |
| camadas |Organizando dados em agrupamentos lógicos com base no uso atual, na idade e no relacionamento com outros dados. O StorSimple organiza automaticamente os dados em camadas. |
| volume |Áreas de armazenamento lógico apresentadas na forma de unidades. Os volumes do StorSimple correspondem aos volumes montados pelo host, incluindo aqueles descobertos por meio do uso de iSCSI e um dispositivo StorSimple. |
| contêiner de volume |Um agrupamento de volumes e as configurações que se aplicam a eles. Todos os volumes em seu dispositivo StorSimple são agrupados em contêineres de volume. As configurações de contêiner de volume incluem contas de armazenamento, configurações de criptografia para dados enviados para a nuvem com chaves de criptografia associadas e largura de banda consumida para operações que envolvem a nuvem. |
| grupo de volumes |No StorSimple Snapshot Manager, um grupo de volumes é uma coleção de volumes configurados para facilitar o processamento de backup. |
| Serviço de Cópias de Sombra de Volume (VSS) |Um serviço do sistema operacional Windows Server que facilita a consistência do aplicativo comunicando-se com aplicativos com reconhecimento de VSS para coordenar a criação de instantâneos incrementais. O VSS garante que os aplicativos estejam temporariamente inativos quando os instantâneos são feitos. |
| Windows PowerShell para StorSimple |Uma interface de linha de comando baseada no Windows PowerShell usada para operar e gerenciar seu dispositivo StorSimple. Ao mesmo tempo em que mantém alguns dos recursos básicos do Windows PowerShell, essa interface tem cmdlets dedicados adicionais que são voltados para gerenciar um dispositivo StorSimple. |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a [segurança do StorSimple](storsimple-8000-security.md).

