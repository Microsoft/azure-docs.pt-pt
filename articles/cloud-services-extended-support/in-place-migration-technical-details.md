---
title: Detalhes técnicos e requisitos para a migração para a Azure Cloud Services (suporte alargado)
description: Fornece detalhes técnicos e requisitos para migrar dos Serviços Azure Cloud (clássico) para Azure Cloud Services (suporte alargado)
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286995"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Detalhes técnicos da migração para a Azure Cloud Services (suporte alargado)   

Este artigo discute os detalhes técnicos relativos à ferramenta de migração relativa aos Serviços cloud (clássico). 

> [!IMPORTANT]
> A migração dos Serviços cloud (clássico) para os Serviços cloud (suporte alargado) utilizando a ferramenta de migração está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Detalhes sobre funcionalidades/cenários suportados para a migração 

### <a name="extensions-and-plugin-migration"></a>Extensões e migração de plugins 
- Todas as extensões ativadas e suportadas serão migradas. 
- As extensões desativadas não serão migradas. 
- Os plugins são um conceito antigo e devem ser removidos antes da migração. São apoiados para a migração e, após a migração, se for necessário ativar a extensão, o plugin tem de ser removido primeiro antes de instalar a extensão. Os plugins e extensões de ambiente de trabalho remoto são mais impactados por isso.   
 
### <a name="certificate-migration"></a>Migração de certificados
- Nos Serviços cloud (suporte alargado), os certificados são armazenados num Cofre-Chave. Como parte da migração, criamos um Cofre-Chave para os clientes que têm o nome de Cloud Service e transferimos todos os certificados do Azure Service Manager para o Key Vault. 
- A referência a este Cofre chave é especificada no modelo ou passou através do PowerShell ou do Azure CLI. 

