---
title: Ver reservas de recursos do Azure | Microsoft Docs
description: Saiba como pode ver reservas do Azure no portal do Azure. Utilize as APIs, o PowerShell, a CLI e o Power BI para ver as reservas e a utilização.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 926e0c8bc0fdff580b4d1e84be5940e0945d52ae
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146271"
---
# <a name="view-azure-reservations"></a>Ver as reservas do Azure

Pode ver e gerir a reserva comprada no portal do Azure.

## <a name="permissions-to-view-a-reservation"></a>Permissões para ver uma reserva

Para ver ou gerir uma reserva, tem de ter permissão de leitor ou de proprietário na mesma. Por predefinição, quando compra uma reserva, é-lhe automaticamente atribuída a função Proprietário a si e ao administrador da conta na encomenda da reserva e na própria reserva. Para permitir que outras pessoas vejam a reserva, tem de adicioná-las como um **Proprietário** ou **Leitor** na encomenda da reserva ou na própria reserva. Adicionar uma pessoa à subscrição fornecida para faturar a reserva não a adiciona à reserva automaticamente. 

Para obter mais informações, veja [Adicionar ou alterar os utilizadores que podem gerir uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Ver reserva e utilização no portal do Azure

Para ver uma reserva como Proprietário ou Leitor

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Aceda a [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. A lista mostra todas as reservas nas quais tem a função Proprietário ou Leitor. Cada reserva mostra a última percentagem de utilização conhecida.
4. Clique na percentagem de utilização para ver o histórico e os detalhes de utilização. Veja os detalhes no vídeo abaixo.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Obter reservas e a utilização com APIs, o PowerShell e a CLI

Utilize os seguintes recursos para obter uma lista de todas as reservas
- [API: Encomenda de Reservas - Lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Encomenda de Reservas - Lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Encomenda de Reservas - Lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Também pode obter a [utilização de reservas](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) com a API de utilização de Instâncias Reservadas. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Ver reservas e a utilização no Power BI

Os utilizadores do Power BI têm duas opções
- Pacote de Conteúdos: as compras de reservas e os dados de utilização estão disponíveis no [pacote de conteúdos Informações de Consumo do Power BI](/power-bi/desktop-connect-azure-cost-management). Crie os relatórios que pretende com este pacote de conteúdos. 
- Aplicação Cost Management: Utilize a [Aplicação Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) para relatórios pré-criados que pode personalizar ainda mais.

## <a name="next-steps"></a>Passos seguintes

- [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).