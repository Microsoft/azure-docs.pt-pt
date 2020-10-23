---
title: Resolver problemas na utilização da reserva do Azure
description: Este artigo ajuda-o a compreender e a resolver problemas com as reservas do Azure que mostram zero (0) ou nenhuma utilização no portal do Azure. A utilização sem correspondência também é explicada.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207440"
---
# <a name="troubleshoot-reservation-utilization"></a>Resolver problemas da utilização da reserva

Este artigo ajuda-o a compreender e a resolver problemas com as reservas do Azure que mostram zero (0) ou nenhuma utilização no portal do Azure. A utilização sem correspondência também é explicada.

## <a name="symptoms"></a>Sintomas

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e navegue para **Reservas**.
1. Na lista de reservas, procure a quantidade de utilização de uma reserva na coluna **Utilização (%)** . Pode ser 0%.
1. Selecione a reserva.
1. Na página de descrição geral da reserva, a sua percentagem utilizada no gráfico pode não corresponder ao valor apresentado na lista de reservas.

## <a name="cause"></a>Causa

A coluna **Utilização (%)** no portal do Azure mostra o valor do dia atual. O valor é calculado à medida que os dados de utilização chegam a partir do local de execução dos recursos. O Azure recorre à utilização para calcular a percentagem de utilização.

Alguns recursos reportam a utilização de forma mais lenta do que outros. Além disso, alguns produtos, como a Base de Dados SQL, apresentam os dados de utilização de forma lenta.

Esta latência pode fazer com que o cálculo de utilização mostre valores inferiores à utilização real. A diferença é percetível ao nível do dia. Nesses casos, se o Azure não obtiver dados de utilização entre quatro e oito horas, calcula um valor de 0%. O valor 0% é mostrado porque não os dados de utilização não chegaram e porque parece que a reserva não está a aplicar qualquer benefício a nenhum recurso.

À medida que os dados de utilização chegam, o valor muda para a percentagem certa. Depois de recolhidos todos os dados de utilização, o valor correto é determinado e devidamente apresentado no gráfico.

## <a name="solution"></a>Solução

Se concluir que os valores de utilização não correspondem às suas expectativas, reveja o gráfico para obter a melhor vista da utilização real. Qualquer valor de ponto com mais de dois dias deve ser correto. Médias maiores, de sete a 30 dias, devem ser corretas.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como gerir reservas, veja [Gerir reservas de recursos do Azure.](manage-reserved-vm-instance.md)