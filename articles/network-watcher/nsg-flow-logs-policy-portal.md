---
title: QuickStart - Implementar e gerir registos de fluxo NSG utilizando a política do Azure
titleSuffix: Azure Network Watcher
description: Este artigo explica como usar as políticas incorporadas para gerir a implementação de registos de fluxo NSG
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011106"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>QuickStart: Implementar e gerir registos de fluxo NSG utilizando a política do Azure 

## <a name="overview"></a>Descrição geral
O Azure Policy ajuda a impor normas organizacionais e a avaliar o cumprimento em escala. Os casos de utilização comum para a Política Azure incluem a implementação da governação para a consistência dos recursos, a conformidade regulamentar, a segurança, os custos e a gestão. Neste artigo, usaremos duas políticas incorporadas disponíveis para os Registos de Fluxo NSG para gerir a configuração dos seus registos de fluxo. A primeira política sinaliza quaisquer NSGs sem registos de fluxo ativados. A segunda política implementa automaticamente os registos de fluxo para NSGs sem registos de fluxo ativados. 

Se estiver a criar uma política Azure pela primeira vez, pode ler: 
- [Descrição geral do Azure Policy](../governance/policy/overview.md) 
- [Tutorial para a criação de política.](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)


## <a name="locate-the-policies"></a>Localizar as políticas
1. Vá ao portal Azure – [portal.azure.com](https://portal.azure.com) 

Navegue para a página política do Azure procurando por Política na página inicial da política da barra de pesquisa ![](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Dirija-se ao **separador atribuições** do painel esquerdo

![Separador de atribuições](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Clique no botão **'Atribuir Política'** 

![Abotoar botão de política](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Clique no menu de três pontos em "Definições de Política" para ver as políticas disponíveis

5. Utilize o filtro Tipo e escolha "Incorporado". Em seguida, procure por "Flow log"

Você deve ver as duas políticas incorporadas para a Lista de Política de Registos de Fluxo ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Escolha a política que pretende atribuir

- *"O registo de fluxo deve ser configurado para cada grupo de segurança* de rede" é a política de auditoria que sinaliza NSGs não conformes, ou seja, NSGs sem registo de fluxo habilitado
- *"Implementar um recurso de registo de fluxo com o grupo de segurança da rede alvo"* é a política com uma ação de implementação, permite registos de fluxo em todos os NSGs sem registos de fluxo

Existem instruções separadas para cada política abaixo.  

## <a name="audit-policy"></a>Política de Auditoria 

### <a name="how-the-policy-works"></a>Como funciona a política

A política verifica todos os objetos ARM existentes do tipo "Microsoft.Network/networkSecurityGroups", isto é, olha para todos os NSGs num determinado âmbito, e verifica a existência de registos de fluxo ligados através da propriedade Flow Logs do NSG. Se a propriedade não existir, o NSG está sinalizado.

Se quiser ver a definição completa da apólice, pode visitar o [separador Definições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) e procurar por "Registos de fluxo" para encontrar a política

### <a name="assignment"></a>Atribuição

1. Preencha os seus detalhes de política

- Âmbito: Uma subscrição é a escolha comum, também pode escolher um grupo de gestão ou grupo de recursos como relevante para si.  
- Definição de Política: Deve ser escolhido como indicado na secção "Localizar as políticas".
- Nome de atribuição: Escolha um nome descritivo 

2. Clique em "Review + Create" para rever a sua atribuição

A política não requer parâmetros. Como está a atribuir uma política de auditoria, não precisa de preencher os detalhes no separador "Remediação".  

![Revisão da Política de Auditoria](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Resultados

Para verificar os resultados, abra o separador Conformidade e procure o nome da sua Atribuição.
Deve ver algo semelhante à imagem que se segue assim que a sua apólice for executado. Caso a sua apólice não tenha funcionando, espere por algum tempo. 

![Resultados da Política de Auditoria](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Política de implantação se não existir 

### <a name="policy-structure"></a>Estrutura Política

A política verifica todos os objetos ARM existentes do tipo "Microsoft.Network/networkSecurityGroups", isto é, olha para todos os NSGs num determinado âmbito, e verifica a existência de registos de fluxo ligados através da propriedade Flow Logs do NSG. Se a propriedade não existir, a apólice implementa um registo Flow. 

Se quiser ver a definição completa da apólice, pode visitar o [separador Definições](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) e procurar por "Registos de fluxo" para encontrar a política. 

### <a name="assignment"></a>Atribuição

1. Preencha os seus detalhes de política

- Âmbito: Uma subscrição é a escolha comum, também pode escolher um grupo de gestão ou grupo de recursos como relevante para si.  
- Definição de Política: Deve ser escolhido como indicado na secção "Localizar as políticas".
- Nome de atribuição: Escolha um nome descritivo 

2. Adicionar parâmetros de política 

O serviço Network Watcher é um serviço regional. Estes parâmetros permitem executar a ação política de implantação de registos de fluxo. 
- Região do NSG: Regiões azures em que a política é direcionada
- ID de armazenamento: Identificação completa do recurso da conta de armazenamento. Nota: Esta conta de armazenamento deve estar na mesma região que o NSG. 
- Rede Observadores RG: Nome do grupo de recursos que contém o seu recurso Network Watcher. Se não o rebatizou, pode introduzir 'NetworkWatcherRG' que é o padrão.
- Nome do Observador de Rede: Nome do serviço regional de observadores de rede. Formato: NetworkWatcher_RegionName. Exemplo: NetworkWatcher_centralus. Veja a lista completa.

![Parâmetros da política DINE](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Adicionar detalhes de remediação

- Verifique a marcação em "Criar tarefa de remediação" se quiser que a política afete os recursos existentes 
- "Criar uma Identidade Gerida" já deve ser verificado
- Selecionado o mesmo local anterior para a sua Identidade Gerida 
- Necessitará de permissões do Colaborador ou do Proprietário para utilizar esta política. Se tiver estas permissões, não deverá ver erros.

![Remediação da política DINE](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Clique em "Review + Create" para rever a sua atribuição Deve ver algo semelhante à imagem seguinte.

![Revisão da política da DINE](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Resultados

Para verificar os resultados, abra o separador Conformidade e procure o nome da sua Atribuição.
Devias ver algo como seguir a imagem uma vez que a tua apólice. Caso a sua apólice não tenha funcionando, espere por algum tempo.

![Resultados da política do DINE](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Passos seguintes 

-   Utilize este [tutorial](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) Vá mais fundo utilizando modelos ARM para implementar Registos de Fluxo e Análise de Tráfego.
-   Saiba mais sobre [o Network Watcher](./index.yml)