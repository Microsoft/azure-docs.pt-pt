---
title: FAQ - Avere vFXT para o Azure
description: Perguntas mais frequentes sobre Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: v-erkell
ms.openlocfilehash: 1dda3e379a9dcec9dc48d741c107ee352c4f2033
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404642"
---
# <a name="avere-vfxt-for-azure-faq"></a>Perguntas Frequentes de Avere vFXT for Azure

Este artigo responde às perguntas que podem ajudar a decidir se Avere vFXT para o Azure é ideal para suas necessidades. Ele fornece informações básicas sobre Avere vFXT e explica como ela funciona com outros componentes do Azure e com produtos de fornecedores externos. 

## <a name="general"></a>Geral 

### <a name="what-is-avere-vfxt-for-azure"></a>O que é o Avere vFXT for Azure?

Avere vFXT para o Azure é um sistema de ficheiros de elevado desempenho que armazena em cache dados ativos na computação do Azure para o processamento eficiente de cargas de trabalho críticas.

### <a name="is-avere-vfxt-a-storage-solution"></a>É uma solução de armazenamento de Avere vFXT?

Não. Avere vFXT é um sistema de ficheiros *cache* que anexa a ambientes de armazenamento, como o EMC ou NetApp NAS ou um contentor de Blobs do Azure. Avere vFXT simplifica a pedidos de dados de clientes e armazena em cache os dados que ele serve para melhorar o desempenho em escala e ao longo do tempo. VFXT Avere propriamente dito não armazena dados. Ele não tem informações sobre a quantidade de dados armazenados por trás dele.

### <a name="is-avere-vfxt-a-tiering-solution"></a>É uma solução de camadas de Avere vFXT?

Avere vFXT não automaticamente dados de escalão entre as camadas frequentes e esporádico.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Como posso saber se um ambiente é adequado para Avere vFXT?

A melhor forma de pensar sobre essa pergunta é perguntar, "É a carga de trabalho em cache?" Ou seja, o que a carga de trabalho tem uma alta taxa de leitura-escrita? Um exemplo é 80/20 ou 70/30 leituras/escritas.

Considere Avere vFXT para o Azure, se tem um pipeline de análise baseado em arquivo que seja executado num grande número de máquinas virtuais do Azure e cumpre um ou mais das seguintes condições:

* Geral desempenho é lento ou inconsistente devido a horas de acesso de ficheiro longos (dezenas de milissegundos ou segundos, dependendo dos requisitos). Esta latência não é aceitável para o cliente.

* Dados necessários para processamento estão localizados no final de um ambiente WAN sem dúvida, e mover esses dados permanentemente é impraticável. Os dados podem ter numa região diferente do Azure ou no Centro de dados do cliente.

* Um número significativo de clientes está a solicitar os dados - por exemplo, num cluster de computação de alto desempenho (HPC). O grande número de pedidos simultâneos pode aumentar a latência.

* O cliente deseja executar o pipeline atual "tal como está" em máquinas virtuais do Azure e às suas necessidades com base em POSIX partilhado de armazenamento (ou a colocação em cache) solução para a escalabilidade. Ao utilizar Avere vFXT para o Azure, não terá de rearquitetar o pipeline de trabalho para fazer chamadas nativas para o armazenamento de Blobs do Azure.

* A aplicação de HPC é baseado em clientes NFSv3. (Em algumas circunstâncias, pode utilizar o SMB 2.1 clientes, mas o desempenho é limitado.)

O diagrama seguinte simplifica a resposta para essa pergunta. Quanto mais próximo seu fluxo de trabalho é o canto superior direito, quanto mais provável é que o Avere solução de colocação em cache é mais adequada para seu ambiente.

