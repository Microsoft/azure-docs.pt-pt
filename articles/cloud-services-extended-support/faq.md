---
title: Perguntas frequentes para a Azure Cloud Services (suporte alargado)
description: Perguntas frequentes para a Azure Cloud Services (suporte alargado)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4db23982dfaed1447023edb417a3732dac94bdc4
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025242"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Perguntas frequentes dos Serviços Cloud do Azure (suporte alargado)
Este artigo abrange perguntas frequentes relacionadas com a Azure Cloud Services (suporte alargado).

## <a name="general"></a>Geral

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Qual é o nome de recurso para Cloud Services (clássico) & Cloud Services (suporte alargado)?
- Serviços cloud (clássico): `microsoft.classiccompute/domainnames`
- Serviços cloud (suporte alargado): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Quais as localizações disponíveis para implantar Serviços cloud (suporte alargado)?
Os Serviços cloud (suporte alargado) estão disponíveis em todas as regiões de nuvem pública.

### <a name="how-does-my-quota-change"></a>Como muda a minha quota? 
Os clientes terão de solicitar quota utilizando os mesmos processos que qualquer outro produto Azure Resource Manager. A quota em Azure Resource Manager é regional e será necessário um pedido de quota separado para cada região.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Por que não vejo mais uma produção & slot de encenação?
Os Serviços cloud (suporte alargado) não suportam o conceito lógico de um serviço hospedado, que incluiu duas faixas horárias (Production & Staging). Cada implementação é uma implementação independente do Cloud Service (suporte alargado). Para testar e encenar uma nova versão de um serviço de nuvem, implementar um serviço de nuvem (suporte alargado) e marcá-lo como VIP permutável com outro serviço de nuvem (suporte alargado)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Por que não posso mais criar um Serviço cloud vazio?
O conceito de nomes de serviço hospedados já não existe, não é possível criar um Serviço cloud vazio (suporte alargado).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Os Serviços cloud (suporte alargado) suportam o Controlo de Saúde dos Recursos (RHC)?
Não, os Serviços cloud (suporte alargado) não suportam o Controlo de Saúde dos Recursos (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>Como estão a mudar as métricas de exemplo de função?
Não há alterações nas métricas de instância de função. 

### <a name="how-are-web--worker-roles-changing"></a>Como é que os papéis dos trabalhadores & web estão a mudar?
Não há alterações no design, arquitetura ou componentes das funções web e trabalhadora. 

### <a name="how-are-role-instances-changing"></a>Como estão a mudar os exemplos de papéis?
Não há alterações no design, arquitetura ou componentes das instâncias de função. 

### <a name="how-will-guest-os-updates-change"></a>Como é que as atualizações dos hóspedes vão mudar?
 Não há alterações no método de lançamento. Os Serviços cloud (clássicos) e Cloud Services (suporte alargado) receberão as mesmas atualizações.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Os Serviços Cloud (apoio alargado) suportam estados com oferta suspensa e parados?

A implantação de Serviços cloud (suporte alargado) apenas suporta o estado parado-Atribuído que aparece como "parado" no portal Azure. Estado parado não é apoiado. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>As implementações dos Serviços cloud (suporte alargado) suportam a escala através de aglomerados, zonas de disponibilidade e regiões?
As implementações de Serviços de Nuvem (suporte alargado) não podem escalar em vários clusters, zonas de disponibilidade e regiões. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Existem diferenças de preços entre os Serviços Cloud (clássico) e os Serviços Cloud (suporte alargado)?
Os Serviços de Nuvem (suporte alargado) utilizam endereços IP públicos Azure Key Vault e Basic (ARM).Os clientes que necessitam de certificados precisam de usar o Cofre da Chave Azure para a gestão de certificados[(saiba mais](https://azure.microsoft.com/pricing/details/key-vault/) sobre os preços do Azure Key Vault.)   Cada endereço IP público para Serviços cloud (suporte alargado) é cobrado separadamente[(saiba mais](https://azure.microsoft.com/pricing/details/ip-addresses/) sobre preços de endereço IP público.) 
## <a name="resources"></a>Recursos 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Que recursos ligados a uma implantação de Cloud Services (suporte alargado) precisam para viver no mesmo grupo de recursos?
Os equilibradores de carga, os grupos de segurança da rede e as tabelas de rotas precisam de viver na mesma região e grupo de recursos. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Que recursos ligados a uma implantação de Cloud Services (apoio alargado) precisam de viver na mesma região?
Key Vault, rede virtual, endereços IP públicos, grupos de segurança de rede e tabelas de rotas precisam de viver na mesma região.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Que recursos ligados a uma implantação de Cloud Services (suporte alargado) precisam para viver na mesma rede virtual?
Os endereços IP públicos, os equilibradores de carga, os grupos de segurança da rede e as tabelas de rotas precisam de viver na mesma rede virtual. 

## <a name="deployment-files"></a>Ficheiros de implantação 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Como posso usar um modelo para implementar ou gerir a minha implantação?
Os ficheiros de modelo e parâmetro podem ser passados como parâmetro usando REST, PowerShell e CLI. Também podem ser carregados através do portal Azure.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Preciso de manter quatro ficheiros agora? (modelo, parâmetro, csdef, cscfg)
Os ficheiros de modelo e parâmetros são utilizados apenas para automatização de implantação. Tal como os Cloud Services (clássicos), pode criar manualmente recursos dependentes primeiro e, em seguida, uma implementação de Serviços cloud (suporte alargado) utilizando comandos PowerShell, CLI ou através do Portal com csdef existente, cscfg.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Como é que o meu código de aplicação muda nos Serviços cloud (suporte alargado)
Não são necessárias alterações para o seu código de aplicação embalado em cspkg. As suas aplicações existentes continuarão a funcionar como antes. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Os Serviços cloud (suporte alargado) permitem o formato do pacote CTP?
O formato do pacote CTP não é suportado em Cloud Services (suporte alargado). No entanto, permite um limite de tamanho de pacote melhorado de 800 MB

## <a name="migration"></a>Migração

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Os Serviços Cloud (suporte alargado) irão atenuar as falhas devido a falhas de alocação?
Não, as implementações do Cloud Service (suporte alargado) estão ligadas a um cluster como o Cloud Services (clássico). Por conseguinte, as falhas de atribuição continuarão a existir se o cluster estiver cheio. 

### <a name="when-do-i-need-to-migrate"></a>Quando é que preciso de emigrar? 
Estimar o tempo necessário e a migração da complexidade depende de uma série de variáveis. O planeamento é o passo mais eficaz para compreender o âmbito do trabalho, os bloqueadores e a complexidade da migração.

## <a name="networking"></a>Rede

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Por que não posso criar uma implantação sem rede virtual?
As redes virtuais são um recurso necessário para qualquer implementação no Azure Resource Manager. A implementação dos Serviços cloud (suporte alargado) deve viver dentro de uma rede virtual. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Porque é que agora estou a ver tantos recursos em rede? 
No Azure Resource Manager, os componentes da sua implementação em Cloud Services (suporte alargado) são expostos como um recurso para uma melhor visibilidade e um melhor controlo. O mesmo tipo de recursos foi usado nos Serviços Cloud (clássicos) no entanto foram apenas escondidos. Um exemplo deste recurso é o Balanceador de Carga Pública, que é agora um recurso explícito de "read only" criado automaticamente pela plataforma

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Quais as restrições aplicáveis a uma sub-rede com os respetivos Serviços cloud (suporte alargado)?
Uma sub-rede que contenha implementações de Serviços cloud (suporte alargado) não pode ser partilhada com implementações de outros produtos computativos, tais como Máquinas Virtuais, Conjuntos de Escala de Máquinas Virtuais, Tecido de Serviço, etc.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Que métodos de atribuição de IP são suportados em serviços Cloud (suporte alargado)?
Os Serviços cloud (suporte alargado) suportam métodos dinâmicos de alocação de IP & estáticos. Os endereços IP estáticos são referenciados como IPs reservados no ficheiro cscfg.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Por que estou a ser cobrado por endereços IP?
Os clientes são faturados para utilização de endereços IP em Serviços cloud (suporte alargado), assim como os utilizadores são faturados para endereços IP associados a máquinas virtuais. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Posso usar um nome DNS com Serviços cloud (suporte alargado)? 
Sim. Os Serviços cloud (suporte alargado) também podem receber um nome DNS. Com o Azure Resource Manager, a etiqueta DNS é uma propriedade opcional do endereço IP público que é atribuído ao Serviço de Cloud. O formato do nome DNS para implementações baseadas em Recursos Azure é `<userlabel>.<region>.cloudapp.azure.com`

## <a name="certificates--key-vault"></a>Certificados & Cofre chave

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Por que preciso de gerir os meus certificados em Cloud Services (suporte alargado)?
A Cloud Services (suporte alargado) adotou o mesmo processo que outras ofertas de computação onde os certificados residem dentro dos Cofres Chave geridos pelo cliente. Isto permite que os clientes tenham controlo total sobre os seus segredos & certificados. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Posso usar um Cofre-Chave para todos os meus destacamentos em todas as regiões?
N.º Key Vault é um recurso regional e os clientes precisam de um Cofre Chave em cada região. No entanto, um Cofre-Chave pode ser usado para todas as implementações dentro de uma determinada região.

## <a name="next-steps"></a>Passos seguintes
Para começar a utilizar serviços cloud (suporte alargado), consulte [implementar um Serviço de Nuvem (suporte alargado) utilizando o PowerShell](deploy-powershell.md)
