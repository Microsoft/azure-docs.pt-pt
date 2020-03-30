---
title: Lançamentos de Tecido de Serviço Azure
description: Notas de lançamento para tecido de serviço Azure. Inclui informações sobre as mais recentes funcionalidades e melhorias no Tecido de Serviço.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064246"
---
# <a name="service-fabric-releases"></a>Lançamentos de Tecido de Serviço

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias de resolução de problemas</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">emitem</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">opções</a> 
| de suporte de rastreio<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">de versões</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Code Samples</a> suportadas

Este artigo fornece mais informações sobre as mais recentes versões e atualizações para o tempo de execução do Tecido de Serviço e SDKs.

## <a name="whats-new-in-service-fabric"></a>O que há de novo no Tecido de Serviço

### <a name="service-fabric-70"></a>Tecido de serviço 7.0

Azure Service Fabric 7.0 já está disponível! Poderá atualizar para 7.0 através do portal Azure ou através de uma implementação do Gestor de Recursos Azure. Devido ao feedback do cliente sobre os lançamentos durante o período de férias, não começaremos a atualizar automaticamente os clusters definidos para receber atualizações automáticas até janeiro.
Em janeiro, retomaremos automaticamente o procedimento padrão de roll-out e os clusters com atualizações automáticas ativadas começarão a receber automaticamente a atualização 7.0. Faremos outro anúncio antes do lançamento começar.
Atualizaremos também as datas de lançamento planeadas para indicar que tomamos em consideração esta política. Procure aqui novidades sobre os nossos futuros horários de [lançamento.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)
 
Este é o mais recente lançamento do Service Fabric e está carregado com funcionalidades e melhorias fundamentais.

