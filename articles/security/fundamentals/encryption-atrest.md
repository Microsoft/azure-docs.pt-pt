---
title: Encriptação de dados do Microsoft Azure em repouso  Microsoft Docs
description: Este artigo fornece uma visão geral da encriptação de dados do Microsoft Azure no descanso, as capacidades globais e considerações gerais.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/07/2020
ms.author: barclayn
ms.openlocfilehash: 682f0b66f7632bce16ae134e71ea27c4df976f43
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243396"
---
# <a name="azure-data-encryption-at-rest"></a>Encriptação de dados azure-at-rest

O Microsoft Azure inclui ferramentas para salvaguardar dados de acordo com as necessidades de segurança e conformidade da sua empresa. Este artigo centra-se em:

- Como os dados são protegidos em repouso através do Microsoft Azure
- Discute os vários componentes que participam na implementação da proteção de dados,
- Analise os prós e os contras das diferentes abordagens de proteção de gestão chave.

A encriptação em Rest é um requisito de segurança comum. No Azure, as organizações podem encriptar dados em repouso sem o risco ou o custo de uma solução de gestão de chaves personalizadas. As organizações têm a opção de deixar o Azure gerir completamente a Encriptação em Rest. Além disso, as organizações têm várias opções para gerir de perto chaves de encriptação ou encriptação.

## <a name="what-is-encryption-at-rest"></a>O que é encriptação em repouso?

A encriptação em Rest é a codificação (encriptação) dos dados quando é persistida. Os designs de encriptação em Rest em Azure usam encriptação simétrica para encriptar e desencriptar grandes quantidades de dados rapidamente de acordo com um modelo conceptual simples:

- Uma chave de encriptação simétrica é usada para encriptar dados tal como está escrito para armazenamento.
- A mesma chave de encriptação é usada para desencriptar esses dados à medida que são corrigidos para serem usados na memória.
- Os dados podem ser divididos e podem ser utilizadas teclas diferentes para cada partição.
- As chaves devem ser armazenadas num local seguro com políticas de controlo de acesso e auditoria baseadas na identidade. As chaves de encriptação de dados são frequentemente encriptadas com uma chave de encriptação no Cofre de Chaves Azure para limitar ainda mais o acesso.

Na prática, os principais cenários de gestão e controlo, bem como garantias de escala e disponibilidade, requerem construções adicionais. Os conceitos e componentes de encriptação Microsoft Azure at Rest são descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>O propósito da encriptação em repouso

A encriptação em repouso fornece proteção de dados para dados armazenados (em repouso). Os ataques contra dados em repouso incluem tentativas de acesso físico ao hardware em que os dados são armazenados e, em seguida, comprometer os dados contidos. Em tal ataque, o disco rígido de um servidor pode ter sido mal tratado durante a manutenção, permitindo que um intruso removesse o disco rígido. Mais tarde, o agressor colocaria o disco rígido num computador sob o seu controlo para tentar aceder aos dados.

A encriptação em repouso foi concebida para impedir que o intruso aceda aos dados não encriptados, garantindo que os dados são encriptados quando estão em disco. Se um intruso obtiver um disco rígido com dados encriptados, mas não com as chaves de encriptação, o intruso deve derrotar a encriptação para ler os dados. Este ataque é muito mais complexo e consumista do que aceder a dados não encriptados num disco rígido. Por esta razão, a encriptação em repouso é altamente recomendada e é um requisito de alta prioridade para muitas organizações.

A encriptação em repouso também pode ser exigida pela necessidade de uma organização para a governação de dados e os esforços de conformidade. Regulamentos da indústria e do governo, tais como HIPAA, PCI e FedRAMP, estabelecem salvaguardas específicas no que diz respeito à proteção de dados e requisitos de encriptação. A encriptação em repouso é uma medida obrigatória necessária para o cumprimento de alguns desses regulamentos.

Além de satisfazer os requisitos de conformidade e regulação, a encriptação em repouso proporciona proteção de defesa aprofundada. O Microsoft Azure fornece uma plataforma compatível para serviços, aplicações e dados. Fornece igualmente instalações abrangentes e segurança física, controlo de acesso a dados e auditoria. No entanto, é importante fornecer medidas de segurança adicionais "sobrepostas" no caso de uma das outras medidas de segurança falhar e a encriptação em repouso fornecer tal medida de segurança

