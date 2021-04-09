---
title: Segurança do lote e boas práticas de conformidade
description: Aprenda as melhores práticas e dicas úteis para melhorar a segurança com as suas soluções Azure Batch.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98723817"
---
# <a name="batch-security-and-compliance-best-practices"></a>Segurança do lote e boas práticas de conformidade

Este artigo fornece orientações e boas práticas para aumentar a segurança ao utilizar o Azure Batch.

Por padrão, as contas do Azure Batch têm um ponto final público e são acessíveis ao público. Quando um pool Azure Batch é criado, o pool é alojado numa sub-rede especificada de uma rede virtual Azure. As máquinas virtuais na piscina do Lote são acedidas através de endereços IP públicos que são criados por Batch. Os nós computacional numa piscina podem comunicar uns com os outros quando necessário, tais como executar tarefas de várias instâncias, mas os nós numa piscina não conseguem comunicar com máquinas virtuais fora da piscina.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagrama mostrando um ambiente típico do Lote.":::

Muitas funcionalidades estão disponíveis para ajudá-lo a criar uma implementação mais segura do Azure Batch. Pode restringir o acesso aos nós e reduzir a descoberta dos nós da [internet, disponibilizando a piscina sem endereços IP públicos.](batch-pool-no-public-ip-address.md) Os nós computacional podem comunicar de forma segura com outras máquinas virtuais ou com uma rede no [local, fornecendo a piscina numa sub-rede de uma rede virtual Azure](batch-virtual-network.md). E pode permitir [o acesso privado a partir de redes virtuais](private-connectivity.md) a partir de um serviço alimentado pela Azure Private Link.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagrama mostrando um ambiente de lote mais seguro.":::

## <a name="general-security-related-best-practices"></a>Boas práticas gerais relacionadas com a segurança

### <a name="pool-configuration"></a>Configuração da piscina