![diagrama que mostra que as cargas de leitura intensiva, com milhares de clientes são melhores adequada para Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Em que a escala de clientes faz o Avere vFXT solução faz mais sentido?

A solução de cache de vFXT Avere foi concebida para lidar com centenas, milhares ou dezenas de milhares de núcleos de computação. Se tiver algumas máquinas em execução de trabalho leve, Avere vFXT não é a solução certa.

Os clientes de vFXT do típicos Avere executar cargas de trabalho exigentes, a partir de aproximadamente 1.000 núcleos de CPU. Estes ambientes podem ter até 50 000 núcleos ou mais. Uma vez Avere vFXT é dimensionável, pode adicionar nós para suportar estas cargas de trabalho, conforme se desenvolve para exigir mais débito ou IOPS mais.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>A quantidade de dados pode armazenar um ambiente de vFXT Avere?

Avere vFXT é uma cache. Especificamente, ele não armazenar dados. Utiliza uma combinação de RAM e SSDs para armazenar os dados em cache. Os dados são permanentemente armazenados num sistema de armazenamento de back-end (por exemplo, um sistema de NetApp NAS ou um contentor de BLOBs). O sistema de vFXT Avere não tem informações sobre a quantidade de dados armazenados por trás dele. Apenas Avere vFXT armazena em cache o subconjunto dos dados solicitados pelos clientes.  

### <a name="what-regions-are-supported"></a>Que regiões são suportadas?

Avere vFXT para o Azure é suportado em todas as regiões, exceto para as regiões de soberania (China, Alemanha). Certifique-se de que a região que pretende utilizar pode oferecer suporte a grande quantidade de núcleos de computação e as instâncias VM necessárias para criar o cluster de vFXT Avere.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Como posso obter ajuda com Avere vFXT?

Um grupo de suporte especializado oferece ajuda com Avere vFXT para o Azure. Siga as instruções em [obter ajuda com o seu sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para abrir um pedido de suporte do portal do Azure. 

### <a name="is-avere-vfxt-highly-available"></a>É Avere vFXT de elevada disponibilidade?

Sim, é executado Avere vFXT exclusivamente como uma solução HA.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT para o Azure também suporta outros serviços em nuvem?

Sim, os clientes podem utilizar mais do que um fornecedor de cloud com o cluster de vFXT Avere. Ele oferece suporte buckets de padrão de AWS S3, registos de padrão de serviços do Google Cloud e contentores de Blobs do Azure. 

> [!NOTE] 
> Uma taxa de software se aplica a utilizar Avere vFXT no AWS ou o Google Cloud, mas não com o Azure.

## <a name="technical-compute"></a>Técnico: Computação

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Pode descrever que um ambiente de vFXT Avere "aparência"?

Avere vFXT é uma aplicação em cluster feita de várias máquinas virtuais do Azure. Uma biblioteca de Python processa a criação do cluster, eliminação e modificação. Leia [o que é Avere vFXT para o Azure?](avere-vfxt-overview.md) para saber mais. 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Que tipo de máquinas virtuais do Azure é executado Avere vFXT no?  

Um vFXT Avere para cluster do Azure utiliza as máquinas virtuais do Microsoft Azure E32s_v3. 

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>O ambiente de vFXT Avere Dimensionar?

O cluster de vFXT Avere pode ser tão pequena como três nós de máquinas virtuais ou tão grande quanto 24 nós. Contacte o suporte técnico do Azure para obter ajuda com o planejamento de se considerar que precisa de um cluster de mais de nove nós. O grande número de nós requer uma arquitetura de implantação maior.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>É o ambiente de vFXT Avere "dimensionamento automático"?

Não. Pode aumentar e reduzir verticalmente o tamanho do cluster, mas adicionar ou remover nós do cluster é um passo manual.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Pode executar o cluster de vFXT Avere como um conjunto de dimensionamento de máquinas virtuais?

Avere vFXT não suporta a implementação de um conjunto de dimensionamento de máquina virtual. Vários mecanismos de suporte de disponibilidade incorporada destinam-se apenas para VMs atômicas, participando de um cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Pode executar o cluster de vFXT Avere em VMs de baixa prioridade?

Não, o sistema exige um conjunto estável subjacente de máquinas virtuais.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Pode executar o cluster de vFXT Avere nos contentores?

Não, Avere vFXT tem de ser implementado como um aplicativo independente.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Fazer o vFXT Avere VMs são contabilizadas na minha quota de computação?

Sim. Certifique-se de que tem uma quota suficiente na região para suportar o cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Posso executar o vFXT Avere cluster máquinas em zonas de disponibilidade diferentes?

Não. O modelo de elevada disponibilidade no vFXT de Avere atualmente não suporta a membros de cluster vFXT individuais Avere localizados em diferentes zonas de disponibilidade.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Pode clonar máquinas de virtuais de vFXT Avere?

Não, tem de utilizar o script de Python suportado para adicionar ou remover nós do cluster de vFXT Avere. Para obter mais informações, leia [gerir o cluster de vFXT Avere](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Existe uma versão de "VM" do software que pode ser executado no meu próprio ambiente local?

Não, o sistema é oferecido como uma aplicação em cluster e testado em tipos de máquina virtual específica. Esta restrição ajuda os clientes a evitar a criação de um sistema que não suporta os requisitos de alto desempenho de um fluxo de trabalho de vFXT Avere normal. 

## <a name="technical-disks"></a>Técnico: Discos

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Que tipos de discos são suportados para as VMs do Azure?

Avere vFXT para o Azure pode utilizar configurações de SSD premium 1 TB ou 4 TB. A configuração de SSD premium pode ser implementada como vários discos geridos.

### <a name="does-the-cluster-support-unmanaged-disks"></a>O cluster suporta discos não geridos?

Não, o cluster requer discos geridos.

### <a name="does-the-system-support-local-attached-ssds"></a>O sistema suporta o SSDs (ligados) locais?

Avere vFXT para o Azure não suporta atualmente SSD local. Discos utilizados para Avere vFXT tem de ser capazes de encerrar e reiniciar, mas só podem ser terminadas locais SSDs ligados nesta configuração.

### <a name="does-the-system-support-ultra-ssds"></a>O sistema suporta o ultra SSDs?

Não, o sistema suporta apenas o configurações de SSD premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Pode anular a exposição de meu SSDs premium e voltar a ligá-los mais tarde para preservar o conteúdo de cache entre a utilização?

A desanexar e reattaching SSDs não são suportado. Conteúdo de metadados ou ficheiros de origem pode ter sido alterado entre utiliza, o que pode provocar problemas de integridade de dados.

### <a name="does-the-system-encrypt-the-cache"></a>O sistema criptografa o cache?

Os dados estão repartidos por discos, mas não estão encriptados. No entanto, os discos propriamente ditas podem ser encriptados. Para obter mais informações, consulte [políticas de utilização e segura em máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Técnico: Redes

### <a name="what-network-is-recommended"></a>Recomenda-se a rede à qual?

Se estiver a utilizar o armazenamento no local com Avere vFXT, deve ter um 1 Gbps ou melhor conexão de rede. Se tiver uma pequena quantidade de dados e está disposto a copiar dados para a cloud antes de executar tarefas, conectividade VPN pode ser suficiente. 

> [!TIP] 
> Mais lenta será a ligação de rede é, mais devagar será as leituras de frios iniciais. Leituras lentas aumentam a latência do pipeline de trabalho. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Posso executar Avere vFXT numa rede virtual diferente do que o meu cluster de computação?

Sim, pode criar o seu sistema de vFXT Avere numa rede virtual diferente. Leia [planear o seu sistema de vFXT Avere](avere-vfxt-deploy-plan.md) para obter detalhes.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT requer sua própria sub-rede?

Sim. Avere vFXT executa estritamente como um cluster de elevada disponibilidade (HA) e requer vários endereços IP para operar. Se o cluster estiver na sua própria sub-rede, a evitar o risco de conflitos de endereços IP, o que pode causar problemas para instalação e o funcionamento normal. Sub-rede do cluster pode ser dentro da rede virtual existente, desde que a sobreposição de endereços não IP.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Posso executar Avere vFXT em InfiniBand?

Não, Avere vFXT utiliza apenas Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Como posso acessar meu ambiente de no local do Avere vFXT?

O ambiente de vFXT Avere é como qualquer outra VM do Azure requer acesso roteado por meio de um gateway de rede ou VPN para o Centro de dados de cliente (e vice-versa). Considere a utilização de conectividade do ExpressRoute do Azure se estiver disponível no seu ambiente.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Quais são os requisitos de largura de banda para Avere vFXT?

O requisito de largura de banda geral depende de dois fatores: 

* A quantidade de dados que está a ser solicitados pela origem 
* Tolerância do sistema de cliente para latência durante o carregamento de dados iniciais  

Para ambientes sensíveis à latência, deve usar uma solução de fibra com uma velocidade de ligação mínimo de 1 Gbps. Utilize o ExpressRoute, se estiver disponível.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Pode executar Avere vFXT com endereços IP públicos?

Não, o Avere vFXT destina-se a ser operado num ambiente de rede protegido através de práticas recomendadas.  

## <a name="technical-back-end-storage-core-filers"></a>Técnico: Armazenamento de back-end (se filtram os núcleos)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Se filtram de núcleo quantos suporta um único ambiente de vFXT Avere?

Um cluster de vFXT Avere suporta até 20 se filtram de núcleo. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Como é que o ambiente de vFXT Avere armazenar dados?

Avere vFXT não é um armazenamento. É uma cache de que lê e escreve dados de vários destinos de armazenamento chamados se filtram de núcleo. Dados armazenados em discos SSD premium no Avere vFXT for transitórios e, eventualmente, são descarregados para o armazenamento de ficheiros de núcleos de back-end.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Que se core filtram suporta Avere vFXT?

Em termos gerais, Avere vFXT para o Azure suporta os seguintes sistemas como se filtram de núcleos: 

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 e 8.1) 
* NetApp ontap fornece (em cluster modo 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) e (modo de 7 7.*, 8.0 8.3) 

  > [!NOTE] 
  > Os ficheiros do Azure NetApp atualmente não é suportado. 

* Contentores de Blobs do Azure (apenas no armazenamento localmente redundante) 
* Registos de AWS S3 
* Registos de Google Cloud

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Por que motivo o Avere vFXT não suporta todos os se filtram NFS?

Embora todas as plataformas NFS cumprem os padrões da IETF mesmo, na prática, cada implementação tem características próprias. Estes detalhes afetam como Avere vFXT interage com o sistema de armazenamento. Os sistemas com suporte são as plataformas mais amplamente utilizadas no marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT suporta o armazenamento de objeto particular (por exemplo, SwiftStack)?

Avere vFXT não suporta o armazenamento de objeto privado.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Como posso obter um produto de armazenamento específico em suporte?

Suporte baseia-se na quantidade de pedido no campo. Se existirem suficiente pedidos com base em receita para oferecer suporte a uma solução NAS, analisaremos a ele. Efetue pedidos através do suporte do Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Posso utilizar o armazenamento de Blobs do Azure como um filtro de núcleos?

Sim, Avere vFXT para o Azure pode utilizar um contentor de BLOBs de bloco como um filtro de núcleos na cloud.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Quais são os requisitos de conta de armazenamento para um filtro de núcleo de blob?

Sua conta de armazenamento tem de ser um para fins gerais v2 (GPv2) conta e configurados para apenas o armazenamento localmente redundante. Armazenamento georredundante e o armazenamento com redundância de zona não são suportadas.

### <a name="can-i-use-archive-blob-storage"></a>Pode utilizar o armazenamento de blob de arquivo?

Não. O contrato de nível de serviço (SLA) para o armazenamento de arquivo não é compatível com as necessidades de acesso de arquivo do sistema de vFXT Avere e directory em tempo real. 

### <a name="can-i-use-cool-blob-storage"></a>Pode utilizar o armazenamento de BLOBs de acesso esporádico?

Pode utilizar o escalão acesso esporádico, mas tenha em atenção que a taxa de operações será muito superior. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Como posso encriptar o contentor de BLOBs?

Pode configurar a encriptação de BLOBs no Azure (preferidas) ou no nível de filtro de Avere vFXT core.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Posso utilizar minha própria chave de encriptação para um filtro de núcleo de blob?

Por predefinição, os dados são encriptados através de chaves geridas pela Microsoft para armazenamento de Blobs do Azure, tabela e fila, além de ficheiros do Azure. Pode dar a sua própria chave de encriptação para o armazenamento de BLOBs e ficheiros do Azure. Se optar por utilizar a encriptação de vFXT Avere, tem de utilizar a chave gerada pelo Avere e armazená-lo localmente. 

## <a name="purchasing"></a>Compra

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Como posso obter Avere vFXT para licenciamento do Azure?

Publicar um vFXT Avere por licença do Azure é fácil através do Azure Marketplace. Inscreva-se numa conta do Azure e, em seguida, siga as instruções em [implementar o cluster de vFXT Avere](avere-vfxt-deploy.md) para criar um cluster de vFXT Avere. 

### <a name="how-much-does-avere-vfxt-cost"></a>Qual é o custo de vFXT Avere?

No Azure, existe uma taxa de licenciamento adicional para utilizar Avere vFXT clusters. Os clientes são responsáveis pelo armazenamento e outras taxas de consumo do Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Avere vFXT VMs podem ser executadas como de baixa prioridade?

Não, os clusters de vFXT de Avere requerem "sempre ativa" serviço. Os clusters podem ser desligados quando não for necessário. 

## <a name="next-steps"></a>Passos Seguintes

Para obter uma introdução Avere vFXT para o Azure, leia os seguintes artigos para saber como planear e implementar seu próprio sistema:

* [Planear o seu sistema de vFXT Avere](avere-vfxt-deploy-plan.md)
* [Descrição geral da implementação](avere-vfxt-deploy-overview.md)
* [Preparar para criar um cluster de vFXT Avere](avere-vfxt-prereqs.md)
* [Implementar o cluster do vFXT Avere](avere-vfxt-deploy.md)

Para saber mais sobre as funcionalidades e casos de utilização para Avere vFXT, visite [Avere vFXT para o Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
