---
title: Configuração Ethernet de BareMetal para Oráculo
description: Saiba mais sobre a configuração das interfaces Ethernet em instâncias BareMetal para cargas de trabalho da Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588892"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Configuração Ethernet de BareMetal para Oráculo

Neste artigo, vamos analisar a configuração das interfaces Ethernet em instâncias BareMetal para cargas de trabalho da Oracle.

Cada instância BareMetal prevista para o Oráculo vem pré-configurada com conjuntos de interfaces Ethernet. As interfaces Ethernet são categorizadas em quatro tipos:

- Usado para ou pelo acesso ao cliente.
- Usado para comunicação nó-a-nó. Esta interface está configurada em todos os servidores, independentemente da topologia solicitada. É usado apenas para cenários de escala.
- Usado para conectividade nó-para-armazenamento.
- Utilizado para a configuração de recuperação de desastres (DR) e conectividade ao alcance global para a conectividade entre regiões.

## <a name="architecture"></a>Arquitetura

O diagrama seguinte ilustra a arquitetura das interfaces Ethernet pré-configuradas da Infraestrutura BareMetal. 

[![Diagrama mostrando a arquitetura das interfaces Ethernet pré-configuradas para cargas de trabalho do Oráculo.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

A configuração padrão vem com uma interface IP do cliente (eth1), conectando-se a partir da sua Rede Virtual Azure (VNET) através da qual pode utilizar Secure Shell (SSH) para aceder a uma instância BareMetal.

> [!NOTE]
> Para outra interface de cliente (eth10) de um VNET Azure diferente, contacte o Microsoft CSA para apresentar um pedido de serviço. Por exemplo, se quiser ambientes de desenvolvimento/teste, bem como de produção/DR.

| **Interface lógica NIC** | **Nome com RHEL OS** | **Cenário de teste** |
| --- | --- | --- |
| A | net1.inquilino | Cliente para caso BareMetal |
| C | net2.inquilino | Nó-a-armazenamento; apoia a coordenação e o acesso aos controladores de armazenamento para a gestão do ambiente de armazenamento. |
| B | net3.inquilino | Nó-a-nó (interligação privada) |
| C | net4.inquilino | Reservado/ iSCSI |
| C | net5.inquilino | Reservado/ Reserva de Registo |
| C | net6.inquilino | Backup nó-a-storage_Data (RMAN, Snapshot) |
| C | net7.inquilino | Nó-a-storage_dNFS-Pri; fornece conectividade com a matriz de armazenamento NetApp. |
| C | net8.inquilino | Nó-a-storage_dNFS-Sec; fornece conectividade com a matriz de armazenamento NetApp. |
| D | net9.inquilino | Conectividade DR para a configuração global de alcance para aceder ao IMC em outra região. |
| A | \*net10.inquilino | \* Cliente para caso BareMetal
 |

Se necessário, pode definir mais cartões controladores de interface de rede (NIC) por si só. No entanto, as configurações dos NICs existentes *não podem* ser alteradas.

## <a name="usage-rules"></a>Regras de utilização

Para os casos BareMetal, o padrão terá nove endereços IP atribuídos nos quatro NICs lógicos. Aplicam-se as seguintes regras de utilização:

- O Ethernet "A" deve ter um endereço IP atribuído que esteja fora do intervalo de endereços IP do servidor que submeteu à Microsoft. Este endereço IP não deve ser mantido no diretório etc/anfitriões do SISTEMA.
- O Ethernet "B" deve ser mantido exclusivamente no diretório etc/anfitriões para comunicação entre as várias instâncias. Mantenha estes endereços IP nas configurações de Clusters de Aplicação Real oracle (RAC) de escala como os endereços IP utilizados para a configuração do nó inter-nosto.
- O Ethernet "C" deve ter um endereço IP atribuído que seja utilizado para comunicação ao armazenamento de NFS. Este tipo de endereço não deve ser mantido no diretório etc/anfitriões.
- O Ethernet "D" deve ser utilizado exclusivamente para a configuração de alcance global para aceder a casos BareMetal na sua região dr.

## <a name="next-step"></a>Passo seguinte

Saiba mais sobre a Infraestruturas BareMetal para arquitetura Oracle.

> [!div class="nextstepaction"]
> [Arquitetura da Infraestrutura BareMetal para Oráculo](oracle-baremetal-architecture.md)
