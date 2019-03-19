---
title: Atualizar um cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre como atualizar a versão ou a configuração de um cluster do Azure Service Fabric.  Este artigo descreve a definição cluster modo de atualização, atualizar certificados, adicionando as portas da aplicação, fazendo os patches de SO e o que pode esperar quando as atualizações são realizadas
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: a3778a0b0e5b4b59eb29cb67c0596d9636eb3ccb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166706"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>A atualizar e a atualizar um cluster do Azure Service Fabric

Para qualquer sistema moderno, a criação de uma upgradability é a chave para alcançar o sucesso a longo prazo de seu produto. Um cluster do Azure Service Fabric é um recurso que o proprietário, mas está parcialmente gerida pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que pode configurar por conta própria.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão de recursos de infraestrutura que é executado no seu cluster

Certifique-se de manter o seu cluster a executar uma [versão de recursos de infraestrutura suportada](service-fabric-versions.md) sempre. Como e quando podemos anunciar o lançamento de uma nova versão do service fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias dessa data. As novas versões sejam anunciadas no blog da equipe de recursos de infraestrutura do serviço. A nova versão está disponível para, em seguida, escolha.

14 dias antes da expiração da versão do que seu cluster está em execução, estado de funcionamento é gerado um evento que coloca o seu cluster num Estado de funcionamento de aviso. O cluster permanecer num Estado de aviso enquanto não atualizar para uma versão de recursos de infraestrutura suportadas.

Pode definir o seu cluster para receber atualizações de recursos de infraestrutura automático à medida que são lançadas pela Microsoft ou pode selecionar uma versão de recursos de infraestrutura suportadas que pretende que o cluster na.  Para saber mais, leia [atualizam a versão de Service Fabric do seu cluster](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Comportamento de atualização de recursos de infraestrutura durante as atualizações automáticas
A Microsoft tem o código de recursos de infraestrutura e a configuração que é executado num cluster do Azure. Executamos automática de atualizações monitorizadas do software de forma conforme necessário. Essas atualizações podem ser de código, configuração ou ambos. Para certificar-se de que a sua aplicação sofre sem impacto ou um impacto mínimo devido a essas atualizações, podemos execute as atualizações nas seguintes fases:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Uma atualização é executada com todas as políticas de estado de funcionamento do cluster
Durante esta fase, as atualizações continuar um domínio de atualização por vez e os aplicativos que estavam em execução no cluster continuam a ser executado sem qualquer período de inatividade. As políticas de estado de funcionamento do cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todos os aplicativos em execução no cluster) sejam seguidos durante a atualização.

Se as políticas de estado de funcionamento do cluster não forem cumpridas, a atualização é revertida. Em seguida, é enviado um e-mail para o proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tínhamos que reverter uma atualização do cluster.
* Sugestões de tomar medidas corretivas, se houver.
* O número de dias (n) até que executamos fase 2.

Tentamos executar a mesma atualização algumas mais vezes no caso de falha de quaisquer atualizações por motivos de infraestrutura. Após os n dias a contar da data, que o e-mail foi enviado, podemos avançar para a fase 2.

As políticas de estado de funcionamento do cluster forem cumpridos, a atualização é considerada bem-sucedida e marcada como concluída. Isto pode acontecer durante a atualização inicial ou qualquer um da volta a executar atualização nesta fase. Não há nenhum e-mail de confirmação de uma execução com êxito. Isso é evitar o envio de demasiadas mensagens de correio eletrónico, receber uma mensagem de e-mail deve ser vista como uma exceção ao normal. Esperamos que a maioria das atualizações de cluster tenha êxito sem afetar a disponibilidade de aplicações.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é executada com as políticas de estado de funcionamento padrão apenas
As políticas de estado de funcionamento nesta fase são definidas de tal forma que o número de aplicativos que estavam em bom estado no início da atualização do permanece igual durante o processo de atualização. Como na fase 1, as atualizações de fase 2 continuar um domínio de atualização por vez e os aplicativos que estavam em execução no cluster continuam a ser executado sem qualquer período de inatividade. As políticas de estado de funcionamento do cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todos os aplicativos em execução no cluster) sejam seguidos durante a atualização.

Se as políticas de estado de funcionamento do cluster em vigor não forem cumpridas, a atualização é revertida. Em seguida, é enviado um e-mail para o proprietário da subscrição. O e-mail contém as seguintes informações:

* Notificação de que tínhamos que reverter uma atualização do cluster.
* Sugestões de tomar medidas corretivas, se houver.
* O número de dias (n) até que executamos fase 3.

Tentamos executar a mesma atualização algumas mais vezes no caso de falha de quaisquer atualizações por motivos de infraestrutura. Um e-mail de lembrete é enviado alguns dias antes de n dias estão operacionais. Após os n dias a contar da data, que o e-mail foi enviado, podemos avançar para a fase 3. Os e-mails, que podemos enviar-lhe na fase 2 devem ser levados a sério e é necessário tomar medidas corretivas.

