---
title: Cofre de cliente para o Microsoft Azure
description: Descrição geral técnica do Cofre de cliente do Microsoft Azure, que fornece controlo sobre o acesso do fornecedor de cloud quando a Microsoft poderá ter de aceder aos dados dos clientes.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 05/07/2019
ms.openlocfilehash: 468e392cd2c45d79cbb24f8d737a6e83fbcd2725
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079274"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Cofre de cliente para o Microsoft Azure

> [!NOTE]
> Para utilizar esta funcionalidade, sua organização tem de ter uma [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **programador**.

Cofre de cliente para o Microsoft Azure fornece uma interface para os clientes rever e aprovar ou rejeitar pedidos de acesso de dados do cliente. É utilizado em casos onde um engenheiro da Microsoft tem de aceder aos dados dos clientes durante um pedido de suporte.

Este artigo aborda como pedidos de Cofre de cliente são iniciados, acompanhou e armazenou análises posteriores e de auditorias.

Cofre de cliente está agora em disponibilidade geral e atualmente ativada para acesso de ambiente de trabalho remoto a máquinas virtuais.

## <a name="workflow"></a>Fluxo de trabalho

Os passos seguintes descrevem um fluxo de trabalho típico para um pedido de Cofre de cliente.

1. Alguém numa organização tem um problema com a sua carga de trabalho do Azure.

2. Depois desta pessoa resolve o problema, mas não é possível corrigi-lo, eles abrirem um pedido de suporte do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). O pedido de suporte é atribuído a um engenheiro de suporte de cliente do Azure.

3. Um engenheiro de suporte do Azure analisa o pedido de serviço e determina os passos seguintes para resolver o problema.

4. Se o engenheiro de suporte não é possível resolver o problema com as ferramentas padrão e telemetria, o próximo passo é solicitar permissões elevadas com um serviço de acesso de ativaram (JIT). Este pedido pode ser de engenheiro de suporte original. Em alternativa, pode ser de um engenheiro diferente porque o problema é escalado para a equipa de DevOps do Azure.

5. Depois do acesso a pedido ser submetido pelo engenheiro do Azure, Just-In-Time serviço avalia a solicitação levando em consideração fatores como:
    - O escopo do recurso
    - Se o autor do pedido é uma identidade isolada ou através da autenticação multifator
    - Níveis de permissões
    
    Com base na regra de JIT, este pedido também pode incluir uma aprovação de aprovadores interno do Microsoft. Por exemplo, o aprovador pode ser o líder de suporte ao cliente ou o Gestor de DevOps.

6. Quando o pedido requer acesso direto aos dados dos clientes, um pedido de Cofre de cliente é iniciado. Por exemplo, ambiente de trabalho acesso remoto máquina de virtual de um cliente.
    
    O pedido está agora num **notificado do cliente** Estado, a aguardar a aprovação do cliente antes de conceder acesso.

