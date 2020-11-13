---
title: Azure Data Encryption-at-Rest - Azure Security
description: Este artigo fornece uma visão geral da encriptação de dados Azure em repouso, as capacidades globais e considerações gerais.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: c9a68661a89f53c5aa27bdd046b5bc09a47db400
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556629"
---
# <a name="azure-data-encryption-at-rest"></a>Encriptação de dados inativa do Azure

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

A encriptação em repouso também pode ser exigida pela necessidade de uma organização para esforços de governação de dados e conformidade. Regulamentos da indústria e do governo, tais como HIPAA, PCI e FedRAMP, estabelecem salvaguardas específicas no que diz respeito aos requisitos de proteção de dados e encriptação. A encriptação em repouso é uma medida obrigatória necessária para o cumprimento de alguns desses regulamentos. Para obter mais informações sobre a abordagem da Microsoft à validação do FIPS 140-2, consulte [a Publicação 140-2 da Federal Information Processing Standard (FIPS).](/microsoft-365/compliance/offering-fips-140-2)

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

As teclas de encriptação de dados, encriptadas com as Chaves de Encriptação de Chaves são armazenadas separadamente e apenas uma entidade com acesso à Chave de Encriptação pode desencriptar estas Chaves de Encriptação de Dados. São suportados diferentes modelos de armazenamento de chaves. Consulte [os modelos de encriptação de dados](encryption-models.md) para obter mais informações.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Encriptação em repouso nos serviços de cloud da Microsoft

Os serviços da Microsoft Cloud são utilizados nos três modelos em nuvem: IaaS, PaaS, SaaS. Abaixo você tem exemplos de como se encaixam em cada modelo:

- Serviços de software, referidos como Software como Um Servidor ou SaaS, que têm aplicações fornecidas pela nuvem como o Microsoft 365.
- Serviços de plataforma que os clientes aproveitam a nuvem nas suas aplicações, usando a nuvem para coisas como armazenamento, análise e funcionalidade de ônibus de serviço.
- Serviços de infraestrutura, ou Infraestrutura como Serviço (IaaS) em que o cliente implementa sistemas operativos e aplicações que estão hospedados na nuvem e possivelmente alavancando outros serviços na nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Encriptação em repouso para clientes SaaS

