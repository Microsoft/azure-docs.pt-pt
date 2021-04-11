---
title: Sistema de Proteção de Dados do Cliente para o Microsoft Azure
description: Visão geral técnica do Bloqueio do Cliente para o Microsoft Azure, que fornece controlo sobre o acesso do fornecedor de nuvem quando a Microsoft pode precisar de aceder aos dados dos clientes.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 04/05/2021
ms.openlocfilehash: 80d1e4f39d69f761b801ccec834c0228057e4847
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448530"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Sistema de Proteção de Dados do Cliente para o Microsoft Azure

> [!NOTE]
> Para utilizar esta funcionalidade, a sua organização deve ter um [plano de suporte Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Desenvolvedor.**

A maioria das operações, suporte e resolução de problemas realizadas por pessoal e sub-processadores da Microsoft não requerem acesso aos dados dos clientes. Nas raras circunstâncias em que esse acesso é necessário, o Customer Lockbox para o Microsoft Azure fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. É usado em casos em que um engenheiro da Microsoft precisa de aceder aos dados dos clientes, seja em resposta a um bilhete de suporte iniciado pelo cliente ou a um problema identificado pela Microsoft.

Este artigo abrange como ativar o Lockbox do Cliente e como os pedidos do Lockbox são iniciados, rastreados e armazenados para avaliações e auditorias posteriores.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios"></a>Serviços e cenários apoiados

### <a name="general-availability"></a>Disponibilidade Geral
Os seguintes serviços estão geralmente disponíveis para o Lockbox do cliente:

- API Management do Azure
- Serviço de Aplicações do Azure
- Serviços Cognitivos do Azure
- Registo de Contentores do Azure
- Base de Dados do Azure para MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure Data Factory
- Base de Dados do Azure para PostgreSQL
- Funções do Azure
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Armazenamento do Azure
- Base de Dados SQL do Azure
- Transferências de subscrição do Azure
- Azure Synapse Analytics
- Máquinas virtuais em Azure (abrangendo o acesso remoto ao ambiente de trabalho, acesso a despejos de memória e discos geridos)

### <a name="public-preview"></a>Pré-visualização pública
Os seguintes serviços estão atualmente em pré-visualização para o Lockbox do cliente:

- Azure Machine Learning
- Azure Batch

## <a name="enable-customer-lockbox"></a>Ativar o bloqueio do cliente

Pode agora ativar o Bloqueio do Cliente a partir do [módulo Administração](https://aka.ms/customerlockbox/administration) na lâmina do Bloqueio do Cliente.  

> [!NOTE]
> Para ativar o Bloqueio do Cliente, a conta de utilizador precisa de ter a [função de Administrador Global atribuída](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>Fluxo de trabalho

Os passos seguintes descrevem um fluxo de trabalho típico para um pedido de bloqueio do cliente.

1. Alguém numa organização tem um problema com a carga de trabalho do Azure.

2. Depois desta pessoa resolver o problema, mas não consegue corrigi-lo, abrem um bilhete de apoio do [portal Azure.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) O bilhete é atribuído a um Engenheiro de Apoio ao Cliente da Azure.

3. Um Engenheiro de Suporte Azure revê o pedido de serviço e determina os próximos passos para resolver o problema.

4. Se o engenheiro de suporte não conseguir resolver o problema utilizando ferramentas padrão e dados gerados por serviço, o próximo passo é solicitar permissões elevadas utilizando um serviço de acesso Just-In-Time (JIT). Este pedido pode ser do engenheiro de suporte original ou de um engenheiro diferente porque o problema é escalado para a equipa de Azure DevOps.

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

8. A notificação por e-mail fornece um link para a lâmina **de bloqueio** do cliente no módulo Administração. Utilizando este link, o aprovador designado assina no portal Azure para visualizar quaisquer pedidos pendentes que a sua organização tenha para o Bloqueio do Cliente:

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

Os pedidos de bloqueio do cliente não são desencadeados nos seguintes cenários de suporte à engenharia:

- Cenários de emergência que ficam fora dos procedimentos operacionais padrão. Por exemplo, uma grande paralisação do serviço requer atenção imediata para recuperar ou restaurar os serviços num cenário inesperado ou imprevisível. Estes eventos de "break glass" são raros e, na maioria dos casos, não requerem qualquer acesso aos dados dos clientes para resolver.
- Um engenheiro da Microsoft acede à plataforma Azure como parte da resolução de problemas e é inadvertidamente exposto aos dados dos clientes. Por exemplo, a Equipa de Rede do Azure executa a resolução de problemas que resulta numa captura de pacotes num dispositivo de rede. É raro que tais cenários resultem no acesso a quantidades significativas de dados dos clientes. Os clientes podem proteger ainda mais os seus dados através da utilização de em trânsito e em repouso encriptação.

Os pedidos de bloqueio do cliente também não são desencadeados por exigências legais externas de dados. Para mais detalhes, consulte a discussão dos pedidos de [dados](https://www.microsoft.com/trust-center/) do governo no Microsoft Trust Center.

## <a name="next-steps"></a>Passos seguintes

O Customer Lockbox está disponível para todos os clientes que tenham um plano de [suporte Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Programador.** Pode ativar o Bloqueio do Cliente a partir do [módulo Administração](https://aka.ms/customerlockbox/administration) na lâmina do Bloqueio do Cliente.

Os pedidos de bloqueio do cliente são iniciados por um engenheiro da Microsoft se esta ação for necessária para progredir num caso de suporte.
