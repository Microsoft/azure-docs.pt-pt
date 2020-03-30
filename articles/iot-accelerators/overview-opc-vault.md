---
title: O que é OPC Vault - Azure Microsoft Docs
description: Este artigo fornece uma visão geral do Cofre opc. Pode configurar, registar e gerir o ciclo de vida do certificado para aplicações da OPC UA na nuvem.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826209"
---
# <a name="what-is-opc-vault"></a>O que é OPC Vault?

OPc Vault é um microserviço que pode configurar, registar e gerir o ciclo de vida do certificado para o servidor da OPC UA e aplicações de clientes na nuvem. Este artigo descreve os casos de uso simples do Cofre opc.

## <a name="certificate-management"></a>Gestão de certificados

Por exemplo, uma empresa de fabrico precisa de ligar a sua máquina de servidor Escópede OPC à sua aplicação de cliente recém-construída. Quando o fabricante efetua o acesso inicial da máquina do servidor, uma mensagem de erro é imediatamente mostrada na aplicação do servidor OPC UA para indicar que a aplicação do cliente não é segura. Este mecanismo é construído na máquina de servidores OPC UA para impedir qualquer acesso não autorizado à aplicação, o que impede a invasão viciosa no chão da loja.

## <a name="application-security-management"></a>Gestão de segurança de aplicações
Um profissional de segurança usa microserviço OPC Vault para permitir facilmente que o servidor DaUA da OPC se comunique com qualquer aplicação do cliente, porque o OPC Vault tem todas as funções para registo de certificados, armazenamento e gestão de ciclo de vida. Agora o servidor OPC UA está conectado de forma segura, pode comunicar com a aplicação cliente recém-construída

## <a name="the-complete-opc-vault-architecture"></a>A arquitetura completa do Cofre OPC
O diagrama que se segue ilustra a arquitetura completa do Cofre opc.

![Arquitetura do Cofre OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre o OPC Vault e os seus usos, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Arquitetura do Cofre OPC](overview-opc-vault-architecture.md)
