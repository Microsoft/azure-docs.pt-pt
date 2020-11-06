---
title: Resolver problemas com a transferência dos detalhes de utilização da reserva do Azure
description: Este artigo ajuda-o a compreender e a resolver problemas relacionados com a indisponibilidade de transferência dos detalhes da instância reservada no portal do Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147342"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Resolver problemas com a transferência dos detalhes de utilização da reserva do Azure

Este artigo ajuda-o a compreender e a resolver problemas relacionados com a indisponibilidade de transferência dos detalhes da instância reservada no portal do Azure.

## <a name="symptoms"></a>Sintomas

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e navegue para **Reservas**.
1. Selecione uma reserva.
1. Na página de descrição geral da reserva, a vista predefinida mostra a utilização nos últimos sete dias. Pode selecionar **Transferir como CSV** para transferir os detalhes de utilização da reserva.
1. Quando altera o intervalo de tempo, a opção para **Transferir como CSV** torna-se indisponível.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Exemplo que mostra a opção Transferir como CSV indisponível" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Causa

Devido a limitações técnicas, o Azure não suporta a transferência de dados de um período superior a sete dias. Os períodos longos de clientes com grandes quantidades de reservas geram grandes quantidades de dados. A devolução de dados através de APIs sobrecarrega os recursos do Azure.

## <a name="solution"></a>Solução

Compreendemos que os clientes pretendem transferir dados de períodos mais longos. No entanto, atualmente, não há forma de transferir dados de utilização de reservas de longos períodos.

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre as reservas, veja [O que são as Reservas do Azure?](save-compute-costs-reservations.md).