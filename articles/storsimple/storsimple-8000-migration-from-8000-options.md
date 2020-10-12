---
title: Opções de migração de dados de dispositivos da série StorSimple 8000
description: Fornece uma visão geral das opções para migrar dados da série StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 78ac7e849a3a8f1742701006638a2cb72299bad7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055780"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opções para migrar dados da série StorSimple 8000

> [!IMPORTANT]
> Em 31 de dezembro de 2022, a série StorSimple 8000 chegará ao fim do estado de suporte (EOS). Recomendamos que os clientes da série StorSimple 8000 migram para uma das alternativas descritas no documento.

A série StorSimple 8000 está a chegar [ao fim do Support](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) em dezembro de 2022. Os clientes que estão a executar a série StorSimple 8000 têm a opção de fazer upgrade para outros serviços híbridos da primeira parte da Azure. Este artigo descreve as opções híbridas Azure disponíveis para migrar dados.

## <a name="migration-options"></a>Opções de migração

Os clientes que usam a série StorSimple 8000 têm opções Azure ou de terceiros.

### <a name="azure-options"></a>Opções do Azure

#### <a name="migrate-to-azure-file-sync"></a>Migrar para o Azure File Sync

Esta nova opção de migração permite que os clientes armazenem as ações de ficheiros da sua organização nos Ficheiros Azure. Estas partilhas de ficheiros são então centralizadas para acesso no local utilizando o Azure File Sync (AFS). O AFS pode ser implantado num anfitrião do Windows Server. A migração de dados real é então realizada como uma cópia hospedeira ou utilizando a ferramenta de migração.

Para obter mais informações sobre como migrar dados para Azure File Sync, vá à [migração StorSimple 8100 e 8600 para Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

### <a name="third-party-options"></a>Opções de terceiros

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrar para Panzura Freedom NAS

StorSimple série 5000-7000 e os clientes da série StorSimple 8000 podem optar por migrar para a Panzura Freedom NAS para manter os seus dados em Azure. A solução Panzura Freedom fornece uma solução NAS que abrange datacenters, escritórios, nuvens públicas e privadas. A solução permite fluxos de trabalho de dados locais, híbridos e em nuvem para clientes NFS, SMB e mobile.

Esta migração é apoiada pela Panzura e os clientes podem começar solicitando apoio à migração a partir do [site panzura.](https://panzura.com/migrate-storsimple-panzura/)

#### <a name="migrate-to-nasuni"></a>Migrar para Nasuni

Mover todo o seu ambiente StorSimple para uma plataforma estável, segura e de alto desempenho de serviços de ficheiros é fácil com a Nasuni. A Nasuni oferece a segurança e o desempenho do armazenamento de ficheiros no local, combinando-o com a escalabilidade e durabilidade do Azure.  Como um fornecedor líder de software independente da Azure (ISV), a Nasuni traz todas as ferramentas necessárias para mover os seus dados StorSimple para uma plataforma moderna que lhe permite partilhar e colaborar com os seus ficheiros em vários locais.

Começa hoje: [site da Nasuni.](https://info.nasuni.com/storsimple8000-webinar)

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migração - Perguntas frequentes

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>P. Quando é que os dispositivos da série StorSimple 8000 chegam ao fim do serviço?

A. Série StorSimple 8000 chega [ao fim do suporte](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) em dezembro de 2022. O fim do suporte implica que a Microsoft deixará de ser capaz de fornecer suporte tanto para hardware como para software destes dispositivos a partir de dezembro de 2022. Recomendamos vivamente que comece a formular um plano para migrar os dados dos seus dispositivos agora.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>P. O que acontece com os dados que guardei em Azure?  

A. Pode continuar a utilizar os dados em Azure assim que os migrar para um serviço mais recente.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>P. O que acontece com os dados que armazenei localmente no meu dispositivo StorSimple?

A. Os dados que se encontrem no dispositivo local podem ser copiados para o serviço mais recente, conforme descrito nos documentos de migração.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>P. O que acontece se eu quiser manter o meu aparelho da série StorSimple 8000?

A. Embora os serviços possam continuar a funcionar, a Microsoft deixará de ser capaz de fornecer suporte a hardware e software. A migração é fortemente recomendada para a continuidade do negócio.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>P. Que opções estão disponíveis para migrar dados de dispositivos da série StorSimple 8000?

A. Dependendo do seu cenário, os utilizadores da série StorSimple 8000 têm as seguintes opções de migração:

* **Migrar para Azure File Sync**: Utilize esta opção quando pretender mudar para o formato nativo do Azure. Pode utilizar o Azure File Sync para uma gestão centralizada de ações de ficheiros.

* **Outras opções**: Pode contactar o Microsoft Support para discutir as opções de migração não listadas aqui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>P. A migração para outras soluções de armazenamento é suportada?

A. Sim. A migração para outras soluções de armazenamento utilizando a cópia hospedeira dos dados é suportada.

### <a name="q-is-migration-supported-by-microsoft"></a>P. A migração é suportada pela Microsoft?

A. Migrar da série 8000 é uma operação totalmente suportada. De facto, a Microsoft recomenda que se chegue ao Suporte antes de iniciar a migração. A migração é atualmente uma operação assistida. Se pretender migrar dados do seu dispositivo da série StorSimple 8000, [contacte o suporte StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>P. Qual é o modelo de preços para a migração para Azure File Sync?

A. Ao utilizar o Azure File Sync, podem ser aplicadas as taxas de subscrição do serviço. Os clientes também terão de pagar os custos de armazenamento em curso. Consulte os [preços da AFS]( https://azure.microsoft.com/pricing/details/storage/files/) para obter uma estimativa.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>P. Quanto tempo demora a concluir uma migração?

A. O tempo para migrar dados depende da quantidade dos dados e da opção de upgrade selecionada.

## <a name="next-steps"></a>Passos seguintes

* [Migrar dados de uma série StorSimple 8000 para Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
