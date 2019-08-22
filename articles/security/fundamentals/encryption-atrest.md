---
title: Microsoft Azure a criptografia de dados em repouso | Microsoft Docs
description: Este artigo fornece uma visão geral de Microsoft Azure criptografia de dados em repouso, os recursos gerais e as considerações gerais.
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
ms.date: 08/20/2019
ms.author: barclayn
ms.openlocfilehash: 910057e0e81219a68608441530d03ca1a2411b02
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875101"
---
# <a name="azure-data-encryption-at-rest"></a>Criptografia de dados do Azure em repouso

O Microsoft Azure inclui ferramentas para proteger os dados de acordo com as necessidades de segurança e conformidade de sua empresa. Este documento se concentra em:

- Como os dados são protegidos em repouso em Microsoft Azure
- Discute os vários componentes que fazem parte da implementação de proteção de dados,
- Revisa os prós e contras das diferentes abordagens de proteção de gerenciamento de chaves.

A criptografia em repouso é um requisito de segurança comum. No Azure, as organizações podem criptografar dados em repouso sem o risco ou o custo de uma solução de gerenciamento de chaves personalizada. As organizações têm a opção de permitir que o Azure gerencie completamente a criptografia em repouso. Além disso, as organizações têm várias opções para gerenciar de forma rigorosa as chaves de criptografia ou criptografia.

## <a name="what-is-encryption-at-rest"></a>O que é criptografia em repouso?

A criptografia em repouso é a codificação (criptografia) de dados quando ela é persistente. Os designs de criptografia em repouso no Azure usam a criptografia simétrica para criptografar e descriptografar grandes quantidades de dados rapidamente de acordo com um modelo conceitual simples:

- Uma chave de criptografia simétrica é usada para criptografar dados conforme eles são gravados no armazenamento.
- A mesma chave de criptografia é usada para descriptografar os dados, pois eles são pronto para uso na memória.
- Os dados podem ser particionados e chaves diferentes podem ser usadas para cada partição.
- As chaves devem ser armazenadas em um local seguro com controle de acesso baseado em identidade e políticas de auditoria. As chaves de criptografia de dados geralmente são criptografadas com criptografia assimétrica para limitar o acesso.

Na prática, os principais cenários de controle e gerenciamento de chaves, bem como garantias de escala e disponibilidade, exigem construções adicionais. Microsoft Azure conceitos e componentes de criptografia em repouso são descritos abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>A finalidade da criptografia em repouso

A criptografia em repouso fornece proteção de dados para dados armazenados (em repouso). Os ataques contra dados em repouso incluem tentativas de obter acesso físico ao hardware no qual os dados são armazenados e, em seguida, comprometer os dados contidos. Nesse tipo de ataque, o disco rígido de um servidor pode ter sido manipulado intensamente durante a manutenção, permitindo que um invasor remova o disco rígido. Posteriormente, o invasor colocaria o disco rígido em um computador sob seu controle para tentar acessar os dados.

A criptografia em repouso foi projetada para impedir que o invasor acesse os dados não criptografados, garantindo que os dados sejam criptografados quando estiverem no disco. Se um invasor obtiver um disco rígido com dados criptografados, mas não as chaves de criptografia, o invasor deverá derrotar a criptografia para ler os dados. Esse ataque é muito mais complexo e consome recursos do que o acesso a dados não criptografados em um disco rígido. Por esse motivo, a criptografia em repouso é altamente recomendada e é um requisito de alta prioridade para muitas organizações.

A criptografia em repouso também pode ser exigida pela necessidade de uma organização de governança de dados e esforços de conformidade. Os regulamentos do setor e do governo, como HIPAA, PCI e FedRAMP, destacam garantias específicas relacionadas à proteção de dados e aos requisitos de criptografia. A criptografia em repouso é uma medida obrigatória necessária para a conformidade com algumas dessas regulamentações.

Além de atender aos requisitos normativos e de conformidade, a criptografia em repouso fornece proteção de defesa profunda. O Microsoft Azure fornece uma plataforma compatível para serviços, aplicativos e dados. Ele também fornece recursos abrangentes e segurança física, controle de acesso a dados e auditoria. No entanto, é importante fornecer medidas de segurança adicionais "sobrepostas" no caso de uma das outras medidas de segurança falhar e a criptografia em repouso fornece tal medida de segurança

