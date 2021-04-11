---
title: Suporte - Azure Dedicado HSM | Microsoft Docs
description: Opções de apoio e áreas de responsabilidade para a Azure Dedicado HSM em diferentes cenários
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606934"
---
# <a name="azure-dedicated-hsm-supportability"></a>Suporte dedicado ao HSM Azure

O Azure Dedicad HSM Service fornece um dispositivo físico para uso exclusivo do cliente com total responsabilidade administrativa e de gestão. O dispositivo disponibilizado é um [modelo Thales Luna 7 HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). A Microsoft não terá acesso administrativo uma vez que seja adesertado por um cliente, para além do acessório de porta em série física como função de monitorização.  Sem acesso, a Microsoft não pode ter nenhuma manutenção de nível de software em curso ou responsabilidades de administração do sistema. Como resultado, os clientes são responsáveis por atividades operacionais típicas.
Os clientes são totalmente responsáveis pelas aplicações que utilizam os HSMs e devem trabalhar com o Thales para assistência baseada em suporte ou consultoria. Devido à extensão da propriedade do cliente de higiene operacional, não é possível para a Microsoft oferecer qualquer tipo de garantia de alta disponibilidade para este serviço. É da responsabilidade do cliente garantir que as suas aplicações estão corretamente configuradas para alcançar uma elevada disponibilidade. A Microsoft monitorizará e manterá a saúde do dispositivo e a conectividade da rede.

## <a name="getting-support"></a>Obter suporte

O apoio ao cliente para o HSM dedicado é um esforço conjunto entre a Microsoft e o Thales. Quaisquer problemas de hardware ou de trajetória de rede serão abordados pela Microsoft, e qualquer coisa a ver com o HSM real, como configuração, software, firmware e desenvolvimento de aplicações, será abordado pelo Thales. Este modelo de suporte garante a rota mais rápida para o suporte mais eficaz. Em caso de dúvida com um determinado problema, levante um pedido de apoio com a Microsoft e asseguraremos que o mesmo é dirigido adequadamente. A Microsoft permanecerá envolvida em todos os cenários de suporte e esforçar-se-á pela melhor experiência de suporte para os nossos clientes.

## <a name="thales-support"></a>Apoio ao Thales

