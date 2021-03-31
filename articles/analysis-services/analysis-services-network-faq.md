---
title: Perguntas frequentes sobre a conectividade da rede de serviços de análise | Microsoft Docs
description: Este artigo fornece respostas a algumas das questões mais comuns sobre a conectividade da rede de Serviços de Análise.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82838506"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Perguntas frequentes sobre a conectividade de rede do Analysis Services

Este artigo fornece respostas a questões comuns sobre a ligação a contas de armazenamento, fontes de dados, firewalls e endereços IP.

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

**Pergunta** - Como posso fazer backup e restaurar usando uma conta de armazenamento que está por trás de uma firewall?   
**Resposta** - Os Serviços de Análise Azure não utilizam endereços IP fixos ou Tags de Serviço. A gama de endereços IP que os servidores dos Serviços de Análise utilizam pode ser qualquer coisa na gama de endereços IP para a *região Azure.* Como os endereços IP do seu servidor são variáveis e podem ser alterados ao longo do tempo, as suas regras de firewall precisam de permitir que toda a gama de endereços IP da região de Azure esteja dentro.

**Pergunta** - A minha conta de armazenamento Azure está numa região diferente do meu servidor de Serviços de Análise. Como posso configurar as definições de firewall de conta de armazenamento?   
**Resposta** - Se a conta de armazenamento estiver numa região diferente, configufique as definições de firewall de conta de armazenamento para permitir o acesso a partir de **redes selecionadas**. Na **gama** Address De Firewall , especifique o intervalo de endereços IP para a região onde se encontra o servidor de Serviços de Análise. Para obter as gamas IP para regiões do Azure, consulte [as Gamas IP Azure e tags de serviço – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Configurar as definições de firewall de conta de armazenamento para permitir o acesso a partir de todas as redes é suportado, no entanto é preferível escolher redes selecionadas e especificar um intervalo de endereços IP. 

**Pergunta** - A minha conta de armazenamento Azure está na mesma região que o meu servidor de Serviços de Análise. Como posso configurar as definições de firewall de conta de armazenamento?   
**Resposta** – Como o servidor e a conta de armazenamento dos serviços de análise estão na mesma região, as comunicações entre eles utilizam intervalos de endereços IP internos, portanto, configurar uma firewall para utilizar redes selecionadas e especificar uma gama de endereços IP não é suportada. Se as políticas de organização requerem uma firewall, deve ser configurada para permitir o acesso a partir de todas as redes.


## <a name="data-source-connections"></a>Ligações de fonte de dados

**Pergunta** - Tenho um VNET para o meu sistema de origem de dados. Como posso permitir que os meus servidores de Serviços de Análise acedam à base de dados a partir do VNET?   
**Resposta** - Os Serviços de Análise Azure não podem aderir a um VNET. A melhor solução aqui é instalar e configurar um Gateway de dados no local no VNET e, em seguida, configurar os seus servidores de Serviços de Análise com a propriedade do servidor **AlwaysUseGateway.** Para saber mais, consulte [o Portal de Utilização de fontes de dados numa Rede Virtual Azure (VNet)](analysis-services-vnet-gateway.md).

**Pergunta:** tenho uma base de dados por trás de uma firewall. Como posso configurar a firewall para permitir que o meu servidor de Serviços de Análise aceda a ela?   
**Resposta** - Os Serviços de Análise Azure não utilizam endereços IP fixos ou Tags de Serviço. A gama de endereços IP que os servidores dos Serviços de Análise utilizam pode ser qualquer coisa na gama de endereços IP para a *região Azure.* Tem de fornecer toda a *gama* de endereços IP para a região Azure do seu servidor nas regras de firewall da base de dados de origem. Outra alternativa, e possivelmente mais segura, é configurar um Data Gateway no local. Em seguida, pode configurar os seus servidores de Serviços de Análise com a propriedade do [servidor AlwaysUseGateway](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)e, em seguida, garantir que o Data Gateway tem um endereço IP permitido pelas regras de firewall da fonte de dados.

## <a name="azure-apps-with-ip-address"></a>Aplicativos Azure com endereço IP

**Pergunta** - Uso uma aplicação baseada em Azure (por exemplo, Azure Functions, Azure Data Factory) com um endereço IP que muda no voo. Como posso gerir as regras de firewall do Azure Analysis Services para permitir dinamicamente o endereço IP onde a minha aplicação está a executar?   
**Resposta** - Os Serviços de Análise Azure não suportam Links Privados, VNETs ou Tags de Serviço. Existem algumas soluções de código aberto (por exemplo, https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) que detetam o endereço IP da aplicação do cliente e atualizam automaticamente e temporariamente as regras de firewall.


## <a name="next-steps"></a>Passos seguintes

[Instale e configuure um portal de dados no local](analysis-services-gateway-install.md)   
[Ligação a fontes de dados no local com gateway de dados no local](analysis-services-gateway.md)   
[Utilizar o gateway das origens de dados numa Rede Virtual do Azure (VNet)](analysis-services-vnet-gateway.md)
