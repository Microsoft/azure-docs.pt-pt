---
title: Supportability - Azure Dedicado HSM [ Microsoft Docs
description: Opções de apoio e áreas de responsabilidade para o Azure Dedicado HSM em diferentes cenários
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881019"
---
# <a name="azure-dedicated-hsm-supportability"></a>Suporte dedicado ao HSM azure

O Serviço HSM Dedicado Azure fornece um dispositivo físico para uso exclusivo do cliente com total responsabilidade de controlo administrativo e gestão. O dispositivo disponibilizado é um [Modelo Gemalto SafeNet Luna 7 HSM A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft não terá acesso administrativo uma vez aprovisionado por um cliente, para além do acessório físico por porta em série como uma função de monitorização.  Sem acesso, a Microsoft não pode ter responsabilidades de manutenção de nível de software ou de administração do sistema em curso. Como resultado, os clientes são responsáveis por atividades operacionais típicas.
Os clientes são totalmente responsáveis por aplicações que utilizam os HSMs e devem trabalhar com a Gemalto para assistência de apoio ou consultoria. Devido à extensão da propriedade do cliente da higiene operacional, não é possível que a Microsoft ofereça qualquer tipo de garantia de alta disponibilidade para este serviço. É da responsabilidade do cliente garantir que as suas aplicações estão corretamente configuradas para alcançar uma elevada disponibilidade. A Microsoft monitorizará e manterá a conectividade da rede e saúde do dispositivo.

## <a name="getting-support"></a>Obter suporte

O apoio ao cliente para o HSM dedicado é um esforço conjunto entre a Microsoft e a Gemalto. Quaisquer problemas de hardware ou problemas de trajetória de rede serão abordados pela Microsoft, e qualquer coisa a ver com o HSM real, como configuração, software, firmware e desenvolvimento de aplicações, será abordado pela Gemalto. Este modelo de suporte garante a rota mais rápida para o suporte mais eficaz. Em caso de dúvida sobre um determinado problema, levante um pedido de suporte com a Microsoft e garantiremos que é dirigido adequadamente. A Microsoft permanecerá envolvida em todos os cenários de suporte e esforçar-se-á pela melhor experiência de suporte para os nossos clientes.

## <a name="gemalto-support"></a>Apoio gemalto

Os clientes que utilizam o serviço HSM dedicado qualificam-se para apoio da Gemalto de acordo com o seu Plano de Apoio Plus. Isto apenas requer um processo de registo utilizando o portal de suporte Gemalto. Será fornecido um ID do cliente e instruções para isso como parte do compromisso inicial com a Microsoft para ter acesso ao serviço HSM dedicado. O mecanismo para obter apoio da Gemalto é através do seu portal de [apoio ao cliente.](https://supportportal.gemalto.com/csm/)
Um ponto chave de nota é que a Gemalto fornecerá todo o software e documentação necessário seletiva para usar o HSM (por exemplo, software de acesso ao cliente e SDKs) através do download no portal de suporte ao cliente.

### <a name="software-components"></a>Componentes de software

Vários componentes de software são utilizados na configuração de dispositivos HSM:

* Software de cliente
* SDK
* Ferramentas

### <a name="guidance"></a>Orientação

Gemalto disponibiliza orientação de administração e configuração através do portal de apoio ao [cliente.](https://supportportal.gemalto.com/csm/) Uma vez assinados usando um ID de cliente válido, estes documentos estão disponíveis para download. A Gemalto também fornece uma série de guias de integração para ajudar os clientes com diferentes cenários e integrações de software. Para mais informações, consulte o site do [parceiro Gemalto para a Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Suporte

Qualquer problema ou questão de nível de software em relação à utilização dos HSMs como parte do serviço HSM dedicado deve ser dirigida diretamente ao suporte da Gemalto. Todos os componentes do software listados acima, e qualquer configuração HSM personalizada que seja pós-provisionamento, será abordada pela Gemalto. Para mais informações, consulte o portal de apoio ao [cliente gemalto.](https://supportportal.gemalto.com/csm/)

### <a name="consulting-services"></a>Serviços de consultadoria

Para qualquer assistência na conceção, desenvolvimento e implementação de aplicações personalizadas que utilizem o HSM, contacte o seu representante da conta Gemalto.

## <a name="microsoft-support"></a>Suporte da Microsoft

A Microsoft garantirá que os dispositivos HSM físicos são acessíveis à rede e em estado operacional para uso exclusivo de um único cliente. Os clientes são responsáveis pela configuração, administração e gestão do dispositivo. As responsabilidades da Microsoft incluem:

* Certificando-se de que o dispositivo tem energia e arrefecimento
* Manter um estado operacional do HSM (por exemplo, cenários de rutura/correção)
* O dispositivo é acessível em toda a rede.

Questões como as seguintes devem ser comunicadas à Microsoft:

* Falhas de componentes
* Falha total do dispositivo
* Problemas de acesso à rede
* Problemas de prestação e desprovisionamento.

A Microsoft tem acesso físico à porta em série do dispositivo através de uma função de monitorização (isto é, não de função administrativa) que permite a telemetria de saúde básica.  Isto permitirá à Microsoft fornecer notificação proativa de problemas ao cliente, a menos que o cliente opte por restringir esta permissão. 

### <a name="provisioning-and-decommissioning"></a>Provisionamento e desmantelamento

Depois de um cliente ter um registo aprovado para o serviço HSM dedicado, eles poderão criar recursos HSM (atualmente através da PowerShell ou interface de linha de comando e não do portal Azure). O recurso passa por um processo de atribuição que mapeia um dispositivo físico numa região especificada, para a rede virtual pré-definida de um cliente (VNet). Uma vez visível num VNet, o cliente pode aceder ao dispositivo e configurá-lo ainda mais de acordo com os requisitos. Os clientes acedem aos seus HSMs dedicados utilizando software e ferramentas de cliente Gemalto. O processo de criação de recursos é suportado pela Microsoft. O processo de configuração personalizada e além são suportados pela Gemalto. (ver suporte gemalto acima). Quando um cliente terminar de utilizar um HSM, este deve ser reiniciado (ou zeroized) para garantir que não haja persistência de dados. O processo de reposição do dispositivo remove toda a configuração e dados personalizados. A Microsoft desaloca o dispositivo e devolve-o à piscina em estado imaculado. Isto significa que quando o dispositivo é devolvido à piscina não há indícios de atividade do cliente anterior. 

### <a name="hardware-issues"></a>Problemas de hardware

O dispositivo HSM dispõe de fontes de alimentação redundantes e substituíveis e unidades de ventilador.  No entanto, a remoção da unidade de ventilação ainda causará um evento de adulteração. Quando ocorrer uma falha de componente, a Microsoft utilizará o processo mais adequado para abordar o problema do nível do componente de uma forma que cause uma interrupção mínima e menor risco para a disponibilidade do serviço dos nossos clientes.
Qualquer falha mais grave do dispositivo resultará na sua substituído por um novo da piscina gratuita. O cliente simplesmente inclui o novo dispositivo no par HA existente para que ele sincronize e regresse ao estado operacional completo. O dispositivo falhado terá os seus dispositivos de suporte de dados removidos e triturados no local no centro de dados. Apenas o chassis será devolvido a Gemalto para reciclagem.


### <a name="networking-issues"></a>Questões de networking

Se os clientes experimentarem problemas de acesso em rede ao dispositivo HSM, devem contactar o suporte da Microsoft. Um teste simples para o acesso em rede é utilizar o SSH para se ligar ao dispositivo HSM. Se isto falhar, contacte o suporte da Microsoft.

## <a name="service-level-expectations-for-support"></a>Expectativas de apoio ao nível do serviço

Para os níveis de serviço de suporte da Microsoft, consulte o plano de [suporte do Azure](https://azure.microsoft.com/support/plans/).
Para os níveis de serviço de suporte gemalto, consulte o [Gemalto Support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que conceitos-chave como alta disponibilidade e segurança sejam bem compreendidos antes do fornecimento de dispositivos e da conceção ou implementação de aplicações.

* [Arquitetura de Implantação](deployment-architecture.md)
* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)

