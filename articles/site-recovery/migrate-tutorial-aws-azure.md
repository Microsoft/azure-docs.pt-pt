---
title: Migrar VMs do AWS para o Azure com o Azure com o serviço Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar VMs do Windows em execução no Amazon Web Services (AWS) para o Azure com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281298"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar VMs do Amazon Web Services (AWS) para o Azure

Este artigo descreve opções para migrar casos da Amazon Web Services (AWS) para Azure.

## <a name="migrate-with-azure-migrate"></a>Migrar com Azure Migrar

Recomendamos que emigre as instâncias AWS para Azure utilizando o serviço [Azure Migrate.](../migrate/migrate-services-overview.md) A Azure Migrate fornece um centro centralizado para avaliação e migração de máquinas no local para Azure, utilizando Azure Migrate, outros serviços Azure e ferramentas de terceiros.

[Aprenda](../migrate/tutorial-migrate-aws-virtual-machines.md) a migrar casos AWS com Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrar com recuperação do local

A recuperação do local deve ser usada apenas para a recuperação de desastres, e não para a migração.

Se já estiver a utilizar a Recuperação do Sítio Azure e pretender continuar a usá-lo para a migração da AWS, siga os mesmos passos que utiliza para configurar a [recuperação de desastres de máquinas físicas.](physical-azure-disaster-recovery.md)


> [!NOTE]
> Quando se corre um fracasso para a recuperação de desastres, como último passo, comete-se o fracasso. Quando migra casos AWS, a opção **Commit** não é relevante. Em vez disso, selecione a opção **Migração Completa.** 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja questões comuns](../migrate/resources-faq.md) sobre Azure Migrate.
