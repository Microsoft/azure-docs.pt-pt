---
title: Ativar CI/CD com plugin Jenkins - Azure IoT Edge / Microsoft Docs
description: A extensão Azure IoT Edge para jenkins permite-lhe integrar tarefas de desenvolvimento e implementação IoT Edge na sua solução de DevOps existente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76510248"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integre a Azure IoT Edge com os oleodutos Jenkins

O Azure IoT Edge tem suporte incorporado para projetos Azure DevOps e Azure DevOps, mas também fornece um plugin para expandir a funcionalidade DevOps para Jenkins. [Jenkins](https://jenkins.io/) é um servidor de automação de código aberto que usa plugins para suportar muitos tipos de projetos de desenvolvimento e implementação, incluindo IoT Edge.

O plugin Azure IoT Edge para Jenkins foca-se na integração contínua e na implementação contínua. Pode criar um gasoduto de construção e de pressão que constrói módulos e empurra as imagens dos seus contentores para o registo do seu contentor. Em seguida, crie um oleoduto de desbloqueio que implante módulos nos seus dispositivos IoT Edge.

Antes de começar a usar o plugin IoT Edge para jenkins, precisa de um hub IoT em Azure e um registo de contentores para guardar as imagens do seu contentor. Utilize um Diretor de Serviço Azure para dar permissão de colaboradores da Jenkins ao seu hub IoT para que o plugin possa criar implementações para os seus dispositivos IoT Edge.

Se estiver pronto para começar, encontre a instalação e use detalhes para o [plugin Azure IoT Edge para Jenkins](https://plugins.jenkins.io/azure-iot-edge).
