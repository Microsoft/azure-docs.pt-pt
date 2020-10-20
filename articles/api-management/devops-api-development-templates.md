---
title: DevOps para Gestão API Azure usando modelos ARM
description: Introdução aos DevOps da API com Azure API Management, utilizando modelos do Azure Resource Manager para gerir implementações de API num pipeline CI/CD
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 144baa0944451adaf08f0df84325b58079e19d65
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210063"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD para gestão de API usando modelos de Gestor de Recursos Azure

Este artigo introduz API DevOps com Azure API Management, utilizando modelos de Gestor de Recursos Azure. Com o valor estratégico das APIs, um gasoduto de integração contínua (CI) e de implantação contínua (CD) tornou-se um aspeto importante do desenvolvimento da API. Permite que as organizações automatizem a implementação de alterações de API sem passos manuais propensos a erros, detetem problemas mais cedo e, em última análise, entreguem valor aos utilizadores finais mais rapidamente. 

Para mais detalhes, ferramentas e amostras de código para implementar a abordagem DevOps descrita neste artigo, consulte o kit de [recursos de gestão de devops de gestão da Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) em GitHub. Como os clientes trazem uma vasta gama de culturas de engenharia e soluções de automação existentes, a abordagem não é uma solução de tamanho único.

## <a name="the-problem"></a>O problema

As organizações têm hoje em dia múltiplos ambientes de implantação (como desenvolvimento, teste, produção) e utilizam instâncias separadas de Gestão de API para cada ambiente. Alguns casos são partilhados por várias equipas de desenvolvimento, que são responsáveis por diferentes APIs com diferentes cadências de lançamento.

Como resultado, os clientes enfrentam os seguintes desafios:

* Como automatizar a implantação de APIs na Gestão da API?
* Como migrar configurações de um ambiente para outro?
* Como evitar interferências entre diferentes equipas de desenvolvimento que partilham o mesmo exemplo de Gestão da API?

## <a name="manage-configurations-in-resource-manager-templates"></a>Gerir configurações em modelos de Gestor de Recursos

A abordagem proposta é ilustrada na imagem seguinte. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="DevOps com Gestão de API":::

Neste exemplo, existem dois ambientes de implantação: *Desenvolvimento* e *Produção.* Cada um tem a sua própria instância de Gestão de API. 

* Os desenvolvedores de API têm acesso à instância de Desenvolvimento e podem usá-lo para desenvolver e testar as suas APIs. 
* A instância de Produção é gerida por uma equipa designada chamada *editoras API.*

A chave nesta abordagem proposta é manter todas as configurações de Gestão da API nos [modelos do Gestor de Recursos Azure](../azure-resource-manager/resource-group-authoring-templates.md). Estes modelos devem ser mantidos num sistema de controlo de fontes, como o Git. Como ilustrado na imagem, existe um repositório editor que contém todas as configurações da instância de Gestão da API de Produção numa coleção de modelos:

|Modelo  |Descrição  |
|---------|---------|
|Modelo de serviço     | Configurações de nível de serviço da instância de Gestão da API, tais como o nível de preços e domínios personalizados         |
|Modelos compartilhados     |  Recursos partilhados ao longo de uma instância de Gestão da API, tais como grupos, produtos e madeireiros    |
|Modelos de API     |  Configurações de APIs e seus subreufontes: operações, políticas, definições de diagnóstico        |
|Modelo principal principal     |   Liga tudo, [ligando-os a](../azure-resource-manager/resource-group-linked-templates.md) todos os modelos e implantando-os em ordem. Para implementar todas as configurações para uma instância de Gestão da API, implemente o modelo principal. Cada modelo também pode ser implementado individualmente.       |

Os desenvolvedores da API irão recorrer ao repositório do Publisher para um repositório de desenvolvedores e trabalharão nas alterações para as suas APIs. Na maioria dos casos, focam-se nos modelos de API para as suas APIs e não precisam de alterar os modelos partilhados ou de serviço.

## <a name="migrate-configurations-to-templates"></a>Migrar configurações para modelos
Existem desafios para os desenvolvedores de API ao trabalhar com modelos de Gestor de Recursos:

* Os desenvolvedores de API trabalham frequentemente com a [Especificação OpenAPI](https://github.com/OAI/OpenAPI-Specification) e podem não estar familiarizados com os esquemas do Gestor de Recursos. Os modelos de autoria manualmente podem ser propensos a erros. 

   Uma ferramenta de utilidade chamada [**Criador**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) no kit de recursos pode ajudar a automatizar a criação de modelos API com base num ficheiro de Especificação API aberto. Além disso, os desenvolvedores podem fornecer políticas de Gestão de API para uma API em formato XML. 

* Para os clientes que já estão a utilizar a API Management, outro desafio é extrair as configurações existentes nos modelos do Gestor de Recursos. Para esses clientes, uma ferramenta chamada [**Extrator**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) no kit de recursos pode ajudar a gerar modelos extraindo configurações das suas instâncias de Gestão de API.  

## <a name="workflow"></a>Fluxo de trabalho

* Uma vez que os desenvolvedores da API tenham terminado de desenvolver e testar uma API, e tenham gerado os modelos API, eles podem submeter um pedido de pull para fundir as alterações ao repositório de editores. 

* Os editores da API podem validar o pedido de puxar e certificar-se de que as alterações são seguras e conformes. Por exemplo, podem verificar se apenas HTTPS é permitido comunicar com a API. A maioria das validações pode ser automatizada como um passo no gasoduto CI/CD.

* Uma vez aprovadas e fundidas com sucesso, os editores da API podem optar por as implementar na instância de Produção, quer no horário, quer a pedido. A implementação dos modelos pode ser automatizada utilizando [ações GitHub,](https://github.com/Azure/apimanagement-devops-samples) [Azure Pipelines,](/devops/pipelines/) [Azure PowerShell,](../azure-resource-manager/templates/deploy-powershell.md) [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md)ou outras ferramentas.

Com esta abordagem, a implementação de alterações da API em instâncias de Gestão de API pode ser automatizada, e é fácil promover mudanças de um ambiente para outro. Uma vez que diferentes equipas de desenvolvimento da API trabalharão em diferentes conjuntos de modelos e ficheiros API, evita interferências entre diferentes equipas.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Passos seguintes

- Consulte o Kit de [Recursos de Gestão Azure Azure Para obter](https://github.com/Azure/azure-api-management-devops-resource-kit) informações adicionais, ferramentas e modelos de amostra.