### <a name="service-configuration-and-service-definition-files"></a>Configuração de serviço e ficheiros de definição de serviço
- Os ficheiros .cscfg e .csdef precisam de ser atualizados para os Serviços cloud (suporte alargado) com pequenas alterações. 
- Os nomes de recursos como a rede virtual e o VM SKU são diferentes. Ver [tradução de recursos e nomear convenção pós migração](#translation-of-resources-and-naming-convention-post-migration)
- Os clientes podem recuperar as suas novas implementações através da [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) e [da Rest API.](https://docs.microsoft.com/rest/api/compute/cloudservices/get) 

### <a name="cloud-service-and-deployments"></a>Serviço de Cloud e implementações
- Cada implementação de Serviços cloud (suporte alargado) é um Serviço cloud independente. A implementação já não é agrupada num serviço de nuvem utilizando slots.
- Se tiver duas ranhuras no seu Serviço cloud (clássico), tem de eliminar uma ranhura (encenação) e utilizar a ferramenta de migração para mover a outra ranhura (produção) para o Azure Resource Manager. 
- O endereço IP público na implementação do Cloud Service permanece o mesmo após a migração para O Gestor de Recursos Azure e é exposto como um recurso Basic SKU IP (dinâmico ou estático). 
- O nome e domínio DNS (cloudapp.azure.net) para o serviço de nuvem migrada permanece o mesmo. 

### <a name="virtual-network-migration"></a>Migração de rede virtual
- Se uma implementação de Cloud Services estiver numa rede virtual, então durante a migração todos os Serviços Cloud e recursos de rede virtuais associados são migrados juntos. 
- Após a migração, a rede virtual é colocada num grupo de recursos diferente do Serviço cloud. 
- Para redes virtuais com múltiplos Serviços Cloud, cada Serviço Cloud é migrado um após o outro. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migração de implantações não numa rede virtual
- Em 2017, o Azure começou a criar automaticamente novas implementações (sem rede virtual especificada pelo cliente) numa plataforma criada por "padrão" de rede virtual. Estas redes virtuais padrão estão escondidas dos clientes.   
- Como parte da migração, esta rede virtual padrão será exposta aos clientes uma vez no Azure Resource Manager. Para gerir ou atualizar a implementação no Azure Resource Manager, os clientes precisam de adicionar esta informação de rede virtual na secção DeConfiguration de Rede do ficheiro .cscfg.    
- A rede virtual padrão, quando migrada para O Gestor de Recursos Azure, é colocada no mesmo grupo de recursos que o Cloud Service.
- Os Serviços Cloud criados antes deste tempo não estarão em nenhuma rede virtual e não podem ser migrados usando a ferramenta. Considere recolocar estes Serviços Cloud diretamente no Azure Resource Manager. 
- Para verificar se uma implantação é elegível para migrar, execute a API validada na implantação. O resultado da API validada conterá uma mensagem de erro que menciona explicitamente se esta implementação for elegível para migrar.     

### <a name="load-balancer"></a>Balanceador de Carga   
- Para um Serviço cloud utilizando um ponto final público, uma plataforma criada balanceador de carga associada ao Serviço cloud está exposta dentro da subscrição do cliente no Azure Resource Manager. O equilibrador de carga é um recurso apenas de leitura, e as atualizações são restringidas apenas através dos ficheiros de Configuração de Serviço (.cscfg) e Definição de Serviço (.csdef). 

### <a name="key-vault"></a>Key Vault
- Como parte da migração, a Azure cria automaticamente um novo Cofre-Chave e migra todos os certificados para ele. A ferramenta não permite utilizar um cofre de chaves existente. 
- Os Serviços cloud (suporte alargado) requerem um Cofre-Chave localizado na mesma região e subscrição. Este Cofre chave é criado automaticamente como parte da migração. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Tradução de recursos e nomeação convenção pós migração
Como parte da migração, os nomes dos recursos são alterados, e poucas funcionalidades dos Serviços cloud são expostas como recursos do Azure Resource Manager. A tabela resume as alterações específicas à migração dos Serviços cloud.

| Serviços cloud (clássico) <br><br> Nome do recurso | Serviços cloud (clássico) <br><br> Syntax| Serviços cloud (suporte alargado) <br><br> Nome do recurso| Serviços cloud (suporte alargado) <br><br> Syntax | 
|---|---|---|---|
| Serviço Cloud | `cloudservicename` | Não associado| Não associado |
| Implantação (portal criado) <br><br> Implantação (não-portal criado)  | `deploymentname` | Serviços cloud (suporte alargado) | `deploymentname` |  
| Rede Virtual | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Não associado |  Rede Virtual (não portal criado) <br><br> Rede Virtual (portal criado) <br><br> Redes Virtuais (Padrão) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Não associado | Não associado | Key Vault | `cloudservicename` | 
| Não associado | Não associado | Grupo de recursos para implementações de serviços em nuvem | `cloudservicename-migrated` | 
| Não associado | Não associado | Grupo de Recursos para Rede Virtual | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Não associado | Não associado | IP público (Dinâmico) | `cloudservicenameContractContract` | 
| Nome IP reservado | `reservedipname` | IP reservado (não-portal criado) <br><br> IP reservado (portal criado) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Não associado| Não associado | Balanceador de Carga | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Questões de migração e como lidar com eles. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>A migração ficou presa numa operação por muito tempo. 
- Comprometer, preparar e abortar pode demorar muito tempo dependendo do número de implementações. As operações vão esgotar-se depois de 24 horas.   
- Cometer, preparar e abortar operações são idempotentes. A maioria dos problemas são reparáveis retídas através de uma nova localização. Pode haver erros transitórios, que podem desaparecer em poucos minutos, recomendamos voltar a tentar depois de uma lacuna. Se migrar utilizando o portal Azure e a operação estiver presa num "estado de progresso", utilize a PowerShell para voltar a tentar a operação. 
- Suporte de contacto para ajudar a migrar ou reverter a colocação a partir do backend. 

### <a name="migration-failed-in-an-operation"></a>A migração falhou numa operação. 
- Se validar falhas, é porque a implementação ou rede virtual contém um cenário/recurso/recurso não suportado. Use a lista de cenários não apoiados para encontrar o trabalho nos documentos.  
- Preparar primeiro a operação faz validação, incluindo algumas validações caras (não cobertas por validação). A falha de preparação pode dever-se a um cenário não apoiado. Encontre o cenário e o trabalho nos documentos públicos. O abort tem de ser chamado para voltar ao estado original e desbloquear a implementação para atualizações e eliminar operações.
- Se o aborto falhar, recandidutar a operação. Se as recaírem, contacte o suporte.
- Se o compromisso falhou, voltou a tentar a operação. Se falhar novamente, contacte o suporte. Mesmo em cometer falhas, não deve haver qualquer problema de avião de dados para a sua implantação. A sua implementação deve ser capaz de lidar com o tráfego do cliente sem qualquer problema. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Portal renovado após Preparar. A experiência reiniciou e cometer ou abortar já não é visível. 
- O Portal armazena a informação de migração localmente e, portanto, após a atualização, começará a partir da fase de validação, mesmo que o Serviço cloud esteja em fase de preparação.  
- Pode utilizar o portal para passar pela validação e preparar novamente passos para expor o botão Abort e Commit. Não vai causar falhas.
- Os clientes podem usar a PowerShell ou a Rest API para abortar ou comprometer-se. 

### <a name="how-much-time-can-the-operations-takebr"></a>Quanto tempo as operações podem demorar?<br>
A validada foi concebida para ser rápida. Preparar é a execução mais longa e leva algum tempo dependendo do número total de instâncias de papel que estão a ser migradas. Abortar e cometer também pode levar tempo, mas levará menos tempo em comparação para se preparar. Todas as operações vão esgotar-se depois das 24 horas. 
