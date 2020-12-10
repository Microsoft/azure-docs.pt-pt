---
title: Elevada disponibilidade e recuperação após desastre
titleSuffix: Azure Digital Twins
description: Descreve as funcionalidades Azure e Azure Digital Twins que o ajudam a construir soluções Azure IoT altamente disponíveis com capacidades de recuperação de desastres.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 35f4aae246f105d832aaf92c5c5797c8a65b44f1
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938551"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins alta disponibilidade e recuperação de desastres

Uma área-chave de consideração para soluções IoT resilientes é a continuidade do negócio e a recuperação de desastres. Conceber para **alta disponibilidade (HA)** e **recuperação de desastres (DR)** pode ajudá-lo a definir e alcançar objetivos adequados para a sua solução.

Este artigo discute as funcionalidades HA e DR oferecidas especificamente pelo serviço Azure Digital Twins.

A Azure Digital Twins suporta estas opções de funcionalidade:
* *Intrarregião HA* – Redundância incorporada para entregar em horário de serviço
* *Região transversal DR* – Falha para uma região de Azure geo-emparelhada no caso de uma falha inesperada do centro de dados

Você também pode ver a secção [*de Boas Práticas*](#best-practices) para orientação geral Azure sobre design para HA/DR.

## <a name="intra-region-ha"></a>HA intrarregião
 
A Azure Digital Twins fornece HA intrarregião implementando despedimentos dentro do serviço. Isto reflete-se no [serviço SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) para o tempo de paragem. **Não são necessários trabalhos adicionais pelos desenvolvedores de uma solução Azure Digital Twins para tirar partido destas funcionalidades HA.** Apesar de a Azure Digital Twins oferecer uma garantia de tempo elevado razoavelmente elevada, ainda se podem esperar falhas transitórias, como em qualquer plataforma de computação distribuída. Devem ser incorporadas políticas de reedição adequadas aos componentes que interagem com uma aplicação em nuvem para lidar com falhas transitórias.

## <a name="cross-region-dr"></a>Região transversal DR

Pode haver algumas situações raras quando um centro de dados experimenta interrupções prolongadas devido a falhas de energia ou outros eventos na região. Tais eventos são raros, e durante tais falhas, a capacidade de HA intrarregião descrita acima pode não ajudar. A Azure Digital Twins aborda esta questão com o failover iniciado pela Microsoft.

**O failover iniciado pela Microsoft** é exercido pela Microsoft em situações raras para falhar todas as instâncias Azure Digital Twins de uma região afetada para a região geo-emparelhada correspondente. Este processo é uma opção padrão (sem forma de os utilizadores optarem por sair), e não requer qualquer intervenção do utilizador. A Microsoft reserva-se o direito de determinar quando esta opção será exercida. Este mecanismo não envolve o consentimento do utilizador antes de a instância do utilizador ser chumbada.

>[!NOTE]
> Alguns serviços Azure também fornecem uma opção adicional chamada **failover iniciado pelo cliente**, que permite que os clientes iniciem uma falha apenas para o seu exemplo, como para executar um berbequim DR. Este mecanismo não é **atualmente suportado** pela Azure Digital Twins. 

## <a name="monitor-service-health"></a>Monitorizar o estado de funcionamento do serviço

À medida que as instâncias Azure Digital Twins são falhadas e recuperadas, pode monitorizar o processo utilizando a ferramenta [Azure Service Health.](https://docs.microsoft.com/azure/service-health/service-health-overview) O Service Health acompanha a saúde dos seus serviços Azure em diferentes regiões e subscrições, e partilha comunicações com impacto de serviços sobre interrupções e tempos de inatividade.

Durante um evento de failover, a Service Health pode fornecer uma indicação de quando o seu serviço está em baixo, e quando é de volta.

Para ver eventos de Saúde de Serviço...
1. Navegue para a [Saúde](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) do Serviço no portal Azure (pode utilizar este link ou pesquisar através da barra de pesquisa do portal).
1. Utilize o menu esquerdo para mudar para a página *de histórico da Saúde.*
1. Procure um *Nome de Emissão* começando com **Azure Digital Twins,** e selecione-o.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Screenshot do portal Azure mostrando a página de Histórico de Saúde. Há uma lista de várias questões dos últimos dias e está em destaque uma questão chamada &quot;Azure Digital Twins - West Europe - Mitigated&quot;." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Para obter informações gerais sobre a paralisação, consulte o *separador Resumo.*

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Na página 'Histórico da Saúde', destaca-se o separador Resumo. O separador apresenta informações gerais, como o recurso que foi afetado, a sua região e a sua subscrição." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Para obter mais informações e atualizações sobre o problema ao longo do tempo, consulte o *separador 'Atualizações de Problemas'.*

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Na página 'Histórico de Saúde', destaca-se o separador 'Atualizações de Problemas'. O separador apresenta várias entradas que mostram o estado atual de há um dia." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Note que as informações apresentadas nesta ferramenta não são específicas de um exemplo Azure Digital. Depois de usar a Saúde do Serviço para entender o que se passa com o serviço Azure Digital Twins numa determinada região ou subscrição, pode dar um passo mais além, utilizando a [ferramenta de saúde de recursos](troubleshoot-resource-health.md) para aprofundar casos específicos e ver se são impactados.

## <a name="best-practices"></a>Melhores práticas

Para as melhores práticas em HA/DR, consulte as seguintes orientações do Azure sobre este tema: 
* O artigo [*Azure Business Continuity Technical Guidance*](/azure/architecture/framework/resiliency/overview) descreve um quadro geral para ajudá-lo a pensar na continuidade do negócio e na recuperação de desastres. 
* O trabalho [*de recuperação de desastres e alta disponibilidade para aplicações Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) fornece orientação de arquitetura sobre estratégias para aplicações Azure para alcançar alta disponibilidade (HA) e recuperação de desastres (DR).

## <a name="next-steps"></a>Passos seguintes 

Leia mais sobre como começar com as soluções Azure Digital Twins:
 
* [*O que é o Azure Digital Twins?*](overview.md)
* [*Quickstart: Explore um cenário de amostra*](quickstart-adt-explorer.md)