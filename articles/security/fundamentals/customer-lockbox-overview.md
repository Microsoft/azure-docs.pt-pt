---
title: Caixa de bloqueio de clientes para o Microsoft Azure
description: Visão geral técnica do Customer Lockbox para o Microsoft Azure, que fornece controlo sobre o acesso do fornecedor de nuvem quando a Microsoft pode precisar de aceder aos dados dos clientes.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/04/2019
ms.openlocfilehash: abc16ae7f7ab8bf15173248a6e7668e689e127de
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561974"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Caixa de bloqueio de clientes para o Microsoft Azure

> [!NOTE]
> Para utilizar esta funcionalidade, a sua organização deve ter um plano de [suporte Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Developer**.

O Bloqueio de Clientes do Microsoft Azure fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. É usado nos casos em que um engenheiro da Microsoft precisa de aceder aos dados dos clientes durante um pedido de suporte.

Este artigo abrange a forma como os pedidos de bloqueio do cliente são iniciados, rastreados e armazenados para avaliações e auditorias posteriores.

O Bloqueio do Cliente está agora geralmente disponível e atualmente está ativado para acesso remoto a máquinas virtuais.

## <a name="workflow"></a>Fluxo de trabalho

Os seguintes passos delineiam um fluxo de trabalho típico para um pedido de bloqueio de cliente.

1. Alguém numa organização tem um problema com a sua carga de trabalho azure.

2. Depois desta pessoa resolver o problema, mas não consegue consertá-lo, abrem um bilhete de apoio do [portal Azure.](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) O bilhete é atribuído a um Engenheiro de Apoio ao Cliente Azure.

3. Um Engenheiro de Apoio Azure analisa o pedido de serviço e determina os próximos passos para resolver o problema.

4. Se o engenheiro de suporte não conseguir resolver o problema utilizando ferramentas padrão e telemetria, o próximo passo é solicitar permissões elevadas utilizando um serviço de acesso Just-In-Time (JIT). Este pedido pode ser do engenheiro de suporte original. Ou, pode ser de um engenheiro diferente porque o problema é agravado para a equipa azure DevOps.

5. Após o pedido de acesso ser submetido pelo Engenheiro Azure, o serviço Just-In-Time avalia o pedido tendo em conta fatores como:
    - O âmbito do recurso
    - Se o solicitador é uma identidade isolada ou usando a autenticação multifactor
    - Níveis de permissões

    Com base na regra do JIT, este pedido também pode incluir uma aprovação dos aprovadores internos da Microsoft. Por exemplo, o aprovador pode ser o suporte ao cliente ou o Gestor de DevOps.

6. Quando o pedido requer acesso direto aos dados do cliente, é iniciado um pedido de bloqueio de cliente. Por exemplo, acesso remoto à máquina virtual de um cliente.

    O pedido encontra-se agora num estado notificado pelo **Cliente,** aguardando a aprovação do cliente antes de conceder acesso.

7. Na organização de clientes, o utilizador que tem a [função Proprietário](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) para a subscrição do Azure recebe um e-mail da Microsoft, para notificá-los sobre o pedido de acesso pendente. Para pedidos de bloqueio de cliente, esta pessoa é o aprovador designado.

    E-mail de exemplo:

    ![Lockbox do cliente Azure - notificação por e-mail](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. A notificação por e-mail fornece um link para a lâmina do **Customer Lockbox** no portal Azure. Utilizando este link, o aprovador designado inscreve-se no portal Azure para ver quaisquer pedidos pendentes que a sua organização tenha para o Customer Lockbox:

    ![Lockbox de cliente Azure - página de aterragem](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   O pedido permanece na fila do cliente por quatro dias. Após este período, o pedido de acesso expira automaticamente e não é concedido acesso aos engenheiros da Microsoft.

9. Para obter os detalhes do pedido pendente, o aprovador designado pode selecionar o pedido de caixa de bloqueio de **pedidos pendentes:**

    ![Lockbox de cliente Azure - consulte o pedido pendente](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. O aprovador designado também pode selecionar o **ID DE PEDIDO DE SERVIÇO** para visualizar o pedido de bilhete de apoio que foi criado pelo utilizador original. Esta informação fornece contexto para o porquê do Microsoft Support estar envolvido, e o histórico do problema relatado. Por exemplo:

    ![Lockbox de cliente Azure - consulte o pedido de bilhete de apoio](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Após a revisão do pedido, o aprovador designado seleciona **Aprovar** ou **Negar:**

    ![Lockbox de cliente Azure - selecione Aprovar ou Negar](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Como resultado da seleção:
    - **Aprovação**: O acesso é concedido ao engenheiro da Microsoft. O acesso é concedido por um período de incumprimento de oito horas.
    - **Negue**: O pedido de acesso elevado do engenheiro da Microsoft é rejeitado e não são tomadas mais medidas.

Para efeitos de auditoria, as ações tomadas neste fluxo de trabalho estão registadas nos registos de pedidos do [Customer Lockbox](#auditing-logs).

## <a name="auditing-logs"></a>Registos de auditoria

Os registos do Lockbox do cliente são armazenados em registos de atividade. No portal Azure, selecione Registos de **Atividades** para visualizar informações de auditoria relacionadas com pedidos de bloqueio de clientes. Pode filtrar para ações específicas, tais como:
- **Negar pedido de lockbox**
- **Criar pedido de lockbox**
- **Aprovar pedido de caixa de bloqueio**
- **Pedido de bloqueio expiração**

Em forma de exemplo:

![Lockbox de cliente Azure - registos de atividade](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Serviços e cenários suportados na disponibilidade geral

Os seguintes serviços e cenários estão atualmente na disponibilidade geral para o Bloqueio do Cliente.

### <a name="remote-desktop-access-to-virtual-machines"></a>Acesso remoto a máquinas virtuais

O Bloqueio do Cliente está atualmente ativado para pedidos de acesso remoto a máquinas virtuais. As seguintes cargas de trabalho são suportadas:
- Plataforma como serviço (PaaS) - Azure Cloud Services (função web e papel de trabalhador)
- Infraestrutura como serviço (IaaS) - Windows e Linux (apenas Gestor de Recursos Azure)
- Conjunto de escala de máquina virtual - Windows e Linux

> [!NOTE]
> As instâncias Clássicas IaaS não são suportadas pelo Customer Lockbox. Se tiver cargas de trabalho em casos IaaS Classic, recomendamos que as emigra dos modelos de implementação Classic para Resource Manager. Para obter instruções, consulte a [migração apoiada pela Plataforma de recursos IaaS do clássico para o Gestor de Recursos Azure.](../../virtual-machines/windows/migration-classic-resource-manager-overview.md)

#### <a name="detailed-audit-logs"></a>Registos de auditoria detalhados

Para cenários que envolvam acesso remoto ao ambiente de trabalho, pode utilizar registos de eventos do Windows para rever as ações tomadas pelo engenheiro da Microsoft. Considere utilizar o Azure Security Center para recolher os registos dos seus eventos e copiar os dados para o seu espaço de trabalho para análise. Para mais informações, consulte a [recolha de dados no Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Serviços e cenários suportados em pré-visualização

Os seguintes serviços estão agora em pré-visualização para o Bloqueio do Cliente:

- Storage do Azure

- BD SQL do Azure

- Azure Data Explorer

- Máquinas virtuais (abrangendo agora também o acesso a depósitos de memória e discos geridos)

- Transferências de subscrição do Azure

Para ativar o Bloqueio de Clientes para estas ofertas de pré-visualização para a sua organização, inscreva-se no [Customer Lockbox para Pré-visualização pública Azure](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Exclusões

Os pedidos de bloqueio do cliente não são desencadeados nos seguintes cenários de suporte à engenharia:

- Um engenheiro da Microsoft precisa de fazer uma atividade que não seja dos procedimentos operacionais padrão. Por exemplo, recuperar ou restaurar serviços em cenários inesperados ou imprevisíveis.

- Um engenheiro da Microsoft acede à plataforma Azure como parte da resolução de problemas e inadvertidamente tem acesso aos dados dos clientes. Por exemplo, a Equipa de Rede Azure realiza uma resolução de problemas que resulta numa captura de pacotes num dispositivo de rede. No entanto, se o cliente encriptou os dados durante o seu transporte, o engenheiro não consegue ler os dados.

## <a name="next-steps"></a>Passos seguintes

O Bloqueio do Cliente está automaticamente disponível para todos os clientes que tenham um plano de [suporte Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **Developer**.

Quando tiver um plano de suporte elegível, não é necessária qualquer ação por si para ativar o Bloqueio do Cliente. Os pedidos de bloqueio de clientes são iniciados por um engenheiro da Microsoft se esta ação for necessária para progredir num bilhete de suporte que é arquivado por alguém da sua organização.
