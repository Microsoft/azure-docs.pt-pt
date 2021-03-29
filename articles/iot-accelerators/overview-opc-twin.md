---
title: O que é OPC Twin - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da OPC Twin. O OPC Twin fornece descoberta, registo e controlo remoto de dispositivos industriais através de APIs REST.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646951"
---
# <a name="what-is-opc-twin"></a>O que é OPC Twin?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

OPC Twin é composto por microserviços que utilizam O Azure IoT Edge e IoT Hub para ligar a nuvem e a rede de fábrica. O OPC Twin fornece descoberta, registo e controlo remoto de dispositivos industriais através de APIs REST. OPC Twin não requer um SDK de Arquitetura Unificada OPC (OPC UA), é agnóstico linguístico de programação, e pode ser incluído num fluxo de trabalho sem servidor. Este artigo descreve vários casos de utilização de OPC Twin.

## <a name="discovery-and-control"></a>Descoberta e controlo
Você pode usar OPC Twin para simples para descoberta e registo.

### <a name="simple-discovery-and-registration"></a>Simples descoberta e registo
A OPC Twin permite que os operadores de fábrica digitalizem a rede de fábrica, para que os servidores OPC UA possam ser descobertos e registados. Como alternativa, os operadores de fábrica também podem registar manualmente dispositivos OPC UA usando um URL de descoberta conhecido. Por exemplo, para ligar a todos os dispositivos OPC UA depois de ter sido instalado o gateway IoT Edge com um módulo OPC Twin no chão da fábrica, o operador da fábrica pode ativar remotamente uma varredura da rede e ver visualmente todos os servidores OPC UA. 

### <a name="simple-control"></a>Controlo simples
A OPC Twin permite que os operadores de fábrica reajam a eventos e reconfigurem as suas máquinas de chão de fábrica a partir da nuvem, tanto automaticamente como manualmente em voo. O OPC Twin fornece APIs REST para invocar serviços no servidor OPC UA, navegar no seu espaço de endereço, bem como para ler/escrever variáveis e executar métodos. Por exemplo, uma caldeira utiliza KPI de temperatura para controlar a linha de produção. O sensor de temperatura publica a alteração de dados utilizando o Editor OPC. O operador da fábrica recebe o alerta de que a temperatura atingiu o limiar. A linha de produção arrefece automaticamente através da OPC Twin. O operador da fábrica é notificado do arrefecimento.

## <a name="authentication"></a>Autenticação
Pode utilizar o OPC Twin para uma simples autenticação e para uma simples experiência de desenvolvimento.

### <a name="simple-authentication"></a>Autenticação simples 
A OPC Twin utiliza a autenticação e auditoria baseadas no Azure Ative Directory (AAD) de ponta a ponta. Por exemplo, o OPC Twin permite que a aplicação seja construída em cima da OPC Twin para determinar o que um operador realizou numa máquina. Do lado da máquina, é através da auditoria da OPC UA. No lado da nuvem, é através do armazenamento de um registo de auditoria de cliente imutável e autenticação AAD na API REST.

### <a name="simple-developer-experience"></a>Experiência simples de desenvolvedor 
OPC Twin pode ser utilizado com aplicações escritas em qualquer linguagem de programação através de REST APIs. Como os desenvolvedores integram um cliente OPC UA numa solução, o conhecimento do OPC UA SDK não é necessário. OPC Twin pode integrar-se perfeitamente em arquiteturas apátridas e sem servidor. Por exemplo, um desenvolvedor web de stack completo que desenvolve uma aplicação para um painel de alarme e evento pode escrever a lógica para responder a eventos em JavaScript ou TypeScript usando OPC Twin sem o conhecimento de C, C#, ou a implementação completa da pilha de UA OPC. 

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre a OPC Twin e os seus usos, aqui está o próximo passo sugerido:

[O que é o Cofre OPC](overview-opc-vault.md)