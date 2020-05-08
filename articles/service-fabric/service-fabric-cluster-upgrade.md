---
title: Atualizar um cluster de tecido de serviço Azure
description: Aprenda a atualizar a versão ou configuração de um cluster Azure Service Fabric — definindo o modo de atualização do cluster, atualizando certificados, adicionando portas de aplicação, fazendo patches de OS e o que pode esperar quando as atualizações forem realizadas.
ms.topic: conceptual
ms.date: 11/12/2018
ms.custom: sfrev
ms.openlocfilehash: d92ac90e0e41d534231bafbe991a05764dbee07d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789560"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Atualizar o cluster do Service Fabric do Azure

Para qualquer sistema moderno, conceber para a graduação é fundamental para alcançar o sucesso a longo prazo do seu produto. Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. Este artigo descreve o que é gerido automaticamente e o que pode configurar-se.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de tecido que corre no seu cluster

Certifique-se de que o seu cluster está sempre a executar uma [versão de tecido suportado](service-fabric-versions.md). Cada vez que a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir dessa data. Novos lançamentos são anunciados no blog da [equipa de Service Fabric.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)

14 dias antes do termo da libertação que o seu cluster está a executar, é gerado um evento de saúde que coloca o seu cluster num estado de saúde de alerta. O cluster permanece em estado de alerta até atualizar para uma versão de tecido suportado.

