---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184185"
---
## <a name="scenario"></a>Cenário
Este documento descreve uma implementação que utiliza várias NICs nas VMs num cenário específico. Neste cenário, tem uma em duas camadas IaaS carga de trabalho alojada no Azure. Cada camada está implementada na sua própria sub-rede numa rede virtual (VNet). O escalão front-end é composta por vários servidores web, agrupados em conjunto num Balanceador de carga definido para elevada disponibilidade. A camada de back-end é composta por vários servidores de base de dados. Os servidores de base de dados são implementados com dois NICs cada um, um para acesso de base de dados, outra para gestão. O cenário também inclui a grupos de segurança de rede (NSGs) para controlar qual o tráfego é permitido para cada sub-rede e NIC na implementação. A imagem seguinte mostra a arquitetura básica neste cenário:

![Cenário de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

