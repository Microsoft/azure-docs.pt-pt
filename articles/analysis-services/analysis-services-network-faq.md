---
title: Perguntas frequentes sobre conectividade da rede de Serviços de Análise [ Microsoft Docs
description: Este artigo fornece respostas a algumas das questões mais comuns sobre a conectividade da rede de Serviços de Análise.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838506"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Perguntas frequentes sobre conectividade da rede de Serviços de Análise

Este artigo fornece respostas a questões comuns sobre a ligação a contas de armazenamento, fontes de dados, firewalls e endereços IP.

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

**Pergunta** - Como posso fazer backup e restaurar usando uma conta de armazenamento que está por trás de uma firewall?   
**Resposta** - Os Serviços de Análise Azure não utilizam endereços IP fixos ou etiquetas de serviço. A gama de endereços IP que os seus servidores de Serviços de Análise utilizam pode ser qualquer coisa na gama de endereços IP para a região de *Azure*. Como os endereços IP do servidor são variáveis e podem alterar-se ao longo do tempo, as suas regras de firewall precisam de permitir que toda a gama de endereços IP da região do Azure esteja em que o seu servidor se encontra.

**Pergunta** - A minha conta de armazenamento Azure encontra-se numa região diferente do meu servidor de Serviços de Análise. Como configurar as definições de firewall da conta de armazenamento?   
**Resposta** - Se a conta de armazenamento estiver numa região diferente, configure as definições de firewall da conta de armazenamento para permitir o acesso a partir de **redes Selecionadas**. Na **gama 'Endereço**firewall', especifique a gama de endereços IP para a região onde o servidor de Serviços de Análise se encontra. Para obter as gamas IP para regiões Azure, consulte [Gamas ip Azur e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço – Nuvem Pública. Configurar as definições de firewall da conta de armazenamento para permitir o acesso a partir de todas as redes é suportado, no entanto, escolher redes Selecionadas e especificar uma gama de endereços IP é preferível. 

**Pergunta** - A minha conta de armazenamento Azure está na mesma região que o meu servidor de Serviços de Análise. Como posso configurar as definições de firewall da conta de armazenamento?   
**Resposta** – Como o servidor e a conta de armazenamento do seu servidor de Serviços de Análise estão na mesma região, as comunicações entre eles utilizam intervalos internos de endereços IP, portanto, configurar uma firewall para utilizar redes Selecionadas e especificar uma gama de endereços IP não é suportada. Se as políticas de organização requerem uma firewall, deve ser configurada para permitir o acesso a partir de todas as redes.


## <a name="data-source-connections"></a>Ligações de fonte de dados

**Pergunta** - Tenho um VNET para o meu sistema de fonte de dados. Como posso permitir que os meus servidores de Serviços de Análise acedam à base de dados do VNET?   
**Resposta** - Os Serviços de Análise Azure não conseguem aderir a um VNET. A melhor solução aqui é instalar e configurar um Gateway de Dados no VNET no prelocal e, em seguida, configurar os seus servidores de Serviços de Análise com a propriedade do servidor **AlwaysUseGateway.** Para saber mais, consulte Utilize a porta de dados para obter fontes de [dados numa Rede Virtual Azure (VNet)](analysis-services-vnet-gateway.md).

**Pergunta-** Tenho uma base de dados atrás de uma firewall. Como posso configurar a firewall para permitir que o meu servidor de Serviços de Análise aceda à sua?   
**Resposta** - Os Serviços de Análise Azure não utilizam endereços IP fixos ou etiquetas de serviço. A gama de endereços IP que os seus servidores de Serviços de Análise utilizam pode ser qualquer coisa na gama de endereços IP para a região de *Azure*. Tem de fornecer *toda* a gama de endereços IP para a região Azure do seu servidor nas regras de firewall da base de dados de origem. Outra alternativa, e possivelmente mais segura, é configurar um Gateway de Dados no local. Em seguida, pode configurar os seus servidores de Serviços de Análise com a propriedade do [servidor AlwaysUseGateway](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)e, em seguida, garantir que o Gateway de Dados no local tem um endereço IP permitido pelas regras de firewall da fonte de dados.

## <a name="azure-apps-with-ip-address"></a>Aplicativos Azure com endereço IP

**Pergunta** - Uso uma aplicação baseada no Azure (por exemplo, Funções Azure, Azure Data Factory) com um endereço IP que muda no voo. Como posso gerir as regras de firewall do Azure Analysis Services para permitir dinamicamente o endereço IP onde a minha aplicação está a executar?   
**Resposta** - Os Serviços de Análise Azure não suportam Ligações Privadas, VNETs ou Etiquetas de Serviço. Existem algumas soluções de código https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) aberto (por exemplo, que detetam o endereço IP da aplicação do cliente e atualizam automaticamente e temporariamente as regras de firewall.


## <a name="next-steps"></a>Passos seguintes

[Instalar e configurar um portal de dados no local](analysis-services-gateway-install.md)   
[Ligação a fontes de dados no local com gateway de dados no local](analysis-services-gateway.md)   
[Utilize o portal para fontes de dados numa Rede Virtual Azure (VNet)](analysis-services-vnet-gateway.md)