Muitas funcionalidades de segurança só estão disponíveis para piscinas configuradas usando [configuração de máquina virtual](nodes-and-pools.md#configurations), e não para piscinas com Configuração de Serviços cloud. Recomendamos a utilização de piscinas de configuração de máquinas virtuais, que utilizam [conjuntos de escala de máquina virtual,](../virtual-machine-scale-sets/overview.md)sempre que possível.

### <a name="batch-account-authentication"></a>Autenticação de conta de lote

O acesso à conta de lote suporta dois métodos de autenticação: Chave Partilhada e [Diretório Ativo Azure (Azure AD)](batch-aad-auth.md).

Recomendamos vivamente a utilização do Azure AD para autenticação da conta Batch. Algumas capacidades do Batch requerem este método de autenticação, incluindo muitas das funcionalidades relacionadas com a segurança discutidas aqui.

### <a name="batch-account-pool-allocation-mode"></a>Modo de atribuição de piscina de conta de lote

Ao criar uma conta Batch, pode escolher entre dois [modos de atribuição de piscinas:](accounts.md#batch-accounts)

- **Serviço de lote**: A opção predefinida, onde o Serviço de Nuvem subjacente ou a escala de máquina virtual definem os recursos utilizados para alocar e gerir os nós de piscina são criados em subscrições internas, e não são diretamente visíveis no portal Azure. Apenas as piscinas e nós do Lote são visíveis. 
- **Subscrição do utilizador**: Os recursos conjuntos de escala de cloud subjacentes ou de escala de máquina virtual são criados na mesma subscrição que a conta Batch. Estes recursos são, portanto, visíveis na subscrição, para além dos recursos correspondentes do Lote.

Com o modo de subscrição do utilizador, os VMs do Lote e outros recursos são criados diretamente na sua subscrição quando um pool é criado. O modo de subscrição do utilizador é necessário se pretender criar pools de Lote utilizando instâncias VM reservadas Azure, utilizar a Política Azure em recursos definidos em escala de máquina virtual e/ou gerir a quota principal na subscrição (partilhada em todas as contas Batch na subscrição). Para criar uma conta do Batch no modo de subscrição de utilizador, também tem de registar a sua subscrição no Azure Batch e associar a conta ao Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Restringir o acesso ao ponto final da rede

### <a name="batch-network-endpoints"></a>Pontos finais da rede de lote

Esteja ciente de que, por padrão, os pontos finais com endereços IP públicos são usados para comunicar com contas Batch, piscinas de lote e nós de piscina.

### <a name="batch-account-api"></a>Conta de lote API

 Quando uma conta Batch é criada, é criado um ponto final público que é usado para invocar a maioria das operações para a conta usando uma [API REST](/rest/api/batchservice/). O ponto final da conta tem um URL base utilizando o formato `https://{account-name}.{region-id}.batch.azure.com` . O acesso à conta Batch é assegurado, com a comunicação ao ponto final da conta a ser encriptada através do HTTPS, e cada pedido autenticado utilizando a autenticação da chave partilhada ou do Azure Ative Directory (Azure AD).

### <a name="azure-resource-manager"></a>Azure Resource Manager

Além de operações específicas de uma conta Batch, [as operações de gestão](/rest/api/batchmanagement/) aplicam-se a contas individuais e múltiplas do Batch. Estas operações de gestão são acedidas através do Azure Resource Manager.

As operações de gestão de lotes via Azure Resource Manager são encriptadas utilizando HTTPS, e cada pedido é autenticado usando a autenticação AZure AD.

### <a name="batch-pool-nodes"></a>Nódoas de piscina de lote

O serviço Batch comunica com um agente de nó de lote que funciona em cada nó na piscina. Por exemplo, o serviço instrui o agente de nó a executar uma tarefa, a parar uma tarefa ou a obter os ficheiros para uma tarefa. A comunicação com o agente de nó é ativada por um ou mais balançadores de carga, o número dos quais depende do número de nós numa piscina. O equilibrador de carga encaminha a comunicação para o nó desejado, com cada nó a ser abordado por um número de porta único. Por predefinição, os equilibradores de carga têm endereços IP públicos associados a eles. Também pode aceder remotamente aos nódos de piscina via RDP ou SSH (este acesso é ativado por predefinição, com comunicação através de balançadores de carga).

### <a name="restricting-access-to-batch-endpoints"></a>Restringir o acesso aos pontos finais do Lote 

Várias capacidades estão disponíveis para limitar o acesso aos vários pontos finais do Batch, especialmente quando a solução utiliza uma rede virtual. 

#### <a name="use-private-endpoints"></a>Utilizar pontos finais privados

[O Azure Private Link](../private-link/private-link-overview.md) permite o acesso aos Serviços Azure PaaS e aos serviços de parceiros do Azure PaaS hospedados num ponto final privado na sua rede virtual. Pode utilizar o Private Link para restringir o acesso a uma conta Batch dentro da rede virtual ou a partir de qualquer rede virtual. Os recursos mapeados para private link também são acessíveis no local através de peering privado através de VPN ou Azure ExpressRoute.

Para utilizar pontos finais privados, uma conta Batch deve ser configurada adequadamente quando criada; a configuração de acesso à rede pública deve ser desativada. Uma vez criados, os pontos finais privados podem ser criados e associados à conta Batch. Para obter mais informações, consulte [utilizar pontos finais privados com contas Azure Batch](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Criar piscinas em redes virtuais

Os nós computacional num pool de Lote podem comunicar uns com os outros, tais como executar tarefas de várias instâncias, sem necessitar de uma rede virtual (VNET). No entanto, por padrão, os nós numa piscina não podem comunicar com máquinas virtuais que estão fora da piscina numa rede virtual e têm endereços IP privados, como servidores de licenças ou servidores de ficheiros.

Para permitir que os nós computacional se comuniquem de forma segura com outras máquinas virtuais, ou com uma rede no local, pode configurar uma piscina para estar numa sub-rede de um Azure VNET.

Quando as piscinas têm pontos finais IP públicos, a sub-rede deve permitir que a comunicação de entrada do serviço Batch seja capaz de agendar tarefas e executar outras operações nos nós de computação, e comunicação de saída para comunicar com o Azure Storage ou outros recursos conforme necessário pela sua carga de trabalho. Para piscinas na configuração da Máquina Virtual, o Batch adiciona grupos de segurança de rede (NSGs) ao nível da interface de rede ligado aos nós de computação. Estes NSGs têm regras que permitem:

- Tráfego TCP de entrada a partir de endereços IP de serviço de lote
- Tráfego TCP de entrada para acesso remoto
- Tráfego de saída em qualquer porta para a rede virtual (pode ser alterado de acordo com as regras NSG de nível de sub-rede)
- Tráfego de saída em qualquer porta para a internet (pode ser alterado de acordo com as regras NSG de nível sub-rede)

Não é preciso especificar NSGs ao nível da sub-rede de rede virtual, porque o Batch configura os seus próprios NSGs. Se tiver um NSG associado à sub-rede onde os nós computacional do Batch são implantados, ou se quiser aplicar regras NSG personalizadas para anular os predefinidos aplicados, deve configurar este NSG com, pelo menos, as regras de segurança de entrada e saída, de modo a permitir a comunicação do serviço Batch aos nós da piscina e comunicação do nó de piscina para a Azure Storage.

Para obter mais informações, consulte [criar uma piscina Azure Batch numa rede virtual.](batch-virtual-network.md)

#### <a name="create-pools-with-static-public-ip-addresses"></a>Criar piscinas com endereços IP públicos estáticos

Por predefinição, os endereços IP públicos associados a piscinas são dinâmicos; são criados quando uma piscina é criada e os endereços IP podem ser adicionados ou removidos quando uma piscina é redimensionada. Quando as aplicações de tarefa em execução em nós de piscina precisam de aceder a serviços externos, o acesso a esses serviços pode ter de ser restringido a IPs específicos.  Neste caso, não será gerível ter endereços IP dinâmicos.

Pode criar recursos de endereço IP públicos estáticos na mesma subscrição que a conta Batch antes da criação do pool. Em seguida, pode especificar estes endereços ao criar a sua piscina.

Para obter mais informações, consulte [criar um pool Azure Batch com endereços IP públicos especificados.](create-pool-public-ip.md)

#### <a name="create-pools-without-public-ip-addresses"></a>Criar piscinas sem endereços IP públicos

Por predefinição, todos os nós de computação num conjunto de configuração de máquina virtual Azure Batch são atribuídos a um ou mais endereços IP públicos. Estes pontos finais são utilizados pelo serviço Batch para agendar tarefas e para comunicação com nós de computação, incluindo acesso de saída à internet.

Para restringir o acesso a estes nós e reduzir a descoberta destes nós a partir da internet, pode providenciar a piscina sem endereços IP públicos.

Para mais informações, consulte [Criar uma piscina sem endereços IP públicos.](batch-pool-no-public-ip-address.md)

#### <a name="limit-remote-access-to-pool-nodes"></a>Limite o acesso remoto aos nosmes da piscina

Por predefinição, o Batch permite que um utilizador de nó com conectividade de rede se conecte externamente a um nó de computação num pool de Lote utilizando RDP ou SSH.

Para limitar o acesso remoto aos nóns, utilize um dos seguintes métodos:

- Configure a [Configuração poolEndpoint](/rest/api/batchservice/pool/add#poolendpointconfiguration) para negar o acesso. O grupo de segurança de rede apropriado (NSG) será associado à piscina.
- Crie a sua piscina [sem endereços IP públicos.](batch-pool-no-public-ip-address.md) Por padrão, estas piscinas não podem ser acedidas fora do VNet.
- Associe um NSG ao VNet para negar o acesso às portas RDP ou SSH.
- Não crie utilizadores no nó. Sem nenhum nó, o acesso remoto não será possível.

## <a name="encrypt-data"></a>Encriptar dados

### <a name="encrypt-data-in-transit"></a>Encriptar dados em trânsito

Toda a comunicação para o ponto final da conta Batch (ou via Azure Resource Manager) deve utilizar HTTPS. Tem de utilizar `https://` nos URLs da conta Batch especificados em APIs quando se ligar ao serviço Batch.

Os clientes que comunicam com o serviço Batch devem ser configurados para utilizar a Segurança da Camada de Transporte (TLS) 1.2.

### <a name="encrypt-batch-data-at-rest"></a>Encripte os dados do Lote em repouso

Algumas das informações especificadas nas APIs do lote, tais como certificados de conta, metadados de trabalho e de tarefa, e linhas de comando de tarefa, são automaticamente encriptadas quando armazenadas pelo serviço Batch. Por padrão, estes dados são encriptados utilizando teclas geridas pela plataforma Azure Batch únicas em cada conta Batch.

Também pode encriptar estes dados utilizando [as teclas geridas pelo cliente](batch-customer-managed-key.md). [O Azure Key Vault](../key-vault/general/overview.md) é utilizado para gerar e armazenar a chave, com o identificador chave registado na sua conta Batch.

### <a name="encrypt-compute-node-disks"></a>Criptografe discos de nó de computação

Os nós computacional do lote têm dois discos por padrão: um disco DE e o SSD temporário local. [Os ficheiros e diretórios geridos](files-and-directories.md) pelo Batch estão localizados no SSD temporário, que é a localização padrão para ficheiros como ficheiros de saída de tarefas. As aplicações de tarefas do lote podem utilizar a localização predefinida no SSD ou no disco OS.

Para uma segurança extra, criptografe estes discos utilizando uma destas capacidades de encriptação do disco Azure:

- [Encriptação gerida do disco em repouso com teclas geridas pela plataforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Encriptação no hospedeiro usando uma chave gerida pela plataforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Serviços de acesso seguro a partir de nóns de computação

Os nós de lote podem [aceder de forma segura a credenciais e segredos armazenados](credential-access-key-vault.md) no [Azure Key Vault](../key-vault/general/overview.md), que podem ser usados por aplicações de tarefas para aceder a outros serviços. Um certificado é usado para conceder aos nós da piscina acesso ao Key Vault.

## <a name="governance-and-compliance"></a>Governação e conformidade

### <a name="compliance"></a>Conformidade

Para ajudar os clientes a cumprirem as suas próprias obrigações de conformidade em indústrias e mercados regulamentados em todo o mundo, a Azure mantém um [grande portfólio de ofertas de conformidade.](https://azure.microsoft.com/overview/trusted-cloud/compliance) 

Estas ofertas baseiam-se em vários tipos de garantias, incluindo certificações formais, atestados, validações, autorizações e avaliações produzidas por empresas independentes de auditoria de terceiros, bem como alterações contratuais, autoavaliações e documentos de orientação do cliente produzidos pela Microsoft. Reveja a [visão geral abrangente das ofertas de conformidade](https://aka.ms/AzureCompliance) para determinar quais podem ser relevantes para as suas soluções Batch.

### <a name="azure-policy"></a>Azure Policy

[A Azure Policy](../governance/policy/overview.md) ajuda a impor normas organizacionais e a avaliar o cumprimento em escala. Os casos de utilização comum para a Política Azure incluem a implementação da governação para a consistência dos recursos, a conformidade regulamentar, a segurança, os custos e a gestão.

Dependendo do modo de atribuição de piscinas e dos recursos a que uma política deve ser aplicada, utilize a Política Azure com o Batch de uma das seguintes formas:

- Diretamente, utilizando o recurso Microsoft.Batch/batchAccounts. Um subconjunto das propriedades de uma conta Batch pode ser usado. Por exemplo, a sua política pode incluir regiões de conta de Lote válidas, modo de atribuição de piscinas permitido, e se uma rede pública está habilitada para contas.
- Indiretamente, utilizando o recurso Microsoft.Compute/virtualMachineScaleSets. As contas de lote com o modo de atribuição de pool de subscrição do utilizador podem ter a política definida na escala virtual de recursos definidos na escala de máquinas que são criados na subscrição da conta Batch. Por exemplo, tamanhos de VM permitidos e certifique-se de que certas extensões são executadas em cada nó de piscina.

## <a name="next-steps"></a>Passos seguintes

- Reveja a [linha de base de segurança Azure para o Lote](security-baseline.md).
- Leia mais [boas práticas para O Lote Azure.](best-practices.md)