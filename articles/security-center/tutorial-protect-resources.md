---
title: Acesso & controlos de aplicação tutorial - Azure Security Center
description: Este tutorial mostra-lhe como configurar uma política de acesso vm just-in-time e uma política de controlo de aplicações.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 89d04588d07407be5dbd7ddbafd6543ed461462e
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945724"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: proteger os seus recursos com o Centro de Segurança do Azure
O Centro de Segurança limita a exposição a ameaças ao utilizar controlos de acesso e aplicações para bloquear atividade maliciosa. O acesso à máquina virtual (VM) just-in-time (VM) reduz a sua exposição a ataques, permitindo-lhe negar o acesso persistente aos VM. Em alternativa, o utilizador fornece acesso controlado e auditado a VMs apenas quando necessário. Os controlos de aplicações adaptáveis ajudam a proteger as VMs contra software maligno ao controlar as aplicações que podem ser executadas nas suas VMs. O Security Center utiliza machine learning para analisar os processos em curso no VM e ajuda-o a aplicar regras de listagem usando esta inteligência.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure uma política de acesso em VM just-in-time
> * Configurar uma política de controlo de aplicações

## <a name="prerequisites"></a>Pré-requisitos
Para analisar as funcionalidades abrangidas por este tutorial, tem de ter o Azure Defender habilitado. Pode tentar a Azure Defender sem custos. Para mais informações, consulte [Try Azure Defender](security-center-pricing.md).

## <a name="manage-vm-access"></a>Gerir o acesso à VM
O acesso JIT VM pode ser utilizado para bloquear o tráfego de entrada nos seus VMs Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário.

As portas de gestão não precisam de estar abertas permanentemente. Apenas têm de estar abertas enquanto estiver ligado à VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o tempo é permitido, o Security Center utiliza as regras do Network Security Group (NSG), que restringem o acesso às portas de gestão para que não possam ser alvo de agressores.

Siga as orientações em [Secure your management ports com acesso just-in-time](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Proteger VMs contra software maligno
Os controlos de aplicações adaptáveis ajudam a definir um conjunto de aplicações cuja execução é permitida em grupos de recursos configurados, o que, entre outras vantagens, ajuda a proteger as suas VMs contra software maligno. O Security Center utiliza machine learning para analisar os processos em curso no VM e ajuda-o a aplicar regras de listagem usando esta inteligência.

Siga as orientações em [Utilizar controlos de aplicação adaptativos para reduzir as superfícies de ataque das suas máquinas](security-center-adaptive-application.md).

## <a name="next-steps"></a>Próximos passos
Neste tutorial, aprendeu a limitar a exposição a ameaças ao:

> [!div class="checklist"]
> * Configurar uma política de acesso em VM just-in-time para fornecer acesso controlado e auditado aos VM apenas quando necessário
> * Configurar uma política de controlos de aplicações adaptáveis para controlar as aplicações que podem ser executadas nas suas VMs

Avance para o próximo tutorial para aprender a responder a incidentes de segurança.

> [!div class="nextstepaction"]
> [Tutorial: responder a incidentes de segurança](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
