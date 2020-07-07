---
title: Encriptação de dados do Microsoft Azure Microsoft Docs
description: Este artigo fornece uma visão geral da encriptação de dados do Microsoft Azure em repouso, as capacidades globais e considerações gerais.
services: security
documentationcenter: na
author: msmbaldwin
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: mbaldwin
ms.openlocfilehash: 1e08e758fbba911d3391794f5bab31aaf6a5fc73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81454684"
---
# <a name="azure-data-encryption-at-rest"></a>Encriptação de dados Azure-at-Rest

O Microsoft Azure inclui ferramentas para salvaguardar os dados de acordo com as necessidades de segurança e conformidade da sua empresa. Este artigo centra-se em:

- Como os dados são protegidos em repouso em toda a Microsoft Azure
- Discute-se os vários componentes que participam na implementação da proteção de dados,
- Revê prós e contras das diferentes abordagens de proteção de gestão chave.

A encriptação em Repouso é um requisito de segurança comum. No Azure, as organizações podem encriptar dados em repouso sem o risco ou o custo de uma solução de gestão de chaves personalizada. As organizações têm a opção de deixar o Azure gerir completamente a Encriptação em Repouso. Além disso, as organizações têm várias opções para gerir de perto chaves de encriptação ou encriptação.

## <a name="what-is-encryption-at-rest"></a>O que é encriptação em repouso?

A encriptação em Rest é a codificação (encriptação) dos dados quando são persistentes. Os designs de Encriptação em Repouso em Azure usam encriptação simétrica para encriptar e desencriptar grandes quantidades de dados rapidamente de acordo com um modelo conceptual simples:

- Uma chave de encriptação simétrica é usada para encriptar dados à medida que são escritos para armazenamento.
- A mesma chave de encriptação é usada para desencriptar esses dados à medida que são reutilizados para serem utilizados na memória.
- Os dados podem ser divididos e podem ser utilizadas teclas diferentes para cada partição.
- As chaves devem ser armazenadas num local seguro com políticas de controlo de acesso e auditoria baseadas em identidade. As chaves de encriptação de dados são frequentemente encriptadas com uma chave de encriptação chave no Cofre da Chave Azure para limitar ainda mais o acesso.

Na prática, os principais cenários de gestão e controlo, bem como garantias de escala e disponibilidade, requerem construções adicionais. A encriptação e componentes do Microsoft Azure em Rest são descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>O propósito da encriptação em repouso

A encriptação em repouso fornece proteção de dados para dados armazenados (em repouso). Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware em que os dados são armazenados e, em seguida, comprometer os dados contidos. Em tal ataque, o disco rígido de um servidor pode ter sido mal tratado durante a manutenção, permitindo que um intruso remova o disco rígido. Mais tarde, o intruso colocaria o disco rígido num computador sob o seu controlo para tentar aceder aos dados.

A encriptação em repouso foi concebida para evitar que o intruso aceda aos dados não encriptados, garantindo que os dados são encriptados quando estão no disco. Se um intruso obtiver um disco rígido com dados encriptados, mas não com as chaves de encriptação, o intruso deve derrotar a encriptação para ler os dados. Este ataque é muito mais complexo e consumista de recursos do que aceder a dados não encriptados num disco rígido. Por esta razão, a encriptação em repouso é altamente recomendada e é um requisito de alta prioridade para muitas organizações.

A encriptação em repouso também pode ser exigida pela necessidade de uma organização para esforços de governação de dados e conformidade. Regulamentos da indústria e do governo, tais como HIPAA, PCI e FedRAMP, estabelecem salvaguardas específicas no que diz respeito aos requisitos de proteção de dados e encriptação. A encriptação em repouso é uma medida obrigatória necessária para o cumprimento de alguns desses regulamentos. Para obter mais informações sobre a abordagem da Microsoft à validação do FIPS 140-2, consulte [a Publicação 140-2 da Federal Information Processing Standard (FIPS).](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2) 

Além de satisfazer os requisitos de conformidade e regulamentação, a encriptação em repouso proporciona uma proteção aprofundada em termos de defesa. O Microsoft Azure fornece uma plataforma compatível para serviços, aplicações e dados. Fornece também facilidade e segurança física abrangentes, controlo de acesso a dados e auditoria. No entanto, é importante fornecer medidas de segurança adicionais de "sobreposição" no caso de uma das outras medidas de segurança falhar e a encriptação em repouso fornecer tal medida de segurança.

