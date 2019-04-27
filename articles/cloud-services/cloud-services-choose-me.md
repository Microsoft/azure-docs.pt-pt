---
title: O que é um serviço de Cloud do Azure | Documentos da Microsoft
description: Saiba mais sobre as novidades de serviços Cloud do Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ce88dcaedf32f293fc121cda2a088388c99badee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337535"
---
# <a name="overview-of-azure-cloud-services"></a>Descrição geral dos serviços Cloud do Azure
Serviços Cloud do Azure é um exemplo de um [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Como [App Service do Azure](../app-service/overview.md), esta tecnologia foi concebida para suportar aplicações que são escaláveis, fiáveis e de baixo custo operar. Da mesma forma que o serviço de aplicações é alojado em máquinas virtuais (VMs), portanto, é demasiado serviços Cloud do Azure. No entanto, tem mais controlo sobre as VMs. Pode instalar o seu próprio software nas VMs que utilizam serviços Cloud do Azure, e pode acessá-los remotamente.

![Diagrama de serviços Cloud do Azure](./media/cloud-services-choose-me/diagram.png)

Mais controle também significa que menos facilidade de utilização. A menos que precise as opções de controle adicional, normalmente é mais rápido e fácil colocar uma aplicação web e em execução nas aplicações Web funcionalidade do serviço de aplicações em comparação com serviços Cloud do Azure.

Existem dois tipos de funções de serviços Cloud do Azure. A única diferença entre os dois é como a sua função está alojada nas VMs:

* **Função da Web**: Automaticamente, implementa e aloja a aplicação por meio de IIS.

* **Função de trabalho**: Não utiliza o IIS e executa sua autónomo de aplicação.

Por exemplo, um aplicativo simples poderá utilizar apenas uma única função da web, que serve um Web site. Uma aplicação mais complexa pode utilizar uma função da web para processar pedidos recebidos de utilizadores e, em seguida, passar esses pedidos para uma função de trabalho para processamento. (Esta comunicação poderá utilizar [do Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) ou [armazenamento de filas do Azure](../storage/common/storage-introduction.md).)

Como a figura anterior sugere, todas as VMs num único aplicativo executam no mesmo serviço cloud. Carregar automaticamente a aplicação através de um único endereço IP público, com pedidos de acesso de utilizadores balanceadas entre VMs da aplicação. A plataforma [dimensiona e implementa](cloud-services-how-to-scale-portal.md) as VMs num aplicativo de serviços Cloud do Azure de forma que evita um ponto único de falha de hardware.

Mesmo que os aplicativos são executados nas VMs, é importante compreender que os serviços Cloud do Azure oferece PaaS, não na infraestrutura como serviço (IaaS). Aqui está uma forma de pensar sobre ele. Com IaaS, como máquinas de virtuais do Azure, primeiro criar e configurar o ambiente da que sua aplicação é executada. Em seguida, implementar a sua aplicação para este ambiente. É responsável por gerenciar muita neste mundo, fazendo coisas como a implementação de novas versões corrigidas do sistema operativo em cada VM. No PaaS, por outro lado, é como se o ambiente já existe. Tudo o que precisa fazer é implementar a sua aplicação. Gerenciamento da plataforma que é executada, incluindo a implementação de novas versões do sistema operacional, é feito automaticamente.

## <a name="scaling-and-management"></a>Dimensionamento e gestão
Com os serviços de Cloud do Azure, não crie máquinas virtuais. Em vez disso, é fornecer um ficheiro de configuração que informa o Azure quantos de cada desejar, como "três instâncias de função da web" e "duas worker instâncias de função." A plataforma, em seguida, cria-los para. Ainda optar [que tamanho](cloud-services-sizes-specs.md) os VMs de segurança devem estar, mas não explicitamente criadas por si. Se seu aplicativo precisar processar uma carga maior, pode solicitar mais VMs e o Azure cria essas instâncias. Se a carga diminuir, pode encerrar as instâncias e deixarão de prestar para eles.

Um aplicativo de serviços Cloud do Azure será disponibilizado para usuários por meio de um processo de dois passos. Um desenvolvedor primeiro [carrega o aplicativo](cloud-services-how-to-create-deploy-portal.md) para área de preparo da plataforma. Quando o desenvolvedor estiver pronto para ativar o aplicativo, utilizar o portal do Azure para trocar de teste com a produção. Isso [alternar entre a transição e produção](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) pode ser feito sem tempo de inatividade, que permite que um aplicativo em execução a ser atualizado para uma nova versão sem prejudicar os seus usuários.

## <a name="monitoring"></a>Monitorização
Serviços Cloud do Azure também fornece a monitorização. Como as máquinas virtuais, Deteta um servidor físico com falhas e reinicia as VMs que estavam em execução nesse servidor numa máquina nova. Mas, serviços Cloud do Azure também Deteta as VMs e aplicações, não apenas as falhas de hardware com falhas. Ao contrário de máquinas virtuais, tem um agente dentro de cada função da web e de trabalho e, portanto, é possível iniciar novas VMs e instâncias da aplicação quando ocorrem falhas.

A natureza de PaaS de serviços Cloud do Azure também tem outras implicações. Um dos mais importante é que os aplicativos criados com essa tecnologia devem ser escritos para serem executados corretamente quando ocorre uma falha de qualquer instância de função web ou de trabalho. Para conseguir isso, um aplicativo de serviços Cloud do Azure não deve manter o estado do sistema de arquivos de suas próprias VMs. Ao contrário das VMs criadas com as máquinas virtuais, escritas realizadas para VMs de serviços Cloud do Azure não persistentes. Não há nada como um disco de dados de máquinas virtuais. Em vez disso, um aplicativo de serviços Cloud do Azure, deve escrever todos os Estados explicitamente para a base de dados do Azure SQL, blobs, tabelas ou algum outro armazenamento externo. Criação de aplicativos dessa maneira torna a mais fáceis de dimensionar e mais resistentes a falhas, que são os dois objetivos importantes de serviços Cloud do Azure.

## <a name="next-steps"></a>Passos Seguintes
* [Criar uma aplicação de serviço cloud em .NET](cloud-services-dotnet-get-started.md) 
* [Criar uma aplicação de serviço de cloud em node. js](cloud-services-nodejs-develop-deploy-app.md) 
* [Criar uma aplicação de serviço cloud em PHP](../cloud-services-php-create-web-role.md) 
* [Criar uma aplicação de serviço cloud em Python](cloud-services-python-ptvs.md)



