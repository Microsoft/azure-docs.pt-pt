---
title: Implementação clássica e Resource Manager
description: Descreve as diferenças entre o modelo de implementação Resource Manager e o modelo de implementação clássica (ou Gestão de Serviço).
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: d9c1a27efc1f474e25d0ed164d106e8f64ba3e96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88511410"
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Implementação Azure Resource Manager vs. implementação clássica: Compreender os modelos de implementação e o estado dos seus recursos.

> [!NOTE]
> As informações fornecidas neste artigo destinam-se a ser utilizadas apenas quando migra da implementação clássica para a implementação do Azure Resource Manager.

Neste artigo, vai ficar a saber mais sobre os modelos de implementação Azure Resource Manager e clássica. Estes modelos representam duas formas distingas de implementar e gerir as suas soluções do Azure. Vai trabalhar com estes modelos através de dois conjuntos de APIs diferentes e os recursos implementados podem conter diferenças importantes. Os dois modelos não são compatíveis um com o outro. Este artigo descreve essas diferenças.

Para simplificar a implementação e gestão de recursos, a Microsoft recomenda que utilize o Resource Manager para todos os recursos novos. Se for possível, a Microsoft recomenda que reimplemente os recursos existentes através do Resource Manager.

Se é novo no Gestor de Recursos, pode querer primeiro rever a terminologia definida na visão geral do [Gestor de Recursos Azure](overview.md).

## <a name="history-of-the-deployment-models"></a>Histórico dos modelos de implementação

Originalmente, o Azure oferecia apenas o modelo de implementação clássica. Neste modelo, cada recurso existia independentemente; não havia forma de agrupar os recursos relacionados. Em vez disso, tinha de acompanhar manualmente quais os recursos que compunham a sua solução ou aplicação e não se esquecer de geri-los através de uma abordagem coordenada. Para implementar uma solução, tinha de criar cada recurso individualmente no portal ou de criar um script que implementasse todos os recursos na ordem certa. Para eliminar uma solução, era necessário eliminar cada recurso individualmente. Não poderia aplicar e atualizar facilmente políticas de controlo de acesso para recursos relacionados. Finalmente, não foi capaz de aplicar etiquetas aos recursos para os rotular com termos que o ajudam a monitorizar os seus recursos e a gerir a faturação.

Em 2014, o Azure introduziu o Resource Manager, que acrescentou o conceito de grupo de recursos. Os grupos de recursos são um contentor para os recursos que partilham um ciclo de vida comum. O modelo de implementação Resource Manager oferece várias vantagens:

* Pode implementar, gerir e monitorizar todos os serviços da sua solução como um grupo, em vez de os processar individualmente.
* Pode implementar repetidamente a solução durante o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente.
* Pode aplicar o controlo de acesso a todos os recursos do seu grupo de recursos e essas políticas são aplicadas automaticamente quando são adicionados recursos novos ao grupo de recursos.
* Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.
* Pode utilizar o JavaScript Object Notation (JSON) para definir a infraestrutura da sua solução. O ficheiro JSON é conhecido como modelo do Resource Manager.
* Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

Quando o Resource Manager foi acrescentado, todos os recursos foram adicionados retroativamente a grupos de recursos predefinidos. Se criar um recurso através de uma implementação clássica agora, o recurso é automaticamente criado dentro de um grupo de recursos predefinido para esse serviço, mesmo que não tenha especificado esse grupo de recursos na implementação. No entanto, apenas existir dentro de um grupo de recursos não significa que o recurso tenha sido convertido para o modelo de Gestor de Recursos.

## <a name="understand-support-for-the-models"></a>Compreender o suporte para os modelos

Existem três cenários a ter em consideração:

1. Os Serviços cloud não suportam o modelo de implementação do Gestor de Recursos.
2. As máquinas virtuais, as contas de armazenamento e as redes virtuais suportam tanto o modelo de implementação clássica, como o Resource Manager.
3. Todos os outros serviços do Azure suportam o Resource Manager.

