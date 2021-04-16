---
title: Armazenamento em BareMetal para cargas de trabalho da Oráculo
description: Conheça o armazenamento oferecido pela Infraestrutura BareMetal para cargas de trabalho da Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559213"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Armazenamento em BareMetal para cargas de trabalho da Oráculo

Neste artigo, vamos dar uma visão geral do armazenamento oferecido pela Infraestrutura BareMetal para cargas de trabalho oracle.

A Infraestrutura BareMetal para a Oracle oferece armazenamento do NetApp Network File System (NFS). O armazenamento NFS não requer certificação Oracle Real Application Clusters (RAC). Para mais informações, consulte [a Oracle RAC Technologies Matrix para Clusters Linux.](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)

Esta oferta de armazenamento inclui suporte de nível 3 de um parceiro OEM, utilizando controladores de armazenamento A700s ou A800s.

O armazenamento da Infraestrutura BareMetal oferece estas capacidades de armazenamento premium:

- Volumes de armazenamento de Dados/log/quórum/FSA oferecidos através do protocolo dNFS.
- Redundância do disco *(Proteção contra até duas falhas de disco).*
- Reduza os seus dados para vários volumes limitados a 100 TB por volume.
- Dimensione para vários controladores de armazenamento até 12 controladores.
- Sem gestão do nível do disco *(adicionar/remover discos),* automaticamente tomado cuidado pela Infra.
- Não há tempo de inatividade para redistribuir o conteúdo do ficheiro para diferentes volumes.
- Capacidade de crescer/encolher volumes.
- Integração do SnapCenter para cópia de segurança utilizando clonagem e SnapVault.
- Encriptação de dados em repouso, suportando FIPS (140-2).

## <a name="next-steps"></a>Passos seguintes

Conheça as considerações de correção da Infraestruturas BareMetal.

> [!div class="nextstepaction"]
> [Ponderações de correção para BareMetal para o Oráculo](oracle-baremetal-patching.md)

