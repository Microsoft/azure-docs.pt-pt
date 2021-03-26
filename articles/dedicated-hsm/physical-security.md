---
title: Segurança física HSM - Azure Dedicado HSM | Microsoft Docs
description: Informação sobre a segurança física dos dispositivos HSM dedicados a Azure nos centros de dados
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606968"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Segurança física dedicada a Azure HSM

O Azure Dedicado HSM ajuda-o a cumprir os requisitos avançados de segurança para o armazenamento de chaves. É gerido seguindo práticas de segurança rigorosas ao longo de todo o seu ciclo de vida para atender às necessidades dos clientes.

## <a name="security-through-procurement"></a>Segurança através de aquisições

A Microsoft segue um processo de aquisição seguro. Gerimos a cadeia de custódia e garantimos que o dispositivo específico encomendado e enviado é o dispositivo que chega aos nossos centros de dados. Os dispositivos estão em sacos e recipientes de plástico de eventos de adulteração serializados. São armazenados numa área de armazenamento segura até serem encomendados na galeria de dados do centro de dados.  Os racks que contêm os dispositivos HSM são considerados de alto impacto comercial (HBI). Os dispositivos estão sempre bloqueados e sob videovigilância à frente e atrás.

## <a name="security-through-deployment"></a>Segurança através da implantação

Os HSMs são instalados em racks juntamente com componentes de rede associados. Uma vez instalados, devem ser configurados antes de serem disponibilizados como parte do Serviço HSM Dedicado Azure. Esta atividade de configuração é realizada por funcionários da Microsoft que foram submetidos a uma verificação de antecedentes. A administração "Just In Time" (JIT) é usada para limitar o acesso apenas aos funcionários certos e apenas pelo tempo que o acesso é necessário. Os procedimentos e sistemas utilizados também asseguram que toda a atividade relacionada com os dispositivos HSM seja registada.

## <a name="security-in-operations"></a>Segurança em operações

Os HSMs são aparelhos de hardware (sendo o HSM real um cartão PCI dentro do aparelho) pelo que é possível que surjam problemas de nível de componente. As questões potenciais incluem, mas não se limitam a falhas de ventilação e de alimentação. Este tipo de evento exigirá atividades de manutenção ou rutura/correção para substituir quaisquer componentes permutáveis.

### <a name="component-replacement"></a>Substituição de componentes

Depois de um dispositivo ser aprovisionado e sob gestão do cliente, a fonte de alimentação a quente é o único componente que seria substituído. Este componente está fora da fronteira de segurança e não causa um evento de adulteração. Um sistema de bilhética é utilizado para autorizar um engenheiro da Microsoft a aceder à parte traseira do rack HBI. Quando o bilhete é processado, é emitida uma chave física temporária. Esta chave dá ao engenheiro acesso ao dispositivo e permite-lhes trocar o componente afetado. Qualquer outro acesso (isto é, causar um evento de adulteração) seria feito quando um dispositivo não é atribuído a um cliente, minimizando assim o risco de segurança e disponibilidade.  

### <a name="device-replacement"></a>Substituição do dispositivo

Em caso de falha total do dispositivo, é seguido um processo semelhante ao utilizado durante a falha do componente. Se um cliente não for capaz de zeroizar o dispositivo, ou o dispositivo estiver num estado desconhecido, os dispositivos de rolamento de dados serão removidos e colocados num contentor de destruição no cesto. Os dispositivos colocados no caixote do lixo serão destruídos de forma controlada e segura. Nenhum dispositivo de suporte de dados de um rack HBI deixará um datacenter da Microsoft.

### <a name="other-rack-access-activities"></a>Outras atividades de acesso ao rack

Se um engenheiro da Microsoft tiver de aceder à cremalheira utilizada por dispositivos HSM (por exemplo, manutenção de dispositivos de rede), serão utilizados procedimentos de segurança padrão para aceder ao rack seguro HBI. Todos os acessos estarão sob videovigilância. Os dispositivos HSM são validados ao [FIPS 140-2 Nível 3,](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) pelo que qualquer acesso não autorizado aos Dispositivos HSM será sinalizado para o cliente e os dados serão zeroizados.

## <a name="logical-level-security-considerations"></a>Considerações lógicas de segurança de nível

Os HSMs são a provisionados a uma rede virtual criada pelo cliente dentro do espaço privado ip address do cliente.  Esta configuração proporciona um valioso isolamento lógico ao nível da rede e garante o acesso apenas pelo cliente. Isto implica que todos os controlos de segurança de nível lógico são da responsabilidade do cliente.

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que todos os conceitos-chave do serviço, tais como alta disponibilidade e segurança e capacidade de suporte, por exemplo, sejam bem compreendidos antes do fornecimento do dispositivo, design de aplicações ou implementação.

* [Alta Disponibilidade](high-availability.md)
* [Rede](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
* [Arquitetura de implantação](deployment-architecture.md)