7. Na organização do cliente, o utilizador que tem o [função de proprietário](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) para o Azure subscrição recebe uma mensagem de e-mail da Microsoft, para notificá-los sobre o pedido de acesso pendente. Para pedidos de Cofre de cliente, essa pessoa é o aprovador designado.
    
    E-mail de exemplo:
    
    ![Cofre de cliente do Azure - notificação por e-mail](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. A notificação de e-mail fornece uma ligação para o **Cofre de cliente** painel no portal do Azure. Utilizar esta ligação, o aprovador designado inicia sessão no portal do Azure para ver quaisquer pedidos que a sua organização tem para o Cofre de cliente pendentes:
    
    ![Cofre de cliente do Azure - página de destino](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   O pedido permanece na fila de cliente para quatro dias. Após esse período, o pedido de acesso expira automaticamente e sem acesso é concedido a engenheiros da Microsoft.

9. Para obter os detalhes do pedido pendente, o aprovador designado pode selecionar o pedido de cofre a partir **pedidos pendentes**:
    
    ![Cofre de cliente do Azure - ver o pedido pendente](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. Também pode selecionar o aprovador designado a **ID de pedido de serviço** para ver o pedido de pedido de suporte que foi criado pelo usuário original. Estas informações fornecem o contexto de por que participam Support da Microsoft e o histórico do problema reportado. Por exemplo:
    
    ![Cofre de cliente do Azure - ver o pedido de pedido de suporte](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. Depois de rever o pedido, o aprovador designado seleciona **aprovar** ou **negar**:
    
    ![Cofre de cliente do Azure - selecione aprovar ou negar](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    Como resultado da seleção:
    - **Aprovar**:  O acesso é concedido para o engenheiro da Microsoft. O acesso é concedido durante um período padrão de oito horas.
    - **Negar**: O pedido de acesso elevado, o engenheiro da Microsoft é rejeitado e nenhuma outra ação é executada.

Para fins de auditoria, as ações executadas neste fluxo de trabalho são registadas no [registos de pedidos de cliente cofre](#auditing-logs).

## <a name="auditing-logs"></a>Registos de auditoria

Registos do Cofre de cliente são armazenados nos registos de atividades. No portal do Azure, selecione **registos de atividades** para ver informações de auditoria relacionados com pedidos de Cofre de cliente. Pode filtrar para ações específicas, tais como:
- **Negar o pedido de cofre**
- **Criar pedido de cofre**
- **Aprovar pedido de cofre**
- **Expiração de pedido de cofre**

Por exemplo:

![Cofre de cliente do Azure – registos de atividades](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Serviços suportados e cenários

Os seguintes serviços e cenários encontram atualmente em disponibilidade geral para o Cofre de cliente.

### <a name="remote-desktop-access-to-virtual-machines"></a>Acesso de ambiente de trabalho remoto a máquinas virtuais

Cofre de cliente está atualmente ativada para pedidos de acesso de ambiente de trabalho remoto para máquinas virtuais. São suportadas as seguintes cargas de trabalho:
- Plataforma como serviço (PaaS) - versão 1
- Infraestrutura como serviço (IaaS) - Windows e Linux (apenas no Azure Resource Manager)
- Conjunto de dimensionamento de máquinas virtuais - Windows e Linux

> [!NOTE]
> Instâncias de IaaS Classic não são suportadas pelo Cofre de cliente. Se tiver cargas de trabalho em execução nas instâncias de IaaS clássico, recomendamos que migre do clássico para modelos de implementação do Resource Manager. Para obter instruções, consulte [migração suportada por plataforma de recursos de IaaS do clássico para o Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Registos de auditoria detalhados

Para cenários que envolvem o acesso de ambiente de trabalho remoto, pode utilizar os registos de eventos do Windows para rever as ações executadas pelo engenheiro da Microsoft. Considere utilizar o Centro de segurança do Azure para recolher os registos de eventos e copiar os dados para a área de trabalho para análise. Para obter mais informações, consulte [recolha de dados no Centro de segurança do Azure](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Exclusões

Pedidos de Cofre de cliente não são acionados nos seguintes cenários de suporte de engenharia:

- Um engenheiro da Microsoft tem de fazer uma atividade que se encontre fora procedimentos operacionais padrão. Por exemplo, para recuperar ou restaurar os serviços em cenários de imprevisíveis ou inesperados.

- Um engenheiro da Microsoft acessa a plataforma do Azure como parte da solução de problemas e inadvertidamente tem acesso aos dados dos clientes. Por exemplo, a equipe de rede do Azure efetua a resolução de problemas que resulta numa captura de pacotes num dispositivo de rede. No entanto, se o cliente encriptados os dados enquanto estava em trânsito, o engenheiro não é possível ler os dados.

## <a name="next-steps"></a>Passos Seguintes

Cofre de cliente está automaticamente disponível para todos os clientes que possuam uma [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **programador**.

Quando tiver um plano de suporte elegíveis, é necessária nenhuma ação por si para ativar o Cofre de cliente. Os pedidos de Cofre de cliente são iniciados por um engenheiro da Microsoft, se esta ação é necessária para um pedido de suporte é arquivado de alguém na sua organização de progresso.
