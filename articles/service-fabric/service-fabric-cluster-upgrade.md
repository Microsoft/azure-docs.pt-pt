---
title: Atualizar um cluster de Service Fabric do Azure
description: Saiba mais sobre como atualizar a versão ou a configuração de um cluster de Service Fabric do Azure.  Este artigo descreve como definir o modo de atualização de cluster, atualizar certificados, adicionar portas de aplicativo, realizar patches de sistema operacional e o que você pode esperar quando as atualizações são executadas
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: 161c720fbcc9370aaf273b241e88a7184f47371b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013308"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Atualizando e atualizando um cluster de Service Fabric do Azure

Para qualquer sistema moderno, a criação de uma grande possibilidade é a chave para atingir o sucesso de longo prazo de seu produto. Um Cluster Service Fabric do Azure é um recurso que você possui, mas é parcialmente gerenciado pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que você pode configurar por conta própria.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão da malha que é executada no cluster

Certifique-se de manter o cluster executando uma [versão de malha com suporte](service-fabric-versions.md) sempre. Como e quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir dessa data. As novas versões são anunciadas no blog da equipe do Service Fabric. A nova versão está disponível para escolha.

14 dias antes da expiração da liberação que o cluster está executando, é gerado um evento de integridade que coloca o cluster em um estado de integridade de aviso. O cluster permanece em um estado de aviso até que você atualize para uma versão de malha com suporte.

Você pode definir seu cluster para receber atualizações automáticas de malha à medida que elas são lançadas pela Microsoft ou você pode selecionar uma versão de malha com suporte na qual você deseja que o cluster esteja.  Para saber mais, leia [atualizar a versão Service Fabric do cluster](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Comportamento de atualização do Fabric durante atualizações automáticas
A Microsoft mantém o código de malha e a configuração que é executada em um cluster do Azure. Executamos atualizações monitoradas automáticas para o software de acordo com a necessidade. Essas atualizações podem ser código, configuração ou ambas. Para garantir que seu aplicativo sofra nenhum impacto ou impacto mínimo devido a essas atualizações, realizamos as atualizações nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: uma atualização é executada usando todas as políticas de integridade do cluster
Durante essa fase, as atualizações passam por um domínio de atualização por vez e os aplicativos que estavam em execução no cluster continuam a ser executados sem nenhum tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e a integridade de todos os aplicativos em execução no cluster) são seguidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Em seguida, um email é enviado para o proprietário da assinatura. O email contém as seguintes informações:

* Notificação de que tivemos que reverter uma atualização de cluster.
* Ações corretivas sugeridas, se houver.
* O número de dias (n) até a execução da fase 2.

Tentamos executar a mesma atualização mais algumas vezes no caso de qualquer atualização falhar por motivos de infraestrutura. Após os n dias a partir da data em que o email foi enviado, continuamos para a fase 2.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada com êxito e marcada como concluída. Isso pode ocorrer durante a atualização inicial ou qualquer retomada da atualização nesta fase. Não há nenhuma confirmação de email de uma execução bem-sucedida. Isso é para evitar o envio de emails demais – o recebimento de um email deve ser visto como uma exceção para normal. Esperamos que a maioria das atualizações do cluster seja realizada com sucesso sem afetar a disponibilidade do aplicativo.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: uma atualização é executada usando somente políticas de integridade padrão
As políticas de integridade nessa fase são definidas de forma que o número de aplicativos que estavam íntegros no início da atualização permaneça o mesmo durante o processo de atualização. Como na fase 1, as atualizações da fase 2 passam por um domínio de atualização por vez e os aplicativos que estavam em execução no cluster continuam a ser executados sem nenhum tempo de inatividade. As políticas de integridade do cluster (uma combinação de integridade do nó e a integridade de todos os aplicativos em execução no cluster) são seguidas durante a atualização.

Se as políticas de integridade do cluster em vigor não forem atendidas, a atualização será revertida. Em seguida, um email é enviado para o proprietário da assinatura. O email contém as seguintes informações:

* Notificação de que tivemos que reverter uma atualização de cluster.
* Ações corretivas sugeridas, se houver.
* O número de dias (n) até a execução da fase 3.

Tentamos executar a mesma atualização mais algumas vezes no caso de qualquer atualização falhar por motivos de infraestrutura. Um email de lembrete é enviado alguns dias antes de n dias. Após os n dias a partir da data em que o email foi enviado, continuamos para a fase 3. Os emails que enviamos para você na fase 2 devem ser levados a sério e ações corretivas devem ser tomadas.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada com êxito e marcada como concluída. Isso pode ocorrer durante a atualização inicial ou qualquer retomada da atualização nesta fase. Não há nenhuma confirmação de email de uma execução bem-sucedida.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: uma atualização é executada usando políticas de integridade agressivas
Essas políticas de integridade nessa fase são voltadas para a conclusão da atualização em vez da integridade dos aplicativos. Algumas atualizações de cluster terminam nesta fase. Se o seu cluster chegar a essa fase, haverá uma boa chance de que seu aplicativo se torne não íntegro e/ou perca a disponibilidade.

