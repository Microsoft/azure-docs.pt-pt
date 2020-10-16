---
title: Atualize um cluster de tecido de serviço Azure
description: Saiba como atualizar a versão ou configuração de um cluster Azure Service Fabric — configurar o modo de atualização do cluster, atualizar certificados, adicionar portas de aplicação, fazer patches de SO e o que pode esperar quando as atualizações forem realizadas.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: d92ac90e0e41d534231bafbe991a05764dbee07d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82789560"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Atualizar o cluster do Service Fabric do Azure

Para qualquer sistema moderno, o design para a atualização é fundamental para alcançar o sucesso a longo prazo do seu produto. Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. Este artigo descreve o que é gerido automaticamente e o que pode configurar a si mesmo.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de tecido que funciona no seu cluster

Certifique-se de que o seu cluster está sempre a executar uma [versão de tecido suportado](service-fabric-versions.md). Sempre que a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias a partir dessa data. Novos lançamentos são anunciados no blog da [equipa de Service Fabric.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)

14 dias antes do termo da libertação que o seu cluster está a executar, é gerado um evento de saúde que coloca o seu cluster em um estado de saúde de alerta. O cluster permanece em estado de alerta até que você atualize para uma versão de tecido suportado.

Pode configurar o seu cluster para receber atualizações automáticas de tecidos à medida que forem lançadas pela Microsoft ou pode selecionar uma versão de tecido suportado que pretende que o seu cluster esteja ligado.  Para saber mais, leia [a atualização da versão De Tecido de Serviço do seu cluster.](service-fabric-cluster-upgrade-version-azure.md)

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Comportamento de upgrade de tecido durante atualizações automáticas

A Microsoft mantém o código de tecido e a configuração que funciona num cluster Azure. Realizamos atualizações automáticas monitorizadas para o software numa base necessária. Estas atualizações podem ser código, configuração ou ambos. Para garantir que a sua aplicação não sofre qualquer impacto ou impacto mínimo devido a estas atualizações, as atualizações são realizadas nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Uma atualização é realizada utilizando todas as políticas de saúde do cluster

Durante esta fase, as atualizações prosseguem um domínio de upgrade de cada vez, e as aplicações que estavam a ser executadas no cluster continuam a funcionar sem qualquer tempo de inatividade. As políticas de saúde do cluster (para a saúde do nó e para a saúde da aplicação) são respeitadas durante a atualização.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida e um e-mail é enviado ao proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tivemos que reverter um upgrade de cluster.
* Sugeriu medidas corretivas, se houver.
* O número de dias *(n*) até executarmos a Fase 2.

Tentamos executar o mesmo upgrade mais algumas vezes no caso de quaisquer atualizações falharem por razões de infraestrutura. Após os dias *n* a partir da data em que o e-mail foi enviado, prosseguimos para a Fase 2.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Isto pode acontecer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação de e-mail de uma corrida bem sucedida. Isto é para evitar o envio de muitos e-mails; receber um e-mail deve ser visto como uma exceção ao normal. Esperamos que a maioria das atualizações do cluster tenham sucesso sem afetar a disponibilidade da sua aplicação.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é realizada utilizando apenas políticas de saúde padrão

As políticas de saúde nesta fase são definidas de forma a que o número de candidaturas saudáveis no início da atualização permaneça o mesmo durante a duração do processo de atualização. Tal como na Fase 1, as atualizações da Fase 2 prosseguem um domínio de atualização de cada vez, e as aplicações que estavam a funcionar no cluster continuam a funcionar sem qualquer tempo de inatividade. As políticas de saúde do cluster (uma combinação de saúde do nó e a saúde a todas as aplicações em execução no cluster) são cumpridas durante a duração da atualização.

Se as políticas de saúde do cluster em vigor não forem cumpridas, a atualização é revertida. Em seguida, um e-mail é enviado ao proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tivemos que reverter um upgrade de cluster.
* Sugeriu medidas corretivas, se houver.
* O número de dias (n) até executarmos a Fase 3.

Tentamos executar o mesmo upgrade mais algumas vezes no caso de quaisquer atualizações falharem por razões de infraestrutura. Um e-mail de lembrete é enviado alguns dias antes do n dias terminarem. Após os dias n a partir da data em que o e-mail foi enviado, prosseguimos para a Fase 3. Os e-mails que lhe enviamos na Fase 2 devem ser levados a sério e devem ser tomadas medidas corretivas.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Isto pode acontecer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação de e-mail de uma corrida bem sucedida.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Uma atualização é realizada utilizando políticas de saúde agressivas

