---
title: FAQ - Avere vFXT para Azure
description: Utilize estas perguntas frequentes para decidir se Avere vFXT para Azure se adequa às suas necessidades. Saiba como o Avere vFXT para a Azure funciona com outros componentes Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342370"
---
# <a name="avere-vfxt-for-azure-faq"></a>Perguntas Frequentes de Avere vFXT for Azure

Este artigo responde a perguntas que podem ajudá-lo a decidir se Avere vFXT para Azure é adequado para as suas necessidades. Fornece informações básicas sobre o Avere vFXT e explica como funciona com outros componentes Azure e com produtos de fornecedores externos.

## <a name="general"></a>Geral

### <a name="what-is-avere-vfxt-for-azure"></a>O que é o Avere vFXT for Azure?

Avere vFXT for Azure é um sistema de ficheiros de alto desempenho que cache dados ativos no cálculo Azure para um processamento eficiente de cargas de trabalho críticas.

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT é uma solução de armazenamento?

N.º Avere vFXT for Azure é uma *cache* de sistema de ficheiros que se liga a ambientes de armazenamento, como o seu EMC ou NetApp NAS, ou um recipiente de bolhas Azure. Avere vFXT simplifica os pedidos de dados dos clientes, e cache os dados que serve para melhorar o desempenho em escala e ao longo do tempo. Avere vFXT em si não armazena dados. Não tem informações sobre a quantidade de dados armazenados por trás.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT é uma solução de tiering?

Avere vFXT para Azure não tier automaticamente dados entre camadas quentes e frias.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Como sei se um ambiente é adequado para Avere vFXT?

A melhor maneira de pensar sobre esta pergunta é perguntar: "A carga de trabalho é cacheável?" Ou seja, a carga de trabalho tem uma elevada relação leitura-a-escrita? Um exemplo é 80/20 ou 70/30 leituras/escritas.

Considere Avere vFXT para Azure se tiver um gasoduto analítico baseado em ficheiros que atravessa um grande número de máquinas virtuais Azure, e satisfaz uma ou mais das seguintes condições:

* O desempenho geral é lento ou inconsistente devido aos longos tempos de acesso aos ficheiros (dezenas de milissegundos ou segundos, dependendo dos requisitos). Esta latência é inaceitável para o cliente.

* Os dados necessários para o processamento estão localizados na extremidade de um ambiente WAN, e mover esses dados permanentemente é impraticável. Os dados podem estar numa região Azure diferente ou num centro de dados do cliente.

* Um número significativo de clientes está a solicitar os dados - por exemplo, num cluster de computação de alto desempenho (HPC). O grande número de pedidos simultâneos pode aumentar a latência.

* O cliente quer executar o seu pipeline atual "como está" em máquinas virtuais Azure, e precisa de uma solução de armazenamento compartilhado (ou caching) baseada em POSIX para escalabilidade. Ao utilizar o Avere vFXT para o Azure, não é preciso rearquitecte o gasoduto de trabalho para fazer chamadas nativas para o armazenamento do Azure Blob.

* A sua aplicação HPC baseia-se em clientes NFSv3. (Em algumas circunstâncias, pode usar clientes SMB 2.1, mas o desempenho é limitado.)

O diagrama a seguir pode ajudá-lo a responder a esta pergunta. Quanto mais perto o seu fluxo de trabalho estiver do canto superior direito, mais provável é que a solução avere vFXT para a azure seja a solução certa para o seu ambiente.

