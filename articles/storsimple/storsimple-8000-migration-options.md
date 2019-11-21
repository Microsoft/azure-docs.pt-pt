---
title: Evaluate options to migrate data from StorSimple 5000-7000 series| Microsoft Docs
description: Provides an overview of the options to migrate data from StorSimple 5000-7000 series.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: d51891c076f1784fbddb88bfaed28ac1d889afdc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227770"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Options to migrate data from StorSimple 5000-7000 series 

> [!IMPORTANT]
> On July 9, 2019 the StorSimple 5000/7000 series will reach end of support (EOS) status. We recommend that StorSimple 5000/7000 series customers migrate to one of the alternatives described in the document.

StorSimple 5000-7000 series is reaching [end of Support](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) in July 2019. The customers who are running StorSimple 5000-7000 series have an option to upgrade to other Azure first party hybrid services. This article describes the Azure hybrid options available to migrate data. 

## <a name="migration-options"></a>Migration options

The customers using StorSimple 5000-7000 series have Azure or third-party options.

### <a name="azure-options"></a>Azure options

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade to StorSimple 8000 Series

Upgrade to StorSimple 8000 series and thus continue on the StorSimple platform.  This upgrade path requires customers to replace their 5000-7000 series devices with an 8000 series. The data is migrated from the 5000-7000 series device by using the migration tool. Once the migration is successfully complete, the StorSimple 8000 series devices will continue to tier data to Azure Blob Storage. 

For more information on how to migrate data using a StorSimple 8000 series, go to [Migrate data from StorSimple 5000-7000 series to 8000 series device](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrar para o Azure File Sync

This brand new migration option enables customers to store their organization’s file shares in the Azure Files. These files shares are then centralized for on-premises access using Azure File Sync (AFS). AFS can be deployed on a Windows Server host. The actual data migration is then performed as a host copy or using the migration tool.

For more information on how to migrate data to Azure File Sync, go to [Migrate data from StorSimple 5000-7000 series to Azure File Sync](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Third-party options

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Migration - Frequently asked questions

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>P. When do the StorSimple 5000 and 7000 series devices reach end of service? 

R. StorSimple 5000-7000 series reach [end of service](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) in July 2019. The end of service implies that Microsoft will no longer be able to provide support for both hardware and software of these devices after July 2019. We strongly recommend that you start formulating a plan to migrate the data from your devices now.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>P. What happens to the data I have stored in Azure?  

R. You can continue to use the data in Azure once you migrate it to a newer service. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>P. What happens to the data I have stored locally on my StorSimple device? 

R. The data that is on the local device can be copied to the newer service as described in the migration documents.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>P. What happens if I want to keep my StorSimple 5000/7000 series appliance? 

R. While the services might continue to work, Microsoft will no longer be able to provide hardware and software support. Migration is strongly recommended for business continuity.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>P. What options are available to migrate data from StorSimple 5000-7000 series devices? 

R. Depending on their scenario, StorSimple 5000-7000 series users have the following migration options. 

 - **Upgrade to 8000 series**: Use this option when you want to continue on StorSimple platform. 
 - **Migrate to Azure File Sync**: Use this option when you want to switch to Azure native format. You can use Azure File Sync for centralized management of file shares. 

You can contact Microsoft Support to discuss migration options not listed here.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>P. Is migration to other storage solutions supported?

R. Sim. Migration to other storage solutions using host copy of the data is supported.

### <a name="q-is-migration-supported-by-microsoft"></a>P. Is migration supported by Microsoft? 

R. Migrating from 5000 or 7000 series is a fully supported operation. In fact, Microsoft recommends reaching out to Support before you start migration. Migration is currently an assisted operation. If you intend to migrate data from your StorSimple 5000-7000 series device, [Open a Support ticket](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>P. What is the pricing model for both the migration options?

R. Cost of migration varies depending on the option you choose. While migration itself is free, if you decide to upgrade to a StorSimple 8000 series, there will be the cost of the hardware device. 

Similarly, when using Azure File Sync, the subscription fees for the service may apply. In each case, customers will also have to pay ongoing storage costs. Refer to the following for an estimate: 
- [StorSimple pricing](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS pricing]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>P.  How long does it take to complete a migration?

R. The time to migrate data depends on the amount of the data and the upgrade option selected. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>P. What is the End of Support date for StorSimple 8000 series?

R. The End of Support date for StorSimple 8000 series is published [here](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Passos seguintes
 - [Migrate data from a StorSimple 5000-7000 series to an 8000 series device](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrate data from a StorSimple 5000-7000 series to Azure File Sync](storsimple-5000-7000-afs-migration.md)
