---
title: Descrição geral da solução de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve a criação de camadas do StorSimple, o dispositivo, dispositivo virtual, serviços e gestão de armazenamento e apresenta os principais termos utilizados no StorSimple.
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
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 63906e65acb8e8aa836e6e59714bddca24ea21eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630214"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série StorSimple 8000: uma solução de armazenamento na cloud híbrida
## <a name="overview"></a>Descrição geral
Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrada que gere tarefas de armazenamento entre dispositivos no local e o armazenamento de nuvem do Microsoft Azure. StorSimple é uma solução de (SAN rede) de área de armazenamento eficiente, rentável e facilmente gerível que elimina muitos dos problemas e despesas associadas a proteção de armazenamento e dados da empresa. Utiliza o dispositivo da série StorSimple 8000 proprietário, integra-se com os serviços cloud e fornece um conjunto de ferramentas de gestão para uma vista homogénea de todo o armazenamento empresarial, incluindo o armazenamento na cloud. (As informações de implementação do StorSimple publicadas no site do Microsoft Azure aplica-se apenas a dispositivos série StorSimple 8000. Se estiver a utilizar um dispositivo da série StorSimple 5000/7000, aceda a [StorSimple ajuda](http://onlinehelp.storsimple.com/).)

O StorSimple utiliza [armazenamento em camadas](#automatic-storage-tiering) para gerir dados armazenados em vários suportes de dados de armazenamento. O conjunto de trabalho atual é armazenado no local em unidades de estado sólido (SSDs) e dados que são utilizados com menos frequência são armazenados em unidades de disco rígido (HDDs) para dados de arquivo são emitidos via push para a cloud. Além disso, o StorSimple utiliza a eliminação de duplicados e a compressão para reduzir a quantidade de armazenamento que consome os dados. Para obter mais informações, aceda a [eliminação de duplicados e compressão](#deduplication-and-compression). Para obter definições de outros termos-chave e conceitos utilizados na documentação de série StorSimple 8000, aceda a [StorSimple terminologia](#storsimple-terminology) no final deste artigo.

Para além da gestão de armazenamento, funcionalidades de proteção de dados do StorSimple permitem-lhe criar sob demanda e cópias de segurança agendadas e, em seguida, armazená-los localmente ou na cloud. Backups são feitos na forma de instantâneos incrementais, que significa que pode ser criadas e restauradas rapidamente. Instantâneos de cloud podem ser extremamente importantes em cenários de recuperação após desastre, pois substituir sistemas de armazenamento secundário (por exemplo, a cópia de segurança de banda) e permitem-lhe restaurar dados para o seu datacenter ou para sites alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Assista ao vídeo para uma rápida introdução ao Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Porquê utilizar o StorSimple?
A tabela seguinte descreve algumas das principais vantagens que o Microsoft Azure StorSimple fornece.

| Funcionalidade | Vantagem |
| --- | --- |
| Integração transparente |Utiliza o protocolo iSCSI invisivelmente ligar recursos de armazenamento de dados. Isso garante que os dados armazenados na cloud, no Centro de dados, ou em servidores remotos parece ser armazenados num único local. |
| Custos de armazenamento reduzidos |Aloca local suficiente ou armazenamento na cloud para satisfazer as necessidades atuais e expande o armazenamento na cloud apenas quando necessário. Ele reduz ainda mais os requisitos de armazenamento e a despesa, eliminando redundantes versões dos mesmos dados (eliminação de duplicados) e através da compressão. |
| Gestão de armazenamento simplificado |Fornece ferramentas de administração para configurar e gerir dados armazenados no local, num servidor remoto e na cloud de sistema. Além disso, pode gerir a cópia de segurança e restaurar as funções a partir de um snap-in Consola de gestão da Microsoft (MMC).|
| Recuperação de desastres aprimorados e conformidade |Não necessita de tempo de recuperação expandida. Em vez disso, ele restaura dados conforme necessário. Isso significa que as operações normais podem continuar com a mínima interrupção. Além disso, pode configurar políticas para especificar agendas de cópia de segurança e retenção de dados. |
| Mobilidade de dados |Dados carregados nos serviços cloud Microsoft Azure podem ser acedidos a partir de outros sites para fins de recuperação e a migração. Além disso, pode utilizar o StorSimple para configurar o StorSimple Cloud Appliances em máquinas virtuais (VMs) em execução no Microsoft Azure. As VMs, em seguida, podem utilizar dispositivos virtuais para acessar dados armazenados para fins de teste ou de recuperação. |
| Continuidade do negócio |Permite que o StorSimple utilizadores de série 5000-7000 migrar os seus dados para um dispositivo da série StorSimple 8000. |
| Disponibilidade no Portal do Azure Government |O StorSimple está disponível no Portal de administração pública do Azure. Para obter mais informações, consulte [implementar o dispositivo do StorSimple no local no Portal do Governo](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Proteção de dados e disponibilidade |A série 8000 do StorSimple suporta com redundância de armazenamento zona (ZRS), além do armazenamento do armazenamento localmente redundante (LRS) e georredundante (GRS). Consulte a [este artigo sobre as opções de redundância de armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para obter detalhes ZRS. |
| Suporte para aplicações críticas |O StorSimple permite-lhe identificar apropriados volumes afixados localmente como que por sua vez garante que os dados necessários para aplicativos críticos não em camadas para a cloud. Os volumes afixados localmente não são sujeitos a latências de cloud ou problemas de conectividade. Para obter mais informações sobre os volumes afixados localmente, consulte [utilizar o serviço StorSimple Device Manager para gerir volumes](storsimple-8000-manage-volumes-u2.md). |
| Baixa latência e elevado desempenho |Pode criar aplicações na cloud que tiram partido de elevado desempenho e recursos de baixa latência de armazenamento premium do Azure. Para obter mais informações sobre aplicações de cloud do StorSimple premium, consulte [implementar e gerir uma StorSimple Cloud Appliance no Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Componentes do StorSimple
A solução Microsoft Azure StorSimple inclui os seguintes componentes:

* **Dispositivo do Microsoft Azure StorSimple** – uma matriz de armazenamento híbrida no local que contém o SSDs e HDDs, juntamente com os controladores de redundantes e capacidades de ativação pós-falha automática. Os controladores de gestão do armazenamento em camadas, colocar atualmente utilizados (ou frequente) dados no armazenamento local (no dispositivo ou no local servidores), ao mover menos dados utilizados com frequência para a cloud.
* **StorSimple Cloud Appliance** – também conhecido como o StorSimple Virtual Appliance, esta é uma versão do software do dispositivo do StorSimple que replica a arquitetura e a maioria das capacidades do dispositivo de armazenamento híbrido físico. A StorSimple Cloud Appliance é executada num único nó numa máquina virtual do Azure. Dispositivos virtuais Premium, que tiram partido do armazenamento premium do Azure, estão disponíveis na atualização 2 e posterior.
* **Serviço StorSimple Device Manager** -uma extensão do portal do Azure que lhe permite gerir uma StorSimple Cloud Appliance ou um dispositivo StorSimple a partir de uma interface web único. Pode utilizar o serviço StorSimple Device Manager para criar e gerir serviços, ver e gerir dispositivos, ver alertas, gerenciar volumes e ver e gerir políticas de cópia de segurança e o catálogo de cópia de segurança.
* **Windows PowerShell para StorSimple** – uma interface de linha de comandos que pode utilizar para gerir o dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem-lhe registar o seu dispositivo StorSimple, configure a interface de rede no seu dispositivo, instalar determinados tipos de atualizações, resolver problemas relacionados com o seu dispositivo ao aceder a sessão de suporte e alterar o dispositivo Estado. Pode acessar a Windows PowerShell para StorSimple, conectando-se à consola de série ou ao utilizar a comunicação remota do Windows PowerShell.
* **Cmdlets do Azure PowerShell StorSimple** – uma coleção de cmdlets do Windows PowerShell que permitem-lhe automatizar tarefas de migração e de nível de serviço a partir da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para o [referência de cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **Snapshot Manager do StorSimple** – um snap-in do MMC que utiliza grupos de volumes e o serviço de cópia de sombra de Volume do Windows para gerar as cópias de segurança consistentes com aplicações. Além disso, pode utilizar o Snapshot Manager do StorSimple para criar agendas de cópia de segurança e clonar ou restaurar volumes.
* **StorSimple Adapter para SharePoint** – uma ferramenta de que forma transparente expande a proteção de dados e de armazenamento do Microsoft Azure StorSimple para o SharePoint Server farms, ao tornar o armazenamento StorSimple podem ser visualizados e gerenciável de Central do SharePoint Portal de administração.

O diagrama abaixo fornece uma visão geral da arquitetura do Microsoft Azure StorSimple e componentes.

![Arquitetura do StorSimple](./media/storsimple-overview/overview-big-picture.png)

As secções seguintes descrevem cada um desses componentes em mais detalhes e explicam como a solução organiza os dados, aloca o armazenamento e facilita a gestão de armazenamento e proteção de dados. A última seção fornece definições para alguns dos termos importantes e conceitos relacionados com componentes do StorSimple e o gerenciamento.

## <a name="storsimple-device"></a>Dispositivo StorSimple
O dispositivo do Microsoft Azure StorSimple é uma matriz de armazenamento híbrida no local que fornece armazenamento primário e o iSCSI acesso aos dados armazenados no mesmo. Ele gerencia a comunicação com o armazenamento na cloud e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados na solução Microsoft Azure StorSimple.

O dispositivo StorSimple inclui o SSDs e HDDs de unidades de disco rígido, bem como suporte para a ativação pós-falha automática e clustering. Ele contém um processador partilhado, armazenamento partilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

* Ligação a um computador anfitrião
* Até seis portas de rede para ligar à rede de área local (LAN)
* Monitoramento de hardware
* Memória de acesso de aleatório não volátil (NVRAM), que mantém informações, mesmo que a energia é interrompida
* Cluster-atualização com suporte para gerir atualizações de software em servidores no cluster de ativação pós-falha, para que as atualizações de tem um mínimo ou nenhum efeito sobre a disponibilidade do serviço
* Cluster de serviço, as funções que, como um cluster de back-end, oferecendo elevada disponibilidade e minimizar quaisquer efeitos adversos que possam ocorrer se um HDD ou SSD falha ou for colocado offline

Apenas um controlador é ativo em qualquer ponto no tempo. Se o controlador ativo falhar, o segundo controlador é ativo automaticamente.

Para obter mais informações, aceda a [StorSimple componentes de hardware e o estado](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Pode utilizar o StorSimple para criar uma aplicação da cloud, que replica a arquitetura e as capacidades do dispositivo de armazenamento híbrido físico. A StorSimple Cloud Appliance (também conhecido como o StorSimple Virtual Appliance) é executado num único nó numa máquina virtual do Azure. (Uma aplicação da cloud só é possível criar uma máquina virtual do Azure. É possível criar um num dispositivo StorSimple ou um servidor no local.)

A aplicação da cloud tem as seguintes funcionalidades:

* Ele se comporte como uma aplicação física e pode oferecer uma interface de iSCSI para máquinas virtuais na cloud.
* Pode criar um número ilimitado de aplicações na cloud na cloud e transformá-los e desativar o conforme necessário.
* Ele pode ajudar a simular ambientes no local em cenários de teste, desenvolvimento e recuperação após desastre e pode ajudar na obtenção ao nível do item a partir de cópias de segurança.

A StorSimple Cloud Appliance está disponível em dois modelos: o dispositivo 8010 (anteriormente conhecido como o modelo de 1100) e o dispositivo 8020. O dispositivo 8010 tem a capacidade máxima de 30 TB. O dispositivo 8020, o que tira partido do armazenamento premium do Azure, tem a capacidade máxima de 64 TB. (Em camadas locais, o armazenamento premium do Azure armazena dados em SSDs, ao passo que o armazenamento standard armazena dados em HDDs.) Tenha em atenção que tem de ter uma conta de armazenamento premium do Azure para utilizar o armazenamento premium.

Para obter mais informações sobre o StorSimple Cloud Appliance, aceda a [implementar e gerir uma StorSimple Cloud Appliance no Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Serviço de Gestor de Dispositivos do StorSimple
O Microsoft Azure StorSimple fornece uma interface do usuário baseada na web (o serviço StorSimple Device Manager) que permite-lhe gerir o Centro de dados e armazenamento na cloud de forma centralizada. Pode utilizar o serviço StorSimple Device Manager para efetuar as seguintes tarefas:

* Configure definições de sistema para dispositivos do StorSimple.
* Configurar e gerir as definições de segurança para dispositivos do StorSimple.
* Configure as credenciais da cloud e propriedades.
* Configurar e gerir volumes num servidor.
* Configure grupos de volumes.
* Criar cópias de segurança e restaurar dados.
* Monitorizar o desempenho.
* Reveja as definições do sistema e identificar possíveis problemas.

Pode utilizar o serviço StorSimple Device Manager para executar todas as tarefas de administração, exceto aqueles que necessitam de sistema de tempo, como a configuração inicial e a instalação de atualizações.

Para obter mais informações, aceda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple
Windows PowerShell para StorSimple fornece uma interface de linha de comandos que pode utilizar para criar e gerir o serviço do Microsoft Azure StorSimple e configurar e monitorizar dispositivos StorSimple. É uma interface de linha de comandos baseados no Windows PowerShell, que inclui cmdlets dedicados para gerir o dispositivo StorSimple. Windows PowerShell para StorSimple tem recursos que permitem-lhe:

* Registe um dispositivo.
* Configure a interface de rede num dispositivo.
* Instale determinados tipos de atualizações.
* Resolver problemas relacionados com o seu dispositivo ao aceder a sessão de suporte.
* Altere o estado do dispositivo.

Pode aceder Windows PowerShell para StorSimple a partir de uma consola de série (num computador anfitrião ligado diretamente ao dispositivo) ou remotamente através da utilização de comunicação remota do Windows PowerShell. Tenha em atenção que o Windows PowerShell para StorSimple de tarefas, como o registo de dispositivos inicial, só pode ser efetuado na consola de série.

Para obter mais informações, aceda a [utilize o Windows PowerShell para StorSimple administrar o seu dispositivo](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Cmdlets do PowerShell StorSimple do Azure
Os cmdlets do Azure PowerShell StorSimple são uma coleção de cmdlets do Windows PowerShell que permitem-lhe automatizar tarefas de migração e de nível de serviço a partir da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para o [referência de cmdlet](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>Snapshot Manager do StorSimple
Snapshot Manager do StorSimple é um snap-in da consola de gestão da Microsoft (MMC), que pode utilizar para criar consistente, cópias de segurança de ponto no tempo de local e dados na cloud. O snap-in é executado num anfitrião baseado no Windows Server. Pode utilizar o Snapshot Manager do StorSimple para:

* Configurar, criar cópias de segurança e elimine volumes.
* Configurar grupos de volumes para se certificar de que a segurança dos dados é consistente com a aplicação.
* Gerir políticas de cópia de segurança, de modo a que os dados são uma cópia de segurança com base numa agenda predeterminada e armazenados num local designado (localmente ou na cloud).
* Restaure volumes e ficheiros individuais.

As cópias de segurança são capturadas como instantâneos, o que gravar apenas as alterações, uma vez que o último instantâneo e exigem muito menos espaço de armazenamento de cópias de segurança completas. Pode criar agendas de cópia de segurança ou efetuar cópias de segurança de imediato, conforme necessário. Além disso, pode utilizar Snapshot Manager do StorSimple para estabelecer políticas de retenção que controlam o número de instantâneos serão guardados. Se pretender restaurar dados a partir de uma cópia de segurança, permite do Snapshot Manager do StorSimple, em seguida, selecione o catálogo do local ou instantâneos de cloud. 

Se ocorrer um desastre ou se precisar de restaurar dados para outro motivo, o Snapshot Manager do StorSimple restaurá-lo incrementalmente conforme necessário. Restauro de dados não requer que encerrada todo o sistema durante a restaurar um ficheiro, substitua o equipamento ou mover as operações para outro site.

Para obter mais informações, aceda a [o que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adaptador do StorSimple para o SharePoint
O Microsoft Azure StorSimple inclui o adaptador do StorSimple para SharePoint, um componente opcional que expande forma transparente a funcionalidades de proteção de dados e de armazenamento StorSimple para farms de servidores SharePoint. O adaptador funciona com um fornecedor de armazenamento de BLOBs remoto (RBS) e a funcionalidade de RBS do SQL Server, permitindo-lhe mover BLOBs para um servidor de cópia de segurança do sistema do Microsoft Azure StorSimple. O Microsoft Azure StorSimple, em seguida, armazena os dados de BLOBS localmente ou na cloud, com base na utilização.

O StorSimple Adapter para SharePoint é gerido a partir do portal de Administração Central do SharePoint. Conseqüentemente, gerenciamento do SharePoint permanece centralizado e todo o armazenamento parece estar num farm do SharePoint.

Para obter mais informações, aceda a [StorSimple Adapter para SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gerenciamento de armazenamento
Além do dispositivo StorSimple dedicado, o dispositivo virtual e outros componentes, o Microsoft Azure StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido aos dados e para reduzir o consumo de armazenamento:

* [A criação de camadas de armazenamento automática](#automatic-storage-tiering) 
* [Aprovisionamento dinâmico](#thin-provisioning) 
* [A eliminação de duplicados e a compressão](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>A criação de camadas de armazenamento automática
O Microsoft Azure StorSimple automaticamente organiza os dados em camadas lógicas com base na utilização atual, a idade e a relação a outros dados. Dados que é mais ativos são armazenados localmente, enquanto os dados menos ativos e inativos é migrados automaticamente para a cloud. O diagrama seguinte ilustra essa abordagem de armazenamento.

![Camadas de armazenamento do StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para ativar o acesso rápido, StorSimple armazena os dados muito ativos (dados de acesso frequente) SSDs no dispositivo StorSimple. Armazena os dados que são utilizados ocasionalmente (entender dados) em HDDs no dispositivo ou em servidores no datacenter. Move os dados inativos, dados de cópia de segurança, e os dados retidos para arquivamento ou fins de compatibilidade para a cloud. 

> [!NOTE]
> Na atualização 2 ou posterior, pode especificar um volume afixado localmente como, caso em que os dados permanecem no dispositivo local e não está em camadas para a cloud. 


StorSimple ajusta e reorganiza a dados e alterar as atribuições de armazenamento como padrões de utilização. Por exemplo, algumas informações podem tornar-se menos ativas ao longo do tempo. Como ele se torna cada vez menos ativo,-é migrado do SSDs em HDDs e, em seguida, para a cloud. Se esses mesmos dados é novamente ativos, é migrada para o dispositivo de armazenamento.

O processo de camadas de armazenamento ocorre da seguinte forma:

1. Um administrador de sistema configura uma conta de armazenamento na cloud do Microsoft Azure.
2. O administrador utiliza a consola de série e o serviço StorSimple Device Manager (em execução no portal do Azure) para configurar o servidor de ficheiro e de dispositivo, criar políticas de proteção de volumes e dados. As máquinas no local (por exemplo, servidores de ficheiros) utilizam o Internet Small Computer System Interface (iSCSI) para aceder ao dispositivo StorSimple.
3. Inicialmente, o StorSimple armazena dados na camada SSD rápida do dispositivo.
4. À medida que a camada SSD aproxima capacidade, StorSimple elimina duplicados e comprime os blocos de dados mais antigos e movê-los na camada HDD.
5. Como a capacidade de abordagens de camada HDD, o StorSimple encripta os blocos de dados mais antigos e envia-os de forma segura para a conta de armazenamento do Microsoft Azure através de HTTPS.
6. Microsoft Azure cria várias réplicas dos dados no seu datacenter e num datacenter remoto, garantindo que os dados podem ser recuperados se ocorrer um desastre.
7. Quando o servidor de ficheiros solicita dados armazenados na cloud, o StorSimple devolve-lo de forma totalmente integrada e armazena uma cópia na camada SSD do dispositivo StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Como o StorSimple gere os dados na cloud

StorSimple elimina duplicados dados do cliente em todos os instantâneos e os dados primários (dados escritos por anfitriões). Embora a eliminação de duplicados seja ótima para uma eficiência de armazenamento, faz a pergunta "o que é na nuvem" complicado. Os dados primários em camadas e os dados de instantâneo sobrepor-se entre si. Um único segmento de dados na cloud poderia ser usado como dados primários em camadas e também de ser referenciado por vários instantâneos. Cada instantâneo de cloud garante que uma cópia de todos os dados de ponto no tempo está bloqueada para a cloud até que seja eliminado nesse instantâneo.

Dados são eliminados apenas a partir da cloud quando não há referências a esses dados. Por exemplo, se podemos tirar um instantâneo de cloud de todos os dados que está no dispositivo StorSimple e, em seguida, elimine alguns dados primários, veremos os _dados primários_ drop imediatamente. O _os dados da cloud_ que inclui os dados em camadas e as cópias de segurança, permanece o mesmo. Isto acontece porque existe um instantâneo ainda referenciar os dados na cloud. Depois da cloud o instantâneo é eliminado (e quaisquer outro instantâneo referenciado os mesmos dados), quedas de consumo da cloud. Antes de removermos dados na cloud, verificamos se não existem instantâneos ainda fazem referência a esses dados. Este processo é denominado _coleta de lixo_ e é um serviço em segundo plano em execução no dispositivo. Remoção de dados na cloud não é imediata, como o serviço de recolha de lixo verifica a existência de outras referências a esses dados antes da eliminação. A velocidade da coleta de lixo depende o número total de instantâneos e o total de dados. Normalmente, os dados da cloud são removidos em menos de uma semana.


### <a name="thin-provisioning"></a>Aprovisionamento dinâmico
Aprovisionamento dinâmico é uma tecnologia de Virtualização na qual armazenamento disponível parece exceder os recursos físicos. Em vez de reservar capacidade de armazenamento suficiente com antecedência, StorSimple utiliza o aprovisionamento dinâmico para alocar espaço apenas suficiente para satisfazer os requisitos atuais. A natureza flexível do armazenamento na cloud facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento na cloud para atender às necessidades em mudança.

> [!NOTE]
> Os volumes afixados localmente não são com aprovisionamento dinâmico. Armazenamento atribuído a um volume somente local é aprovisionado em sua totalidade quando o volume é criado.


### <a name="deduplication-and-compression"></a>A eliminação de duplicados e a compressão
O Microsoft Azure StorSimple utiliza a compactação de dados e de eliminação de duplicados para reduzir ainda mais os requisitos de armazenamento.

A eliminação de duplicados reduz a quantidade global dos dados armazenados ao eliminar a redundância no conjunto de dados armazenado. Que informações é alterado, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações desnecessárias. 

> [!NOTE]
> Dados em volumes localmente afixados não é eliminação de duplicados ou comprimidos. No entanto, as cópias de segurança de volumes localmente afixados são de eliminação de duplicados e comprimidas.


## <a name="storsimple-workload-summary"></a>Resumida da carga de trabalho do StorSimple
Um resumo das cargas de trabalho suportadas do StorSimple é apresentado abaixo.

| Cenário | Carga de trabalho | Suportadas | Restrições | Version |
| --- | --- | --- | --- | --- |
| Colaboração |Partilha de ficheiros |Sim | |Todas as versões |
| Colaboração |Partilha de ficheiros distribuído |Sim | |Todas as versões |
| Colaboração |SharePoint |Sim* |Suportado apenas com os volumes afixados localmente |Atualização 2 e posterior |
| Arquivamento |Arquivamento de ficheiro Simple |Sim | |Todas as versões |
| Virtualização |Máquinas virtuais |Sim* |Suportado apenas com os volumes afixados localmente |Atualização 2 e posterior |
| Base de Dados |SQL |Sim* |Suportado apenas com os volumes afixados localmente |Atualização 2 e posterior |
| Vigilância de vídeo |Vigilância de vídeo |Sim* |Suportada quando o dispositivo StorSimple dedica-se apenas a esta carga de trabalho |Atualização 2 e posterior |
| Cópia de segurança |Cópia de segurança de destino principal |Sim* |Suportada quando o dispositivo StorSimple dedica-se apenas a esta carga de trabalho |Atualização 3 e posterior |
| Cópia de segurança |Cópia de segurança de destino secundária |Sim* |Suportada quando o dispositivo StorSimple dedica-se apenas a esta carga de trabalho |Atualização 3 e posterior |

*Sim&#42; -diretrizes de solução e restrições devem ser aplicadas.*

As cargas de trabalho seguintes não são suportadas por dispositivos da série StorSimple 8000. Se implementada no StorSimple, estas cargas de trabalho irão resultar numa configuração não suportada.

* Geração de imagens médica
* Troca
* VDI
* Oracle
* SAP
* Macrodados
* Distribuição de conteúdo
* Arranque a partir da SCSI

Segue-se uma lista dos componentes de infraestrutura de suporte do StorSimple.

| Cenário | Carga de trabalho | Suportadas | Restrições | Version |
| --- | --- | --- | --- | --- |
| Geral |ExpressRoute |Sim | |Todas as versões |
| Geral |DataCore FC |Sim* |Suportado com DataCore SANsymphony |Todas as versões |
| Geral |DFSR |Sim* |Suportado apenas com os volumes afixados localmente |Todas as versões |
| Geral |Indexação |Sim* |Para volumes em camadas, a indexação de metadados apenas é suportada (sem dados).<br>Para volumes afixados localmente, a indexação completa é suportada. |Todas as versões |
| Geral |Software antivírus |Sim* |A volumes em camadas, é suportada apenas análise em abrir e fechar.<br> Para volumes afixados localmente, a análise completa é suportada. |Todas as versões |

*Sim&#42; -diretrizes de solução e restrições devem ser aplicadas.*

Segue-se uma lista de outro software que são utilizados com o StorSimple para criar soluções.

| Tipo de carga de trabalho | Software utilizado com o StorSimple | Versões suportadas|Ligar a guia de soluções| 
| --- | --- | --- | --- |
| Destino de cópia de segurança |Veeam |Veeam v 9 e posterior |[StorSimple como um destino de cópia de segurança com Veaam](storsimple-configure-backup-target-veeam.md)|
| Destino de cópia de segurança |Exec de cópia de segurança VERITAS |Exec de cópia de segurança 16 e posterior |[StorSimple como um destino de cópia de segurança com Exec de cópia de segurança](storsimple-configure-backup-target-using-backup-exec.md)|
| Destino de cópia de segurança |VERITAS NetBackup |NetBackup 7.7.x e posterior  |[StorSimple como um destino de cópia de segurança com NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Compartilhamento de arquivos global <br></br> Colaboração |Talon  |[StorSimple com Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologia do StorSimple
Antes de implementar a solução Microsoft Azure StorSimple, recomendamos que reveja os seguintes termos e definições.

### <a name="key-terms-and-definitions"></a>Principais termos e definições
| Termo (acrônimo ou abreviatura) | Descrição |
| --- | --- |
| registo de controlo de acesso (ACR) |Um registo associado a um volume no dispositivo do Microsoft Azure StorSimple que determina os anfitriões que podem se conectar a ele. A determinação baseia-se no iSCSI nome qualificado (IQN) de anfitriões (contidos no ACR), que estão a ligar o dispositivo StorSimple. |
| AES-256 |Um algoritmo de encriptação AES (Advanced Standard) de 256 bits para criptografar dados, pois ela se move de e para a cloud. |
| tamanho da unidade de alocação (UA) |Sistemas de ficheiros a menor quantidade de espaço em disco que pode ser alocado para armazenar um ficheiro no seu Windows. Se um tamanho de ficheiro não for um mesmo múltiplo do tamanho do cluster, o espaço extra deve ser usado para armazenar o ficheiro (até o próximo múltiplo do tamanho do cluster) resulta em espaço perdido e a fragmentação do disco rígido. <br>Os AUS recomendados para volumes do StorSimple do Azure é 64 KB, pois funciona bem com os algoritmos de eliminação de duplicados. |
| a criação de camadas de armazenamento automática |Mover automaticamente dados menos ativos do SSDs, HDDs e, em seguida, para um escalão na cloud e, em seguida, ativar a gestão de todo o armazenamento de uma interface do usuário central. |
| catálogo de cópia de segurança |Uma coleção de cópias de segurança, normalmente, relacionados pelo tipo de aplicação que foi utilizado. Esta coleção é apresentada no painel do catálogo de cópia de segurança do serviço StorSimple Device Manager da interface do Usuário. |
| arquivo de catálogo de cópia de segurança |Um ficheiro que contém uma lista de instantâneos disponíveis atualmente armazenados na base de dados cópia de segurança do Snapshot Manager do StorSimple. |
| política de cópia de segurança |Uma seleção de volumes, tipo de cópia de segurança e uma Cronograma que permite-lhe criar cópias de segurança num agendamento predefinido. |
| objetos binários grandes (BLOBs) |Uma coleção de dados binários armazenados como uma única entidade num sistema de gestão de base de dados. Os bLOBs são, normalmente, imagens, áudio ou outros objetos de multimídia, embora o código executável binário, às vezes, é armazenado como um BLOB. |
| Protocolo de autenticação de Handshake de desafio (CHAP) |Um protocolo utilizado para autenticar o elemento de rede de uma ligação, com base no ponto de partilha uma palavra-passe ou um segredo. CHAP pode ser unidirecional ou bidirecional. Com CHAP unidirecional, o destino autentica um iniciador. CHAP mútua requer que o destino autenticar o iniciador e que o iniciador autentica o destino. |
| Clone |Uma cópia duplicada de um volume. |
| Na nuvem como uma camada (CaaT) |Integrado como uma camada na arquitetura de armazenamento para que todo o armazenamento aparece para fazer parte da rede de armazenamento de uma empresa de armazenamento da cloud. |
| fornecedor de serviços cloud (CSP) |Um fornecedor de serviços de informática na cloud. |
| instantâneo de cloud |Uma cópia de ponto no tempo dos dados de volume que são armazenados na cloud. Um instantâneo de cloud é equivalente a um instantâneo replicado num sistema de armazenamento diferentes, fora das instalações. Instantâneos de cloud são particularmente úteis em cenários de recuperação após desastre. |
| chave de encriptação de armazenamento na nuvem |Uma palavra-passe ou uma chave usada pelo seu dispositivo StorSimple para acessar os dados criptografados enviados pelo seu dispositivo para a cloud. |
| atualização com suporte para o cluster |Gerir atualizações de software em servidores no cluster de ativação pós-falha, para que as atualizações de tem um mínimo ou nenhum efeito sobre a disponibilidade do serviço. |
| DataPath |Uma coleção de unidades funcionais que realizam operações de processamento de dados entre ligados. |
| desativar |Uma ação permanente interrompe a ligação entre o dispositivo StorSimple e o serviço de nuvem associada. Instantâneos de cloud do dispositivo permanecem após este processo e podem ser clonados ou utilizados para recuperação após desastre. |
| espelhamento de disco |Unidades de replicação de volumes de disco lógicas num disco rígido de diferente em tempo real para garantir a disponibilidade contínua. |
| espelhamento de disco dinâmico |Replicação de volumes de disco lógico em discos dinâmicos. |
| discos dinâmicos |Um formato de volume de disco que utiliza o Gestor de discos lógicos (LDM) para armazenar e gerir os dados em vários discos físicos. Discos dinâmicos podem ser aumentados, para fornecer mais espaço livre. |
| Bastidor expandida de monte de discos (EBOD) |Um bastidor secundário do seu dispositivo do Microsoft Azure StorSimple que contém discos de disco rígido extra para armazenamento adicional. |
| FAT de aprovisionamento |Um convencional aprovisionamento de armazenamento no qual armazenamento espaço é alocado com base em previsto necessidades (e está, normalmente, além da necessidade atual). Consulte também *aprovisionamento dinâmico*. |
| unidade de disco rígido (HDD) |Uma unidade que utiliza platters rotação para armazenar dados. |
| armazenamento na cloud híbrida |Uma arquitetura de armazenamento que utiliza recursos locais e fora do local, incluindo o armazenamento na cloud. |
| Internet Small Computer System Interface (iSCSI) |Um padrão de rede com base no IP de protocolo de Internet de armazenamento para ligar equipamento de armazenamento de dados ou recursos. |
| iniciador iSCSI |Um componente de software que permite que o computador anfitrião com o Windows para ligar a uma rede de armazenamento baseado em iSCSI externo. |
| iSCSI nome qualificado (IQN) |Um nome exclusivo que identifica um destino iSCSI ou um iniciador. |
| destino iSCSI |Um componente de software que oferece iSCSI centralizado subsistemas de disco em redes de área de armazenamento. |
| Live arquivamento |Uma abordagem de armazenamento em que dados de arquivo são acessíveis o tempo todo (não são armazenadas fora do local em banda, por exemplo). O Microsoft Azure StorSimple utiliza o arquivamento em direto. |
| volume afixado localmente |um volume que reside no dispositivo e que nunca é em camadas para a cloud. |
| instantâneo local |Uma cópia de ponto no tempo dos dados de volume armazenados no dispositivo StorSimple do Microsoft Azure. |
| Microsoft Azure StorSimple |Uma solução poderosa que consiste num dispositivo de armazenamento do Centro de dados e o software que permite que as organizações de TI tirar partido do armazenamento na cloud como se fosse o armazenamento do Centro de dados. StorSimple simplifica o gerenciamento de dados e proteção de dados, reduzindo os custos. A solução consolida primária armazenamento, arquivo, cópia de segurança e recuperação após desastre (DR) através da integração suave com a cloud. Ao combinar a gestão de dados de armazenamento e na cloud SAN numa plataforma de classe empresarial, os dispositivos do StorSimple ativar velocidade, a simplicidade e confiabilidade para todas as necessidades relacionadas com o armazenamento. |
| Energia e refrigeração módulo (PCM) |Componentes de hardware do dispositivo StorSimple consistindo as fontes de alimentação e refrigeração ventoinha, por conseguinte, o nome de energia e refrigeração módulo. O bastidor principal do dispositivo tem dois 764W PCMs, ao passo que o bastidor EBOD tem dois 580W PCMs. |
| Bastidor principal |Bastidor principal do dispositivo StorSimple que contém os controladores de plataforma do aplicativo. |
| Objetivo de tempo de recuperação (RTO) |A quantidade máxima de tempo que deve ser diz antes de um processo comercial ou o sistema está totalmente restaurada após um desastre. |
| série attached SCSI (SAS) |Um tipo de unidade de disco rígido (HDD). |
| chave de encriptação de dados do serviço |Uma chave disponibilizada para qualquer novo dispositivo do StorSimple que regista com o serviço StorSimple Device Manager. Os dados de configuração transferidos entre o serviço StorSimple Device Manager e o dispositivo são encriptados com uma chave pública e, em seguida, podem ser desencriptados apenas no dispositivo com uma chave privada. Chave de encriptação de dados do serviço permite que o serviço obter esta chave privada para desencriptação. |
| chave de registo do serviço |Uma chave que ajuda a registar o dispositivo StorSimple com o serviço StorSimple Device Manager para que seja apresentado no portal do Azure para obter mais ações de gestão. |
| Small Computer System Interface (SCSI) |Um conjunto de padrões para a ligação de computadores e transmitindo dados entre-los fisicamente. |
| unidade de estado sólido (SSD) |Um disco que contém não partes móveis; Por exemplo, uma unidade flash. |
| conta de armazenamento |Um conjunto de credenciais de acesso ligada à sua conta de armazenamento para fornecedor de serviços cloud determinado. |
| Adaptador do StorSimple para o SharePoint |Um componente do Microsoft Azure StorSimple que expande forma transparente a proteção de armazenamento e dados do StorSimple para farms de servidores SharePoint. |
| Serviço de Gestor de Dispositivos do StorSimple |Uma extensão do portal do Azure permite-lhe gerir o Azure StorSimple no local e os dispositivos virtuais. |
| Snapshot Manager do StorSimple |Snap-in uma consola de gestão da Microsoft (MMC) para o gerenciamento de operações de cópia de segurança e restauro no Microsoft Azure StorSimple. |
| Efetuar cópia de segurança |Uma funcionalidade que permite que o usuário faça uma cópia de segurança interativa de um volume. É uma maneira alternativa de fazer uma cópia de segurança manual de um volume em vez de fazer uma cópia de segurança automatizada por meio de uma política definida. |
| Aprovisionamento dinâmico |Um método de otimizar a eficiência com que o espaço de armazenamento disponível é utilizado em sistemas de armazenamento. Aprovisionamento dinâmico, o armazenamento podem é alocado entre vários usuários com base no espaço de mínimo necessário para cada utilizador em qualquer momento. Consulte também *aprovisionamento fat*. |
| disposição em camadas |Organizar dados em agrupamentos lógicos com base na utilização atual, a idade e a relação a outros dados. StorSimple automaticamente organiza os dados em camadas. |
| volume |Áreas de armazenamento lógico apresentadas na forma de unidades. Volumes do StorSimple correspondem aos volumes montados pelo host, incluindo as descobertas por meio de iSCSI e um dispositivo StorSimple. |
| contentor de volumes |Um agrupamento de volumes e as definições que se aplicam aos mesmos. Todos os volumes no dispositivo StorSimple são agrupados em contentores de volumes. Definições de contentor de volume incluem as contas de armazenamento, definições de encriptação de dados enviados para a cloud com as chaves de encriptação associados e largura de banda consumida para operações que envolvem a cloud. |
| grupo de volumes |No Snapshot Manager do StorSimple, um grupo de volume é uma coleção de volumes configurados para facilitar o processamento de cópia de segurança. |
| Serviço de cópia de sombra de volumes (VSS) |Um serviço de sistema operativo Windows Server, que facilita a consistência de aplicação através da comunicação com aplicativos que reconhecem VSS para coordenar a criação de instantâneos incrementais. VSS garante que as aplicações estão temporariamente Inativas, quando são tirados instantâneos. |
| Windows PowerShell para StorSimple |Uma interface de linha de comandos com base no Windows PowerShell utilizada para operar e gerir o dispositivo StorSimple. Mantendo a alguns recursos básicos do Windows PowerShell, essa interface possui cmdlets dedicados adicionais que são direcionados para gerir um dispositivo StorSimple. |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [StorSimple segurança](storsimple-8000-security.md).

