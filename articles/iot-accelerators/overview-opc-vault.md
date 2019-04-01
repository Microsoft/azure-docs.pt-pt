---
title: O que é a gestão de certificados do Azure IoT OPC UA | Documentos da Microsoft
description: Descrição geral do cofre OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759317"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>O que é a gestão de certificados do Azure IoT aberto plataforma comunicações (OPC) UA?

O Azure IoT OPC UA gestão de certificados, também conhecido como cofre OPC, é um microsserviço que pode configurar, registre-se e gerir o ciclo de vida do certificado para aplicações de servidor e cliente de OPC UA na cloud. Este artigo descreve casos de uso simples do cofre OPC.

## <a name="certificate-management"></a>Gestão de certificados

Por exemplo, uma empresa industrial precisa se conectar a suas máquinas de servidor OPC UA à respetiva aplicação de cliente acabou de criar. Quando o fabricante faz o acesso inicial da máquina do servidor, uma mensagem de erro imediatamente é mostrada no aplicativo de servidor OPC UA para indicar que a aplicação de cliente não está protegida. Esse mecanismo baseia-se na máquina do servidor OPC UA para impedir que qualquer acesso de aplicação não autorizado, o que impede que os hackers vicioso no chão shop.

## <a name="application-security-management"></a>Gestão de segurança de aplicações
Um profissional de segurança utiliza o Cofre OPC microsserviços para permitir facilmente o servidor OPC UA comunicar com qualquer aplicativo de cliente, porque o Cofre de OPC tem todas as funções para registro de certificado, armazenamento e gestão de ciclo de vida. Agora o servidor OPC UA é conectado com segurança, pode comunicar com a aplicação cliente acabou de criar

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura de cofre OPC completa
O diagrama seguinte ilustra a arquitetura de cofre OPC completa.

![Arquitetura do Cofre de OPC](media/overview-opc-vault-architecture/opc-vault.png)