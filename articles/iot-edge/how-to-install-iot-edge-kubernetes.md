---
title: Como instalar ioT Edge em Kubernetes Microsoft Docs
description: Saiba como instalar ioT Edge em Kubernetes usando um ambiente de cluster de desenvolvimento local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471290"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Como instalar o IoT Edge em Kubernetes (Pré-visualização)

O IoT Edge pode integrar-se com kubernetes usando-o como uma camada de infraestrutura resiliente e altamente disponível. Aqui é onde este suporte se encaixa numa solução IoT Edge de alto nível:

![k8s introdução](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Um bom modelo mental para esta integração é pensar na Kubernetes como outro ambiente operativo que as aplicações IoT Edge podem funcionar para além do Linux e do Windows.

## <a name="architecture"></a>Arquitetura 
Em Kubernetes, O IoT Edge fornece definição de *recursos personalizados* (CRD) para implementações de carga de trabalho de borda. O IoT Edge Agent assume o papel de um controlador de *CRD* que concilia o estado desejado pela nuvem com o estado de aglomerado local.

A vida útil do módulo é gerida pelo programador Kubernetes, que mantém a disponibilidade do módulo e escolhe a sua colocação. O IoT Edge gere a plataforma de aplicação de borda que funciona por cima, conciliando continuamente o estado desejado especificado no IoT Hub com o estado no cluster de borda. O modelo de aplicação ainda é o modelo familiar baseado em módulos e rotas IoT Edge. O controlador IoT Edge Agent executa o modelo de aplicação de tradução *automática* IoT Edge para as construções nativas kubernetes como cápsulas, implementações, serviços, etc.

Aqui está um diagrama de arquitetura de alto nível:

![kubernetes arco](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Todos os componentes da implementação da borda são reparados num espaço de nome kubernetes específico do dispositivo, permitindo partilhar os mesmos recursos de cluster entre vários dispositivos de borda e as suas implementações.

>[!NOTE]
>IoT Edge on Kubernetes está em [pré-visualização pública.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Tutoriais e referências 

Consulte o [IoT Edge no mini-site de pré-visualização do cs da Cubernetes](https://aka.ms/edgek8sdoc) para obter mais informações, incluindo tutoriais aprofundados e referências.
