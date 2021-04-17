---
title: O que é a Infraestrutura BareMetal para o Oráculo?
description: Conheça as funcionalidades que a Infraestrutura BareMetal oferece para as cargas de trabalho da Oracle.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559282"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>O que é a Infraestrutura BareMetal para o Oráculo?

Este artigo apresenta uma visão geral das funcionalidades que a BareMetal Infrastructure oferece para as cargas de trabalho da Oracle.

A Infraestrutura BareMetal para a Oracle baseia-se no Sistema de Computação Unificada (UCS) e no FLexPod certificado pela Oracle. A plataforma FlexPod fornece tecnologias de armazenamento, rede e servidor pré-validados. Oferece armazenamento NFS, proporcionando integração usando o protocolo DirectNFS. Os servidores BareMetal são dedicados a si, sem hipervisor nas instâncias BareMetal. 

Estes casos são para executar aplicações críticas da missão que requerem uma carga de trabalho da Oráculo. As instâncias BareMetal fornecem baixa latência (0,35 ms) às suas aplicações em execução em máquinas virtuais Azure (VMs). A BareMetal fornece disco de armazenamento partilhado e suporta multi-fundição necessária para a comunicação nó-a-nó com uma rede de interligação privada dedicada. 

Outras características da Infraestrutura BareMetal para o Oráculo incluem:

- Lâminas UCS certificadas pela Oracle - UCSB200-M5, UCSB460-M4, UCSB480-M5
- Comunicação oracle Real Application Clusters (RAC) nó-a-nó (multi-cast) usando lan virtual privado (VLAN) -40 Gb.
- Hardware gerido pela Microsoft
  - Armazenamento redundante, rede, energia, gestão
  - Monitorização de Infra, reparações e substituição
  - Inclui Azure ExpressRoute ao controlador de domínio do cliente
  - Segurança física e de rede segura, pode aceder a todos os serviços em nuvem Azure

### <a name="supported-protocols"></a>Protocolos suportados

Os seguintes protocolos são utilizados para diferentes pontos de montagem dentro dos servidores BareMetal para a carga de trabalho da Oracle.

- Suporte de SO – iSCSI
- Dados/registos - NFSv3
- backup/archieve - NFSv4

### <a name="licensing"></a>Licenciamento

- Trazes o teu próprio sistema operativo no local e licenças da Oracle.

### <a name="operating-system"></a>Sistema operativo

Os servidores estão pré-carregados com o sistema operativo RHEL 7.6.

## <a name="next-steps"></a>Passos seguintes

Conheça as SKUs para cargas de trabalho Oracle BareMetal.

> [!div class="nextstepaction"]
> [SKUs BareMetal para cargas de trabalho da Oráculo](oracle-baremetal-skus.md)
