---
title: Ponderações de correção para BareMetal para o Oráculo
description: Saiba mais sobre as considerações de correção do sistema operativo/kernel para a Infraestrutura BareMetal para a Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559222"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Ponderações de correção para BareMetal para o Oráculo

Neste artigo, vamos analisar considerações importantes de correção do sistema operativo/kernel para a Infraestrutura BareMetal para a Oracle.

Para um desempenho adequado da rede e estabilidade do sistema, instale a versão específica do SISTEMA dos controladores eNIC e FNIC, conforme indicado na tabela de compatibilidade. 

Os servidores são entregues a clientes com versões compatíveis. No entanto, durante o sistema operativo (SISTEMA)/kernel, os condutores podem ser revirados para as versões do controlador predefinido. Por isso, certifique-se de confirmar que a versão adequada do controlador está a funcionar seguindo as operações de correção de OS/Kernel.

| Fornecedor de OS | Versão do pacote de SO | Versão do Firmware | Condutor eNIC | condutor fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a configuração Ethernet de BareMetal para o Oráculo.

> [!div class="nextstepaction"]
> [Configuração Ethernet de BareMetal para Oráculo](oracle-baremetal-ethernet.md)

