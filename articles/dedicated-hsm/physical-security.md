---
title: Segurança física hSM - Azure Dedicado HSM [ Microsoft Docs
description: Informação sobre a segurança física dos dispositivos HSM dedicados do Azure nos centros de dados
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70881020"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Segurança física dedicada ao HSM azure

O Azure Dedicado HSM ajuda-o a cumprir os requisitos avançados de segurança para o armazenamento de chaves. É gerido seguindo práticas de segurança rigorosas ao longo de todo o seu ciclo de vida para atender às necessidades dos clientes.

## <a name="security-through-procurement"></a>Segurança através de aquisições

A Microsoft segue um processo de aquisição seguro. Gerimos a cadeia de custódia e garantimos que o dispositivo específico encomendado e enviado é o dispositivo que chega aos nossos centros de dados. Os dispositivos estão em plástico de eventos de adulteração. São armazenados numa área de armazenamento segura até serem encomendados na galeria de dados do centro de dados.  As prateleiras que contêm os dispositivos HSM são consideradas de elevado impacto empresarial (HBI). Os dispositivos estão bloqueados e sob videovigilância a todo o momento, na frente e atrás.

## <a name="security-through-deployment"></a>Segurança através da implantação

Os HSMs são instalados em prateleiras juntamente com componentes de rede associados. Uma vez instalados, devem ser configurados antes de serem disponibilizados como parte do Serviço HSM dedicado ao Azure. Esta atividade de configuração é realizada por funcionários da Microsoft que foram submetidos a uma verificação de antecedentes. A administração "Just In Time" (JIT) é utilizada para limitar o acesso apenas aos trabalhadores certos e apenas pelo tempo que é necessário aceder. Os procedimentos e sistemas utilizados também asseguram que toda a atividade relacionada com os dispositivos HSM está registada.

## <a name="security-in-operations"></a>Segurança nas operações

Os HSMs são aparelhos de hardware (sendo o HSM atual um cartão PCI dentro do aparelho) pelo que é possível que surjam problemas de nível de componente. As questões potenciais incluem, mas não se limitam a falhas de alimentação de ventiladores e de alimentação. Este tipo de evento exigirá atividades de manutenção ou rutura/correção para substituir quaisquer componentes permutáveis.

### <a name="component-replacement"></a>Substituição de componentes

Depois de um dispositivo ser provisionado e sob gestão do cliente, a fonte de alimentação permutável a quente é o único componente que seria substituído. Este componente está fora do limite de segurança e não causa um incidente de adulteração. Um sistema de bilhética é usado para autorizar um engenheiro da Microsoft a aceder à parte traseira da cremalheira HBI. Quando o bilhete é processado é emitida uma chave física temporária. Esta chave dá ao engenheiro acesso ao dispositivo e permite-lhes trocar o componente afetado. Qualquer outro acesso (isto é, adulteração de eventos) seria feito quando um dispositivo não é atribuído a um cliente, minimizando assim o risco de segurança e disponibilidade.  

### <a name="device-replacement"></a>Substituição do dispositivo

Em caso de falha total do dispositivo, segue-se um processo semelhante ao utilizado durante a falha do componente. Se um cliente não conseguir zeroizar o dispositivo, ou se o dispositivo estiver num estado desconhecido, os dispositivos de suporte de dados serão removidos e colocados num contentor de destruição no cesto. Os dispositivos colocados no caixote do lixo serão destruídos de forma controlada e segura. Nenhum dispositivo de suporte de dados de um rack HBI deixará um datacenter da Microsoft.

### <a name="other-rack-access-activities"></a>Outras atividades de acesso ao rack

Se um engenheiro da Microsoft tiver acesso à cremalheira utilizada pelos dispositivos HSM (por exemplo, manutenção de dispositivos de rede), serão utilizados procedimentos de segurança padrão para aceder à cremalheira segura HBI. Todos os acessos estarão sob videovigilância. Os dispositivos HSM são validados para [o Nível 3 140-2 FIPS](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) para que qualquer acesso não autorizado aos Dispositivos HSM seja sinalizado ao cliente e os dados serão zeroizados.

## <a name="logical-level-security-considerations"></a>Considerações lógicas de segurança a nível

Os HSMs são provisionados para uma rede virtual criada pelo cliente. Este é o espaço privado iUP Address de um cliente.  Esta configuração proporciona um valioso isolamento lógico ao nível da rede e garante o acesso apenas pelo cliente. Isto implica que todos os controlos de segurança de nível lógico são da responsabilidade do cliente.

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que todos os conceitos-chave do serviço, tais como alta disponibilidade e segurança e capacidade de apoio, por exemplo, sejam bem compreendidos antes do fornecimento, conceção ou implementação de dispositivos.

* [Alta Disponibilidade](high-availability.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
* [Arquitetura de implantação](deployment-architecture.md)