Estas políticas de saúde nesta fase destinam-se à conclusão da atualização e não à saúde das aplicações. Poucos upgrades de cluster acabam nesta fase. Se o seu cluster chegar a esta fase, há uma boa chance de que a sua aplicação se torne insalubre e/ou perca disponibilidade.

Semelhante às outras duas fases, as atualizações da Fase 3 procedem a um domínio de atualização de cada vez.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida. Tentamos executar o mesmo upgrade mais algumas vezes no caso de quaisquer atualizações falharem por razões de infraestrutura. Depois disso, o cluster é fixado, para que não receba mais suporte e/ou upgrades.

Um e-mail com esta informação é enviado ao proprietário da subscrição, juntamente com as ações corretivas. Não esperamos que nenhum aglomerado entre num estado em que a Fase 3 falhou.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Isto pode acontecer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação de e-mail de uma corrida bem sucedida.

## <a name="manage-certificates"></a>Gerir certificados

O Service Fabric utiliza [certificados de servidor X.509](service-fabric-cluster-security.md) que especifica quando cria um cluster para garantir comunicações entre nós de cluster e autenticar clientes. Pode adicionar, atualizar ou eliminar certificados para o cluster e cliente no [portal Azure](https://portal.azure.com) ou utilizar o PowerShell/Azure CLI.  Para saber mais, leia [adicionar ou remover certificados](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Portas de aplicação abertas

Pode alterar as portas de aplicação alterando as propriedades de recursos do Balanceador de Carga que estão associadas ao tipo de nó. Pode utilizar o portal Azure ou utilizar o PowerShell/Azure CLI. Para mais informações, leia [portas de aplicação abertas para um cluster.](create-load-balancer-rule.md)

## <a name="define-node-properties"></a>Definir propriedades de nó

Por vezes, pode querer garantir que determinadas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs enquanto outros não. Para cada um dos tipos de nó num cluster, pode adicionar propriedades de nó personalizado aos nós de cluster. Constrangimentos de colocação são as declarações anexas a serviços individuais que selecionam para uma ou mais propriedades de nó. Os constrangimentos de colocação definem onde os serviços devem ser executados.

Para mais detalhes sobre a utilização de restrições de colocação, propriedades de nó e como defini-las, leia [propriedades de nó e restrições de colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Adicione métricas de capacidade

Para cada um dos tipos de nó, pode adicionar métricas de capacidade personalizadas que pretende utilizar nas suas aplicações para reportar a carga. Para obter detalhes sobre a utilização de métricas de capacidade para reportar a carga, consulte os documentos do Gestor de Recursos do Cluster de Tecidos de Serviço na [descrição do seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Definir políticas de saúde para atualizações automáticas

Pode especificar políticas de saúde personalizadas para upgrade de tecido. Se definiu o seu cluster para atualizações automáticas de tecidos, então estas políticas são aplicadas na Fase 1 das atualizações automáticas do tecido.
Se definiu o seu cluster para atualizações manuais de tecidos, então estas políticas são aplicadas cada vez que seleciona uma nova versão que desencadeie o sistema para iniciar a atualização do tecido no seu cluster. Se não anular as apólices, os predefinidos são utilizados.

Pode especificar as políticas de saúde personalizadas ou rever as definições atuais sob a lâmina de "upgrade de tecido", selecionando as definições de atualização avançadas. Reveja a seguinte imagem sobre como.

![Gerir políticas de saúde personalizadas][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Personalize as configurações de tecido para o seu cluster

Muitas configurações diferentes podem ser personalizadas num cluster, como o nível de fiabilidade das propriedades do cluster e do nó. Para mais informações, leia [as definições de tecido de cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Remendar o SO nos nos nosmes de cluster

A aplicação de orquestração de patch (POA) é uma aplicação de Tecido de Serviço que automatiza o patching do sistema operativo num cluster de Tecido de Serviço sem tempo de inatividade. A [Aplicação de Orquestração de Patch para Windows](service-fabric-patch-orchestration-application.md) pode ser implantada no seu cluster para instalar patches de forma orquestrada, mantendo os serviços disponíveis a toda a hora.

## <a name="next-steps"></a>Passos seguintes

* Saiba como personalizar algumas das [configurações](service-fabric-cluster-fabric-settings.md) do tecido de serviço
* Saiba como [escalar o seu cluster dentro e fora](service-fabric-cluster-scale-in-out.md)
* Saiba mais [sobre atualizações de aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
