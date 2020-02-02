---
title: Versões de Service Fabric do Azure
description: Notas de versão do Azure Service Fabric. Inclui informações sobre os recursos e melhorias mais recentes em Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 73624df3cb7b04c3c442a6654f1388017482b2ba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935446"
---
# <a name="service-fabric-releases"></a>Versões Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Issue Tracking</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Support Options</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">Supported Versions</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Code Samples</a>

Este artigo fornece mais informações sobre as versões e atualizações mais recentes para o Service Fabric Runtime e SDKs.

## <a name="whats-new-in-service-fabric"></a>O que há de novo no Service Fabric

### <a name="service-fabric-70"></a>Service Fabric 7,0

O Azure Service Fabric 7,0 já está disponível! Você poderá atualizar para o 7,0 por meio do portal do Azure ou por meio de uma implantação de Azure Resource Manager. Devido aos comentários dos clientes sobre as versões em todo o período de Natal, não começaremos a atualizar automaticamente os clusters definidos para receber atualizações automáticas até Janeiro.
Em Janeiro, retomaremos o procedimento de distribuição padrão e os clusters com as atualizações automáticas habilitadas começarão a receber a atualização 7,0 automaticamente. Forneceremos outro comunicado antes de o lançamento começar.
Também atualizaremos nossas datas de lançamento planejadas para indicar que levamos essa política em consideração. Procure atualizações em nossos [cronogramas de lançamentos](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)futuros.
 
Esta é a versão mais recente do Service Fabric e é carregada com os principais recursos e aprimoramentos.

