---
title: Configurar o Windows aplicações de Java - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como configurar as aplicações de Java para executar nas instâncias do Windows padrão no serviço de aplicações do Azure.
keywords: serviço de aplicações do Azure, aplicação web, windows, sistemas operacionais, java
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
ms.openlocfilehash: 25434360bcc0155411451dbac065e0b7fad9c3bf
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617474"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurar um Windows aplicação de Java para o serviço de aplicações do Azure

Serviço de aplicações do Azure permite aos programadores de Java para rapidamente criar, implementar e dimensionar seus Tomcat ou aplicativos de web num serviço totalmente gerido com base em Windows de pacote de Java Standard Edition (SE). Implemente aplicações com o plug-ins do Maven na linha de comandos ou em editores de como o IntelliJ, o Eclipse ou o Visual Studio Code.

Este guia fornece os conceitos chave e instruções para programadores de Java no serviço de aplicações a utilizar. Se nunca tiver utilizado o serviço de aplicações do Azure, deve ler os [início rápido de Java](app-service-web-get-started-java.md) primeiro. Perguntas gerais sobre como utilizar o serviço de aplicações que não sejam específicas para o desenvolvimento de Java são respondidas no [FAQ do Windows de serviço de aplicações](faq-configuration-and-management.md).

> [!NOTE]
> Não consegue encontrar o que procura? Consulte a [FAQ de sistemas operacionais Windows](faq-configuration-and-management.md) ou o [guia de configuração do Linux de Java](containers/configure-language-java.md) para obter informações sobre como implantar e proteger a sua aplicação Java.

## <a name="configuring-tomcat"></a>Configurar o Tomcat

Para editar do Tomcat `server.xml` ou outros ficheiros de configuração, primeiro tome nota da sua versão principal do Tomcat no portal.

1. Localizar o diretório de raiz do Tomcat para a sua versão ao executar o `env` comando. Procure a variável de ambiente que começa com `AZURE_TOMCAT`e corresponde à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório de Tomcat para Tomcat 8.5.
1. Depois de ter identificado o diretório de raiz do Tomcat para a sua versão, copie o diretório de configuração para `D:\home`. Por exemplo, se `AZURE_TOMCAT85_HOME` tinha um valor de `D:\Program Files (x86)\apache-tomcat-8.5.37`, o caminho completo do diretório copiado configuração seria `D:\home\tomcat\conf`.

Por fim, reinicie o serviço de aplicações. As suas implementações devem ir para `D:\home\site\wwwroot\webapps` como antes.

## <a name="java-runtime-statement-of-support"></a>Instrução de tempo de execução Java de suporte

### <a name="jdk-versions-and-maintenance"></a>Versões do JDK e manutenção

Suportados Java Development Kit (JDK do Azure) é [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido por meio [Azul Systems](https://www.azul.com/).

Atualizações de versão principal serão fornecidas através de novas opções de tempo de execução no serviço de aplicações do Azure para Windows. Atualizar para estas versões mais recentes do Java ao configurar a sua implementação do serviço de aplicações de clientes e são responsáveis por testar e garantir que a atualização importante atende às suas necessidades.

JDKs suportados sejam automaticamente corrigidos trimestralmente em Janeiro, Abril, Julho e Outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Patches e correções para as vulnerabilidades de segurança importantes serão lançadas assim que estiverem disponíveis da Azul Systems. Uma vulnerabilidade de "maior" é definida por uma pontuação básica de 9.0 ou superior no [NIST vulnerabilidade de classificação sistema comum, versão 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Preterição e retirada

Se um runtime de Java suportada será extinto, os programadores do Azure com o tempo de execução afetado terá a chance um aviso de preterição, pelo menos, seis meses antes do tempo de execução foi descontinuado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem transferir a produção edição de Azul Zulu Enterprise JDK para desenvolvimento local do [site de download do Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

Suporte de produto para o [suportada pelo Azure JDK do Azul Zulu](https://www.azul.com/downloads/azure-only/zulu/) está disponível através do Microsoft quando desenvolver para o Azure ou [do Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [qualificado plano de suporte do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de tempo de execução

Os programadores podem [abra um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o JDKs de Zulu da Azul através do suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos Seguintes

Este tópico fornece a instrução de tempo de execução Java de suporte para o serviço de aplicações do Azure no Windows.

- Para saber mais sobre o alojamento de aplicações web com o serviço de aplicações do Azure, veja [descrição geral do serviço de aplicações](overview.md).
- Para obter informações sobre o Java no desenvolvimento do Azure, veja [do Azure para o Centro de desenvolvimento Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
