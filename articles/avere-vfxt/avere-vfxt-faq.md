---
title: Perguntas frequentes-avere vFXT para Azure
description: Perguntas frequentes sobre o avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: rohogue
ms.openlocfilehash: 5340952d6d30ae80d53234530a7e2ca6c067cf1f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415345"
---
# <a name="avere-vfxt-for-azure-faq"></a>Perguntas Frequentes de Avere vFXT for Azure

Este artigo responde a perguntas que podem ajudá-lo a decidir se o avere vFXT for Azure é adequado para suas necessidades. Ele fornece informações básicas sobre o avere vFXT e explica como ele funciona com outros componentes do Azure e com produtos de fornecedores externos.

## <a name="general"></a>Geral

### <a name="what-is-avere-vfxt-for-azure"></a>O que é o Avere vFXT for Azure?

O avere vFXT for Azure é um sistema de arquivos de alto desempenho que armazena em cache os dados ativos na computação do Azure para o processamento eficiente de cargas de trabalho críticas.

### <a name="is-avere-vfxt-a-storage-solution"></a>O avere vFXT uma solução de armazenamento?

Não. Avere vFXT é um *cache* de sistema de arquivos que é anexado a ambientes de armazenamento, como seu nas da EMC ou NetApp ou um contêiner de BLOBs do Azure. O avere vFXT simplifica as solicitações de dados de clientes e armazena em cache os dados que ele serve para melhorar o desempenho em escala e ao longo do tempo. O avere vFXT em si não armazena dados. Ele não tem informações sobre a quantidade de dados armazenados por trás dele.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT uma solução de camadas?

Avere vFXT não hierarquiza automaticamente os dados entre as camadas quentes e frias.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Como fazer saber se um ambiente é adequado para avere vFXT?

A melhor maneira de pensar nessa questão é perguntar, "a carga de trabalho é armazenável em cache?" Ou seja, a carga de trabalho tem uma alta taxa de leitura para gravação? Um exemplo é 80/20 ou 70/30 leituras/gravações.

Considere o avere vFXT for Azure se você tiver um pipeline analítico baseado em arquivo executado em um grande número de máquinas virtuais do Azure e atender a uma ou mais das seguintes condições:

* O desempenho geral é lento ou inconsistente devido a tempos de acesso a arquivos longos (dezenas de milissegundos ou segundos, dependendo dos requisitos). Essa latência é inaceitável para o cliente.

* Os dados necessários para o processamento estão localizados na extremidade distante de um ambiente de WAN, e mover esses dados permanentemente é impraticável. Os dados podem estar em uma região do Azure diferente ou em um datacenter do cliente.

* Um número significativo de clientes está solicitando os dados, por exemplo, em um cluster HPC (computação de alto desempenho). O grande número de solicitações simultâneas pode aumentar a latência.

* O cliente deseja executar seu pipeline atual "no estado em que se encontra" em máquinas virtuais do Azure e precisa de uma solução de armazenamento compartilhado (ou cache) com base em POSIX para escalabilidade. Usando o avere vFXT para Azure, você não precisa rearquitetar o pipeline de trabalho para fazer chamadas nativas para o armazenamento de BLOBs do Azure.

* Seu aplicativo HPC é baseado em clientes NFSv3. (Em algumas circunstâncias, ele pode usar clientes SMB 2,1, mas o desempenho é limitado.)

O diagrama a seguir simplifica a resposta para essa pergunta. Quanto mais próximo o fluxo de trabalho estiver no canto superior direito, mais provável será que a solução de cache avere seja adequada para seu ambiente.

