---
title: Quais são as soluções para executar o Oracle WebLogic Server no Serviço Azure Kubernetes
description: Saiba como executar o Oracle WebLogic Server no Serviço Azure Kubernetes.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669013"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Quais são as soluções para executar o Oracle WebLogic Server no Serviço Azure Kubernetes?

Esta página descreve as soluções para executar o Oracle WebLogic Server (WLS) no Serviço Azure Kubernetes (AKS). Estas soluções são desenvolvidas e apoiadas conjuntamente pela Oracle e Microsoft.

Também é possível executar o Servidor WebLogic em Azure Virtual Machines. As soluções para o fazer são descritas [neste artigo da Microsoft](./oracle-weblogic.md).

O WebLogic Server é um servidor de aplicações java líder que executa algumas das aplicações java mais críticas da missão em todo o mundo. O WebLogic Server forma a base de middleware para a suite de software Oracle. A Oracle e a Microsoft estão empenhadas em capacitar os clientes do WebLogic Server com escolha e flexibilidade para executar cargas de trabalho no Azure como uma plataforma de nuvem líder.

## <a name="wls-on-aks-certified-and-supported"></a>WLS em AKS certificado e suportado
O WebLogic Server é certificado pela Oracle e Microsoft para funcionar bem em AKS. O Servidor WebLogic em soluções AKS destinam-se a facilitar o mais possível a execução das aplicações Java contentorizadas e orquestradas na infraestrutura de Docker e Kubernetes. As soluções focam-se na fiabilidade, escalabilidade, gestão e apoio empresarial.

Os clusters do WebLogic Server estão totalmente habilitados a funcionar em Kubernetes através do Operador De Kubernetes WebLogic (referido simplesmente como 'Operador' a partir de agora). O Operador segue o padrão do Operador Kubernetes. Simplifica a gestão e o funcionamento de domínios WebLogic e implementações em Kubernetes automatizando tarefas manuais de outra forma e adicionando funcionalidades de fiabilidade operacional extra. O Operador suporta o Oracle WebLogic Server 12c, Oracle Fusion Middleware Infrastructure 12c e não só. Testámos as imagens oficiais do Docker para o WebLogic Server 12.2.1.3 e 12.2.1.4 com o Operador. Para mais informações sobre o Operador, consulte a [documentação oficial da Oracle.](https://oracle.github.io/weblogic-kubernetes-operator/)

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Orientação, scripts e amostras para WLS em AKS
Além de certificar o Servidor WebLogic em AKS, Oracle e Microsoft em conjunto, fornece instruções detalhadas, scripts e amostras para executar o WebLogic Server em AKS. A orientação está incorporada na secção de amostra de serviço Azure Kubernetes da [documentação do Operador.](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/) A orientação destina-se a tornar a produção do WebLogic Server em implementações AKS o mais fácil possível. A orientação utiliza imagens oficiais do WebLogic Server Docker fornecidas pela Oracle. O failover é alcançado através de Ficheiros Azure acedidos através de Alegações de Volume Persistentes kubernetes. Os balançadores de carga Azure são suportados quando alojados utilizando um Serviço Kubernetes do tipo 'LoadBalancer'. O Registo de Contentores Azure (ACR) é suportado para implantar domínios WLS dentro de imagens personalizadas do Docker. A orientação permite um alto grau de configuração e personalização.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="Pode utilizar os scripts de amostra para implementar o Servidor WebLogic em AKS":::

As soluções incluem duas formas de implantar domínios WLS para AKS. Os domínios podem ser implantados diretamente nos Volumes Persistentes de Kubernetes. Esta opção de implementação é boa se quiser migrar para AKS mas ainda assim pretende administrar WLS usando a Consola de Administração ou a Ferramenta de Scripting WebLogic (WLST). A opção também permite que você mude para AKS sem adotar o desenvolvimento de Docker. Quanto mais kubernetes forma nativa de implantar domínios WLS para AKS é construir imagens personalizadas do Docker com base em imagens oficiais do Registo de Contentores oracle, publicar as imagens personalizadas para ACR e implementar o domínio para AKS usando o Operador. Esta opção na solução também permite atualizar o domínio através da Kubernetes ConfigMaps após a implementação ser feita.

No futuro, as integrações de serviços da Azure são possíveis através de ofertas de serviços Oracle WebLogic server em soluções Azure Virtual Machines.

_Estas soluções são Bring-Your-Own-License_. Eles assumem que você já tem as licenças apropriadas com a Oracle e estão devidamente licenciados para executar ofertas em Azure.

_Se estiver interessado em trabalhar de perto nos seus cenários de migração com a equipa de engenharia que desenvolve estas soluções, preencha [este pequeno inquérito](https://aka.ms/wls-on-azure-survey) e inclua as suas informações de contacto._ Gestores de programas, arquitetos e engenheiros vão contactá-lo em breve e iniciar uma estreita colaboração. A oportunidade de colaborar num cenário de migração é gratuita enquanto as soluções estão em desenvolvimento inicial ativo.

## <a name="deployment-architectures"></a>Arquiteturas de implantação

As soluções para executar o Oracle WebLogic Server no Serviço Azure Kubernetes permitirão uma vasta gama de arquiteturas de implantação prontas a produção com relativa facilidade.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Implementações complexas do Servidor WebLogic são ativadas em AKS":::

Para além do que as soluções proporcionam aos clientes a flexibilidade total para personalizar ainda mais as suas implementações. É provável que, além da implementação de aplicações, os clientes integrem mais recursos Azure com as suas implementações. Os clientes são encorajados a fornecer feedback no inquérito sobre a melhoria das soluções.

## <a name="next-steps"></a>Passos seguintes

Explore a execução do Oracle WebLogic Server no Serviço Azure Kubernetes.

> [!div class="nextstepaction"]
> [Orientação, scripts e amostras para executar WLS em AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [Operador webLogic Kubernetes](https://oracle.github.io/weblogic-kubernetes-operator/)
