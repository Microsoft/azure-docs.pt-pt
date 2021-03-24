---
title: Frequência de atualização de software VMware
description: Frequência de atualização de software VMware suportada para Solução VMware Azure.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869868"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

A Microsoft é responsável pela gestão do ciclo de vida do software VMware (ESXi, vCenter, PSC e NXS) na nuvem privada Azure VMware Solution.

O software de nuvem privada é atualizado numa programação que acompanha a versão do pacote de software a partir de VMware. A sua nuvem privada não requer tempo de inatividade para atualizações.

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software lançado a partir de VMware. As versões privadas de software em nuvem podem diferir das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN).

As atualizações de software incluem:

- **Patches** - Patches de segurança ou correções de bugs lançadas pela VMware
- **Atualizações** - Alteração de versão menor de um componente de pilha VMware
- **Upgrades** - Alteração de versão principal de um componente de pilha VMware

A Microsoft testa um patch de segurança crítico assim que estiver disponível a partir de VMware.

As soluções de solução VMware documentadas são implementadas em vez de instalar um patch correspondente até que as próximas atualizações programadas sejam implementadas. 