As políticas de estado de funcionamento do cluster forem cumpridos, a atualização é considerada bem-sucedida e marcada como concluída. Isto pode acontecer durante a atualização inicial ou qualquer um da volta a executar atualização nesta fase. Não há nenhum e-mail de confirmação de uma execução com êxito.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Uma atualização é executada ao utilizar políticas de estado de funcionamento agressivo
Estas políticas de estado de funcionamento nesta fase são direcionadas para conclusão da atualização em vez do Estado de funcionamento dos aplicativos. Algumas atualizações de cluster acabam nesta fase. Se obtiver seu cluster para esta fase, existe uma boa chance de que a aplicação fica em mau estado de funcionamento e/ou perder a disponibilidade.

Assim como as outras duas fases, atualizações de fase 3 continuar um domínio de atualização de cada vez.

Se as políticas de estado de funcionamento do cluster não forem cumpridas, a atualização é revertida. Tentamos executar a mesma atualização algumas mais vezes no caso de falha de quaisquer atualizações por motivos de infraestrutura. Depois disso, o cluster está afixado, para que ele deixará de receber suporte e/ou as atualizações.

É enviado um e-mail com essas informações para o proprietário da subscrição, juntamente com as ações corretivas. Não Esperamos quaisquer clusters para chegar a um Estado em que a fase 3 falhou.

As políticas de estado de funcionamento do cluster forem cumpridos, a atualização é considerada bem-sucedida e marcada como concluída. Isto pode acontecer durante a atualização inicial ou qualquer um da volta a executar atualização nesta fase. Não há nenhum e-mail de confirmação de uma execução com êxito.

## <a name="manage-certificates"></a>Gerir certificados
O Service Fabric utiliza [certificados de servidor X.509](service-fabric-cluster-security.md) que especificou ao criar um cluster para proteger as comunicações entre os nós de cluster e autenticar clientes. Pode adicionar, atualizar ou eliminar certificados para o cliente no cluster a [portal do Azure](https://portal.azure.com) ou com a CLI do PowerShell/Azure.  Para saber mais, leia [adicionar ou remover certificados](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Portas da aplicação aberto
Pode alterar as portas de aplicação ao alterar as propriedades de recurso do Balanceador de carga que estão associadas com o tipo de nó. Pode utilizar o portal do Azure ou pode utilizar a CLI do PowerShell/Azure. Para obter mais informações, leia [abrir portas da aplicação para um cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definir propriedades de nó
Por vezes, pode querer garantir que determinadas cargas de trabalho são executados apenas em determinados tipos de nós do cluster. Por exemplo, pode exigir alguma carga de trabalho GPUs ou SSDs, enquanto outros podem não ter. Para cada tipo de nó num cluster, pode adicionar propriedades de nó personalizado para nós de cluster. Restrições de posicionamento são as declarações anexadas aos serviços individuais que selecionar para uma ou mais propriedades de nó. Restrições de posicionamento definem em que os serviços devem ser executados.

Para obter detalhes sobre a utilização de restrições de posicionamento, propriedades de nó e como defini-las, leia [propriedades de nó e restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Adicionar métricas de capacidade
Para cada tipo de nó, pode adicionar métricas de capacidade personalizado que pretende utilizar nas suas aplicações para comunicar carga. Para obter detalhes sobre o uso de métricas de capacidade para comunicar carga, consulte os documentos de Gestor de recursos de Cluster do Service Fabric no [que descreva o seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Definir políticas de estado de funcionamento pelas atualizações automáticas
Pode especificar políticas de estado de funcionamento personalizados para atualização do fabric. Se tiver definido o seu cluster a atualizações de recursos de infraestrutura automática, em seguida, estas políticas são aplicadas a fase 1 das atualizações automáticas de recursos de infraestrutura.
Se tiver definido o cluster para recursos de infraestrutura Manual atualizações, estas políticas são aplicadas sempre que selecione uma nova versão do sistema para iniciar a atualização de recursos de infraestrutura no seu cluster a acionar. Se não substituir as políticas, são utilizadas as predefinições.

Pode especificar as políticas de estado de funcionamento personalizado ou reveja as definições atuais no painel de "atualização do fabric", ao selecionar as definições avançadas de atualização. Reveja a imagem seguinte sobre como. 

![Gerir políticas de estado de funcionamento personalizados][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar as definições de recursos de infraestrutura para o seu cluster
Várias definições de configuração diferentes podem ser personalizadas num cluster, como o nível de fiabilidade das propriedades do cluster e nó. Para obter mais informações, leia [definições de recursos de infraestrutura de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Patch do SO de nós de cluster
O aplicativo de orquestração do patch (POA) é uma aplicação do Service Fabric que automatiza o sistema operativo, aplicação de patches no cluster do Service Fabric sem tempo de inatividade. O [Patch Orchestration Application para Windows](service-fabric-patch-orchestration-application.md) ou [Patch Orchestration Application para Linux](service-fabric-patch-orchestration-application-linux.md) pode ser implementado no seu cluster para instalar os patches de forma organizada, mantendo os serviços disponível sempre. 


## <a name="next-steps"></a>Passos Seguintes
* Saiba como personalizar algumas do [definições de recursos de infraestrutura de cluster de recursos de infraestrutura do serviço](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar e reduzir o seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [as atualizações de aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
