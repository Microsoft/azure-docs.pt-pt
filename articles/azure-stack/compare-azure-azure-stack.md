---
title: Comparar o Azure Stack e o global Azure | Documentos da Microsoft
description: Saiba como a Microsoft fornece o Azure e a família de serviços no único ecossistema do Azure do Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650109"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Diferenças entre global do Azure, Azure Stack e HCI de pilha do Azure

A Microsoft fornece o Azure e a família de serviços no único ecossistema do Azure do Azure Stack. Utilize o mesmo modelo aplicacional, portais self-service e as APIs com o Azure Resource Manager para fornecer recursos baseados na nuvem, independentemente de sua empresa utiliza o global Azure ou recursos no local.

Este artigo descreve as capacidades globais do Azure, o Azure Stack e o Azure Stack HCI e fornece recomendações de cenário comum para o ajudar a tomar a melhor opção para o fornecimento de serviços com base na cloud da Microsoft para a sua organização.

![Descrição geral do ecossistema do Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Global Azure

O Microsoft Azure é um conjunto de serviços cloud em expansão permanente que ajuda a sua organização a enfrentar os desafios empresariais. É a liberdade para criar, gerir e implementar aplicações numa rede global em grande escala através das suas ferramentas e arquiteturas preferidas.

O Azure global oferece mais de 100 serviços disponíveis em 54 regiões em todo o mundo. Para obter a lista mais recente de serviços globais do Azure, consulte a [ *produtos disponíveis por região*](https://azure.microsoft.com/regions/services). Os serviços disponíveis no Azure estão disponíveis por meio de pré-visualização ou listadas por categoria, bem como se eles estão disponíveis em geral.

Para obter mais informações sobre serviços globais do Azure, consulte [introdução ao Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

O Azure Stack é uma extensão do Azure que leva a agilidade e inovação da informática na cloud para o seu ambiente no local. Implementar no local, Azure Stack pode ser utilizado para fornecer serviços consistentes do Azure, que um ligado à internet (e do Azure) ou em ambientes desconectados sem conectividade internet. O Azure Stack utiliza as mesmas tecnologias subjacentes como o Azure global, que inclui os componentes nucleares de infraestrutura-como-um-serviço (IaaS), Software-como-serviço (SaaS), e capacidades de plataforma-como-serviço (PaaS) opcionais, incluindo:

- VMs do Azure para Windows e Linux
- Aplicações Web do Azure e funções
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Contentores
- O Hub IoT do Azure e Hubs de eventos
- Ferramentas de administração (planos, ofertas, RBAC, etc.)

Os recursos de PaaS do Azure Stack são opcionais, porque o Azure Stack não é operado por Microsoft — ele é operado por nossos clientes. Isso significa que pode oferecer a qualquer serviço de PaaS que pretende os utilizadores finais se está preparado para abstrair a infraestrutura subjacente e a processos do usuário final. No entanto, o Azure Stack inclui vários fornecedores de serviços de PaaS opcionais incluindo o serviço de aplicações, bases de dados SQL e bases de dados MySQL. Estas são entregues como fornecedores de recursos, para que sejam preparado para o multi-inquilino, atualizado ao longo do tempo com o padrão de atualizações do Azure Stack, visível no portal do Azure Stack e plenamente integrado com o Azure Stack.

Além dos fornecedores de recursos descritos acima, existem serviços de PaaS adicionais disponíveis e testadas como [soluções baseadas em modelos do Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) que executam em IaaS, mas, como um pode de operador do Azure Stack disponibilizá-las como Serviços de PaaS para os seus utilizadores, incluindo:

- Service Fabric
- Serviço de contentor do Kubernetes
- IoT Hub e Hub de eventos
- Etherium Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Casos de utilização de exemplo para o Azure Stack:

- Modelagem financeira
- Dados clínicos e afirmações
- Análise de dispositivos de IoT
- Otimização da variedade de varejo
- Otimização de cadeia de fornecimento
- IoT Industrial
- Manutenção preditiva
- Cidade inteligente
- Envolvimento dos cidadãos

Saiba mais sobre o Azure Stack na [o que é o Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Soluções do Azure Stack HCI permitem-lhe executar as máquinas virtuais no local e facilmente ligar ao Azure com uma solução de infraestrutura (HCI) hiperconvergido. Crie e execute aplicações na cloud com serviços do Azure consistentes no local para cumprir os requisitos regulamentares ou técnicos. Além de executar aplicativos virtualizados no local, o Azure Stack HCI permite-lhe substituir e consolidar a infraestrutura de servidor de classificação por vencimento e ligar ao Azure para serviços em nuvem com o de Windows Admin Center.

O Azure Stack HCI fornece soluções HCI validadas com tecnologia Hyper-V e espaços de armazenamento direto com o Windows Server 2019 definida pelo software Data Center (SDDC). De Windows Admin Center é utilizado para gestão e integrado, tais como o acesso aos serviços do Azure:

- Azure Backup
- Azure Site Recovery
- O Azure Monitor e atualização

Para uma lista atualizada do Azure de serviços que pode ligar o Azure Stack HCI para ver [ligar o Windows Server para serviços híbridos do Azure](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Casos de utilização de exemplo para o Azure Stack HCI
- Sistemas de office remoto ou filial
- Consolidação de Datacenter
- Infraestrutura de ambiente de trabalho virtual
- Infraestrutura de crítico para a empresa
- Armazenamento de custo mais baixo
- Elevada disponibilidade e recuperação após desastre na cloud
- Aplicações empresariais como o SQL Server

Visite o [Web site do Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci/) para ver 70 do Azure Stack HCI soluções disponíveis atualmente em parceiros da Microsoft.

## <a name="next-steps"></a>Passos Seguintes

[Noções básicas de administração do Azure Stack](azure-stack-manage-basics.md)

[Início rápido: utilizar o portal de administração do Azure Stack](azure-stack-manage-portals.md)