De forma semelhante às outras duas fases, as atualizações da fase 3 passam por um domínio de atualização por vez.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Tentamos executar a mesma atualização mais algumas vezes no caso de qualquer atualização falhar por motivos de infraestrutura. Depois disso, o cluster é fixado, para que ele não receba mais suporte e/ou atualizações.

Um email com essas informações é enviado para o proprietário da assinatura, juntamente com as ações corretivas. Não esperamos que nenhum cluster seja inserido em um estado em que a fase 3 falhou.

Se as políticas de integridade do cluster forem atendidas, a atualização será considerada com êxito e marcada como concluída. Isso pode ocorrer durante a atualização inicial ou qualquer retomada da atualização nesta fase. Não há nenhuma confirmação de email de uma execução bem-sucedida.

## <a name="manage-certificates"></a>Gerir certificados
O Service Fabric usa [certificados de servidor X. 509](service-fabric-cluster-security.md) que você especifica ao criar um cluster para proteger as comunicações entre nós de cluster e autenticar clientes. Você pode adicionar, atualizar ou excluir certificados para o cluster e o cliente no [portal do Azure](https://portal.azure.com) ou usando o PowerShell/CLI do Azure.  Para saber mais, leia [Adicionar ou remover certificados](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Abrir portas de aplicativo
Você pode alterar as portas do aplicativo alterando as propriedades do recurso de Load Balancer que estão associadas ao tipo de nó. Você pode usar o portal do Azure ou pode usar o PowerShell/CLI do Azure. Para obter mais informações, leia [abrir portas de aplicativo para um cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definir propriedades de nó
Às vezes, talvez você queira garantir que determinadas cargas de trabalho sejam executadas apenas em determinados tipos de nós no cluster. Por exemplo, alguma carga de trabalho pode exigir GPUs ou SSDs, enquanto outras não podem. Para cada um dos tipos de nó em um cluster, você pode adicionar propriedades de nó personalizadas a nós de cluster. As restrições de posicionamento são as instruções anexadas a serviços individuais que selecionam uma ou mais propriedades de nó. As restrições de posicionamento definem onde os serviços devem ser executados.

Para obter detalhes sobre o uso de restrições de posicionamento, propriedades de nó e como defini-las, leia [as propriedades do nó e as restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade
Para cada um dos tipos de nó, você pode adicionar métricas de capacidade personalizadas que deseja usar em seus aplicativos para relatar a carga. Para obter detalhes sobre o uso de métricas de capacidade para relatar carga, consulte os documentos Service Fabric cluster Resource Manager sobre como [descrever o cluster e as](service-fabric-cluster-resource-manager-cluster-description.md) [métricas e a carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Definir políticas de integridade para atualizações automáticas
Você pode especificar políticas de integridade personalizadas para atualização do fabric. Se você tiver definido o cluster para atualizações automáticas de malha, essas políticas serão aplicadas à fase 1 das atualizações automáticas de malha.
Se você tiver definido o cluster para atualizações de malha manual, essas políticas serão aplicadas cada vez que você selecionar uma nova versão disparando o sistema para iniciar a atualização do Fabric no cluster. Se você não substituir as políticas, os padrões serão usados.

Você pode especificar as políticas de integridade personalizadas ou examinar as configurações atuais na folha "atualização da malha", selecionando as configurações de atualização avançadas. Examine a imagem a seguir sobre como. 

![Gerenciar políticas de integridade personalizadas][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar as configurações de malha para o cluster
Muitas definições de configuração diferentes podem ser personalizadas em um cluster, como o nível de confiabilidade das propriedades do cluster e do nó. Para obter mais informações, leia [Service Fabric configurações de malha de cluster](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Corrigir o sistema operacional nos nós do cluster
O POA (aplicativo de orquestração de patch) é um aplicativo Service Fabric que automatiza a aplicação de patch do sistema operacional em um Cluster Service Fabric sem tempo de inatividade. O [aplicativo de orquestração de patch para Windows](service-fabric-patch-orchestration-application.md) pode ser implantado em seu cluster para instalar patches de maneira orquestrada enquanto mantém os serviços disponíveis o tempo todo.


## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas das configurações de [malha de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar o cluster para dentro e para fora](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [atualizações de aplicativos](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