### <a name="key-announcements"></a>Principais anúncios
 - [**Suporte keyVaultReference para segredos de aplicação (Pré-visualização)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Aplicações de tecido de serviço que tenham ativado [identidades geridas](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) podem agora referir diretamente um URL secreto do Cofre chave como uma variável ambiental, parâmetro de aplicação ou credencial de repositório de contentores. O Tecido de Serviço resolverá automaticamente o segredo utilizando a identidade gerida da aplicação. 
     
- **Melhoria da segurança de upgrade para serviços apátridas**: Para garantir a disponibilidade durante uma atualização de aplicações, introduzimos novas configurações para definir o [número mínimo de instâncias para serviços apátridas](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) a serem considerados disponíveis. Anteriormente este valor era 1 para todos os serviços e não era mutável. Com esta nova verificação de segurança por serviço, pode garantir que os seus serviços mantêm um número mínimo de casos de up durante as atualizações de aplicações, upgrades de clusters e outramanutenção que depende das verificações de saúde e segurança da Service Fabric.
  
- [**Limites de recursos para serviços de utilizador**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): Os utilizadores podem configurar limites de recursos para os serviços de utilizador num nó para evitar cenários como a exaustão de recursos dos serviços do sistema Service Fabric. 
  
- [**O custo**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) de movimento de serviço muito elevado para um tipo de réplica. As réplicas com custode movimento muito elevado só serão movidas se houver uma violação de restrição no cluster que não pode ser corrigida de outra forma. Consulte os médicos para obter informações adicionais sobre quando a utilização de um custo de movimento "Muito Alto" é razoável e para considerações adicionais.
  
-  **Verificações adicionais de segurança do cluster**: Nesta versão introduzimos uma verificação de segurança de quórum de nó de semente configurável. Isto permite personalizar quantas sementes os nós devem estar disponíveis durante os cenários de ciclo de vida e gestão do cluster. As operações que levariam o cluster abaixo do valor configurado estão bloqueadas. Hoje em dia, o valor padrão é sempre um quórum dos nós de sementes, por exemplo, se tiver 7 nós de sementes, uma operação que o levaria abaixo de 5 nós de sementes seria bloqueado por padrão. Com esta mudança, você poderia fazer o valor mínimo seguro 6, o que permitiria que apenas um nó de sementes descesse de cada vez.
   
- Suporte adicional para [**gestão do serviço de backup e restauro no Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Isto torna possíveis as seguintes atividades diretamente a partir do SFX: descobrir o serviço de backup e restaurar, criar uma política de backup, permitir backups automáticos, pegar em backups adhoc, desencadear operações de restauro e navegar nas cópias de segurança existentes.

- Anunciando a disponibilidade do [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Esta ferramenta ajuda a validar que os tipos utilizados em coleções fiáveis são compatíveis para a frente e para trás durante uma atualização de aplicação em rolo. Isto ajuda a prevenir falhas de atualização ou perda de dados e corrupção de dados devido a tipos em falta ou incompatíveis.

- [**Habilitar leituras estáveis em réplicas secundárias**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):As leituras estáveis restringirão as réplicas secundárias aos valores de devolução que foram aquecidas pelo quórum.

Além disso, esta versão contém outras novas funcionalidades, correções de bugs e capacidade de suporte, fiabilidade e melhorias de desempenho. Para obter a lista completa de alterações, consulte as notas de [lançamento](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Lançamentos de Tecido de Serviço 7.0

| Data da versão | Libertar | Mais informações |
|---|---|---|
| 18 de novembro de 2019 | [Tecido de serviço Azure 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 de janeiro de 2020 | [Lançamento de atualização de tecido de serviço Azure 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 de fevereiro de 2020 | [Lançamento de atualização de tecido de serviço Azure 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

### <a name="service-fabric-65"></a>Tecido de serviço 6.5

Esta versão inclui capacidade de suporte, fiabilidade e melhorias de desempenho, novas funcionalidades, correções de bugs e melhorias para facilitar a gestão do ciclo de vida do cluster e da aplicação.

> [!IMPORTANT]
> Service Fabric 6.5 é o lançamento final com suporte de ferramentas Service Fabric no Visual Studio 2015. Os clientes são aconselhados a mudarem-se para o [Visual Studio 2019.](https://visualstudio.microsoft.com/vs/)

Aqui está o que há de novo no Tecido de Serviço 6.5:

- Service Fabric Explorer inclui um [Visualizador](service-fabric-visualizing-your-cluster.md#image-store-viewer) de Loja de Imagem para inspecionar aplicações que fez upload para loja de imagens.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) inclui muitas melhorias de autodiagnóstico. Recomenda-se aos clientes da POA que se mudem para esta versão.

- [O Serviço EventStore está ativado por padrão](service-fabric-visualizing-your-cluster.md#event-store) para os clusters Service Fabric 6.5, a menos que tenha optado por sair.

- Acrescentou [eventos de ciclo](service-fabric-diagnostics-event-generation-operational.md#replica-events) de vida de réplica para serviços estatais.

- [Melhor visibilidade do estado do nó de sementes,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)incluindo avisos ao nível do cluster se um nó de sementes não for saudável (*Down,* *Removed* ou *Unknown*).

- Ferramenta de recuperação de desastres de [aplicação](https://github.com/Microsoft/Service-Fabric-AppDRTool) de tecido de serviço permite que serviços de serviço fabric audalhes recuperem rapidamente quando o cluster primário encontra um desastre. Os dados do cluster primário são continuamente sincronizados na aplicação secundária de espera utilizando cópia de segurança periódica e restauro.

- Suporte visual studio para [a publicação de aplicações .NET Core para clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- O Tecido de [Serviço Azure CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) será instalado automaticamente para o Tecido de Serviço 6.5 (e versões posteriores) quando atualizar ou criar um novo cluster Linux no Azure.

- [O SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) é instalado por padrão nos clusters MacOS/Linux OneBox.

Para mais detalhes, consulte as notas de [lançamento do Tecido de Serviço 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Lançamentos de Tecido de Serviço 6.5

| Data da versão | Libertar | Mais informações |
|---|---|---|
| 11 de junho de 2019 | [Tecido de serviço Azure 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Lançamento de atualização do tecido de serviço Azure 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 de julho de 2019 | [Lançamento de atualização do tecido de serviço Azure 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 de agosto de 2019 | [Lançamento de atualização do tecido de serviço Azure 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 out, 2019 | [Lançamento de atualização do tecido de serviço Azure 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-64-releases"></a>Lançamentos de Tecido de Serviço 6.4

| Data da versão | Libertar | Mais informações |
|---|---|---|
| 30 de novembro de 2018 | [Tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de dezembro de 2018 | [Lançamento de atualização do tecido de serviço Azure 6.4 para clusters Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de fevereiro de 2019 | [Lançamento de atualização de tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de março de 2019 | [Lançamento de atualização de tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de abril de 2019 | [Lançamento de atualização de tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de maio de 2019 | [Lançamento de atualização de tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de maio de 2019 | [Lançamento de atualização de tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
