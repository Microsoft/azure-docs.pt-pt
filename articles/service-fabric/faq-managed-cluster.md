---
title: Questões comuns sobre clusters geridos de Tecido de Serviço
description: Frequentemente, perguntas sobre clusters geridos de Service Fabric, incluindo capacidades, casos de uso e cenários comuns.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 77d7b3fba62fb9f059df97c4f7fae99db783f3f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729003"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Clusters geridos de tecido de serviço frequentemente fazem perguntas

Existem muitas perguntas comumente sobre o que os clusters geridos pela Service Fabric (pré-visualização) podem fazer e como devem ser usados. Este documento abrange muitas dessas perguntas comuns e as suas respostas.

## <a name="general"></a>Geral

### <a name="what-are-service-fabric-managed-clusters"></a>O que são clusters geridos pela Service Fabric?

Os clusters geridos pelo Tecido de Serviço são uma evolução do modelo de recursos de cluster de tecido de serviço projetado para facilitar a implementação e gestão de clusters. Um cluster gerido por um Tecido de Serviço utiliza o modelo de encapsulamento do Azure Resource Manager para que um utilizador apenas precise de definir e implementar um único recurso de cluster em comparação com os muitos recursos independentes que devem implementar hoje (Virtual Machine Scale set, Load Balancer, IP, e muito mais).

### <a name="what-regions-are-supported-in-the-preview"></a>Que regiões são apoiadas na pré-estreia?

As regiões apoiadas para a pré-visualização de clusters geridos pelo Tecido de Serviço `centraluseuap` incluem, , , , e `eastus2euap` `eastasia` `northeurope` `westcentralus` `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Posso fazer uma migração no local do meu cluster de tecido de serviço existente para um recurso de cluster gerido?

Neste momento, você precisaria criar um novo recurso de cluster de Tecido de Serviço para usar o novo tipo de cluster gerido de Tecido de Serviço.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Existe um custo adicional para os clusters geridos pela Service Fabric?

Não, não existe nenhum custo adicional associado a um cluster gerido por Tecido de Serviço para além do custo do computação subjacente, armazenamento e recursos de networking necessários para o cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Existe um novo SLA introduzido pelo recurso de cluster gerido pelo Service Fabric?

O SLA não muda a partir do atual modelo de recursos do Service Fabric.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Qual é a diferença entre um cluster Basic e Standard SKU?

Um cluster SKU básico significa que a maioria das configurações são fornecidas pelo fornecedor de recursos de Tecido de Serviço. Os agrupamentos SKU básicos destinam-se a ser utilizados para ambientes de teste e pré-produção. Um cluster SKU standard permite que os utilizadores configurem o cluster para satisfazer especificamente as suas necessidades. Para mais informações, consulte [o Service Fabric gerido cluster SKUs](https://docs.microsoft.com/azure/service-fabric/overview-managed-cluster#service-fabric-managed-cluster-skus) para obter mais detalhes.

## <a name="cluster-deployment-and-management"></a>Implantação e Gestão de Clusters

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Executo extensões de script personalizadas no meu Conjunto de Escala de Máquina Virtual, posso continuar a fazê-lo com um recurso de Tecido de Serviço gerido?

Sim, ainda pode especificar extensões VM num tipo de nó. Para obter mais informações, consulte a amostra de extensão do tipo nó para obter mais detalhes.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Quero ter um equilibrador interno de carga, é possível?

Atualmente não é possível ter um equilibrador de carga interno. Recomendamos que bloqueie as regras do Grupo de Segurança da Rede para bloquear qualquer tráfego de entrada/saída não existente.

### <a name="can-i-autoscale-my-cluster"></a>Posso autodimensioná-lo? 
A autoscalagem não está atualmente disponível na pré-visualização.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Posso implantar o meu cluster em zonas de disponibilidade? 
Os clusters de zona de disponibilidade cruzada não estão disponíveis na pré-visualização.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Posso selecionar entre atualizações automáticas e manuais para o meu intervalo? 
Na pré-visualização, todas as atualizações de tempo de execução serão concluídas automaticamente.

## <a name="applications"></a>Aplicações

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Existe uma experiência de desenvolvimento local para clusters geridos pela Service Fabric?

A experiência de desenvolvimento local permanece inalterada em função dos clusters existentes de Tecidos de Serviço. Para mais informações, consulte [Criar uma Aplicação .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-quickstart-dotnet) para obter mais detalhes sobre a experiência de desenvolvimento local.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Posso implementar as minhas aplicações como recurso Azure Resource Manager?

Na pré-visualização, não é possível implementar aplicações como recurso Azure Resource Manager. As aplicações devem ser implantadas ligando-se diretamente ao cluster através do PowerShell ou do CLI. Esta funcionalidade será adicionada antes que os clusters de tecido de serviço manged insira a disponibilidade geral.
