---
title: O que é o Cofre de OPC - Azure | Documentos da Microsoft
description: Descrição geral do cofre OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99dfcaeb1ef5b52e6827f1b3ac65d6201557a8fb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783522"
---
# <a name="what-is-opc-vault"></a>O que é o OPC Cofre?

O Cofre de OPC é um microsserviço que pode configurar, registe-se e gerenciar o ciclo de vida do certificado para o servidor OPC UA e aplicações de cliente na cloud. Este artigo descreve casos de uso simples do cofre OPC.

## <a name="certificate-management"></a>Gestão de certificados

Por exemplo, uma empresa industrial precisa se conectar a suas máquinas de servidor OPC UA à respetiva aplicação de cliente acabou de criar. Quando o fabricante faz o acesso inicial da máquina do servidor, uma mensagem de erro imediatamente é mostrada no aplicativo de servidor OPC UA para indicar que a aplicação de cliente não está protegida. Esse mecanismo baseia-se na máquina do servidor OPC UA para impedir que qualquer acesso de aplicação não autorizado, o que impede que os hackers vicioso no chão shop.

## <a name="application-security-management"></a>Gestão de segurança de aplicações
Um profissional de segurança utiliza o Cofre OPC microsserviços para permitir facilmente o servidor OPC UA comunicar com qualquer aplicativo de cliente, porque o Cofre de OPC tem todas as funções para registro de certificado, armazenamento e gestão de ciclo de vida. Agora o servidor OPC UA é conectado com segurança, pode comunicar com a aplicação cliente acabou de criar

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura de cofre OPC completa
O diagrama seguinte ilustra a arquitetura de cofre OPC completa.

![Arquitetura do Cofre de OPC](media/overview-opc-vault-architecture/opc-vault.png)