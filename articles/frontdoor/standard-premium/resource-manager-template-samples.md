---
title: Amostras de modelo do gestor de recursos - Porta frontal Azure
description: Informações sobre modelos de gestor de recursos Azure fornecidos para a porta frontal Azure.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561751"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Modelos de gestor de recursos Azure para Azure Front Door

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A tabela seguinte inclui links para modelos Azure Resource Manager para Azure Front Door, com arquiteturas de referência incluindo outros serviços Azure.

| Sample | Descrição |
|-|-|
| [Conjunto de regras](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Cria um perfil da porta da frente e conjunto de regras.  |
|**Origens do Serviço de Aplicações**| **Descrição** |
| [Serviço de Aplicações](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Cria uma aplicação de Serviço de Aplicações com um ponto final público e um perfil da Porta frontal.  |
| [Serviço de Aplicações com Link Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Cria uma aplicação de Serviço de Aplicações com um ponto final privado e um perfil da Porta frontal.  |
| [Ambiente de serviço de aplicativos com link privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Cria um ambiente de Serviço de Aplicações, uma app com um ponto final privado e um perfil da Porta frontal.  |
|**Origens das funções Azure**| **Descrição** |
| [Funções do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Cria uma aplicação Azure Functions com um ponto final público e um perfil da Porta frontal.  |
| [Funções Azure com Ligação Privada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Cria uma aplicação Azure Functions com um ponto final privado e um perfil da Porta Frontal.  |
|**Origens de Gestão da API**| **Descrição** |
| [Gestão da API (externa)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Cria um caso de Gestão API com integração VNet externa e um perfil da Porta Frontal.  |
|**Origens de armazenamento**| **Descrição** |
| [Site estático de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Cria uma conta de Armazenamento Azure e um website estático com um ponto final público e um perfil da Porta frontal.  |
| [Bolhas de armazenamento com link privado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Cria uma conta de armazenamento Azure e um recipiente blob com um ponto final privado e um perfil da porta da frente.  |
|**Origens do Gateway de Aplicação**| **Descrição** |
| [Gateway de Aplicação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Cria um Gateway de Aplicação e um perfil da porta da frente. |
|**Origens de máquinas virtuais**| **Descrição** |
| [Máquina virtual com serviço private link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Cria uma máquina virtual e um serviço de Ligação Privada e um perfil da Porta frontal. |
| | |
