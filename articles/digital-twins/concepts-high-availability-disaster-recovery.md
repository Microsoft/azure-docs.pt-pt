---
title: Elevada disponibilidade e recuperação após desastre
titleSuffix: Azure Digital Twins
description: Descreve as funcionalidades Azure e Azure Digital Twins que o ajudam a construir soluções Azure IoT altamente disponíveis com capacidades de recuperação de desastres.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 85fd5a4246e891ef6640438b07e12a9c32ad12fa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094555"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins alta disponibilidade e recuperação de desastres

Uma área-chave de consideração para soluções IoT resilientes é a continuidade do negócio e a recuperação de desastres. Conceber para **alta disponibilidade (HA)** e **recuperação de desastres (DR)** pode ajudá-lo a definir e alcançar objetivos adequados para a sua solução.

Este artigo discute as funcionalidades HA e DR oferecidas especificamente pelo serviço Azure Digital Twins.

A Azure Digital Twins suporta estas opções de funcionalidade:
* *Intrarregião HA* – Redundância incorporada para entregar em horário de serviço
* *Região transversal DR* – Falha para uma região de Azure geo-emparelhada no caso de uma falha inesperada do centro de dados

Você também pode ver a secção [*de Boas Práticas*](#best-practices) para orientação geral Azure sobre design para HA/DR.

## <a name="intra-region-ha"></a>HA intrarregião
 
A Azure Digital Twins fornece HA intrarregião implementando despedimentos dentro do serviço. **Não são necessários trabalhos adicionais pelos desenvolvedores de uma solução Azure Digital Twins para tirar partido destas funcionalidades HA.** Apesar de a Azure Digital Twins oferecer uma garantia de tempo elevado razoavelmente elevada, ainda se podem esperar falhas transitórias, como em qualquer plataforma de computação distribuída. Devem ser incorporadas políticas de reedição adequadas aos componentes que interagem com uma aplicação em nuvem para lidar com falhas transitórias.

## <a name="cross-region-dr"></a>Região transversal DR

Pode haver algumas situações raras quando um centro de dados experimenta interrupções prolongadas devido a falhas de energia ou outros eventos na região. Tais eventos são raros, e durante tais falhas, a capacidade de HA intrarregião descrita acima pode não ajudar. A Azure Digital Twins aborda esta questão com o failover iniciado pela Microsoft.

**O failover iniciado pela Microsoft** é exercido pela Microsoft em situações raras para falhar todas as instâncias Azure Digital Twins de uma região afetada para a região geo-emparelhada correspondente. Este processo é uma opção padrão (sem forma de os utilizadores optarem por sair), e não requer qualquer intervenção do utilizador. A Microsoft reserva-se o direito de determinar quando esta opção será exercida. Este mecanismo não envolve o consentimento do utilizador antes de a instância do utilizador ser chumbada.

>[!NOTE]
> Alguns serviços Azure também fornecem uma opção adicional chamada **failover iniciado pelo cliente**, que permite que os clientes iniciem uma falha apenas para o seu exemplo, como para executar um berbequim DR. Este mecanismo não é **atualmente suportado** pela Azure Digital Twins. 

## <a name="best-practices"></a>Melhores práticas

Para as melhores práticas em HA/DR, consulte as seguintes orientações do Azure sobre este tema: 
* O artigo [*Azure Business Continuity Technical Guidance*](/azure/architecture/framework/resiliency/overview) descreve um quadro geral para ajudá-lo a pensar na continuidade do negócio e na recuperação de desastres. 
* O trabalho [*de recuperação de desastres e alta disponibilidade para aplicações Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) fornece orientação de arquitetura sobre estratégias para aplicações Azure para alcançar alta disponibilidade (HA) e recuperação de desastres (DR).

## <a name="next-steps"></a>Passos seguintes 

Leia mais sobre como começar com as soluções Azure Digital Twins:
 
* [*O que é o Azure Digital Twins?*](overview.md)
* [*Quickstart: Explore um cenário de amostra*](quickstart-adt-explorer.md)