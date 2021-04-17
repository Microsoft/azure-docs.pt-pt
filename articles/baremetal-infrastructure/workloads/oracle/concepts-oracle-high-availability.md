---
title: Alta disponibilidade e recuperação de desastres para a Oracle em BareMetal
description: Saiba mais sobre a alta disponibilidade e recuperação de desastres para a Oracle na Infraestrutura Azure BareMetal.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590308"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Alta disponibilidade e recuperação de desastres para a Oracle em BareMetal

Neste artigo, vamos olhar para o básico de alta disponibilidade e recuperação de desastres. Em seguida, vamos introduzir como você pode alcançar alta disponibilidade e recuperação de desastres em um ambiente Oráculo na Infraestrutura BareMetal.

## <a name="high-availability-vs-disaster-recovery"></a>Alta disponibilidade vs. recuperação de desastres

Tanto a alta disponibilidade como a recuperação de desastres fornecem cobertura, mas de diferentes tipos de falhas. Utilizam diferentes funcionalidades e opções da Base de Dados Oráculo.

A alta disponibilidade permite que um sistema supere múltiplas falhas sem afetar a experiência do utilizador da aplicação. As características comuns de um sistema altamente disponível incluem:

- Hardware redundante que não tem um único ponto de falha.
- Recuperação automática de falhas não críticas, tais como discos falhados ou cabos de rede defeituosos.
- A capacidade de rolar hardware e software muda sem qualquer efeito percetível no processamento.
- Cumpre ou excede os objetivos para objetivos de tempo de recuperação (RTO) e objetivos de ponto de recuperação (RPO).

A característica mais comum da Oracle usada para alta disponibilidade é [o Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92).

A recuperação de desastres protege-o de falhas localizadas irrecuperáveis que prejudicariam a sua estratégia primária de alta disponibilidade. No ecossistema Oráculo, é fornecido através da replicação da base de dados, também conhecido como [Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre funcionalidades de alta disponibilidade para a Oracle:

> [!div class="nextstepaction"]
> [Funcionalidades de alta disponibilidade para o Oráculo em Infraestruturas BareMetal](high-availability-features.md)