A Microsoft está empenhada em encriptar opções de repouso em serviços na nuvem e dar aos clientes o controlo de chaves de encriptação e registos de utilização chave. Além disso, a Microsoft está a trabalhar para encriptar todos os dados dos clientes em repouso por padrão.

## <a name="azure-encryption-at-rest-components"></a>Encriptação Azure nos componentes de repouso

Como descrito anteriormente, o objetivo de encriptação em repouso é que os dados que são persistidos no disco são encriptados com uma chave de encriptação secreta. Para atingir esse objetivo, deve ser fornecida a criação, armazenamento, controlo de acesso e gestão das chaves de encriptação. Embora os detalhes possam variar, as implementações de encriptação de serviços Azure no Rest podem ser descritas em termos ilustrados no diagrama seguinte.

![Componentes](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A localização de armazenamento das chaves de encriptação e o controlo de acesso a essas teclas é central para uma encriptação no modelo de repouso. As chaves devem ser altamente seguras, mas geríveis por utilizadores especificados e disponíveis para serviços específicos. Para os serviços Azure, o Azure Key Vault é a solução de armazenamento chave recomendada e proporciona uma experiência de gestão comum entre serviços. As chaves são armazenadas e geridas em cofres chave, e o acesso a um cofre chave pode ser dado aos utilizadores ou serviços. O Azure Key Vault suporta a criação de chaves ou a importação de chaves para uso no cliente em cenários-chave de encriptação geridos pelo cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

As permissões para utilizar as chaves armazenadas no Cofre de Chaves Azure, quer para gerir quer para aceder às mesmas para encriptação em Rest encriptação e desencriptação, podem ser dadas às contas do Diretório Ativo do Azure.

### <a name="key-hierarchy"></a>Hierarquia-chave

Mais de uma chave de encriptação é usada numa encriptação em repouso. Armazenar uma chave de encriptação no Cofre de Chaves Azure garante o acesso seguro à chave e a gestão central das teclas. No entanto, o acesso local de serviço às chaves de encriptação é mais eficiente para encriptação a granel e desencriptação do que interagir com o Key Vault para cada operação de dados, permitindo uma encriptação mais forte e um melhor desempenho. Limitar a utilização de uma única chave de encriptação diminui o risco de a chave ser comprometida e o custo da reencriptação quando uma chave deve ser substituída. As encriptações azure nos modelos de repouso utilizam uma hierarquia-chave composta dos seguintes tipos de teclas para responder a todas estas necessidades:

- **Chave de encriptação de dados (DEK)** – Uma chave Simétrica AES256 utilizada para encriptar uma partilha ou bloco de dados.  Um único recurso pode ter muitas divisórias e muitas Chaves de Encriptação de Dados. Encriptar cada bloco de dados com uma tecla diferente torna os ataques de análise de cripto mais difíceis. O acesso aos DEKs é necessário pelo fornecedor de recursos ou pela instância de aplicação que está a encriptar e a desencriptar um bloco específico. Quando um DEK é substituído por uma nova chave apenas os dados do seu bloco associado devem ser novamente encriptados com a nova chave.
- **Chave de encriptação (KEK)** – Uma chave de encriptação utilizada para encriptar as Teclas de encriptação de dados. A utilização de uma chave de encriptação que nunca sai do Key Vault permite que as chaves de encriptação de dados sejam encriptadas e controladas. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma entidade pode intermediar o acesso ao DEK para limitar o acesso de cada DEK a uma partição específica. Uma vez que o KEK é obrigado a desencriptar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente eliminados por eliminação do KEK.

As Chaves de Encriptação de Dados, encriptadas com as Chaves de Encriptação de Chaves são armazenadas separadamente e apenas uma entidade com acesso à Chave de Encriptação pode desencriptar estas Chaves de Encriptação de Dados. São suportados diferentes modelos de armazenamento chave. Discutiremos cada modelo mais detalhadamente mais tarde na próxima secção.

## <a name="data-encryption-models"></a>Modelos de encriptação de dados

A compreensão dos vários modelos de encriptação e dos seus prós e contras é essencial para entender como os vários fornecedores de recursos em Azure implementam encriptação em Rest. Estas definições são partilhadas em todos os fornecedores de recursos em Azure para garantir a linguagem e a taxonomia comuns.

Existem três cenários para encriptação do lado do servidor:

- Encriptação do lado do servidor usando chaves geridas pelo serviço
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Microsoft gere as chaves
  - Funcionalidade de nuvem completa

- Encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre de Chaves Azure
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Cliente controla chaves via Cofre chave Azure
  - Funcionalidade de nuvem completa

- Encriptação do lado do servidor usando chaves geridas pelo cliente em hardware controlado pelo cliente
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Cliente controla chaves em hardware controlado pelo cliente
  - Funcionalidade de nuvem completa

Para encriptação do lado do cliente, considere o seguinte:

- Os serviços do Azure não conseguem ver dados desencriptados
- Os clientes gerem e armazenam chaves no local (ou noutras lojas seguras). As chaves não estão disponíveis para os serviços do Azure
- Funcionalidade reduzida da nuvem

Os modelos de encriptação suportados em Azure dividiram-se em dois grupos principais: "Encriptação do cliente" e "Encriptação do lado do servidor", como mencionado anteriormente. Independentemente da encriptação no modelo de repouso utilizado, os serviços Azure recomendam sempre a utilização de um transporte seguro, como TLS ou HTTPS. Por conseguinte, a encriptação nos transportes deve ser abordada pelo protocolo de transporte e não deve ser um fator importante para determinar qual a encriptação no modelo de repouso a utilizar.

### <a name="client-encryption-model"></a>Modelo de encriptação do cliente

O modelo de encriptação do cliente refere-se à encriptação que é realizada fora do Fornecedor de Recursos ou Azure pelo serviço ou aplicação de chamada. A encriptação pode ser realizada pela aplicação de serviço em Azure, ou por uma aplicação em execução no centro de dados do cliente. Em qualquer dos casos, ao aproveitar este modelo de encriptação, o Fornecedor de Recursos Azure recebe uma bolha de dados encriptada sem a capacidade de desencriptar os dados de qualquer forma ou ter acesso às chaves de encriptação. Neste modelo, a gestão chave é feita pelo serviço/aplicação de chamada e é opaca ao serviço Azure.

![Cliente](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de encriptação do lado do servidor

Os modelos de encriptação do lado do servidor referem-se à encriptação que é executada pelo serviço Azure. Neste modelo, o Fornecedor de Recursos realiza as operações de encriptação e desencriptação. Por exemplo, o Armazenamento Azure pode receber dados em operações de texto simples e executar a encriptação e desencriptação internamente. O Fornecedor de Recursos poderá utilizar chaves de encriptação geridas pela Microsoft ou pelo cliente, dependendo da configuração fornecida.

![Servidor](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gestão de chaves de encriptação do lado do servidor

Cada uma das encriptações do lado do servidor nos modelos de repouso implica características distintas da gestão chave. Isto inclui onde e como as chaves de encriptação são criadas, e armazenadas, bem como os modelos de acesso e os procedimentos de rotação chave.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Encriptação do lado do servidor usando chaves geridas pelo serviço

Para muitos clientes, o requisito essencial é garantir que os dados são encriptados sempre que estão em repouso. A encriptação do lado do servidor utilizando chaves geridas pelo serviço permite este modelo, permitindo que os clientes marquem o recurso específico (Conta de Armazenamento, SQL DB, etc.) para encriptação e deixando todos os aspetos de gestão chave, tais como emissão de chaves, rotação e backup para a Microsoft . A maioria dos Serviços Azure que suportam encriptação em repouso normalmente suportam este modelo de descarregamento da gestão das chaves de encriptação para o Azure. O fornecedor de recursos Azure cria as chaves, coloca-as em armazenamento seguro e recupera-as quando necessário. Isto significa que o serviço tem acesso total às chaves e o serviço tem total controlo sobre a gestão do ciclo de vida credencial.

![gerido](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

A encriptação do lado do servidor utilizando chaves geridas pelo serviço, portanto, rapidamente aborda a necessidade de ter encriptação em repouso com baixas despesas gerais para o cliente. Quando disponível, um cliente normalmente abre o portal Azure para o fornecedor de subscrição e recursos alvo e verifica uma caixa indicando, eles gostariam que os dados fossem encriptados. Em alguns gestores de recursos, a encriptação do lado do servidor com chaves geridas pelo serviço está a ser adenada por defeito.

A encriptação do lado do servidor com chaves geridas pela Microsoft implica que o serviço tem acesso total para armazenar e gerir as chaves. Embora alguns clientes queiram gerir as chaves porque sentem que ganham maior segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizadas devem ser considerados na avaliação deste modelo. Em muitos casos, uma organização pode determinar que os constrangimentos de recursos ou riscos de uma solução no local podem ser maiores do que o risco de gestão na nuvem da encriptação em chaves de descanso.  No entanto, este modelo pode não ser suficiente para organizações que têm requisitos para controlar a criação ou ciclo de vida das chaves de encriptação ou para ter pessoal diferente a gerir as chaves de encriptação de um serviço do que aqueles que gerem o serviço (isto é, segregação de gestão chave do modelo de gestão global para o serviço).

##### <a name="key-access"></a>Acesso chave

Quando a encriptação do lado do Servidor com chaves geridas pelo serviço é utilizada, a chave criação, armazenamento e acesso ao serviço são todos geridos pelo serviço. Normalmente, os fornecedores de recursos azure fundamentais armazenarão as Chaves de Encriptação de Dados numa loja próxima dos dados e rapidamente disponíveis e acessíveis enquanto as Chaves de Encriptação chave são armazenadas numa loja interna segura.

**Vantagens**

- Configuração simples
- Microsoft gere rotação de chaves, backup e redundância
- O cliente não tem o custo associado à implementação ou ao risco de um esquema de gestão de chaves personalizadas.

**Desvantagens**

- Nenhum controlo do cliente sobre as chaves de encriptação (especificação chave, ciclo de vida, revogação, etc.)
- Nenhuma capacidade de segregar a gestão chave do modelo de gestão global para o serviço

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre de Chaves Azure

Para cenários em que o requisito é encriptar os dados em repouso e controlar as chaves de encriptação, os clientes podem usar encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre de Chaves. Alguns serviços podem armazenar apenas a chave de encriptação chave no Cofre de Chaves Azure e armazenar a chave de encriptação de dados encriptada num local interno mais próximo dos dados. Nesse cenário, os clientes podem trazer as suas próprias chaves para o Key Vault (BYOK – Bring Your Own Key), ou gerar novas, e usá-las para encriptar os recursos pretendidos. Enquanto o Fornecedor de Recursos executa as operações de encriptação e desencriptação, utiliza a chave de encriptação configurada como chave de raiz para todas as operações de encriptação.

Perda de chaves de encriptação significa perda de dados. Por esta razão, as chaves não devem ser eliminadas. As chaves devem ser apoiadas sempre que forem criadas ou giradas. [O Soft-Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ativado em qualquer cofre que armazene chaves de encriptação. Em vez de apagar uma chave, definido ativado para falsear ou definir a data de validade.

##### <a name="key-access"></a>Acesso chave

O modelo de encriptação do lado do servidor com chaves geridas pelo cliente no Cofre de Chaves Azure envolve o serviço de acesso às chaves para encriptar e desencriptar conforme necessário. A encriptação em chaves de repouso é tornada acessível a um serviço através de uma política de controlo de acesso. Esta política concede ao serviço acesso à identidade para receber a chave. Um serviço Azure em funcionamento em nome de uma subscrição associada pode ser configurado com uma identidade nessa subscrição. O serviço pode realizar a autenticação do Diretório Ativo Azure e receber um símbolo de autenticação identificando-se como esse serviço agindo em nome da subscrição. Esse token pode então ser apresentado ao Cofre chave para obter uma chave a que foi dado acesso.

Para operações utilizando chaves de encriptação, uma identidade de serviço pode ter acesso a qualquer uma das seguintes operações: desencriptar, encriptar, desembrulharKey, wrapKey, verificar, assinar, obter, lista, atualizar, criar, importar, excluir, backup e restaurar.

Para obter uma chave para utilização na encriptação ou desencriptação de dados no resto da identidade de serviço que a instância de serviço do Gestor de Recursos funcionará como deve ter UnwrapKey (para obter a chave para desencriptação) e WrapKey (para inserir uma chave no cofre chave ao criar uma nova chave).

>[!NOTE]
>Para obter mais detalhes sobre a autorização do Cofre chave, consulte a página segura do cofre na documentação do [Cofre de Chaves Azure](../../key-vault/key-vault-secure-your-key-vault.md).

**Vantagens**

- Controlo total sobre as teclas utilizadas – as chaves de encriptação são geridas no Cofre chave do cliente sob o controlo do cliente.
- Capacidade de encriptar múltiplos serviços a um mestre
- Pode segregar gestão chave do modelo de gestão global para o serviço
- Pode definir serviço e localização chave em todas as regiões

**Desvantagens**

- O cliente tem total responsabilidade pela gestão chave do acesso
- Cliente tem total responsabilidade na gestão chave do ciclo de vida
- Custos adicionais de configuração e configuração

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Encriptação do lado do servidor usando chaves geridas pelo cliente em hardware controlado pelo cliente

Alguns serviços Azure permitem o modelo de gestão chave da sua própria chave (HYOK) do anfitrião. Este modo de gestão é útil em cenários em que há necessidade de encriptar os dados em repouso e gerir as chaves num repositório proprietário fora do controlo da Microsoft. Neste modelo, o serviço deve recuperar a chave de um site externo. As garantias de desempenho e disponibilidade são impactadas, e a configuração é mais complexa. Além disso, uma vez que o serviço tem acesso ao DEK durante as operações de encriptação e desencriptação, as garantias globais de segurança deste modelo são semelhantes às de quando as chaves são geridas pelo cliente no Cofre chave azure.  Como resultado, este modelo não é adequado para a maioria das organizações, a menos que tenham requisitos específicos de gestão. Devido a estas limitações, a maioria dos Serviços Azure não suporta maçação do lado do servidor utilizando chaves geridas pelo servidor em hardware controlado pelo cliente.

##### <a name="key-access"></a>Acesso chave

Quando a encriptação do lado do servidor utilizando chaves geridas pelo serviço em hardware controlado pelo cliente é utilizada, as chaves são mantidas num sistema configurado pelo cliente. Os serviços Azure que suportam este modelo fornecem um meio de estabelecer uma ligação segura a uma loja-chave fornecida pelo cliente.

**Vantagens**

- Controlo total sobre a chave de raiz utilizada – as chaves de encriptação são geridas por uma loja fornecida pelo cliente
- Capacidade de encriptar múltiplos serviços a um mestre
- Pode segregar gestão chave do modelo de gestão global para o serviço
- Pode definir serviço e localização chave em todas as regiões

**Desvantagens**

- Total responsabilidade pelo armazenamento chave, segurança, desempenho e disponibilidade
- Plena responsabilidade pela gestão chave do acesso
- Plena responsabilidade pela gestão chave do ciclo de vida
- Custos significativos de configuração, configuração e manutenção contínua
- Maior dependência da disponibilidade da rede entre o datacenter do cliente e os datacenters do Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Encriptação em repouso nos serviços de nuvem da Microsoft

Os serviços microsoft Cloud são usados nos três modelos em nuvem: IaaS, PaaS, SaaS. Abaixo tem exemplos de como se encaixam em cada modelo:

- Serviços de software, referidos como Software como um Servidor ou SaaS, que têm aplicação fornecida pela nuvem, como o Office 365.
- Serviços de plataforma que os clientes alavancam a nuvem nas suas aplicações, utilizando a nuvem para coisas como armazenamento, análise e funcionalidade de ônibus de serviço.
- Serviços de infraestrutura, ou Infraestruturas como Serviço (IaaS) em que o cliente implementa sistemas operativos e aplicações que estão alojados na nuvem e possivelmente alavancando outros serviços na nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Encriptação em repouso para clientes SaaS

Os clientes do Software como Serviço (SaaS) normalmente têm encriptação em repouso ativada ou disponível em cada serviço. O Office 365 tem várias opções para os clientes verificarem ou ativarem a encriptação em repouso. Para obter informações sobre os serviços do Office 365, consulte [encriptação no Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Encriptação em repouso para clientes PaaS

Os dados do cliente da Plataforma como Serviço (PaaS) residem normalmente num serviço de armazenamento como o Blob Storage, mas também podem ser armazenados em cache ou armazenados no ambiente de execução da aplicação, como uma máquina virtual. Para ver a encriptação nas opções de repouso disponível para si, examine a tabela abaixo para as plataformas de armazenamento e aplicação que utiliza.

### <a name="encryption-at-rest-for-iaas-customers"></a>Encriptação em repouso para clientes IaaS

Os clientes da Infraestrutura como Serviço (IaaS) podem ter uma variedade de serviços e aplicações em uso. Os serviços IaaS podem permitir a encriptação em repouso no seu Azure hospedado em máquinas virtuais e VHDs usando encriptação de disco azure.

#### <a name="encrypted-storage"></a>Armazenamento encriptado

Tal como o PaaS, as soluções IaaS podem aproveitar outros serviços Azure que armazenam dados encriptados em repouso. Nestes casos, pode ativar o suporte de Encriptação em Repouso, conforme fornecido por cada serviço Azure consumido. A tabela abaixo enumera as principais plataformas de armazenamento, serviços e aplicações e o modelo de Encriptação em Repouso suportado. 

#### <a name="encrypted-compute"></a>Computação encriptada

Todos os Discos Geridos, Snapshots e Imagens são encriptados usando encriptação do serviço de armazenamento usando uma chave gerida pelo serviço. Uma solução de encriptação mais completa no Rest garante que os dados nunca são persistidos de forma não encriptada. Durante o processamento dos dados de uma máquina virtual, os dados podem ser persistidos no ficheiro da página do Windows ou no ficheiro de troca de Linux, num despejo de falha ou num registo de aplicações. Para garantir que estes dados são encriptados em repouso, as aplicações IaaS podem utilizar encriptação de disco Azure numa máquina virtual Azure IaaS (Windows ou Linux) e em disco virtual.

#### <a name="custom-encryption-at-rest"></a>Encriptação personalizada em repouso

Recomenda-se que sempre que possível, as aplicações IaaS alavancam as opções de encriptação e encriptação do Disco Azure em Repouso fornecidas por quaisquer serviços Azure consumidos. Em alguns casos, tais como requisitos de encriptação irregulares ou armazenamento não baseado em Azure, um desenvolvedor de uma aplicação IaaS pode precisar implementar encriptação em si mesmos. Os desenvolvedores de soluções IaaS podem integrar-se melhor com a gestão do Azure e as expectativas dos clientes, alavancando certos componentes do Azure. Especificamente, os desenvolvedores devem usar o serviço Azure Key Vault para fornecer armazenamento de chaves seguras, bem como fornecer aos seus clientes opções de gestão chave consistentes com a maioria dos serviços da plataforma Azure. Além disso, as soluções personalizadas devem utilizar identidades de serviço geridas pelo Azure para permitir que as contas de serviço acedam a chaves de encriptação. Para obter informações sobre o Cofre de Chaves Azure e identidades de serviço geridos, consulte os respetivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de encriptação de fornecedores de recursos Azure

Os Serviços Microsoft Azure suportam cada um ou mais a encriptação em modelos de repouso. Para alguns serviços, no entanto, um ou mais dos modelos de encriptação podem não ser aplicáveis. Para serviços que suportam cenários-chave geridos pelo cliente, eles podem suportar apenas um subconjunto dos tipos-chave que o Azure Key Vault suporta para chaves de encriptação chave. Além disso, os serviços podem libertar suporte para estes cenários e tipos-chave em diferentes horários. Esta secção descreve a encriptação no suporte de repouso no momento desta escrita para cada um dos principais serviços de armazenamento de dados azure.

### <a name="azure-disk-encryption"></a>Encriptação de disco azure

Qualquer cliente que utilize as funcionalidades da Infraestrutura Azure como serviço (IaaS) pode obter encriptação em repouso para os seus VMs iaaS e discos através da Encriptação do Disco Azure. Para obter mais informações sobre encriptação do Disco Azure, consulte a documentação de encriptação do [Disco Azure](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Storage do Azure

Todos os serviços de armazenamento azure (armazenamento blob, armazenamento de fila, armazenamento de mesa e Ficheiros Azure) suportam encriptação do lado do servidor em repouso; alguns serviços suportam adicionalmente chaves geridas pelo cliente e encriptação do lado do cliente. 

- Lado do servidor: Todos os Serviços de Armazenamento Azure permitem a encriptação do lado do servidor por padrão utilizando chaves geridas pelo serviço, o que é transparente para a aplicação. Para mais informações, consulte a Encriptação do Serviço de [Armazenamento Azure para dados em repouso](../../storage/common/storage-service-encryption.md). O armazenamento Azure Blob e os Ficheiros Azure também suportam chaves geridas por clientes RSA 2048 no Cofre chave Azure. Para mais informações, consulte a Encriptação do Serviço de [Armazenamento utilizando chaves geridas pelo cliente no Cofre de Chaves Azure](../../storage/common/storage-encryption-keys-portal.md).
- Lado do cliente: As Bolhas Azure, As Mesas e as Filas suportam a encriptação do lado do cliente. Ao utilizar a encriptação do lado do cliente, os clientes encriptam os dados e fazem o upload dos dados como uma bolha encriptada. A gestão chave é feita pelo cliente. Para mais informações, consulte a Encriptação do Lado do Cliente e o [Cofre de Chaves Azure para o Armazenamento Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Tualmente, a Azure SQL Database suporta encriptação em repouso para cenários de encriptação geridos pela Microsoft e do lado do cliente.

O suporte para encriptação do servidor é fornecido atualmente através da funcionalidade SQL chamada Encriptação de Dados Transparentes. Uma vez que um cliente de base de dados Azure SQL ativa a chave TDE são automaticamente criados e geridos para eles. A encriptação em repouso pode ser ativada nos níveis de base de dados e servidores. A partir de junho de 2017, a Encriptação transparente de [dados (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) é ativada por padrão em bases de dados recém-criadas. A Base de Dados Azure SQL suporta chaves geridas por clientes RSA 2048 no Cofre chave Azure. Para mais informações, consulte a Encriptação transparente de dados com o Bring Your Own Key suporte para a Base de Dados E Armazém de [Dados Azure SQL](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

A encriptação do lado do cliente dos dados da Base de Dados Azure SQL é suportada através da funcionalidade [Always Encrypted.](https://msdn.microsoft.com/library/mt163865.aspx) Sempre encriptado usa uma chave que criou e armazena pelo cliente. Os clientes podem armazenar a chave principal numa loja de certificados Windows, Azure Key Vault ou num Módulo de Segurança de Hardware local. Utilizando o SQL Server Management Studio, os utilizadores sQL escolhem qual a chave que gostariam de usar para encriptar qual coluna.

#### <a name="encryption-model-and-key-management-table"></a>Modelo de encriptação e tabela de gestão de chaves

|                                  |                    | **Modelo de encriptação e gestão de chaves** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Servidor-lado usando chave gerida pelo serviço**     | **Lado do servidor usando a chave gerida pelo cliente**             | **Lado do cliente usando chave gerida pelo cliente**      |
| **IA e Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Sim                | Sim                | -                  |
| Azure Machine Learning           | Sim                | Sim                | -                  |
| Azure Machine Learning Studio    | Sim                | Pré-visualização, RSA 2048-bit | -               |
| Power BI                         | Sim                | Pré-visualização, RSA 2048-bit | -                  |
| **Análise**                    |                    |                    |                    |
| Azure Stream Analytics           | Sim                | -                  | -                  |
| Hubs de Eventos                       | Sim                | Sim, todos os Comprimentos RSA. | -                  |
| Funções                        | Sim                | Sim, todos os Comprimentos RSA. | -                  |
| Azure Analysis Services          | Sim                | -                  | -                  |
| Catálogo de Dados do Azure               | Sim                | -                  | -                  |
| Apache Kafka no Azure HDInsight  | Sim                | Todos os comprimentos RSA.   | -                  |
| Insights de aplicação do Monitor Azure | Sim                | Sim                | -                  |
| Análise de log do Monitor Azure | Sim                | Sim                | -                  |
| Azure Data Explorer              | Sim                | Sim                | -                  |
| Azure Data Factory               | Sim                | Sim                | -                  |
| Azure Data Lake Store            | Sim                | Sim, RSA 2048-bit  | -                  |
| **Contentores**                   |                    |                    |                    |
| Azure Kubernetes Service         | Sim                | Sim                | -                  |
| Container Instances              | Sim                | Sim                | -                  |
| Registo de Contentor               | Sim                | Sim                | -                  |
| **Computação**                      |                    |                    |                    |
| Virtual Machines                 | Sim                | Sim, RSA 2048-bit  | -                  |
| Conjunto de escala de máquina virtual        | Sim                | Sim, RSA 2048-bit  | -                  |
| SAP HANA                         | Sim                | Sim, RSA 2048-bit  | -                  |
| Serviço de Aplicações                      | Sim                | Sim                | -                  |
| Automatização                       | Sim                | Sim                | -                  |
| Portal do Azure                     | Sim                | Sim                | -                  |
| Aplicações Lógicas                       | Sim                | Sim                | -                  |
| Aplicações Geridas do Azure       | Sim                | Sim                | -                  |
| Service Bus                      | Sim                | Sim                | -                  |
| Site Recovery                    | Sim                | Sim                | -                  |
| **Bases de dados**                    |                    |                    |                    |
| SQL Server em Máquinas Virtuais   | Sim                | Sim, RSA 2048-bit  | Sim                |
| Base de Dados SQL do Azure               | Sim                | Sim, RSA 2048-bit  | Sim                |
| Base de Dados Azure SQL para MariaDB   | Sim                | -                  | -                  |
| Base de dados Azure SQL para MySQL     | Sim                | Sim                | -                  |
| Base de dados Azure SQL para PostgreSQL | Sim               | Sim                | -                  |
| Azure Synapse Analytics          | Sim                | Sim, RSA 2048-bit  | Sim                |
| SQL Server Stretch Database      | Sim                | Sim, RSA 2048-bit  | Sim                |
| Armazenamento de Tabelas                    | Sim                | Sim                | Sim                |
| Azure Cosmos DB                  | Sim                | Sim                | -                  |
| Azure Databricks                 | Sim                | Sim                | -                  |
| **DevOps**                       |                    |                    |                    |
| DevOps do Azure                     | Sim                | -                  | Sim                |
| Repositórios do Azure                      | Sim                | -                  | Sim                |
| **Identidade**                     |                    |                    |                    |
| Azure Active Directory           | Sim                | -                  | -                  |
| Azure Active Directory Domain Services | Sim          | Sim, RSA 2048-bit  | -                  |
| **Integração**                  |                    |                    |                    |
| Service Bus                      | Sim                | Sim                | Sim                |
| Event Grid                       | Sim                | -                  | -                  |
| Gestão de API                   | Sim                | -                  | -                  |
| **Serviços IoT**                 |                    |                    |                    |
| IoT Hub                          | Sim                | Sim                | Sim                |
| **Gestão e Governação**    |                    |                    |                    |
| Azure Site Recovery              | Sim                | -                  | -                  |
| **Meios de comunicação**                        |                    |                    |                    |
| Serviços de Multimédia                   | Sim                | -                  | Sim                |
| **Armazenamento**                      |                    |                    |                    |
| Armazenamento de Blobs                     | Sim                | Sim, RSA 2048-bit  | Sim                |
| Armazenamento em Disco                     | Sim                | Sim                | -                  |
| Armazenamento de disco gerido             | Sim                | Sim                | -                  |
| Armazenamento de Ficheiros                     | Sim                | Sim, RSA 2048-bit  | -                  |
| Armazenamento de filas                    | Sim                | Sim                | Sim                |
| Avere vFXT                       | Sim                | -                  | -                  |
| Azure NetApp Files               | Sim                | -                  | -                  |
| Armazenamento de Arquivo                  | Sim                | Sim, RSA 2048-bit  | -                  |
| StorSimple                       | Sim                | Sim, RSA 2048-bit  | Sim                |
| Azure Backup                     | Sim                | Sim                | Sim                |
| Data Box                         | Sim                | -                  | Sim                |
| Data Box Edge                    | Sim                | Sim                | -                  |

## <a name="conclusion"></a>Conclusão

A proteção dos dados dos clientes armazenados nos Serviços Azure é da maior importância para a Microsoft. Todos os serviços hospedados do Azure estão empenhados em fornecer opções de Encriptação em Repouso. Serviços fundacionais como o Azure Storage, Azure SQL Database e serviços de análise e inteligência chave já fornecem opções de Encriptação no Rest. Alguns destes serviços suportam chaves controladas pelo cliente e encriptação do lado do cliente, bem como chaves e encriptação geridas pelo serviço. Os serviços do Microsoft Azure estão a melhorar em termos gerais a disponibilidade de Encriptação no Rest e estão previstas novas opções para pré-visualização e disponibilidade geral nos próximos meses.
