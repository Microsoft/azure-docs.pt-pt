---
title: Sobre a Azure Cloud Services (suporte alargado)
description: Saiba mais sobre os elementos infantis do elemento de configuração de rede do ficheiro de configuração de serviço, que especifica os valores de Rede Virtual e DNS.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ecf115b7ce902fcd8b50f0eca32ffda6ef47e068
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618479"
---
# <a name="about-azure-cloud-services-extended-support"></a>Sobre a Azure Cloud Services (suporte alargado)

> [!IMPORTANT]
> Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cloud Services (suporte alargado) é um novo modelo de implementação baseado em [Recursos Azure para](../azure-resource-manager/management/overview.md) o produto [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) e está atualmente em pré-visualização pública. Os Serviços cloud (suporte alargado) têm o principal benefício de fornecer resiliência regional, juntamente com a paridade de recursos com os Serviços Azure Cloud implantados usando o Azure Service Manager. Também oferece algumas capacidades ARM, tais como acesso e controlo baseados em funções (RBAC), tags, políticas e modelos de implementação de suporte.  

Com esta alteração, o modelo de implementação baseado no Azure Service Manager para serviços cloud será renomeado [Cloud Services (clássico)](../cloud-services/cloud-services-choose-me.md). Irá manter a capacidade de construir e implementar rapidamente as suas aplicações e serviços web e cloud. Poderá escalar a sua infraestrutura de serviços em nuvem com base na procura atual e garantir que o desempenho das suas aplicações pode acompanhar, reduzindo simultaneamente os custos.  

## <a name="what-does-not-change"></a>O que não muda 
- Cria-se o código, define-se as configurações e implanta-se no Azure. O Azure configura o ambiente computacional, executa o seu código e depois monitoriza e mantém-no para si.
- Os Serviços cloud (suporte alargado) também suportam dois tipos de funções, [web e trabalhador.](../cloud-services/cloud-services-choose-me.md) Não há alterações no design, arquitetura ou componentes das funções web e trabalhadora. 
- Os três componentes de um serviço em nuvem, a definição de serviço (.csdef), o config de serviço (.cscfg) e o pacote de serviço (.cspkg) são transportados para a frente e não há alteração nos seus [formatos.](cloud-services-model-and-package.md) 
- Não são necessárias alterações ao código de tempo de funcionamento, uma vez que o plano de dados é o mesmo e o plano de controlo só está a mudar. 
- Os lançamentos do Azure GuestOS e as atualizações associadas estão alinhados com os Serviços Cloud (clássicos)
- Processo de atualização subjacente no que diz respeito à atualização de domínios, como a atualização prossegue, reversão e alterações permitidas de serviço durante uma atualização não alteram

## <a name="changes-in-deployment-model"></a>Alterações no modelo de implementação

São necessárias alterações mínimas nos ficheiros de Configuração de Serviço (.cscfg) e definição de serviço (.csdef) para implantar Serviços cloud (suporte alargado). Não são necessárias alterações ao código de tempo de execução. No entanto, os scripts de implementação terão de ser atualizados para chamar as novas APIs baseadas em recursos Azure. 

:::image type="content" source="media/overview-image-1.png" alt-text="A imagem mostra a configuração clássica do serviço de nuvem com a adição da secção do modelo. ":::

As principais diferenças entre os Serviços Cloud (clássicos) e os Serviços cloud (suporte alargado) no que diz respeito à implantação são: 

- As implementações do Azure Resource Manager utilizam [modelos ARM](../azure-resource-manager/templates/overview.md) que é um ficheiro JavaScript Object Notation (JSON) que define a infraestrutura e configuração do seu projeto. O modelo utiliza a sintaxe declarativa, que permite afirmar o que quer implementar sem ter de escrever a sequência de comandos de programação para a criar. O ficheiro de configuração de serviço e definição de serviço tem de ser consistente com o [modelo ARM](../azure-resource-manager/templates/overview.md) enquanto implementa serviços na Nuvem (suporte alargado). Isto pode ser conseguido quer [criando manualmente o modelo ARM,](deploy-template.md) quer utilizando [o PowerShell,](deploy-powershell.md) [Portal](deploy-portal.md) e [Visual Studio](deploy-visual-studio.md).  

