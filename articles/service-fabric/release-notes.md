---
title: Lançamentos de tecido de serviço Azure
description: Notas de lançamento para Azure Service Fabric. Inclui informações sobre as mais recentes funcionalidades e melhorias no Tecido de Serviço.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 28abe5dbd9064fb0a6bc1b3f167830672deacdcc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452701"
---
# <a name="service-fabric-releases"></a>Lançamentos de tecido de serviço

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias de resolução de problemas</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Rastreio de problemas</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Opções de Suporte</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versões suportadas</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Amostras de código</a>

Este artigo fornece mais informações sobre as mais recentes versões e atualizações para o tempo de execução do Tecido de Serviço e SDKs.

## <a name="service-fabric-72"></a>Tecido de Serviço 7.2

Estamos entusiasmados por anunciar que o lançamento 7.2 do tempo de execução do Service Fabric começou a ser lançado para as várias regiões do Azure, juntamente com as atualizações de ferramentas e SDK. As atualizações para .NET SDK, Java SDK e Service Fabric estão disponíveis através do Instalador de Plataforma Web, pacotes NuGet e repositórios Maven.

### <a name="key-announcements"></a>Principais anúncios

- **Pré-visualização**: [**Os clusters geridos por tecido de serviço**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) estão agora em visualização pública. Os clusters geridos pela Service Fabric visam simplificar a implementação e gestão do cluster, encapsulando os recursos subjacentes que compõem um cluster de Tecido de Serviço num único recurso ARM. Para mais detalhes, consulte, [a visão geral do cluster gerido pelo Service Fabric.](./overview-managed-cluster.md)
- **Pré-visualização**: [**Apoiar serviços apátridas com uma série de casos superiores ao número de nós**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) está agora em pré-visualização pública. Uma política de colocação permite a criação de múltiplos casos apátridas de uma partição num nó.
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) já está disponível.
    - Agora pode executar FabricObserver em clusters Linux e Windows.
    - Agora pode construir plugins observadores personalizados. Consulte [plugins readme](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) e o [projeto de plugin](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) de amostra para detalhes e código.
    - Pode agora alterar qualquer definição de observador através da atualização dos parâmetros de aplicação. Isto significa que já não precisa de recolocar o FO para modificar as definições específicas do observador. Por favor, veja a [amostra.](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates)
