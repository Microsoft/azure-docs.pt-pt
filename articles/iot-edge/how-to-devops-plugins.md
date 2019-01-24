---
title: Ativar o CI/CD com o plug-in Jenkins - Azure IoT Edge | Documentos da Microsoft
description: A extensão Azure IoT Edge do Jenkins permite-lhe integrar as tarefas de devlopment e implementação de IoT Edge em sua solução de DevOps existente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 016d1c5d389cf1b9e82194e9d273863da1138d2b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830376"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrar o Azure IoT Edge com Jenkins pipelines

O Azure IoT Edge tem suporte incorporado para DevOps do Azure e projetos de DevOps do Azure, mas também fornece um plug-in para expandir a funcionalidade de DevOps jenkins. [Jenkins](https://jenkins.io/) é um servidor de automatização de código-fonte aberto que utiliza o plug-ins para suportar muitos tipos de projetos de desenvolvimento e implementação, incluindo o IoT Edge. 

O plug-in do Azure IoT Edge do Jenkins se concentra na integração contínua e implementação contínua. Pode criar um pipeline de compilação e emissão, que cria módulos e envia suas imagens de contentor para o seu registo de contentor. Em seguida, crie um pipeline de lançamento que implementa módulos para os seus dispositivos IoT Edge. 

Antes de começar a utilizar o plug-in do IoT Edge do Jenkins, precisa de um hub IoT no Azure e um registo de contentores para armazenar as imagens de contentor. Utilize um Principal de serviço do Azure para dar permissões de contribuinte do Jenkins ao seu hub IoT, para que o plug-in pode criar implementações para o seu IoT Edge dispositivos. 

Se estiver pronto para começar a utilizar, localizar a instalação e utilizar detalhes para o [Plug-in do Azure IoT Edge para Jenkinx](https://plugins.jenkins.io/azure-iot-edge).