### <a name="key-announcements"></a>Principais anúncios
 - [**Suporte KeyVaultReference para segredos do aplicativo (versão prévia)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric aplicativos que habilitaram [identidades gerenciadas](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) agora podem fazer referência diretamente a uma URL secreta Key Vault como uma variável de ambiente, um parâmetro de aplicativo ou uma credencial de repositório de contêiner. Service Fabric resolverá automaticamente o segredo usando a identidade gerenciada do aplicativo. 
     
- **Segurança de atualização aprimorada para serviços sem estado**: para garantir a disponibilidade durante uma atualização de aplicativo, introduzimos novas configurações para definir o [número mínimo de instâncias para que os serviços sem estado](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) sejam considerados disponíveis. Anteriormente, esse valor era 1 para todos os serviços e não era possível alterá-lo. Com essa nova verificação de segurança por serviço, você pode garantir que seus serviços mantenham um número mínimo de instâncias acima durante atualizações de aplicativos, atualizações de cluster e outras manutenções que se baseiam em verificações de integridade e segurança de Service Fabric.
  
- [**Limites de recursos para serviços de usuário**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): os usuários podem configurar limites de recursos para os serviços de usuário em um nó para evitar cenários como esgotamento de recursos dos serviços do sistema Service Fabric. 
  
- [**Custo de movimentação de serviço muito alto**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) para um tipo de réplica. As réplicas com custo de movimentação muito alta serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de nenhuma outra maneira. Consulte os documentos para obter informações adicionais sobre quando o uso de um custo de movimentação "muito alto" é razoável e para considerações adicionais.
  
-  **Verificações de segurança de cluster adicionais**: nesta versão, apresentamos uma verificação de segurança de quorum de nó de semente configurável. Isso permite que você personalize quantos nós de semente devem estar disponíveis durante cenários de gerenciamento e ciclo de vida do cluster. Operações que levam o cluster abaixo do valor configurado são bloqueadas. Hoje, o valor padrão é sempre um quorum dos nós de semente, por exemplo, se você tiver sete nós de semente, uma operação que o levaria abaixo de 5 nós de semente seria bloqueada por padrão. Com essa alteração, você pode tornar o valor de segurança mínimo 6, o que permitiria que apenas um nó de semente fique inativo por vez.
   
- Suporte adicionado para [**gerenciar o serviço de backup e restauração no Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Isso torna as seguintes atividades possíveis diretamente de dentro de SFX: descoberta do serviço de backup e restauração, criação de política de backup, habilitação de backups automáticos, obtenção de backups adhoc, disparo de operações de restauração e navegação de backups existentes.

- Anunciando a disponibilidade do [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): essa ferramenta ajuda a validar que os tipos usados em coleções confiáveis são compatíveis com versões posteriores e retroativas durante uma atualização de aplicativo sem interrupção. Isso ajuda a evitar falhas de atualização ou perda de dados e corrupção de dados devido a tipos ausentes ou incompatíveis.

- [**Habilitar leituras estáveis em réplicas secundárias**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1): as leituras estáveis restringirão as réplicas secundárias a retornar valores que foram confirmado de quorum.

Além disso, esta versão contém outros novos recursos, correções de bugs e melhorias de desempenho, confiabilidade e suporte. Para obter a lista completa de alterações, consulte as [notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Lançamentos de Tecido de Serviço 7.0

| Data de lançamento | Lance | Mais informações |
|---|---|---|
| 18 de novembro de 2019 | [Service Fabric do Azure 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 de janeiro de 2020 | [Lançamento de atualização de tecido de serviço Azure 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|


### <a name="service-fabric-65"></a>Service Fabric 6,5

Esta versão inclui melhorias de suporte, confiabilidade e desempenho, novos recursos, correções de bugs e aprimoramentos para facilitar o gerenciamento do ciclo de vida do cluster e do aplicativo.

> [!IMPORTANT]
> Service Fabric 6,5 é a versão final com suporte a ferramentas de Service Fabric no Visual Studio 2015. Os clientes são aconselhados a mudar para o [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) no futuro.

Aqui está o que há de novo no Service Fabric 6,5:

- Service Fabric Explorer inclui um [Visualizador de repositório de imagens](service-fabric-visualizing-your-cluster.md#image-store-viewer) para inspecionar aplicativos que você carregou no repositório de imagens.

- O [POA (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) inclui muitos aprimoramentos de autodiagnóstico. Os clientes do POA são recomendados para migrar para esta versão.

- O [serviço EventStore está habilitado por padrão](service-fabric-visualizing-your-cluster.md#event-store) para clusters Service Fabric 6,5, a menos que você tenha optado por você.

- [Eventos de ciclo de vida de réplica](service-fabric-diagnostics-event-generation-operational.md#replica-events) adicionados para serviços com estado.

- [Melhor visibilidade do status do nó de semente](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), incluindo avisos de nível de cluster se um nó de semente não estiver íntegro (*inativo*, *removido* ou *desconhecido*).

- [Service Fabric ferramenta de recuperação de desastres de aplicativos](https://github.com/Microsoft/Service-Fabric-AppDRTool) permite que Service Fabric serviços com estado se recuperem rapidamente quando o cluster primário encontra um desastre. Os dados do cluster primário são sincronizados continuamente no aplicativo em espera secundário usando backup e restauração periódicos.

- Suporte do Visual Studio para [publicação de aplicativos .NET Core em clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- A [CLI do Service Fabric do Azure (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) será instalada automaticamente para o Service Fabric 6,5 (e versões posteriores) quando você atualizar ou criar um novo cluster do Linux no Azure.

- O [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) é instalado por padrão em clusters Onebox do MacOS/Linux.

Para obter mais detalhes, consulte as [notas de versão do Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Versões do Service Fabric 6,5

| Data de lançamento | Lance | Mais informações |
|---|---|---|
| 11 de junho de 2019 | [Service Fabric do Azure 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 de julho de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 de agosto de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 de outubro de 2019 | [Versão de atualização do Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-64-releases"></a>Versões do Service Fabric 6,4

| Data de lançamento | Lance | Mais informações |
|---|---|---|
| 30 de novembro de 2018 | [Service Fabric do Azure 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de dezembro de 2018 | [Versão de atualização do Azure Service Fabric 6,4 para clusters do Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de fevereiro de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de março de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de abril de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de maio de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de maio de 2019 | [Versão de atualização do Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