![Diagrama de gráficos mostrando que cargas pesadas de leitura com milhares de clientes são mais adequadas para Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Em que escala de clientes a solução Avere vFXT faz mais sentido?

A solução de cache Avere vFXT é construída para lidar com centenas, milhares ou dezenas de milhares de núcleos de computação. Se tiver algumas máquinas a trabalhar com luz, avere vFXT não é a solução certa.

Os clientes típicos da Avere vFXT executam cargas de trabalho exigentes a partir de cerca de 1.000 núcleos de CPU. Estes ambientes podem ser tão grandes como 50.000 núcleos ou mais. Como o Avere vFXT é escalável, pode adicionar nós para suportar estas cargas de trabalho à medida que crescem para exigir mais produção ou mais IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Quantos dados pode uma loja de ambiente Avere vFXT?

Avere vFXT para Azure é um cache. Não armazena especificamente dados. Utiliza uma combinação de RAM e SSDs para armazenar os dados em cache. Os dados são armazenados permanentemente num sistema de armazenamento de back-end (por exemplo, um sistema NetApp NAS ou um recipiente blob). O sistema Avere vFXT não dispõe de informação sobre a quantidade de dados armazenados por trás do mesmo. Avere vFXT apenas cache o subconjunto desses dados que os clientes solicitam.  

### <a name="what-regions-are-supported"></a>Que regiões são apoiadas?

Avere vFXT para Azure é apoiado em todas as regiões, exceto em regiões soberanas (China, Alemanha). Certifique-se de que a região que pretende utilizar pode suportar a grande quantidade de núcleos de computação e as instâncias VM necessárias para criar o cluster Avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Como consigo ajuda com Avere vFXT?

Um grupo especializado de pessoal de apoio oferece ajuda com Avere vFXT para a Azure. Siga as instruções em [Obter ajuda com o seu sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para abrir um bilhete de apoio a partir do portal Azure.

### <a name="is-avere-vfxt-highly-available"></a>A Avere vFXT está altamente disponível?

Sim, Avere vFXT funciona exclusivamente como uma solução HA.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>A Avere vFXT para o Azure também suporta outros serviços na nuvem?

Sim, os clientes podem usar mais do que um fornecedor de nuvem com o cluster Avere vFXT. Suporta baldes padrão AWS S3, baldes standard do Google Cloud Services e recipientes blob Azure.

> [!NOTE]
> Uma taxa de software aplica-se para usar Avere vFXT com armazenamento AWS ou Google Cloud. Não existe uma taxa adicional de software para a utilização do armazenamento de bolhas Azure.

## <a name="technical-compute"></a>Técnico: Cálculo

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Pode descrever como é um ambiente Avere vFXT?

Avere vFXT é um aparelho agrupado feito de várias máquinas virtuais Azure. Uma biblioteca Python lida com a criação, supressão e modificação de clusters. Leia [O que é Avere vFXT para Azure?](avere-vfxt-overview.md)

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Que tipo de máquinas virtuais Azure funciona a Avere vFXT?  

Um cluster Avere vFXT para Azure utiliza máquinas virtuais E32s_v3 Microsoft Azure.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>A escala ambiental avere vFXT?

O cluster Avere vFXT pode ser tão pequeno como três nós de máquina virtual ou tão grande como 24 nós. Contacte o suporte técnico da Azure para obter ajuda no planeamento se acreditar que precisa de um aglomerado de mais de nove nós. O maior número de nós requer uma arquitetura de implantação maior.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>O ambiente Avere vFXT "autoscale"?

N.º Pode escalar o tamanho do cluster para cima e para baixo, mas adicionar ou remover nós de cluster é um passo manual.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Posso executar o cluster Avere vFXT como um conjunto de escala de máquina virtual?

Avere vFXT não suporta a implantação de um conjunto de escala de máquina virtual. Vários mecanismos de apoio à disponibilidade incorporados são projetados apenas para VMs atómicos que participam num cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Posso executar o cluster Avere vFXT em VMs de baixa prioridade?

Não, o sistema requer um conjunto estável subjacente de máquinas virtuais.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Posso executar o aglomerado Avere vFXT em contentores?

Não, Avere vFXT deve ser implementado como uma aplicação independente.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Os VMs Avere vFXT contam com a minha quota de cálculo?

Sim. Certifique-se de que tem uma quota suficiente na região para apoiar o cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Posso executar as máquinas de cluster Avere vFXT em diferentes zonas de disponibilidade?

N.º O modelo de alta disponibilidade em Avere vFXT atualmente não suporta membros individuais do cluster Avere vFXT localizados em diferentes zonas de disponibilidade.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Posso clonar máquinas virtuais Avere vFXT?

Não, deve utilizar o script Python suportado para adicionar ou remover nós no cluster Avere vFXT. Para mais informações, leia [Gerir o cluster Avere vFXT](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Existe uma versão "VM" do software que posso executar no meu próprio ambiente local?

Não, o sistema é oferecido como um aparelho agrupado e testado em tipos de máquinas virtuais específicos. Esta restrição ajuda os clientes a evitar a criação de um sistema que não suporta os requisitos de alto desempenho de um fluxo de trabalho típico de Avere vFXT.

## <a name="technical-disks"></a>Técnico: Discos

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Que tipos de discos são suportados para os VMs Azure?

Avere vFXT para Azure pode usar configurações SSD premium 1-TB ou 4-TB. A configuração premium SSD pode ser implementada como vários discos geridos.

### <a name="does-the-cluster-support-unmanaged-disks"></a>O cluster suporta discos não geridos?

Não, o cluster requer discos geridos.

### <a name="does-the-system-support-local-attached-ssds"></a>O sistema suporta SSDs locais (anexados)?

Avere vFXT para Azure não suporta atualmente SSDs locais. Os discos utilizados para Avere vFXT devem ser capazes de desligar e reiniciar, mas os SSDs locais anexados nesta configuração só podem ser terminados.

### <a name="does-the-system-support-ultra-ssds"></a>O sistema suporta ultra SSDs?

Não, o sistema suporta apenas configurações SSD premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Posso desprender os meus SSDs premium e recolocá-los mais tarde para preservar o conteúdo da cache entre a utilização?

A descoloragem e a recolocação dos SSDs não são suportadas. Os metadados ou conteúdos de ficheiros na fonte podem ter mudado entre utilizações, o que pode causar problemas de integridade dos dados.

### <a name="does-the-system-encrypt-the-cache"></a>O sistema encripta a cache?

Os dados são riscados nos discos, mas não são encriptados. No entanto, os próprios discos podem ser encriptados. Para obter mais informações, consulte [Políticas Seguras e utilize políticas em máquinas virtuais em Azure](../virtual-machines/security-policy.md#encryption).

## <a name="technical-networking"></a>Técnico: Networking

### <a name="what-network-is-recommended"></a>Que rede é recomendada?

Se estiver a utilizar o armazenamento no local com o Avere vFXT, deverá ter uma ligação de rede de 1-Gbps ou melhor rede entre o seu armazenamento e o cluster. Se tiver uma pequena quantidade de dados e estiver disposto a copiar dados para a nuvem antes de executar trabalhos, a conectividade VPN pode ser suficiente.

> [!TIP]
> Quanto mais lenta for a ligação de rede, mais lenta será a leitura inicial de "frio". As leituras lentas aumentam a latência do gasoduto de trabalho.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Posso executar Avere vFXT numa rede virtual diferente do meu cluster de computação?

Sim, pode criar o seu sistema Avere vFXT numa rede virtual diferente. Leia [o Plano do seu sistema Avere vFXT](avere-vfxt-deploy-plan.md) para mais detalhes.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>A Avere vFXT necessita da sua própria sub-rede?

Sim. Avere vFXT funciona estritamente como um cluster de alta disponibilidade (HA) e requer vários endereços IP para operar. Se o cluster estiver na sua própria sub-rede, evita-se o risco de conflitos de endereços IP, o que pode causar problemas de instalação e funcionamento normal. A sub-rede do cluster pode estar dentro de uma rede virtual utilizada por outros recursos, desde que nenhum endereço IP se sobreponha.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Posso executar Avere vFXT na InfiniBand?

Não, Avere vFXT usa apenas Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Como acesso ao meu ambiente NAS no local a partir de Avere vFXT?

O ambiente Avere vFXT é como qualquer outro Azure VM, na medida em que requer acesso encaminhado através de uma porta de rede ou VPN para o centro de dados do cliente (e de volta). Considere usar a conectividade Azure ExpressRoute se estiver disponível no seu ambiente.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Quais são os requisitos de largura de banda para Avere vFXT?

O requisito global de largura de banda depende de dois fatores:

* A quantidade de dados solicitados à fonte
* A tolerância do sistema cliente para a latência durante o carregamento inicial de dados  

Para ambientes sensíveis à latência, deve utilizar uma solução de fibra com uma velocidade mínima de ligação de 1 Gbps. Utilize o ExpressRoute se estiver disponível.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Posso executar Avere vFXT com endereços IP públicos?

Não, Avere vFXT deve ser operado num ambiente de rede protegido através das melhores práticas.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Posso restringir o acesso à Internet da rede virtual do meu cluster?

Em geral, pode configurar segurança adicional na sua rede virtual, conforme necessário, mas algumas restrições podem interferir com o funcionamento do cluster.

Por exemplo, restringir o acesso à Internet de saída da sua rede virtual causa problemas para o cluster, a menos que também adicione uma regra que permita explicitamente o acesso ao AzureCloud. Esta situação é descrita em [documentação suplementar sobre o GitHub.](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md)

Para obter ajuda com segurança personalizada, contacte o suporte descrito como descrito no [Get Help with your system](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Técnico: Armazenamento de back-end (filetes principais)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Quantos ficheiros principais suporta um único suporte ambiental Avere vFXT?

Um cluster Avere vFXT suporta até 20 ficheiros centrais.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Como é que o avere vFXT armazena dados de armazenamento?

Avere vFXT não é armazenamento. É uma cache que lê e escreve dados de vários alvos de armazenamento chamados ficheiros principais. Os dados armazenados em discos SSD premium em Avere vFXT são transitórios e são eventualmente lavados para o armazenamento de ficheiros de núcleo back-end.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Quais os ficheiros centrais que o Avere vFXT suporta?

Em termos gerais, o Avere vFXT para a Azure suporta os seguintes sistemas como ficheiros principais:

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 e 8.1) 
* NetApp ONTAP (Modo Agrupado 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) e (7-Mode 7.*, 8.0-8.3)

* Recipientes blob Azure (apenas armazenamento localmente redundante)
* Baldes AWS S3
* Baldes google Cloud

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Porque é que a Avere vFXT não suporta todos os ficheiros NFS?

Apesar de todas as plataformas NFS cumprirem os mesmos padrões IETF, na prática cada implementação tem as suas próprias peculiaridades. Estes detalhes afetam a forma como o Avere vFXT interage com o sistema de armazenamento. Os sistemas suportados são as plataformas mais utilizadas no mercado.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>A Avere vFXT suporta o armazenamento de objetos privados (como o SwiftStack)?

Avere vFXT não suporta armazenamento de objetos privados.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Como posso obter um produto de armazenamento específico sob suporte?

O apoio baseia-se na quantidade de procura no terreno. Se houver pedidos suficientes baseados em receitas para apoiar uma solução NAS, vamos considerá-la. Faça pedidos através do apoio da Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Posso usar o armazenamento do Azure Blob como ficheiro principal?

Sim, Avere vFXT para Azure pode usar um recipiente blob de bloco como um filete de núcleo de nuvem.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Quais são os requisitos da conta de armazenamento para um ficheiro de núcleo blob?

A sua conta de armazenamento deve ser uma conta v2 (GPv2) de uso geral e configurada apenas para armazenamento localmente redundante. O armazenamento geo-redundante e o armazenamento redundante da zona não são suportados.

Leia [o ficheiro central da nuvem de armazenamento Azure Blob](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) para obter mais detalhes sobre os requisitos da conta de armazenamento.

### <a name="can-i-use-archive-blob-storage"></a>Posso usar o armazenamento de bolhas de arquivo?

N.º O acordo de nível de serviço (SLA) para armazenamento de arquivo não é compatível com as necessidades de acesso a ficheiros e de acesso a ficheiros em tempo real do sistema Avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>Posso usar um armazenamento de bolhas legais?

O armazenamento de bolhas de nível frio não é geralmente recomendado para um Avere vFXT para filete de núcleo Azure. O nível cool oferece custos de armazenamento mais baixos, mas custos de operações mais elevados. (Consulte [os preços do blob do bloco](<https://azure.microsoft.com/pricing/details/storage/blobs/>) para mais detalhes.) Se os dados forem acedidos e modificados ou eliminados com frequência, considere a utilização do nível Hot.

[Os níveis de acesso](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier) dão mais informações sobre quando poderá fazer sentido utilizar o armazenamento de nível Cool como um ficheiro de núcleo vFXT.

### <a name="how-do-i-encrypt-the-blob-container"></a>Como criptografo o recipiente de bolhas?

Pode configurar a encriptação blob em Azure (preferencial) ou no nível de ficheiro central Avere vFXT.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Posso usar a minha própria chave de encriptação para um ficheiro de núcleo de bolhas?

Por predefinição, os dados são encriptados através das teclas geridas pela Microsoft para o armazenamento de Azure Blob, Table e Queue, além de Ficheiros Azure. Pode trazer a sua própria chave para encriptação para armazenamento Blob e Ficheiros Azure. Se optar por utilizar a encriptação Avere vFXT, deve utilizar a chave gerada por Avere e armazená-la localmente.

## <a name="purchasing"></a>Compras

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Como consigo avere vFXT para licenciamento Azure?

Obter uma licença Avere vFXT para Azure é fácil através do Azure Marketplace. Inscreva-se numa conta Azure e, em seguida, siga as instruções no [Conjunto Avere vFXT](avere-vfxt-deploy.md) para criar um cluster Avere vFXT.

### <a name="how-much-does-avere-vfxt-cost"></a>Quanto custa Avere vFXT?

Em Azure, não existe uma taxa de licenciamento adicional para a utilização de clusters Avere vFXT. Os clientes são responsáveis pelo armazenamento e outras taxas de consumo da Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Os VMs Avere vFXT podem ser executados como baixa prioridade?

Não, os clusters Avere vFXT requerem um serviço "sempre ligado". Os aglomerados podem ser desligados quando não são necessários.

## <a name="next-steps"></a>Passos seguintes

Para começar com Avere vFXT para a Azure, leia estes artigos para aprender a planear e implementar o seu próprio sistema:

* [Planear o seu sistema Avere vFXT](avere-vfxt-deploy-plan.md)
* [Descrição geral da implementação](avere-vfxt-deploy-overview.md)
* [Prepare-se para criar um cluster Avere vFXT](avere-vfxt-prereqs.md)
* [Implementar o cluster do vFXT Avere](avere-vfxt-deploy.md)

Para saber mais sobre as capacidades e utilizar casos para Avere vFXT, visite [Avere vFXT para Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).