---
title: Configurar aplicativos Java do Windows – serviço de Azure App | Microsoft Docs
description: Saiba como configurar aplicativos Java para serem executados nas instâncias padrão do Windows no serviço Azure App.
keywords: serviço de aplicativo do Azure, aplicativo Web, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498518"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurar um aplicativo Java do Windows para o serviço Azure App

Azure App serviço permite aos desenvolvedores de Java criar, implantar e dimensionar rapidamente seus aplicativos Web empacotados Tomcat ou Java Standard Edition (SE) em um serviço totalmente gerenciado baseado no Windows. Implante aplicativos com plug-ins do Maven a partir da linha de comando ou em editores como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores Java usando o no serviço de aplicativo. Se você nunca usou Azure App serviço, leia primeiro o [início rápido do Java](app-service-web-get-started-java.md) . As perguntas gerais sobre como usar o serviço de aplicativo que não são específicas do desenvolvimento do Java são respondidas nas [perguntas frequentes do Windows do serviço de aplicativo](faq-configuration-and-management.md).

> [!NOTE]
> Não consegue encontrar o que procura? Consulte as [perguntas frequentes sobre o OSS do Windows](faq-configuration-and-management.md) ou o guia de configuração do [Java Linux](containers/configure-language-java.md) para obter informações sobre como implantar e proteger seu aplicativo Java.

## <a name="configuring-tomcat"></a>Configurando o Tomcat

Para editar os arquivos `server.xml` de configuração ou do Tomcat, primeiro anote a sua versão principal do Tomcat no Portal.

1. Localize o diretório base do Tomcat para sua versão executando o `env` comando. Procure a variável de ambiente que começa com `AZURE_TOMCAT`e corresponde à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório Tomcat para Tomcat 8,5.
1. Depois de identificar o diretório base do Tomcat para sua versão, copie o diretório de configuração `D:\home`para. Por exemplo, se `AZURE_TOMCAT85_HOME` tiver um valor de `D:\Program Files (x86)\apache-tomcat-8.5.37`, o novo caminho do diretório copiado seria `D:\home\apache-tomcat-8.5.37`.

Por fim, reinicie o serviço de aplicativo. Suas implantações devem ir `D:\home\site\wwwroot\webapps` para assim como antes.

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do Java Runtime

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O Java Development Kit (JDK) com suporte do Azure é [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido por meio de [sistemas azul](https://www.azul.com/).

As atualizações da versão principal serão fornecidas por meio de novas opções de tempo de execução no serviço Azure App para Windows. Os clientes atualizam para essas versões mais recentes do Java Configurando sua implantação do serviço de aplicativo e são responsáveis por testar e garantir que a atualização principal atenda às suas necessidades.

Os JDKs com suporte são automaticamente corrigidos em uma base trimestral em Janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para vulnerabilidades de segurança principais serão lançados assim que forem disponibilizados por meio de sistemas azul. Uma vulnerabilidade "principal" é definida por uma pontuação base de 9,0 ou superior no [sistema de Pontuação de vulnerabilidade comum do NIST, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e aposentadoria

Se um tempo de execução de Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de substituição pelo menos seis meses antes de o tempo de execução ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a edição de produção do azul Zulu Enterprise JDK para o desenvolvimento local do [site de download do azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte a desenvolvimento

O suporte ao produto para o [azul Zulu com suporte do Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível por meio da Microsoft ao desenvolver para o azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o azul Zulu JDKs por meio do suporte do Azure se eles tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos Seguintes

Este tópico fornece a declaração de suporte do Java em tempo de execução para Azure App serviço no Windows.

- Para saber mais sobre como hospedar aplicativos Web com o serviço Azure App, consulte [visão geral do serviço de aplicativo](overview.md).
- Para obter informações sobre o Java no desenvolvimento do Azure, consulte [Azure para Java dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