- Os clientes devem utilizar [o Azure Key Vault](../key-vault/general/overview.md) para gerir [certificados em Serviços Cloud (suporte alargado)](certificates-and-key-vault.md). O Azure Key Vault permite armazenar e gerir credenciais de aplicação de forma segura, tais como segredos, chaves e certificados num repositório de nuvem central e seguro. As suas aplicações podem autenticar-se no Key Vault na hora de executar para obter credenciais. 

- Todos os recursos utilizados através do [Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) devem estar dentro de uma rede virtual. As redes e sub-redes virtuais são criadas no Azure Resource Manager utilizando as APIs existentes do Gestor de Recursos Azure e terão de ser referenciadas na secção DeConfiguration de Rede do .cscfg ao implementar serviços cloud (suporte alargado).   

- Cada serviço em nuvem (suporte alargado) é uma única implantação independente. Os serviços na nuvem (suporte alargado) não suportam várias ranhuras dentro de um único serviço de nuvem.  
    - A capacidade de troca VIP <sup>*</sup> pode ser utilizada para trocar entre dois serviços na nuvem (suporte alargado). Para testar e encenar uma nova versão de um serviço de nuvem, implementar um serviço de nuvem (suporte alargado) e marcá-lo como VIP permutável com outro serviço de nuvem (suporte alargado)  

- A etiqueta Do Serviço de Nome de Domínio (DNS) é opcional para um serviço de cloud (suporte alargado). No Azure Resource Manager, a etiqueta DNS é uma propriedade do recurso IP público associado ao serviço de nuvem. 


<sup>*</sup> A troca VIP por Serviços cloud (suporte alargado) não está disponível durante a pré-visualização pública.  

## <a name="migration-to-azure-resource-manager"></a>Migração para Azure Resource Manager

Os Serviços cloud (suporte alargado) fornecem dois caminhos para migrar de [Azure Service Manager](/powershell/azure/servicemanagement/overview) para [Azure Resource Manager](../azure-resource-manager/management/overview.md). 
1) Os clientes implementam serviços na nuvem diretamente no Azure Resource Manager e depois apagam o antigo serviço de cloud no Azure Service Manager. 
2) A migração no local suporta a capacidade de migrar os Serviços Cloud (clássicos) com o mínimo ou nenhum tempo de inatividade para os Serviços Cloud (suporte alargado). 

### <a name="additional-migration-options"></a>Opções de migração adicionais

Ao avaliar os planos de migração dos Serviços cloud (clássicos) para serviços cloud (suporte alargado) poderá querer investigar serviços adicionais de Azure, tais como: [Conjuntos de escala de máquinas virtuais,](../virtual-machine-scale-sets/overview.md) [Serviço de Aplicações,](../app-service/overview.md) [Serviço Azure Kubernetes](../aks/intro-kubernetes.md)e [Azure Service Fabric](../service-fabric/service-fabric-overview.md). Estes serviços continuarão a contar com capacidades adicionais, enquanto os Cloud Services (suporte alargado) manterão principalmente a paridade de funcionalidades com os Serviços cloud (clássicos).) 

Dependendo da aplicação, os Serviços cloud (suporte alargado) podem exigir substancialmente menos esforço para se mudarem para o Azure Resource Manager em comparação com outras opções. Se a sua aplicação não estiver a evoluir, a Cloud Services (suporte alargado) é uma opção viável a considerar, uma vez que fornece uma rota de migração rápida. Inversamente, se a sua aplicação está em constante evolução e precisa de um conjunto de funcionalidades mais moderna, explore outros serviços Azure para melhor atender aos seus requisitos atuais e futuros. 

## <a name="next-steps"></a>Passos seguintes
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).