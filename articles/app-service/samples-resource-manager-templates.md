---
title: Amostras de modelo de gestor de recursos azure
description: Encontre amostras de modelo do Gestor de Recursos Azure para alguns dos cenários comuns do Serviço de Aplicações. Saiba automatizar as suas tarefas de implementação ou gestão do Serviço de Aplicações.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 2d77dcbd07a67e3ba50d70770515416b45fbc7a3
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637912"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelos de Gestor de Recursos Azure para serviço de aplicações

A tabela seguinte inclui links para modelos de Gestor de Recursos Azure para o Serviço de Aplicações Azure. Para recomendações sobre como evitar erros comuns ao criar modelos de aplicações, consulte [orientação sobre a implementação de aplicações com modelos](deploy-resource-manager-template.md)de Gestor de Recursos Azure .

Para conhecer a sintaxe jSON e propriedades para recursos de Serviços de Aplicações, consulte os tipos de [recursos Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Implementação de uma aplicação**||
| [Plano de serviço de aplicativos e aplicativo básico linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Implementa uma aplicação de Serviço de Aplicações que está configurada para o Linux. |
| [Plano de serviço de aplicativos e aplicativo básico do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Implementa uma aplicação do Serviço de Aplicações que está configurada para windows. |
| [App ligada a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implementa uma aplicação do App Service que retira código do GitHub. |
| [App com ranhuras de implementação personalizadas](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implementa uma aplicação de Serviço de Aplicações com ranhuras/ambientes de implementação personalizadas. |
|**Configurar uma aplicação**||
| [Certificado de aplicativo de Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implementa um certificado de aplicação do App Service a partir de um segredo azure Key Vault e usa-o para ligação TLS/SSL. |
| [App com um domínio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implementa uma aplicação de Serviço de Aplicações com um nome de anfitrião personalizado. |
| [App com domínio personalizado e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implementa uma aplicação de Serviço de Aplicações com um nome de anfitrião personalizado e obtém um certificado de aplicação da Key Vault para a ligação TLS/SSL. |
| [App com extensão GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implementa uma aplicação de Serviço de Aplicações com a extensão do site Golang. Em seguida, pode executar as aplicações Web desenvolvidas no Golang no Azure. |
| [App com Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implementa uma aplicação de Serviço de Aplicações com Java 8 e Tomcat 8 ativado. Em seguida, pode executar aplicações Java no Azure. |
| [App com integração regional vNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Implementa uma aplicação de Serviço de Aplicações com integração vnet regional ativada. |
|**Proteger uma aplicação**||
| [App integrada com Gateway de Aplicação Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Implementa uma aplicação de Serviço de Aplicações e um Gateway de Aplicações, e isola o tráfego usando o ponto final do serviço e restrições de acesso. |
|**Aplicativo Linux com recursos conectados**||
| [App em Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implementa uma aplicação de serviço de aplicações no Linux com base de dados Azure para MySQL. |
| [App em Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implementa uma aplicação de Serviço de Aplicações no Linux com base de dados Azure para PostgreSQL. |
|**App com recursos conectados**||
| [App com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implementa uma aplicação de serviço de aplicações no Windows com base de dados Azure para MySQL. |
| [App com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implementa uma aplicação de serviço de aplicações no Windows com base de dados Azure para PostgreSQL. |
| [App com base de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implementa uma aplicação de Serviço de Aplicações e uma base de dados SQL ao nível básico de serviço. |
| [App com uma conexão de armazenamento Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implementa uma aplicação de Serviço de Aplicações com uma cadeia de conexão de armazenamento Azure Blob. Em seguida, pode utilizar o armazenamento Blob a partir da aplicação. |
| [App com um Cache Azure para Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implementa uma aplicação de Serviço de Aplicações com um Azure Cache para Redis. |
|**Ambiente de serviço de aplicativos**||
| [Criar um Ambiente de Serviço de Aplicações v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual. |
| [Criar um ambiente de Serviço de Aplicações v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configura o certificado SSL predefinido para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado padrão TLS/SSL para um ambiente de serviço de aplicações ILB ou um ambiente de serviço de aplicações ILB v2. |
| | |
