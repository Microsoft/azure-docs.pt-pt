---
title: Configure O Encaminhamento DNS para o Azure Red Hat OpenShift 4
description: Configure O Encaminhamento DNS para o Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232637"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Configure o reencaminhado DNS num cluster OpenShift 4 do chapéu vermelho azure

Para configurar o DNS Forwarding num cluster OpenShift do Chapéu Vermelho Azure, terá de modificar o operador DNS. Esta modificação permitirá que as suas cápsulas de aplicação que executem dentro do cluster resolvam nomes hospedados num servidor Privado DNS fora do cluster. Estes passos estão documentados para OpenShift 4.3 [aqui](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

Por exemplo, se quiser encaminhar todos os pedidos de DNS para que o example.com servidor 192.168.100.10 seja reencaminhado para o qual o operador seja resolvido:
 
```bash
oc edit dns.operator/default
```
 
Isto lançará um editor `spec: {}` e poderá substituir por:
 
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
Confira mais informações sobre o reencaminhador DNS para OpenShift 4.3 [aqui](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).