A Microsoft está comprometida com as opções de criptografia em repouso nos serviços de nuvem e oferece aos clientes controle de chaves de criptografia e logs de uso de chave. Além disso, a Microsoft está trabalhando para criptografar todos os dados do cliente em repouso por padrão.

## <a name="azure-encryption-at-rest-components"></a>Componentes de criptografia em repouso do Azure

Conforme descrito anteriormente, o objetivo da criptografia em repouso é que os dados persistentes no disco são criptografados com uma chave de criptografia secreta. Para atingir essa meta, a criação de chave segura, o armazenamento, o controle de acesso e o gerenciamento das chaves de criptografia devem ser fornecidos. Embora os detalhes possam variar, as implementações de criptografia em repouso dos serviços do Azure podem ser descritas em termos ilustrados no diagrama a seguir.

![Componentes](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

O local de armazenamento das chaves de criptografia e do controle de acesso a essas chaves é central para um modelo de criptografia em repouso. As chaves precisam ser altamente protegidas, mas gerenciáveis por usuários especificados e disponíveis para serviços específicos. Para os serviços do Azure, Azure Key Vault é a solução de armazenamento de chave recomendada e fornece uma experiência de gerenciamento comum entre serviços. As chaves são armazenadas e gerenciadas em cofres de chaves, e o acesso a um cofre de chaves pode ser fornecido a usuários ou serviços. Azure Key Vault dá suporte à criação de chaves de cliente ou importação de chaves do cliente para uso em cenários de chave de criptografia gerenciada pelo cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

As permissões para usar as chaves armazenadas no Azure Key Vault, seja para gerenciar ou acessá-las para criptografia em repouso e descriptografia, podem ser dadas a contas de Azure Active Directory.

### <a name="key-hierarchy"></a>Hierarquia de chave

Mais de uma chave de criptografia é usada em uma implementação de criptografia em repouso. A criptografia assimétrica é útil para estabelecer a confiança e a autenticação necessárias para o gerenciamento e o acesso à chave. A criptografia simétrica é mais eficiente para criptografia e descriptografia em massa, permitindo uma criptografia mais forte e melhor desempenho. Limitar o uso de uma única chave de criptografia diminui o risco de que a chave seja comprometida e o custo de nova criptografia quando uma chave deve ser substituída. Os modelos de criptografia do Azure em repouso usam uma hierarquia de chave composta pelos seguintes tipos de chaves:

- **DEK (chave de criptografia de dados)** – uma chave de aes256s simétrica usada para criptografar uma partição ou um bloco de dados.  Um único recurso pode ter muitas partições e muitas chaves de criptografia de dados. A criptografia de cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso ao DEKs é necessário para o provedor de recursos ou instância do aplicativo que está Criptografando e descriptografando um bloco específico. Quando um DEK é substituído por uma nova chave, somente os dados em seu bloco associado devem ser criptografados novamente com a nova chave.
- Chave **de criptografia de chave (Kek)** – uma chave de criptografia assimétrica usada para criptografar as chaves de criptografia de dados. O uso de uma chave de criptografia de chave permite que as próprias chaves de criptografia de dados sejam criptografadas e controladas. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma entidade pode ser um agente de acesso ao DEK para limitar o acesso de cada DEK a uma partição específica. Como o KEK é necessário para descriptografar o DEKs, o KEK é efetivamente um único ponto pelo qual DEKs pode ser efetivamente excluído pela exclusão do KEK.

As chaves de criptografia de dados, criptografadas com as chaves de criptografia de chave, são armazenadas separadamente e apenas uma entidade com acesso à chave de criptografia de chave pode obter quaisquer chaves de criptografia de dados criptografadas com essa chave. Há suporte para diferentes modelos de armazenamento de chaves. Discutiremos cada modelo em mais detalhes posteriormente na próxima seção.

## <a name="data-encryption-models"></a>Modelos de criptografia de dados

Uma compreensão dos vários modelos de criptografia e seus prós e contras é essencial para entender como os vários provedores de recursos no Azure implementam a criptografia em repouso. Essas definições são compartilhadas entre todos os provedores de recursos no Azure para garantir a linguagem e a taxonomia comuns.

Há três cenários para a criptografia do lado do servidor:

- Criptografia do lado do servidor usando chaves gerenciadas pelo serviço
  - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
  - A Microsoft gerencia as chaves
  - Funcionalidade de nuvem completa

- Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault
  - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
  - O cliente controla as chaves via Azure Key Vault
  - Funcionalidade de nuvem completa

- Criptografia do lado do servidor usando chaves gerenciadas pelo cliente em hardware controlado pelo cliente
  - Provedores de recursos do Azure executam as operações de criptografia e descriptografia
  - O cliente controla as chaves no hardware controlado pelo cliente
  - Funcionalidade de nuvem completa

Para criptografia do lado do cliente, considere o seguinte:

- Os serviços do Azure não podem ver dados descriptografados
- Os clientes gerenciam e armazenam chaves locais (ou em outros repositórios seguros). As chaves não estão disponíveis para os serviços do Azure
- Funcionalidade de nuvem reduzida

Os modelos de criptografia com suporte no Azure são divididos em dois grupos principais: "Criptografia de cliente" e "criptografia no lado do servidor", conforme mencionado anteriormente. Independentemente do modelo de criptografia em repouso usado, os serviços do Azure sempre recomendam o uso de um transporte seguro como, por exemplo, TLS ou HTTPS. Portanto, a criptografia no transporte deve ser resolvida pelo protocolo de transporte e não deve ser um fator importante para determinar o modelo de criptografia em repouso a ser usado.

### <a name="client-encryption-model"></a>Modelo de criptografia de cliente

O modelo de criptografia de cliente refere-se à criptografia que é executada fora do provedor de recursos ou do Azure pelo serviço ou aplicativo de chamada. A criptografia pode ser executada pelo aplicativo de serviço no Azure ou por um aplicativo em execução no data center do cliente. Em ambos os casos, ao aproveitar esse modelo de criptografia, o provedor de recursos do Azure recebe um blob criptografado de dados sem a capacidade de descriptografar os dados de forma alguma ou ter acesso às chaves de criptografia. Nesse modelo, o gerenciamento de chaves é feito pelo aplicativo ou serviço de chamada e é opaco para o serviço do Azure.

![Cliente](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de criptografia do lado do servidor

Os modelos de criptografia do lado do servidor referem-se à criptografia que é executada pelo serviço do Azure. Nesse modelo, o provedor de recursos executa as operações de criptografar e descriptografar. Por exemplo, o armazenamento do Azure pode receber dados em operações de texto sem formatação e executará a criptografia e descriptografia internamente. O provedor de recursos pode usar chaves de criptografia que são gerenciadas pela Microsoft ou pelo cliente, dependendo da configuração fornecida.

![Servidor](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gerenciamento de chaves de criptografia do lado do servidor

Cada um dos modelos de criptografia em repouso do lado do servidor implica características distintivos do gerenciamento de chaves. Isso inclui onde e como as chaves de criptografia são criadas e armazenadas, bem como os modelos de acesso e os procedimentos de rotação de chaves.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Criptografia do lado do servidor usando chaves gerenciadas pelo serviço

Para muitos clientes, o requisito essencial é garantir que os dados sejam criptografados sempre que estiverem em repouso. A criptografia do lado do servidor usando chaves gerenciadas pelo serviço habilita esse modelo permitindo que os clientes marquem o recurso específico (conta de armazenamento, BD SQL, etc.) para criptografia e deixando todos os aspectos de gerenciamento de chaves, como emissão de chave, rotação e backup para a Microsoft . A maioria dos serviços do Azure que dão suporte à criptografia em repouso normalmente dá suporte a esse modelo de descarregamento do gerenciamento das chaves de criptografia para o Azure. O provedor de recursos do Azure cria as chaves, coloca-as no armazenamento seguro e as recupera quando necessário. Isso significa que o serviço tem acesso completo às chaves e o serviço tem controle total sobre o gerenciamento do ciclo de vida da credencial.

![administra](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

A criptografia do lado do servidor usando chaves gerenciadas pelo serviço, portanto, resolve rapidamente a necessidade de ter criptografia em repouso com baixa sobrecarga para o cliente. Quando disponível, um cliente normalmente abre o portal do Azure para a assinatura de destino e o provedor de recursos e marca uma caixa indicando que eles gostariam que os dados fossem criptografados. Em alguns gerenciadores de recursos, a criptografia do lado do servidor com chaves gerenciadas pelo serviço está ativada por padrão.

A criptografia do lado do servidor com chaves gerenciadas pela Microsoft implica que o serviço tem acesso completo para armazenar e gerenciar as chaves. Embora alguns clientes possam querer gerenciar as chaves porque acham que têm maior segurança, o custo e o risco associados a uma solução de armazenamento de chaves personalizada devem ser considerados ao avaliar esse modelo. Em muitos casos, uma organização pode determinar que as restrições de recursos ou os riscos de uma solução local podem ser maiores do que o risco de gerenciamento de nuvem das chaves de criptografia em repouso.  No entanto, esse modelo pode não ser suficiente para organizações que têm requisitos para controlar a criação ou o ciclo de vida das chaves de criptografia ou para que pessoas diferentes gerenciem chaves de criptografia de um serviço do que aquelas que gerenciam o serviço (ou seja, segregação do gerenciamento de chaves do modelo de gerenciamento geral para o serviço).

##### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do servidor com chaves gerenciadas pelo serviço é usada, a criação de chave, o armazenamento e o acesso ao serviço são gerenciados pelo serviço. Normalmente, os provedores de recursos do Azure fundamentais armazenarão as chaves de criptografia de dados em um armazenamento que está próximo dos dados e rapidamente disponíveis e acessíveis enquanto as chaves de criptografia de chave são armazenadas em um repositório interno seguro.

**Principais**

- Configuração simples
- A Microsoft gerencia a rotação de chaves, o backup e a redundância
- O cliente não tem o custo associado à implementação ou o risco de um esquema de gerenciamento de chaves personalizado.

**Desvantagens**

- Nenhum controle de cliente sobre as chaves de criptografia (especificação de chave, ciclo de vida, revogação, etc.)
- Sem capacidade de separar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault

Para cenários em que o requisito é criptografar os dados em repouso e controlar as chaves de criptografia, os clientes podem usar a criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Key Vault. Alguns serviços podem armazenar apenas a chave de criptografia de chave raiz em Azure Key Vault e armazenar a chave de criptografia de dados criptografados em um local interno mais próximo dos dados. Nesse cenário, os clientes podem trazer suas próprias chaves para Key Vault (BYOK – Bring Your Own Key) ou gerar novas e usá-las para criptografar os recursos desejados. Enquanto o provedor de recursos executa as operações de criptografia e descriptografia, ele usa a chave configurada como a chave raiz para todas as operações de criptografia.

##### <a name="key-access"></a>Acesso à chave

O modelo de criptografia do lado do servidor com chaves gerenciadas pelo cliente no Azure Key Vault envolve o serviço que acessa as chaves para criptografar e descriptografar conforme necessário. Chaves de criptografia em repouso tornam-se acessíveis para um serviço por meio de uma política de controle de acesso. Essa política concede o acesso de identidade de serviço para receber a chave. Um serviço do Azure em execução em nome de uma assinatura associada pode ser configurado com uma identidade nessa assinatura. O serviço pode executar Azure Active Directory autenticação e receber um token de autenticação que se identifica como aquele serviço que atua em nome da assinatura. Esse token pode ser apresentado a Key Vault para obter uma chave para a qual tenha sido concedido acesso.

Para operações que usam chaves de criptografia, uma identidade de serviço pode receber acesso a qualquer uma das seguintes operações: descriptografar, criptografar, unwrapKey, wrapKey, verificar, assinar, obter, listar, atualizar, criar, importar, excluir, fazer backup e restaurar.

Para obter uma chave para uso na criptografia ou descriptografia de dados em repouso, a identidade do serviço que a instância do serviço do Gerenciador de recursos executará como deve ter UnwrapKey (para obter a chave para descriptografia) e WrapKey (para inserir uma chave no cofre de chaves ao criar uma nova chave).

>[!NOTE]
>Para obter mais detalhes sobre a autorização de Key Vault, consulte a página proteger o cofre de chaves na [documentação do Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md).

**Principais**

- Controle total sobre as chaves usadas – as chaves de criptografia são gerenciadas no Key Vault do cliente sob o controle do cliente.
- Capacidade de criptografar vários serviços para um mestre
- Pode separar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço
- Pode definir o local do serviço e da chave entre regiões

**Desvantagens**

- O cliente tem total responsabilidade pelo gerenciamento de acesso a chaves
- O cliente tem total responsabilidade pelo gerenciamento do ciclo de vida da chave
- A configuração adicional & sobrecarga de configurações

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Criptografia do lado do servidor usando chaves gerenciadas pelo serviço em hardware controlado pelo cliente

Alguns serviços do Azure habilitam o modelo de gerenciamento de chaves de HYOK (hospedar sua própria chave). Esse modo de gerenciamento é útil em cenários em que há a necessidade de criptografar os dados em repouso e gerenciar as chaves em um repositório proprietário fora do controle da Microsoft. Nesse modelo, o serviço deve recuperar a chave de um site externo. As garantias de desempenho e disponibilidade são afetadas e a configuração é mais complexa. Além disso, como o serviço tem acesso ao DEK durante as operações de criptografia e descriptografia, as garantias gerais de segurança desse modelo são semelhantes quando as chaves são gerenciadas pelo cliente no Azure Key Vault.  Como resultado, esse modelo não é apropriado para a maioria das organizações, a menos que tenham requisitos específicos de gerenciamento de chaves. Devido a essas limitações, a maioria dos serviços do Azure não oferece suporte à criptografia do lado do servidor usando chaves gerenciadas pelo servidor em hardware controlado pelo cliente.

##### <a name="key-access"></a>Acesso à chave

Quando a criptografia do lado do servidor usando chaves gerenciadas pelo serviço no hardware controlado pelo cliente é usada, as chaves são mantidas em um sistema configurado pelo cliente. Os serviços do Azure que dão suporte a esse modelo fornecem um meio de estabelecer uma conexão segura com um repositório de chaves fornecido pelo cliente.

**Principais**

- Controle total sobre a chave raiz usada – as chaves de criptografia são gerenciadas por um repositório fornecido pelo cliente
- Capacidade de criptografar vários serviços para um mestre
- Pode separar o gerenciamento de chaves do modelo de gerenciamento geral para o serviço
- Pode definir o local do serviço e da chave entre regiões

**Desvantagens**

- Responsabilidade total pelo armazenamento de chaves, segurança, desempenho e disponibilidade
- Responsabilidade total pelo gerenciamento de acesso a chaves
- Responsabilidade total pelo gerenciamento do ciclo de vida da chave
- Custos significativos de configuração, configuração e manutenção contínua
- Maior dependência da disponibilidade de rede entre o datacenter do cliente e os datacenters do Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Criptografia em repouso nos serviços em nuvem da Microsoft

Os serviços de Microsoft Cloud são usados em todos os três modelos de nuvem: IaaS, PaaS, SaaS. Abaixo, você tem exemplos de como eles se encaixam em cada modelo:

- Serviços de software, chamados de software como um servidor ou SaaS, que têm o aplicativo fornecido pela nuvem, como o Office 365.
- Serviços de plataforma que os clientes aproveitam a nuvem em seus aplicativos, usando a nuvem para coisas como o armazenamento, a análise e a funcionalidade do barramento de serviço.
- Serviços de infraestrutura ou IaaS (infraestrutura como serviço) em que o cliente implanta sistemas operacionais e aplicativos hospedados na nuvem e, possivelmente, aproveitando outros serviços de nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Criptografia em repouso para clientes SaaS

Os clientes SaaS (software como serviço) normalmente têm criptografia em repouso habilitada ou disponível em cada serviço. O Office 365 tem várias opções para que os clientes verifiquem ou habilitem a criptografia em repouso. Para obter informações sobre os serviços do Office 365, consulte [criptografia no Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Criptografia em repouso para clientes de PaaS

Os dados do cliente de PaaS (plataforma como serviço) normalmente residem em um serviço de armazenamento, como o armazenamento de BLOB, mas também podem ser armazenados em cache ou armazenadas no ambiente de execução do aplicativo, como uma máquina virtual. Para ver as opções de criptografia em repouso disponíveis para você, examine a tabela abaixo para as plataformas de armazenamento e aplicativo que você usa.

### <a name="encryption-at-rest-for-iaas-customers"></a>Criptografia em repouso para clientes IaaS

Os clientes de IaaS (infraestrutura como serviço) podem ter uma variedade de serviços e aplicativos em uso. Os serviços de IaaS podem habilitar a criptografia em repouso em suas máquinas virtuais hospedadas do Azure e VHDs usando Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Armazenamento criptografado

Como a PaaS, as soluções de IaaS podem aproveitar outros serviços do Azure que armazenam dados criptografados em repouso. Nesses casos, você pode habilitar o suporte de criptografia em repouso conforme fornecido por cada serviço do Azure consumido. A tabela abaixo enumera o armazenamento, os serviços e as plataformas de aplicativos principais e o modelo de criptografia em repouso com suporte. 

#### <a name="encrypted-compute"></a>Computação criptografada

Todos os Managed Disks, instantâneos e imagens são criptografados usando Criptografia do Serviço de Armazenamento usando uma chave gerenciada por serviço. Uma solução mais completa de criptografia em repouso garante que os dados nunca sejam persistidos em formato não criptografado. Durante o processamento dos dados em uma máquina virtual, os dados podem ser persistidos no arquivo de paginação do Windows ou no arquivo de permuta do Linux, em um despejo de memória ou em um log de aplicativo. Para garantir que esses dados sejam criptografados em repouso, os aplicativos IaaS podem usar Azure Disk Encryption em uma máquina virtual IaaS do Azure (Windows ou Linux) e disco virtual.

#### <a name="custom-encryption-at-rest"></a>Criptografia personalizada em repouso

É recomendável que, sempre que possível, os aplicativos IaaS aproveitem as opções de Azure Disk Encryption e criptografia em repouso fornecidas por qualquer serviço do Azure consumido. Em alguns casos, como requisitos de criptografia irregulares ou armazenamento não baseado no Azure, um desenvolvedor de um aplicativo IaaS pode precisar implementar a criptografia em repouso. Os desenvolvedores de soluções de IaaS podem se integrar melhor com o gerenciamento do Azure e as expectativas do cliente, aproveitando determinados componentes do Azure. Especificamente, os desenvolvedores devem usar o serviço de Azure Key Vault para fornecer armazenamento de chaves seguro, bem como fornecer aos clientes opções de gerenciamento de chaves consistentes com o da maioria dos serviços da plataforma Azure. Além disso, as soluções personalizadas devem usar as identidades de serviço gerenciadas pelo Azure para permitir que as contas de serviço acessem as chaves de criptografia. Para obter informações de desenvolvedor sobre Azure Key Vault e identidades de serviço gerenciado, consulte seus respectivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de criptografia de provedores de recursos do Azure

Os serviços Microsoft Azures oferecem suporte a um ou mais dos modelos de criptografia em repouso. Para alguns serviços, no entanto, um ou mais dos modelos de criptografia podem não ser aplicáveis. Para serviços que dão suporte a cenários de chave gerenciado pelo cliente, eles podem dar suporte a apenas um subconjunto dos tipos de chave que Azure Key Vault suporte para chaves de criptografia de chave. Além disso, os serviços podem liberar suporte para esses cenários e tipos de chave em agendas diferentes. Esta seção descreve o suporte à criptografia em repouso no momento da elaboração deste artigo para cada um dos principais serviços de armazenamento de dados do Azure.

### <a name="azure-disk-encryption"></a>Criptografia de disco do Azure

Qualquer cliente que use recursos de IaaS (infraestrutura como serviço) do Azure pode alcançar a criptografia em repouso para suas VMs e discos de IaaS por meio de Azure Disk Encryption. Para obter mais informações sobre a criptografia de disco do Azure, consulte a [documentação do Azure Disk Encryption](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Storage do Azure

Todos os serviços de armazenamento do Azure (armazenamento de BLOBs, armazenamento de filas, armazenamento de tabelas e arquivos do Azure) dão suporte à criptografia no lado do servidor em repouso; alguns serviços adicionalmente dão suporte a chaves gerenciadas pelo cliente e criptografia do lado do cliente. 

- No lado do servidor: Todos os serviços de armazenamento do Azure habilitam a criptografia do lado do servidor por padrão usando chaves gerenciadas pelo serviço, que é transparente para o aplicativo. Para obter mais informações, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](../../storage/common/storage-service-encryption.md). O armazenamento de BLOBs do Azure e os arquivos do Azure também dão suporte a chaves gerenciadas pelo cliente RSA de 2048 bits no Azure Key Vault. Para obter mais informações, consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- No lado do cliente: Os BLOBs, tabelas e filas do Azure dão suporte à criptografia do lado do cliente. Ao usar a criptografia do lado do cliente, os clientes criptografam os dados e carregam os dados como um blob criptografado. O gerenciamento de chaves é feito pelo cliente. Para obter mais informações, consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Atualmente, o banco de dados SQL do Azure dá suporte à criptografia em repouso para cenários de criptografia do lado do cliente e dos serviços gerenciados pela Microsoft.

No momento, o suporte para criptografia de servidor é fornecido por meio do recurso SQL chamado Transparent Data Encryption. Depois que um cliente do banco de dados SQL do Azure habilita a chave TDE, ela é automaticamente criada e gerenciada para elas. A criptografia em repouso pode ser habilitada nos níveis do banco de dados e do servidor. A partir de junho de 2017, a [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) é habilitada por padrão em bancos de dados recém-criados. O banco de dados SQL do Azure dá suporte a chaves gerenciadas pelo cliente RSA de 2048 bits no Azure Key Vault. Para obter mais informações, consulte [Transparent Data Encryption com suporte de Bring your own Key para o banco de dados SQL do Azure e data warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

A criptografia do lado do cliente de dados do Azure SQL Database é suportada por meio do recurso [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) . Always Encrypted usa uma chave que foi criada e armazenada pelo cliente. Os clientes podem armazenar a chave mestra em um repositório de certificados do Windows, Azure Key Vault ou um módulo de segurança de hardware local. Usando SQL Server Management Studio, os usuários do SQL escolhem qual chave gostaria de usar para criptografar qual coluna.

#### <a name="encryption-model-and-key-management"></a>Modelo de criptografia e gerenciamento de chaves

| **Lado do servidor usando chave gerenciada pelo serviço** |**Lado do servidor usando chave gerenciada pelo cliente**| **No lado do cliente usando gerenciado pelo cliente** | **IA e Machine Learning**   |
|--------------------|-------------------|----------------------|--------------------|
| Azure Search                     | Sim                | -                  | -                  |
| Serviço Azure Machine Learning   | Sim                | -                  | -                  |
| Azure Machine Learning Studio    | Sim                | Versão prévia, RSA 2048 bits | -               |
| Power BI                         | Sim                | Versão prévia, RSA 2048 bits | -               |
| **Análise**                    |                    |                    |                    |
| Azure Stream Analytics           | Sim                | -                  | -                  |
| Hubs de Eventos                       | Sim                | -                  | -                  |
| Azure Analysis Services          | Sim                | -                  | -                  |
| Catálogo de Dados do Azure               | Sim                | -                  | -                  |
| Apache Kafka no Azure HDInsight  | Sim                | Todos os comprimentos RSA.   | -                  |
| Azure Data Factory               | Sim                | -                  | -                  |
| Azure Data Lake Store            | Sim                | Sim, RSA 2048 bits  | -                  |
| **Contentores**                   |                    |                    |                    |
| Serviço Kubernetes do Azure         | Sim                | -                  | -                  |
| Registo de Contentor               | Sim                | -                  | -                  |
| **Computação**                      |                    |                    |                    |
| Virtual Machines                 | Sim                | Sim, RSA 2048 bits  | -                  |
| Conjunto de Dimensionamento da Máquina Virtual        | Sim                | Sim, RSA 2048 bits  | -                  |
| SAP HANA                         | Sim                | Sim, RSA 2048 bits  | -                  |
| **Bases de dados**                    |                    |                    |                    |
| SQL Server em Máquinas Virtuais   | Sim                | Sim, RSA 2048 bits  | Sim                |
| Base de Dados SQL do Azure               | Sim                | Sim, RSA 2048 bits  | Sim                |
| Banco de dados SQL do Azure para MariaDB   | Sim                | -                  | -                  |
| Banco de dados SQL do Azure para MySQL     | Sim                | -                  | -                  |
| Banco de dados SQL do Azure para PostgreSQL | Sim                | -                  | -                 |
| Azure SQL Data Warehouse         | Sim                | Sim, RSA 2048 bits  | Sim                |
| SQL Server Stretch Database      | Sim                | Sim, RSA 2048 bits  | Sim                |
| Armazenamento de Tabelas                    | Sim                | -                  | Sim                |
| Azure Cosmos DB                  | Sim                | -                  | -                  |
| **DevOps**                       |                    |                    |                    |
| DevOps do Azure                     | Sim                | -                  | Sim                |
| Repositórios do Azure                      | Sim                | -                  | Sim                |
| **Identidade**                     |                    |                    |                    |
| Azure Active Directory           | Sim                | -                  | -                  |
| Azure Active Directory Domain Services | Sim          | Sim, RSA 2048 bits  | -                  |
| **Integrar**                  |                    |                    |                    |
| Service Bus                      | Sim                | -                  | Sim                |
| Event Grid                       | Sim                | -                  | -                  |
| Gestão de API                   | Sim                | -                  | -                  |
| **Serviços de IoT**                 |                    |                    |                    |
| IoT Hub                          | Sim                | -                  | Sim                |
| **Gerenciamento e governança**    |                    |                    |                    |
| Azure Site Recovery              | Sim                | Sim, RSA 2048 bits  | Sim                |
| **Meio**                        |                    |                    |                    |
| Serviços de Multimédia                   | Sim                | -                  | Sim                |
| **Armazenamento**                      |                    |                    |                    |
| Armazenamento de Blobs                     | Sim                | Sim, RSA 2048 bits  | Sim                |
| Armazenamento em Disco                     | Sim                | -                  | -                  |
| Armazenamento de Disco Gerido             | Sim                | -                  | -                  |
| Armazenamento de Ficheiros                     | Sim                | Sim, RSA 2048 bits  | -                  |
| Armazenamento de filas                    | Sim                | -                  | Sim                |
| Avere vFXT                       | Sim                | -                  | -                  |
| Ficheiros NetApp do Azure               | Sim                | -                  | -                  |
| Armazenamento de Arquivo                  | Sim                | Sim, RSA 2048 bits  | -                  |
| StorSimple                       | Sim                | Sim, RSA 2048 bits  | Sim                |
| Azure Backup                     | Sim                | -                  | Sim                |
| Data Box                         | Sim                | -                  | Sim                |

## <a name="conclusion"></a>Conclusão

A proteção dos dados do cliente armazenados nos serviços do Azure é de extrema importância para a Microsoft. Todos os serviços hospedados do Azure estão comprometidos em fornecer opções de criptografia em repouso. Serviços fundamentais, como o armazenamento do Azure, o banco de dados SQL do Azure e o Key Analytics e os serviços de inteligência, já fornecem opções de criptografia em repouso. Alguns desses serviços dão suporte a chaves controladas pelo cliente e criptografia do lado do cliente, bem como chaves e criptografia gerenciadas pelo serviço. Microsoft Azure serviços estão amplamente aprimorando a criptografia em disponibilidade em repouso e novas opções são planejadas para a versão prévia e a disponibilidade geral nos próximos meses.
