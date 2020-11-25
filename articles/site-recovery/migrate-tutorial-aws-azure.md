---
title: Migrar VMS AWS para Azure com Azure Migrate
description: Este artigo descreve opções para migrar instâncias AWS para Azure, e recomenda Azure Migrate.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009063"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar VMs do Amazon Web Services (AWS) para o Azure

Este artigo descreve opções para migrar casos da Amazon Web Services (AWS) para Azure.

## <a name="migrate-with-azure-migrate"></a>Migrar com Azure Migrar

Recomendamos que emigre as instâncias AWS EC2 para Azure utilizando o serviço [Azure Migrate.](../migrate/migrate-services-overview.md) Azure Migrate é construído de propósito para a migração de servidores. Azure Migrate fornece um centro centralizado para a descoberta, avaliação e migração de máquinas no local para Azure.

[Aprenda](../migrate/tutorial-migrate-aws-virtual-machines.md) a migrar casos AWS com Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrar com recuperação do local

A recuperação do local deve ser usada apenas para a recuperação de desastres, e não para a migração.

Se já estiver a utilizar a Recuperação do Sítio Azure e pretender continuar a usá-lo para a migração da AWS, siga os mesmos passos que utiliza para configurar a [recuperação de desastres de máquinas físicas.](physical-azure-disaster-recovery.md)


> [!NOTE]
> Quando se corre um fracasso para a recuperação de desastres, como último passo, comete-se o fracasso. Quando migra casos AWS, a opção **Commit** não é relevante. Em vez disso, selecione a opção **Migração Completa.** 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja questões comuns](../migrate/resources-faq.md) sobre Azure Migrate.
