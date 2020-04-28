---
title: O que é Azure Cloud Services [ Serviços Azure Cloud] Microsoft Docs
description: Saiba o que é o Azure Cloud Services.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386855"
---
# <a name="overview-of-azure-cloud-services"></a>Visão geral dos Serviços Azure Cloud
O Azure Cloud Services é um exemplo de uma [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Tal como o [Azure App Service,](../app-service/overview.md)esta tecnologia foi concebida para apoiar aplicações escaláveis, fiáveis e baratas para operar. Da mesma forma que o Serviço de Aplicações está hospedado em máquinas virtuais (VMs), assim como os Serviços Azure Cloud. No entanto, tem mais controlo sobre os VMs. Pode instalar o seu próprio software em VMs que utilizam os Serviços Azure Cloud e pode aceder-lhes remotamente.

![Diagrama de Serviços de Nuvem Azure](./media/cloud-services-choose-me/diagram.png)

Mais controlo também significa menos facilidade de utilização. A menos que necessite de opções de controlo adicionais, é tipicamente mais rápido e fácil obter uma aplicação web em funcionamento na funcionalidade de Aplicações Web do App Service em comparação com o Azure Cloud Services.

Existem dois tipos de funções azure Cloud Services. A única diferença entre os dois é como o seu papel é hospedado nos VMs:

* **Função web**: Implementa automaticamente e acolhe a sua aplicação através do IIS.

* **Função do trabalhador**: Não utiliza o IIS e executa a sua aplicação autónoma.

Por exemplo, uma aplicação simples pode usar apenas um único papel web, servindo um site. Uma aplicação mais complexa pode usar uma função web para lidar com os pedidos de entrada dos utilizadores e, em seguida, passar esses pedidos para uma função de trabalhador para processamento. (Esta comunicação pode utilizar [o Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) ou o armazenamento da fila [Azure](../storage/common/storage-introduction.md).)

Como a figura anterior sugere, todos os VMs numa única aplicação funcionam no mesmo serviço na nuvem. Os utilizadores acedem à aplicação através de um único endereço IP público, com pedidos de carga automaticamente equilibrados em todos os VMs da aplicação. A plataforma [escala e implanta](cloud-services-how-to-scale-portal.md) os VMs numa aplicação Azure Cloud Services de uma forma que evita um único ponto de falha de hardware.

Apesar de as aplicações funcionarem em VMs, é importante entender que a Azure Cloud Services fornece paaS, não infraestrutura como serviço (IaaS). Aqui está uma maneira de pensar sobre isto. Com o IaaS, como as Máquinas Virtuais Azure, cria-se primeiro e configura o ambiente em que a sua aplicação se encontra. Depois implantas a tua aplicação neste ambiente. É responsável por gerir grande parte deste mundo, fazendo coisas como implementar novas versões remendadas do sistema operativo em cada VM. No PaaS, pelo contrário, é como se o ambiente já existisse. Tudo o que tem que fazer é implementar a sua aplicação. A gestão da plataforma em que funciona, incluindo a implementação de novas versões do sistema operativo, é manuseada para si.

## <a name="scaling-and-management"></a>Escala e gestão
Com os Serviços Azure Cloud, não se criam máquinas virtuais. Em vez disso, fornece um ficheiro de configuração que diz ao Azure quantos de cada um gostaria, como "três instâncias de papel web" e "duas instâncias de papel de trabalhador". A plataforma cria-as para si. Ainda escolhes [o tamanho](cloud-services-sizes-specs.md) que esses VMs de apoio devem ter, mas não os crias explicitamente. Se a sua aplicação precisar de lidar com uma maior carga, pode pedir mais VMs e o Azure cria esses casos. Se a carga diminuir, pode desligar esses casos e parar de pagar por elas.

Uma aplicação Azure Cloud Services é normalmente disponibilizada aos utilizadores através de um processo em duas etapas. Um desenvolvedor envia primeiro [a aplicação](cloud-services-how-to-create-deploy-portal.md) para a área de preparação da plataforma. Quando o desenvolvedor está pronto para tornar a aplicação ao vivo, eles usam o portal Azure para trocar a encenação com a produção. Este [alternamento entre a encenação e](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) a produção pode ser feito sem tempo de inatividade, o que permite que uma aplicação em execução seja atualizada para uma nova versão sem perturbar os seus utilizadores.

## <a name="monitoring"></a>Monitorização
A Azure Cloud Services também fornece monitorização. Tal como as Máquinas Virtuais, deteta um servidor físico falhado e reinicia os VMs que estavam a funcionar nesse servidor numa nova máquina. Mas a Azure Cloud Services também deteta VMs e aplicações falhadas, e não apenas falhas de hardware. Ao contrário das Máquinas Virtuais, tem um agente dentro de cada função web e trabalhadora, e por isso é capaz de iniciar novos VMs e instâncias de aplicação quando ocorrem falhas.

A natureza PaaS dos Serviços Azure Cloud também tem outras implicações. Uma das mais importantes é que as aplicações construídas sobre esta tecnologia devem ser escritas corretamente quando qualquer instância de papel web ou trabalhador falha. Para tal, uma aplicação Azure Cloud Services não deve manter o estado no sistema de ficheiros dos seus próprios VMs. Ao contrário dos VMs criados com Máquinas Virtuais, os escritos feitos aos VMs da Azure Cloud Services não são persistentes. Não há nada como um disco de dados das Máquinas Virtuais. Em vez disso, uma aplicação Azure Cloud Services deve escrever explicitamente todo o estado à Base de Dados Azure SQL, bolhas, tabelas ou algum outro armazenamento externo. Construir aplicações desta forma torna-as mais fáceis de escalar e mais resistentes ao fracasso, que são ambos objetivos importantes dos Serviços azure cloud.

## <a name="next-steps"></a>Passos seguintes
* [Crie uma aplicação de serviço na nuvem em .NET](cloud-services-dotnet-get-started.md) 
* [Crie uma aplicação de serviço na nuvem em Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Crie uma aplicação de serviço na nuvem em PHP](../cloud-services-php-create-web-role.md) 
* [Crie uma aplicação de serviço na nuvem em Python](cloud-services-python-ptvs.md)






