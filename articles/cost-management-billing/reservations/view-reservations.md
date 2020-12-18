---
title: Ver reservas de recursos do Azure
description: Saiba como pode ver reservas do Azure no portal do Azure. Utilize as APIs, o PowerShell, a CLI e o Power BI para ver as reservas e a utilização.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561109"
---
# <a name="view-azure-reservations"></a>Ver as reservas do Azure

Este artigo explica como ver as reservas do Azure no portal do Azure. Pode ver e gerir reservas compradas no portal do Azure.

## <a name="who-can-manage-a-reservation-by-default"></a>Quem pode gerir reservas por predefinição

Por predefinição, os utilizadores seguintes podem ver e gerir as reservas:

- A pessoa que comprar uma reserva e o administrador de conta da subscrição de faturação utilizada para a compra são adicionados à encomenda da reserva.
- Administradores de faturação do Contrato Enterprise e do Contrato de Cliente Microsoft.

Para permitir que outras pessoas façam a gestão de reservas, tem duas opções:

- Delegar a gestão do acesso a encomendas de reservas individuais:
    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    1. Selecione **Todos os Serviços** > **Reserva** para listar as reservas às quais tem acesso.
    1. Selecione a reserva cujo o acesso quer delegar a outros utilizadores.
    1. Nos Detalhes da reserva, selecione a encomenda da reserva.
    1. Selecione **Controlo de acesso (IAM)** .
    1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se quiser conceder acesso limitado, selecione uma função diferente.
    1. Introduza o endereço de e-mail do utilizador que quer adicionar como proprietário.
    1. Selecione o utilizador e, em seguida, selecione **Guardar**.

- Adicionar um utilizador como administrador de faturação a um Contrato Enterprise ou Contrato de Cliente Microsoft:
    - Num Contrato Enterprise, adicione utilizadores com a função _Administrador do Enterprise_ para ver e gerir todas as encomendas de reservas que se aplicam a esse Contrato Enterprise. Os utilizadores com a função _Administrador do Enterprise (só de leitura)_ só podem ver a reserva. Os administradores de departamentos e os proprietários de conta não podem ver as reservas, _a não ser_ que sejam adicionados explicitamente às mesmas com o Controlo de acesso (IAM). Para obter mais informações, veja [Gerir funções empresariais do Azure](../manage/understand-ea-roles.md).

        _Os administradores Enterprise podem assumir a propriedade de uma encomenda de reserva e podem adicionar outros utilizadores a uma reserva com o Controlo de acesso (IAM)._
    - Num Contrato de Cliente Microsoft, os utilizadores com a função de proprietário de perfil de faturação ou a função contribuidor de perfil de faturação podem gerir todas as compras de reservas feitas com o perfil de faturação. Os leitores de perfil de faturação e os gestores de faturas podem ver todas as reservas que são pagas com o perfil de faturação. No entanto, não podem fazer alterações às reservas.
    Para obter mais informações, veja [Funções e tarefas do perfil de faturação](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Como os administradores de faturação veem ou gerem as reservas

1. Aceda a **Cost Management + Faturação** e, no lado esquerdo da página, selecione **Transações de Reservas**.
2. Se tiver as permissões de faturação necessárias, pode ver e gerir reservas. Se não vir reservas, confirme que tem sessão iniciada com o inquilino do Azure AD em que as reservas foram criadas.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Ver reserva e utilização no portal do Azure

Para ver uma reserva como Proprietário ou Leitor

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Aceda a [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. A lista mostra todas as reservas onde possui a função Proprietário ou Leitor. Cada reserva mostra a última percentagem de utilização conhecida.
4. Selecione a percentagem de utilização para ver o histórico e os detalhes de utilização. Veja os detalhes no vídeo abaixo.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Obter reservas e a utilização com APIs, o PowerShell e a CLI

Utilize os seguintes recursos para obter uma lista de todas as reservas:

- [API: Encomenda de Reservas - Lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Encomenda de Reservas - Lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Encomenda de Reservas - Lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Também pode obter a [utilização de reservas](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) com a API de utilização de Instâncias Reservadas. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Ver reservas e a utilização no Power BI

Os utilizadores do Power BI têm duas opções
- Pacote de Conteúdos: a data de compra e os dados de utilização da reserva estão disponíveis no [pacote de conteúdos Informações de Consumo do Power BI](/power-bi/desktop-connect-azure-cost-management). Crie os relatórios que pretende com este pacote de conteúdos. 
- Aplicação Cost Management: Utilize a [Aplicação Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) para relatórios pré-criados que pode personalizar ainda mais.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md).
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md).
- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations).

