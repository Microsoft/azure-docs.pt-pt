---
title: Nódosos e piscinas em Lote Azure
description: Saiba mais sobre os nódosos e piscinas de computação e como são usados num fluxo de trabalho do Lote Azure do ponto de vista do desenvolvimento.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: eadc5236926fed12ebee087f7354c492ae5fc745
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791156"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Nódosos e piscinas em Lote Azure

Num fluxo de trabalho do Lote Azure, um nó de *computação* (ou *nó)* é uma máquina virtual que processa uma parte da carga de trabalho da sua aplicação. Uma *piscina* é uma coleção destes nódosos para a sua aplicação para correr. Este artigo explica mais sobre nós e piscinas, juntamente com considerações ao criá-los e usá-los em um fluxo de trabalho de Lote Azure.

## <a name="nodes"></a>Nós

Um nó é uma máquina virtual Azure (VM) ou vM de serviço em nuvem que se dedica a processar uma parte da carga de trabalho da sua aplicação. O tamanho de um nó determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de ficheiros local que está alocado ao nó.

Pode criar conjuntos de nós do Windows ou Linux com imagens dos Serviços Cloud do Azure, imagens do [Marketplace das Máquinas Virtuais do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) ou imagens personalizadas preparadas por si.

Os nós podem executar qualquer executável ou script que seja suportado pelo ambiente dos respetivos sistemas operativos. Os executíveis ou scripts incluem \* .exe, \* .cmd, \* .bat e PowerShell scripts (para Windows) e binários, shell e Python scripts (para Linux).

Todos os nós de computação do Batch incluem também:

