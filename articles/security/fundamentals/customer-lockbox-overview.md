---
title: Sistema de Proteção de Dados do Cliente para o Microsoft Azure
description: Visão geral técnica do Bloqueio do Cliente para o Microsoft Azure, que fornece controlo sobre o acesso do fornecedor de nuvem quando a Microsoft pode precisar de aceder aos dados dos clientes.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/15/2020
ms.openlocfilehash: 52cb5ac5423aac0599ba2827667ee670dde286a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331663"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Sistema de Proteção de Dados do Cliente para o Microsoft Azure

> [!NOTE]
> Para utilizar esta funcionalidade, a sua organização deve ter um [plano de suporte Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Desenvolvedor.**

O Sistema de Proteção de Dados do Cliente para o Microsoft Azure proporciona uma interface para os clientes reverem e aprovarem ou rejeitarem pedidos de acesso a dados do cliente. É utilizado nos casos em que um engenheiro da Microsoft precisa de aceder aos dados do cliente durante um pedido de suporte.

Este artigo cobre a forma como os pedidos do Customer Lockbox são iniciados, rastreados e armazenados para posteriores revisões e auditorias.

O Sistema de Proteção de Dados do Cliente está agora disponível para o público geral e atualmente ativado para o acesso de ambiente de trabalho remoto a máquinas virtuais.

## <a name="supported-services-and-scenarios-in-preview"></a>Serviços e cenários apoiados na pré-visualização

Os seguintes serviços estão atualmente em pré-visualização para o Sistema de Proteção de Dados do Cliente:

- Gestão de API
- Serviço de Aplicações do Azure
- Serviços Cognitivos
- Container Registry
- Base de Dados do Azure para MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure Data Factory
- Base de Dados do Azure para PostgreSQL
- Funções do Azure
- HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Storage do Azure
- BD SQL do Azure
- Transferências de subscrição do Azure
- Azure Synapse Analytics
- Máquinas virtuais (agora também abrangem o acesso a informações do estado da memória e discos geridos)

Para ativar o Bloqueio do Cliente para estas ofertas de pré-visualização para a sua organização, inscreva-se no [Customer Lockbox para pré-visualização pública Azure](https://aka.ms/customerlockbox/insiderprogram).

## <a name="supported-services-and-scenarios-in-general-availability"></a>Serviços e cenários apoiados em disponibilidade geral

Os seguintes serviços e cenários estão atualmente em disponibilidade geral para o Lockbox do Cliente.

### <a name="remote-desktop-access-to-virtual-machines"></a>Acesso ao ambiente de trabalho remoto para máquinas virtuais

O Sistema de Proteção de Dados do Cliente está atualmente ativado para pedidos de acesso de ambiente de trabalho remoto a máquinas virtuais. As seguintes cargas de trabalho são suportadas:
- Plataforma como serviço (PaaS) – Serviços Cloud do Azure (função da Web e função de trabalho)
- Infraestrutura como serviço (IaaS) – Windows e Linux (apenas o Azure Resource Manager)
- Conjunto de dimensionamento de máquinas virtuais – Windows e Linux

> [!NOTE]
> As instâncias iaaS Classic não são suportadas pelo Lockbox do Cliente. Se tiver cargas de trabalho em casos clássicos do IaaS, recomendamos que as emigres de modelos de implementação Classic para Resource Manager. Para obter instruções, veja [Migração suportada por plataforma dos recursos IaaS clássicos para o Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Registos de auditoria detalhados

Para cenários que envolvem o acesso ao ambiente de trabalho remoto, pode utilizar registos de eventos do Windows para rever as ações executadas pelo engenheiro da Microsoft. Considere utilizar o Centro de Segurança do Azure para recolher os registos de eventos e copiar os dados para a área de trabalho para análise. Para obter mais informações, veja [Recolha de dados no Centro de Segurança do Azure](../../security-center/security-center-enable-data-collection.md).

## <a name="workflow"></a>Fluxo de trabalho

Os passos seguintes descrevem um fluxo de trabalho típico para um pedido de bloqueio do cliente.

1. Alguém numa organização tem um problema com a carga de trabalho do Azure.

2. Depois desta pessoa resolver o problema, mas não consegue corrigi-lo, abrem um bilhete de apoio do [portal Azure.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) O bilhete é atribuído a um Engenheiro de Apoio ao Cliente da Azure.

3. Um Engenheiro de Suporte Azure revê o pedido de serviço e determina os próximos passos para resolver o problema.

4. Se o engenheiro de suporte não conseguir resolver o problema utilizando ferramentas padrão e telemetria, o próximo passo é solicitar permissões elevadas utilizando um serviço de acesso Just-In-Time (JIT). Este pedido pode ser do engenheiro de suporte original. Ou pode ser de um engenheiro diferente porque o problema é escalado para a equipa de Azure DevOps.

5. Após o pedido de acesso ser submetido pelo Engenheiro Azure, o serviço Just-In-Time avalia o pedido tendo em conta fatores como:
    - O âmbito do recurso
    - Se o solicitador é uma identidade isolada ou usando a autenticação de vários fatores
    - Níveis de permissões

    Com base na regra JIT, este pedido também pode incluir uma aprovação dos Approvers Internos da Microsoft. Por exemplo, o aprovador pode ser o líder de suporte ao Cliente ou o Gestor de DevOps.

6. Quando o pedido requer acesso direto aos dados do cliente, é iniciado um pedido de Bloqueio de Cliente. Por exemplo, acesso remoto ao ambiente de trabalho à máquina virtual de um cliente.

    O pedido encontra-se agora num estado **notificado do Cliente,** aguardando a aprovação do cliente antes de conceder acesso.

7. Na organização do cliente, o utilizador que tem o [papel de Proprietário](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) para a subscrição do Azure recebe um e-mail da Microsoft, para os notificar sobre o pedido de acesso pendente. Para pedidos de bloqueio de clientes, esta pessoa é o aprovador designado.

    Exemplo de e-mail:

    ![Azure Customer Lockbox - notificação de e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. A notificação por e-mail fornece um link para a lâmina **do Bloqueio** do Cliente no portal Azure. Utilizando este link, o aprovador designado assina no portal Azure para visualizar quaisquer pedidos pendentes que a sua organização tenha para o Bloqueio do Cliente:

    ![Azure Customer Lockbox - página de aterragem](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   O pedido permanece na fila do cliente por quatro dias. Após este período, o pedido de acesso expira automaticamente e não é concedido acesso aos engenheiros da Microsoft.

9. Para obter os detalhes do pedido pendente, o aprovador designado pode selecionar o pedido de lockbox a partir de **Pedidos Pendentes**:

    ![Azure Customer Lockbox - veja o pedido pendente](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. O aprovador designado também pode selecionar o **ID DO PEDIDO DE SERVIÇO** para visualizar o pedido de bilhete de suporte que foi criado pelo utilizador original. Estas informações fornecem contexto para o porquê do Microsoft Support estar envolvido e o histórico do problema relatado. Por exemplo:

    ![Azure Customer Lockbox - veja o pedido de bilhete de apoio](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Após a revisão do pedido, o aprovador designado seleciona **Aprovar** ou **Negar:**

    ![Azure Customer Lockbox - selecione Aprovar ou Negar](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Como resultado da seleção:
    - **Aprovação:** O acesso é concedido ao engenheiro da Microsoft. O acesso é concedido por um período de incumprimento de oito horas.
    - **Negar:** O pedido de acesso elevado do engenheiro da Microsoft é rejeitado e não são tomadas mais medidas.

Para efeitos de auditoria, as ações tomadas neste fluxo de trabalho são registadas nos [registos de pedidos de bloqueio do cliente](#auditing-logs).

## <a name="auditing-logs"></a>Registos de auditoria

Os registos do Sistema de Proteção de Dados do Cliente são armazenados nos registos de atividade. No portal Azure, selecione **Registos de Atividade para** visualizar informações de auditoria relacionadas com pedidos de Bloqueio de Clientes. Pode filtrar ações específicas, como:
- **Recusar o Pedido do Sistema de Proteção de Dados**
- **Criar o Pedido do Sistema de Proteção de Dados**
- **Aprovar o Pedido do Sistema de Proteção de Dados**
- **Expiração do pedido de bloqueio**

Em forma de exemplo:

![Azure Customer Lockbox - registos de atividade](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Integração do Sistema de Proteção de Dados do Cliente com a Referência de Segurança do Azure

Introduzimos um novo controlo de base[(3.13)](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)no Azure Security Benchmark que cobre a aplicabilidade do Lockbox do Cliente. Os clientes podem agora aproveitar o benchmark para rever a aplicabilidade do Customer Lockbox para um serviço.

## <a name="exclusions"></a>Exclusões

Os pedidos do Sistema de Proteção de Dados do Cliente não são acionados nos seguintes cenários de suporte de engenharia:

- Um engenheiro da Microsoft precisa de realizar uma atividade que está fora dos procedimentos operacionais padrão. Por exemplo, para recuperar ou restaurar serviços em cenários inesperados ou imprevisíveis.

- Um engenheiro da Microsoft acede à plataforma do Azure como parte da resolução de problemas e, inadvertidamente, tem acesso aos dados do cliente. Por exemplo, a Equipa de Rede do Azure executa a resolução de problemas que resulta numa captura de pacotes num dispositivo de rede. No entanto, se o cliente encriptou os dados enquanto estavam em trânsito, o engenheiro não poderá ler os dados.

## <a name="next-steps"></a>Passos seguintes

O Customer Lockbox está automaticamente disponível para todos os clientes que tenham um plano de [suporte Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Programador.**

Quando tem um plano de suporte elegível, não é necessária nenhuma ação por si para ativar o Lockbox do Cliente. Os pedidos de Bloqueio do Cliente são iniciados por um engenheiro da Microsoft se esta ação for necessária para progredir num bilhete de suporte que é arquivado por alguém da sua organização.