- [**Suporte para imagens de contentores Ubuntu 18.04 OneBox**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Pré-visualização**: [ **KeyVault Reference for Service Fabric applications suporta **apenas segredos versados**. Segredos sem versões não são suportados.**](./service-fabric-keyvault-references.md)
- A SF SDK exige que a mais recente atualização VS 2019 16.7.6 ou 16.8 Preview 4 possa criar novos projetos .NET Framework apátridas/stateful/actors. Se não tiver a mais recente atualização VS, depois de criar o projeto de serviço, utilize o gestor de pacotes para instalar o Microsoft.ServiceFabric.Services (versão 4.2.x) para projetos stateful/apátridas e Microsoft.ServiceFabric.Actors (versão 4.2.x) para projetos de atores de nuget.org.
- **RunToCompletion**: Service Fabric suporta conceito de corrida até à conclusão para executáveis de hóspedes. Com esta atualização assim que a réplica for concluída, serão lançados os recursos de cluster atribuídos a esta réplica.
- [**O apoio à governação dos recursos foi reforçado:**](./service-fabric-resource-governance.md)permitindo pedidos e limitações especificações para os recursos de cpu e memória.

### <a name="service-fabric-72-releases"></a>Lançamentos de Tecido de Serviço 7.2
| Data da versão | Libertar | Saiba mais |
|---|---|---|
| 21 de outubro de 2020 | [Tecido de Serviço Azure 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 de novembro de 2020 | [Azure Service Fabric 7.2 Second Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 de novembro de 2020  | Azure Service Fabric 7.2 Terceiro Lançamento de Atualização | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 de dezembro de 2020 | [Azure Service Fabric 7.2 Quarto Lançamento de Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 de janeiro de 2021 | [Azure Service Fabric 7.2 Quinto Lançamento de Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 de fevereiro de 2021 | [Azure Service Fabric 7.2 Sexto Lançamento de Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)

## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-71"></a>Tecido de Serviço 7.1

Devido à atual crise COVID-19, e tendo em conta os desafios enfrentados pelos nossos clientes, estamos a disponibilizar 7.1, mas não atualizaremos automaticamente os clusters definidos para receber atualizações automáticas. Estamos a fazer uma pausa nas atualizações automáticas até novo aviso para garantir que os clientes podem aplicar upgrades quando mais adequados para eles, para evitar perturbações inesperadas.

Poderá atualizar para 7.1 através do [portal Azure](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) ou através de uma [implementação do Azure Resource Manager](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template).

Os clusters de tecido de serviço com atualizações automáticas ativadas começarão a receber automaticamente a atualização 7.1 assim que retomarmos o procedimento de lançamento padrão. Faremos outro anúncio antes do lançamento padrão no [Site comunitário de tecnologia de tecido de serviço.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
Também publicámos atualizações até ao fim da data de suporte para grandes lançamentos a partir de 6.5 até 7.1 [aqui](./service-fabric-versions.md#supported-versions). 

#### <a name="key-announcements"></a>Principais anúncios

- **Disponibilidade Geral** de [ **Identidades Geridas de Tecido** de Serviço para aplicações de Tecido de Serviço](./concepts-managed-identity.md)
- [**Apoio a Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Pré-visualização: Conjunto de escala de máquina virtual Suporte ao disco Efeméride OS**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: Os discos EFÉMER SÃO armazenamento criados na máquina virtual local e não guardados para armazenamento remoto de Azure. São recomendados para todos os tipos de nó de tecido de serviço (Primário e Secundário), porque em comparação com os discos tradicionais persistentes de OS, discos de OS efémeros:
      -  Reduzir a latência de leitura/escrita para o disco DE
      -  Ativar operações de gestão de nó de reset/re-imagem mais rápidas
      -  Reduzir os custos globais (os discos são gratuitos e não incorrem em custos adicionais de armazenamento)
- Apoio à declaração de [**certificados de Endpoint de Serviço de aplicações de Tecido de Serviço por nome comum**](./service-fabric-service-manifest-resources.md)sujeito .
- [**Suporte a sondas de saúde para serviços contentorizados**](./probes-codepackage.md): Mecanismo de apoio à sonda liveness para aplicações contentorizadas. A Liveness Probe ajuda a anunciar a vivacidade da aplicação contentorizada e quando não respondem em tempo útil, resultará num recomeço. 
- [**Suporte para Pacotes de Código Inicializador**](./initializer-codepackages.md) para [contentores](/azure/service-fabric/service-fabric-containers-overview) e aplicações [executáveis de hóspedes.](/azure/service-fabric/service-fabric-guest-executables-introduction) Isto permite executar pacotes de código (por exemplo, contentores), numa ordem especificada, para executar a inicialização do Pacote de Serviço.
- **FabricObserver e ClusterObserver** são aplicações apátridas que capturam Telemetria de Tecido de Serviço relacionada com diferentes aspetos de um cluster SF. Ambas as aplicações estão prontas para serem implementadas em clusters de produção do Windows para capturar telemetria rica com suporte implementado para ApplicationInsights, EventSource e LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- funciona em todos os nós, gera eventos de saúde, emite telemetria quando os limiares de utilização de recursos configurados pelo utilizador são atingidos. Esta versão contém várias melhorias através da monitorização, gestão de dados, detalhes do evento de saúde, telemetria estruturada.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - funciona num nó, captura telemetria sanitária de nível de cluster. Nesta versão, o ClusterObserver também monitoriza o estado do nó e emite telemetria quando o nó está para baixo/desativado/desativado por um período de tempo mais longo do que o previsto pelo utilizador.

#### <a name="improve-application-life-cycle-experience"></a>Melhorar a experiência do ciclo de vida da aplicação

- **[Pré-visualização:Pedido de drenagem](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: Durante a manutenção planeada do serviço, tais como atualizações de serviço ou desativação de nó, gostaria de permitir que os serviços drene graciosamente as ligações. Esta função adiciona uma duração de atraso de proximidade de instância na configuração do serviço. Durante as operações planeadas, a SF removerá o endereço do Serviço da descoberta e, em seguida, esperará esta duração antes de desligar o serviço.
- **[Deteção e equilíbrio automáticos do subclusão:](./cluster-resource-manager-subclustering.md)** A subclustering acontece quando os serviços com diferentes restrições de colocação têm uma [métrica](./service-fabric-cluster-resource-manager-metrics.md)de carga comum . Se a carga nos diferentes conjuntos de nós diferir significativamente, o Service Fabric Cluster Resource Manager acredita que o cluster está desequilibrado, mesmo quando tem o melhor equilíbrio possível devido às restrições de colocação. Como resultado, tenta reequilibrar o cluster, potencialmente causando movimentos de serviço desnecessários (uma vez que o "desequilíbrio" não pode ser substancialmente melhorado). A partir desta versão, o Cluster Resource Manager tentará agora detetar automaticamente este tipo de configurações e compreender quando o desequilíbrio pode ser corrigido através do movimento, e quando em vez disso deve deixar as coisas em paz, uma vez que não é possível fazer nenhuma melhoria substancial.  
- [**Custo de Movimento Diferente para réplicas secundárias**](./service-fabric-cluster-resource-manager-movement-cost.md): Introduzimos um novo valor de custo de movimento VeryHigh que proporciona flexibilidade adicional em alguns cenários para definir se um custo de movimento separado deve ser usado para réplicas secundárias.
- Mecanismo [**de sonda Liveness**](./probes-codepackage.md) ativado para aplicações contentorizadas. A Liveness Probe ajuda a anunciar a vivacidade da aplicação contentorizada e quando não respondem em tempo útil, resultará num recomeço.
- [**Executar até a conclusão/uma vez para serviços**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Melhorias da Loja de Imagens
 - O Tecido de Serviço 7.1 utiliza **transporte personalizado para garantir a transferência de ficheiros entre nós por padrão**. A dependência da partilha de ficheiros SMB é removida da versão 7.1. As ações de ficheiro SMB seguras ainda existem em nós que contêm réplica do Serviço de Loja de Imagens para a escolha do cliente de excluir o padrão e para upgrade e downgrade para versão antiga.
       
 #### <a name="reliable-collections-improvements"></a>Melhorias fiáveis das coleções

- [**Na memória apenas armazena o suporte para serviços estatais usando Coleções Fiáveis**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): As Coleções Fiáveis Voláteis permitem que os dados sejam persistidos em disco para durabilidade contra interrupções em larga escala, podendo ser usados para cargas de trabalho como cache replicada, por exemplo, onde a perda ocasional de dados pode ser tolerada. Com base nas [limitações e restrições de Coleções Fiáveis Voláteis,](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)recomendamos isto para cargas de trabalho que não precisam de persistência, para serviços que lidam com as raras ocasiões de Qurum Loss.
- [**Pré-visualização: Service Fabric Backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): Para facilitar a gestão de backups de coleções fiáveis para aplicações Stateful de Tecido de Serviço, o Service Fabric Backup Explorer permite que os utilizadores o permitam
    - Auditoria e revisão do conteúdo das Coleções Fiáveis,
    - Atualizar o estado atual para uma visão consistente
    - Criar Cópia de Segurança do instantâneo atual das Coleções Fiáveis
    - Corrigir a corrupção de dados
                 
#### <a name="service-fabric-71-releases"></a>Lançamentos de Tecido de Serviço 7.1
| Data da versão | Libertar | Saiba mais |
|---|---|---|
| 20 de abril de 2020 | [Tecido de Serviço Azure 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Notas de versão](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 de junho de 2020 | [Microsoft Azure Service Fabric 7.1 Primeira Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 de julho de 2020 | [Microsoft Azure Service Fabric 7.1 Segundo Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 de agosto de 2020 | [Microsoft Azure Service Fabric 7.1 Terceira Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 de setembro de 2020 | [Microsoft Azure Service Fabric 7.1 Quarto Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 de outubro de 2020 | Microsoft Azure Service Fabric 7.1 Sexta Atualização | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 de novembro de 2020 | Microsoft Azure Service Fabric 7.1 Oitavo Atualização | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Tecido de Serviço 7.0

O Azure Service Fabric 7.0 já está disponível! Poderá atualizar para 7.0 através do portal Azure ou através de uma implementação do Azure Resource Manager. Devido ao feedback do cliente sobre os lançamentos em torno do período de férias, não começaremos a atualizar automaticamente os clusters definidos para receber atualizações automáticas até janeiro.
Em janeiro, retomaremos o procedimento padrão de roll-out e os clusters com atualizações automáticas ativadas começarão a receber automaticamente a atualização 7.0. Faremos outro anúncio antes do lançamento começar.
Também atualizaremos as nossas datas de lançamento planeadas para indicar que tomamos em consideração esta política. Procure aqui por atualizações sobre os nossos futuros [horários de lançamento.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)

#### <a name="key-announcements"></a>Principais anúncios
 - [**Suporte keyVaultReference para segredos de aplicação (Preview)**](./service-fabric-keyvault-references.md): Aplicações de tecido de serviço que tenham ativado [identidades geridas](./concepts-managed-identity.md) podem agora referenciar diretamente um URL secreto key Vault como uma variável ambiental, parâmetro de aplicação ou credencial de repositório de contentores. O Service Fabric resolverá automaticamente o segredo utilizando a identidade gerida da aplicação. 
     
- **Segurança melhorada para serviços apátridas**: Para garantir a disponibilidade durante uma atualização de aplicações, introduzimos novas configurações para definir o [número mínimo de casos para serviços apátridas](/dotnet/api/system.fabric.description.statelessservicedescription) a considerar disponíveis. Anteriormente este valor era 1 para todos os serviços e não era mutável. Com esta nova verificação de segurança por serviço, pode garantir que os seus serviços retêm um número mínimo de casos durante as atualizações de aplicações, atualizações de clusters e outras manutenção que dependem das verificações de saúde e segurança da Service Fabric.
  
- [**Limites de Recursos para Serviços ao Utilizador**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): Os utilizadores podem estabelecer limites de recursos para os serviços do utilizador num nó para evitar cenários como o esgotamento de recursos dos serviços do sistema Service Fabric. 
  
- [**Custo de movimento de serviço muito alto**](./service-fabric-cluster-resource-manager-movement-cost.md) para um tipo de réplica. As réplicas com custo de movimento muito elevado só serão movidas se houver uma violação de restrição no cluster que não possa ser corrigida de outra forma. Consulte o documento vinculado para obter informações adicionais sobre quando a utilização de um custo de movimento "Muito Elevado" é razoável e para considerações adicionais.
  
-  **Verificações adicionais de segurança do cluster**: Nesta versão, introduzimos uma verificação de segurança do nó de sementes configurável. Isto permite-lhe personalizar quantos nós de sementes devem estar disponíveis durante o ciclo de vida do cluster e cenários de gestão. As operações que levariam o cluster abaixo do valor configurado estão bloqueadas. Hoje em dia, o valor padrão é sempre um quórum dos nós de sementes, por exemplo, se tiver 7 nós de sementes, uma operação que o levaria abaixo de 5 nós de sementes seria bloqueado por padrão. Com esta alteração, você poderia fazer o valor mínimo de segurança 6, o que permitiria que apenas um nó de sementes para baixo de cada vez.
   
- Suporte adicional para [**a gestão do serviço de backup e restauro no Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Isto torna possível as seguintes atividades diretamente do SFX: descobrir o serviço de backup e restaurar, criar uma política de backup, permitir backups automáticos, fazer backups adhoc, desencadear operações de restauro e navegar nas cópias de segurança existentes.

- Anunciando a disponibilidade do [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Esta ferramenta ajuda a validar que os tipos utilizados em coleções fiáveis são compatíveis para a frente e para trás durante uma atualização de aplicações rolantes. Isto ajuda a prevenir falhas de upgrade ou perda de dados e corrupção de dados devido a tipos em falta ou incompatíveis.

- [**Permitir leituras estáveis em réplicas secundárias**](./service-fabric-reliable-services-configuration.md#configuration-names-1):As leituras estáveis restringirão as réplicas secundárias aos valores de retorno que foram quórum-acked.

Além disso, esta versão contém outras novidades, correções de bugs e suporte, fiabilidade e melhorias de desempenho. Para obter a lista completa de alterações, consulte as [notas de lançamento](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Lançamentos de Tecido de Serviço 7.0

| Data da versão | Libertar | Saiba mais |
|---|---|---|
| 18 de novembro de 2019 | [Tecido de Serviço Azure 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 de janeiro de 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 de fevereiro de 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 de março de 2020 | [Azure Service Fabric 7.0 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 de maio de 2020 | [Azure Service Fabric 7.0 Sexto Lançamento de Atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 de outubro de 2020 | Azure Service Fabric 7.0 No nono lançamento | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Tecido de serviço 6.5

Esta versão inclui melhorias de suporte, fiabilidade e desempenho, novas funcionalidades, correções de bugs e melhorias para facilitar a gestão do ciclo de vida do cluster e da aplicação.

> [!IMPORTANT]
> Service Fabric 6.5 é o lançamento final com suporte de ferramentas Service Fabric no Visual Studio 2015. Os clientes são aconselhados a mudarem-se para [o Visual Studio 2019.](https://visualstudio.microsoft.com/vs/)

Novidades no Tecido de Serviço 6.5:

- O Service Fabric Explorer inclui um [Visualizador de Loja](service-fabric-visualizing-your-cluster.md#image-store-viewer) de Imagens para inspecionar as aplicações que fez para a loja de imagens.

- A versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) da [Aplicação de Orquestração de Remendos (POA)](service-fabric-patch-orchestration-application.md) inclui muitas melhorias de autodiagnóscto. Recomenda-se aos clientes da POA que se mudem para esta versão.

- [O Serviço de Loja de Eventos está ativado por predefinição](service-fabric-visualizing-your-cluster.md#event-store) para clusters Service Fabric 6.5, a menos que tenha optado por não o fazer.

- Adicionou [eventos de ciclo de vida de réplica](service-fabric-diagnostics-event-generation-operational.md#replica-events) para serviços estatais.

- [Melhor visibilidade do estado do nó de sementes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), incluindo avisos de nível de aglomerado se um nó de sementes não for saudável *(Para baixo*, *removido* ou *desconhecido).*

- [Ferramenta de recuperação de desastres de aplicação de tecido](https://github.com/Microsoft/Service-Fabric-AppDRTool) de serviço permite que os serviços estatais do Service Fabric recuperem rapidamente quando o cluster primário encontra um desastre. Os dados do cluster primário são continuamente sincronizados na aplicação de espera secundária utilizando cópias de segurança periódicas e restauro.

- Suporte do Estúdio Visual para [publicar aplicações .NET Core para clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [O Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) será instalado automaticamente para o Service Fabric 6.5 (e versões posteriores) quando atualizar ou criar um novo cluster Linux no Azure.

- [O SFCTL](./service-fabric-cli.md) é instalado por padrão nos clusters MacOS/Linux OneBox.

Para mais detalhes, consulte as [notas de lançamento do Tecido de Serviço 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Lançamentos de Tecido de Serviço 6.5

| Data da versão | Libertar | Saiba mais |
|---|---|---|
| 11 de junho de 2019 | [Tecido de Serviço Azure 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 de julho de 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 de agosto de 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 out, 2019 | [Azure Service Fabric 6.5 Refresh Release](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notas de lançamento] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Lançamentos de Tecido de Serviço 6.4

| Data da versão | Libertar | Saiba mais |
|---|---|---|
| 30 de novembro de 2018 | [Tecido de Serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de dezembro de 2018 | [Azure Service Fabric 6.4 Refresh Release para clusters Windows](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de fevereiro de 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de março de 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de abril de 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de maio de 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de maio de 2019 | [Azure Service Fabric 6.4 Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
