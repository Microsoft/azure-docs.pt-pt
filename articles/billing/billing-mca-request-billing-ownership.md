---
title: Obter a propriedade de cobrança das assinaturas do Azure
description: Saiba como solicitar a propriedade de cobrança de assinaturas do Azure de outros usuários.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019566"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Obter a propriedade de cobrança das assinaturas do Azure de outras contas

Talvez você queira assumir a propriedade das assinaturas do Azure se o proprietário da cobrança existente sair da sua organização ou se você quiser pagar pelas assinaturas por meio de sua conta de cobrança. Assumir a propriedade transfere as responsabilidades de cobrança das assinaturas para sua conta.

Este artigo se aplica a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato do cliente da Microsoft](#check-for-access).

Para solicitar a propriedade de cobrança, você deve ser um **proprietário de seção de fatura** ou colaborador de seção de **fatura**. Para saber mais, veja [tarefas de funções da seção fatura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Solicitar propriedade de cobrança

1. Entre no [portal do Azure](https://portal.azure.com) como um proprietário ou colaborador da seção de uma fatura para uma conta de cobrança do contrato do cliente da Microsoft.

2. Procure **Gerenciamento de custos + cobrança**.

   ![Captura de tela que mostra portal do Azure pesquisa de gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Selecione **seções de fatura** do lado esquerdo. Dependendo do seu acesso, talvez seja necessário selecionar uma conta de cobrança ou um perfil de cobrança. Na conta de cobrança ou perfil, selecione **seções da fatura**.
   
   ![Captura de tela que mostra a seleção de seções de fatura](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selecione uma seção de fatura na lista. Depois de assumir a propriedade das assinaturas, elas serão cobradas para esta seção da fatura.

5. Selecione **solicitações de transferência** do lado inferior esquerdo e, em seguida, selecione **Adicionar**.
 
   ![Captura de tela que mostra a seleção de solicitações de transferência](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Insira o endereço de email do usuário do qual você está solicitando a propriedade de cobrança. O usuário deve ser um administrador de conta em uma conta de cobrança do programa do Microsoft Online Service ou um proprietário de conta em um Enterprise Agreement. Para obter mais informações, consulte [exibir suas contas de cobrança no portal do Azure](billing-view-all-accounts.md). Selecione **Enviar solicitação de transferência**.

   ![Captura de tela que mostra o envio de uma solicitação de transferência](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. O usuário recebe um email com instruções para revisar sua solicitação de transferência.

   ![Captura de tela que mostra revisar email de solicitação de transferência](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções.

    ![Captura de tela que mostra revisar email de solicitação de transferência](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificar o status da solicitação de transferência

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Gerenciamento de custos + cobrança**.

   ![Captura de tela que mostra portal do Azure pesquisa de gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Selecione **seções de fatura** do lado esquerdo. Dependendo do seu acesso, talvez seja necessário selecionar uma conta de cobrança ou um perfil de cobrança. Na conta de cobrança ou perfil, selecione **seções da fatura**.
   
   ![Captura de tela que mostra a seleção de seções de fatura](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Selecione a seção fatura na lista para a qual você enviou a solicitação de transferência.

5. Selecione **solicitações de transferência** do lado inferior esquerdo. A página solicitações de transferência exibe as seguintes informações:

    ![Captura de tela que mostra a lista de solicitações de transferência](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Coluna|Definição|
   |---------|---------|
   |Data da solicitação|A data em que a solicitação de transferência foi enviada|
   |Destinatário|O endereço de email do usuário que você enviou a solicitação para transferir a propriedade de cobrança|
   |Data de validade|A data em que a solicitação expira|
   |Estado|O status da solicitação de transferência|

    A solicitação de transferência pode ter um dos seguintes status:

   |Estado|Definição|
   |---------|---------|
   |Em curso|O usuário não aceitou a solicitação de transferência|
   |A processar|O usuário aprovou a solicitação de transferência. A cobrança de assinaturas que o usuário selecionou está sendo transferida para a seção de faturas|
   |Concluído| A cobrança das assinaturas que o usuário selecionou é transferida para a seção de faturas|
   |Concluído com erros|A solicitação foi concluída, mas a cobrança de algumas assinaturas que o usuário selecionou não pôde ser transferida|
   |Fora do prazo|O usuário não aceitou a solicitação no tempo e expirou|
   |Cancelado|Alguém com acesso à solicitação de transferência cancelou a solicitação|
   |Recusada|O usuário recusou a solicitação de transferência|

7. Selecione uma solicitação de transferência para exibir detalhes. A página detalhes da transferência exibe as seguintes informações:
   
   ![Captura de tela que mostra a lista de assinaturas transferidas](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Coluna  |Definição|
   |---------|---------|
   |ID da solicitação de transferência|A ID exclusiva para sua solicitação de transferência. Se você enviar uma solicitação de suporte, compartilhe a ID com o suporte do Azure para agilizar sua solicitação de suporte|
   |Transferência solicitada em|A data em que a solicitação de transferência foi enviada|
   |Transferência solicitada por|O endereço de email do usuário que enviou a solicitação de transferência|
   |A solicitação de transferência expira em| A data em que a solicitação de transferência expira|
   |Endereço de email do destinatário|O endereço de email do usuário que você enviou a solicitação para transferir a propriedade de cobrança|
   |Link de transferência enviado ao destinatário|A URL que foi enviada ao usuário para examinar a solicitação de transferência|

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Você pode solicitar a propriedade de cobrança dos tipos de assinatura listados abaixo.

- [Pacote de ação](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure no Licenciamento Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Patrocínio de Azure Pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Desenvolvimento/teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Desenvolvimento/teste pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plano de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta Microsoft Azure patrocinada](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*Qualquer crédito disponível na assinatura não estará disponível na nova conta após a transferência.

\*\*Só há suporte para assinaturas em contas criadas durante a inscrição no site do Azure.


## <a name="additional-information"></a>Informações adicionais

A seção a seguir fornece informações adicionais sobre como transferir assinaturas.

### <a name="no-service-downtime"></a>Sem tempo de inatividade do serviço

Os serviços do Azure na assinatura continuam em execução sem nenhuma interrupção. Só fazemos a transição da relação de cobrança para as assinaturas do Azure que o usuário seleciona para transferir.

### <a name="disabled-subscriptions"></a>Assinaturas desabilitadas

Assinaturas desabilitadas não podem ser transferidas. As assinaturas devem estar no estado ativo para transferir sua propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos das assinaturas, como VMs, discos e transferência de sites.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Os produtos do Azure Marketplace são transferidos junto com suas respectivas assinaturas.

### <a name="azure-reservations-transfer"></a>Transferência de reservas do Azure

As reservas do Azure não se movem automaticamente com assinaturas. [Contate o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Acesso a usuários, grupos ou entidades de serviço existentes que foram atribuídos usando (Azure RBAC (controle de acesso baseado em função)) [.. /Role-Based-Access-Control/Overview.MD] não é afetado durante a transição.

### <a name="azure-support-plan"></a>Plano de suporte do Azure

O suporte do Azure não é transferido com as assinaturas. Se o usuário transferir todas as assinaturas do Azure, peça para cancelar seu plano de suporte.

### <a name="charges-for-transferred-subscription"></a>Cobranças de assinatura transferida

O proprietário original da cobrança das assinaturas é responsável por quaisquer encargos que foram relatados até o ponto em que a transferência foi concluída. Sua seção fatura é responsável pelas cobranças relatadas a partir do momento da transferência em diante. Pode haver alguns encargos que ocorreram antes da transferência, mas que foram relatados posteriormente. Esses encargos aparecem na seção fatura.

### <a name="cancel-a-transfer-request"></a>Cancelar uma solicitação de transferência

Você pode cancelar a solicitação de transferência até que a solicitação seja aprovada ou recusada. Para cancelar a solicitação de transferência, vá para a [página de detalhes de transferência](#check-the-transfer-request-status) e selecione Cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Transferência de SaaS (software como serviço)

Os produtos SaaS não são transferidos com as assinaturas. Peça ao usuário para [contatar o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade da cobrança de produtos SaaS. Junto com a propriedade de cobrança, o usuário também pode transferir a propriedade do recurso. A propriedade do recurso permite executar operações de gerenciamento, como excluir e exibir os detalhes do produto. O usuário deve ser um proprietário de recurso no produto SaaS para transferir a propriedade do recurso.

## <a name="check-for-access"></a>Verificar o acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- A propriedade de cobrança das assinaturas do Azure é transferida para a seção fatura. Acompanhe os encargos dessas assinaturas no [portal do Azure](https://portal.azure.com).
- Conceda a outras permissões para exibir e gerenciar a cobrança dessas assinaturas. Para obter mais informações, consulte [funções e tarefas de seção de fatura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
