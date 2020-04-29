---
title: Ativar CI/CD com plugin Jenkins - Azure IoT Edge [ Microsoft Docs
description: A extensão Azure IoT Edge para jenkins permite-lhe integrar tarefas de desenvolvimento e implementação do IoT Edge na sua solução DevOps existente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76510248"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrar o Azure IoT Edge com os oleodutos Jenkins

O Azure IoT Edge tem suporte integrado para os projetos Azure DevOps e Azure DevOps, mas também fornece um plugin para expandir a funcionalidade DevOps à Jenkins. [Jenkins](https://jenkins.io/) é um servidor de automação de código aberto que usa plugins para suportar muitos tipos de projetos de desenvolvimento e implementação, incluindo IoT Edge.

O plugin Azure IoT Edge para Jenkins centra-se na integração contínua e na implantação contínua. Pode criar um oleoduto de construção e impulso que constrói módulos e empurra as suas imagens de contentores para o seu registo de contentores. Em seguida, crie um gasoduto de libertação que implemente módulos para os seus dispositivos IoT Edge.

Antes de começar a utilizar o plugin IoT Edge para jenkins, precisa de um hub IoT em Azure e de um registo de contentores para guardar as imagens do seu contentor. Utilize um Diretor de Serviço Azure para dar permissões ao colaborador jenkins para o seu hub IoT para que o plugin possa criar implementações para os seus dispositivos IoT Edge.

Se estiver pronto para começar, encontre a instalação e use detalhes para o [plugin Azure IoT Edge para Jenkins](https://plugins.jenkins.io/azure-iot-edge).
