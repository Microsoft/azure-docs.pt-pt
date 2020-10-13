---
title: Amostras de modelo do gestor de recursos Azure
description: Encontre amostras de modelo do Azure Resource Manager para alguns dos cenários comuns do Serviço de Aplicações. Saiba como automatizar as suas tarefas de implementação ou gestão do Serviço de Aplicações.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88891050"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelos de gestor de recursos Azure para o Serviço de Aplicações

A tabela seguinte inclui links para modelos de Gestor de Recursos Azure para o Serviço de Aplicações Azure. Para recomendações sobre como evitar erros comuns quando estiver a criar modelos de aplicações, consulte [Orientação sobre a implementação de aplicações com modelos do Azure Resource Manager](deploy-resource-manager-template.md).

Para saber mais sobre a sintaxe JSON e propriedades para recursos de Serviços de Aplicação, consulte os [tipos de recursos microsoft.Web](/azure/templates/microsoft.web/allversions).

| Implementação de uma aplicação | Descrição |
|-|-|
| [Plano de Serviço de Aplicações e app básica do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Implementa uma aplicação de Serviço de Aplicações que está configurada para o Linux. |
| [Plano de Serviço de Aplicações e aplicação básica do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Implementa uma aplicação de Serviço de Aplicações que está configurada para windows. |
| [App ligada a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implementa uma aplicação de Serviço de Aplicações que retira código do GitHub. |
| [App com slots de implementação personalizadas](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implementa uma aplicação de Serviço de Aplicações com slots/ambientes de implementação personalizados. |
| [App com Ponto Final Privado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| Implementa uma aplicação de Serviço de Aplicações com um Ponto Final Privado. |
|**Configurar uma aplicação**| **Descrição** |
| [Certificado de aplicativo do Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implementa um certificado de aplicação do Serviço de Aplicações a partir de um segredo do Azure Key Vault e usa-o para a ligação TLS/SSL. |
| [App com um domínio personalizado e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implementa uma aplicação de Serviço de Aplicações com um nome de anfitrião personalizado e obtém um certificado de aplicação do Key Vault para a ligação TLS/SSL. |
| [App com extensão GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implementa uma aplicação de Serviço de Aplicações com a extensão do site Golang. Em seguida, pode executar as aplicações Web desenvolvidas no Golang no Azure. |
| [App com Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implementa uma aplicação de Serviço de Aplicações com Java 8 e Tomcat 8 ativada. Em seguida, pode executar aplicações Java no Azure. |
| [App com integração regional de VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Implementa uma aplicação de Serviço de Aplicações com integração regional de VNet ativada. |
|**Proteger uma aplicação**| **Descrição** |
| [App integrada com Gateway de Aplicação Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Implementa uma aplicação de Serviço de Aplicações e um Gateway de Aplicações, e isola o tráfego usando o ponto final do serviço e as restrições de acesso. |
|**App Linux com recursos conectados**| **Descrição** |
| [App em Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implementa uma aplicação de Serviço de Aplicações no Linux com Base de Dados Azure para o MySQL. |
| [App em Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implementa uma aplicação de Serviço de Aplicações no Linux com Base de Dados Azure para PostgreSQL. |
|**App com recursos conectados**| **Descrição** |
| [App com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implementa uma aplicação de Serviço de Aplicações no Windows com Base de Dados Azure para o MySQL. |
| [App com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implementa uma aplicação de Serviço de Aplicações no Windows com Base de Dados Azure para PostgreSQL. |
| [App com base de dados na Base de Dados Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implementa uma aplicação de Serviço de Aplicações e uma base de dados na Base de Dados Azure SQL ao nível do serviço Básico. |
| [App com uma ligação de armazenamento Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implementa uma aplicação de Serviço de Aplicações com uma cadeia de ligação de armazenamento Azure Blob. Em seguida, pode utilizar o armazenamento Blob a partir da aplicação. |
| [App com cache Azure para Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implementa uma aplicação de Serviço de Aplicações com uma Cache Azure para Redis. |
| [App ligada a um webapp backend](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| Implementa duas aplicações web (frontend e backend) conectadas de forma segura juntamente com a injeção de VNet e o Private Endpoint. |
|**Ambiente do Serviço de Aplicações**| **Descrição** |
| [Criar um Ambiente de Serviço de Aplicações v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual. |
| [Criar um ambiente de Serviço de Aplicações v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configura o certificado SSL predefinido para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado TLS/SSL padrão para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações ILB v2. |
| | |
