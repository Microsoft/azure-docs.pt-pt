---
title: Cargas de trabalho da IBM no Azure Microsoft Docs
description: Utilize um emulador principal e outros serviços de parceiros da Microsoft para realojar as suas cargas de trabalho IBM z/OS utilizando o Microsoft Azure.
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
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68834579"
---
# <a name="ibm-workloads-on-azure"></a>Cargas de trabalho da IBM no Azure

Muitas cargas de trabalho do mainframe da IBM baseadas no z/OS podem ser replicadas no Azure sem perda de funcionalidade e sem que os utilizadores percebam alterações nos seus sistemas subjacentes. Realojar aplicações no Azure dá-lhe as funcionalidades semelhantes ao mainframe que necessita, além da elasticidade, disponibilidade e potencial poupança de custos da nuvem.

O Azure apoia a integração com os ambientes principais da IBM existentes, permitindo-lhe migrar os aplicadores que fazem sentido, executar soluções híbridas sempre que necessário, e migrar ao longo do tempo. Embora possa reescrever completamente os programas baseados no mainframe existentes para o Azure, é mais comum reacolhê-los. A reescrita adiciona custo, complexidade e tempo aos projetos de migração. Com realojamento, pode:

- Mova aplicações para um emulador baseado em nuvem.

- Migrar a base de dados para uma base de dados baseada em nuvem.

- Substitua os módulos e o código utilizando motores de transformação de códigos.

Além disso, o software IBM, incluindo WebSphere e MQ, está agora no Azure Marketplace. Com uma licença para software IBM, pode aproveitar a escala de infraestrutura a pedido fornecida pelo Azure para iniciar rapidamente uma máquina virtual.

Um vasto ecossistema de parceiros está disponível para ajudá-lo a migrar os sistemas mainframe da IBM para o Azure. A maioria segue uma abordagem pragmática de reutilização sempre que possível antes de embarcar numa implementação faseada de reescrita ou substituição de aplicações. Obtenha mais orientação e ajuda de parceiros no [centro de migração Azure Mainframe](https://azure.microsoft.com/migration/mainframe/).

**Passos seguintes**

- [Migração mainframe: mitos e factos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instale ibm zD&T dev/test environment no Azure](./install-ibm-z-environment.md)
- [Criar uma distribuição controlada por desenvolvedores de aplicações (ADCD) em IBM zD&T v1](./demo.md)
- [IBM DB2 pureScale on Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
