---
title: Configure DNS Reencaminhamento para Azure Red Hat OpenShift 4
description: Configure DNS Reencaminhamento para Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: aa18959d0dc78dedf8b57dc120ab6744afa9051f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070598"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Configurar o encaminhamento de DNS num Azure Red Hat OpenShift 4 Cluster

Para configurar o reencaminhamento de DNS num cluster Azure Red Hat OpenShift, terá de modificar o operador DNS. Esta modificação permitirá que as cápsulas de aplicação que executam dentro do cluster resolvam os nomes alojados num servidor PRIVADO DNS fora do cluster. Estes passos estão documentados para OpenShift 4.6 [aqui.](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)

Por exemplo, se pretender encaminhar todos os pedidos DNS para que *.example.com sejam resolvidos por um servidor DNS 192.168.100.100, pode editar a configuração do operador executando:
 
```bash
oc edit dns.operator/default
```
 
Isto lançará um editor e poderá substituir `spec: {}` por:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Guarde o ficheiro e saia do seu editor.

## <a name="next-steps"></a>Passos seguintes
Consulte mais informações sobre o encaminhamento de DNS para OpenShift 4.6 [aqui](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).