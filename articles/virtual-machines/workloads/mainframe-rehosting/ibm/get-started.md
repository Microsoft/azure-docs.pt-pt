---
title: Cargas de trabalho IBM no Azure | Microsoft Docs
description: Use um emulador de mainframe e outros serviços de parceiros da Microsoft para hospedar novamente suas cargas de trabalho do IBM z/OS usando Microsoft Azure.
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
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834579"
---
# <a name="ibm-workloads-on-azure"></a>Cargas de trabalho IBM no Azure

Muitas cargas de trabalho de mainframe IBM baseadas em z/OS podem ser replicadas no Azure sem perda de funcionalidade e sem que os usuários percebam alterações em seus sistemas subjacentes. A hospedagem de aplicativos no Azure oferece os recursos do tipo mainframe de que você precisa, além da elasticidade, da disponibilidade e da economia de custo potencial da nuvem.

O Azure dá suporte à integração com ambientes de mainframe IBM existentes, permitindo que você migre os applicates que fazem sentido, execute soluções híbridas quando necessário e migre ao longo do tempo. Embora você possa reescrever completamente os programas existentes baseados em mainframe para o Azure, é mais comum rehospedá-los. Reescrever adiciona custo, complexidade e tempo para projetos de migração. Com a rehospedagem, você pode:

- Mova aplicativos para um emulador baseado em nuvem.

- Migre o banco de dados para um banco de dados baseado em nuvem.

- Substitua os módulos e o código usando mecanismos de transformação de código.

Além disso, o software IBM, incluindo o WebSphere e o MQ, agora está no Azure Marketplace. Com uma licença para software IBM, pode tirar partido do dimensionamento de infraestrutura a pedido fornecido pelo Azure para começar uma máquina virtual rapidamente.

Um amplo ecossistema de parceiros está disponível para ajudá-lo a migrar os sistemas de mainframe IBM para o Azure. A maioria segue uma abordagem pragmática de reutilização sempre que possível antes de se embarcar em uma implantação em fases de regravação ou substituição de aplicativos. Obtenha mais orientações e ajuda de parceiros no [centro de migração de mainframe do Azure](https://azure.microsoft.com/migration/mainframe/).

**Passos seguintes?**

- [Migração de mainframe: mitos e fatos](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Instalar o ambiente de desenvolvimento/teste do IBM zD & T no Azure](./install-ibm-z-environment.md)
- [Configurar uma ADCD (distribuição controlada por desenvolvedores de aplicativos) no IBM zD & T v1](./demo.md)
- [IBM DB2 pureScale no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
