---
title: Upgrade clusters de tecido de serviço Azure
description: Saiba mais sobre as opções para atualizar o seu cluster de tecido de serviço Azure
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731122"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Upgrade e atualização de clusters de tecidos de serviço Azure

Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. Este artigo descreve as opções para quando e como atualizar o seu cluster de Tecido de Serviço Azure.

## <a name="automatic-versus-manual-upgrades"></a>Atualizações automáticas versus manuais

É fundamental garantir que o seu cluster de Tecido de Serviço está sempre a executar uma [versão de tempo de execução suportada.](service-fabric-versions.md) Sempre que a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para *o fim do suporte* após um mínimo de 60 dias a partir dessa data. Novos lançamentos são anunciados no blog da [equipa de Service Fabric.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)

Catorze dias antes do termo da libertação que o seu cluster está a executar, é gerado um evento de saúde que coloca o seu cluster num estado de saúde *de advertência.* O cluster permanece em estado de alerta até que você atualize para uma versão de tempo de execução suportado.

Pode configurar o seu cluster para receber atualizações automáticas do Service Fabric à medida que forem lançadas pela Microsoft, ou pode escolher manualmente entre uma lista de versões suportadas atualmente. Estas opções estão disponíveis na secção **de atualizações** do Tecido do seu recurso cluster De Tecido de Serviço.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Selecione atualizações automáticas ou manuais na secção 'Upgrades de tecido' do seu recurso cluster no portal Azure.":::

Também pode definir o seu modo de atualização de cluster e selecionar uma versão de tempo de [execução utilizando um modelo de Gestor de Recursos.](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)