Nas máquinas virtuais, contas de armazenamento e redes virtuais, se o recurso tiver sido criado com a implementação clássica, tem de continuar a trabalhar no mesmo através de operações clássicas. Se a máquina virtual, conta de armazenamento ou rede virtual tiver sido criada através da implementação Resource Manager, tem de continuar a utilizar operações do Resource Manager. Esta distinção pode ser confusa se a sua subscrição contiver uma mistura de recursos criados com ambos os modelos de implementação. Esta combinação de recursos pode criar resultados inesperados porque os recursos não suportam as mesmas operações.

Em alguns casos, um comando do Resource Manager pode obter informações sobre um recurso criado através da implementação clássica ou pode realizar uma tarefa administrativa, tal como mover um recurso clássico para outro grupo de recursos. Mas estes casos não devem dar a impressão de que o tipo suporta as operações do Gestor de Recursos. Por exemplo, suponha que tem um grupo de recursos que contém uma máquina virtual que foi criada com a implementação clássica. Se executar o seguinte comando do PowerShell do Resource Manager:

```powershell
Get-AzResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Devolve a máquina virtual:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

No entanto, o Gestor de Recursos da **Cmdlet Get-AzVM** apenas devolve máquinas virtuais implantadas através do Gestor de Recursos. O seguinte comando não devolve a máquina virtual criada através da implantação clássica.

```powershell
Get-AzVM -ResourceGroupName ExampleGroup
```

Só os recursos criados com o Resource Manager suportam etiquetas. Não se pode aplicar etiquetas a recursos clássicos.

## <a name="changes-for-compute-network-and-storage"></a>Alterações a computação, rede e armazenamento

O diagrama seguinte apresenta recursos de computação, rede e armazenamento implementados através do Resource Manager.

![Arquitetura do Resource Manager](./media/deployment-models/arm_arch3.png)

SRP: Fornecedor de Recursos de Armazenamento, CRP: Fornecedor de Recursos Computacional, NRP: Fornecedor de Recursos de Rede

Repare nas seguintes relações entre os recursos:

* Todos os recursos existem dentro de um grupo de recursos.
* A máquina virtual depende de uma conta de armazenamento específica definida no Fornecedor de recursos de armazenamento para armazenar os respetivos discos no armazenamento de blobs (obrigatório).
* A máquina virtual faz referência a um cartão de interface de rede específico definido no fornecedor de recursos de rede (obrigatório) e um conjunto de disponibilidade definido no fornecedor de recursos Compute (opcional).
* O cartão de interface de rede faz referência ao endereço IP atribuído pela máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (necessária) e a um Grupo de Segurança de Rede (opcional).
* A sub-rede dentro de uma rede virtual referencia um Grupo de Segurança de Rede (opcional).
* A instância do balançador de carga refere-se ao conjunto de backend de endereços IP que incluem o cartão de interface de rede de uma máquina virtual (opcional) e refere um endereço IP público ou privado (opcional).

Seguem-se os componentes e as respetivas relações para a implementação clássica:

![arquitetura clássica](./media/deployment-models/arm_arch1.png)

A solução clássica para alojar uma máquina virtual inclui:

* Um serviço cloud obrigatório que atue como um contentor para o alojamento de máquinas virtuais (computação). As máquinas virtuais são automaticamente fornecidas com um cartão de interface de rede e um endereço IP atribuído pela Azure. Além disso, o serviço cloud contém uma instância do balanceador de carga externa, um endereço IP público e pontos finais predefinidos para permitir o tráfego do ambiente de trabalho remoto e do PowerShell remoto para máquinas virtuais baseadas em Windows e o tráfego Secure Shell (SSH) para máquinas virtuais baseadas em Linux.
* Uma conta de armazenamento necessária que armazena os discos rígidos virtuais para uma máquina virtual, incluindo o sistema operativo, discos de dados temporários e adicionais (armazenamento).
* Uma rede virtual opcional que funciona como um recipiente adicional, no qual pode criar uma estrutura sub-rede e escolher a sub-rede em que a máquina virtual está localizada (rede).

A tabela seguinte descreve as alterações na forma como os fornecedores de Computação, Rede e Armazenamento interagem:

| Item | Clássico | Resource Manager |
| --- | --- | --- |
| Serviço em Nuvem para Máquinas Virtuais |O Serviço em Nuvem era um contentor para manter máquinas virtuais que exigiam Disponibilidade a partir de plataforma e o Balanceamento de Carga. |O Serviço em Nuvem já não é um objeto necessário para criar uma Máquina Virtual com o novo modelo. |
| Redes Virtuais |Uma rede virtual é opcional para a máquina virtual. Se incluída, a rede virtual não pode ser implantada com o Gestor de Recursos. |A máquina virtual requer uma rede virtual que tenha sido implementada com o Resource Manager. |
| Contas de Armazenamento |A máquina virtual requer uma conta de armazenamento que armazena os discos rígidos virtuais para o sistema operativo, discos de dados temporários e adicionais. |A máquina virtual requer uma conta de armazenamento para armazenar os respetivos discos no armazenamento de blobs. |
| Conjuntos de Disponibilidade |A Disponibilidade para a plataforma era indicada configurando o mesmo “AvailabilitySetName” nas Máquinas Virtuais. A contagem máxima de domínios de falhas era 2. |O Conjunto de Disponibilidade é um recurso exposto pelo Fornecedor Microsoft.Compute. As Máquinas Virtuais que requerem elevada disponibilidade têm de ser incluídas no Conjunto de Disponibilidade. A contagem máxima de domínios de falhas é agora 3. |
| Grupos de Afinidade |Os Grupos de Afinidade eram necessários para criar Redes Virtuais. No entanto, com a introdução das Redes Virtuais Regionais, isso já não era necessário. |Para simplificar, o conceito de Grupos de Afinidade não existe nas APIs expostas através do Azure Resource Manager. |
| Balanceamento de Carga |A criação de um Serviço em Nuvem fornece um balanceador de carga implícito para as Máquinas Virtuais implementadas. |O Balanceador de Carga é um recurso exposto pelo fornecedor Microsoft.Network. A interface de rede primária das Máquinas Virtuais que precisam de balanceamento de carga deve mencionar o balanceador de carga. Os Balanceadores de Carga podem ser internos ou externos. Uma instância do balanceador de carga referencia o conjunto de back-end de endereços IP que incluem a NIC de uma máquina virtual (opcional) e um endereço IP público ou privado do balanceador de carga (opcional). |
| Endereço IP Virtual |Os Serviços Cloud obtêm um VIP (Endereço IP Virtual) predefinido quando uma VM é adicionada a um serviço cloud. O Endereço IP Virtual é o endereço associado ao balanceador de carga implícito. |O endereço IP público é um recurso exposto pelo fornecedor Microsoft.Network. O endereço IP público pode ser estático (reservado) ou dinâmico. Os IPs públicos dinâmicos podem ser atribuídos a um Balanceador de Carga. Os IPs Públicos podem ser protegidos utilizando Grupos de Segurança. |
| Endereço IP Reservado |Pode reservar um Endereço IP no Azure e associá-lo a um Serviço em Nuvem para garantir que o Endereço IP é temporário. |O Endereços IP Público pode ser criado no modo estático e oferece a mesma capacidade dos endereços IP reservados. |
| Endereço IP Público (PIP) por VM |O Endereço IP Público também pode ser associado diretamente a uma VM. |O endereço IP público é um recurso exposto pelo fornecedor Microsoft.Network. O Endereço IP Público pode ser estático (reservado) ou dinâmico. |
| Pontos Finais |Os Pontos Finais de Entrada tinham de ser configurados numa Máquina Virtual para estarem abertos à conectividade para determinadas portas. Um dos modos comuns de ligar a máquinas virtuais era configurando pontos finais de entrada. |As Regras NAT de Entrada podem ser configuradas em Balanceadores de Carga para obter a mesma capacidade de ativação de pontos finais em portas específicas para estabelecer ligação às VMs. |
| Nome DNS |Um serviço em nuvem teria de obter um nome DNS implícito globalmente exclusivo. Por exemplo: `mycoffeeshop.cloudapp.net`. |Os Nomes DNS são parâmetros opcionais que podem ser especificados num recurso de Endereço IP Público. O FQDN está no formato `<domainlabel>.<region>.cloudapp.azure.com`. |
| Interfaces de Rede |As Interfaces de Rede Primária e Secundária e as respetivas propriedades eram definidas como configuração de rede de uma Máquina Virtual. |A Interface de Rede é um recurso exposto pelo Fornecedor Microsoft.Network. O ciclo de vida da Interface de Rede não está ligado a uma Máquina Virtual. Referencia o endereço IP atribuído da máquina virtual (obrigatório), a sub-rede da rede virtual da máquina virtual (obrigatório) e um Grupo de Segurança de Rede (opcional). |

Para saber mais sobre como ligar redes virtuais a partir de modelos de implementação diferentes, veja [Connect virtual networks from different deployment models in the portal](../../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) (Ligar redes virtuais a partir de modelos de implementação diferentes no portal).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrar da implementação clássica para Resource Manager

Se estiver pronto para migrar os seus recursos da implantação clássica para a implementação do Gestor de Recursos, consulte:

1. [Technical deep dive on platform-supported migration from classic to Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-deep-dive.md) (Análise detalhada técnica sobre a migração suportada por plataforma da clássica para Azure Resource Manager)
2. [Platform supported migration of IaaS resources from Classic to Azure Resource Manager](../../virtual-machines/windows/migration-classic-resource-manager-overview.md) (Planear a migração suportada de recursos IaaS de Clássica para Azure Resource Manager)
3. [Migrate IaaS resources from classic to Azure Resource Manager by using Azure PowerShell](../../virtual-machines/windows/migration-classic-resource-manager-ps.md) (Migrar recursos IaaS de clássica para Azure Resource Manager com o Azure PowerShell)
4. [Migrate IaaS resources from classic to Azure Resource Manager by using Azure CLI](../../virtual-machines/linux/migration-classic-resource-manager-cli.md) (Migrar recursos IaaS de clássica para Azure Resource Manager com a CLI do Azure)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Poss criar uma máquina virtual com o Resource Manager para implementar numa rede virtual criada com a implementação clássica?**

Esta configuração não é suportada. Não é possível utilizar o Gestor de Recursos para implantar uma máquina virtual numa rede virtual que foi criada usando uma implementação clássica.

**Posso criar uma Máquina Virtual com o Resource Manager a partir de uma imagem de utilizador que tenha sido criada com o modelo de implementação clássica?**

Esta configuração não é suportada. No entanto, é possível copiar os ficheiros de discos em papel virtual a partir de uma conta de armazenamento que foi criada utilizando o modelo clássico de implementação e adicioná-los a uma nova conta criada através do Gestor de Recursos.

**Qual foi o impacto na quota da minha subscrição?**

As quotas das máquinas virtuais, das redes virtuais e das contas de armazenamento criadas com o Azure Resource Manager são separadas das outras quotas. Cada subscrição recebe quotas para criar os recursos com as APIs novas. Pode ler mais sobre as quotas adicionais [aqui](../../azure-resource-manager/management/azure-subscription-service-limits.md).

**Posso continuar a utilizar os meus scripts automatizados para o aprovisionamento de máquinas virtuais, redes virtuais e contas de armazenamento através das APIs do Resource Manager?**

Toda a automatização e os scripts que criou vão continuar a funcionar nas máquinas virtuais e nas redes virtuais existentes criadas no modo Gestão de Serviço do Azure. No entanto, os scripts têm de ser atualizados para utilizar o novo esquema para criarem os mesmos recursos através do modo Azure Resource Manager.

**Onde posso encontrar exemplos de modelos do Azure Resource Manager?**

Pode encontrar um conjunto abrangente de modelos iniciais em [Modelos de Início Rápido do Azure Resource Manager](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Passos seguintes

* Para ver os comandos para implementar um modelo, veja [Deploy an application with Azure Resource Manager template](../templates/deploy-powershell.md) (Implementar uma aplicação com um modelo do Azure Resource Manager).
