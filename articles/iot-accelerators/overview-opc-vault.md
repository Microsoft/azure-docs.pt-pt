---
title: O que é o cofre do OPC – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do cofre OPC. Ele pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos OPC UA na nuvem.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826209"
---
# <a name="what-is-opc-vault"></a>O que é o cofre do OPC?

O cofre do OPC é um microserviço que pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos cliente e servidor OPC UA na nuvem. Este artigo descreve os casos de uso simples do cofre do OPC.

## <a name="certificate-management"></a>Gestão de certificados

Por exemplo, uma empresa de fabricação precisa conectar seu computador de servidor OPC UA ao seu aplicativo cliente recém-criado. Quando o fabricante faz o acesso inicial da máquina do servidor, uma mensagem de erro é exibida imediatamente no aplicativo de servidor do OPC UA para indicar que o aplicativo cliente não é seguro. Esse mecanismo é criado no computador do servidor OPC UA para evitar qualquer acesso de aplicativo não autorizado, o que impede a invasão de vicioso no chão de fábrica.

## <a name="application-security-management"></a>Gerenciamento de segurança de aplicativos
Um profissional de segurança usa o microserviço do cofre OPC para permitir que o servidor OPC UA se comunique com qualquer aplicativo cliente, porque o cofre do OPC tem todas as funções para o registro de certificado, o armazenamento e o gerenciamento do ciclo de vida. Agora o servidor OPC UA está conectado com segurança, ele pode se comunicar com o aplicativo cliente criado recentemente

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura completa do cofre OPC
O diagrama a seguir ilustra a arquitetura completa do cofre OPC.

![Arquitetura do cofre OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre o cofre do OPC e seus usos, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Arquitetura do cofre OPC](overview-opc-vault-architecture.md)