As atualizações automáticas são o modo de atualização recomendado, uma vez que esta opção garante que o seu cluster permanece num estado suportado e beneficia das mais recentes correções e funcionalidades, permitindo-lhe também agendar atualizações de uma forma menos disruptiva para as suas cargas de trabalho utilizando uma estratégia [de implementação de ondas.](#wave-deployment-for-automatic-upgrades)

## <a name="wave-deployment-for-automatic-upgrades"></a>Implementação de ondas para upgrades automáticos

Com a implementação de ondas, pode minimizar a interrupção de um upgrade para o seu cluster selecionando o nível de maturidade de uma atualização, dependendo da sua carga de trabalho. Por exemplo, pode configurar um pipeline de implantação de ondas *de fase de teste* para os seus  ->    ->   vários clusters de Tecido de Serviço, a fim de testar a compatibilidade de um upgrade de tempo de execução antes de aplicá-lo às suas cargas de trabalho de produção.

Para optar pela colocação de ondas, especifique um dos seguintes valores de onda para o seu cluster (no seu modelo de implantação):

* **Onda 0**: Os clusters são atualizados assim que uma nova construção do Tecido de Serviço for lançada. Destinado a agrupamentos de teste/dev.
* **Onda 1**: Os clusters são atualizados uma semana (sete dias) após o lançamento de uma nova construção. Destinado a agrupamentos pré-prod/staging.
* **Onda 2**: Os clusters são atualizados duas semanas (14 dias) após o lançamento de uma nova construção. Destinado a clusters de produção.

Pode registar-se para notificações por e-mail com links para ajudar ainda mais se uma atualização do cluster falhar. Consulte [a implementação de Wave para obter atualizações automáticas](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) para começar.

## <a name="phases-of-automatic-upgrade"></a>Fases de atualização automática

A Microsoft mantém o código de tempo de execução do Tecido de Serviço e a configuração que funciona num cluster Azure. Executamos atualizações monitorizadas automaticamente para o software numa base necessária. Estas atualizações podem ser código, configuração ou ambos. Para minimizar o impacto destas atualizações nas suas aplicações, são realizadas nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Uma atualização é realizada utilizando todas as políticas de saúde do cluster

Durante esta fase, as atualizações prosseguem um domínio de upgrade de cada vez, e as aplicações que estavam a ser executadas no cluster continuam a funcionar sem qualquer tempo de inatividade. As políticas de saúde do cluster (para a saúde do nó e para a saúde da aplicação) são respeitadas durante a atualização.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida e um e-mail é enviado ao proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tivemos que reverter um upgrade de cluster.
* Sugeriu medidas corretivas, se houver.
* O número de dias *(n*) até executarmos a Fase 2.

Tentamos executar o mesmo upgrade mais algumas vezes no caso de quaisquer atualizações falharem por razões de infraestrutura. Após os *dias n* a partir da data em que o e-mail foi enviado, continuamos para a Fase 2.

Se as políticas de saúde do cluster forem cumpridas, a atualização é considerada bem sucedida e marcada completa. Esta situação pode ocorrer durante a atualização inicial ou qualquer uma das repetições de atualização nesta fase. Não há confirmação de e-mail de uma execução bem sucedida, para evitar o envio de e-mails excessivos. Receber um e-mail indica uma exceção às operações normais. Esperamos que a maioria das atualizações do cluster tenham sucesso sem afetar a disponibilidade da sua aplicação.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é realizada utilizando apenas políticas de saúde padrão

As políticas de saúde nesta fase são definidas de tal forma que o número de aplicações que foram saudáveis no início da atualização permanece o mesmo durante o processo de upgrade. Tal como na Fase 1, as atualizações da Fase 2 prosseguem um domínio de atualização de cada vez, e as aplicações que estavam a funcionar no cluster continuam a funcionar sem qualquer tempo de inatividade. As políticas de saúde do cluster (uma combinação de saúde do nó e a saúde a todas as aplicações em execução no cluster) são cumpridas durante a atualização.

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

## <a name="custom-policies-for-manual-upgrades"></a>Políticas personalizadas para atualizações manuais

Pode especificar políticas personalizadas para atualizações de clusters manuais. Estas políticas são aplicadas sempre que seleciona uma nova versão de tempo de execução, que desencadeia o sistema para iniciar a atualização do seu cluster. Se não anular as apólices, os predefinidos são utilizados. Para mais informações, consulte [configurar as polícias personalizadas para atualizações manuais.](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades)

## <a name="other-cluster-updates"></a>Outras atualizações de cluster

Além de atualizar o tempo de execução, existem várias outras ações que poderá ter de realizar para manter o seu cluster atualizado, incluindo o seguinte:

### <a name="managing-certificates"></a>Gestão de certificados

O Service Fabric utiliza [certificados de servidor X.509](service-fabric-cluster-security.md) que especifica quando cria um cluster para garantir comunicações entre nós de cluster e autenticar clientes. Pode adicionar, atualizar ou eliminar certificados para o cluster e cliente no [portal Azure](https://portal.azure.com) ou utilizar o PowerShell/Azure CLI.  Para saber mais, leia [adicionar ou remover certificados](service-fabric-cluster-security-update-certs-azure.md)

### <a name="opening-application-ports"></a>Portas de abertura de candidaturas

Pode alterar as portas de aplicação alterando as propriedades de recursos do Balanceador de Carga que estão associadas ao tipo de nó. Pode utilizar o portal Azure ou utilizar o PowerShell/Azure CLI. Para mais informações, leia [portas de aplicação abertas para um cluster.](create-load-balancer-rule.md)

### <a name="defining-node-properties"></a>Definição de propriedades de nó

Por vezes, pode querer garantir que determinadas cargas de trabalho funcionam apenas em certos tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs enquanto outros não. Para cada um dos tipos de nó num cluster, pode adicionar propriedades de nó personalizado aos nós de cluster. Constrangimentos de colocação são as declarações anexas a serviços individuais que selecionam para uma ou mais propriedades de nó. Os constrangimentos de colocação definem onde os serviços devem ser executados.

Para mais detalhes sobre a utilização de restrições de colocação, propriedades de nó e como defini-las, leia [propriedades de nó e restrições de colocação](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

### <a name="adding-capacity-metrics"></a>Adicionar métricas de capacidade

Para cada um dos tipos de nó, pode adicionar métricas de capacidade personalizadas que pretende utilizar nas suas aplicações para reportar a carga. Para obter detalhes sobre a utilização de métricas de capacidade para reportar a carga, consulte os documentos do Gestor de Recursos do Cluster de Tecidos de Serviço na [descrição do seu cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

### <a name="customizing-settings-for-your-cluster"></a>Personalização de configurações para o seu cluster

Muitas configurações diferentes podem ser personalizadas num cluster, como o nível de fiabilidade das propriedades do cluster e do nó. Para mais informações, leia [as definições de tecido de cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md).

### <a name="upgrading-os-images-for-cluster-nodes"></a>Atualizar imagens de SO para nóns de cluster

Permitir atualizações automáticas de imagem de SO para os seus nós de cluster de tecido de serviço é uma boa prática. Para tal, existem vários requisitos e passos a tomar. Outra opção é a utilização da Aplicação de Orquestração patch (POA, uma aplicação de Tecido de Serviço que automatiza o patching do sistema operativo num cluster de Tecido de Serviço sem tempo de inatividade. Para saber mais sobre estas opções, consulte [Patch the Windows operating system in your Service Fabric cluster](service-fabric-patch-orchestration-application.md).

## <a name="next-steps"></a>Passos seguintes

* [Gerir atualizações de tecido de serviço](service-fabric-cluster-upgrade-version-azure.md)
* Personalize as [definições do cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md)
* [Escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md)
* Saiba mais [sobre atualizações de aplicações](service-fabric-application-upgrade.md)
