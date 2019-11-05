---
title: Sistema de Proteção de Dados do Cliente para Microsoft Azure
description: Visão geral técnica do Sistema de Proteção de Dados do Cliente para Microsoft Azure, que fornece controle sobre o acesso do provedor de nuvem quando a Microsoft pode precisar acessar os dados do cliente.
author: cabailey
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 11/04/2019
ms.openlocfilehash: 7c0409d48876a0f830366381c2a46821c4aa03a0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466417"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Sistema de Proteção de Dados do Cliente para Microsoft Azure

> [!NOTE]
> Para usar esse recurso, sua organização deve ter um [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **desenvolvedor**.

Sistema de Proteção de Dados do Cliente para Microsoft Azure fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente. Ele é usado nos casos em que um engenheiro da Microsoft precisa acessar os dados do cliente durante uma solicitação de suporte.

Este artigo aborda como Sistema de Proteção de Dados do Cliente solicitações são iniciadas, controladas e armazenadas para revisões e auditorias posteriores.

O Sistema de Proteção de Dados do Cliente agora está disponível para o público geral e atualmente habilitado para acesso à área de trabalho remota a máquinas virtuais.

## <a name="workflow"></a>Fluxo de trabalho

As etapas a seguir descrevem um fluxo de trabalho típico para uma solicitação de Sistema de Proteção de Dados do Cliente.

1. Alguém de uma organização tem um problema com sua carga de trabalho do Azure.

2. Depois que essa pessoa solucionar o problema, mas não puder corrigi-lo, ele abrirá um tíquete de suporte da [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). O tíquete é atribuído a um engenheiro de suporte ao cliente do Azure.

3. Um engenheiro de suporte do Azure revisa a solicitação de serviço e determina as próximas etapas para resolver o problema.

4. Se o engenheiro de suporte não puder solucionar o problema usando ferramentas e telemetria padrão, a próxima etapa será solicitar permissões elevadas usando um serviço de acesso JIT (just-in-time). Essa solicitação pode ser do engenheiro de suporte original. Ou, pode ser de um engenheiro diferente porque o problema é escalonado para a equipe do Azure DevOps.

5. Depois que a solicitação de acesso é enviada pelo engenheiro do Azure, o serviço just-in-time avalia a solicitação levando em consideração os fatores como:
    - O escopo do recurso
    - Se o solicitante é uma identidade isolada ou usando a autenticação multifator
    - Níveis de permissões
    
    Com base na regra JIT, essa solicitação também pode incluir uma aprovação de aprovadores internos da Microsoft. Por exemplo, o aprovador pode ser o líder de atendimento ao cliente ou o gerente DevOps.

6. Quando a solicitação requer acesso direto aos dados do cliente, uma solicitação de Sistema de Proteção de Dados do Cliente é iniciada. Por exemplo, acesso à área de trabalho remota à máquina virtual de um cliente.
    
    A solicitação está agora em um estado **notificado do cliente** , aguardando a aprovação do cliente antes de conceder acesso.

7. Na organização do cliente, o usuário que tem a [função proprietário](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) para a assinatura do Azure recebe um email da Microsoft para notificá-los sobre a solicitação de acesso pendente. Para solicitações de Sistema de Proteção de Dados do Cliente, essa pessoa é o aprovador designado.
    
    Email de exemplo:
    
    ![Sistema de Proteção de Dados do Cliente do Azure-notificação por email](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. A notificação por email fornece um link para a folha **sistema de proteção de dados do cliente** na portal do Azure. Usando esse link, o aprovador designado entra no portal do Azure para exibir as solicitações pendentes que sua organização tem para Sistema de Proteção de Dados do Cliente:
    
    ![Sistema de Proteção de Dados do Cliente do Azure – página inicial](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)
    
   A solicitação permanece na fila de clientes por quatro dias. Após esse período, a solicitação de acesso expira automaticamente e nenhum acesso é concedido aos engenheiros da Microsoft.

9. Para obter os detalhes da solicitação pendente, o aprovador designado pode selecionar a solicitação de lockbox de **solicitações pendentes**:
    
    ![Sistema de Proteção de Dados do Cliente do Azure – exibir a solicitação pendente](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. O aprovador designado também pode selecionar a **ID de solicitação de serviço** para exibir a solicitação de tíquete de suporte que foi criada pelo usuário original. Essas informações fornecem contexto sobre por que Suporte da Microsoft está envolvido e o histórico do problema relatado. Por exemplo:
    
    ![Sistema de Proteção de Dados do Cliente do Azure – exibir a solicitação de tíquete de suporte](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Depois de revisar a solicitação, o aprovador designado seleciona **aprovar** ou **negar**:
    
    ![Sistema de Proteção de Dados do Cliente do Azure – selecione aprovar ou negar](./media/customer-lockbox-overview/customer-lockbox-approval.png)
    
    Como resultado da seleção:
    - **Aprovar**: o acesso é concedido ao engenheiro da Microsoft. O acesso é concedido por um período padrão de oito horas.
    - **Deny**: a solicitação de acesso elevado pelo engenheiro da Microsoft é rejeitada e nenhuma ação adicional é executada.

Para fins de auditoria, as ações executadas nesse fluxo de trabalho são registradas [sistema de proteção de dados do cliente logs de solicitação](#auditing-logs).

## <a name="auditing-logs"></a>Logs de auditoria

Os logs de Sistema de Proteção de Dados do Cliente são armazenados nos logs de atividade. No portal do Azure, selecione **logs de atividade** para exibir informações de auditoria relacionadas a sistema de proteção de dados do cliente solicitações. Você pode filtrar ações específicas, como:
- **Negar solicitação de lockbox**
- **Criar solicitação de lockbox**
- **Aprovar solicitação de lockbox**
- **Expiração da solicitação de lockbox**

Em forma de exemplo:

![Logs de atividades de Sistema de Proteção de Dados do Cliente do Azure](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Serviços e cenários com suporte em disponibilidade geral

Os seguintes serviços e cenários estão atualmente em disponibilidade geral para Sistema de Proteção de Dados do Cliente.

### <a name="remote-desktop-access-to-virtual-machines"></a>Acesso à área de trabalho remota a máquinas virtuais

O Sistema de Proteção de Dados do Cliente está habilitado no momento para solicitações de acesso à área de trabalho remota para máquinas virtuais. Há suporte para as seguintes cargas de trabalho:
- PaaS (plataforma como serviço)-serviços de nuvem do Azure (função Web e função de trabalho)
- IaaS (infraestrutura como serviço)-Windows e Linux (somente Azure Resource Manager)
- Conjunto de dimensionamento de máquinas virtuais-Windows e Linux

> [!NOTE]
> As instâncias clássicas do IaaS não são suportadas pelo Sistema de Proteção de Dados do Cliente. Se você tiver cargas de trabalho em execução em instâncias clássicas de IaaS, recomendamos migrá-las dos modelos de implantação clássico para o Resource Manager. Para obter instruções, consulte [migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Logs de auditoria detalhados

Para cenários que envolvem acesso à área de trabalho remota, você pode usar logs de eventos do Windows para examinar as ações executadas pelo engenheiro da Microsoft. Considere usar a central de segurança do Azure para coletar seus logs de eventos e copiar os dados para o espaço de trabalho para análise. Para obter mais informações, consulte [coleta de dados na central de segurança do Azure](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Serviços e cenários com suporte na visualização

Os serviços a seguir agora estão atualmente em visualização para Sistema de Proteção de Dados do Cliente:

- Armazenamento do Azure 

- BD SQL do Azure 

- Azure Data Explorer 

- Máquinas virtuais (agora também abrangendo o acesso a despejos de memória e discos gerenciados) 

- Transferências de assinatura do Azure

Para habilitar Sistema de Proteção de Dados do Cliente para essas ofertas de visualização para sua organização, Inscreva-se para [sistema de proteção de dados do cliente para visualização pública do Azure](https://aka.ms/customerlockbox/insiderprogram).


## <a name="exclusions"></a>Exclusões

Sistema de Proteção de Dados do Cliente solicitações não são disparadas nos seguintes cenários de suporte de engenharia:

- Um engenheiro da Microsoft precisa fazer uma atividade que está fora dos procedimentos operacionais padrão. Por exemplo, para recuperar ou restaurar serviços em cenários inesperados ou imprevisíveis.

- Um engenheiro da Microsoft acessa a plataforma do Azure como parte da solução de problemas e, inadvertidamente, tem acesso aos dados do cliente. Por exemplo, a equipe de rede do Azure executa a solução de problemas que resulta em uma captura de pacote em um dispositivo de rede. No entanto, se o cliente criptografou os dados enquanto estava em trânsito, o engenheiro não poderá ler os dados.

## <a name="next-steps"></a>Passos seguintes

Sistema de Proteção de Dados do Cliente está automaticamente disponível para todos os clientes que têm um [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **desenvolvedor**.

Quando você tem um plano de suporte qualificado, nenhuma ação é exigida por você para habilitar Sistema de Proteção de Dados do Cliente. Sistema de Proteção de Dados do Cliente solicitações são iniciadas por um engenheiro da Microsoft se essa ação for necessária para progredir um tíquete de suporte que é arquivado de alguém em sua organização.