Pode configurar o seu cluster para receber atualizações automáticas de tecido saem à medida que são lançadas pela Microsoft ou pode selecionar uma versão de tecido suportado em que pretende que o seu cluster esteja ligado.  Para saber mais, leia [atualizar a versão Service Fabric do seu cluster](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Comportamento de upgrade de tecido durante atualizações automáticas

A Microsoft mantém o código de tecido e a configuração que funciona num cluster Azure. Realizamos atualizações automáticas monitorizadas para o software de forma necessária. Estas atualizações podem ser código, configuração ou ambos. Para garantir que a sua aplicação não sofre qualquer impacto ou impacto mínimo devido a estas atualizações, as atualizações são realizadas nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Uma atualização é realizada utilizando todas as políticas de saúde do cluster

Durante esta fase, as atualizações procedem a um domínio de atualização de cada vez, e as aplicações que estavam a funcionar no cluster continuam a funcionar sem qualquer tempo de inatividade. As políticas de saúde do cluster (para a saúde do nó e a saúde das aplicações) são cumpridas durante a atualização.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é retiteira e um e-mail é enviado ao proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tivemos que reverter um upgrade de cluster.
* Sugeriu ações corretivas, se houver.
* O número de dias *(n)* até executarmos a Fase 2.

Tentamos executar a mesma atualização mais algumas vezes caso quaisquer atualizações falhassem por razões de infraestrutura. Após os *dias n* a partir da data em que o e-mail foi enviado, procedemos à Fase 2.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Isto pode acontecer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação por e-mail de uma corrida bem sucedida. Isto é para evitar enviar-lhe demasiados e-mails; receber um e-mail deve ser visto como uma exceção ao normal. Esperamos que a maioria das atualizações do cluster tenham sucesso sem afetar a disponibilidade da sua aplicação.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é realizada apenas através de políticas de saúde padrão

As políticas de saúde nesta fase são definidas de modo a que o número de aplicações que foram saudáveis no início da atualização permaneça o mesmo durante a duração do processo de atualização. Tal como na Fase 1, as atualizações da Fase 2 procedem a um domínio de atualização de cada vez, e as aplicações que estavam a decorrer no cluster continuam a funcionar sem qualquer tempo de inatividade. As políticas de saúde do cluster (uma combinação de saúde nó e a saúde de todas as aplicações em execução no cluster) são cumpridas durante a duração da atualização.

Se as políticas de saúde do cluster em vigor não forem cumpridas, a atualização é revertida. Em seguida, um e-mail é enviado para o proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tivemos que reverter um upgrade de cluster.
* Sugeriu ações corretivas, se houver.
* O número de dias (n) até executarmos a Fase 3.

Tentamos executar a mesma atualização mais algumas vezes caso quaisquer atualizações falhassem por razões de infraestrutura. Um e-mail de lembrete é enviado alguns dias antes de n dias acabarem. Após os dias n a partir da data em que o e-mail foi enviado, procedemos à Fase 3. Os e-mails que lhe enviamos na Fase 2 devem ser levados a sério e devem ser tomadas medidas corretivas.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Isto pode acontecer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação por e-mail de uma corrida bem sucedida.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Uma atualização é realizada através de políticas de saúde agressivas

Estas políticas de saúde nesta fase estão orientadas para a conclusão da atualização e não para a saúde das aplicações. Poucas atualizações de cluster acabam nesta fase. Se o seu cluster chegar a esta fase, há uma boa hipótese de a sua aplicação se tornar insalubre e/ou perder disponibilidade.

Semelhantes às outras duas fases, as atualizações da Fase 3 procedem a um domínio de atualização de cada vez.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida. Tentamos executar a mesma atualização mais algumas vezes caso quaisquer atualizações falhassem por razões de infraestrutura. Depois disso, o cluster é fixado, para que não receba mais suporte e/ou upgrades.

Um e-mail com esta informação é enviado ao proprietário da subscrição, juntamente com as ações corretivas. Não esperamos que nenhum agrupamento entre num estado em que a Fase 3 falhou.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Isto pode acontecer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação por e-mail de uma corrida bem sucedida.

## <a name="manage-certificates"></a>Gerir certificados

Service Fabric utiliza certificados de [servidor X.509](service-fabric-cluster-security.md) que especifica quando cria um cluster para proteger as comunicações entre nós de cluster e autenticar clientes. Pode adicionar, atualizar ou eliminar certificados para o cluster e cliente no [portal Azure](https://portal.azure.com) ou utilizar o PowerShell/Azure CLI.  Para saber mais, leia [adicionar ou remover certificados](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Portas de aplicação abertas

Pode alterar as portas de aplicação alterando as propriedades de recursos do Balancer de Carga que estão associadas ao tipo de nó. Pode utilizar o portal Azure ou pode utilizar o PowerShell/Azure CLI. Para mais informações, leia portas de [aplicação abertas para um cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definir propriedades do nó

Por vezes, é possível garantir que determinadas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs, enquanto outros não podem. Para cada um dos tipos de nós em um cluster, você pode adicionar propriedades personalizadas do nó aos nós de cluster. Os constrangimentos de colocação são as declarações anexas a serviços individuais que selecionam para uma ou mais propriedades do nó. Os constrangimentos de colocação definem onde os serviços devem ser executados.

Para mais detalhes sobre a utilização de restrições de colocação, propriedades do nó e como defini-las, leia as propriedades do nó e os constrangimentos de [colocação.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade

Para cada um dos tipos de nós, pode adicionar métricas de capacidade personalizadas que pretende utilizar nas suas aplicações para reportar carga. Para obter mais detalhes sobre a utilização de métricas de capacidade para reportar a carga, consulte os documentos do Gestor de Recursos de Cluster de Cluster de Tecidos de Serviço sobre [a descrição](service-fabric-cluster-resource-manager-cluster-description.md) do seu cluster e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Definir políticas de saúde para upgrades automáticos

Pode especificar políticas de saúde personalizadas para a atualização do tecido. Se tiver definido o seu cluster para atualizações automáticas de tecidos, então estas políticas são aplicadas à Fase-1 das atualizações automáticas de tecido.
Se tiver definido o seu cluster para atualizações de tecidomanual, estas políticas são aplicadas cada vez que seleciona uma nova versão que aciona o sistema para iniciar a atualização do tecido no seu cluster. Se não anular as políticas, os incumprimentos são utilizados.

Pode especificar as políticas de saúde personalizadas ou rever as definições atuais sob a lâmina de "upgrade de tecido", selecionando as definições avançadas de atualização. Reveja a seguinte imagem sobre como.

![Gerir políticas de saúde personalizadas][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Personalize as definições de tecido para o seu cluster

Muitas configurações de configuração diferentes podem ser personalizadas num cluster, como o nível de fiabilidade das propriedades do cluster e do nó. Para mais informações, leia [as definições](service-fabric-cluster-fabric-settings.md)de tecido de cluster de tecido de serviço .

## <a name="patch-the-os-in-the-cluster-nodes"></a>Remendar o SO nos nós do cluster

A aplicação de orquestração de patch (POA) é uma aplicação service Fabric que automatiza o sistema operativo patching em um cluster de Tecido de Serviço sem tempo de inatividade. A Aplicação de [Orquestração de Patch para Windows](service-fabric-patch-orchestration-application.md) pode ser implementada no seu cluster para instalar patches de forma orquestrada, mantendo os serviços sempre disponíveis.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a personalizar algumas das [configurações](service-fabric-cluster-fabric-settings.md) de tecido de tecido de serviço
* Aprenda a [escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md)
* Conheça as atualizações de [aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