A Microsoft está empenhada em encriptar as opções de repouso em todos os serviços na nuvem e dar aos clientes o controlo das chaves de encriptação e registos de utilização das chaves. Além disso, a Microsoft está a trabalhar no sentido de encriptar todos os dados do cliente em repouso por padrão.

## <a name="azure-encryption-at-rest-components"></a>Encriptação Azure nos Componentes de Repouso

Como descrito anteriormente, o objetivo da encriptação em repouso é que os dados que são persistidos no disco são encriptados com uma chave de encriptação secreta. Para atingir esse objetivo, deve ser fornecida a criação, armazenamento, controlo de acesso e gestão das chaves de encriptação. Embora os detalhes possam variar, os serviços Azure Encriptação em Rest podem ser descritos em termos ilustrados no diagrama seguinte.

![Componentes](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A localização de armazenamento das chaves de encriptação e o controlo de acesso a essas teclas é central para uma encriptação no modelo de repouso. As chaves precisam de ser altamente seguras, mas manejáveis por utilizadores especificados e disponíveis para serviços específicos. Para os serviços Azure, o Azure Key Vault é a solução de armazenamento de chaves recomendada e proporciona uma experiência de gestão comum em todos os serviços. As chaves são armazenadas e geridas em cofres-chave, e o acesso a um cofre chave pode ser dado aos utilizadores ou serviços. O Azure Key Vault suporta a criação de chaves ou a importação de chaves de cliente para utilização em cenários-chave de encriptação geridos pelo cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

As permissões de utilização das teclas armazenadas no Cofre da Chave Azure, quer para gerir ou aceder a elas para encriptação em repouso, podem ser dadas às contas do Azure Ative Directory.

### <a name="key-hierarchy"></a>Hierarquia-chave

Mais de uma chave de encriptação é usada numa encriptação na implementação do descanso. Armazenar uma chave de encriptação no Azure Key Vault garante um acesso seguro à chave e uma gestão central das chaves. No entanto, o acesso local ao serviço às chaves de encriptação é mais eficiente para encriptação e desencriptação a granel do que interagir com o Key Vault para cada operação de dados, permitindo uma encriptação mais forte e um melhor desempenho. Limitar a utilização de uma única chave de encriptação diminui o risco de a chave ser comprometida e o custo da reencriminação quando uma chave deve ser substituída. As encriptações Azure em modelos de repouso utilizam uma hierarquia chave composta pelos seguintes tipos de teclas para responder a todas estas necessidades:

- **Chave de encriptação de dados (DEK)** – Uma chave AES256 simétrica usada para encriptar uma divisória ou bloco de dados.  Um único recurso pode ter muitas divisórias e muitas chaves de encriptação de dados. Encriptar cada bloco de dados com uma chave diferente torna os ataques de análise cripto mais difícil. O acesso aos DEKs é necessário pelo fornecedor de recursos ou pela instância de aplicação que está a encriptar e desencriptar um bloco específico. Quando um DEK é substituído por uma nova chave, apenas os dados do seu bloco associado devem ser reencri encriptados com a nova chave.
- **Chave Chave Chave De encriptação (KEK)** – Uma chave de encriptação usada para encriptar as Chaves de Encriptação de Dados. A utilização de uma chave de encriptação que nunca sai do Key Vault permite que as próprias chaves de encriptação de dados sejam encriptadas e controladas. A entidade que tem acesso à KEK pode ser diferente da entidade que requer o DEK. Uma entidade pode intermediar o acesso ao DEK para limitar o acesso de cada DEK a uma divisória específica. Uma vez que o KEK é obrigado a desencriptar os DEKs, o KEK é efetivamente um único ponto pelo qual os DEKs podem ser efetivamente eliminados pela supressão do KEK.

As teclas de encriptação de dados, encriptadas com as Chaves de Encriptação de Chaves são armazenadas separadamente e apenas uma entidade com acesso à Chave de Encriptação pode desencriptar estas Chaves de Encriptação de Dados. São suportados diferentes modelos de armazenamento de chaves. Discutiremos cada modelo mais detalhadamente mais tarde na próxima secção.

## <a name="data-encryption-models"></a>Modelos de encriptação de dados

Uma compreensão dos vários modelos de encriptação e dos seus prós e contras é essencial para entender como os vários fornecedores de recursos em Azure implementam encriptação em Rest. Estas definições são partilhadas em todos os fornecedores de recursos em Azure para garantir a linguagem comum e a taxonomia.

Existem três cenários para encriptação do lado do servidor:

- Encriptação do lado do servidor utilizando teclas geridas por serviço
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Microsoft gere as chaves
  - Funcionalidade de nuvem completa

- Encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre da Chave Azure
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - O cliente controla as chaves através do Cofre da Chave Azure
  - Funcionalidade de nuvem completa

- Encriptação do lado do servidor utilizando chaves geridas pelo cliente em hardware controlado pelo cliente
  - Os Fornecedores de Recursos Azure realizam as operações de encriptação e desencriptação
  - Chaves de controlo do cliente no hardware controlado pelo cliente
  - Funcionalidade de nuvem completa

Para encriptação do lado do cliente, considere o seguinte:

- Serviços Azure não podem ver dados desencriptados
- Os clientes gerem e armazenam chaves no local (ou noutras lojas seguras). As chaves não estão disponíveis para os serviços da Azure
- Funcionalidade de nuvem reduzida

Os modelos de encriptação suportados no Azure dividem-se em dois grupos principais: "Encriptação do Cliente" e "Encriptação do lado do servidor", como mencionado anteriormente. Independentemente da encriptação no modelo de repouso utilizado, os serviços Azure recomendam sempre a utilização de um transporte seguro, como TLS ou HTTPS. Por conseguinte, a encriptação nos transportes deve ser abordada pelo protocolo de transporte e não deve ser um fator importante para determinar qual a encriptação no modelo de repouso a utilizar.

### <a name="client-encryption-model"></a>Modelo de encriptação do cliente

O modelo de encriptação do cliente refere-se à encriptação que é realizada fora do Fornecedor de Recursos ou Azure pelo serviço ou aplicação de chamada. A encriptação pode ser realizada pela aplicação de serviço em Azure, ou por uma aplicação em execução no centro de dados do cliente. Em qualquer dos casos, ao alavancar este modelo de encriptação, o Fornecedor de Recursos Azure recebe uma bolha encriptada de dados sem a capacidade de desencriptar os dados de qualquer forma ou ter acesso às chaves de encriptação. Neste modelo, a gestão chave é feita pelo serviço de chamada/aplicação e é opaca ao serviço Azure.

![Cliente](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de encriptação do lado do servidor

Os modelos de encriptação do lado do servidor referem-se à encriptação que é executada pelo serviço Azure. Neste modelo, o Fornecedor de Recursos executa as operações de encriptação e desencriptação. Por exemplo, o Azure Storage pode receber dados em operações de texto simples e executar a encriptação e desencriptação internamente. O Fornecedor de Recursos poderá utilizar chaves de encriptação que são geridas pela Microsoft ou pelo cliente, dependendo da configuração fornecida.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gestão de chaves de encriptação do lado do servidor

Cada uma das encriptações do lado do servidor nos modelos de repouso implica características distintas da gestão das chaves. Isto inclui onde e como as chaves de encriptação são criadas, e armazenadas, bem como os modelos de acesso e os principais procedimentos de rotação.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Encriptação do lado do servidor usando chaves geridas pelo serviço

Para muitos clientes, o requisito essencial é garantir que os dados são encriptados sempre que estão em repouso. A encriptação do lado do servidor utilizando chaves geridas pelo serviço permite que os clientes marquem o recurso específico (Conta de Armazenamento, DB SQL, etc.) para encriptação e deixando todos os aspetos de gestão chave, tais como emissão de chaves, rotação e backup para a Microsoft. A maioria dos Serviços Azure que suportam a encriptação em repouso normalmente suportam este modelo de descarregamento da gestão das chaves de encriptação para o Azure. O fornecedor de recursos Azure cria as chaves, coloca-as em armazenamento seguro e recupera-as quando necessário. Isto significa que o serviço tem acesso total às chaves e o serviço tem controlo total sobre a gestão do ciclo de vida credencial.

![gerido](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

A encriptação do lado do servidor utilizando as teclas geridas pelo serviço resolve-se rapidamente a necessidade de ter encriptação em repouso com sobrecarga baixa para o cliente. Quando disponível, um cliente normalmente abre o portal Azure para o fornecedor de subscrição e recursos alvo e verifica uma caixa indicando, eles gostariam que os dados fossem encriptados. Em alguma encriptação do lado do servidor de Gestores de Recursos com teclas geridas pelo serviço está por defeito.

A encriptação do lado do servidor com as teclas geridas pela Microsoft implica que o serviço tem acesso total à loja e gerir as chaves. Embora alguns clientes possam querer gerir as chaves porque sentem que ganham maior segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizada devem ser considerados na avaliação deste modelo. Em muitos casos, uma organização pode determinar que as restrições de recursos ou riscos de uma solução no local podem ser maiores do que o risco de gestão em nuvem da encriptação em chaves de repouso.  No entanto, este modelo pode não ser suficiente para as organizações que têm requisitos para controlar a criação ou ciclo de vida das chaves de encriptação ou para ter pessoal diferente a gerir as chaves de encriptação de um serviço do que aqueles que gerem o serviço (isto é, a segregação da gestão chave do modelo de gestão geral para o serviço).

##### <a name="key-access"></a>Acesso à chave

Quando a encriptação do lado do servidor com teclas geridas pelo serviço é utilizada, a criação, armazenamento e acesso ao serviço são geridos pelo serviço. Normalmente, os fornecedores de recursos Azure fundacionais armazenarão as Chaves de Encriptação de Dados numa loja que está próxima dos dados e rapidamente disponível e acessível enquanto as Chaves de Encriptação chave são armazenadas numa loja interna segura.

**Vantagens**

- Configuração simples
- Microsoft gere rotação chave, backup e redundância
- O cliente não tem o custo associado à implementação ou o risco de um esquema de gestão de chaves personalizado.

**Desvantagens**

- Sem controlo do cliente sobre as chaves de encriptação (especificação chave, ciclo de vida, revogação, etc.)
- Sem capacidade de segregar a gestão chave do modelo de gestão global para o serviço

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do lado do servidor usando chaves geridas pelo cliente no Cofre da Chave Azure

Para cenários em que o requisito é encriptar os dados em repouso e controlar as chaves de encriptação, os clientes podem usar encriptação do lado do servidor utilizando chaves geridas pelo cliente no Cofre de Chaves. Alguns serviços podem armazenar apenas a chave de encriptação raiz no Cofre de Chaves Azure e armazenar a Chave de Encriptação de Dados encriptada numa localização interna mais próxima dos dados. Nesse cenário, os clientes podem trazer as suas próprias chaves para o Key Vault (BYOK – Bring Your Own Key), ou gerar novas, e usá-las para encriptar os recursos desejados. Enquanto o Fornecedor de Recursos executa as operações de encriptação e desencriptação, utiliza a chave de encriptação configurada como chave de raiz para todas as operações de encriptação.

Perda das chaves de encriptação significa perda de dados. Por esta razão, as chaves não devem ser apagadas. As teclas devem ser apoiadas sempre que criadas ou giradas. [O Soft-Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ativado em qualquer chave de encriptação do cofre. Em vez de eliminar uma chave, o conjunto pode ser falso ou fixado a data de validade.

##### <a name="key-access"></a>Acesso à Chave

O modelo de encriptação do lado do servidor com chaves geridas pelo cliente no Cofre da Chave Azure envolve o serviço que acede às chaves para encriptar e desencriptar conforme necessário. A encriptação nas chaves de repouso é tornada acessível a um serviço através de uma política de controlo de acesso. Esta política concede ao serviço acesso à identidade para receber a chave. Um serviço Azure em execução em nome de uma subscrição associada pode ser configurado com uma identidade nessa subscrição. O serviço pode realizar a autenticação do Azure Ative Directory e receber um token de autenticação identificando-se como esse serviço agindo em nome da subscrição. Esse símbolo pode então ser apresentado ao Key Vault para obter uma chave a que foi dado acesso.

Para operações que utilizem chaves de encriptação, pode ser concedido um acesso a qualquer uma das seguintes operações: desencriptação, encriptação, desembrulharKey, wrapKey, verificar, assinar, obter, lista, atualizar, criar, importar, eliminar, backup e restaurar.

Para obter uma chave para utilização na encriptação ou desencriptação de dados em repouso, a identidade do serviço que a instância de serviço do Gestor de Recursos irá executar como deve ter UnwrapKey (para obter a chave para a desencriptação) e WrapKey (para inserir uma chave no cofre chave ao criar uma nova chave).

>[!NOTE]
>Para obter mais detalhes sobre a autorização do Key Vault consulte a página do cofre da chave na documentação do [Cofre da Chave Azure](../../key-vault/general/secure-your-key-vault.md).

**Vantagens**

- Controlo total sobre as teclas utilizadas – as chaves de encriptação são geridas no Cofre chave do cliente sob o controlo do cliente.
- Capacidade de encriptar vários serviços a um mestre
- Pode segregar a gestão chave do modelo de gestão global para o serviço
- Pode definir serviço e localização chave em todas as regiões

**Desvantagens**

- O cliente tem total responsabilidade pela gestão de acessos chave
- O cliente tem total responsabilidade pela gestão chave do ciclo de vida
- Configuração adicional & configuração sobrecarga

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Encriptação do lado do servidor utilizando chaves geridas pelo cliente em hardware controlado pelo cliente

Alguns serviços Azure permitem o modelo de gestão de chaves host Your Own Key (HYOK). Este modo de gestão é útil em cenários onde existe a necessidade de encriptar os dados em repouso e gerir as chaves num repositório proprietário fora do controlo da Microsoft. Neste modelo, o serviço deve recuperar a chave de um site externo. As garantias de desempenho e disponibilidade são impactadas e a configuração é mais complexa. Além disso, uma vez que o serviço tem acesso ao DEK durante as operações de encriptação e desencriptação, as garantias de segurança globais deste modelo são semelhantes às quando as chaves são geridas pelo cliente no Azure Key Vault.  Como resultado, este modelo não é adequado para a maioria das organizações, a menos que tenham requisitos específicos de gestão. Devido a estas limitações, a maioria dos Serviços Azure não suporta encriptação do lado do servidor utilizando chaves geridas pelo servidor em hardware controlado pelo cliente.

##### <a name="key-access"></a>Acesso à Chave

Quando a encriptação do lado do servidor utilizando chaves geridas pelo serviço no hardware controlado pelo cliente é utilizada, as teclas são mantidas num sistema configurado pelo cliente. Os serviços Azure que suportam este modelo fornecem um meio de estabelecer uma ligação segura a uma loja de chaves fornecida pelo cliente.

**Vantagens**

- Controlo total sobre a chave raiz utilizada – as chaves de encriptação são geridas por um cliente fornecido
- Capacidade de encriptar vários serviços a um mestre
- Pode segregar a gestão chave do modelo de gestão global para o serviço
- Pode definir serviço e localização chave em todas as regiões

**Desvantagens**

- Total responsabilidade pelo armazenamento, segurança, desempenho e disponibilidade
- Responsabilidade total pela gestão de acessos chave
- Responsabilidade total pela gestão chave do ciclo de vida
- Configuração, configuração e custos de manutenção em curso
- Aumento da dependência da disponibilidade de rede entre o datacenter do cliente e os datacenters da Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Encriptação em repouso nos serviços de cloud da Microsoft

Os serviços da Microsoft Cloud são utilizados nos três modelos em nuvem: IaaS, PaaS, SaaS. Abaixo você tem exemplos de como se encaixam em cada modelo:

- Serviços de software, referidos como Software como Servidor ou SaaS, que têm aplicação fornecida pela nuvem como o Office 365.
- Serviços de plataforma que os clientes aproveitam a nuvem nas suas aplicações, usando a nuvem para coisas como armazenamento, análise e funcionalidade de ônibus de serviço.
- Serviços de infraestrutura, ou Infraestrutura como Serviço (IaaS) em que o cliente implementa sistemas operativos e aplicações que estão hospedados na nuvem e possivelmente alavancando outros serviços na nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Encriptação em repouso para clientes SaaS

Os clientes de software como serviço (SaaS) normalmente têm encriptação em repouso ativada ou disponível em cada serviço. O Office 365 tem várias opções para os clientes verificarem ou permitirem a encriptação em repouso. Para obter informações sobre os serviços do Office 365, consulte [a Encriptação no Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Encriptação em repouso para clientes PaaS

Os dados do cliente da Plataforma como Um Serviço (PaaS) normalmente residem num serviço de armazenamento como o Blob Storage, mas também podem ser em cache ou armazenados no ambiente de execução de aplicações, como uma máquina virtual. Para ver a encriptação nas opções de repouso disponíveis, examine a tabela abaixo para as plataformas de armazenamento e aplicação que utiliza.

### <a name="encryption-at-rest-for-iaas-customers"></a>Encriptação em repouso para clientes IaaS

Os clientes de infraestrutura como Serviço (IaaS) podem ter uma variedade de serviços e aplicações em uso. Os serviços iaaS podem permitir a encriptação em repouso nas suas máquinas virtuais e VHDs alojadas em Azure.

#### <a name="encrypted-storage"></a>Armazenamento encriptado

Tal como o PaaS, as soluções IaaS podem alavancar outros serviços Azure que armazenam dados encriptados em repouso. Nestes casos, pode ativar o suporte de Encriptação em Repouso, conforme fornecido por cada serviço Azure consumido. A tabela abaixo enumera as principais plataformas de armazenamento, serviços e aplicações e o modelo de Encriptação em Repouso suportado. 

#### <a name="encrypted-compute"></a>Cálculo encriptado

Todos os discos geridos, instantâneos e imagens são encriptados utilizando encriptação do serviço de armazenamento utilizando uma chave gerida pelo serviço. Uma solução de encriptação mais completa no Rest garante que os dados nunca são persistidos de forma não encriptada. Durante o processamento dos dados numa máquina virtual, os dados podem ser persistidos no ficheiro da página do Windows ou no ficheiro de troca de Linux, num depósito de falhas ou num registo de aplicações. Para garantir que estes dados são encriptados em repouso, as aplicações iaaS podem utilizar encriptação de disco Azure numa máquina virtual Azure IaaS (Windows ou Linux) e disco virtual.

#### <a name="custom-encryption-at-rest"></a>Encriptação personalizada em repouso

Recomenda-se que, sempre que possível, as aplicações da IaaS aproveitem as opções de Encriptação e Encriptação do Disco Azure fornecidas por quaisquer serviços Azure consumidos. Em alguns casos, como requisitos de encriptação irregulares ou armazenamento não-Azure, um desenvolvedor de uma aplicação IaaS pode precisar implementar encriptação em repouso. Os desenvolvedores de soluções IaaS podem integrar-se melhor com a gestão da Azure e as expectativas dos clientes, alavancando certos componentes Azure. Especificamente, os desenvolvedores devem usar o serviço Azure Key Vault para fornecer armazenamento de chaves seguros, bem como fornecer aos seus clientes opções de gestão de chaves consistentes com a maioria dos serviços da plataforma Azure. Além disso, as soluções personalizadas devem usar identidades de serviço geridas pelo Azure para permitir que as contas de serviço tenham acesso a chaves de encriptação. Para obter informações sobre o Azure Key Vault e as Identidades de Serviço Gerido, consulte os respetivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de encriptação de fornecedores de recursos Azure

Os Serviços Microsoft Azure suportam cada um ou mais da encriptação nos modelos de repouso. Para alguns serviços, no entanto, um ou mais dos modelos de encriptação podem não ser aplicáveis. Para serviços que suportam cenários-chave geridos pelo cliente, podem suportar apenas um subconjunto dos tipos-chave que o Azure Key Vault suporta para chaves de encriptação chave. Além disso, os serviços podem libertar suporte para estes cenários e tipos-chave em diferentes horários. Esta secção descreve a encriptação no suporte de repouso no momento desta escrita para cada um dos principais serviços de armazenamento de dados Azure.

### <a name="azure-disk-encryption"></a>Encriptação do disco Azure

Qualquer cliente que utilize as funcionalidades Azure Infrastructure como serviço (IaaS) pode obter encriptação em repouso para os seus VMs e discos IaaS através da Encriptação do Disco Azure. Para obter mais informações sobre a encriptação do disco Azure, consulte a [documentação de encriptação do disco Azure](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Storage do Azure

Todos os serviços de Armazenamento Azure (armazenamento de bolhas, armazenamento de fila, armazenamento de mesa e ficheiros Azure) suportam a encriptação do lado do servidor em repouso; alguns serviços suportam ainda chaves geridas pelo cliente e encriptação do lado do cliente. 

- Lado do servidor: Todos os Serviços de Armazenamento Azure permitem a encriptação do lado do servidor por padrão utilizando teclas geridas pelo serviço, que é transparente para a aplicação. Para obter mais informações, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso.](../../storage/common/storage-service-encryption.md) O armazenamento Azure Blob e os Ficheiros Azure também suportam chaves geridas pelo cliente RSA 2048 no Cofre da Chave Azure. Para obter mais informações, consulte [a encriptação do serviço de armazenamento utilizando as chaves geridas pelo cliente no Cofre da Chave Azure](../../storage/common/storage-encryption-keys-portal.md).
- Lado do cliente: Azure Blobs, Tabelas e Filas suportam encriptação do lado do cliente. Ao utilizar a encriptação do lado do cliente, os clientes encriptam os dados e carregam os dados como uma bolha encriptada. A gestão de chaves é feita pelo cliente. Para obter mais informações, consulte [a encriptação do lado do cliente e o cofre da chave Azure para o Armazenamento Azure da Microsoft](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Azure SQL Database suporta atualmente a encriptação em repouso para o lado do serviço gerido pela Microsoft e cenários de encriptação do lado do cliente.

O suporte para encriptação do servidor é atualmente fornecido através da funcionalidade SQL chamada Encriptação de Dados Transparente. Uma vez que um cliente da Base de Dados Azure SQL permite a chave TDE são automaticamente criados e geridos para eles. A encriptação em repouso pode ser ativada nos níveis de base de dados e servidor. A partir de junho de 2017, [a Encriptação de Dados Transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) é ativada por padrão em bases de dados recém-criadas. A Azure SQL Database suporta chaves geridas pelo cliente RSA 2048 no Cofre da Chave Azure. Para obter mais informações, consulte [encriptação de dados transparente com suporte de chave própria para Azure SQL Database and Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

A encriptação do lado do cliente dos dados da Base de Dados Azure SQL é suportada através da funcionalidade [Always Encrypted.](https://msdn.microsoft.com/library/mt163865.aspx) Sempre encriptado utiliza uma chave que foi criada e armazenada pelo cliente. Os clientes podem armazenar a chave principal numa loja de certificados Windows, Azure Key Vault ou num Módulo de Segurança de Hardware local. Utilizando o SQL Server Management Studio, os utilizadores do SQL escolhem a chave que gostariam de usar para encriptar qual coluna.

#### <a name="encryption-model-and-key-management-table"></a>Modelo de encriptação e tabela de gestão chave

|                                  |                    | **Modelo de encriptação e gestão de chaves** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Lado do servidor utilizando a chave gerida pelo serviço**     | **Lado do servidor usando a chave gerida pelo cliente**             | **Lado do cliente usando a chave gerida pelo cliente**      |
| **IA e Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Sim                | Sim                | -                  |
| Serviços Cognitivos do Azure         | Sim                | Sim                | -                  |
| Azure Machine Learning           | Sim                | Sim                | -                  |
| Azure Machine Learning Studio    | Yes                | Pré-visualização, RSA 2048-bit | -               |
| Content Moderator                | Sim                | Sim                | -                  |
| Face                             | Sim                | Sim                | -                  |
| Compreensão de Idiomas           | Sim                | Sim                | -                  |
| Personalizador                     | Sim                | Sim                | -                  |
| Criador de FAQ                        | Sim                | Sim                | -                  |
| Serviços de Voz                  | Sim                | Sim                | -                  |
| Tradução de Texto                  | Sim                | Sim                | -                  |
| Power BI                         | Yes                | Pré-visualização, RSA 2048-bit | -                  |
| **Análise**                    |                    |                    |                    |
| Azure Stream Analytics           | Yes                | N/D\*            | -                  |
| Hubs de Eventos                       | Yes                | Sim, todos os comprimentos RSA. | -                  |
| Funções                        | Yes                | Sim, todos os comprimentos RSA. | -                  |
| Azure Analysis Services          | Yes                | -                  | -                  |
| Catálogo de Dados do Azure               | Yes                | -                  | -                  |
| Azure HDInsight                  | Yes                | Todos                | -                  |
| Insights de aplicação do monitor Azure | Sim                | Sim                | -                  |
| Azure Monitor Log Analytics      | Sim                | Sim                | -                  |
| Azure Data Explorer              | Sim                | Sim                | -                  |
| Azure Data Factory               | Sim                | Sim                | -                  |
| Azure Data Lake Store            | Yes                | Sim, RSA 2048-bit  | -                  |
| **Contentores**                   |                    |                    |                    |
| Azure Kubernetes Service         | Sim                | Sim                | -                  |
| Container Instances              | Sim                | Sim                | -                  |
| Container Registry               | Sim                | Sim                | -                  |
| **Computação**                      |                    |                    |                    |
| Máquinas Virtuais                 | Yes                | Sim, RSA 2048-bit  | -                  |
| Conjunto de escala de máquina virtual        | Yes                | Sim, RSA 2048-bit  | -                  |
| SAP HANA                         | Yes                | Sim, RSA 2048-bit  | -                  |
| Serviço de Aplicações                      | Yes                | Sim, é o seu\*\*            | -                  |
| Automatização                       | Yes                | Sim, é o seu\*\*            | -                  |
| Funções do Azure                  | Yes                | Sim, é o seu\*\*            | -                  |
| Portal do Azure                     | Yes                | Sim, é o seu\*\*            | -                  |
| Logic Apps                       | Sim                | Sim                | -                  |
| Aplicações Geridas do Azure       | Yes                | Sim, é o seu\*\*            | -                  |
| Service Bus                      | Sim                | Sim                | -                  |
| Recuperação de sites                    | Sim                | Sim                | -                  |
| **Bases de dados**                    |                    |                    |                    |
| SQL Server nas Máquinas Virtuais   | Yes                | Sim, RSA 2048-bit  | Yes                |
| Base de Dados SQL do Azure               | Yes                | Sim, RSA 2048-bit  | Yes                |
| Base de Dados Azure SQL para MariaDB   | Yes                | -                  | -                  |
| Base de Dados Azure SQL para MySQL     | Sim                | Sim                | -                  |
| Base de Dados Azure SQL para PostgreSQL | Sim               | Sim                | -                  |
| Azure Synapse Analytics          | Yes                | Sim, RSA 2048-bit  | -                  |
| SQL Server Stretch Database      | Yes                | Sim, RSA 2048-bit  | Yes                |
| Armazenamento de Tabelas                    | Sim                | Sim                | Sim                |
| Azure Cosmos DB                  | Sim                | Sim                | -                  |
| Azure Databricks                 | Sim                | Sim                | -                  |
| Azure Database Migration Service | Yes                | N/D\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Serviços de DevOps do Azure            | Sim                | -                  | Sim                |
| Repositórios do Azure                      | Sim                | -                  | Sim                |
| **Identidade**                     |                    |                    |                    |
| Azure Active Directory           | Yes                | -                  | -                  |
| Azure Active Directory Domain Services | Yes          | Sim, RSA 2048-bit  | -                  |
| **Integração**                  |                    |                    |                    |
| Service Bus                      | Sim                | Sim                | Sim                |
| Event Grid                       | Yes                | -                  | -                  |
| Gestão de API                   | Yes                | -                  | -                  |
| **Serviços IoT**                 |                    |                    |                    |
| Hub IoT                          | Sim                | Sim                | Sim                |
| Provisão de dispositivos de hub IoT      | Sim                | Sim                | -                  |
| **Gestão e Governação**    |                    |                    |                    |
| Azure Site Recovery              | Yes                | -                  | -                  |
| Azure Migrate                    | Sim                | Sim                | -                  |
| **Media**                        |                    |                    |                    |
| Serviços de Multimédia                   | Sim                | -                  | Sim                |
| **Segurança**                     |                    |                    |                    |
| Centro de Segurança do Azure para IoT    | Sim                | Sim                | -                  |
| Azure Sentinel                   | Sim                | Sim                | -                  |
| **Armazenamento**                      |                    |                    |                    |
| Armazenamento de Blobs                     | Yes                | Sim, RSA 2048-bit  | Yes                |
| Armazenamento Premium Blob             | Yes                | Sim, RSA 2048-bit  | Yes                |
| Armazenamento de Discos                     | Sim                | Sim                | -                  |
| Armazenamento ultra disco               | Sim                | Sim                | -                  |
| Armazenamento de discos geridos             | Sim                | Sim                | -                  |
| Armazenamento de Ficheiros                     | Yes                | Sim, RSA 2048-bit  | -                  |
| Armazenamento Premium de Arquivo             | Yes                | Sim, RSA 2048-bit  | -                  |
| File Sync                         | Yes                | Sim, RSA 2048-bit  | -                  |
| Armazenamento de Filas                    | Sim                | Sim                | Sim                |
| Avere vFXT                       | Yes                | -                  | -                  |
| Cache do Azure para Redis            | Yes                | N/D\*              | -                  |
| Azure NetApp Files               | Sim                | Sim                | -                  |
| Armazenamento de Arquivo                  | Yes                | Sim, RSA 2048-bit  | -                  |
| StorSimple                       | Yes                | Sim, RSA 2048-bit  | Yes                |
| Azure Backup                     | Sim                | Sim                | Sim                |
| Data Box                         | Sim                | -                  | Sim                |
| Data Box Edge                    | Sim                | Sim                | -                  |

\*Este serviço não persiste em dados. Caches transitórios, se houver, são encriptados com uma chave Microsoft.

\*\*Este serviço suporta o armazenamento de dados no seu próprio Cofre chave, conta de armazenamento ou outro serviço que persiste em dados que já suporta encriptação do lado do servidor com chave gerida pelo cliente.

## <a name="conclusion"></a>Conclusão

A proteção dos dados dos clientes armazenados nos Serviços Azure é da maior importância para a Microsoft. Todos os serviços hospedados pela Azure estão empenhados em fornecer opções de Encriptação em Rest. Os serviços Azure suportam chaves geridas pelo serviço, chaves geridas pelo cliente ou encriptação do lado do cliente. Os serviços Azure estão a melhorar amplamente a encriptação na disponibilidade de repouso e estão previstas novas opções para pré-visualização e disponibilidade geral nos próximos meses.
