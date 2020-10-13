---
title: Cargas de trabalho da IBM no Azure Microsoft Docs
description: Utilize um emulador de computador principal e outros serviços de parceiros da Microsoft para reencaminhar as suas cargas de trabalho IBM z/OS utilizando o Microsoft Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: e99685b4d1476ee527b9eb7f1332f6a6beece669
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499977"
---
# <a name="ibm-workloads-on-azure"></a>Cargas de trabalho da IBM em Azure

Muitas cargas de trabalho do computador principal da IBM baseadas em z/OS podem ser replicadas no Azure sem perda de funcionalidade e sem que os utilizadores percebam alterações nos seus sistemas subjacentes. A rehosagem de aplicações no Azure dá-lhe as funcionalidades semelhantes ao mainframe que precisa, além da elasticidade, disponibilidade e potencial poupança de custos da nuvem.

O Azure apoia a integração com os ambientes principais da IBM existentes, permitindo-lhe migrar os aplicações que fazem sentido, executar soluções híbridas sempre que necessário, e migrar ao longo do tempo. Embora possa reescrever completamente os programas baseados no mainframe para o Azure, é mais comum reenca encontrá-los. A reescrita adiciona custos, complexidade e tempo aos projetos de migração. Com a rehosing, pode:

- Mover aplicações para um emulador baseado na nuvem.

- Migrar a base de dados para uma base de dados baseada em nuvem.

- Substitua os módulos e o código utilizando motores de transformação de código.

Além disso, o software IBM, incluindo WebSphere e MQ, está agora no Azure Marketplace. Com uma licença para software IBM, você pode aproveitar a escala de infraestrutura a pedido fornecida pela Azure para iniciar rapidamente uma máquina virtual.

Um extenso ecossistema parceiro está disponível para ajudá-lo a migrar os sistemas de computadores principais da IBM para Azure. A maioria segue uma abordagem pragmática de reutilização sempre que possível antes de embarcar numa implementação faseada de reescrita ou substituição de aplicações. Obtenha mais orientação e ajuda dos parceiros do [Centro de Migração do Azure Mainframe.](https://azure.microsoft.com/migration/mainframe/)

**Próximos passos**

- [Migração do quadro principal: mitos e factos](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instale o ambiente ibm zD&T dev/test em Azure](./install-ibm-z-environment.md)
- [Configurar uma Distribuição Controlada de Desenvolvedores de Aplicações (ADCD) em IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale em Azure](../../../linux/ibm-db2-purescale-azure.md)