Os clientes que utilizam o serviço Dedicado HSM qualificam-se para o apoio da Thales de acordo com o seu Plano de Suporte Plus. Isto apenas requer um processo de registo usando o portal de suporte Thales. Será fornecido um ID do Cliente e instruções para que isso seja feito como parte do compromisso inicial com a Microsoft para ter acesso ao serviço dedicado HSM. O mecanismo para obter apoio do Thales é através do seu portal de [apoio ao cliente.](https://supportportal.thalesgroup.com/csm)
Um ponto chave de nota é que o Thales fornecerá todo o software e documentação necessário para usar o HSM (por exemplo, software de acesso ao cliente e SDKs) através do download no portal de apoio ao cliente.

### <a name="software-components"></a>Componentes de software

Vários componentes de software são utilizados na configuração de dispositivos HSM:

* Software de cliente
* SDK
* Ferramentas

### <a name="guidance"></a>Orientação

O Thales disponibiliza orientação de administração e configuração através do [portal de apoio ao cliente Thales.](https://supportportal.thalesgroup.com/csm) Uma vez assinados usando um ID de cliente válido, estes documentos estão disponíveis para download. O Thales também fornece uma série de guias de integração para ajudar os clientes com diferentes cenários e integrações de software. Para mais informações, consulte o [site de parceiros Thales para a Microsoft.](https://cpl.thalesgroup.com/partners/overview)

### <a name="support"></a>Suporte

Qualquer problema ou questão de nível de software em relação à utilização dos HSMs como parte do serviço dedicado HSM deve ser endereçado diretamente ao suporte do Thales. Todos os componentes de software listados acima, e qualquer configuração HSM personalizada que seja pós-provisão, serão abordados pelo Thales. Para mais informações, consulte o [portal de apoio ao cliente thales.](https://supportportal.thalesgroup.com/csm)

### <a name="consulting-services"></a>Serviços de consultadoria

Para qualquer assistência na conceção, desenvolvimento e implementação de aplicações personalizadas que utilizem o HSM, contacte o seu representante da conta Thales.

## <a name="microsoft-support"></a>Suporte da Microsoft

A Microsoft garantirá que os dispositivos físicos de HSM são acessíveis à rede e em estado operacional para uso exclusivo de um único cliente. Os clientes são responsáveis pela configuração, administração e gestão do dispositivo. As responsabilidades da Microsoft incluem:

* Certificando-se de que o dispositivo tem energia e arrefecimento
* Manutenção de um estado operacional do HSM (por exemplo, cenários de rutura/correção)
* O dispositivo é acessível por toda a rede.

Questões como as seguintes devem ser comunicadas à Microsoft:

* Falhas de componentes
* Falha total do dispositivo
* Problemas de acesso à rede
* Problemas de provisionamento e desprovisionamento.

A Microsoft tem acesso físico à porta em série ao dispositivo através de uma função de monitorização (que é uma função não administrativa) que permite telemetria básica de saúde.  Isto permitirá à Microsoft fornecer uma notificação proativa de problemas ao cliente, a menos que o cliente opte por restringir esta permissão. 

### <a name="provisioning-and-decommissioning"></a>Provisionamento e desmantelamento

Depois de um cliente ter um registo aprovado para o serviço dedicado HSM, eles serão capazes de criar recursos HSM (atualmente através da Interface PowerShell ou da linha de comando e não o portal Azure). O recurso passa por um processo de atribuição que mapeia um dispositivo físico numa região especificada, para a rede virtual pré-definida de um cliente (VNet). Uma vez visível num VNet, o cliente pode aceder ao dispositivo e configurá-lo ainda mais de acordo com os requisitos. Os clientes acedem aos seus HSMs dedicados utilizando software e ferramentas de cliente Thales. O processo de criação de recursos é suportado pela Microsoft. O processo de configuração personalizada e além são suportados pelo Thales. (ver suporte thales acima). Quando um cliente tiver terminado de usar um HSM, deve ser reiniciado (ou zeroizado) para garantir que não persiste em dados. O processo de reposição do dispositivo remove todas as configurações e dados personalizados. A Microsoft negoceia o dispositivo e devolve-o à piscina num estado imaculado. Isto significa que quando o dispositivo é devolvido à piscina não há evidências de atividade prévia do cliente. 

### <a name="hardware-issues"></a>Problemas de hardware

O dispositivo HSM tem fontes de alimentação redundantes e substituíveis e unidades de ventilação.  No entanto, a remoção da unidade do ventilador continuará a causar um evento de adulteração. Quando ocorrer uma falha de componente, a Microsoft utilizará o processo mais adequado para resolver o problema do nível de componente de uma forma que cause uma interrupção mínima e menor risco para a disponibilidade do serviço dos nossos clientes.
Qualquer falha mais grave do dispositivo resultará na substituição do dispositivo por um novo dispositivo da piscina gratuita. O cliente simplesmente inclui o novo dispositivo no par HA existente para que ele sincronize e regresse ao estado operacional completo. O dispositivo falhado terá os seus dispositivos de rolamento de dados removidos e triturados no local do centro de dados. 

### <a name="networking-issues"></a>Questões de networking

Se os clientes experimentarem problemas de acesso em rede ao dispositivo HSM, devem contactar o suporte da Microsoft. Um teste simples para o acesso em rede é utilizar o SSH para ligar ao dispositivo HSM. Se isto falhar, contacte o suporte da Microsoft.

## <a name="service-level-expectations-for-support"></a>Expectativas de nível de serviço para suporte

Para os níveis de serviço de suporte da Microsoft, consulte o [plano de suporte Azure](https://azure.microsoft.com/support/plans/).
Para os níveis de serviço de suporte thales, consulte o [Thales Support Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que conceitos-chave como alta disponibilidade e segurança sejam bem compreendidos antes do fornecimento de dispositivos e design de aplicações ou implementação.

* [Arquitetura de Implantação](deployment-architecture.md)
* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Rede](networking.md)

