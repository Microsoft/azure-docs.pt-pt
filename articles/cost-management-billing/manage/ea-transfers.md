---
title: Transferências do Azure Enterprise
description: Descreve as transferências do Azure EA
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943695"
---
# <a name="azure-enterprise-transfers"></a>Transferências do Azure Enterprise

Este artigo apresenta uma descrição geral das transferências Enterprise.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Transferir uma conta corporativa para uma nova inscrição

Uma transferência de conta move um proprietário de conta de uma inscrição para outra. Todas as subscrições relacionadas do proprietário da conta serão movidas para a inscrição de destino. Utilize a transferência de conta se tiver várias inscrições ativas e quiser remover apenas determinados proprietários de conta.

Esta secção destina-se a fins meramente informativos, pois a ação não pode ser executada por administradores da empresa. É necessário um pedido de suporte para transferir uma conta empresarial para uma inscrição nova.

Tenha os seguintes pontos em mente ao transferir uma conta corporativa para uma nova inscrição:

- Apenas as contas especificadas no pedido são transferidas. Se forem escolhidas todas as contas, serão todas transferidas.
- A inscrição de origem retém o estado como ativo ou expandido. Pode continuar com a inscrição até que expire.

### <a name="prerequisites"></a>Pré-requisitos

Ao pedir uma transferência de conta, forneça as seguintes informações:

- O número da inscrição de destino, o nome da conta e o e-mail do proprietário da conta a transferir
- Relativamente à inscrição de origem, o número de inscrição e a conta a ser transferida

Outros pontos a ter em mente antes de uma transferência de conta:

- É necessária a aprovação de um administrador de EA para a inscrição de origem e de destino
- Se uma transferência de conta não atender aos seus requisitos, considere uma transferência de inscrição.
- A transferência de conta transfere todos os serviços e subscrições relacionados com as contas específicas.
- Uma vez concluída a transferência, a conta transferida aparece inativa na inscrição de origem e ativa na inscrição de destino.
- A conta mostra a data de fim que corresponde à data de transferência efetiva na inscrição de origem e aparece como data de início na inscrição de destino.
- Qualquer utilização ocorrida na conta antes da data de transferência efetiva permanece na inscrição de origem.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Transferir inscrição corporativa para uma nova

Considera-se uma transferência de inscrição quando:

- O termo de Pré-pagamento de uma inscrição atual tiver terminado.
- Uma inscrição se encontra no estado expirado/alargado e é negociado um novo contrato.
- Tem várias inscrições e pretende combinar todas as contas e a faturação numa única inscrição.

