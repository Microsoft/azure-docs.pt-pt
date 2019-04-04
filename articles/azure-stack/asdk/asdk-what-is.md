---
title: Uma introdução ao Azure Stack Development Kit (ASDK) | Documentos da Microsoft
description: Descreve o que é o ASDK e casos de utilização comuns para avaliar o Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 54eb2ff43a5f36999294b8d0c580bc425ab65b28
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629074"
---
# <a name="what-is-the-azure-stack-development-kit"></a>O que é o Kit de desenvolvimento do Azure Stack?
[Sistemas integrados do Microsoft Azure Stack](../azure-stack-poc.md) de intervalo de tamanho de 4 a 16 nós e, em conjunto, são suportados por um parceiro de hardware e a Microsoft. Utilize sistemas integrados do Azure Stack para ativar novos cenários para as cargas de trabalho de produção. Se for um operador do Azure Stack que gerencia a infra-estrutura de sistemas integrados e oferece serviços, consulte nosso [documentação de operador](https://docs.microsoft.com/azure/azure-stack).

O Azure Stack Development Kit (ASDK) é uma implementação de nó único do Azure Stack que pode transferir e utilizar **gratuitamente**. Todos os componentes ASDK estão instalados nas máquinas virtuais em execução num computador de servidor de anfitrião único que tem de cumprir ou exceder os [requisitos mínimos de hardware](asdk-deploy-considerations.md#hardware). O ASDK destina-se para fornecer um ambiente em que pode avaliar o Azure Stack e desenvolver aplicações modernas com APIs e de ferramentas consistentes com o Azure numa *que não seja de produção* ambiente. 

> [!IMPORTANT]
> O ASDK não se destina a ser utilizada ou suportado num ambiente de produção.

Como todos os componentes ASDK são implementados para um computador de anfitrião do kit de desenvolvimento único, existem limitados de recursos físicos disponíveis. Com implementações de ASDK, as VMs de infraestrutura do Azure Stack e o inquilino VMs coexistam no mesmo computador do servidor. Esta configuração não se destina a avaliação de dimensionamento ou desempenho.

O ASDK foi concebido para proporcionar uma experiência de cloud híbrida consistentes do Azure para:
- **Os administradores** (operadores do Azure Stack). O ASDK é um ótimo recurso para avaliar e saiba mais sobre os serviços disponíveis do Azure Stack.
- **Os desenvolvedores**. O ASDK pode ser usado para desenvolver híbrida ou aplicações modernas no local (ambientes de dev/test). Isso oferece a capacidade de repetição de experiência em desenvolvimento anterior, ou em conjunto com as implementações de produção do Azure Stack. 

Veja este pequeno vídeo para saber mais sobre o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Diferenças do Azure Stack ASDK e com vários nós
Implementações de ASDK de nó único diferem das implementações do Azure Stack com vários nós algumas diferenças importantes que deve estar atento.

|Descrição|ASDK|Com vários nó do Azure Stack|
|-----|-----|-----|
|**Dimensionamento**|Todos os componentes são instalados num computador de servidor de nó único.|Pode variar de tamanho de 4 a 16 nós.|
|**Resiliência**|Configuração de nó único não fornece elevada disponibilidade|[Elevada disponibilidade](../azure-stack-overview.md#providing-high-availability) capacidades são suportadas.|
|**Redes**|O anfitrião ASDK encaminha todo o tráfego de rede ASDK. Não existem não requisitos de comutador adicionais.|Mais complexos [infraestrutura de encaminhamento de rede](../azure-stack-network.md#network-infrastructure) em implementações de vários nós é necessário, incluindo Top-Of-Rack (TOR), controlador de gestão de placas base (BMC) e comutadores de limite (rede de centro de dados).|
|**Processo de patch e atualização**|Para mover para uma nova versão do ASDK, tem de voltar a implementar o ASDK no computador de anfitrião do kit de desenvolvimento.|[Aplicar o patch e atualizar](../azure-stack-updates.md) processo usado para atualizar a versão instalada do Azure Stack.|
|**Suporte**|Fórum de MSDN do Azure Stack. Suporte de atendimento ao cliente Microsoft e de suporte (CSS) está *não* disponíveis para ambientes de não produção.|[Fórum de MSDN do Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) e dar suporte a CSS completas.|
| | |

## <a name="learn-about-available-services"></a>Conheça os serviços disponíveis
Como um operador de pilha do Azure, precisa saber quais os serviços que pode disponibilizar aos seus utilizadores. O Azure Stack suporta um subconjunto de serviços do Azure e a lista de serviços com suporte continuará a evoluir ao longo do tempo.

### <a name="foundational-services"></a>Serviços básicos
Por predefinição, o Azure Stack inclui os seguintes "fundamentais serviços" ao implementar o ASDK:
- Computação
- Armazenamento
- Redes
- Cofre de Chaves

Com estes serviços fundamentais, pode oferecer infraestrutura-como-serviço (IaaS) aos seus utilizadores com configuração mínima.

### <a name="additional-services"></a>Serviços adicionais
Atualmente, são suportados os seguintes serviços de plataforma-como-serviço (PaaS) adicionais:
- Serviço de Aplicações
- Funções do Azure
- Bases de dados SQL e o MySQL

> [!NOTE]
> Esses serviços exigem configuração adicional antes de pode disponibilizá-los aos seus utilizadores e não estão disponíveis por predefinição quando instala o ASDK.

## <a name="service-roadmap"></a>Plano de serviço
O Azure Stack vai continuar a adicionar suporte para serviços adicionais do Azure. Para saber mais sobre futuras com o Azure Stack, veja a [plano do Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Passos Seguintes
Para começar a avaliar o Azure Stack, precisa primeiro [transferir o mais recente ASDK](asdk-download.md) e preparar o computador do anfitrião ASDK. Depois de preparar o anfitrião do kit de desenvolvimento, pode instalar o ASDK e iniciar sessão nos portais de administrador e utilizador para começar a utilizar o Azure Stack.