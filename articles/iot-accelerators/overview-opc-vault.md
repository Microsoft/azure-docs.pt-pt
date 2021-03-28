---
title: O que é OPC Vault - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do OPC Vault. Pode configurar, registar e gerir o ciclo de vida do certificado para aplicações OPC UA na nuvem.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646926"
---
# <a name="what-is-opc-vault"></a>O que é o Cofre OPC?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O OPC Vault é um microserviço que pode configurar, registar e gerir o ciclo de vida do certificado para servidores UA OPC e aplicações de clientes na nuvem. Este artigo descreve os simples casos de utilização do Cofre OPC.

## <a name="certificate-management"></a>Gestão de certificados

Por exemplo, uma empresa de fabrico precisa de ligar a sua máquina de servidores OPC UA à sua aplicação de cliente recém-construída. Quando o fabricante faz o acesso inicial à máquina do servidor, é imediatamente mostrada uma mensagem de erro na aplicação do servidor UA OPC para indicar que a aplicação do cliente não é segura. Este mecanismo é construído na máquina de servidores OPC UA para impedir qualquer acesso não autorizado à aplicação, o que impede a invasão viciosa no chão da loja.

## <a name="application-security-management"></a>Gestão da segurança de aplicações
Um profissional de segurança utiliza o microserviço OPC Vault para permitir facilmente que o servidor OPC UA comunique com qualquer aplicação do cliente, porque o OPC Vault tem todas as funções para registo de certificados, armazenamento e gestão do ciclo de vida. Agora que o servidor OPC UA está conectado de forma segura, pode comunicar com a aplicação de cliente recém-construída

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura completa do Cofre OPC
O diagrama seguinte ilustra a arquitetura completa do Cofre OPC.

![Arquitetura do Cofre OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre o OPC Vault e os seus usos, aqui está o próximo passo sugerido:

[Arquitetura do Cofre OPC](overview-opc-vault-architecture.md)