---
title: Exemplos de modelos do Azure Resource Manager - Serviço de Aplicações | Microsoft Docs
description: Exemplos de modelos do Azure Resource Manager para o serviço de aplicações
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 842ec98245522095334b9f17e8c12292b7c1dda8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835435"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelos do Azure Resource Manager para o serviço de aplicações

A tabela seguinte inclui ligações para modelos do Azure Resource Manager para o serviço de aplicações do Azure. Para obter recomendações sobre como evitar erros comuns ao criar modelos de aplicações, veja [documentação de orientação sobre a implementação de aplicações com modelos Azure Resource Manager](deploy-resource-manager-template.md).

Para saber mais sobre a sintaxe JSON e propriedades de recursos de serviços de aplicações, veja [tipos de recursos Microsoft. Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Implementar uma aplicação**||
| [Plano do serviço de aplicações e a aplicação básica do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Implementa uma aplicação de serviço de aplicações que está configurada para Linux. |
| [Plano do serviço de aplicações e a aplicação básica do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Implementa uma aplicação de serviço de aplicações que está configurada para Windows. |
| [Aplicação ligada a um repositório do GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implementa uma aplicação de serviço de aplicações que solicita código a partir do GitHub. |
| [Aplicação com blocos de implementação personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implementa uma aplicação de serviço de aplicações com ambientes/blocos de implementação personalizado. |
|**Configuração de uma aplicação**||
| [Certificado de aplicação do Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implementa um certificado de aplicação do serviço de aplicações a partir de um segredo do Azure Key Vault e utiliza-o para o enlace SSL. |
| [Aplicação com um domínio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implementa uma aplicação de serviço de aplicações com um nome de anfitrião personalizado. |
| [Aplicação com um domínio personalizado e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implementa uma aplicação de serviço de aplicações com um nome de anfitrião personalizado e obtém um certificado de aplicação do Key Vault para o enlace SSL. |
| [Aplicação com uma extensão GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implementa uma aplicação de serviço de aplicações com a extensão de site Golang. Em seguida, pode executar as aplicações Web desenvolvidas no Golang no Azure. |
| [Aplicação com Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implementa uma aplicação de serviço de aplicações com Java 8 e Tomcat 8 ativados. Em seguida, pode executar aplicações Java no Azure. |
|**Aplicação do Linux com recursos ligados**||
| [Aplicações no Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implementa uma aplicação de serviço de aplicações no Linux com a base de dados do Azure para MySQL. |
| [Aplicações no Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implementa uma aplicação de serviço de aplicações no Linux com a base de dados do Azure para PostgreSQL. |
|**Aplicação com recursos ligados**||
| [Aplicação com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implementa uma aplicação de serviço de aplicações no Windows com a base de dados do Azure para MySQL. |
| [Aplicação com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implementa uma aplicação de serviço de aplicações no Windows com a base de dados do Azure para PostgreSQL. |
| [Aplicação com uma base de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implementa uma aplicação de serviço de aplicações e uma base de dados SQL no nível de serviço básico. |
| [Aplicação com uma ligação de armazenamento de BLOBs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implementa uma aplicação de serviço de aplicações com uma cadeia de ligação de armazenamento de Blobs do Azure. Em seguida, pode utilizar o armazenamento de BLOBs a partir da aplicação. |
| [Aplicação com uma Cache do Azure para Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implementa uma aplicação de serviço de aplicações com uma Cache do Azure para Redis. |
|**Ambiente do Serviço de Aplicações para PowerApps**||
| [Criar um Ambiente de Serviço de Aplicações v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual. |
| [Criar um ambiente de Serviço de Aplicações v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configura o certificado SSL predefinido para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado SSL predefinido para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações v2 ILB. |
| | |
