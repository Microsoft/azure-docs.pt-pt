---
title: Ver utilização da reserva Azure
description: Saiba como obter a utilização da reserva e detalhes.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568949"
---
# <a name="view-reservation-utilization-after-purchase"></a>Ver utilização da reserva após a compra

Pode ver a percentagem de utilização da reserva e os recursos que utilizaram a reserva no portal Azure e na aplicação Cost Management Power BI.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Ver utilização no portal Azure com acesso a Azure RBAC

Para visualizar a utilização da reserva, você deve ter acesso Azure RBAC à reserva ou você deve ter acesso elevado para gerir todas as subscrições e grupos de gestão Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Aceda a [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. A lista mostra todas as reservas onde possui a função Proprietário ou Leitor. Cada reserva mostra a última percentagem de utilização conhecida.
1. Selecione a percentagem de utilização para ver o histórico e os detalhes de utilização. O vídeo que se segue mostra um exemplo.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Ver utilização como administrador de faturação

Um administrador do Enterprise Agreement (EA) ou um administrador de faturação do Microsoft Customer Agreement (MCA) pode ver a utilização da Gestão de **Custos + Faturação**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Ir para **a Gestão de Custos +** Reservas de  >  **Faturação.**
1. Selecione a percentagem de utilização para ver o histórico e os detalhes de utilização.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Obter reservas e a utilização com APIs, o PowerShell e a CLI

Pode obter a [utilização](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) da reserva utilizando a API de utilização de Instâncias Reservadas.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Ver reservas e a utilização no Power BI

Existem duas opções para utilizadores de Power BI:

- Conector Azure Cost Management para Power BI Desktop - Data de compra de reserva e dados de utilização estão disponíveis no [conector Azure Cost Management para Power BI Desktop](/power-bi/desktop-connect-azure-cost-management). Crie os relatórios que pretende utilizando o conector.
- Azure Cost Management Power BI App - Use a [App Azure Cost Management Power BI](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) para relatórios pré-criados que pode personalizar ainda mais.

## <a name="next-steps"></a>Passos seguintes

- [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md).
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md).
- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations).
