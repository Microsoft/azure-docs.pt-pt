---
title: Recolher dados DNS em pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Saiba como recolher dados DNS no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: a7f075b74876ec807d790f3ffbea5dad14163535
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530421"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar-se a qualquer servidor de nomes de domínio (DNS) em execução no Windows para sentinela do Azure. Isso é feito ao instalar um agente na máquina de DNS. Registos de utilização do DNS, pode obter segurança, desempenho e informações de relacionados com operações de mensagens em fila para a infraestrutura DNS da sua organização ao recolher, analisar, e correlacionar analíticas e registos de auditoria e outros dados relacionados de servidores DNS.

Ao ativar a recolha de registos DNS, pode:
- Identificar os clientes que tentam resolver os nomes de domínios maliciosos
- Identificar registos de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS conversador
- Carga de pedidos de vista nos servidores DNS
- Falhas de registo DNS dinâmicas do Vista

## <a name="how-it-works"></a>Como funciona

Coleção de DNS é conseguida ao instalar um agente na máquina de DNS. O agente recebe eventos do DNS e os passa para o Log Analytics.

## <a name="connect-your-dns-appliance"></a>Ligar a sua aplicação de DNS

1. No portal do Azure sentinela, selecione **recolha de dados** e escolha o **DNS** mosaico.
1. Se as máquinas DNS no Azure:
    1. Clique em **transferir e instalar o agente para máquinas de virtuais do Windows**.
    1. Na **máquinas virtuais** , selecione a máquina DNS que pretende transmitir para o Azure sentinela. Certifique-se de que esta é uma VM do Windows.
    1. Na janela que se abre para essa VM, clique em **Connect**.  
    1. Clique em **habilitar** no **conector do DNS** janela. 

2. Se a sua máquina DNS não é uma VM do Azure:
    1. Clique em **transferir e instalar o agente para máquinas do Windows não pertencente ao Azure**.
    1. Na **agente direto** janela, selecione **agente de transferir o Windows (64 bits)** ou **agente de transferir o Windows (32 bits)**.
    1. Instale o agente no seu computador DNS. Copiar o **ID da área de trabalho**, **chave primária**, e **chave secundária** e utilizá-los quando lhe for pedido durante a instalação.

3. Para utilizar o esquema relevante no Log Analytics para os registos DNS, procure **DnsEvents**.

## <a name="validate"></a>Validar 

No Log Analytics, procure o esquema **DnsEvents** e certifique-se de eventos.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar a aplicações no local de DNS para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