- Uma [estrutura de pastas](files-and-directories.md) padrão e [variáveis de ambiente](jobs-and-tasks.md) associadas que também estão disponíveis para referência por parte das tarefas.
- Definições de **Firewall** que são configuradas para controlar o acesso.
- [Acesso remoto](error-handling.md#connect-to-compute-nodes) para Windows (protocolo RDP (Remote Desktop Protocol)) e nós Linux (Secure Shell (SSH)).

## <a name="pools"></a>Conjuntos

Uma piscina é a coleção de nódosos que a sua aplicação executa.

Conjuntos do Azure Batch criados com base na plataforma de computação principal do Azure. Fornecem a atribuição em larga escala, instalação de aplicações, distribuição de dados, monitorização da saúde e ajuste flexível[(escalação)](#automatic-scaling-policy)do número de nós de cálculo dentro de uma piscina.

Um nome e um endereço IP exclusivos são atribuídos a cada nó que seja adicionado a um conjunto. Quando um nó é removido de um conjunto, as alterações feitas ao sistema operativo ou aos ficheiros perdem-se e o respetivo nome e endereço IP são libertados para utilização futura. Quando um nó deixa um conjunto, a sua duração termina.

Os conjuntos só podem ser utilizados pela conta do Batch em que foram criados. Uma conta de Lote pode criar vários pools para satisfazer os requisitos de recursos das aplicações que irá executar.

A piscina pode ser criada manualmente, ou automaticamente pelo serviço 'Lote', quando especifica o trabalho a fazer. Quando cria um conjunto, pode especificar os seguintes atributos:

- [Sistema operativo do nó e versão](#operating-system-and-version)
- [Tipo de nó e número de alvo de nós](#node-type-and-target)
- [Tamanho do nó](#node-size)
- [Política de escala automática](#automatic-scaling-policy)
- [Política de agendamento de tarefas](#task-scheduling-policy)
- [Estado da comunicação](#communication-status)
- [Iniciar tarefas](#start-tasks)
- [Pacotes de aplicações](#application-packages)
- [Configurar a rede virtual (VNet) e a firewall](#virtual-network-vnet-and-firewall-configuration)
- [Vida útil](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> As contas do Batch têm uma quota predefinida que limita o número de núcleos nas mesmas. O número de núcleos corresponde ao número de nós de computação. Pode encontrar as quotas predefinidas e instruções sobre como [aumentar uma quota](batch-quota-limit.md#increase-a-quota) em [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Quotas e limites para o serviço Azure Batch). Se o seu agrupamento não estiver a obter o número de destino de nós, tal poderá dever-se à quota de núcleos.

## <a name="operating-system-and-version"></a>Sistema operativo e versão

Ao criar uma piscina de Lote, especifice a configuração da máquina virtual Azure e o tipo de sistema operativo que pretende executar em cada nó de computação na piscina.

## <a name="configurations"></a>Configurações

Existem dois tipos de configurações de piscina disponíveis em Batch.

### <a name="virtual-machine-configuration"></a>Configuração da máquina virtual

A **Configuração** da Máquina Virtual especifica que a piscina é composta por máquinas virtuais Azure. Estas VMs podem ser criadas a partir de imagens do Linux ou do Windows.

Quando cria um agrupamento com base na Configuração de Máquina Virtual, tem de especificar não só o tamanho dos nós e a origem das imagens utilizadas para criá-los, como também a **referência da imagem da máquina virtual** e o **SKU do agente de nó** do Batch a instalar nos nós. Para obter mais informações sobre como especificar estas propriedades dos conjuntos, veja [Provision Linux compute nodes in Azure Batch pools (Aprovisionar nós de computação do Linux em conjuntos do Azure Batch)](batch-linux-nodes.md). Opcionalmente, pode anexar um ou mais discos de dados vazio às VMs do agrupamento criadas a partir de imagens do Marketplace ou incluir os discos de dados em imagens personalizadas utilizadas para criar as VMs. Ao incluir discos de dados, é necessário montar e formatar os discos de dentro de um VM para os utilizar.

### <a name="cloud-services-configuration"></a>Configuração de serviços em nuvem

A **Configuração de Serviços cloud** especifica que a piscina é composta por nódos os serviços de nuvem Azure. Cloud Services fornece *apenas*nódosos de computação windows .

Os sistemas operativos disponíveis para os conjuntos de Configuração de Serviços Cloud estão listados em [Azure Guest OS releases and SDK compatibility matrix (Versões de SO Convidado do Azure e matriz de compatibilidade de SDK)](../cloud-services/cloud-services-guestos-update-matrix.md). Quando cria uma piscina que contenha nós de Serviços cloud, precisa especificar o tamanho do nó e a sua *Família OS* (que determina quais as versões de .NET instaladas com o SISTEMA). Os Serviços cloud são implantados para o Azure mais rapidamente do que as máquinas virtuais que executam o Windows. Se quiser agrupamentos de nós de computação do Windows, vai chegar à conclusão de que os Serviços Cloud são mais vantajosos no que diz respeito ao tempo de implementação.

Tal como acontece com as funções de trabalho nos Serviços Cloud, pode especificar uma *Versão de SO* (para obter mais informações sobre as funções de trabalho, consulte a [descrição geral dos Serviços Cloud](../cloud-services/cloud-services-choose-me.md)). Recomendamos que especifique para a versão OS para `Latest (*)` que os nós sejam automaticamente atualizados, e não há nenhum trabalho necessário para atender a versões recém-lançadas. *OS Version* O principal motivo para selecionar uma versão de SO específica é garantir a compatibilidade da aplicação, o que permite fazer testes de retrocompatibilidade antes de permitir a atualização da versão. Após validação, a *versão OS* para o pool pode ser atualizada e a nova imagem de OS pode ser instalada. Quaisquer tarefas de execução serão interrompidas e ressonadas.

### <a name="node-agent-skus"></a>Agente do Nó SKUs

Quando cria um agrupamento, tem de selecionar o **nodeAgentSkuId** adequado, consoante o SO da imagem de base do VHD. Você pode obter um mapeamento de iDs do agente de nó disponível SKU para as suas referências de imagem osso, ligando para a operação do agente de [nó suportado lista SKUs.](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus)

### <a name="custom-images-for-virtual-machine-pools"></a>Imagens personalizadas para agrupamentos de Máquinas Virtuais

Para aprender a criar uma piscina com imagens personalizadas, consulte [Use a Galeria de Imagem Partilhada para criar uma piscina personalizada](batch-sig-images.md).

Em alternativa, pode criar um conjunto personalizado de máquinas virtuais utilizando um recurso de [imagem gerido.](batch-custom-images.md) Para obter informações sobre como preparar imagens do Linux personalizadas a partir de VMs do Azure, veja [Como criar uma imagem de uma máquina virtual ou VHD](../virtual-machines/linux/capture-image.md). Para obter informações sobre como preparar imagens do Windows personalizadas a partir de VMs do Azure, veja [Criar uma imagem gerida de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Suporte de contentor em agrupamentos de Máquinas Virtuais

Ao criar um agrupamento de Configuração de Máquinas Virtuais com as APIs do Batch, pode configurar o agrupamento para executar tarefas nos contentores do Docker. Atualmente, tem de criar o agrupamento através de uma imagem que suporte contentores do Docker. Utilize o Windows Server 2016 Datacenter com a imagem de Contentores do Azure Marketplace ou forneça uma imagem da VM personalizada que inclua o Docker Community Edition ou Enterprise Edition e quaisquer controladores necessários. As definições do agrupamento têm de incluir uma [configuração de contentor](/rest/api/batchservice/pool/add) que copie as imagens do contentor para as VMs quando for criado o agrupamento. As tarefas executadas no agrupamento podem, então, referenciar as imagens e as opções de execução do contentor.

Para obter mais informações, veja [Aplicações de contentor do Run Docker no Azure Batch](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Tipo de nó e alvo

Ao criar uma piscina, pode especificar quais os tipos de nós que deseja e o número de destino para cada um. Os dois tipos de nós são:

- **Nóteos dedicados.** Os nós de computação dedicados estão reservados para as suas cargas de trabalho. São mais dispendiosos do que os nós de baixa prioridade, mas é garantido que nunca serão substituídos.
- **Nódoreos de baixa prioridade.** Os nós de baixa prioridade tiram partido da capacidade excedente do Azure para executar as cargas de trabalho do Batch. Os nós de baixa prioridade são menos caros por hora do que os nós dedicados, e permitem cargas de trabalho que requerem uma potência significativa do cálculo. Para obter mais informações, veja [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Utilizar VMs de baixa prioridade com o Batch).

Os nódos de baixa prioridade podem ser antecipados quando o Azure não tiver uma capacidade excedentária insuficiente. Se um nó for substituído enquanto está a executar tarefas, estas são colocadas novamente na fila e executadas assim que esteja disponível um nó de computação. Os nós de baixa prioridade são uma boa opção para cargas de trabalho em que o tempo de conclusão do trabalho é flexível e este pode ser distribuído por muitos nós. Antes de decidir usar nós de baixa prioridade para o seu cenário, certifique-se de que qualquer trabalho perdido devido à antecipação será mínimo e fácil de recriar.

Pode ter nós de computação de baixa prioridade e dedicados no mesmo conjunto. Cada tipo de nó tem a sua própria definição de alvo, para a qual pode especificar o número desejado de nós.

O número de nós de computação é referido com *destino* porque, em algumas situações, é possível que o seu conjunto não atinja o número de nós pretendido. Por exemplo, um conjunto pode não alcançar o destino se atingir primeiro a [quota de núcleos](batch-quota-limit.md) da sua conta do Batch. Ou, a piscina pode não atingir o alvo se tiver aplicado uma fórmula de escala automática à piscina que limita o número máximo de nós.

Para obter informações sobre preços tanto para nós de baixa prioridade como para os demais, consulte [o Preço do Lote](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Tamanho do nó

Quando cria um conjunto do Azure Batch, pode escolher entre quase todas as famílias e tamanhos de VM disponíveis no Azure. O Azure oferece um leque diversificado de tamanhos de VM para diferentes cargas de trabalho, incluindo tamanhos de VM especializados preparados para [HPC](../virtual-machines/linux/sizes-hpc.md) ou [GPU](../virtual-machines/linux/sizes-gpu.md). 

Para obter mais informações, veja [Escolher um tamanho de VM para nós de computação num conjunto do Azure Batch](batch-pool-vm-sizes.md).

## <a name="automatic-scaling-policy"></a>Política de escala automática

Para cargas de trabalho dinâmicas, pode aplicar uma política de escala automática a uma piscina. O serviço De lote avaliará periodicamente a sua fórmula e ajustará dinamicamente o número de nós dentro da piscina de acordo com a carga de trabalho atual e o uso de recursos do seu cenário de computação. Esta funcionalidade permite-lhe reduzir o custo global de execução da sua aplicação ao utilizar apenas os recursos de que precisa e libertar aqueles de que não precisa.

A ativação do dimensionamento automático é feita ao escrever uma [fórmula de dimensionamento automático](batch-automatic-scaling.md#automatic-scaling-formulas) e associá-la a um conjunto. O serviço Batch utiliza esta fórmula para determinar o número de destino de nós no conjunto para o próximo intervalo de dimensionamento (um intervalo que pode configurar). Pode especificar as definições de dimensionamento automático de um conjunto quando o criar ou ativar o dimensionamento num conjunto mais tarde. Também pode atualizar as definições de dimensionamento num conjunto preparado para dimensionamento.

Como exemplo, talvez um trabalho exija que submeta um grande número de tarefas a executar. Pode atribuir uma fórmula de dimensionamento ao conjunto que ajusta o número de nós no conjunto com base no número atual de tarefas na fila, bem como na taxa de conclusão das tarefas desse trabalho. O serviço Batch avalia periodicamente a fórmula e redimensiona o conjunto com base na carga de trabalho e nas outras definições de fórmula. O serviço adiciona nós conforme necessário quando existe um grande número de tarefas em fila e remove nós quando não existem tarefas em execução ou em fila.

Uma fórmula de dimensionamento pode basear-se nas métricas seguintes:

- **Métricas de tempo**: baseadas em estatísticas recolhidas a cada cinco minutos no número de horas especificado.
- **Métricas de recurso**: baseadas na utilização da CPU, da largura de banda, da memória e no número de nós.
- **Métricas de tarefas**: baseadas no estado da tarefa, como *Ativa* (em fila), *Em Execução* ou *Concluída*.

Quando o dimensionamento automático diminuir o número de nós de computação de um conjunto, tem de pensar como vai processar as tarefas que estão a ser executadas no momento da operação de diminuição. Para acomodar isto, o Batch fornece uma opção de [*deatribuição*](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) de nó que pode incluir nas suas fórmulas. Por exemplo, pode especificar que as tarefas em execução são paradas imediatamente e recolocadas em fila para execução noutro nó ou que podem ser concluídas antes de o nó ser removido do conjunto. Note que a definição da opção de deatribuição do nó como `taskcompletion` ou `retaineddata` impedirá as operações de redimensionamento do pool até que todas as tarefas estejam concluídas, ou todos os períodos de retenção de tarefas tenham expirado, respectivamente.

Para obter mais informações sobre o dimensionamento automático de uma aplicação, consulte [Dimensionar automaticamente nós de computação num conjunto do Azure Batch](batch-automatic-scaling.md).

> [!TIP]
> Para maximizar a utilização de recursos de computação, defina o número de destino de nós para zero no final de uma tarefa, mas permita que as tarefas em execução sejam concluídas.

## <a name="task-scheduling-policy"></a>Política de agendamento de tarefas

A opção de configuração [máximo de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó de computação dentro do conjunto.

A configuração predefinida especifica que uma tarefa seja executada num nó de computação de cada vez, mas existem cenários onde é vantajoso ter duas ou mais tarefas executadas num nó em simultâneo. Veja o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo [Tarefas de nó simultâneas](batch-parallel-node-tasks.md) para saber como tirar partido de várias tarefas por nó.

Também pode especificar um tipo de *preenchimento,* que determina se o Batch espalha as tarefas uniformemente em todos os nós de uma piscina, ou embala cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó.

## <a name="communication-status"></a>Estado da comunicação

Na maioria dos cenários, as tarefas funcionam de forma independente e não têm de comunicar entre si. Contudo, poderão existir algumas aplicações nas quais as tarefas têm de comunicar, como em [cenários de MPI](batch-mpi.md).

Você pode configurar uma piscina para permitir a **comunicação internade** para que os nódos dentro de uma piscina possam comunicar em tempo de execução. Se a comunicação internós estiver ativada, os nós nos conjuntos de Configuração de Serviços Cloud podem comunicar entre si em portas maiores do que a 1.100 e os conjuntos de Configuração de Máquina Virtual não restringem o tráfego em nenhuma porta.

Permitir a comunicação interna também afeta a colocação dos nós dentro dos clusters e pode limitar o número máximo de nós numa piscina devido a restrições de implantação. Se a aplicação não necessitar de comunicação entre nós, o serviço Batch pode alocar um número potencialmente grande de nós ao conjunto a partir de vários clusters e centros de dados diferentes para permitir uma maior potência de processamento paralelo.

## <a name="start-tasks"></a>Iniciar tarefas

Se desejar, pode adicionar uma [tarefa](jobs-and-tasks.md#start-task) inicial que executará em cada nó à medida que o nó se junta à piscina, e cada vez que um nó é reiniciado ou reimagedo. A tarefa inicial é especialmente útil para preparar nós de computação para a execução de tarefas, como instalar as aplicações que as suas tarefas executam nos nós de computação.

## <a name="application-packages"></a>Pacotes de aplicações

Pode especificar pacotes de aplicações para implementar os nós de computação no conjunto. Os pacotes de aplicações fornecem uma implementação simplificada e o controlo de versões das aplicações que as suas tarefas executam. Os pacotes de aplicações que especificou para um conjunto são instalados em cada nó associado a esse conjunto, e sempre que um nó é reiniciado ou sempre que a respetiva imagem for recriada.

Para obter mais informações sobre a utilização de pacotes de aplicações para implementar as aplicações em nós do Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configurar a rede virtual (VNet) e a firewall

Quando aprovisiona um conjunto de nós de computação no Batch, pode associá-lo a uma sub-rede de uma [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) do Azure. Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).  

### <a name="vnet-requirements"></a>Requisitos de VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Para obter mais informações sobre como configurar um agrupamento do Batch numa VNet, veja [Criar um agrupamento de máquinas virtuais com a rede virtual](batch-virtual-network.md).

## <a name="pool-and-compute-node-lifetime"></a>Duração do nó de computação e de conjunto

Quando desenha a sua solução De Lote Azure, deve especificar como e quando as piscinas são criadas, e quanto tempo os nós de computação dentro dessas piscinas são mantidos disponíveis.

Num extremo do espetro, pode criar um conjunto para cada trabalho que submeter e eliminá-lo assim que a execução das respetivas tarefas terminar. Isto maximiza a utilização porque os nós só são atribuídos quando necessário, e são desligados quando estão inativos. Embora isto signifique que o trabalho deve esperar que os nós sejam atribuídos, é importante notar que as tarefas estão agendadas para a execução assim que os nós são individualmente atribuídos e a tarefa inicial está concluída. O Batch *não* espera que todos os nós de um conjunto estejam disponíveis para lhes atribuir tarefas. Assim, garante-se a máxima utilização de todos os nós disponíveis.

No outro extremo do espetro, se a prioridade mais elevada for dar início aos trabalhos, pode criar um conjunto antecipadamente e disponibilizar os respetivos nós antes da submissão dos trabalhos. Neste cenário, as tarefas podem ser iniciadas de imediato, mas os nós podem manter-se inativos enquanto aguardam que as tarefas sejam atribuídas.

Uma abordagem combinada é normalmente usada para manusear uma carga variável, mas contínua. Você pode ter uma piscina em que vários trabalhos são submetidos, e pode escalar o número de nós para cima ou para baixo de acordo com a carga de trabalho. Pode fazer estes ajustes reativamente, com base na carga atual, ou pró-ativamente, se for possível prever a carga. Para mais informações, consulte [a política automática de escalação.](#automatic-scaling-policy)

## <a name="security-with-certificates"></a>Segurança com certificados

Normalmente, tem de utilizar certificados quando encriptar ou desencriptar informações confidenciais relativas a tarefas, como a chave de uma [conta de Armazenamento do Azure](accounts.md#azure-storage-accounts). Para suportar esta situação, pode instalar certificados em nós. Os segredos encriptados são transmitidos para as tarefas através dos parâmetros da linha de comandos ou incorporados num dos recursos da tarefa, sendo que os certificados instalados podem ser utilizados para desencriptá-los.

Utilize a operação [Adicionar certificado](https://docs.microsoft.com/rest/api/batchservice/certificate/add) (REST do Batch) ou o método [CertificateOperations.CreateCertificate](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.certificateoperations) (.NET do Batch) para adicionar um certificado a uma conta do Batch. Em seguida, pode associar o certificado a um conjunto novo ou existente.

Quando um certificado é associado a um conjunto, o serviço Batch instala o certificado em cada nó no conjunto. O serviço Batch instala os certificados apropriados quando o nó começa, antes de lançar quaisquer tarefas (incluindo a tarefa de tarefa de [início](jobs-and-tasks.md#start-task) e de gestor de [emprego).](jobs-and-tasks.md#job-manager-task)

Se adicionar um certificado a uma piscina existente, deve reiniciar os seus nós de computação para que o certificado seja aplicado aos nós.

## <a name="next-steps"></a>Passos seguintes

- Conheça [empregos e tarefas.](jobs-and-tasks.md)
