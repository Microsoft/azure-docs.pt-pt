---
title: Obter a propriedade da faturação das subscrições do Azure do Contrato de Parceiro da Microsoft (MPA)
description: Saiba como pedir a propriedade da faturação das subscrições do Azure de outros utilizadores.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: 312808517c80041cb45e2e8ac46566c1f59884e1
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376795"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Obter a propriedade da faturação das subscrições do Azure da conta MPA

Para fornecer uma única fatura consolidada para os serviços geridos e o consumo do Azure, o Fornecedor de Soluções Cloud (CSP) pode assumir a propriedade de faturação das subscrições do Azure dos clientes com o Contrato Enterprise (EA).

Esta funcionalidade está disponível apenas para os Parceiros de Faturação Direta do CSP certificados como [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Está sujeita à governação e às políticas da Microsoft e pode exigir análise e aprovação para determinados clientes.

Para pedir a propriedade de faturação, precisa de ter a função de **Administrador Global** ou **Agente de Administração**. Para saber mais, veja [Partner Center - Assign users roles and permissions](https://docs.microsoft.com/partner-center/permissions-overview) (Centro de Parceiros – Atribuir funções e permissões de utilizadores).

Este artigo aplica-se às contas de faturação dos Contratos de Parceiro da Microsoft. Estas contas foram criadas para os Fornecedores de Soluções Cloud (CSPs) gerirem a faturação dos clientes na nova experiência comercial. A nova experiência está disponível apenas para os parceiros, que têm pelo menos um cliente que aceitou um Contrato de Cliente da Microsoft (MCA) e tem um Plano do Azure. [Verifique se tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Pré-requisitos

1. Estabeleça uma [relação de revendedor ](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) com o cliente. Veja [CSP Regional Authorization Overview](https://docs.microsoft.com/partner-center/regional-authorization-overview) (Descrição Geral de Autorização Regional do CSP) para garantir que o cliente e o Inquilino parceiro estão dentro das mesmas regiões autorizadas.  

2. [Confirme que o cliente aceitou o Contrato de Cliente da Microsoft](https://docs.microsoft.com/partner-center/confirm-customer-agreement).

3. Configure o [plano do Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) para o cliente. Se o cliente estiver a comprar através de vários revendedores, precisará de configurar o plano do Azure para cada combinação de cliente e revendedor.

## <a name="request-billing-ownership"></a>Pedir a propriedade da faturação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais do Agente de Administração do CSP.

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa da opção cost management + faturação no portal do Azure](./media/mpa-request-ownership/search-cmb.png)

3. Selecione **Clientes** no lado esquerdo e, em seguida, selecione um cliente na lista.
   
   ![Captura de ecrã a mostrar a seleção de clientes](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Selecione **Pedidos de transferência** no canto inferior esquerdo e, em seguida, selecione **Adicionar um novo pedido**.
 
   ![Captura de ecrã que mostra a seleção dos pedidos de transferência](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Introduza o endereço de e-mail do utilizador na organização do cliente que aceitará o pedido de transferência. O utilizador terá de ser um proprietário de conta num Contrato Enterprise. Selecione **Enviar pedido de transferência**.

   ![Captura de ecrã que mostra o envio de um pedido de transferência](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. O utilizador recebe um e-mail com instruções para rever o pedido de transferência.

   ![Captura de ecrã que mostra o e-mail da revisão do pedido de transferência](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. Para aprovar o pedido de transferência, o utilizador seleciona a ligação no e-mail e segue as instruções.

    ![Captura de ecrã que mostra o e-mail da revisão do pedido de transferência](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificar o estado do pedido de transferência

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa da opção cost management + faturação no portal do Azure](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Selecione **Clientes** no lado esquerdo.

   ![Captura de ecrã a mostrar a seleção de clientes](./media/mpa-request-ownership/mpa-select-customers.png)

4. Selecione o cliente na lista para a qual enviou o pedido de transferência.

5. Selecione **Pedidos de transferência** no canto inferior esquerdo. A página Pedidos de transferência apresenta as seguintes informações:

    ![Captura de ecrã que mostra a lista de pedidos de transferência](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Coluna|Definição|
   |---------|---------|
   |Data do pedido|A data em que o pedido de transferência foi enviado|
   |Destinatário|O endereço de e-mail do utilizador para o qual enviou o pedido para transferir a propriedade de faturação|
   |Data de validade|A data em que o pedido expirou|
   |Estado|O estado do pedido de transferência|

    O pedido de transferência pode ter um dos seguintes estados:

   |Estado|Definição|
   |---------|---------|
   |Em curso|O utilizador não aceitou o pedido de transferência|
   |Em processamento|O utilizador aprovou o pedido de transferência. A faturação das subscrições que o utilizador selecionou está a ser transferida para a conta|
   |Concluído| A faturação das subscrições que o utilizador selecionou foi transferida para a conta|
   |Concluído com erros|O pedido foi concluído, mas a faturação de algumas subscrições que o utilizador selecionou não pôde ser transferida|
   |Fora do prazo|O utilizador não aceitou o pedido a tempo e expirou|
   |Cancelado|Alguém com acesso ao pedido de transferência cancelou o pedido|
   |Recusado|O utilizador recusou o pedido de transferência|

6. Selecione um pedido de transferência para ver os detalhes. A página detalhes da transferência apresenta as seguintes informações: ![Captura de ecrã a mostrar a lista das subscrições transferidas](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Coluna  |Definição|
   |---------|---------|
   |ID do pedido de transferência|O ID exclusivo do pedido de transferência. Se submeter um pedido de suporte, partilhe o ID com a equipa do suporte do Azure para acelerar o pedido|
   |Transferência pedida a|A data em que o pedido de transferência foi enviado|
   |Transferência pedida por|O endereço de e-mail do utilizador que enviou o pedido de transferência|
   |Pedido de transferência expirou a| A data em que o pedido de transferência expirou|
   |Endereço de e-mail do destinatário|O endereço de e-mail do utilizador para o qual enviou o pedido para transferir a propriedade de faturação|
   |Ligação da transferência enviada ao destinatário|O URL que foi enviado ao utilizador para analisar o pedido de transferência|

## <a name="supported-subscription-types"></a>Tipos de subscrições suportadas

Pode pedir a propriedade da faturação dos tipos de subscrição listados abaixo.

- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\* 
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* A subscrição Enterprise DevTest será faturada à tarifa pay as you go após a transferência. Qualquer desconto oferecido como parte da oferta do Enterprise DevTest através do EA do cliente não estará disponível para o parceiro do programa CSP.

## <a name="additional-information"></a>Informações adicionais

A secção seguinte fornece informações adicionais sobre como transferir subscrições.

### <a name="no-service-downtime"></a>Nenhum tempo de inatividade do serviço

Os serviços do Azure na subscrição continuam em execução sem quaisquer interrupções. Apenas realizamos a transição da relação de faturação para as subscrições do Azure que o utilizador seleciona para transferir.

### <a name="disabled-subscriptions"></a>Subscrições desativadas

As subscrições desativadas não podem ser transferidas. As subscrições devem estar no estado ativo para transferir a propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos das subscrições, como VMs, discos e transferência de sites.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Os produtos do Azure Marketplace que estão disponíveis para as subscrições que são geridas por Fornecedores de Solução Cloud são transferidos juntamente com as subscrições. As subscrições que têm produtos do Azure Marketplace que não estão ativados para os CSPs não podem ser transferidas.

### <a name="azure-reservations-transfer"></a>Transferência das Reservas do Azure

As Reservas do Azure não são movidas automaticamente com as subscrições. [Contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as Reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Durante a transição, o acesso a utilizadores, grupos ou principais de serviço existentes atribuído com o [RBAC (controlo de acesso baseado em funções) do Azure](../role-based-access-control/overview.md) não é afetado. O parceiro não ficará com novos acessos RBAC para as subscrições.  

Os parceiros devem colaborar com o cliente para obterem acesso às subscrições.  Os parceiros precisam de ter acesso de [Administração em Nome de – AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) ou de [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) para abrir pedidos de suporte.

### <a name="azure-support-plan"></a>Plano de suporte do Azure

O suporte do Azure não é transferido com as subscrições. Se o utilizador transferir todas as subscrições do Azure, peça-lhe para cancelar o plano de suporte. Após a transferência, o parceiro CSP é responsável pelo suporte. O cliente deverá colaborar com o parceiro CSP para eventuais pedidos de suporte.  

### <a name="charges-for-transferred-subscription"></a>Custos da subscrição transferida

O proprietário da faturação original das subscrições é responsável por quaisquer custos que foram comunicados até ao ponto em que a transferência foi concluída. É responsável pelos custos comunicados a partir do momento da transferência e posteriormente. Podem existir custos incorridos antes da transferência, mas que foram relatados mais tarde. Esses custos aparecem na fatura.

### <a name="cancel-a-transfer-request"></a>Cancelar um pedido de transferência

Pode cancelar o pedido de transferência até que o pedido seja aprovado ou recusado. Para cancelar o pedido de transferência, aceda à [página de detalhes da transferência](#check-the-transfer-request-status) e selecione Cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Transferência de Software como Serviço (SaaS)

Os produtos SaaS não são transferidos com as subscrições. Peça ao utilizador para [Contactar o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade da faturação dos produtos SaaS. Juntamente com a propriedade da faturação, o utilizador também pode transferir a propriedade do recurso. A propriedade do recurso permite executar operações de gestão, como eliminar e visualizar os detalhes do produto. O utilizador deve ser um proprietário de recursos no produto SaaS para transferir a propriedade dos recursos.

### <a name="additional-approval-for-certain-customers"></a>Aprovação adicional para determinados clientes

Alguns pedidos de transição de clientes podem exigir um processo adicional de análise da Microsoft devido à natureza da atual estrutura de inscrição do Enterprise do cliente. O parceiro será notificado sobre estes requisitos ao tentar enviar um convite aos clientes. Pede-se aos parceiros que colaborem com o Gestor de Desenvolvimento de Parceiros e com a Equipa da conta do cliente para concluir este processo de análise.

### <a name="azure-subscription-directory"></a>Diretório de subscrições do Azure 
O diretório das subscrições do Azure que são transferidas tem de corresponder ao diretório do cliente que foi selecionado durante o estabelecimento da relação com o CSP. 

Se estes dois diretórios não corresponderem, as subscrições não poderão ser transferidas. Precisará de estabelecer uma nova relação de revendedor de CSP com o cliente ao selecionar o diretório das subscrições do Azure ou de alterar o diretório das subscrições do Azure para corresponder ao diretório da relação do CSP com o cliente. Para obter mais informações, veja [Associar uma subscrição existente ao diretório do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- A propriedade da faturação das subscrições do Azure é transferida para si. Controle os custos destas subscrições no [portal do Azure.](https://portal.azure.com)

- Trabalhe com o cliente para obter acesso às subscrições do Azure transferidas. [Gira o acesso aos recursos do Azure com o RBAC e o portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
