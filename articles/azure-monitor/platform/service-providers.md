---
title: O Azure Monitor para fornecedores de serviços | Documentos da Microsoft
description: Monitor do Azure pode ajudar a fornecedores de serviços geridos (MSPs), grandes empresas, fornecedores independentes de Software (ISVs) e fornecedores de serviços de alojamento, gerirem e monitorizar os servidores no local do cliente ou na infraestrutura de nuvem.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.openlocfilehash: 97d8d6fac93ebabac8fb319ce2f1ab8719f5f86b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452658"
---
# <a name="azure-monitor-for-service-providers"></a>O Azure Monitor para fornecedores de serviços
Áreas de trabalho do log Analytics no Azure Monitor podem ajudar a fornecedores de serviços geridos (MSPs), grandes empresas, fornecedores independentes de software (ISVs) e alojamento fornecedores de serviços, gerir e monitorizar os servidores no local do cliente ou na infraestrutura de nuvem. 

As grandes empresas compartilham diversas semelhanças com fornecedores de serviços, especialmente quando existe uma equipa de TI centralizada, que é responsável por gerenciar IT para muitas unidades de negócios diferentes. Para simplificar, este documento utiliza o termo *fornecedor de serviços* , mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e fornecedores de serviços que fazem parte do [fornecedor de soluções Cloud (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) programa, o Log Analytics no Azure Monitor é um dos serviços do Azure disponíveis em [subscrições do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Arquiteturas para fornecedores de serviços

Áreas de trabalho do log Analytics fornecem um método para o administrador controlar o fluxo e o isolamento dos registos e criar uma arquitetura de registo que atende suas necessidades empresariais específicas. [Este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) explica as considerações gerais em torno do gerenciamento de área de trabalho. Fornecedores de serviços tem considerações adicionais.

Existem três arquiteturas possíveis para fornecedores de serviços em relação a áreas de trabalho do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuído - os registos são armazenados em áreas de trabalho localizadas no inquilino do cliente 

Nesta arquitetura, uma área de trabalho é implementada no inquilino do cliente que é utilizado para todos os registos de cliente. Os administradores do fornecedor de serviço são concedidos acesso a esta área de trabalho utilizar [utilizadores do Azure Active Directory convidado (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Os administradores do fornecedor de serviço tem de mudar para o diretório do seu cliente no portal do Azure para poder aceder a estas áreas de trabalho.

As vantagens desta arquitetura são:
* O cliente pode gerir o acesso aos logs com os seus próprios [acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Cada cliente pode ter diferentes configurações para a sua área de trabalho, como a retenção e capping de dados.
* Isolamento entre os clientes de regulamentação e conformidade.
* A cobrança para cada área de trabalho será revertida para a subscrição do cliente.
* Os registos podem ser recolhidos de todos os tipos de recursos, não apenas baseados em agente. Por exemplo, registos de auditoria do Azure.

As desvantagens dessa arquitetura são:
* É mais difícil para o fornecedor de serviço gerir um grande número de inquilinos do cliente de uma só vez.
* Os administradores do fornecedor de serviços tem de ser aprovisionado no diretório do cliente.
* O fornecedor de serviço não é possível analisar os dados através de seus clientes.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central - registos são armazenados numa área de trabalho localizada no inquilino do fornecedor de serviço

Nesta arquitetura, os registos não são armazenados em inquilinos do cliente, mas apenas num local central dentro de uma das subscrições do fornecedor de serviços. Os agentes que estão instalados em VMs do cliente estão configurados para enviar os registos para esta área de trabalho com o ID de área de trabalho e a chave secreta.

As vantagens desta arquitetura são:
* É fácil de gerenciar um grande número de clientes e integrá-las para vários sistemas de back-end.
* O fornecedor de serviços tem propriedade completa sobre os registos e de vários artefactos, como as funções e consultas guardadas.
* O fornecedor de serviços pode efetuar análises em todos os seus clientes.

As desvantagens dessa arquitetura são:
* Esta arquitetura é aplicável apenas para dados baseados em agente da VM, não abordará origens de dados de recursos de infraestrutura PaaS, SaaS e no Azure.
* Talvez seja difícil separar os dados entre os clientes quando eles são mesclados numa única área de trabalho. O método apenas bom para o fazer consiste em utilizar o nome de domínio completamente qualificado do computador (FQDN) ou por meio de subscrição do Azure ID. 
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única fatura e as mesmas definições de retenção e a configuração.
* Recursos de infraestrutura do Azure e PaaS serviços como o diagnóstico do Azure e os registos de auditoria do Azure requer a área de trabalho para estar no mesmo inquilino, como o recurso, portanto, eles não é possível enviar os registos para a área de trabalho central.
* Todos os agentes VM de todos os clientes serão autenticados para a área de trabalho central, utilizando o mesmo ID de área de trabalho e a chave. Não existe nenhum método para bloquear os registos de um cliente específico sem interromper a outros clientes.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Híbrido - registos são armazenados na área de trabalho localizada no inquilino do cliente e alguns deles são extraídos para uma localização central.

A terceira mistura de arquitetura entre as duas opções. Baseia-se na arquitetura distribuída primeiro onde os registos são locais para cada cliente, mas usando algum mecanismo para criar um repositório central de registos. Uma parte dos registos é extraída para uma localização central para relatórios e análises. Essa parte pode ser pequeno número de tipos de dados ou um resumo da atividade como estatísticas diárias.

Existem duas opções para implementar os registos numa localização central:

1. Área de trabalho central: O fornecedor de serviços pode criar uma área de trabalho no seu inquilino e utilizar um script que utiliza a [API de consulta](https://dev.loganalytics.io/) com o [API de recolha de dados](../../azure-monitor/platform/data-collector-api.md) para colocar os dados de vários espaços de trabalho para esse local central. Outra opção, que não seja um script, é usar [do Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI, como um local central: Power BI pode atuar como o local central quando vários espaços de trabalho exportar dados, ao mesmo através da integração entre a área de trabalho do Log Analytics e [Power BI](../../azure-monitor/platform/powerbi.md). 


## <a name="next-steps"></a>Próximos Passos
* Automatizar a criação e configuração de espaços de trabalho usando [modelos do Resource Manager](template-workspace-configuration.md)
* Automatizar a criação de áreas de trabalho com [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 
* Uso [alertas](../../azure-monitor/platform/alerts-overview.md) para integrar com sistemas existentes
* Gerar relatórios de resumo com [Power BI](../../azure-monitor/platform/powerbi.md)
* Reveja o processo de [configurando o Log Analytics e o Power BI para monitorizar vários clientes CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
