---
title: Suportabilidade - Azure dedicado por HSM | Documentos da Microsoft
description: Opções de suporte e áreas de responsabilidade de HSM dedicados do Azure em cenários diferentes
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881019"
---
# <a name="azure-dedicated-hsm-supportability"></a>Capacidade de suporte do Azure de HSM dedicados

O serviço do Azure dedicado HSM fornece um dispositivo físico para utilização exclusiva de cliente completa administrativa responsabilidade de controle e gerenciamento. Se o dispositivo disponibilizado um [modelos de HSM de 7 Gemalto SafeNet Luna A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft terá sem acesso administrativo aprovisionado uma vez por um cliente, além de anexo de porta serial física, como uma função de monitorização.  Sem acesso, a Microsoft não pode ter nenhum software contínuo nível manutenção ou de sistema administração responsabilidades. Como resultado, os clientes são responsáveis por atividades operacionais típicas.
Os clientes são totalmente responsáveis por aplicativos que usam os HSMs e deverá trabalhar com Gemalto para suporte ou de assistência de consultoria com base em. Devido à extensão da propriedade de cliente de higiene de troca operacional, não é possível que a Microsoft, ofereceram qualquer tipo de garantia de disponibilidade elevada para este serviço. É responsabilidade do cliente para garantir que seus aplicativos estão corretamente configuradas para alcançar a elevada disponibilidade. A Microsoft irá monitorizar e manter a conetividade de rede e estado de funcionamento do dispositivo.

## <a name="getting-support"></a>Obter suporte

O atendimento ao cliente para HSM dedicado é um esforço conjunto entre a Microsoft e o Gemalto. Quaisquer problemas de hardware ou de caminho de rede serão abordados pela Microsoft, e qualquer coisa a fazer com o HSM real, como configuração, software, firmware e desenvolvimento de aplicativos, será abordada pelo Gemalto. Esse modelo de suporte garante a rota mais rápida para o suporte mais eficaz. Se estiver em dúvida com um problema específico, gere uma solicitação de suporte com a Microsoft e garantiremos que você será direcionado adequadamente. A Microsoft se manterá envolvida em todos os cenários de suporte e buscará a melhor experiência de suporte para nossos clientes.

## <a name="gemalto-support"></a>Suporte da Gemalto

Os clientes que usam o serviço HSM dedicado se qualificam para o suporte do Gemalto de acordo com seu plano de suporte Plus. Isso apenas requer um processo de registro usando o portal de suporte do Gemalto. Uma ID de cliente e instruções serão fornecidas para isso como parte do envolvimento inicial com a Microsoft para obter acesso ao serviço HSM dedicado. O mecanismo para obter suporte a partir da Gemalto é por meio de seus [portal de suporte do cliente](https://supportportal.gemalto.com/csm/).
Um ponto importante de observação é que o Gemalto fornecerá todos os softwares e a documentação necessários para usar o HSM (por exemplo, software de acesso para cliente e SDKs) por meio do download no portal de atendimento ao cliente.

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

A Microsoft garantirá que os dispositivos HSM físicos estejam acessíveis à rede e em um estado operacional para o uso exclusivo de um único cliente. Os clientes são responsáveis pela configuração, administração e gerenciamento do dispositivo. Responsabilidades da Microsoft incluem:

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

O dispositivo HSM tem de alimentação redundantes e substituíveis e unidades de ventoinha.  No entanto, a remoção da unidade do ventilador ainda causará um evento de violação. Quando ocorre uma falha de componente, a Microsoft irá utilizar o processo mais adequado para resolver o problema de nível de componente de uma forma que faz com que uma interrupção mínima e o risco mais baixo para nossa disponibilidade de serviço aos clientes.
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