![Diagrama mostrando que cargas pesadas de leitura com milhares de clientes são mais adequadas para avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Em qual escala de clientes a solução avere vFXT faz mais sentido?

A solução de cache avere vFXT foi criada para lidar com centenas, milhares ou dezenas de milhares de núcleos de computação. Se você tiver alguns computadores executando um trabalho leve, avere vFXT não será a solução certa.

Os clientes típicos do avere vFXT executam cargas de trabalho exigentes a partir de cerca de 1.000 núcleos de CPU. Esses ambientes podem ser tão grandes quanto 50.000 núcleos ou mais. Como o avere vFXT é escalonável, você pode adicionar nós para dar suporte a essas cargas de trabalho à medida que elas crescem para exigir mais taxa de transferência ou mais IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Qual a quantidade de dados que um ambiente avere vFXT pode armazenar?

Avere vFXT é um cache. Ele não armazena dados especificamente. Ele usa uma combinação de RAM e SSDs para armazenar os dados armazenados em cache. Os dados são armazenados permanentemente em um sistema de armazenamento de back-end (por exemplo, um sistema NAS do NetApp ou um contêiner de BLOB). O sistema avere vFXT não tem informações sobre a quantidade de dados armazenados por trás dele. Avere vFXT armazena apenas em cache o subconjunto dos dados que os clientes solicitam.  

### <a name="what-regions-are-supported"></a>Quais regiões têm suporte?

O avere vFXT para Azure tem suporte em todas as regiões, exceto nas regiões de soberanas (China, Alemanha). Verifique se a região que você deseja usar pode dar suporte à grande quantidade de núcleos de computação e às instâncias de VM necessárias para criar o cluster avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Como fazer obter ajuda com o avere vFXT?

Um grupo de suporte especializado oferece ajuda com o avere vFXT para Azure. Siga as instruções em [obter ajuda com o sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para abrir um tíquete de suporte no portal do Azure.

### <a name="is-avere-vfxt-highly-available"></a>O avere vFXT é altamente disponível?

Sim, avere vFXT é executado exclusivamente como uma solução de alta disponibilidade.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>O avere vFXT for Azure também dá suporte a outros serviços de nuvem?

Sim, os clientes podem usar mais de um provedor de nuvem com o cluster avere vFXT. Ele dá suporte a buckets padrão AWS S3, os buckets padrão do Google Cloud Services e os contêineres de blob do Azure.

> [!NOTE]
> Uma taxa de software se aplica ao uso do avere vFXT no AWS ou Google Cloud, mas não com o Azure.

## <a name="technical-compute"></a>Técnico: computação

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Você pode descrever a aparência de um ambiente avere vFXT?

Avere vFXT é um dispositivo clusterizado composto por várias máquinas virtuais do Azure. Uma biblioteca Python lida com A criação, exclusão e modificação do cluster. Leia [o que é o avere vFXT for Azure?](avere-vfxt-overview.md) para saber mais.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Que tipo de máquinas virtuais do Azure avere vFXT executar?  

Um avere vFXT para o cluster do Azure usa Microsoft Azure máquinas virtuais E32s_v3.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>O ambiente avere vFXT é dimensionado?

O cluster avere vFXT pode ser tão pequeno quanto três nós de máquina virtual ou tão grandes quanto 24 nós. Contate o suporte técnico do Azure para obter ajuda com o planejamento se você acreditar que precisa de um cluster com mais de nove nós. O número maior de nós requer uma arquitetura de implantação maior.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>O ambiente avere vFXT "dimensionamento automático"?

Não. Você pode dimensionar o tamanho do cluster para cima e para baixo, mas adicionar ou remover nós de cluster é uma etapa manual.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Posso executar o cluster avere vFXT como um conjunto de dimensionamento de máquinas virtuais?

Avere vFXT não dá suporte à implantação de um conjunto de dimensionamento de máquinas virtuais. Vários mecanismos internos de suporte de disponibilidade são projetados apenas para VMs atômicas que participam de um cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Posso executar o cluster avere vFXT em VMs de baixa prioridade?

Não, o sistema requer um conjunto estável subjacente de máquinas virtuais.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Posso executar o cluster avere vFXT em contêineres?

Não, avere vFXT deve ser implantado como um aplicativo independente.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>A contagem de VMs avere vFXT está em relação à minha cota de computação?

Sim. Verifique se você tem uma cota suficiente na região para dar suporte ao cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Posso executar as máquinas de cluster avere vFXT em diferentes zonas de disponibilidade?

Não. O modelo de alta disponibilidade no avere vFXT atualmente não dá suporte a membros de cluster avere vFXT individuais localizados em zonas de disponibilidade diferentes.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Posso clonar máquinas virtuais avere vFXT?

Não, você deve usar o script Python com suporte para adicionar ou remover nós no cluster avere vFXT. Para obter mais informações, leia [gerenciar o cluster avere vFXT](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Há uma versão "VM" do software que posso executar em meu próprio ambiente local?

Não, o sistema é oferecido como um dispositivo em cluster e testado em tipos de máquina virtual específicos. Essa restrição ajuda os clientes a evitar a criação de um sistema que não oferece suporte aos requisitos de alto desempenho de um fluxo de trabalho avere vFXT típico.

## <a name="technical-disks"></a>Técnico: discos

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Que tipos de discos têm suporte para as VMs do Azure?

O avere vFXT para Azure pode usar configurações de SSD Premium de 1 TB ou 4 TB. A configuração do SSD Premium pode ser implantada como vários discos gerenciados.

### <a name="does-the-cluster-support-unmanaged-disks"></a>O cluster dá suporte a discos não gerenciados?

Não, o cluster requer discos gerenciados.

### <a name="does-the-system-support-local-attached-ssds"></a>O sistema dá suporte ao SSDs local (anexado)?

No momento, o avere vFXT para Azure não dá suporte ao SSDs local. Os discos usados para avere vFXT devem ser capazes de desligar e reiniciar, mas o SSDs anexado local nessa configuração só pode ser encerrado.

### <a name="does-the-system-support-ultra-ssds"></a>O sistema dá suporte ao ultra SSDs?

Não, o sistema só dá suporte a configurações de SSD Premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Posso desanexar meu SSDs Premium e reanexá-los mais tarde para preservar o conteúdo do cache entre o uso?

Não há suporte para desanexar e reanexar o SSDs. Os metadados ou o conteúdo do arquivo na fonte podem ter sido alterados entre os usos, o que pode causar problemas de integridade de dados.

### <a name="does-the-system-encrypt-the-cache"></a>O sistema criptografa o cache?

Os dados são distribuídos entre os discos, mas não são criptografados. No entanto, os próprios discos podem ser criptografados. Para obter mais informações, consulte [proteger e usar políticas em máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Técnico: rede

### <a name="what-network-is-recommended"></a>Qual rede é recomendada?

Se você estiver usando o armazenamento local com avere vFXT, você deverá ter uma conexão de rede de 1 Gbps ou melhor. Se você tiver uma pequena quantidade de dados e estiver disposto a copiar dados para a nuvem antes de executar trabalhos, a conectividade VPN poderá ser suficiente. 

> [!TIP]
> Quanto mais lento for o link de rede, mais lenta serão as leituras frias iniciais. As leituras lentas aumentam a latência do pipeline de trabalho.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Posso executar avere vFXT em uma rede virtual diferente do meu cluster de computação?

Sim, você pode criar seu sistema avere vFXT em uma rede virtual diferente. Leia [planejar seu sistema avere vFXT](avere-vfxt-deploy-plan.md) para obter detalhes.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>O avere vFXT requer sua própria sub-rede?

Sim. Avere vFXT é executado estritamente como um cluster de alta disponibilidade (HA) e requer vários endereços IP para operar. Se o cluster estiver em sua própria sub-rede, você evitará o risco de conflitos de endereço IP, o que pode causar problemas de instalação e operação normal. A sub-rede do cluster pode estar na rede virtual existente, desde que nenhum endereço IP se sobreponha.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Posso executar avere vFXT no InfiniBand?

Não, avere vFXT usa somente Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Como fazer acessar meu ambiente NAS local do avere vFXT?

O ambiente avere vFXT é como qualquer outra VM do Azure, pois requer acesso roteado por meio de um gateway de rede ou VPN para o datacenter do cliente (e vice-versa). Considere usar a conectividade do Azure ExpressRoute se ela estiver disponível em seu ambiente.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Quais são os requisitos de largura de banda para avere vFXT?

O requisito geral de largura de banda depende de dois fatores:

* A quantidade de dados que estão sendo solicitados da origem
* A tolerância do sistema cliente para latência durante o carregamento de dados inicial  

Para ambientes sensíveis à latência, você deve usar uma solução de fibra com uma velocidade de vínculo mínima de 1 Gbps. Use o ExpressRoute se ele estiver disponível.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Posso executar avere vFXT com endereços IP públicos?

Não, o avere vFXT deve ser operado em um ambiente de rede protegido por meio de práticas recomendadas.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Posso restringir o acesso à Internet da minha rede virtual do cluster?

Em geral, você pode configurar a segurança adicional em sua rede virtual conforme necessário, mas algumas restrições podem interferir na operação do cluster.

Por exemplo, restringir o acesso à Internet de saída de sua rede virtual causa problemas para o cluster, a menos que você também adicione uma regra que permita explicitamente o acesso ao AzureCloud. Essa situação é descrita em [documentação suplementar no GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Para obter ajuda com a segurança personalizada, entre em contato com o suporte conforme descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Técnico: armazenamento de back-end (Filers principais)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>A quantos Filers principais há suporte para um único ambiente de avere vFXT?

Um cluster avere vFXT dá suporte a até 20 Filers principais.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Como o ambiente avere vFXT armazena dados?

Avere vFXT não é armazenamento. É um cache que lê e grava dados de vários destinos de armazenamento chamados de principais Filers. Os dados armazenados em discos SSD Premium no avere vFXT são transitórios e, eventualmente, são liberados para o armazenamento principal do Filer do núcleo de back-end.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Quais Filers principais o avere vFXT dá suporte?

Em termos gerais, o avere vFXT para o Azure dá suporte aos seguintes sistemas como principais Filers:

* Dell EMC Isilon (OneFS 7,1, 7,2, 8,0 e 8,1) 
* NetApp ONTAP (modo clusterizado 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) e (7-modo 7. *, 8.0-8.3)

  > [!NOTE]
  > Não há suporte para Azure NetApp Files no momento.

* Contêineres de blob do Azure (somente armazenamento com redundância local)
* Buckets S3 AWS
* Buckets de nuvem do Google

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Por que a avere vFXT não dá suporte a todos os Filers NFS?

Embora todas as plataformas NFS atendam aos mesmos padrões IETF, na prática cada implementação tem suas próprias sutilezas. Esses detalhes afetam como o avere vFXT interage com o sistema de armazenamento. Os sistemas com suporte são as plataformas mais amplamente usadas no Marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>O avere vFXT dá suporte ao armazenamento de objetos privados (como SwiftStack)?

Avere vFXT não dá suporte ao armazenamento de objetos privados.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Como posso obter um produto de armazenamento específico em suporte?

O suporte é baseado na quantidade de demanda no campo. Se houver solicitações baseadas em receita suficientes para dar suporte a uma solução de NAS, vamos considerá-la. Faça solicitações por meio do suporte do Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Posso usar o armazenamento de BLOBs do Azure como um Filer principal?

Sim, avere vFXT para o Azure pode usar um contêiner de blob de blocos como um Filer do Cloud Core.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Quais são os requisitos de conta de armazenamento para um Filer principal de BLOB?

Sua conta de armazenamento deve ser uma conta de uso geral v2 (GPv2) e configurada apenas para armazenamento com redundância local. Não há suporte para armazenamento com redundância geográfica e armazenamento com redundância de zona.

### <a name="can-i-use-archive-blob-storage"></a>Posso usar o armazenamento de blobs de arquivo?

Não. O SLA (contrato de nível de serviço) para o armazenamento de arquivos não é compatível com as necessidades de acesso de diretório e arquivo em tempo real do sistema avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>Posso usar o armazenamento de BLOBs frio?

O armazenamento de blobs de camada fria geralmente não é recomendado para um avere vFXT para o Azure Core Filer. A camada fria oferece custos de armazenamento menores, mas custos de operações mais altos. (Consulte [preço do blob de blocos](<https://azure.microsoft.com/pricing/details/storage/blobs/>) para obter mais detalhes.) Se os dados forem acessados e modificados ou excluídos com frequência, considere o uso da camada quente.

As [camadas de acesso](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) fornecem mais informações sobre quando pode fazer sentido usar o armazenamento de camada fria como um vFXT Core Filer.

### <a name="how-do-i-encrypt-the-blob-container"></a>Como fazer criptografar o contêiner de BLOB?

Você pode configurar a criptografia de blob no Azure (preferencial) ou no nível do avere vFXT Core Filer.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Posso usar minha própria chave de criptografia para um Filer principal de BLOB?

Por padrão, os dados são criptografados por meio de chaves gerenciadas pela Microsoft para armazenamento de BLOBs, tabelas e filas do Azure, além de arquivos do Azure. Você pode trazer sua própria chave para criptografia para armazenamento de BLOBs e arquivos do Azure. Se você optar por usar a criptografia avere vFXT, deverá usar a chave gerada pelo avere e armazená-la localmente.

## <a name="purchasing"></a>Compra

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Como fazer obter avere vFXT para o licenciamento do Azure?

Obter uma avere vFXT para a licença do Azure é fácil por meio do Azure Marketplace. Inscreva-se em uma conta do Azure e siga as instruções em [implantar o cluster avere vFXT](avere-vfxt-deploy.md) para criar um cluster avere vFXT.

### <a name="how-much-does-avere-vfxt-cost"></a>Quanto custa o avere vFXT?

No Azure, não há nenhuma taxa de licenciamento adicional para usar clusters avere vFXT. Os clientes são responsáveis pelo armazenamento e outras tarifas de consumo do Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>As VMs avere vFXT podem ser executadas como prioridade baixa?

Não, os clusters avere vFXT exigem o serviço "Always on". Os clusters podem ser desativados quando não forem necessários.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o avere vFXT para Azure, leia estes artigos para saber como planejar e implantar seu próprio sistema:

* [Planear o sistema Avere vFXT](avere-vfxt-deploy-plan.md)
* [Descrição geral da implementação](avere-vfxt-deploy-overview.md)
* [Preparar para criar um cluster avere vFXT](avere-vfxt-prereqs.md)
* [Implementar o cluster do vFXT Avere](avere-vfxt-deploy.md)

Para saber mais sobre os recursos e casos de uso para avere vFXT, visite [avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
