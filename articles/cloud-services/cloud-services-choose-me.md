---
title: O que são os serviços de nuvem do Azure | Microsoft Docs
description: Saiba mais sobre o que são os serviços de nuvem do Azure.
services: cloud-services
author: georgewallace
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 61369d51056607d8176d301afa945c7c77895b12
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359700"
---
# <a name="overview-of-azure-cloud-services"></a>Visão geral dos serviços de nuvem do Azure
Os serviços de nuvem do Azure são um exemplo de PaaS ( [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) ). Assim como o [serviço Azure app](../app-service/overview.md), essa tecnologia foi projetada para dar suporte a aplicativos escalonáveis, confiáveis e baratos para operar. Da mesma forma que o serviço de aplicativo é hospedado em VMs (máquinas virtuais), também são os serviços de nuvem do Azure. No entanto, você tem mais controle sobre as VMs. Você pode instalar seu próprio software em VMs que usam os serviços de nuvem do Azure e pode acessá-los remotamente.

![Diagrama dos serviços de nuvem do Azure](./media/cloud-services-choose-me/diagram.png)

Mais controle também significa menos facilidade de uso. A menos que você precise das opções de controle adicionais, normalmente é mais rápido e fácil colocar um aplicativo Web em funcionamento no recurso aplicativos Web do serviço de aplicativo em comparação com os serviços de nuvem do Azure.

Há dois tipos de funções dos serviços de nuvem do Azure. A única diferença entre os dois é como sua função é hospedada nas VMs:

* **Função Web**: Implanta e hospeda automaticamente seu aplicativo por meio do IIS.

* **Função de trabalho**: Não usa o IIS e executa seu aplicativo autônomo.

Por exemplo, um aplicativo simples pode usar apenas uma única função Web, servindo um site. Um aplicativo mais complexo pode usar uma função Web para lidar com solicitações de entrada de usuários e, em seguida, passar essas solicitações para uma função de trabalho para processamento. (Essa comunicação pode usar o [barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) ou o [armazenamento de filas do Azure](../storage/common/storage-introduction.md).)

Como a figura anterior sugere, todas as VMs em um único aplicativo são executadas no mesmo serviço de nuvem. Os usuários acessam o aplicativo por meio de um único endereço IP público, com solicitações com balanceamento de carga automático entre as VMs do aplicativo. A plataforma [dimensiona e implanta](cloud-services-how-to-scale-portal.md) as VMs em um aplicativo de serviços de nuvem do Azure de uma maneira que evita um único ponto de falha de hardware.

Embora os aplicativos sejam executados em VMs, é importante entender que os serviços de nuvem do Azure fornecem PaaS, não infraestrutura como serviço (IaaS). Aqui está uma maneira de pensar nisso. Com IaaS, como máquinas virtuais do Azure, você primeiro cria e configura o ambiente no qual seu aplicativo é executado. Em seguida, você implanta seu aplicativo nesse ambiente. Você é responsável por gerenciar grande parte desse mundo, fazendo coisas como implantar novas versões com patches do sistema operacional em cada VM. No PaaS, por outro lado, é como se o ambiente já existe. Tudo o que você precisa fazer é implantar seu aplicativo. O gerenciamento da plataforma em que ele é executado, incluindo a implantação de novas versões do sistema operacional, é tratado para você.

## <a name="scaling-and-management"></a>Dimensionamento e gerenciamento
Com os serviços de nuvem do Azure, você não cria máquinas virtuais. Em vez disso, você fornece um arquivo de configuração que informa ao Azure quantas delas você gostaria, como "três instâncias de função Web" e "duas instâncias de função de trabalho". Em seguida, a plataforma as cria para você. Você ainda escolhe [o tamanho que](cloud-services-sizes-specs.md) as VMs de backup devem ter, mas você mesmo não as cria explicitamente. Se seu aplicativo precisar lidar com uma carga maior, você poderá solicitar mais VMs e o Azure criará essas instâncias. Se a carga diminuir, você poderá desligar essas instâncias e parar de pagar por elas.

Um aplicativo dos serviços de nuvem do Azure normalmente é disponibilizado para os usuários por meio de um processo de duas etapas. Primeiro, um desenvolvedor [carrega o aplicativo](cloud-services-how-to-create-deploy-portal.md) na área de preparo da plataforma. Quando o desenvolvedor estiver pronto para tornar o aplicativo ativo, ele usará o portal do Azure para alternar o preparo com a produção. Essa [mudança entre preparo e produção](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) pode ser feita sem tempo de inatividade, o que permite que um aplicativo em execução seja atualizado para uma nova versão sem perturbar seus usuários.

## <a name="monitoring"></a>Monitorização
Os serviços de nuvem do Azure também fornecem monitoramento. Assim como as máquinas virtuais, ele detecta um servidor físico com falha e reinicia as VMs que estavam em execução no servidor em um novo computador. Mas os serviços de nuvem do Azure também detectam VMs e aplicativos com falha, não apenas falhas de hardware. Ao contrário das máquinas virtuais, ela tem um agente dentro de cada função Web e de trabalho e, portanto, é capaz de iniciar novas VMs e instâncias de aplicativo quando ocorrem falhas.

A natureza de PaaS dos serviços de nuvem do Azure também tem outras implicações. Uma das mais importantes é que os aplicativos criados nessa tecnologia devem ser escritos para serem executados corretamente quando qualquer instância de função de trabalho ou Web falhar. Para conseguir isso, um aplicativo dos serviços de nuvem do Azure não deve manter o estado no sistema de arquivos de suas próprias VMs. Ao contrário das VMs criadas com máquinas virtuais, as gravações feitas nas VMs dos serviços de nuvem do Azure não são persistentes. Não há nada como um disco de dados de máquinas virtuais. Em vez disso, um aplicativo de serviços de nuvem do Azure deve gravar explicitamente todo o estado no banco de dados SQL do Azure, BLOBs, tabelas ou algum outro armazenamento externo. Criar aplicativos dessa maneira torna mais fácil dimensionar e mais resistente a falhas, que são metas importantes dos serviços de nuvem do Azure.

## <a name="next-steps"></a>Passos Seguintes
* [Criar um aplicativo de serviço de nuvem no .NET](cloud-services-dotnet-get-started.md) 
* [Criar um aplicativo de serviço de nuvem no node. js](cloud-services-nodejs-develop-deploy-app.md) 
* [Criar um aplicativo de serviço de nuvem no PHP](../cloud-services-php-create-web-role.md) 
* [Criar um aplicativo de serviço de nuvem em Python](cloud-services-python-ptvs.md)



