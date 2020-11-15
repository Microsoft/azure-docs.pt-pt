---
title: Notas de lançamento para O Centro de Segurança Azure
description: Uma descrição do que é novo e mudou no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: c0333f9faeae99ee83beda381f77f4f95b0a9192
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636125"
---
# <a name="whats-new-in-azure-security-center"></a>O que há de novo no Centro de Segurança Azure?

O Centro de Segurança está em desenvolvimento ativo e recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece-lhe informações sobre novas funcionalidades, correções de bugs e funcionalidades preprecadas.

Esta página é atualizada com frequência, por isso revisite-a com frequência. 

Para saber mais sobre as mudanças *planeadas* que estão a chegar em breve ao Centro de Segurança, consulte [as próximas alterações importantes para o Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Se procura itens com mais de seis meses, vai encontrá-los no Arquivo para o [que há de novo no Azure Security Center.](release-notes-archive.md)


## <a name="november-2020"></a>Novembro de 2020

As atualizações em novembro incluem:

- [29 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 adicionado ao painel de conformidade regulamentar do Centro de Segurança](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)
- [Experiência de provisionamento automático melhorada e expandida](#auto-provisioning-experience-improved-and-expanded)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure

A azure Security Benchmark é o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. [Saiba mais sobre a Referência de Segurança do Azure](../security/benchmarks/introduction.md).

As seguintes 29 recomendações de pré-visualização foram adicionadas ao Centro de Segurança para aumentar a cobertura deste benchmark.

As recomendações de pré-visualização não tornam um recurso insalubre, e não estão incluídas nos cálculos da sua pontuação segura. Remedia-os sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação. Saiba mais sobre como responder a estas recomendações em [recomendações remedias no Azure Security Center](security-center-remediate-recommendations.md).

| Controlo de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Encriptar dados em trânsito              | - A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL<br>- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL<br>- O TLS deve ser atualizado para a versão mais recente da sua aplicação API<br>- O TLS deve ser atualizado para a versão mais recente da sua aplicação de função<br>- O TLS deve ser atualizado para a versão mais recente da sua aplicação web<br>- FTPS deve ser exigido na sua App API<br>- FTPS deve ser exigido na sua app de função<br>- FTPS deve ser exigido na sua Web App                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gerir acessos e permissões        | - As aplicações web devem solicitar um certificado SSL para todos os pedidos de entrada<br>- Identidade gerida deve ser usada na sua App API<br>- Identidade gerida deve ser usada na sua função App<br>- Identidade gerida deve ser usada na sua web App                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restringir o acesso não autorizado à rede | - O ponto final privado deve ser ativado para servidores PostgreSQL<br>- O ponto final privado deve ser ativado para servidores MariaDB<br>- O ponto final privado deve ser ativado para servidores MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Permitir a auditoria e a exploração madeireira          | - Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementar as melhores práticas de segurança    | - A azure Backup deve ser ativado para máquinas virtuais<br>- Cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para a MariaDB<br>- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para o MySQL<br>- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para postgreSQL<br>- PHP deve ser atualizado para a versão mais recente para a sua aplicação API<br>- PHP deve ser atualizado para a versão mais recente para a sua aplicação web<br>- A Java deve ser atualizada para a versão mais recente da sua aplicação API<br>- A Java deve ser atualizada para a versão mais recente da sua aplicação de função<br>- Java deve ser atualizado para a versão mais recente para a sua aplicação web<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação API<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação de função<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação web<br>- A retenção de auditoria para servidores SQL deve ser definida para pelo menos 90 dias |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre a Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre as aplicações AZURE API](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicações de função Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre as aplicações web Azure](../app-service/overview.md)
- [Saiba mais sobre a Azure Database for MariaDB](../mariadb/overview.md)
- [Saiba mais sobre a Base de Dados Azure para o MySQL](../mysql/overview.md)
- [Saiba mais sobre a Base de Dados Azure para PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 adicionado ao painel de conformidade regulamentar do Centro de Segurança

A norma NIST SP 800-171 R2 está agora disponível como uma iniciativa integrada para uso com o painel de conformidade regulamentar do Azure Security Center. Os mapeamentos para os controlos são descritos em [Detalhes da iniciativa integrada NIST SP 800-171 R2 Conformidade Regulamentar.](../governance/policy/samples/nist-sp-800-171-r2.md) 

Para aplicar a norma às suas subscrições e monitorizar continuamente o seu estado de conformidade, utilize as instruções para [Personalizar o conjunto de normas no seu painel de conformidade regulamentar](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="A norma NIST SP 800 171 R2 no painel de conformidade regulamentar do Centro de Segurança":::

Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Lista de recomendações agora inclui filtros

Pode agora filtrar a lista de recomendações de segurança de acordo com uma série de critérios. No exemplo seguinte, a lista de recomendações foi filtrada para mostrar recomendações que:

- estão **geralmente disponíveis** (ou seja, não pré-visualização)
- são para **contas de armazenamento**
- apoiar a **reparação rápida da correção**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtros para a lista de recomendações":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Experiência de provisionamento automático melhorada e expandida

A funcionalidade de provisionamento automático ajuda a reduzir a sobrecarga de gestão instalando as extensões necessárias em novos VMs - e existentes - para que possam beneficiar das proteções do Centro de Segurança. 

À medida que o Azure Security Center cresce, mais extensões foram desenvolvidas e o Centro de Segurança pode monitorizar uma lista maior de tipos de recursos. As ferramentas de fornecimento automático foram agora expandidas para apoiar extensões adicionais e tipos de recursos, aproveitando as capacidades da Política Azure.

Pode agora configurar o provisionamento automático de:

- Agente do Log Analytics
- (Novo) Azure Policy Add-on para Kubernetes
- (Novo) Agente de dependência da Microsoft

Saiba mais em [agentes de provisionamento automático e extensões do Azure Security Center](security-center-enable-data-collection.md).

## <a name="october-2020"></a>Outubro de 2020

As atualizações em outubro incluem:
- [Avaliação de vulnerabilidade para máquinas no local e multi-nuvens (pré-visualização)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Recomendação do Azure Firewall adicionada (pré-visualização)](#azure-firewall-recommendation-added-preview)
- [As gamas IP autorizadas devem ser definidas na recomendação dos Serviços Kubernetes atualizada com uma correção rápida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Painel de conformidade regulamentar agora inclui opção para remover normas](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft.Security/securityStatuses removido do Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Avaliação de vulnerabilidade para máquinas no local e multi-nuvens (pré-visualização)

[O Azure Defender para](defender-for-servers-introduction.md)o scanner integrado de avaliação de vulnerabilidades dos servidores (alimentado por Qualys) agora verifica servidores ativados a Azure Arc.

Quando tiver ativado o Azure Arc nas suas máquinas não-Azure, o Security Center oferecer-se-á para implementar o scanner integrado de vulnerabilidade neles - manual e à escala.

Com esta atualização, pode libertar o poder do **Azure Defender para os servidores** consolidarem o seu programa de gestão de vulnerabilidades em todos os seus ativos Azure e não-Azure.

Principais capacidades:

- Monitorização do estado de provisionamento do scanner VA (avaliação de vulnerabilidade) nas máquinas Azure Arc
- Provisionando o agente VA integrado às máquinas de Windows e Arú azul desprotegidas (manual e à escala)
- Receber e analisar vulnerabilidades detetadas de agentes implantados (manualmente e à escala)
- Experiência unificada para VMs Azure e máquinas Azure Arc

[Saiba mais sobre a implementação do scanner de vulnerabilidade integrado nas suas máquinas híbridas.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Saiba mais sobre os servidores ativados do Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Recomendação do Azure Firewall adicionada (pré-visualização)

Foi adicionada uma nova recomendação para proteger todas as suas redes virtuais com a Azure Firewall.

A recomendação, **as redes virtuais devem ser protegidas pelo Azure Firewall,** aconselha-o a restringir o acesso às suas redes virtuais e a prevenir potenciais ameaças utilizando o Azure Firewall.

Saiba mais sobre [a Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>As gamas IP autorizadas devem ser definidas na recomendação dos Serviços Kubernetes atualizada com uma correção rápida

A recomendação **De que os intervalos de IP autorizados sejam definidos nos Serviços Kubernetes** tem agora uma opção de correção rápida.

Para mais detalhes sobre esta recomendação e todas as outras recomendações do Centro de Segurança, consulte [recomendações de Segurança - um guia de referência](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="As gamas IP autorizadas devem ser definidas na recomendação dos Serviços Kubernetes com a opção de correção rápida":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Painel de conformidade regulamentar agora inclui opção para remover normas

O painel de conformidade regulamentar do Security Center fornece informações sobre a sua postura de conformidade com base na forma como está a cumprir controlos e requisitos específicos de conformidade.

O painel inclui um conjunto padrão de normas regulamentares. Se algum dos padrões fornecidos não for relevante para a sua organização, é agora um processo simples para simplesmente removê-los da UI para uma subscrição. As normas só podem ser removidas ao nível da *subscrição;* não o âmbito do grupo de gestão.

Saiba mais [na remoção de um padrão do seu painel de instrumentos.](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard)


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft.Security/securityStatuses removido do Azure Resource Graph (ARG)

O Azure Resource Graph é um serviço em Azure que é projetado para fornecer uma exploração eficiente de recursos com a capacidade de consultar em escala através de um determinado conjunto de subscrições para que possa governar eficazmente o seu ambiente. 

Para o Azure Security Center, pode utilizar o ARG e a [Língua De Consulta de Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança. Por exemplo:

- Utilizações de inventário de ativos (ARG)
- Documentamos uma consulta de amostra ARG sobre como [identificar contas sem autenticação de vários fatores (MFA) ativada](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Dentro da ARG existem tabelas de dados para utilizar nas suas consultas.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer e as tabelas disponíveis":::

> [!TIP]
> A documentação ARG lista todas as tabelas disponíveis na [tabela Azure Resource Graph e referência do tipo de recurso.](../governance/resource-graph/reference/supported-tables-resources.md)

A partir desta atualização, a tabela **Microsoft.Security/securityStatuses** foi removida. A API de segurançastatuses ainda está disponível.

A substituição de dados pode ser utilizada pela tabela Microsoft.Security/Assessments.

A grande diferença entre microsoft.Security/securityStatuses e Microsoft.Security/Assessments é que, embora a primeira mostre agregação de avaliações, os segundos detenha um único recorde para cada um.

Por exemplo, microsoft.Security/securityStatuses devolveria um resultado com uma série de duas avaliações de política:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Considerando que a Microsoft.Security/Assessments manterá um registo para cada avaliação de política da seguinte forma:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exemplo de conversão de uma consulta ARG existente utilizando segurançaSés para utilizar agora o quadro de avaliações:**

Consulta que refere Índices de Segurança:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Consulta de substituição do quadro de avaliações:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Saiba mais nos seguintes links:
- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Linguagem de Consulta Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Setembro de 2020

As atualizações em setembro incluem:
- [O Centro de Segurança tem um novo visual!](#security-center-gets-a-new-look)
- [Azure Defender lançado](#azure-defender-released)
- [Azure Defender para Key Vault está geralmente disponível](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender para proteção de armazenamento de ficheiros e ADLS Gen2 está geralmente disponível](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [As ferramentas de inventário de ativos estão agora geralmente disponíveis](#asset-inventory-tools-are-now-generally-available)
- [Desative uma constatação específica de vulnerabilidade para análises de registos de contentores e máquinas virtuais](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Isentar um recurso de uma recomendação](#exempt-a-resource-from-a-recommendation)
- [Conectores AWS e GCP no Centro de Segurança trazem uma experiência multi-nuvem](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Pacote de recomendação de proteção da carga de trabalho kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Os resultados da avaliação da vulnerabilidade estão agora disponíveis em exportação contínua](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Prevenir as configurações erradas de segurança, aplicando recomendações na criação de novos recursos](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Recomendações do grupo de segurança da rede melhoradas](#network-security-group-recommendations-improved)
- [Recomendação precída da AKS "As políticas de segurança do pod devem ser definidas nos Serviços Kubernetes"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Notificações de email do Azure Security Center melhoradas](#email-notifications-from-azure-security-center-improved)
- [A pontuação segura não inclui recomendações de pré-visualização](#secure-score-doesnt-include-preview-recommendations)
- [As recomendações agora incluem um indicador de gravidade e o intervalo de frescura](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>O Centro de Segurança tem um novo visual!

Lançámos uma atualização das páginas do portal do Centro de Segurança. As novas páginas incluem uma nova página de visão geral, bem como dashboards para pontuação segura, inventário de ativos e Azure Defender.

A página de visão geral redesenhada tem agora um azulejo para aceder à pontuação segura, inventário de ativos e dashboards Azure Defender. Possui ainda um azulejo que liga ao painel de conformidade regulamentar.

Saiba mais sobre a [página geral.](overview-page.md)


### <a name="azure-defender-released"></a>Azure Defender lançado

**O Azure Defender** é a plataforma de proteção da carga de trabalho em nuvem (CWPP) integrada no Security Center para uma proteção avançada, inteligente, das suas cargas de trabalho Azure e híbridas. Substitui a opção padrão de preços do Security Center. 

Quando ativa o Azure Defender a partir da área de Preços e Definições do Azure Security Center, os **seguintes planos** do Defender estão todos ativados simultaneamente e fornecem defesas abrangentes para as camadas de computação, dados e serviço do seu ambiente:

- [Azure Defender para servidores](defender-for-servers-introduction.md)
- [Azure Defender para Serviço de Aplicações](defender-for-app-service-introduction.md)
- [Azure Defender para Armazenamento](defender-for-storage-introduction.md)
- [Azure Defender para SQL](defender-for-sql-introduction.md)
- [Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender para registos de contentor](defender-for-container-registries-introduction.md)

Cada um destes planos é explicado separadamente na documentação do Centro de Segurança.

Com o seu dashboard dedicado, o Azure Defender fornece alertas de segurança e proteção avançada de ameaças para máquinas virtuais, bases de dados SQL, contentores, aplicações web, sua rede, e muito mais.

[Saiba mais sobre o Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender para Key Vault está geralmente disponível

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. 

**O Azure Defender for Key Vault** fornece proteção de ameaças avançadas e nativas do Azure para o Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. Por extensão, o Azure Defender for Key Vault está, consequentemente, a proteger muitos dos recursos dependentes das suas contas Key Vault.

O plano opcional agora é GA. Esta funcionalidade estava em pré-visualização como "proteção avançada de ameaças para Azure Key Vault".

Além disso, as páginas key Vault no portal Azure agora incluem uma página de **Segurança** dedicada para recomendações e alertas **do Centro de Segurança.**

Saiba mais no [Azure Defender para Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender para proteção de armazenamento de ficheiros e ADLS Gen2 está geralmente disponível 

**O Azure Defender for Storage** deteta atividade potencialmente prejudicial nas suas contas de Armazenamento Azure. Os seus dados podem ser protegidos quer sejam armazenados como recipientes blob, partilhas de ficheiros ou lagos de dados.

O suporte para [ficheiros Azure](../storage/files/storage-files-introduction.md) e [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) está disponível em geral.

A partir de 1 de outubro de 2020, começaremos a cobrar pela proteção de recursos nestes serviços.

Saiba mais no [Azure Defender para armazenamento.](defender-for-storage-introduction.md)


### <a name="asset-inventory-tools-are-now-generally-available"></a>As ferramentas de inventário de ativos estão agora geralmente disponíveis

A página de inventário de ativos do Azure Security Center fornece uma única página para visualizar a postura de segurança dos recursos que ligou ao Centro de Segurança.

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como remediar essas vulnerabilidades.

Quando algum recurso tiver recomendações pendentes, aparecerão no inventário.

Saiba mais em [Explorar e gerir os seus recursos com ferramentas de inventário de ativos e gestão.](asset-inventory.md)



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Desative uma constatação específica de vulnerabilidade para análises de registos de contentores e máquinas virtuais

O Azure Defender inclui scanners de vulnerabilidade para digitalizar imagens no registo do seu contentor Azure e nas suas máquinas virtuais.

Se tiver uma necessidade organizacional de ignorar uma descoberta, em vez de remediar, pode desativá-la opcionalmente. As descobertas desativadas não afetam a sua pontuação segura ou geram ruídos indesejados.

Quando uma descoberta corresponde aos critérios que definiu nas suas regras de desativação, não constará na lista de resultados.

Esta opção está disponível nas páginas de detalhes das recomendações para:

- **Vulnerabilidades em imagens do Registo de Contentores de Azure devem ser remediadas**
- **As vulnerabilidades nas suas máquinas virtuais devem ser remediadas**

Saiba mais em [Desativar as descobertas específicas para as suas imagens de recipientes](defender-for-container-registries-usage.md#disable-specific-findings-preview) e [desativar as descobertas específicas para as suas máquinas virtuais.](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)


### <a name="exempt-a-resource-from-a-recommendation"></a>Isentar um recurso de uma recomendação

Ocasionalmente, um recurso será listado como insalubre em relação a uma recomendação específica (e, portanto, reduzindo a sua pontuação segura) mesmo que considere que não deveria ser. Pode ter sido remediado por um processo não rastreado pelo Centro de Segurança. Ou talvez a sua organização tenha decidido aceitar o risco para esse recurso específico. 

Nesses casos, pode criar uma regra de isenção e garantir que o recurso não está listado entre os recursos pouco saudáveis no futuro. Estas regras podem incluir justificações documentadas, tal como descrito abaixo.

Saiba mais em [Isentar um recurso de recomendações e pontuação segura](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Conectores AWS e GCP no Centro de Segurança trazem uma experiência multi-nuvem

Com cargas de trabalho em nuvem geralmente abrangendo várias plataformas de nuvem, os serviços de segurança na nuvem devem fazer o mesmo.

O Azure Security Center protege agora as cargas de trabalho em Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

A bordo das suas contas AWS e GCP no Security Center, integra o AWS Security Hub, o GCP Security Command e o Azure Security Center. 

Saiba mais em [Ligar as suas contas AWS ao Azure Security Center](quickstart-onboard-aws.md) e ligar as suas contas [GCP ao Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Pacote de recomendação de proteção da carga de trabalho kubernetes

Para garantir que as cargas de trabalho de Kubernetes estão seguras por defeito, o Security Center está a adicionar recomendações de endurecimento de nível de Kubernetes, incluindo opções de aplicação com o controlo de admissão de Kubernetes.

Quando tiver instalado o addon Azure Policy para Kubernetes no seu cluster AKS, todos os pedidos para o servidor API de Kubernetes serão monitorizados contra o conjunto de boas práticas pré-definido antes de serem persistidos no cluster. Em seguida, pode configurar para impor as melhores práticas e mandatá-las para futuras cargas de trabalho.

Por exemplo, pode ordenar que os contentores privilegiados não sejam criados, e quaisquer pedidos futuros para o fazer serão bloqueados.

Saiba mais sobre [as melhores práticas de proteção da carga de trabalho utilizando o controlo de admissão de Kubernetes.](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Os resultados da avaliação da vulnerabilidade estão agora disponíveis em exportação contínua

Utilize a exportação contínua para transmitir os seus alertas e recomendações em tempo real para Azure Event Hubs, Log Analytics workspaces ou Azure Monitor. A partir daí, pode integrar estes dados com SIEMs (como Azure Sentinel, Power BI, Azure Data Explorer, entre outros.

As ferramentas integradas de avaliação de vulnerabilidades do Security Center devolvem as conclusões sobre os seus recursos como recomendações acccáveis dentro de uma recomendação de "pais", como "Vulnerabilidades nas suas máquinas virtuais devem ser remediadas". 

Os resultados de segurança estão agora disponíveis para exportação através de exportação contínua quando seleciona recomendações e permite a opção **de conclusões de segurança.**

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir resultados de segurança alternando na configuração contínua da exportação" :::

Páginas relacionadas:

- [Solução integrada de avaliação de vulnerabilidade do Security Center para máquinas virtuais Azure](deploy-vulnerability-assessment-vm.md)
- [Solução integrada de avaliação de vulnerabilidade do Centro de Segurança para imagens do Registo de Contentores do Azure](defender-for-container-registries-usage.md)
- [Exportação contínua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Prevenir as configurações erradas de segurança, aplicando recomendações na criação de novos recursos

As configurações erradas de segurança são a principal causa de incidentes de segurança. O Centro de Segurança tem agora a capacidade de ajudar a *prevenir* a má configuração de novos recursos no que diz respeito a recomendações específicas. 

Esta função pode ajudar a manter as suas cargas de trabalho seguras e estabilizar a sua pontuação segura.

A aplicação de uma configuração segura, baseada numa recomendação específica, é oferecida em dois modos:

- Usando o efeito **Deny** da Política Azure, você pode impedir que recursos insalubres sejam criados

- Utilizando a opção **Enforce,** pode tirar partido do efeito **DeployIfNotExist** da política da Azure e remediar automaticamente recursos não conformes após a criação
 
Isto está disponível para recomendações de segurança selecionadas e pode ser encontrado no topo da página de detalhes de recursos.

Saiba mais em [Prevenir configurações erradas com recomendações de Cumprir/Negar](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Recomendações do grupo de segurança da rede melhoradas

As seguintes recomendações de segurança relacionadas com grupos de segurança de rede foram melhoradas para reduzir alguns casos de falsos positivos.

- Todas as portas de rede devem ser restringidas no NSG associado ao seu VM
- As portas de gestão devem ser fechadas nas suas máquinas virtuais
- Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede
- As subnetas devem ser associadas a um Grupo de Segurança de Rede


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Recomendação precída da AKS "As políticas de segurança do pod devem ser definidas nos Serviços Kubernetes"

A recomendação de pré-visualização "Pod Security Policies deve ser definida nos Serviços Kubernetes" está a ser depreciada como descrito na documentação do [Serviço Azure Kubernetes.](../aks/use-pod-security-policies.md)

A funcionalidade de política de segurança do pod (pré-visualização), está definida para depreciação e deixará de estar disponível a partir de 15 de outubro de 2020 a favor da Azure Policy for AKS.

Após a deprecação da política de segurança do pod (pré-visualização), tem de desativar a funcionalidade em quaisquer clusters existentes utilizando a funcionalidade prevadida para realizar futuras atualizações de clusters e permanecer dentro do suporte do Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Notificações de email do Azure Security Center melhoradas

Foram melhoradas as seguintes áreas dos e-mails relativos aos alertas de segurança: 

- Acrescentou a capacidade de enviar notificações de email sobre alertas para todos os níveis de gravidade
- Adicionou a capacidade de notificar utilizadores com diferentes funções de RBAC na subscrição
- Estamos a notificar proativamente os proprietários de subscrições por padrão em alertas de alta gravidade (que têm uma alta probabilidade de serem violações genuínas)
- Removemos o campo de números de telefone da página de configuração de notificações de e-mail

Saiba mais em [Configurar notificações de email para alertas de segurança.](security-center-provide-security-contact-details.md)


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A pontuação segura não inclui recomendações de pré-visualização 

O Security Center avalia continuamente os seus recursos, subscrições e organização para questões de segurança. Em seguida, agrega todas as conclusões numa única pontuação para que possa dizer, num ápice, a sua situação de segurança atual: quanto maior for a pontuação, menor o nível de risco identificado.

À medida que novas ameaças são descobertas, novos conselhos de segurança são disponibilizados no Centro de Segurança através de novas recomendações. Para evitar alterações surpresa na sua pontuação segura e para proporcionar um período de carência no qual pode explorar novas recomendações antes de impactar as suas pontuações, as recomendações sinalizadas como **Preview** já não estão incluídas nos cálculos da sua pontuação segura. Devem continuar a ser remediados sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Além disso, as recomendações **de pré-visualização** não tornam um recurso "insalubre".

Um exemplo de uma recomendação de pré-visualização:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com a bandeira de pré-visualização":::

[Saiba mais sobre pontuação segura](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>As recomendações agora incluem um indicador de gravidade e o intervalo de frescura

A página de detalhes para recomendações inclui agora um indicador de intervalo de frescura (sempre que relevante) e uma clara demonstração da gravidade da recomendação.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Página de recomendação mostrando frescura e gravidade":::



## <a name="august-2020"></a>Agosto de 2020

As atualizações em agosto incluem:

- [Inventário de ativos - nova visão poderosa da postura de segurança dos seus ativos](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Suporte adicional para padrão de segurança do Azure Ative Directory (para autenticação de vários fatores)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Recomendação dos diretores de serviço adicionada](#service-principals-recommendation-added)
- [Avaliação da vulnerabilidade em VMs - recomendações e políticas consolidadas](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Novas políticas de segurança AKS adicionadas à iniciativa ASC_default – para utilização apenas por clientes privados de pré-visualização](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventário de ativos - nova visão poderosa da postura de segurança dos seus ativos

O inventário de ativos do Security Center (atualmente em pré-visualização) fornece uma forma de ver a postura de segurança dos recursos que ligou ao Centro de Segurança.

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como remediar essas vulnerabilidades. Quando algum recurso tiver recomendações pendentes, aparecerão no inventário.

Pode utilizar a vista e os seus filtros para explorar os seus dados de postura de segurança e tomar mais ações com base nas suas descobertas.

Saiba mais sobre [o inventário de ativos.](asset-inventory.md)


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Suporte adicional para padrão de segurança do Azure Ative Directory (para autenticação de vários fatores)

O Security Center adicionou suporte total para [falhas de segurança,](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)as proteções de segurança de identidade gratuitas da Microsoft.

Os incumprimentos de segurança fornecem configurações de segurança de identidade pré-configuradas para defender a sua organização de ataques comuns relacionados com a identidade. Incumprimentos de segurança já protegem mais de 5 milhões de inquilinos em geral; 50.000 inquilinos também estão protegidos pelo Centro de Segurança.

O Security Center fornece agora uma recomendação de segurança sempre que identifica uma subscrição do Azure sem falhas de segurança ativadas. Até agora, o Security Center recomendou a autenticação de vários fatores utilizando o acesso condicional, que faz parte da licença premium Azure Ative Directory (AD). Para clientes que usam Azure AD gratuitamente, recomendamos agora permitir incumprimentos de segurança. 

O nosso objetivo é incentivar mais clientes a garantir os seus ambientes em nuvem com MFA, e mitigar um dos maiores riscos que é também o mais impactante para a sua [pontuação segura.](secure-score-security-controls.md)

Saiba mais sobre [os incumprimentos de segurança.](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)


### <a name="service-principals-recommendation-added"></a>Recomendação dos diretores de serviço adicionada

Foi adicionada uma nova recomendação para recomendar que os clientes do Security Center utilizem certificados de gestão para gerir as suas subscrições comutadores para os principais serviços.

A recomendação, **os principais do serviço devem ser usados para proteger as suas subscrições em vez de Certificados de Gestão** aconselha-o a usar os Principais de Serviço ou O Gestor de Recursos Azure para gerir mais de forma mais segura as suas subscrições. 

Saiba mais sobre [aplicações e objetos principais de serviço no Azure Ative Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Avaliação da vulnerabilidade em VMs - recomendações e políticas consolidadas

O Centro de Segurança inspeciona os seus VMs para detetar se estão a executar uma solução de avaliação de vulnerabilidade. Se não for encontrada nenhuma solução de avaliação de vulnerabilidade, o Security Center fornece uma recomendação para simplificar a implementação.

Quando as vulnerabilidades são encontradas, o Centro de Segurança fornece uma recomendação que resume as conclusões para que investigue e remediar se necessário.

Para garantir uma experiência consistente para todos os utilizadores, independentemente do tipo de scanner que estão a usar, un we unificou quatro recomendações para as seguintes duas:

|Recomendação unificada|Alterar descrição|
|----|:----|
|**Uma solução de avaliação de vulnerabilidades deve ser ativada nas suas máquinas virtuais**|Substitui as duas seguintes recomendações:<br> **• •** Permitir a solução de avaliação de vulnerabilidade incorporada em máquinas virtuais (alimentadas por Qualys (agora depreciadas) (Incluída com nível padrão)<br> **• •** A solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais (agora depreciadas) (Níveis standard e gratuitos)|
|**As vulnerabilidades nas suas máquinas virtuais devem ser remediadas**|Substitui as duas seguintes recomendações:<br>**• •** Corrigir vulnerabilidades encontradas nas suas máquinas virtuais (alimentadas por Qualys) (agora depreciadas)<br>**• •** As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades (agora depreciada)|
|||

Agora você usará a mesma recomendação para implementar a extensão de avaliação de vulnerabilidade do Security Center ou uma solução licenciada privada ("BYOL") de um parceiro como Qualys ou Rapid7.

Além disso, quando as vulnerabilidades forem encontradas e reportadas ao Security Center, uma única recomendação irá alertá-lo para as conclusões, independentemente da solução de avaliação de vulnerabilidade que as identificou.

#### <a name="updating-dependencies"></a>Atualização das dependências

Se tiver scripts, consultas ou automatizações referentes às recomendações anteriores ou chaves/nomes de apólices anteriores, utilize as tabelas abaixo para atualizar as referências:

##### <a name="before-august-2020"></a>Antes de agosto de 2020

|Recomendação|Âmbito|
|----|:----|
|**Permitir a solução de avaliação de vulnerabilidade incorporada em máquinas virtuais (alimentadas por Qualys)**<br>Chave: 550e890b-e652-4d22-8274-60b3bdb24c63|Incorporado|
|**Corrigir vulnerabilidades encontradas nas suas máquinas virtuais (alimentadas por Qualys)**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Incorporado|
|**Solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais**<br>Chave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades**<br>Chave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Política|Âmbito|
|----|:----|
|**A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais**<br>ID da política: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Incorporado|
|**As vulnerabilidades devem ser remediadas por uma solução de avaliação de vulnerabilidades**<br>ID da política: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>A partir de agosto de 2020

|Recomendação|Âmbito|
|----|:----|
|**Uma solução de avaliação de vulnerabilidades deve ser ativada nas suas máquinas virtuais**<br>Chave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Incorporado + BYOL|
|**As vulnerabilidades nas suas máquinas virtuais devem ser remediadas**<br>Chave: 1195afff-c881-495e-9bc5-1486211ae03f|Incorporado + BYOL|
||||

|Política|Âmbito|
|----|:----|
|[**A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID da política: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Incorporado + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Novas políticas de segurança AKS adicionadas à iniciativa ASC_default – para utilização apenas por clientes privados de pré-visualização

Para garantir que as cargas de trabalho de Kubernetes estão seguras por defeito, o Security Center está a adicionar políticas de nível de Kubernetes e recomendações de endurecimento, incluindo opções de aplicação com o controlo de admissão de Kubernetes.

A fase inicial deste projeto inclui uma pré-visualização privada e a adição de novas políticas (desativadas por defeito) à iniciativa ASC_default.

Pode ignorar com segurança estas políticas e não haverá impacto no seu ambiente. Se quiser ative-los, inscreva-se para a pré-visualização https://aka.ms/SecurityPrP e selecione a partir das seguintes opções:

1. **Pré-visualização única** – Para juntar apenas esta pré-visualização privada. Mencione explicitamente "ASC Continuous Scan" como a pré-visualização que gostaria de aderir.
1. **Programa em curso** – A ser adicionado a esta e futuras antestreias privadas. Você precisará completar um acordo de perfil e privacidade.


## <a name="july-2020"></a>Julho de 2020

As atualizações em julho incluem:
- [Avaliação de vulnerabilidade para máquinas virtuais já está disponível para imagens não-marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Proteção contra ameaças para o Azure Storage expandido para incluir ficheiros Azure e Azure Data Lake Storage Gen2 (pré-visualização)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Oito novas recomendações para permitir funcionalidades de proteção de ameaças](#eight-new-recommendations-to-enable-threat-protection-features)
- [Melhorias na segurança do contentor - digitalização mais rápida do registo e documentação atualizada](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Controlos de aplicações adaptativos atualizados com uma nova recomendação e suporte para wildcards nas regras de caminho](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Seis políticas para a segurança avançada de dados SQL depreciadas](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Avaliação de vulnerabilidade para máquinas virtuais já está disponível para imagens não-marketplace

Ao implementar uma solução de avaliação de vulnerabilidades, o Security Center efetuou previamente uma verificação de validação antes de ser implementado. O cheque foi para confirmar um mercado SKU da máquina virtual de destino. 

A partir desta atualização, a verificação foi removida e agora pode implementar ferramentas de avaliação de vulnerabilidades para máquinas 'personalizadas' Windows e Linux. As imagens personalizadas são aquelas que modificou a partir das predefinições do mercado.

Embora agora possa implementar a extensão integrada de avaliação de vulnerabilidade (alimentada por Qualys) em muitas mais máquinas, o suporte só está disponível se estiver a utilizar um SISTEMA listado no [Deploy o scanner integrado de vulnerabilidade para VMs de nível padrão](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Saiba mais sobre o [scanner de vulnerabilidade integrado para máquinas virtuais (requer Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Saiba mais sobre a utilização da sua própria solução de avaliação de vulnerabilidades licenciada em privado a partir da Qualys ou Rapid7 na [implementação de uma solução de digitalização de vulnerabilidade de parceiros.](deploy-vulnerability-assessment-vm.md)


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Proteção contra ameaças para o Azure Storage expandido para incluir ficheiros Azure e Azure Data Lake Storage Gen2 (pré-visualização)

A proteção contra ameaças para o Armazenamento Azure deteta atividade potencialmente prejudicial nas suas contas de Armazenamento Azure. O Centro de Segurança exibe alertas quando deteta tentativas de aceder ou explorar as suas contas de armazenamento. 

Os seus dados podem ser protegidos quer sejam armazenados como recipientes blob, partilhas de ficheiros ou lagos de dados.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Oito novas recomendações para permitir funcionalidades de proteção de ameaças

Foram adicionadas oito novas recomendações para fornecer uma forma simples de permitir as funcionalidades de proteção de ameaças do Azure Security Center para os seguintes tipos de recursos: máquinas virtuais, planos de Serviço de Aplicações, servidores de base de dados Azure SQL, servidores SQL em máquinas, contas de armazenamento Azure, clusters de serviço Azure Kubernetes, registos de registos de contentores Azure e cofres da Azure Vault.

As novas recomendações são:

- **A segurança avançada dos dados deve ser ativada nos servidores da Base de Dados Azure SQL**
- **A segurança avançada dos dados deve ser ativada em servidores SQL em máquinas**
- **Proteção avançada de ameaças deve ser ativada nos planos do Azure App Service**
- **A proteção avançada de ameaças deve ser ativada nos registos do Registo de Contentores de Azure**
- **Proteção avançada de ameaças deve ser ativada em cofres Azure Key Vault**
- **A proteção avançada de ameaças deve ser ativada nos clusters de serviços Azure Kubernetes**
- **A proteção avançada de ameaças deve ser ativada nas contas de armazenamento do Azure**
- **A proteção avançada de ameaças deve ser ativada em máquinas virtuais**

Estas novas recomendações pertencem ao controlo de segurança **Enable Azure Defender.**

As recomendações também incluem a capacidade de correção rápida. 

> [!IMPORTANT]
> A reparação de qualquer destas recomendações resultará em encargos para a proteção dos recursos relevantes. Estas taxas começarão imediatamente se tiver recursos relacionados na subscrição atual. Ou no futuro, se os adicionar mais tarde.
> 
> Por exemplo, se não tiver nenhum cluster de Serviço Azure Kubernetes na sua subscrição e ativar a proteção contra ameaças, não serão incorridos quaisquer encargos. Se, no futuro, adicionar um cluster na mesma subscrição, será automaticamente protegido e os encargos começarão nessa altura.

Saiba mais sobre cada um destes na página de referência das [recomendações de segurança](recommendations-reference.md).

Saiba mais sobre [a proteção contra ameaças no Centro de Segurança Azure.](azure-defender.md)




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Melhorias na segurança do contentor - digitalização mais rápida do registo e documentação atualizada

Como parte dos investimentos contínuos no domínio de segurança do contentor, temos o prazer de partilhar uma melhoria significativa de desempenho nas imagens dinâmicas do Security Center de imagens de contentores armazenadas no Registo de Contentores Azure. Os exames normalmente completam-se em aproximadamente dois minutos. Em alguns casos, podem levar até 15 minutos.

Para melhorar a clareza e orientação sobre as capacidades de segurança do Azure Security Center, também refrescamos as páginas de documentação de segurança do contentor. 

Saiba mais sobre a segurança do contentor do Security Center nos seguintes artigos:

- [Visão geral das funcionalidades de segurança do centro de segurança](container-security.md)
- [Detalhes da integração com o Registo de Contentores Azure](defender-for-container-registries-introduction.md)
- [Detalhes da integração com o Serviço Azure Kubernetes](defender-for-kubernetes-introduction.md)
- [Como digitalizar os seus registos e endurecer os seus anfitriões Docker](container-security.md)
- [Alertas de segurança das funcionalidades de proteção contra ameaças para os clusters de serviços Azure Kubernetes](alerts-reference.md#alerts-akscluster)
- [Alertas de segurança das funcionalidades de proteção contra ameaças para os anfitriões do Serviço Azure Kubernetes](alerts-reference.md#alerts-containerhost)
- [Recomendações de segurança para contentores](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Controlos de aplicações adaptativos atualizados com uma nova recomendação e suporte para wildcards nas regras de caminho

A funcionalidade de controlos de aplicações adaptativas recebeu duas atualizações significativas:

* Uma nova recomendação identifica um comportamento potencialmente legítimo que não foi permitido anteriormente. A nova recomendação, **as regras Allowlist na sua política de controlo de aplicações adaptativas devem ser atualizadas,** leva-o a adicionar novas regras à política existente para reduzir o número de falsos positivos nos alertas de violação de controlos de aplicações adaptativos.

* As regras do caminho agora suportam wildcards. A partir desta atualização, pode configurar as regras de caminho permitidas usando wildcards. Há dois cenários apoiados:

    * Usando um wildcard no final de um caminho para permitir todos os executáveis dentro desta pasta e sub-pastas

    * Utilizar um wildcard no meio de um caminho para permitir um nome executável conhecido com um nome de pasta em mudança (por exemplo, pastas pessoais de utilizador com nomes de pastas geradas automaticamente, executáveis, etc.).


[Saiba mais sobre controlos de aplicações adaptativos.](security-center-adaptive-application.md)



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Seis políticas para a segurança avançada de dados SQL depreciadas

Estão a ser depreciadas seis políticas relacionadas com a segurança avançada dos dados para máquinas SQL:

- Os tipos avançados de proteção contra ameaças devem ser definidos como 'All' em definições avançadas de segurança de dados de casos de SQL
- Os tipos avançados de proteção de ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- As definições avançadas de segurança de dados para a ocorrência gerida pelo SQL devem conter um endereço de e-mail para receber alertas de segurança
- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança
- As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados de casos DE SQL
- As notificações por e-mail para administradores e proprietários de subscrições devem ser ativadas nas definições avançadas de segurança de dados do servidor SQL

Saiba mais sobre [políticas incorporadas.](./policy-reference.md)





## <a name="june-2020"></a>Junho de 2020

As atualizações em junho incluem:
- [API de pontuação segura (pré-visualização)](#secure-score-api-preview)
- [Segurança avançada de dados para máquinas SQL (Azul, outras nuvens e on-prem) (pré-visualização)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Duas novas recomendações para implantar o agente Log Analytics nas máquinas Azure Arc (pré-visualização)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Novas políticas para criar configurações contínuas de automação de exportação e fluxo de trabalho em escala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nova recomendação para a utilização de NSGs para proteger máquinas virtuais não-orientadas para a Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Novas políticas para permitir a proteção de ameaças e segurança avançada de dados](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API de pontuação segura (pré-visualização)

Pode agora aceder à sua pontuação através da [pontuação segura API](/rest/api/securitycenter/securescores/) (atualmente em pré-visualização). Os métodos API fornecem a flexibilidade para consultar os dados e construir o seu próprio mecanismo de reporte das suas pontuações seguras ao longo do tempo. Por exemplo, pode utilizar a API **de Pontuações Seguras** para obter a pontuação de uma subscrição específica. Além disso, pode utilizar a API **de Controlos de Pontuação Segura** para listar os controlos de segurança e a pontuação atual das suas subscrições.

Por exemplo, ferramentas externas possibilitadas com a pontuação segura API, consulte [a área de pontuação segura da nossa comunidade GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre [os controlos de pontuação e segurança seguros no Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Segurança avançada de dados para máquinas SQL (Azul, outras nuvens e on-prem) (pré-visualização)

A segurança avançada de dados do Azure Security Center para máquinas SQL protege agora os SqL Servers alojados em Azure, em outros ambientes de nuvem e até mesmo máquinas no local. Isto alarga as proteções para os seus SqL Servers nativos do Azure para suportar totalmente ambientes híbridos.

A segurança avançada de dados fornece uma avaliação de vulnerabilidade e proteção avançada de ameaças para as suas máquinas SQL onde quer que estejam localizadas.

A configuração envolve dois passos:

1. Implantar o agente Log Analytics na máquina de anfitrião do seu SQL Server para fornecer a ligação à conta Azure.

1. Ativar o pacote opcional na página de preços e definições do Security Center.

Saiba mais sobre [a segurança avançada de dados para máquinas SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Duas novas recomendações para implantar o agente Log Analytics nas máquinas Azure Arc (pré-visualização)

Foram adicionadas duas novas recomendações para ajudar a implantar o [Agente Desanalização](../azure-monitor/platform/log-analytics-agent.md) do Log para as suas máquinas Azure Arc e garantir que estão protegidas pelo Azure Security Center:

- **O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Pré-visualização)**
- **O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas em Linux (Pré-visualização)**

Estas novas recomendações aparecerão nos mesmos quatro controlos de segurança que a recomendação existente (relacionada), o **agente de monitorização deve ser instalado nas suas máquinas** : remediar as configurações de segurança, aplicar o controlo adaptativo da aplicação de aplicações, aplicar atualizações do sistema e permitir a proteção do ponto final.

As recomendações também incluem a capacidade de correção rápida para ajudar a acelerar o processo de implementação. 

Saiba mais sobre estas duas novas recomendações na tabela [de recomendações computacional e de aplicações.](recommendations-reference.md#recs-computeapp)

Saiba mais sobre como o Azure Security Center utiliza o agente em [O que é o agente Log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

Saiba mais sobre [extensões para máquinas Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Novas políticas para criar configurações contínuas de automação de exportação e fluxo de trabalho em escala

Automatizar os processos de monitorização e resposta a incidentes da sua organização pode melhorar consideravelmente o tempo necessário para investigar e mitigar incidentes de segurança.

Para implementar as suas configurações de automação em toda a sua organização, utilize estas políticas Azure 'DeployIfdNotExist' incorporadas para criar e configurar procedimentos [contínuos](continuous-export.md) de exportação e [automatização de fluxos de trabalho:](workflow-automation.md)

As políticas podem ser encontradas na política de Azure:


|Objetivo  |Política  |ID de política  |
|---------|---------|---------|
|Exportação contínua para centro de eventos|[Implementar a exportação no Hub de Eventos para alertas e recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Exportação contínua para log analytics espaço de trabalho|[Implementar a exportação na área de trabalho do Log Analytics para alertas e recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatização do fluxo de trabalho para alertas de segurança|[Implementar a Automatização do Fluxo de Trabalho para os alertas do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatização do fluxo de trabalho para recomendações de segurança|[Implementar a Automatização do Fluxo de Trabalho para as recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Começa com [modelos de automatização de fluxos de trabalho.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Saiba mais sobre a utilização das duas políticas de exportação na [automatização do fluxo de trabalho configure em escala utilizando as políticas fornecidas](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) e [Crie uma exportação contínua.](continuous-export.md#set-up-a-continuous-export)


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nova recomendação para a utilização de NSGs para proteger máquinas virtuais não-orientadas para a Internet

O controlo de segurança "implementar as melhores práticas de segurança" inclui agora a seguinte nova recomendação:

- **Máquinas virtuais não orientadas para a Internet devem ser protegidas com grupos de segurança de rede**

Uma recomendação existente, **as máquinas virtuais viradas para a Internet devem ser protegidas com grupos** de segurança de rede, não distinguindo entre VMs virados para a Internet e não virados para a Internet. Para ambos, uma recomendação de alta gravidade foi gerada se um VM não fosse atribuído a um grupo de segurança de rede. Esta nova recomendação separa as máquinas que não se estamos a passar pela Internet para reduzir os falsos positivos e evitar alertas desnecessários de alta gravidade.

Saiba mais na tabela [de recomendações da Rede.](recommendations-reference.md#recs-network)




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Novas políticas para permitir a proteção de ameaças e segurança avançada de dados

As novas políticas abaixo foram adicionadas à iniciativa ASC Default e destinam-se a ajudar a permitir a proteção de ameaças ou a segurança avançada de dados para os tipos de recursos relevantes.

As políticas podem ser encontradas na política de Azure:


| Política                                                                                                                                                                                                                                                                | ID de política                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A segurança avançada dos dados deve ser ativada nos servidores da Base de Dados Azure SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A segurança avançada dos dados deve ser ativada em servidores SQL em máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [A proteção avançada de ameaças deve ser ativada nas contas de armazenamento do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Proteção avançada de ameaças deve ser ativada em cofres Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Proteção avançada de ameaças deve ser ativada nos planos do Azure App Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [A proteção avançada de ameaças deve ser ativada nos registos do Registo de Contentores de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [A proteção avançada de ameaças deve ser ativada nos clusters de serviços Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [A proteção avançada de ameaças deve ser ativada em Máquinas Virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Saiba mais sobre [a proteção contra ameaças no Centro de Segurança Azure](azure-defender.md).
