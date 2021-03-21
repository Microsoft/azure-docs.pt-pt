---
title: O que é Azure Cloud Services (clássico) | Microsoft Docs
description: Saiba o que é o Azure Cloud Services, especificamente que foi projetado para suportar aplicações que são escaláveis, fiáveis e baratas para operar.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cbb9aae57b952c05aa722f81309158a11aef826b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742816"
---
# <a name="overview-of-azure-cloud-services-classic"></a>Visão geral dos Serviços de Nuvem Azure (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

O Azure Cloud Services é um exemplo de uma [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Tal como [o Azure App Service,](../app-service/overview.md)esta tecnologia destina-se a suportar aplicações que sejam escaláveis, fiáveis e baratas para operar. Da mesma forma que o Serviço de Aplicações está hospedado em máquinas virtuais (VMs), também o Azure Cloud Services. No entanto, tens mais controlo sobre os VMs. Pode instalar o seu próprio software em VMs que utilizam os Serviços Azure Cloud, e pode aceder-lhes remotamente.

![Diagrama dos Serviços Azure Cloud](./media/cloud-services-choose-me/diagram.png)

Mais controlo também significa menos facilidade de utilização. A menos que precise das opções de controlo adicionais, é normalmente mais rápido e fácil obter uma aplicação web em funcionamento na funcionalidade de Aplicações Web do Serviço de Aplicações em comparação com os Serviços Azure Cloud.

Existem dois tipos de funções da Azure Cloud Services. A única diferença entre os dois é como o seu papel é hospedado nos VMs:

* **Função web**: Implementa e hospeda automaticamente a sua aplicação através do IIS.

* **Função do trabalhador**: Não utiliza o IIS e executa a sua aplicação autónoma.

Por exemplo, uma aplicação simples pode usar apenas uma função web, servindo um site. Uma aplicação mais complexa pode usar uma função web para lidar com pedidos de entrada dos utilizadores e, em seguida, passar esses pedidos para uma função de trabalhador para o processamento. (Esta comunicação pode utilizar [o Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) ou o armazenamento da Fila [Azure](../storage/common/storage-introduction.md).)

Como a figura anterior sugere, todos os VMs numa única aplicação são executados no mesmo serviço de nuvem. Os utilizadores acedem à aplicação através de um único endereço IP público, com pedidos automaticamente carregados equilibrados em todos os VMs da aplicação. A plataforma [escala e implementa](cloud-services-how-to-scale-portal.md) os VMs numa aplicação Azure Cloud Services de forma a evitar um único ponto de falha de hardware.

Apesar de as aplicações funcionarem em VMs, é importante entender que a Azure Cloud Services fornece PaaS, não infraestrutura como um serviço (IaaS). Aqui está uma maneira de pensar nisso. Com o IaaS, como as Máquinas Virtuais Azure, primeiro cria e configura o ambiente em que a sua aplicação funciona. Em seguida, coloque a sua aplicação neste ambiente. Você é responsável por gerir grande parte deste mundo, fazendo coisas como implementar novas versões remendadas do sistema operativo em cada VM. No PaaS, pelo contrário, é como se o ambiente já existisse. Tudo o que tem que fazer é implementar a sua candidatura. A gestão da plataforma em que funciona, incluindo a implementação de novas versões do sistema operativo, é gerida para si.

## <a name="scaling-and-management"></a>Escalagem e gestão
Com o Azure Cloud Services, não se criam máquinas virtuais. Em vez disso, fornece um ficheiro de configuração que diz ao Azure quantos de cada um gostaria, como "três instâncias de papel web" e "duas instâncias de papel de trabalhador". A plataforma cria-as para si. Ainda escolhes [o tamanho desses](cloud-services-sizes-specs.md) VMs de apoio, mas não os crias explicitamente. Se a sua aplicação precisar de lidar com uma carga maior, pode pedir mais VMs, e a Azure cria esses casos. Se a carga diminuir, pode desligar esses casos e parar de pagar por elas.

Uma aplicação Azure Cloud Services é normalmente disponibilizada aos utilizadores através de um processo em duas etapas. Um desenvolvedor [primeiro envia a aplicação](cloud-services-how-to-create-deploy-portal.md) para a área de preparação da plataforma. Quando o desenvolvedor está pronto para fazer a aplicação ao vivo, eles usam o portal Azure para trocar a encenação com a produção. Este [interruptor entre a encenação e a produção](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) pode ser feito sem tempo de inatividade, o que permite que uma aplicação em execução seja atualizada para uma nova versão sem perturbar os seus utilizadores.

## <a name="monitoring"></a>Monitorização
O Azure Cloud Services também fornece monitorização. Tal como as Máquinas Virtuais, deteta um servidor físico falhado e reinicia os VMs que estavam a funcionar naquele servidor numa nova máquina. Mas o Azure Cloud Services também deteta VMs e aplicações falhadas, e não apenas falhas de hardware. Ao contrário das Máquinas Virtuais, tem um agente dentro de cada web e papel de trabalhador, e por isso é capaz de iniciar novos VMs e instâncias de aplicação quando as falhas ocorrem.

A natureza PaaS da Azure Cloud Services também tem outras implicações. Uma das mais importantes é que as aplicações construídas sobre esta tecnologia devem ser escritas para funcionar corretamente quando qualquer instância de função web ou trabalhador falha. Para isso, uma aplicação Azure Cloud Services não deve manter o estado no sistema de ficheiros dos seus próprios VMs. Ao contrário dos VMs criados com Máquinas Virtuais, as escritas feitas para VMs Azure Cloud Não são persistentes. Não há nada como um disco de dados das Máquinas Virtuais. Em vez disso, uma aplicação Azure Cloud Services deve escrever explicitamente todos os estados para Azure SQL Database, blobs, tabelas ou algum outro armazenamento externo. A construção de aplicações desta forma torna-as mais fáceis de escalar e mais resistentes ao fracasso, que são ambos objetivos importantes dos Serviços Azure Cloud.

## <a name="next-steps"></a>Passos seguintes
* [Criar uma aplicação de serviço em nuvem em .NET](cloud-services-dotnet-get-started.md) 
* [Crie uma aplicação de serviço em nuvem em Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Criar uma aplicação de serviço em nuvem em PHP](../cloud-services-php-create-web-role.md) 
* [Crie uma aplicação de serviço em nuvem em Python](cloud-services-python-ptvs.md)






