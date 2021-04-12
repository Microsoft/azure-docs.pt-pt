---
title: Migrar serviços em nuvem Azure (clássico) para Azure Cloud Services (suporte alargado)
description: Visão geral da migração dos Serviços cloud (clássico) para o Serviço de Nuvem (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287000"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrar serviços em nuvem Azure (clássico) para Azure Cloud Services (suporte alargado)

Este artigo fornece uma visão geral sobre a ferramenta de migração suportada pela plataforma e como usá-lo para migrar [a Azure Cloud Services (clássico)](../cloud-services/cloud-services-choose-me.md) para [Azure Cloud Services (suporte alargado)](overview.md).

A ferramenta de migração utiliza as mesmas APIs e tem a mesma experiência que a [migração da Máquina Virtual (clássica).](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview) 

> [!IMPORTANT]
> A migração dos Serviços cloud (clássico) para os Serviços cloud (suporte alargado) utilizando a ferramenta de migração está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Consulte os seguintes recursos se precisar de assistência na sua migração: 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): Microsoft e suporte comunitário para a migração.
- [Apoio à Migração Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): Equipa de apoio dedicada à assistência técnica durante a migração. Os clientes sem suporte técnico podem utilizar [a capacidade de suporte gratuito](https://aka.ms/cs-migration-errors) fornecida especificamente para esta migração.
- Se a sua empresa/organização tiver uma parceria com a Microsoft ou trabalhar com representantes da Microsoft, como arquitetos de soluções cloud ou gestores técnicos de contas, contacte-os para obter mais recursos para a migração.
- Complete [este inquérito](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) para fornecer feedback ou levantar problemas à equipa de produtos cloud Services (suporte alargado). 

## <a name="migration-benefits"></a>Benefícios migratórios
A migração suportada pela plataforma proporciona os seguintes benefícios principais:
- A migração é totalmente orquestrada pela plataforma, movendo toda a implementação e recursos associados para Azure Resource Manager.
- Sem migração de tempo de inatividade.
- Migração fácil e rápida em comparação com outros caminhos de migração, minimizando as tarefas manuais. 
- Mantém o endereço IP dos Serviços cloud e a etiqueta DNS como parte da migração. 

Para outros benefícios e por que deve migrar, consulte [os Serviços cloud (suporte alargado)](overview.md) e [o Gestor de Recursos Azure.](../azure-resource-manager/management/overview.md) 

## <a name="setup-access-for-migration"></a>Acesso à configuração para migração

Para realizar esta migração, você deve ser adicionado como um coadministrator para a subscrição e registar os fornecedores necessários. 

1. Inicie sessão no portal do Azure.
3. No menu Hub, selecione Subscrição. Se não o vir, selecione Todos os serviços.
3. Encontre a entrada de subscrição adequada e, em seguida, olhe para o campo MY ROLE. Para um coadministrador, o valor deve ser a administração da conta. Se não conseguir adicionar um coadministrador, contacte um administrador de serviço ou coadministrador para que a subscrição seja adicionada.

4. Registe a sua subscrição para Microsoft.ClassicInfrastructureMigrate namespace usando [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Registe a sua subscrição para a funcionalidade de pré-visualização da migração dos Serviços cloud utilizando [o Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Verifique o estado do seu registo. As inscrições podem demorar alguns minutos a ser concluídas. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Como é que a migração para serviços cloud (clássico) é diferente das Máquinas Virtuais (clássicas)?
O Azure Service Manager suporta dois produtos computativos [diferentes, Azure Virtual Machines (clássico)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) e [Azure Cloud Services (clássico)](../cloud-services/cloud-services-choose-me.md) ou web/worker roles. Os dois produtos diferem com base no tipo de implantação que se encontra dentro do Serviço cloud. A Azure Cloud Services (clássico) utiliza o Cloud Service contendo implementações com funções Web/Worker. A Azure Virtual Machines (clássico) utiliza um serviço de nuvem contendo implementações com IaaS VMs.

A lista de cenários suportados difere entre cloud Services (clássico) e Virtual Machines (clássico) devido às diferenças nos tipos de implementação.

## <a name="migration-steps"></a>Passos de migração
 
Os clientes podem migrar as suas implementações em Cloud Services (clássicas) utilizando as mesmas quatro operações usadas para migrar Máquinas Virtuais (clássicas). 

1. **Validar a Migração** - Valida que a migração não será impedida por cenários comuns não apoiados.
2. **Preparar Migração** – Duplica os metadados de recursos no Azure Resource Manager. Todos os recursos estão bloqueados para operações de criação/atualização/eliminação para garantir que os metadados de recursos estão sincronizados entre o Azure Server Manager e o Azure Resource Manager. Todas as operações de leitura funcionarão usando apis de Serviços cloud (clássicos) e cloud services (suporte alargado).
3. **Abortar migração** - Remove metadados de recursos do Gestor de Recursos Azure. Desbloqueia todos os recursos para criar/atualizar/eliminar operações.
4. **Cometer migração** - Remove metadados de recursos do Gestor de Serviços Azure. Desbloqueia o recurso para operações de criação/atualização/eliminação. Abortar já não é permitido depois de ter sido tentado.

>[!NOTE]
> Prepare, Aborte e Comprometa são idempotentes e, portanto, se falhado, uma nova tentativa deve corrigir o problema.

:::image type="content" source="media/in-place-migration-1.png" alt-text="A imagem mostra o diagrama de passos associados à migração.":::

Para mais informações, consulte [a visão geral da migração suportada pela Plataforma de recursos iaas do clássico para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Recursos e funcionalidades suportados disponíveis para migração associada aos Serviços cloud (clássico)
-   Contas de Armazenamento
-   Redes Virtuais
-   Grupos de Segurança de Rede
-   Endereços IP públicos reservados
-   Listas de Controlo de Acesso ao Ponto Final
-   Rotas Definidas pelo Utilizador
-   Balanceador de carga interno
-   Migração de certificados para cofre chave
-   Plugins e Extensão (baseado em XML e Json)
-   No início / Em Tarefas de paragem
-   Implementações com Rede Acelerada
-   Implementações utilizando funções únicas ou múltiplas
-   Balanceador de carga básico
-   Entrada, Entrada de Exemplo, Pontos finais Internos
-   Endereços IP públicos dinâmicos
-   Nome DNS 
-   Regras de tráfego de rede
-   Rede virtual hypernet

## <a name="supported-configurations--migration-scenarios"></a>Configurações suportadas / cenários de migração
Estes são os principais cenários envolvendo combinações de recursos, funcionalidades e Serviços Cloud. Esta lista não é exaustiva.

| Serviço | Configuração | Comentários | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Redes virtuais que contêm serviços de Domínio do Diretório Ativo Azure. | A rede virtual que contém tanto a implementação do Cloud Service como os serviços de Domínio AD Azure são suportados. O cliente precisa primeiro de migrar separadamente os serviços de Domínio AD Azure e, em seguida, migrar a rede virtual deixada apenas com a implementação do Cloud Service |
| Serviço Cloud | Serviço cloud com uma implantação apenas numa única ranhura. | Os Serviços cloud que contenham uma colocação de ranhuras de prod ou de paragem podem ser migrados |
| Serviço Cloud | Implantação não numa rede virtual publicamente visível (implementação de rede virtual padrão) | Um Cloud Service pode estar numa rede virtual publicamente visível, numa rede virtual escondida ou não em qualquer rede virtual.  Os Serviços Cloud numa rede virtual oculta e redes virtuais publicamente visíveis são suportados para migração. O cliente pode utilizar a API Validada para saber se uma implementação está dentro de uma rede virtual padrão ou não e assim determinar se pode ser migrada. |
|Serviço Cloud | Extensões XML (BGInfo, Visual Studio Debugger, Web Deploy e Remote Debugging). | Todas as extensões xml são suportadas para a migração 
| Rede Virtual | Rede virtual contendo vários Serviços Cloud. | A rede virtual contém vários serviços em nuvem é suportada para migração. A rede virtual e todos os Serviços Cloud dentro dela serão migrados juntos para o Azure Resource Manager. |
| Rede Virtual | Migração de redes virtuais criadas via Portal (Requer a utilização de "Nome VNet-Nome de grupo de grupo de grupo de grupo de grupo" em ficheiro .cscfg)  | Como parte da migração, o nome da rede virtual no CSCFG será alterado para utilizar o ID do Gestor de Recursos Azure da rede virtual. (subscrição/subscrição-id/grupo de recursos/grupo de recursos-nome/recurso/vnet-name) <br><br>Para gerir a implementação após a migração, atualize a cópia local do ficheiro .cscfg para começar a usar o ID do Gestor de Recursos Azure em vez do nome de rede virtual. <br><br>Um ficheiro .cscfg que utiliza o antigo esquema de nomeação não passará na validação. 
| Rede Virtual | Migração de implantação com funções em diferentes sub-redes. | Um serviço em nuvem com diferentes funções em diferentes sub-redes é suportado para migração. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Recursos e recursos não disponíveis para migração
Estes são os principais cenários envolvendo combinações de recursos, funcionalidades e Serviços Cloud. Esta lista não é exaustiva. 

| Recurso | Próximos passos / work-around | 
|---|---|
| Regras de escala automática | A migração passa, mas as regras são retiradas. [Recrie as regras](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) após a migração em Cloud Services (suporte alargado). | 
| Alertas | A migração passa, mas os alertas são retirados. [Recrie as regras](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) após a migração em Cloud Services (suporte alargado). | 
| Gateway de VPN | Remova o Gateway VPN antes de iniciar a migração e, em seguida, recrie o Gateway VPN uma vez que a migração esteja completa. | 
| Portal da Rota Expressa (apenas na mesma subscrição que a Rede Virtual) | Retire o Gateway da Rota Expressa antes de iniciar a migração e, em seguida, recrie o Gateway uma vez que a migração esteja completa. | 
| Quota  | A quota não é migrada. [Solicite uma nova quota](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) ao Gestor de Recursos Azure antes da migração para que a validação seja bem sucedida. | 
| Grupos de Afinidade | Não suportado. Remova quaisquer grupos de afinidade antes da migração.  | 
| Redes virtuais usando [olhando para a rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)| Antes de migrar uma rede virtual que é espreitada para outra rede virtual, elimine o espreitamento, migrar a rede virtual para o Resource Manager e recriar o espreitamento. Isto pode causar tempo de inatividade dependendo da arquitetura. | 
| Redes virtuais que contêm ambientes de Serviço de Aplicações | Não suportado | 
| Redes virtuais que contêm serviços HDInsight | Não suportado. 
| Redes virtuais que contêm implementações de Gestão API da Azure | Não suportado. <br><br> Para migrar a rede virtual, altere a rede virtual da implementação da API Management. Isto não é uma operação de inatividade. | 
| Circuitos clássicos da Rota Expressa | Não suportado. <br><br>Estes circuitos precisam de ser migrados para O Gestor de Recursos Azure antes de iniciar a migração do PaaS. Para saber mais, consulte os [circuitos Moving ExpressRoute do clássico ao modelo de implementação do Gestor de Recursos.](../expressroute/expressroute-howto-move-arm.md) |  
| Controlo de Acesso Baseado em Funções | Após a migração, o URI das mudanças de recursos das políticas do `Microsoft.ClassicCompute` `Microsoft.Compute` RBAC tem de ser atualizado após a migração. | 
| Gateway de Aplicação | Não suportado. <br><br> Remova o Gateway de Aplicação antes de iniciar a migração e, em seguida, recrie o Gateway de aplicação uma vez que a migração esteja concluída para O Gestor de Recursos Azure | 

## <a name="unsupported-configurations--migration-scenarios"></a>Configurações/cenários de migração não suportados

| Configuração / Cenário  | Próximos passos / work-around | 
|---|---|
| Migração de algumas implementações mais antigas não numa rede virtual | Algumas implementações do Cloud Service que não estão numa rede virtual não são suportadas para migração. <br><br> 1. Utilize a API validada para verificar se a implantação é elegível para migrar. <br> 2. Se elegíveis, as implementações serão transferidas para O Gestor de Recursos Azure sob uma rede virtual com prefixo de "DefaultRdFeVnet" | 
| Migração de implantações que contenham a produção e a colocação de slots de produção utilizando endereços IP dinâmicos | A migração de um Serviço Cloud de duas faixas horárias requer a eliminação da ranhura de paragem. Uma vez eliminada a ranhura de paragem, migrar a ranhura de produção como um Serviço Cloud independente (suporte alargado) no Azure Resource Manager. Em seguida, reimplante o ambiente de preparação como um novo Cloud Service (suporte alargado) e torná-lo permutável com o primeiro. | 
| Migração de implantações que contenham a produção e a colocação de slots de produção utilizando endereços IP reservados | Não suportado. | 
| Migração de produção e implantação de encenação em diferentes redes virtuais|A migração de um serviço de nuvem de dois slot requer a eliminação da ranhura de paragem. Uma vez eliminada a ranhura de paragem, migrar a ranhura de produção como um serviço de nuvem independente (suporte alargado) no Azure Resource Manager. Uma nova implantação de Cloud Services (suporte alargado) pode então ser ligada à implementação migrada com propriedade permutável ativada. Os ficheiros de implementação da antiga colocação de ranhuras de paragem podem ser reutilizados para criar esta nova implementação permutável. | 
| Migração do Serviço de Nuvem Vazio (Serviço cloud sem implantação) | Não suportado. | 
| Migração de implementação contendo o plugin de ambiente de trabalho remoto e as extensões remotas do ambiente de trabalho | Opção 1: Remova o plugin remoto do ambiente de trabalho antes da migração. Isto requer alterações nos ficheiros de implantação. A migração passará então. <br><br> Opção 2: Remover a extensão remota do ambiente de trabalho e migrar a implementação. Após a migração, retire o plugin e instale a extensão. Isto requer alterações nos ficheiros de implantação. <br><br> Retire o plugin e a extensão antes da migração. [Os plugins não são recomendados](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) para utilização em Serviços cloud (suporte alargado).| 
| Redes virtuais com implantação paaS e IaaS |Não suportado <br><br> Mover as implementações paaS ou IaaS para uma rede virtual diferente. Isto vai causar tempo de descanso. | 
Implementações de Serviço de Nuvem utilizando tamanhos de funções legados (tais como Small ou ExtraLarge). | A migração estará concluída, mas os tamanhos das funções serão atualizados para utilizar os tamanhos modernos das funções. Não há alteração de custos ou as propriedades SKU e máquina virtual não serão reiniciadas para esta mudança. Atualize todos os artefactos de implantação para fazer referência a estes novos tamanhos de função modernos. Para mais informações, consulte [os tamanhos VM disponíveis](available-sizes.md)|
| Migração do Serviço cloud para diferentes redes virtuais | Não suportado <br><br> 1. Mover a implantação para uma rede virtual clássica diferente antes da migração. Isto vai causar tempo de descanso. <br> 2. Migrar a nova rede virtual para o Azure Resource Manager. <br><br> Ou <br><br> 1. Migrar a rede virtual para O Gestor de Recursos Azure <br>2. Mover o Serviço cloud para uma nova rede virtual. Isto vai causar tempo de descanso. | 
| Cloud Service em uma rede virtual mas não tem uma sub-rede explícita atribuída | Não suportado. A mitigação envolve mover o papel para uma sub-rede, que requer um reinício de funções (tempo de inatividade) | 


## <a name="post-migration-changes"></a>Alterações pós-migração
A implementação dos Serviços cloud (clássicos) é convertida para uma implementação de Cloud Service (suporte alargado). Consulte a [documentação dos Serviços cloud (suporte alargado)](deploy-prerequisite.md) para obter mais detalhes.  

### <a name="changes-to-deployment-files"></a>Alterações nos ficheiros de implantação 

São feitas pequenas alterações no ficheiro .csdef e .cscfg do cliente para que os ficheiros de implantação estejam em conformidade com os requisitos do Azure Resource Manager e dos Serviços cloud (suporte alargado). A migração pós-migração recupera os seus novos ficheiros de implantação ou atualiza os ficheiros existentes. Isto será necessário para operações de atualização/eliminação.  

- A Rede Virtual utiliza o ID completo do Gestor de Recursos Azure em vez de apenas o nome de recursos na secção DeConfiguration de Rede do ficheiro .cscfg. Por exemplo, `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Para redes virtuais pertencentes ao mesmo grupo de recursos que o serviço de cloud, pode optar por atualizar o ficheiro .cscfg de volta para usar apenas o nome de rede virtual.  

- Tamanhos clássicos como Small, Large, ExtraLarge são substituídos pelos seus novos nomes de tamanho, Standard_A*. Os nomes de tamanho precisam de ser alterados para os seus novos nomes no ficheiro .csdef. Para obter mais informações, consulte [os pré-requisitos de implantação dos Serviços cloud (suporte alargado)](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Utilize a API get the last copy of the deployment files. 
    - Obtenha o modelo usando [Portal,](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal) [PowerShell,](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates) [CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)e [Rest API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 
    - Obtenha o ficheiro .csdef utilizando [a PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) ou [a Rest API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    - Obtenha o ficheiro .cscfg utilizando [a PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) ou [a API de repouso](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Alterações na Automação do cliente, pipeline CI/CD, scripts personalizados, dashboards personalizados, ferramentas personalizadas, etc.  

Os clientes precisam de atualizar as suas ferramentas e automatização para começarem a utilizar os novos APIs/comandos para gerir a sua implementação. O cliente pode facilmente adotar novas funcionalidades e capacidades de Azure Resource Manager/Cloud Services (suporte alargado) como parte desta mudança. 

- Alterações aos nomes do Grupo de Recursos e Recursos após migração
    - Como parte da migração, os nomes de poucos recursos como o Cloud Service, endereços IP públicos, etc. mudam. Estas alterações podem ter de ser refletidas em ficheiros de implementação antes da atualização do Cloud Service. [Saiba mais sobre os nomes dos recursos que mudam.](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration)  

- Recriar regras e políticas necessárias para gerir e escalar serviços em nuvem 
    - [As regras da escala automática](configure-scaling.md) não são migradas. Após a migração, recrie as regras da escala automática.  
    - [Os alertas](enable-alerts.md) não são migrados. Após a migração, recrie os alertas.
    - O Cofre-Chave é criado sem políticas de acesso. [Crie políticas adequadas](../key-vault/general/assign-access-policy-portal.md) no Cofre-Chave para visualizar ou gerir os seus certificados. Os certificados serão visíveis nas definições no separador chamado segredos.

## <a name="next-steps"></a>Passos seguintes
- [Visão geral da migração suportada pela Plataforma de recursos iaas do clássico para o Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrar para serviços em nuvem (suporte alargado) usando o [portal Azure](in-place-migration-portal.md)
- Migrar para serviços em nuvem (suporte alargado) usando [PowerShell](in-place-migration-powershell.md)
