---
title: Suportabilidade - Azure dedicado por HSM | Documentos da Microsoft
description: Opções de suporte e áreas de responsabilidade de HSM dedicados do Azure em cenários diferentes
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/18/2019
ms.author: barclayn
ms.openlocfilehash: 8e4bbe3a5e2de8ba919cf0b641ee4e6776c5e8d4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225557"
---
# <a name="azure-dedicated-hsm-supportability"></a>Capacidade de suporte do Azure de HSM dedicados

O serviço do Azure dedicado HSM fornece um dispositivo físico para utilização exclusiva de cliente completa administrativa responsabilidade de controle e gerenciamento. Se o dispositivo disponibilizado um [modelos de HSM de 7 Gemalto SafeNet Luna A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft terá sem acesso administrativo aprovisionado uma vez por um cliente, além de anexo de porta serial física, como uma função de monitorização.  Sem acesso, a Microsoft não pode ter nenhum software contínuo nível manutenção ou de sistema administração responsabilidades. Como resultado, os clientes são responsáveis por atividades operacionais típicas.
Os clientes são totalmente responsáveis por aplicativos que usam os HSMs e deverá trabalhar com Gemalto para suporte ou de assistência de consultoria com base em. Devido à extensão da propriedade de cliente de higiene de troca operacional, não é possível que a Microsoft, ofereceram qualquer tipo de garantia de disponibilidade elevada para este serviço. É responsabilidade do cliente para garantir que seus aplicativos estão corretamente configuradas para alcançar a elevada disponibilidade. A Microsoft irá monitorizar e manter a conetividade de rede e estado de funcionamento do dispositivo.

## <a name="getting-support"></a>Obter suporte

Suporte ao cliente para o HSM dedicada é uma iniciativa conjunta entre a Microsoft e da Gemalto. Quaisquer problemas de hardware ou problemas de caminho de rede serão resolvidos pela Microsoft e algo a ver com o HSM real, como a configuração, o software, o desenvolvimento de firmware e do aplicativo, da Gemalto será resolvido. Este modelo de suporte garante a rota mais rápida para o suporte mais eficiente. Em caso de dúvida com um problema específico, faça um pedido de suporte com a Microsoft e garantiremos que será direcionado adequadamente. A Microsoft irá permanecer envolvido em todos os cenários de suporte e se esforçar para a melhor experiência de suporte para os nossos clientes.

## <a name="gemalto-support"></a>Suporte da Gemalto

Clientes que utilizam o serviço de HSM dedicados se qualificar para o suporte da Gemalto de acordo com suas além de plano de suporte. Isso requer apenas um processo de registo com o portal de suporte da Gemalto. Serão fornecidas um ID de cliente e instruções para isso como parte do compromisso inicial com a Microsoft para obter acesso ao serviço de HSM dedicados. O mecanismo para obter suporte a partir da Gemalto é por meio de seus [portal de suporte do cliente](https://supportportal.gemalto.com/csm/).
Um ponto importante de nota é que a Gemalto fornecerá todo o software e documentação necessária para utilizar o HSM (por exemplo, software de acesso de cliente e SDKs) através de download no portal de suporte do cliente.

### <a name="software-components"></a>Componentes de software

Vários componentes de software são utilizados na configuração de dispositivos HSM:

* Software de cliente
* SDK
* Ferramentas

### <a name="guidance"></a>Orientação

Gemalto torna a orientação de administração e configuração disponível através da [portal de suporte do cliente](https://supportportal.gemalto.com/csm/). Depois de iniciar sessão com um ID de cliente válido, esses documentos estão disponíveis para download. Gemalto também fornece uma série de guias de integração para ajudar os clientes com diferentes cenários e integrações de software. Para obter mais informações, consulte a [Gemalto o site de parceiro da Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Suporte

Qualquer problema de nível de software ou uma pergunta em relação ao utilizar os HSMs como parte integrante do serviço de HSM dedicados, devem ser endereçadas a Gemalto suporte diretamente. Todos os componentes de software listados acima e qualquer configuração HSM personalizada que é pós-aprovisionamento, serão resolvidos pelo Gemalto. Para obter mais informações, consulte a [portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Serviços de consultoria

Para obter ajuda no design, desenvolvimento e implantação de aplicativos personalizados que utilizam o HSM, contacte o seu representante de conta de Gemalto.

## <a name="microsoft-support"></a>Suporte da Microsoft

Microsoft garantirá a dispositivos físicos de HSM são acessível e num estado operacional para utilização exclusiva de um único cliente de rede. Os clientes são responsáveis pela configuração, administração e gestão do dispositivo. Responsabilidades da Microsoft incluem:

* Tornar-se de que o dispositivo tem energia e refrigeração
* Manter o estado operacional do HSM (por exemplo, cenários de garantia de reparação/assistência)
* O dispositivo está acessível através da rede.

Problemas como a seguir devem ser informados para a Microsoft:

* Falhas de componentes
* Falha do dispositivo completo
* Problemas de acesso de rede
* Problemas de aprovisionamento e desaprovisionamento.

A Microsoft tem acesso de porta serial física para o dispositivo através de uma função de monitorização (ou seja, função de não administrador) que permite a telemetria de estado de funcionamento básico.  Isso permitirá que a Microsoft fornecer notificação proativa de problemas para o cliente, a menos que o cliente escolhe para restringir esta permissão. 

### <a name="provisioning-and-decommissioning"></a>Aprovisionamento e a desativação

Depois de um cliente possui um registo aprovado para o serviço de HSM dedicados, poderão criar recursos HSM (atualmente através do PowerShell ou a interface de linha de comandos e não o portal do Azure). O recurso executa um processo de alocação que mapeia um dispositivo físico numa determinada região, para predefinidas de rede virtual um cliente (VNet). Depois de visíveis numa VNet, o cliente pode aceder ao dispositivo e configurá-lo ainda mais o acordo com os requisitos. Os clientes aceder aos seus usando software de cliente da Gemalto e ferramentas de HSMs dedicados. O processo de criação de recursos é suportado pela Microsoft. Configuração personalizada processar e posteriores são suportados pelo Gemalto. (consulte a Gemalto suportar acima). Quando um cliente tiver concluído a utilizar um HSM, tem de ser reposto (ou zeroized) para garantir que nenhuma persistência de dados. O processo de repor o dispositivo remove todos os dados e de configuração personalizada. Microsoft desaloca o dispositivo e a devolve para o conjunto num estado "pura". Isso significa que, quando o dispositivo é devolvido para o conjunto que não existe nenhum provas de atividade do cliente anterior. 

### <a name="hardware-issues"></a>Problemas de hardware

O dispositivo HSM tem de alimentação redundantes e substituíveis e unidades de ventoinha.  No entanto, a remoção de unidade de ventoinha ainda causará um evento de adulteração. Quando ocorre uma falha de componente, a Microsoft irá utilizar o processo mais adequado para resolver o problema de nível de componente de uma forma que faz com que uma interrupção mínima e o risco mais baixo para nossa disponibilidade de serviço aos clientes.
Qualquer falha mais grave do dispositivo irá resultar em que o dispositivo a ser substituído por uma raiz do conjunto gratuito. O cliente inclui simplesmente do novo dispositivo no par de HA existente para o mesmo para sincronizar e voltar ao estado operacional completa. O dispositivo com falha terão os seus dados, tendo de dispositivos removido e shredded no site no Centro de dados. Apenas os chassis serão retornadas a Gemalto para reciclagem.


### <a name="networking-issues"></a>Problemas de rede

Se os clientes sofrer problemas de acesso de rede para o dispositivo HSM, deverá contactar o suporte da Microsoft. É um teste simples para acesso de rede utilizar o SSH para ligar ao dispositivo HSM. Se isto falhar, contacte o suporte da Microsoft.

## <a name="service-level-expectations-for-support"></a>Expetativas de nível de serviço para o suporte

Para níveis de serviço de suporte Microsoft, consulte a [plano de suporte do Azure](https://azure.microsoft.com/support/plans/).
Para níveis de serviço de suporte da Gemalto, consulte a [Essentials de suportar a Gemalto](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passos Seguintes

Recomenda-se que os conceitos-chave, como elevada disponibilidade e segurança são bem compreendidos antes de aprovisionamento de dispositivos e de design do aplicativo ou de implementação.

* [Arquitetura de implantação](deployment-architecture.md)
* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Monitorização](monitoring.md)