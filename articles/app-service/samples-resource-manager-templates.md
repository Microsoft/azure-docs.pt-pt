---
title: Exemplos de modelo de Azure Resource Manager
description: Encontre exemplos de modelo de Azure Resource Manager para alguns dos cenários comuns do serviço de aplicativo. Saiba como automatizar suas tarefas de implantação ou gerenciamento do serviço de aplicativo.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: b1d5f20ccd2f2c637d7db668af10ef331947d018
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971201"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Modelos de Azure Resource Manager para o serviço de aplicativo

A tabela a seguir inclui links para modelos de Azure Resource Manager para Azure App Service. Para obter recomendações sobre como evitar erros comuns ao criar modelos de aplicativo, consulte [diretrizes sobre como implantar aplicativos com modelos de Azure Resource Manager](deploy-resource-manager-template.md).

Para saber mais sobre a sintaxe JSON e as propriedades dos recursos dos serviços de aplicativos, consulte [tipos de recurso Microsoft. Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Implantando um aplicativo**||
| [Plano do serviço de aplicativo e aplicativo Linux básico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Implanta um aplicativo do serviço de aplicativo configurado para Linux. |
| [Plano do serviço de aplicativo e aplicativo básico do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Implanta um aplicativo do serviço de aplicativo configurado para o Windows. |
| [Aplicativo vinculado a um repositório GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Implanta um aplicativo do serviço de aplicativo que recebe o código do GitHub. |
| [Aplicativo com slots de implantação personalizados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Implanta um aplicativo do serviço de aplicativo com ambientes/slots de implantação personalizados. |
|**Configurando um aplicativo**||
| [Certificado do aplicativo de Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Implanta um certificado de aplicativo do serviço de aplicativo de um segredo Azure Key Vault e o utiliza para associação SSL. |
| [Aplicativo com um domínio personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Implanta um aplicativo do serviço de aplicativo com um nome de host personalizado. |
| [Aplicativo com um domínio personalizado e SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Implanta um aplicativo do serviço de aplicativo com um nome de host personalizado e Obtém um certificado de aplicativo do Key Vault para associação SSL. |
| [Aplicativo com uma extensão GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Implanta um aplicativo do serviço de aplicativo com a extensão de site Golang. Em seguida, pode executar as aplicações Web desenvolvidas no Golang no Azure. |
| [Aplicativo com Java 8 e Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Implanta um aplicativo do serviço de aplicativo com Java 8 e Tomcat 8 habilitados. Em seguida, pode executar aplicações Java no Azure. |
|**Protegendo um aplicativo**||
| [Aplicativo integrado com gateway de Aplicativo Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Implanta um aplicativo do serviço de aplicativo e um gateway de aplicativo e isola o tráfego usando o ponto de extremidade de serviço e as restrições de acesso. |
|**Aplicativo Linux com recursos conectados**||
| [Aplicativo no Linux com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Implanta um aplicativo do serviço de aplicativo no Linux com o banco de dados do Azure para MySQL. |
| [Aplicativo no Linux com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Implanta um aplicativo do serviço de aplicativo no Linux com o banco de dados do Azure para PostgreSQL. |
|**Aplicativo com recursos conectados**||
| [Aplicativo com MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Implanta um aplicativo do serviço de aplicativo no Windows com o banco de dados do Azure para MySQL. |
| [Aplicativo com PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Implanta um aplicativo do serviço de aplicativo no Windows com o banco de dados do Azure para PostgreSQL. |
| [Aplicativo com um banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Implanta um aplicativo do serviço de aplicativo e um banco de dados SQL no nível de serviço básico. |
| [Aplicativo com uma conexão de armazenamento de BLOBs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Implanta um aplicativo do serviço de aplicativo com uma cadeia de conexão de armazenamento de BLOBs do Azure. Em seguida, você pode usar o armazenamento de BLOBs do aplicativo. |
| [Aplicativo com um cache do Azure para Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Implanta um aplicativo do serviço de aplicativo com um cache do Azure para Redis. |
|**Ambiente do Serviço de Aplicações**||
| [Criar um Ambiente de Serviço de Aplicações v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual. |
| [Criar um ambiente de Serviço de Aplicações v2 com um endereço ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Cria um ambiente de Serviço de Aplicações v2 na sua rede virtual com um endereço de balanceador de carga interno privado. |
| [Configura o certificado SSL predefinido para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações v2 ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Configura o certificado SSL predefinido para um ambiente de Serviço de Aplicações ILB ou um ambiente de Serviço de Aplicações v2 ILB. |
| | |
