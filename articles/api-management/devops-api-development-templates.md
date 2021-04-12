---
title: CI/CD para gestão da API Azure utilizando modelos ARM
description: Introdução aos DevOps da API com Azure API Management, utilizando modelos do Azure Resource Manager para gerir implementações de API num pipeline CI/CD
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183163"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD para gestão de API usando modelos de Gestor de Recursos Azure

Este artigo mostra-lhe como usar API DevOps com Azure API Management, através de modelos Azure Resource Manager. Com o valor estratégico das APIs, um gasoduto de integração contínua e implantação contínua (CI/CD) tornou-se um aspeto importante do desenvolvimento da API. Permite que as organizações automatizem a implementação de alterações de API sem passos manuais propensos a erros, detetem problemas mais cedo e, em última análise, entreguem valor aos utilizadores mais rapidamente. 

Para mais detalhes, ferramentas e amostras de código para implementar a abordagem DevOps descrita neste artigo, consulte o kit de [recursos de gestão de devops de gestão da Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) em GitHub. Como os clientes trazem uma vasta gama de culturas de engenharia e soluções de automação existentes, a abordagem não é uma solução de tamanho único.

## <a name="the-problem"></a>O problema

As organizações hoje em dia têm normalmente múltiplos ambientes de implantação (como desenvolvimento, teste e produção) e utilizam instâncias separadas de Gestão de API para cada ambiente. Alguns casos são partilhados por várias equipas de desenvolvimento, que são responsáveis por diferentes APIs com diferentes cadências de lançamento.

Como resultado, os clientes enfrentam os seguintes desafios:

* Como automatizar a implantação de APIs na Gestão de API
* Como migrar configurações de um ambiente para outro
* Como evitar interferências entre diferentes equipas de desenvolvimento que partilham a mesma instância de Gestão da API

## <a name="manage-configurations-in-resource-manager-templates"></a>Gerir configurações em modelos de Gestor de Recursos

A imagem a seguir ilustra a abordagem proposta. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagrama que ilustra DevOps com Gestão de API.":::

Neste exemplo, existem dois ambientes de implantação: *Desenvolvimento* e *Produção.* Cada um tem a sua própria instância de Gestão de API. 

* Os desenvolvedores de API têm acesso à instância de Desenvolvimento e podem usá-lo para desenvolver e testar as suas APIs. 
* Uma equipa designada chamada *API editores* gere a instância de Produção.

A chave nesta abordagem proposta é manter todas as configurações de Gestão da API nos [modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md). A organização deve manter estes modelos num sistema de controlo de fontes como o Git. Como ilustrado na imagem, um repositório de editores contém todas as configurações da instância de Gestão da API de Produção numa coleção de modelos:

|Modelo  |Description  |
|---------|---------|
|Modelo de serviço     | Configurações de nível de serviço da instância de Gestão da API, tais como o nível de preços e domínios personalizados.         |
|Modelos compartilhados     |  Recursos partilhados ao longo de uma instância de Gestão da API, tais como grupos, produtos e madeireiros.    |
|Modelos de API     |  Configurações de APIs e seus subreufontes: operações, políticas, definições de diagnóstico.        |
|Modelo principal principal     |   Liga tudo, [ligando-os a](../azure-resource-manager/templates/linked-templates.md) todos os modelos e implantando-os em ordem. Para implementar todas as configurações para uma instância de Gestão da API, implemente o modelo principal. Também pode implementar cada modelo individualmente.       |

Os desenvolvedores da API irão recorrer ao repositório do Publisher para um repositório de desenvolvedores e trabalharão nas alterações para as suas APIs. Na maioria dos casos, focam-se nos modelos de API para as suas APIs e não precisam de alterar os modelos partilhados ou de serviço.

## <a name="migrate-configurations-to-templates"></a>Migrar configurações para modelos
Os desenvolvedores de API enfrentam desafios ao trabalhar com modelos de Gestor de Recursos:

* Os desenvolvedores de API muitas vezes trabalham com a [Especificação OpenAPI](https://github.com/OAI/OpenAPI-Specification) e podem não estar familiarizados com esquemas de Gestor de Recursos. Os modelos de autoria manualmente podem ser propensos a erros. 

   Uma ferramenta chamada [Criador](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) no kit de recursos pode ajudar a automatizar a criação de modelos API com base num ficheiro de especificação API aberto. Além disso, os desenvolvedores podem fornecer políticas de Gestão de API para uma API em formato XML. 

* Para os clientes que já estão a utilizar a API Management, outro desafio é extrair as configurações existentes nos modelos do Gestor de Recursos. Para esses clientes, uma ferramenta chamada [Extrator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) no kit de recursos pode ajudar a gerar modelos extraindo configurações das suas instâncias de Gestão de API.  

## <a name="workflow"></a>Fluxo de trabalho

* Depois de os desenvolvedores da API terem terminado o desenvolvimento e teste de uma API, e terem gerado os modelos API, podem apresentar um pedido de pull para fundir as alterações ao repositório de editores. 

* Os editores da API podem validar o pedido de puxar e certificar-se de que as alterações são seguras e conformes. Por exemplo, podem verificar se apenas HTTPS é permitido comunicar com a API. A maioria das validações pode ser automatizada como um passo no gasoduto CI/CD.

* Uma vez aprovadas e fundidas com sucesso, os editores da API podem optar por as implementar na instância de Produção, quer no horário, quer a pedido. A implementação dos modelos pode ser automatizada utilizando [ações GitHub,](https://github.com/Azure/apimanagement-devops-samples) [Azure Pipelines,](/azure/devops/pipelines) [Azure PowerShell,](../azure-resource-manager/templates/deploy-powershell.md) [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md)ou outras ferramentas.


Com esta abordagem, uma organização pode automatizar a implementação de alterações da API em instâncias de Gestão de API, e é fácil promover mudanças de um ambiente para outro. Como diferentes equipas de desenvolvimento da API trabalharão em diferentes conjuntos de modelos e ficheiros API, evita interferências entre diferentes equipas.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Passos seguintes

- Consulte o Kit de [Recursos de Gestão Azure Azure Para obter](https://github.com/Azure/azure-api-management-devops-resource-kit) informações adicionais, ferramentas e modelos de amostra.