Esta secção destina-se a fins meramente informativos, pois a ação não pode ser executada por administradores da empresa. É necessário um pedido de apoio para transferir uma inscrição de uma empresa para uma nova, a menos que a inscrição se qualifique para [a transferência de inscrição automática.](#auto-enrollment-transfer)

Quando pede a transferência de uma inscrição corporativa inteira para uma inscrição, ocorrem as seguintes ações:

- Todos os serviços, subscrições, contas, departamentos e toda a estrutura de inscrição, incluindo todos os administradores de departamentos do EA, são transferidos para uma inscrição de destino nova.
- O estado da inscrição é definido para _Transferida_. A inscrição transferida está disponível apenas para fins de relatório de histórico de utilização.
- Não pode adicionar funções ou subscrições a uma inscrição transferida. O estado de transferência impede uma maior utilização contra a inscrição.
- Perderá todo o saldo do Pré-pagamento do Azure restante no contrato, incluindo os termos futuros.
-    Se a inscrição que você está transferindo tem compras ri, a taxa de compra RI permanecerá na inscrição de fonte, no entanto todos os benefícios ri serão transferidos para uso na nova inscrição.
-    A taxa de compra única do mercado e quaisquer taxas fixas mensais já incorridas na matrícula antiga não são transferidas para a nova matrícula. As taxas do marketplace baseadas no consumo serão transferidas.

### <a name="effective-transfer-date"></a>Data de transferência efetiva

O dia da transferência efetiva pode ser na data de início da inscrição de destino ou numa data posterior.

A utilização da inscrição de origem é cobrada como Pré-pagamento do Azure ou utilização excedida. O uso que ocorre após a data de transferência efetiva é transferido para a nova inscrição e cobrado.

### <a name="prerequisites"></a>Pré-requisitos

Ao pedir uma transferência de inscrição, forneça as seguintes informações:

- Relativamente à inscrição de origem, o número de inscrição.
- Relativamente à inscrição de destino, o número de inscrição para o qual transferir.
- Relativamente à data efetiva da transferência de inscrição, pode ser uma data na ou após a data de início da inscrição de destino. A data escolhida não pode afetar o uso de qualquer fatura de excesso já emitida.

Outros pontos a ter em mente antes de uma transferência de inscrição:

- É necessária a aprovação dos Administradores EA da inscrição de origem e de destino.
- Se uma transferência de inscrição não atender aos seus requisitos, considere uma transferência de conta.
- O estado da inscrição de origem será atualizado para transferido e só estará disponível para fins de relatório de histórico de utilização.

### <a name="auto-enrollment-transfer"></a>Transferência de inscrição automática

Pode ver que uma inscrição tem o Estado **transferido,** mesmo que não tenha enviado um bilhete de apoio para solicitar uma transferência de inscrição. O estado **transferido** resulta do processo de transferência de inscrição automática. Para que o processo de transferência de inscrição automática ocorra durante a frase de renovação, existem alguns itens que devem ser incluídos no novo acordo:

- Número de inscrição prévia (deve existir no portal EA)
- A data de validade do número de matrículas prévia é um dia antes da data de início efetiva do novo acordo
- O novo acordo tem uma ordem de pré-pagamento faturada do Azure que tem uma data atual ou está atrasada
- A nova inscrição é criada no portal da EA

Se não houver dados de utilização em falta no portal da EA entre a inscrição prévia e a nova inscrição, então não terá de criar um bilhete de apoio à transferência.

### <a name="azure-prepayment"></a>Pré-pagamento do Azure

O Pré-pagamento do Azure não é transferível entre inscrições. Os saldos do Pré-pagamento do Azure são associados contratualmente à inscrição em que foi pedido. O Pré-pagamento do Azure não é transferido como parte do processo de transferência de inscrição ou conta.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Nenhum serviço é afetado pelas transferências de inscrições e de contas

Não há tempo de inatividade durante a transferência da inscrição ou da conta. Pode ser concluída no mesmo dia do pedido se todas as informações necessárias forem fornecidas.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Transferir uma subscrição Enterprise para uma subscrição Pay As You Go

Para transferir uma subscrição Enterprise para uma subscrição individual com tarifas Pay As You Go, tem de criar um novo pedido de suporte no portal Azure Enterprise. Para criar um pedido de suporte, selecione **+ Novo pedido de suporte** na área **Ajuda e Suporte**.

## <a name="change-azure-subscription-or-account-ownership"></a>Alterar a propriedade de uma subscrição ou conta do Azure

O Azure EA Portal pode transferir subscrições de um proprietário de conta para outro. Para obter mais informações, veja [Alterar a propriedade de uma subscrição ou conta do Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Efeitos da transferência da subscrição

Quando uma subscrição do Azure é transferida para uma conta no mesmo inquilino do AAD, todos os utilizadores, grupos e principais de serviço que tinham o [controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) para gerir recursos mantêm o respetivo acesso.

Para visualizar os utilizadores com acesso RBAC à subscrição:

1. No portal do Azure, abra as **Subscrições**.
2. Selecione a subscrição que pretende visualizar e, em seguida, selecione **Controlo de acesso (IAM)** .
3. Selecione **Atribuições de funções**. A página de atribuições de funções lista todos os utilizadores que têm acesso RBAC à subscrição.

Se a subscrição for transferida para uma conta num inquilino do AAD diferente, todos os utilizadores, grupos e principais de serviço que tinham [RBAC](../../role-based-access-control/overview.md) para gerir recursos _perdem_ o respetivo acesso. Embora o acesso RBAC não esteja presente, o acesso à subscrição poderá estar disponível por meio de mecanismos de segurança, incluindo:

- Certificados de gestão que concedem ao utilizador direitos de administrador aos recursos da subscrição. Para obter mais informações, veja [Criar e carregar um certificado de gestão do Azure](../../cloud-services/cloud-services-certs-create.md).
- Chaves de acesso dos serviços como o Armazenamento. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../../storage/common/storage-account-overview.md).
- Credenciais de Acesso Remoto dos serviços como máquinas virtuais do Azure.

Se o destinatário precisar de restringir o acesso aos recursos do Azure, deverá considerar a atualização de todos os segredos associados ao serviço. A maioria dos recursos pode ser atualizada com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, selecione **Todos os recursos**.
3. Selecione o recurso.
4. Na página de recursos, selecione **Definições** para ver e atualizar os segredos existentes.

## <a name="next-steps"></a>Passos seguintes

- Se precisar de ajuda na resolução de problemas com o Azure EA Portal, veja [Resolver problemas de acesso do Azure EA Portal](ea-portal-troubleshoot.md).