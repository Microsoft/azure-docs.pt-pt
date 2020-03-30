---
title: O que é OPC Twin - Azure Microsoft Docs
description: Este artigo fornece uma visão geral da OPC Twin. A OPC Twin fornece descoberta, registo e controlo remoto de dispositivos industriais através de APIs REST.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826229"
---
# <a name="what-is-opc-twin"></a>O que é OPC Twin?

A OPC Twin é constituída por microserviços que utilizam o Azure IoT Edge e o IoT Hub para ligar a nuvem e a rede de fábrica. A OPC Twin fornece descoberta, registo e controlo remoto de dispositivos industriais através de APIs REST. OPc Twin não requer um SDK de Arquitetura Unificada OPC (OPC UA), é agnóstico de linguagem de programação, e pode ser incluído num fluxo de trabalho sem servidores. Este artigo descreve vários casos de utilização da OPC Twin.

## <a name="discovery-and-control"></a>Descoberta e controlo
Pode utilizar o OPC Twin como simples para descoberta e registo.

### <a name="simple-discovery-and-registration"></a>Simples descoberta e registo
A OPC Twin permite que os operadores de fábrica scaneem a rede de fábrica, para que os servidores da OPC UA possam ser descobertos e registados. Como alternativa, os operadores de fábrica também podem registar manualmente dispositivos OPC UA usando um URL de descoberta conhecido. Por exemplo, para ligar a todos os dispositivos OPC UA depois de ter sido instalado o portal IoT Edge com um módulo OPC Twin no chão da fábrica, o operador de fábrica pode acionar remotamente uma varredura da rede e ver visualmente todos os servidores DaUA OPC. 

### <a name="simple-control"></a>Controlo simples
A OPC Twin permite que os operadores de fábrica reajam aos acontecimentos e reconfigurem as suas máquinas de chão de fábrica a partir da nuvem, automaticamente ou manualmente, em voo. O PcP Twin fornece APIs REST para invocar serviços no servidor OPC UA, navegar no seu espaço de endereço, bem como para ler/escrever variáveis e executar métodos. Por exemplo, uma caldeira utiliza kPI de temperatura para controlar a linha de produção. O sensor de temperatura publica a alteração dos dados utilizando o OPC Publisher. O operador da fábrica recebe o alerta de que a temperatura atingiu o limiar. A linha de produção arrefece automaticamente através da OPC Twin. O operador da fábrica é notificado do arrefecimento.

## <a name="authentication"></a>Autenticação
Pode utilizar o OPC Twin como simples para autenticação e para uma experiência simples de desenvolvimento.

### <a name="simple-authentication"></a>Autenticação simples 
A OPC Twin utiliza autenticação e auditoria baseadas em Azure Ative (AAD) de ponta a ponta. Por exemplo, a OPC Twin permite que a aplicação seja construída em cima da OPC Twin para determinar o que um operador realizou numa máquina. Do lado da máquina, é através da auditoria da OPC UA. Do lado da nuvem, é através do armazenamento de um registo imutável de auditoria de clientes e autenticação AAD na API REST.

### <a name="simple-developer-experience"></a>Experiência simples de desenvolvedor 
O PcPc Twin pode ser usado com aplicações escritas em qualquer linguagem de programação através de APIs REST. À medida que os desenvolvedores integram um cliente da OPC UA numa solução, o conhecimento do OPC UA SDK não é necessário. OPc Twin pode integrar-se perfeitamente em arquiteturas apátridas e sem servidores. Por exemplo, um desenvolvedor de web de pilha completa que desenvolve uma aplicação para um painel de alarme e evento pode escrever a lógica para responder a eventos em JavaScript ou TypeScript usando OPC Twin sem o conhecimento de C, C#, ou a implementação completa da pilha de UA OPC. 

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre o OPC Twin e os seus usos, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [O que é o Cofre OPC](overview-opc-vault.md)
