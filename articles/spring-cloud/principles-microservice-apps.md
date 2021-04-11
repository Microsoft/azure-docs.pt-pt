---
title: Java e OS base para aplicações de microserviços Azure Spring Cloud
description: Princípios para manter java saudável e sistema operativo base para aplicações de microservice Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c90062f1968cc7be5a742a67363f57b9632fdfa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878529"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java e Base OS para Aplicações de Microsserviços Spring

**Este artigo aplica-se a:** ✔️ Java

Seguem-se os princípios para a manutenção de java saudável e sistema operativo base para aplicações de microserviços da primavera.
## <a name="principles-for-healthy-java-and-base-os"></a>Princípios para java saudável e base OS
* Deve ser o mesmo sistema operativo de base entre os níveis - | básicos | padrão Premium, prémio.
    * Atualmente, as aplicações em Azure Spring Cloud usam uma mistura de Debian 10 e Ubuntu 18.04.
    * O serviço de construção VMware utiliza ubuntu 18.04.
* Deve ser o mesmo sistema operativo de base, independentemente dos pontos de partida de implantação - fonte | RIO JAR
    * Atualmente, as aplicações em Azure Spring Cloud usam uma mistura de Debian 10 e Ubuntu 18.04.
* O sistema operativo de base deve estar livre de vulnerabilidades de segurança.
    * O sistema operativo base Debian 10 tem 147 CVEs abertos.
    * O sistema operativo base Ubuntu 18.04 tem 132 CVEs abertos.
* Deve usar JRE sem cabeça.
    * Atualmente, aplicativos em Azure Spring Cloud usam JDK. JRE sem cabeça é uma imagem menor.
* Usará as mais recentes construções de Java.
    * Atualmente, as aplicações em Azure Spring Cloud usam Java 8 build 242. Esta é uma construção antiquada.
 
A Azul Systems irá procurar continuamente alterações nos sistemas operativos base e manter as últimas imagens incorporadas atualizadas. A Azure Spring Cloud procura alterações nas imagens e atualiza-as continuamente através das implementações.
 
## <a name="faq-for-azure-spring-cloud"></a>FAQ sobre o Azure Spring Cloud

* Que versões de Java são suportadas? Versão principal e número de construção.
    * Apoiar versões LTS - Java 8 e 11.
    * Usa a construção mais recente - por exemplo, neste momento, Java 8 construir 252 e Java 11 construir 7.
* Quem construiu estes tempos de java?
    * Sistemas Azul.
* O que é o sistema operativo base para imagens?
    * Ubuntu 20.04 LTS (Focal Fossa). As aplicações continuarão a manter-se na mais recente versão LTS do Ubuntu.
    * Ver [Ubuntu 20.04 LTS (Focal Fossa)](http://releases.ubuntu.com/focal/)
* Como posso descarregar um tempo de java suportado para dev local? 
    * Ver [Instalar o JDK para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-install)
* Como posso obter apoio para problemas a nível de tempo de java?
    * Abra um bilhete de apoio com suporte Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Implementação padrão na Nuvem de primavera de Azure

> ![Implementação predefinido](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)
* [Suporte a longo prazo de Java para Azure e Azure Stack](/azure/developer/java/fundamentals/java-jdk-long-term-support)