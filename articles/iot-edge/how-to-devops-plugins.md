---
title: Habilitar CI/CD com plug-in Jenkins-Azure IoT Edge | Microsoft Docs
description: A extensão Azure IoT Edge para Jenkins permite que você integre IoT Edge tarefas de desenvolvimento e implantação à sua solução DevOps existente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510248"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrar Azure IoT Edge com pipelines do Jenkins

Azure IoT Edge tem suporte interno para o Azure DevOps e Azure DevOps Projects, mas também fornece um plug-in para expandir a funcionalidade DevOps para Jenkins. O [Jenkins](https://jenkins.io/) é um servidor de automação de software livre que usa plug-ins para dar suporte a muitos tipos de projetos de desenvolvimento e implantação, incluindo IOT Edge.

O plug-in Azure IoT Edge para Jenkins se concentra na integração contínua e na implantação contínua. Você pode criar um pipeline de compilação e push que cria módulos e envia por push suas imagens de contêiner para o registro de contêiner. Em seguida, crie um pipeline de liberação que implanta módulos para seus dispositivos IoT Edge.

Antes de começar a usar o plug-in IoT Edge para Jenkins, você precisa de um hub IoT no Azure e um registro de contêiner para armazenar suas imagens de contêiner. Use uma entidade de serviço do Azure para fornecer permissões de colaborador de Jenkins para o Hub IoT para que o plug-in possa criar implantações para seus dispositivos IoT Edge.

Se você estiver pronto para começar, encontre os detalhes de instalação e uso do [plug-in Azure IOT Edge para Jenkins](https://plugins.jenkins.io/azure-iot-edge).