Os clientes de software como serviço (SaaS) normalmente têm encriptação em repouso ativada ou disponível em cada serviço. O Microsoft 365 tem várias opções para os clientes verificarem ou permitirem a encriptação em repouso. Para obter informações sobre os serviços microsoft 365, consulte [a Encriptação no Microsoft 365](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Encriptação em repouso para clientes PaaS

Os dados do cliente da Plataforma como Um Serviço (PaaS) normalmente residem num serviço de armazenamento como o Blob Storage, mas também podem ser em cache ou armazenados no ambiente de execução de aplicações, como uma máquina virtual. Para ver a encriptação nas opções de repouso disponíveis, examine a tabela abaixo para as plataformas de armazenamento e aplicação que utiliza.

### <a name="encryption-at-rest-for-iaas-customers"></a>Encriptação em repouso para clientes IaaS

Os clientes de infraestrutura como Serviço (IaaS) podem ter uma variedade de serviços e aplicações em uso. Os serviços iaaS podem permitir a encriptação em repouso nas suas máquinas virtuais e VHDs alojadas em Azure.

#### <a name="encrypted-storage"></a>Armazenamento encriptado

Tal como o PaaS, as soluções IaaS podem alavancar outros serviços Azure que armazenam dados encriptados em repouso. Nestes casos, pode ativar o suporte de Encriptação em Repouso, conforme fornecido por cada serviço Azure consumido. A tabela abaixo enumera as principais plataformas de armazenamento, serviços e aplicações e o modelo de Encriptação em Repouso suportado.

#### <a name="encrypted-compute"></a>Cálculo encriptado

Todos os discos geridos, instantâneos e imagens são encriptados utilizando encriptação do serviço de armazenamento utilizando uma chave gerida pelo serviço. Uma solução de encriptação mais completa no Rest garante que os dados nunca são persistidos de forma não encriptada. Durante o processamento dos dados numa máquina virtual, os dados podem ser persistidos no ficheiro da página do Windows ou no ficheiro de troca de Linux, num depósito de falhas ou num registo de aplicações. Para garantir que estes dados são encriptados em repouso, as aplicações iaaS podem utilizar encriptação de disco Azure numa máquina virtual Azure IaaS (Windows ou Linux) e disco virtual.

#### <a name="custom-encryption-at-rest"></a>Encriptação personalizada em repouso

Recomenda-se que, sempre que possível, as aplicações da IaaS aproveitem as opções de Encriptação e Encriptação do Disco Azure fornecidas por quaisquer serviços Azure consumidos. Em alguns casos, como requisitos de encriptação irregulares ou armazenamento não-Azure, um desenvolvedor de uma aplicação IaaS pode precisar implementar encriptação em repouso. Os desenvolvedores de soluções IaaS podem integrar-se melhor com a gestão da Azure e as expectativas dos clientes, alavancando certos componentes Azure. Especificamente, os desenvolvedores devem usar o serviço Azure Key Vault para fornecer armazenamento de chaves seguros, bem como fornecer aos seus clientes opções de gestão de chaves consistentes com a maioria dos serviços da plataforma Azure. Além disso, as soluções personalizadas devem usar Azure-Managed Identidades de Serviço para permitir que as contas de serviço tenham acesso a chaves de encriptação. Para obter informações sobre o Azure Key Vault e as Identidades de Serviço Gerido, consulte os respetivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte ao modelo de encriptação de fornecedores de recursos Azure

Os Serviços Microsoft Azure suportam cada um ou mais da encriptação nos modelos de repouso. Para alguns serviços, no entanto, um ou mais dos modelos de encriptação podem não ser aplicáveis. Para serviços que suportam cenários-chave geridos pelo cliente, podem suportar apenas um subconjunto dos tipos-chave que o Azure Key Vault suporta para chaves de encriptação chave. Além disso, os serviços podem libertar suporte para estes cenários e tipos-chave em diferentes horários. Esta secção descreve a encriptação no suporte de repouso no momento desta escrita para cada um dos principais serviços de armazenamento de dados Azure.

### <a name="azure-disk-encryption"></a>Encriptação do disco Azure

Qualquer cliente que utilize as funcionalidades Azure Infrastructure como serviço (IaaS) pode obter encriptação em repouso para os seus VMs e discos IaaS através da Encriptação do Disco Azure. Para obter mais informações sobre a encriptação do disco Azure, consulte a [documentação de encriptação do disco Azure](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Storage do Azure

Todos os serviços de Armazenamento Azure (armazenamento de bolhas, armazenamento de fila, armazenamento de mesa e ficheiros Azure) suportam a encriptação do lado do servidor em repouso; alguns serviços suportam ainda chaves geridas pelo cliente e encriptação do lado do cliente.

- Lado do servidor: Todos os Serviços de Armazenamento Azure permitem a encriptação do lado do servidor por padrão utilizando teclas geridas pelo serviço, que é transparente para a aplicação. Para obter mais informações, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso.](../../storage/common/storage-service-encryption.md) O armazenamento Azure Blob e os Ficheiros Azure também suportam chaves geridas pelo cliente RSA 2048 no Cofre da Chave Azure. Para obter mais informações, consulte [a encriptação do serviço de armazenamento utilizando as chaves geridas pelo cliente no Cofre da Chave Azure](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Lado do cliente: Azure Blobs, Tabelas e Filas suportam encriptação do lado do cliente. Ao utilizar a encriptação do lado do cliente, os clientes encriptam os dados e carregam os dados como uma bolha encriptada. A gestão de chaves é feita pelo cliente. Para obter mais informações, consulte [a encriptação do lado do cliente e o cofre da chave Azure para o Armazenamento Azure da Microsoft](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Azure SQL Database suporta atualmente a encriptação em repouso para o lado do serviço gerido pela Microsoft e cenários de encriptação do lado do cliente.

O suporte para encriptação do servidor é atualmente fornecido através da funcionalidade SQL chamada Encriptação de Dados Transparente. Uma vez que um cliente da Base de Dados Azure SQL permite a chave TDE são automaticamente criados e geridos para eles. A encriptação em repouso pode ser ativada nos níveis de base de dados e servidor. A partir de junho de 2017, [a Encriptação de Dados Transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) é ativada por padrão em bases de dados recém-criadas. A Azure SQL Database suporta chaves geridas pelo cliente RSA 2048 no Cofre da Chave Azure. Para obter mais informações, consulte [encriptação de dados transparente com suporte de chave própria para Azure SQL Database and Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

A encriptação do lado do cliente dos dados da Base de Dados Azure SQL é suportada através da funcionalidade [Always Encrypted.](/sql/relational-databases/security/encryption/always-encrypted-database-engine) Sempre encriptado utiliza uma chave que foi criada e armazenada pelo cliente. Os clientes podem armazenar a chave principal numa loja de certificados Windows, Azure Key Vault ou num Módulo de Segurança de Hardware local. Utilizando o SQL Server Management Studio, os utilizadores do SQL escolhem a chave que gostariam de usar para encriptar qual coluna.

## <a name="conclusion"></a>Conclusão

A proteção dos dados dos clientes armazenados nos Serviços Azure é da maior importância para a Microsoft. Todos os serviços hospedados pela Azure estão empenhados em fornecer opções de Encriptação em Rest. Os serviços Azure suportam chaves geridas pelo serviço, chaves geridas pelo cliente ou encriptação do lado do cliente. Os serviços Azure estão a melhorar amplamente a encriptação na disponibilidade de repouso e estão previstas novas opções para pré-visualização e disponibilidade geral nos próximos meses.

## <a name="next-steps"></a>Passos seguintes

- Consulte [os modelos](encryption-models.md) de encriptação de dados para saber mais sobre chaves geridas pelo serviço e chaves geridas pelo cliente.
- Saiba como o Azure usa [encriptação dupla](double-encryption.md) para mitigar ameaças que vêm com dados encriptadores.
- Saiba o que a Microsoft faz para garantir a [integridade da plataforma e a segurança](platform.md) dos anfitriões através dos pipelines de construção, integração, operacionalização e reparação de hardware.