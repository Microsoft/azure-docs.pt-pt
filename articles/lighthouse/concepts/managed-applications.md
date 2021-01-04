---
title: Azure Lighthouse e aplicações geridas do Azure
description: Entenda como o Farol Azure e as aplicações geridas aZure podem ser usadas em conjunto.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 5c30c3234a57e25ceaa521ad485f58d4d663ebe9
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693978"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse e aplicações geridas do Azure

Tanto a Azure geriu aplicações como o trabalho do Farol Azure, permitindo a um prestador de serviços aceder a recursos que residem no inquilino do cliente. Pode ser útil compreender as diferenças na forma como funcionam e nos cenários que ajudam a permitir e como podem ser usados em conjunto.

> [!TIP]
> Embora nos refiramos a prestadores de serviços e clientes neste tópico, [as empresas que gerem vários inquilinos](enterprise.md) podem usar os mesmos processos e ferramentas.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Comparando as aplicações geridas pelo Farol de Azure e Azure

Esta tabela ilustra algumas diferenças de alto nível que podem ter impacto se você pode optar por usar aplicações geridas pelo Azure Lighthouse ou Azure. Como nota abaixo, também pode desenhar uma solução que os utilize em conjunto.

|Consideração  |Azure Lighthouse  |Aplicações geridas do Azure  |
|---------|---------|---------|
|Utilizador típico     |Prestadores de serviços ou empresas que gerem vários inquilinos         |Fornecedores de software independentes (ISVs)         |
|Âmbito de acesso ao inquilino transversal     |Subscrições ou grupos de recursos         |Grupo de recursos (abrangido por uma única aplicação)         |
|Adescaizável no Mercado Azure     |Não (as ofertas podem ser publicadas no Azure Marketplace, mas os clientes são faturados separadamente)        |Yes         |
|Proteção IP     |Sim (IP pode permanecer no inquilino do prestador de serviços)        |Sim (por design, o grupo de recursos está bloqueado aos clientes)         |
|Atribuições de negação     |No         |Yes        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

Com [o Azure Lighthouse,](../overview.md)um prestador de serviços pode executar uma vasta gama de tarefas de gestão diretamente na subscrição (ou grupo de recursos) de um cliente. Este acesso é conseguido através de uma projeção lógica, permitindo aos prestadores de serviços inscreverem-se no seu próprio inquilino e recursos de acesso que pertencem ao arrendatário do cliente. O cliente pode determinar quais subscrições ou grupos de recursos para delegar ao prestador de serviços, e o cliente mantém o acesso total a esses recursos. Também podem remover o acesso do prestador de serviços a qualquer momento.

Para utilizar o Farol Azure, os clientes estão a bordo para [a gestão de recursos delegados da Azure,](azure-delegated-resource-management.md) quer [através da implementação de modelos ARM,](../how-to/onboard-customer.md) quer através de uma [oferta de Serviço Gerido no Mercado Azure.](managed-services-offers.md) Pode acompanhar o seu impacto nos compromissos com os clientes [ligando o ID do seu parceiro.](../how-to/partner-earned-credit.md)

O Azure Lighthouse é normalmente utilizado quando um prestador de serviços executa tarefas de gestão para um cliente numa base contínua.

### <a name="azure-managed-applications"></a>Aplicações geridas do Azure

[As aplicações geridas pela Azure](../../azure-resource-manager/managed-applications/overview.md) permitem que um prestador de serviços ou ISV ofereça soluções em nuvem que sejam fáceis de implementar e utilizar nas suas próprias subscrições.

Numa aplicação gerida, os recursos utilizados pela aplicação são agregados e implantados num grupo de recursos gerido pela editora. Este grupo de recursos está presente na assinatura do cliente, mas uma identidade no inquilino da editora tem acesso a ele. O ISV continua a gerir e manter a aplicação gerida, enquanto o cliente não tem acesso direto ao trabalho no seu grupo de recursos, nem qualquer acesso aos seus recursos.

As aplicações geridas suportam experiências e integração [personalizadas do portal Azure](../../azure-resource-manager/managed-applications/concepts-view-definition.md) [com fornecedores personalizados.](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md) Estas opções podem ser usadas para proporcionar uma experiência mais personalizada e integrada, facilitando aos clientes a realização de algumas tarefas de gestão.

As aplicações geridas podem ser [publicadas no Azure Marketplace,](../../marketplace/create-new-azure-apps-offer.md)seja como uma oferta privada para uso específico de um cliente, quer como ofertas públicas que vários clientes podem comprar. Também podem ser entregues aos utilizadores dentro da sua [organização, publicando aplicações geridas no seu catálogo de serviços.](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md) Pode implementar tanto o catálogo de serviços como as instâncias do Marketplace utilizando modelos ARM, que podem incluir o identificador único de um parceiro do Mercado Comercial para acompanhar a atribuição de utilização do [cliente.](../../marketplace/azure-partner-customer-usage-attribution.md)

As aplicações geridas pelo Azure são normalmente utilizadas para uma necessidade específica do cliente que pode ser alcançada através de uma solução chave na mão que é totalmente gerida pelo prestador de serviços.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Usando o Farol Azure e a Azure geriu aplicações em conjunto

Embora as aplicações geridas pelo Azure e pelo Azure utilizem diferentes mecanismos de acesso para atingir objetivos diferentes, pode haver cenários em que faz sentido um prestador de serviços utilizar ambos com o mesmo cliente.

Por exemplo, um cliente pode querer serviços geridos prestados por um prestador de serviços através do Farol Azure, para que tenham visibilidade nas ações do parceiro, juntamente com o controlo contínuo da sua subscrição delegada. No entanto, o prestador de serviços pode não querer que o cliente aceda a determinados recursos que serão armazenados no arrendatário do cliente, ou que permita quaisquer ações personalizadas nesses recursos. Para cumprir estes objetivos, o prestador de serviços pode publicar uma oferta privada como uma aplicação gerida. A aplicação gerida pode incluir um grupo de recursos que é implantado no inquilino do cliente, mas que não pode ser acedido diretamente pelo cliente.

Os clientes também podem estar interessados em aplicações geridas de vários prestadores de serviços, quer também utilizem ou não serviços geridos através do Farol Azure de qualquer um desses prestadores de serviços. Além disso, os parceiros do programa Cloud Solution Provider (CSP) podem revender certas aplicações geridas publicadas por outros ISVs a clientes que suportam através do Farol Azure. Com um vasto leque de opções, os prestadores de serviços podem escolher o equilíbrio certo para atender às necessidades dos seus clientes, limitando o acesso aos recursos quando apropriado.

## <a name="next-steps"></a>Passos seguintes

- Conheça as [aplicações geridas pela Azure.](../../azure-resource-manager/managed-applications/overview.md)
- Saiba como embarcar [numa subscrição do Azure Lighthouse.](../how-to/onboard-customer.md)
- Conheça os [cenários isv com o Farol Azure.](isv-scenarios.md)