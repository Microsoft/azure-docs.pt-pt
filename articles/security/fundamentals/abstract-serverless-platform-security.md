---
title: Abstra-Azure Functions e segurança de plataforma sem servidor
description: Resumo para o Azure Functions e white paper de segurança de plataforma sem servidor.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: cf59f6343960a164be66ae27170d722eb26dc173
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727704"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Azure Functions e segurança de plataforma sem servidor
## <a name="abstract"></a>Abstrato
A maioria das empresas precisa de uma quantidade significativa de recursos e tempo para gerenciar servidores, o que aumenta o custo. Se as empresas puderem usar menos recursos para gerenciar servidores, elas poderão se concentrar na criação de ótimos aplicativos.  

A computação sem servidor ajuda você a fazer exatamente isso, porque a infraestrutura que você precisa para executar e dimensionar seus aplicativos é gerenciada para você. A computação sem servidor é a abstração dos servidores, da infraestrutura e dos sistemas operativos. A computação sem servidor é orientada pela reação a eventos e gatilhos, que estão ocorrendo quase em tempo real — na nuvem. 

Como um serviço totalmente gerenciado, o gerenciamento de servidores e o planejamento de capacidade são invisíveis para o desenvolvedor. A estrutura sem servidor ajuda a desenvolver e implantar aplicativos sem servidor usando o Azure Functions. É uma CLI (interface de linha de comando) que oferece estrutura e automação para ajudá-lo a criar arquiteturas sofisticadas, controladas por eventos e sem servidor, compostas por funções e eventos. Uma função do Azure é uma unidade independente de implantação, como um Microservice. É meramente código, implantado na nuvem, que é escrito com mais frequência para executar um único trabalho.

Apesar dos benefícios, a segurança sem servidor tem seus próprios fatores de risco para lidar. A abordagem sem servidor não introduz novas preocupações de segurança, mas requer ter uma abordagem para as preocupações de segurança existentes. Este white paper concentra-se nessas questões de segurança: 
* Benefícios de uma plataforma sem servidor
* Problemas de segurança na computação sem servidor
* Problemas críticos de segurança e mitigações no contexto do Azure
* Protegendo a plataforma sem servidor da Microsoft

[Baixar o white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

